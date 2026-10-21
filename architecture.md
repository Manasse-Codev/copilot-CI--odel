# Ashley : Architecture technique

Ce document décrit l'architecture technique proposée pour Ashley. Il complète le [README](./README.md) : le README présente le produit, ce document explique comment le construire et pourquoi.

## Table des matières

1. [Options étudiées](#1-options-étudiées)
2. [Architecture retenue](#2-architecture-retenue)
3. [Stack technique](#3-stack-technique)
4. [Backend : monolithe modulaire](#4-backend--monolithe-modulaire)
5. [Multi-tenant : deux lignes de défense](#5-multi-tenant--deux-lignes-de-défense)
6. [Transaction de vente](#6-transaction-de-vente)
7. [Mode hors ligne](#7-mode-hors-ligne)
8. [Alertes, rapports et tâches planifiées](#8-alertes-rapports-et-tâches-planifiées)
9. [Copilot IA : pipeline technique](#9-copilot-ia--pipeline-technique)
10. [Frontend](#10-frontend)
11. [Authentification](#11-authentification)
12. [Déploiement et observabilité](#12-déploiement-et-observabilité)
13. [Ce que l'on ne fait pas](#13-ce-que-lon-ne-fait-pas)
14. [Ordre de construction](#14-ordre-de-construction)
15. [Décisions encore ouvertes](#15-décisions-encore-ouvertes)

---

## 1. Options étudiées

> **Décision : option A retenue** (monolithe modulaire FastAPI + PostgreSQL + PWA React + worker séparé).

| Option | Idée | Verdict |
|---|---|---|
| **A. Monolithe modulaire** | FastAPI + PostgreSQL + PWA React + worker séparé | **Recommandée** |
| **B. Backend-as-a-Service** | Supabase (base, auth, RLS) + front React + petit service Python pour le Copilot | Bonne variante si le développement est mené seul avec peu de temps |
| **C. Microservices** | Un service par domaine | À écarter : complexité sans bénéfice à ce stade |

**Option A.** Elle donne le contrôle total sur la transaction de vente, le multi-tenant et le Copilot, qui sont le cœur du produit. Le coût est d'écrire et d'opérer plus de code.

**Option B.** Elle accélère le MVP, car l'auth et la RLS sont fournies. En contrepartie, le projet se couple à la plateforme, et la logique métier critique (vente atomique, stock) finit dans des fonctions SQL ou des edge functions, plus difficiles à tester. À choisir seulement si le temps de développement est très contraint.

---

## 2. Architecture retenue

```
        Smartphone Android (PWA React + TypeScript)
        IndexedDB (Dexie) · outbox hors ligne · service worker
                            │
                            │ HTTPS · REST + SSE
                            ▼
              ┌──────────────────────────────┐
              │   API FastAPI (stateless)    │
              │                              │
              │  identity · catalog          │
              │  inventory · sales           │
              │  finance · insights          │
              │  copilot (outils + LLM)      │
              └───────┬──────────────┬───────┘
                      │              │
                      ▼              ▼
            ┌────────────────┐   ┌────────────────┐
            │  PostgreSQL    │   │  Fournisseur   │
            │  managé        │   │  de LLM        │
            │  RLS + backups │   │  (abstrait)    │
            └────────┬───────┘   └────────────────┘
                     ▲
                     │
            ┌────────┴───────┐
            │ Worker         │
            │ alertes,       │
            │ rapports       │
            └────────────────┘
```

### Les cinq choix structurants

1. **Un seul déploiement, des modules étanches.** Un module appelle le service d'un autre, jamais ses tables.
2. **Isolation par entreprise en deux couches** : filtrage structurel dans les repositories, et Row Level Security PostgreSQL derrière.
3. **Stock = journal de mouvements**, avec un cache `stock_levels` mis à jour dans la même transaction que la vente.
4. **Hors ligne limité aux opérations en ajout seul** (ventes, dépenses, entrées de stock), avec des identifiants générés côté client. Pas de conflits à résoudre.
5. **Copilot par outils en lecture seule**, avec `company_id` injecté par le serveur, quotas vérifiés avant chaque appel, et fournisseur de LLM derrière une interface.

---

## 3. Stack technique

| Couche | Choix |
|---|---|
| Frontend | React, TypeScript, Vite, TanStack Query, Dexie, plugin PWA de Vite |
| Client d'API | Généré depuis l'OpenAPI de FastAPI |
| Backend | FastAPI, Pydantic, SQLAlchemy, Alembic |
| Base de données | PostgreSQL managé, sauvegardes et restauration à un instant donné |
| Authentification | JWT émis par FastAPI, connexion par OTP SMS |
| Tâches planifiées | Processus worker séparé, verrous PostgreSQL |
| Observabilité | Sentry, logs structurés, suivi du coût IA par entreprise |
| Déploiement | Conteneurs Docker sur un PaaS ou une petite VM, staging + production |
| Hébergement | Région européenne (ex. Paris) pour la latence depuis Abidjan |

---

## 4. Backend : monolithe modulaire

Un seul déploiement, mais des modules aux frontières nettes :

```
identity     utilisateurs, entreprises, rôles
catalog      produits
inventory    mouvements et niveaux de stock
sales        ventes et lignes de vente
finance      dépenses
insights     alertes, rapports, dashboard
copilot      outils, prompts, appels LLM
```

Chaque module suit le même schéma :

```
router  →  service  →  repository
```

**Règle d'étanchéité :** un module n'accède jamais aux tables d'un autre. Il appelle son service. Par exemple, `sales` appelle `inventory.record_movement()` dans la même transaction, mais n'écrit pas lui-même dans `stock_movements`. Si un module doit un jour être extrait, la couture existe déjà.

---

## 5. Multi-tenant : deux lignes de défense

### Première ligne : applicative

- Le `company_id` provient du JWT, **jamais du corps de la requête**.
- Une dépendance FastAPI construit un `TenantContext(company_id, user_id, role)`.
- Le repository de base refuse de s'instancier sans ce contexte : il est impossible d'écrire une requête non filtrée par accident.

### Seconde ligne : base de données

- Row Level Security activée sur toutes les tables métier.
- À chaque transaction, le backend exécute `SET LOCAL app.company_id = ...`, et les policies filtrent sur cette valeur.
- Si un bug applicatif oublie le filtre, la base bloque quand même l'accès.

### Intégrité entre tables

Utiliser des **clés étrangères composites** `(company_id, product_id)`. Sans cela, rien n'empêche techniquement une vente d'une entreprise A de référencer un produit d'une entreprise B.

### Tests

Une batterie de tests dédiée vérifie qu'un utilisateur ne peut jamais lire ni modifier les données d'une autre entreprise. Ces tests s'écrivent dès le premier jour et font partie de la CI.

---

## 6. Transaction de vente

Dans **une seule transaction PostgreSQL** :

1. insertion de `sale` et de ses `sale_items`, avec prix de vente et coût **figés** au moment de la vente ;
2. insertion des `stock_movements` (source de vérité) ;
3. mise à jour atomique de `stock_levels` (`UPDATE ... SET qty = qty - x`), pour lire le stock sans faire un `SUM` sur tout le journal.

### Décisions associées

- **Idempotence.** Chaque vente porte un `client_id` (UUID généré sur l'appareil) avec une contrainte unique `(company_id, client_id)`. Un renvoi après coupure réseau ne crée jamais de doublon.
- **Stock négatif autorisé, avec alerte.** Un commerçant vend souvent avant d'avoir saisi son réapprovisionnement. Bloquer la vente parce que le stock affiche 0 ferait abandonner l'outil. Mieux vaut vendre et signaler l'écart.
- **Montants en entiers** (le FCFA n'a pas de centimes en pratique), jamais en flottants.
- **Méthode de coût** (coût moyen pondéré ou dernier prix d'achat) : à trancher avant d'implémenter les ventes.

---

## 7. Mode hors ligne

Le piège serait de vouloir tout synchroniser dans les deux sens. La solution réaliste est de limiter le hors-ligne aux **opérations en ajout seul** : ventes, dépenses, entrées de stock. Ces opérations sont **commutatives** (l'ordre d'arrivée ne change pas le résultat final), donc il n'y a pas de conflits à résoudre.

La modification d'un produit ou d'un prix reste réservée au mode connecté.

### Mise en œuvre

- **IndexedDB via Dexie** pour le cache de lecture et une **outbox** (file d'opérations en attente).
- **Service worker** (plugin PWA de Vite, basé sur Workbox) pour le shell applicatif.
- Un endpoint `POST /sync` qui accepte un lot d'opérations idempotentes et renvoie le statut de chacune.
- Le serveur reste l'autorité.
- L'interface affiche clairement l'état : « 3 ventes en attente d'envoi ».

### Limites de la V1

Un appareil principal par boutique. Le cas de plusieurs appareils vendant hors ligne en parallèle sur le même stock est géré par le stock négatif autorisé et les alertes d'écart, pas par une résolution de conflits.

---

## 8. Alertes, rapports et tâches planifiées

- **Ne pas lancer de scheduler dans le processus API** : avec deux instances, chaque alerte serait calculée deux fois.
- Utiliser un **processus worker séparé** (même code, autre commande de démarrage).
- Protéger les exécutions par un **verrou** (advisory lock PostgreSQL).
- Rendre les alertes **idempotentes** via une clé de déduplication, par exemple `low_stock:{product_id}:{date}`.
- Générer les rapports lourds de façon **asynchrone**, avec notification à la fin.

Les alertes sont détectées par des **règles déterministes**, pas par l'IA. L'IA peut ensuite les reformuler ou les contextualiser.

### Analytique

PostgreSQL suffit largement : index sur `(company_id, date)`, puis une table d'agrégats journaliers quand les volumes grossissent. Pas d'entrepôt de données à ce stade.

---

## 9. Copilot IA : pipeline technique

```
Question utilisateur
        ↓
Contrôle du quota (avant tout appel LLM)
        ↓
Le LLM choisit un outil prédéfini
        ↓
Le backend exécute une requête paramétrée,
filtrée par company_id injecté par le serveur
        ↓
Résultat chiffré renvoyé au LLM
        ↓
Le LLM explique et recommande, en streaming (SSE)
        ↓
Journalisation des tokens consommés
```

### Règles

- **Registre d'outils** : chaque outil est une fonction Python avec un schéma Pydantic, en **lecture seule**.
- Le `company_id` est **injecté par le serveur**, jamais fourni par le modèle.
- **Limites dures** : nombre maximal d'appels d'outils par question, timeout, plafond de tokens.
- **Injection de prompt indirecte** : un nom de produit ou une note saisie par un utilisateur est du texte non fiable qui revient dans le contexte du modèle. Les résultats d'outils sont traités comme des données, et les outils restent en lecture seule, ce qui limite le risque.
- Une interface `LLMProvider` permet de changer de fournisseur sans toucher au reste du code.
- Le **streaming** de la réponse améliore nettement la perception de rapidité sur un réseau lent.

### Outils prévus

```
get_sales_summary(période)
top_products_by_margin(période, limite)
low_stock_items()
expenses_by_category(période)
compare_periods(période_a, période_b)
```

### Suivi des coûts

Le coût IA par entreprise est mesuré dès le début. Il permet de vérifier que le prix de l'offre Premium couvre le coût réel du Copilot.

---

## 10. Frontend

- React et TypeScript.
- **TanStack Query** pour l'état serveur.
- **Client d'API généré** depuis l'OpenAPI de FastAPI : les types front et back restent synchronisés.
- **Budget de performance** : par exemple un bundle initial compressé inférieur à environ 200 Ko, avec découpage par route. Les utilisateurs ont souvent des Android d'entrée de gamme.
- Interface pensée pour la saisie rapide : produits favoris, grands boutons, peu de champs.

---

## 11. Authentification

**Recommandation :** JWT géré par FastAPI (jeton d'accès court, jeton de rafraîchissement) avec connexion par **OTP SMS**.

- Les commerçants s'identifient plus naturellement par numéro de téléphone que par e-mail.
- Une seule source d'identité : le `company_id` et le rôle vivent directement dans les tables du projet.
- Limitation de débit sur l'envoi d'OTP, qui a un coût réel en SMS.

**Compromis :** la responsabilité de la sécurité repose sur le projet. Utiliser des bibliothèques éprouvées et ne rien inventer.

**Alternative :** Supabase Auth, qui réduit le code à écrire mais lie le projet à leur modèle de données et d'authentification.

---

## 12. Déploiement et observabilité

- Conteneurs Docker, sur un PaaS ou une petite VM.
- PostgreSQL managé, avec **sauvegardes et restauration à un instant donné**, restauration testée.
- Deux environnements dès le début : **staging** et **production**.
- **Sentry** pour les erreurs.
- **Logs structurés** avec un identifiant de requête.
- **Tableau de bord interne** suivant le coût IA par entreprise.

---

## 13. Ce que l'on ne fait pas

- Pas de Kubernetes, ni de microservices, ni de file de messages type Kafka : PostgreSQL et un worker suffisent.
- Pas d'application mobile native au départ : la PWA couvre le besoin.
- Pas de scheduler dans le processus API.
- Pas d'entrepôt de données séparé : index et agrégats journaliers dans PostgreSQL.

---

## 14. Ordre de construction

1. **Auth, entreprise, `TenantContext` et RLS**, avec les tests d'isolation écrits dès le premier jour.
2. **Produits, journal de stock et transaction de vente idempotente.**
3. **Saisie rapide côté PWA**, puis outbox hors ligne.
4. **Dashboard**, puis worker d'alertes.
5. **Copilot**, en dernier, quand des données réelles existent.

---

## 15. Décisions encore ouvertes

| Sujet | Options | Critère |
|---|---|---|
| ~~Option d'architecture~~ | **Décidé : option A (monolithe modulaire FastAPI)** | Contrôle de la transaction de vente, du multi-tenant et du Copilot |
| Authentification | JWT FastAPI + OTP SMS · Supabase · Firebase | Simplicité, coût des SMS, source d'identité unique |
| Méthode de coût | Coût moyen pondéré · dernier prix d'achat | Précision de la marge vs simplicité de saisie |
| Fournisseur de LLM | À comparer | Coût par requête, qualité du français, localisation des données |
| Hébergeur | PaaS ou VM, région européenne | Coût, latence depuis Abidjan, conformité |
| Fournisseur d'OTP SMS | À comparer | Coût par SMS, couverture des opérateurs ivoiriens |
| Périmètre hors ligne V1 | Lecture seule · ventes · complet | Besoin réel constaté sur le terrain |
