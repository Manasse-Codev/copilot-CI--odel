# Ashley

### L'assistant de gestion des petits commerces : vendre, suivre son stock, comprendre son activité.

> **Les données de votre entreprise. L'intelligence pour mieux décider.**

**Statut :** conception / pré-MVP · **Type :** PWA (application web installable, mobile-first) · **Cible :** commerçants, TPE et PME · **Marché initial :** Côte d'Ivoire 🇨🇮

---

## Table des matières

1. [Présentation](#1-présentation)
2. [Problème et hypothèses à valider](#2-problème-et-hypothèses-à-valider)
3. [Principes de conception](#3-principes-de-conception)
4. [Fonctionnalités](#4-fonctionnalités)
5. [Copilot IA](#5-copilot-ia)
6. [Architecture](#6-architecture)
7. [Stack technique](#7-stack-technique)
8. [Modèle de données](#8-modèle-de-données)
9. [Sécurité et conformité](#9-sécurité-et-conformité)
10. [Mode hors ligne](#10-mode-hors-ligne)
11. [API](#11-api)
12. [Structure du projet](#12-structure-du-projet)
13. [Installation et lancement](#13-installation-et-lancement)
14. [Tests](#14-tests)
15. [Déploiement et CI/CD](#15-déploiement-et-cicd)
16. [Roadmap](#16-roadmap)
17. [Risques](#17-risques)
18. [Modèle économique](#18-modèle-économique)
19. [Décisions ouvertes](#19-décisions-ouvertes)
20. [Contribution](#20-contribution)
21. [Licence](#21-licence)

---

## 1. Présentation

**Ashley** permet à un commerçant de remplacer son cahier par un outil simple, utilisable sur smartphone, qui centralise :

- les produits ;
- les stocks ;
- les ventes ;
- les dépenses ;
- les indicateurs de performance (chiffre d'affaires, marge, bénéfice estimé).

Au-dessus de ces données, Ashley ajoute progressivement :

- des **alertes** (stock faible, baisse des ventes, dépense inhabituelle) ;
- des **rapports** ;
- un **Copilot IA** qui répond en langage naturel à partir des données réelles de l'entreprise.

Trajectoire : `cahier → numérique → données → analyse → décision`.

---

## 2. Problème et hypothèses à valider

Beaucoup de petits commerces :

- enregistrent leurs ventes à la main ;
- suivent leur stock sur papier ou de mémoire ;
- ne connaissent pas leur marge réelle ;
- n'ont pas de vision claire de leurs dépenses ;
- découvrent les ruptures de stock trop tard.

**Hypothèses à valider avec de vrais commerçants avant d'investir dans l'IA :**

| # | Hypothèse | Comment la valider |
|---|-----------|--------------------|
| H1 | Un commerçant accepte d'enregistrer ses ventes dans l'outil s'il le fait en quelques secondes | Test terrain avec 5 à 10 commerçants |
| H2 | Le suivi du stock et les alertes apportent une valeur perçue sans IA | Retours des pilotes sur la phase 1 |
| H3 | Les commerçants sont prêts à payer 2 500 à 5 000 FCFA/mois pour des analyses avancées | Entretiens et pilote payant |
| H4 | Le coût de l'IA par utilisateur reste inférieur au prix de l'offre Premium | Mesure du coût réel par requête |

> **Le risque n°1 du projet n'est pas technique : c'est l'adoption.** Si saisir une vente est plus lent que d'écrire dans un cahier, l'outil sera abandonné, quelle que soit la qualité de l'IA.

---

## 3. Principes de conception

1. **Saisie ultra-rapide d'abord.** Enregistrer une vente doit prendre quelques secondes, avec peu de champs, des produits favoris en accès direct et des boutons larges.
2. **Mobile-first.** Les utilisateurs sont principalement sur smartphone Android, avec un réseau parfois instable et des données mobiles coûteuses.
3. **Valeur dès le premier jour.** Le tableau de bord et les alertes doivent être utiles sans IA. Le Copilot devient pertinent une fois les données accumulées.
4. **Les chiffres sont calculés par le code, jamais par l'IA.** L'IA explique et recommande ; elle ne calcule pas.
5. **Isolation stricte entre entreprises.** Aucune donnée ne doit pouvoir fuiter d'un commerçant à un autre.
6. **Monolithe simple.** Un seul backend, modulaire. Pas de microservices à ce stade.

---

## 4. Fonctionnalités

### 4.1 Tableau de bord
Chiffre d'affaires, marge, dépenses, bénéfice estimé, nombre de ventes, produits les plus vendus, état des stocks, alertes actives.

### 4.2 Produits
Nom, catégorie, prix d'achat, prix de vente, seuil d'alerte, statut. Création, modification, archivage, recherche, filtres. Une liste de produits favoris accélère la saisie des ventes.

### 4.3 Stocks
Le stock est **déduit d'un journal de mouvements** et non d'une simple colonne modifiée à la main :

| Type de mouvement | Exemple |
|-------------------|---------|
| Entrée | Réapprovisionnement |
| Vente | Décrémenté automatiquement |
| Ajustement | Correction après inventaire |
| Perte | Casse, péremption, vol |

Ce journal permet d'auditer les écarts et de corriger les erreurs sans perdre l'historique.

### 4.4 Ventes
Une vente est une **transaction atomique** : création de la vente, des lignes, des mouvements de stock, le tout ensemble ou rien.

Chaque ligne de vente enregistre **le prix de vente et le coût au moment de la vente**. Ainsi, modifier un prix plus tard ne fausse pas l'historique des marges.

### 4.5 Dépenses
Achats, transport, salaires, communication, charges, autres. Intégrées au calcul du bénéfice estimé.

### 4.6 Alertes (basées sur des règles)
Les alertes sont détectées par des **règles déterministes** exécutées par des tâches planifiées, pas par l'IA :

- stock faible / critique ;
- baisse significative des ventes par rapport à la période précédente ;
- dépense inhabituelle par rapport à la moyenne ;
- produit particulièrement performant.

L'IA peut ensuite reformuler ou contextualiser une alerte, mais ne la détecte pas.

### 4.7 Rapports
Synthèses des ventes, dépenses, marges, stocks et performances mensuelles, consultables et exportables.

---

## 5. Copilot IA

Le Copilot permet d'interroger ses données en langage naturel :

> « Quel est mon produit le plus rentable ? »
> « Quels produits dois-je réapprovisionner ? »
> « Combien ai-je vendu ce mois-ci ? »
> « Pourquoi mes ventes ont-elles baissé cette semaine ? »

### Principe : appel d'outils (function calling)

Le modèle n'a **pas** accès à la base de données et n'écrit **pas** de SQL libre.

```
Question utilisateur
        ↓
Le LLM choisit un outil prédéfini
        ↓
Le backend exécute une requête paramétrée,
filtrée par company_id
        ↓
Résultat chiffré renvoyé au LLM
        ↓
Le LLM explique et recommande en langage naturel
```

Exemples d'outils :

- `get_sales_summary(période)`
- `top_products_by_margin(période, limite)`
- `low_stock_items()`
- `expenses_by_category(période)`
- `compare_periods(période_a, période_b)`

**Avantages :** montants exacts (calculés par le code), pas d'hallucination sur les chiffres, pas de fuite entre entreprises, pas de risque d'injection SQL.

### Maîtrise des coûts

Chaque requête IA a un coût. Il faut :

- des **quotas par offre** (par jour ou par mois) ;
- du **cache** pour les questions répétées ;
- un **modèle léger** pour les demandes simples ;
- un **suivi du coût réel par utilisateur**, à comparer au prix de l'abonnement (hypothèse H4).

### Garde-fous

- Les données de l'entreprise ne sont envoyées au fournisseur d'IA que sous forme de résultats agrégés nécessaires à la réponse.
- Le Copilot indique clairement qu'il fournit une aide à la décision et non un conseil comptable ou financier.
- Les conversations sont journalisées (`AIConversation`, `AIMessage`) pour le suivi de qualité et de coût.

---

## 6. Architecture

Monolithe modulaire client-serveur.

```
┌────────────────────────────────────────────┐
│  Navigateur / smartphone (PWA)             │
│  React + TypeScript                        │
│  Cache local + file d'attente hors ligne   │
└─────────────────────┬──────────────────────┘
                      │ HTTPS · REST (JSON)
                      ▼
┌────────────────────────────────────────────┐
│  Backend FastAPI (Python)                  │
│  Auth · Companies · Products · Stocks      │
│  Sales · Expenses · Alerts · Reports       │
│  Analytics · Copilot (outils + LLM)        │
└───────────┬─────────────────┬──────────────┘
            │ SQL / ORM       │ HTTPS
            ▼                 ▼
┌──────────────────┐   ┌─────────────────────┐
│   PostgreSQL     │   │  API de LLM         │
│   (multi-tenant) │   │  (fournisseur tiers)│
└──────────────────┘   └─────────────────────┘
            ▲
            │
┌──────────────────────────────────────────┐
│  Tâches planifiées                       │
│  Calcul des alertes, rapports            │
└──────────────────────────────────────────┘
```

### Multi-tenant

Chaque table métier porte un `company_id`. Le filtrage est **imposé structurellement**, jamais laissé à la discipline de chaque endpoint :

- une couche d'accès aux données (repository) qui injecte toujours `company_id` ;
- et/ou **Row Level Security** PostgreSQL ;
- des tests automatisés vérifiant qu'un utilisateur ne peut jamais lire ou modifier les données d'une autre entreprise.

---

## 7. Stack technique

| Couche | Choix | Remarque |
|--------|-------|----------|
| Frontend | React, **TypeScript**, Vite, React Router, Tailwind CSS | TypeScript retenu pour la fiabilité à long terme |
| Mobile | **PWA** (service worker, manifeste, installable) | Application native envisagée plus tard seulement si nécessaire |
| Backend | Python, FastAPI, Pydantic, SQLAlchemy, Alembic | Monolithe modulaire |
| Base de données | PostgreSQL | Migrations versionnées |
| Authentification | **À trancher** (voir [§19](#19-décisions-ouvertes)) | Éviter deux sources d'identité non synchronisées |
| IA | API de LLM avec appel d'outils | Fournisseur à choisir selon coût, qualité, localisation des données |
| Tâches planifiées | Worker léger (ex. APScheduler ou équivalent) | Alertes et rapports |
| Hébergement | Cloud, **région européenne** (ex. Paris) | Meilleure latence depuis Abidjan |
| Versioning / CI | Git, GitHub, GitHub Actions | |

---

## 8. Modèle de données

Entités principales :

```
Company ─┬─ User (rôle : propriétaire, gérant, vendeur)
         ├─ Product
         ├─ StockMovement
         ├─ Sale ── SaleItem
         ├─ Expense
         ├─ Alert
         ├─ Report
         └─ AIConversation ── AIMessage
```

Règles importantes :

- **Toutes** les tables métier contiennent `company_id`.
- `StockMovement` : `product_id`, `type` (entrée, vente, ajustement, perte), `quantité`, `date`, `auteur`, `référence` (vente liée le cas échéant).
- `SaleItem` : `product_id`, `quantité`, **`prix_vente_unitaire`**, **`coût_unitaire`** (valeurs figées au moment de la vente).
- Les montants sont stockés en **entiers (FCFA, sans décimales)** ou en type décimal exact, jamais en flottants.
- Les suppressions de produits sont logiques (archivage) pour préserver l'historique.
- Les ventes saisies hors ligne portent un **identifiant unique généré côté client** pour éviter les doublons à la synchronisation.

**Méthode de coût (à trancher) :** coût moyen pondéré ou dernier prix d'achat. Ce choix détermine le calcul de la marge et doit être fait avant d'implémenter les ventes.

---

## 9. Sécurité et conformité

### Sécurité applicative

- authentification sécurisée et gestion des rôles ;
- contrôle d'accès par entreprise et par rôle sur chaque endpoint ;
- validation stricte des entrées (Pydantic) ;
- HTTPS obligatoire ;
- limitation de débit sur les endpoints sensibles et sur le Copilot ;
- journalisation des opérations sensibles ;
- sauvegardes régulières de la base, avec test de restauration ;
- mise à jour régulière des dépendances ;
- aucun secret dans le code : variables d'environnement ou gestionnaire de secrets ;
- aucun mot de passe en clair.

### Données personnelles et conformité

- Vérifier les obligations applicables auprès de l'**ARTCI** (protection des données personnelles en Côte d'Ivoire), notamment si des données de clients finaux sont enregistrées.
- Identifier où le fournisseur d'IA traite et conserve les données, et choisir un fournisseur dont les conditions sont compatibles.
- Prévoir l'export et la suppression des données d'une entreprise à sa demande.

---

## 10. Mode hors ligne

Les commerçants travaillent souvent avec une connexion instable. La PWA doit permettre de **continuer à vendre sans réseau**.

Approche minimale pour le MVP :

- cache local des produits et du stock pour la lecture ;
- **file d'attente locale** des ventes saisies hors ligne ;
- synchronisation automatique au retour du réseau ;
- identifiants uniques générés côté client pour l'idempotence ;
- indicateur visible de l'état de synchronisation.

Points d'attention : conflits de stock quand plusieurs appareils vendent hors ligne, ordre de synchronisation, ajustements d'inventaire concurrents. Ce chantier est le plus complexe du projet et doit être cadré dès la conception, même si la première version reste simple (un appareil principal par boutique).

---

## 11. API

API REST sous `/api` :

```
/api/auth
/api/companies
/api/users
/api/products
/api/stocks          (mouvements et niveaux)
/api/sales
/api/expenses
/api/alerts
/api/reports
/api/dashboard
/api/ai/chat         (Copilot)
```

La documentation interactive est générée automatiquement par FastAPI (`/docs`). L'API est versionnée (`/api/v1/...`) dès le départ pour permettre des évolutions sans casser les clients installés.

---

## 12. Structure du projet

```
Ashley/
├── frontend/
│   ├── public/                 # manifeste PWA, icônes
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── hooks/
│   │   ├── services/           # appels API
│   │   ├── offline/            # cache local, file d'attente, sync
│   │   ├── stores/
│   │   ├── utils/
│   │   └── main.tsx
│   └── package.json
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/               # config, sécurité, multi-tenant
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── services/
│   │   ├── repositories/       # accès données filtré par company_id
│   │   ├── jobs/               # alertes, rapports planifiés
│   │   ├── copilot/            # outils, prompts, appels LLM
│   │   └── main.py
│   ├── migrations/
│   ├── tests/
│   └── requirements.txt
├── docs/
│   └── Business_Plan_PME_Copilot_CI_2026.md
├── .env.example
├── .gitignore
├── docker-compose.yml
└── README.md
```

---

## 13. Installation et lancement

### Prérequis

Git, Node.js (LTS), npm, Python 3.11+, PostgreSQL. Les versions exactes seront figées dans la documentation technique.

### Cloner

```bash
git clone https://github.com/Manasse-Codev/<nom-du-depot>.git
cd <nom-du-depot>
```

### Configuration

Copier `.env.example` vers `.env` et renseigner les valeurs :

```
DATABASE_URL=postgresql://user:password@localhost:5432/ashley
SECRET_KEY=change_me
AI_API_KEY=change_me
```

> Ne jamais commiter `.env`. Il doit figurer dans `.gitignore`.

### Backend

```bash
cd backend
python -m venv venv
source venv/bin/activate        # Windows : venv\Scripts\activate
pip install -r requirements.txt
alembic upgrade head
uvicorn app.main:app --reload
```

API : `http://localhost:8000` · Documentation : `http://localhost:8000/docs`

### Frontend

```bash
cd frontend
npm install
npm run dev
```

---

## 14. Tests

| Niveau | Contenu |
|--------|---------|
| Backend unitaires | Calcul de marge, mouvements de stock, règles d'alerte |
| Backend intégration | Transaction de vente atomique, migrations |
| **Isolation multi-tenant** | Vérifier qu'un utilisateur ne peut jamais accéder aux données d'une autre entreprise |
| Copilot | Chaque outil renvoie des données filtrées et correctes ; tests sur des questions types |
| Frontend | Composants, saisie rapide d'une vente |
| Hors ligne | Vente sans réseau, synchronisation, absence de doublons |
| Parcours complet | Inscription → entreprise → produit → stock → vente → tableau de bord → question au Copilot |

---

## 15. Déploiement et CI/CD

Pipeline GitHub Actions :

```
Push / Pull Request
   ↓
Lint (frontend + backend)
   ↓
Tests (dont isolation multi-tenant)
   ↓
Build frontend
   ↓
Déploiement staging
   ↓
Validation → déploiement production
```

Exigences minimales avant le premier pilote : environnement de staging, HTTPS, sauvegardes automatiques testées, supervision des erreurs et des coûts IA.

---

## 16. Roadmap

### Phase 0 : Validation terrain
- Entretiens avec 10 commerçants.
- Maquettes de la saisie de vente, testées sur papier ou sur prototype.
- Décisions techniques ouvertes ([§19](#19-décisions-ouvertes)).

### Phase 1 : MVP « vendre et suivre » (sans IA)
- Authentification, entreprise, rôles.
- Produits, journal de stock, ventes avec saisie rapide.
- Dépenses.
- Tableau de bord simple.
- PWA installable, avec saisie hors ligne de base.
- **Pilote avec quelques commerçants réels.**

### Phase 2 : Alertes et rapports
- Alertes par règles (stock, ventes, dépenses).
- Rapports mensuels.
- Notifications.
- Amélioration de l'UX à partir des retours du pilote.

### Phase 3 : Copilot IA
- Outils d'analyse paramétrés et filtrés par entreprise.
- Quotas, cache, suivi des coûts.
- Recommandations de réapprovisionnement et d'analyse de rentabilité.

### Phase 4 : Monétisation
- Intégration du paiement Mobile Money (Orange Money, MTN MoMo, Wave, via un agrégateur).
- Offres Gratuit / Premium / Business.
- Déploiement pilote élargi, monitoring, documentation.

### Phase 5 : Évolution
Fournisseurs, clients, facturation, comptabilité simplifiée, prévisions de ventes, multi-boutiques avancé, application native si justifiée.

---

## 17. Risques

| Risque | Gravité | Mitigation |
|--------|---------|------------|
| **Adoption : la saisie est trop lourde** | Très élevée | Saisie en quelques secondes, tests terrain dès la phase 0 |
| **Réseau instable, smartphones modestes** | Élevée | PWA mobile-first, mode hors ligne, pages légères |
| Fuite de données entre entreprises | Élevée | Filtrage structurel par `company_id`, RLS, tests dédiés |
| Coût de l'IA supérieur au revenu par utilisateur | Moyenne à élevée | Quotas, cache, modèle léger, calcul avant fixation des prix |
| Paiement de l'abonnement | Moyenne | Agrégateur Mobile Money, à sélectionner tôt |
| Conformité données personnelles | Moyenne | Vérification ARTCI, choix du fournisseur d'IA, export/suppression |
| Conflits de synchronisation hors ligne | Moyenne | Identifiants uniques, périmètre limité en V1 |
| Marge faussée par un mauvais modèle de coût | Moyenne | Coût figé par ligne de vente, méthode choisie dès la conception |
| Dépendance à un fournisseur d'IA | Faible à moyenne | Couche d'abstraction autour du LLM |

---

## 18. Modèle économique

**Freemium + abonnement.** Les tarifs sont **indicatifs** et à valider par le pilote (hypothèse H3) et par le calcul du coût réel de l'IA (hypothèse H4).

| Offre | Prix indicatif | Contenu |
|-------|----------------|---------|
| **Gratuit** | 0 | Produits, ventes, stocks de base, tableau de bord limité |
| **Premium** | 2 500 à 5 000 FCFA/mois | Alertes avancées, rapports, Copilot IA (avec quota), historique complet |
| **Business** | 10 000 à 25 000 FCFA/mois | Multi-utilisateurs, multi-boutiques, rapports personnalisés, administration des rôles |

Paiement prévu par Mobile Money. Des services professionnels (mise en place, formation, rapports sur mesure) peuvent compléter l'offre.

---

## 19. Décisions ouvertes

| Sujet | Options | Critère de décision |
|-------|---------|---------------------|
| **Authentification** | JWT géré par FastAPI · Supabase (Postgres + auth) · Firebase Auth (avec mapping UID → `users`) | Simplicité, une seule source d'identité, coût, support OTP par SMS |
| **Méthode de coût** | Coût moyen pondéré · dernier prix d'achat | Précision de la marge vs simplicité de saisie |
| **Fournisseur d'IA** | À comparer | Coût par requête, qualité du français, localisation et conservation des données |
| **Hébergement** | Cloud européen (Paris) | Latence depuis Abidjan, coût, conformité |
| **Agrégateur Mobile Money** | À comparer | Frais, couverture des opérateurs, conditions d'intégration |
| **Périmètre hors ligne V1** | Lecture seule · saisie de ventes · complet | Effort vs besoin réel constaté sur le terrain |
| **Nom du dépôt** | Aligner sur « Ashley » | Cohérence avec le nom du produit |

---

## 20. Contribution

1. Créer une branche dédiée : `git checkout -b feature/nom-de-la-fonctionnalité`
2. Développer et ajouter ou mettre à jour les tests.
3. Vérifier lint et tests en local.
4. Documenter les changements.
5. Ouvrir une Pull Request.

Convention de commits : `feat:`, `fix:`, `docs:`, `refactor:`, `test:`, `chore:`.

Toute modification touchant l'accès aux données doit être accompagnée d'un test d'isolation entre entreprises.

---

## 21. Licence

Projet **propriétaire** en attendant une décision des responsables du projet. Le code ne doit pas être redistribué sans autorisation.

---

**Ashley** transforme les données de l'entreprise en informations compréhensibles, puis en recommandations utiles à la décision.
