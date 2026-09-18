# Ashley

### La plateforme web intelligente pour aider les petites entreprises à mieux gérer et décider

> **Les données de votre entreprise. L'intelligence pour mieux décider.**

---

## 📋 Table des matières

* [1. Présentation](#1--présentation)
* [2. Problématique](#2--problématique)
* [3. Solution](#3--solution)
* [4. Objectifs](#4--objectifs)
* [5. Fonctionnalités](#5--fonctionnalités)
* [6. Copilot IA](#6--copilot-ia)
* [7. Architecture](#7--architecture)
* [8. Stack technique](#8--stack-technique)
* [9. Structure du projet](#9--structure-du-projet)
* [10. Modules fonctionnels](#10--modules-fonctionnels)
* [11. Gestion des données](#11--gestion-des-données)
* [12. Authentification et sécurité](#12--authentification-et-sécurité)
* [13. API](#13--api)
* [14. Interface utilisateur](#14--interface-utilisateur)
* [15. Intelligence artificielle](#15--intelligence-artificielle)
* [16. Installation](#16--installation)
* [17. Configuration](#17--configuration)
* [18. Lancement du projet](#18--lancement-du-projet)
* [19. Tests](#19--tests)
* [20. Déploiement](#20--déploiement)
* [21. CI/CD](#21--cicd)
* [22. Roadmap](#22--roadmap)
* [23. Modèle économique](#23--modèle-économique)
* [24. Contribution](#24--contribution)
* [25. Sécurité](#25--sécurité)
* [26. Licence](#26--licence)
* [27. Vision](#27--vision)

---

# 1. 📌 Présentation

**Ashley** est une plateforme Web intelligente destinée aux petites et moyennes entreprises, commerçants et entrepreneurs.

Son objectif est de permettre à une entreprise de **centraliser ses données opérationnelles** et de les transformer progressivement en informations utiles à la prise de décision.

Ashley combine :

* gestion des produits ;
* gestion des stocks ;
* gestion des ventes ;
* gestion des dépenses ;
* indicateurs de performance ;
* alertes ;
* rapports ;
* intelligence artificielle.

Contrairement à un simple logiciel de gestion, Ashley ne se limite pas à enregistrer et afficher les informations.

La plateforme cherche à transformer le cycle :

```text
Données
   ↓
Analyse
   ↓
Compréhension
   ↓
Recommandation
   ↓
Décision
```

Le projet est actuellement développé en priorité sous la forme d'une **application Web responsive**.

Une application mobile native pourra être envisagée ultérieurement.

---

# 2. 🔴 Problématique

De nombreuses petites entreprises rencontrent des difficultés dans la gestion quotidienne de leur activité.

Les principales difficultés identifiées sont notamment :

* gestion manuelle des ventes ;
* suivi des stocks sur papier ou avec des outils peu adaptés ;
* difficulté à calculer précisément les bénéfices ;
* manque de visibilité sur les dépenses ;
* difficulté à analyser la rentabilité des produits ;
* absence d'alertes concernant les stocks critiques ;
* données dispersées dans différents outils ;
* difficulté à transformer les données disponibles en décisions.

Le problème fondamental peut être résumé ainsi :

> **Les entrepreneurs disposent de données sur leur activité, mais ne disposent pas toujours des outils nécessaires pour les comprendre et les exploiter efficacement.**

Ashley cherche donc à fournir une plateforme permettant de centraliser ces informations et de les rendre exploitables.

---

# 3. 💡 Solution

Ashley fournit une interface Web permettant à l'utilisateur de gérer son activité depuis un navigateur.

La plateforme centralise notamment :

```text
Produits
   │
   ├── Stocks
   │
   ├── Ventes
   │
   ├── Dépenses
   │
   └── Performances
          │
          ▼
      Ashley IA
          │
          ├── Analyse
          ├── Alertes
          ├── Recommandations
          └── Rapports
```

L'objectif est de permettre à l'utilisateur de passer progressivement :

**du cahier → au numérique**

puis :

**du numérique → à la donnée**

et enfin :

**de la donnée → à la décision intelligente.**

---

# 4. 🎯 Objectifs

## Objectif principal

Créer une plateforme Web simple permettant aux petites entreprises de mieux gérer leur activité et d'exploiter leurs données.

## Objectifs spécifiques

Ashley doit permettre de :

* centraliser les données de l'entreprise ;
* suivre les produits ;
* suivre les stocks ;
* enregistrer les ventes ;
* suivre les dépenses ;
* calculer les principaux indicateurs ;
* identifier les tendances ;
* détecter certaines anomalies ;
* générer des rapports ;
* utiliser un assistant IA pour interroger les données ;
* recevoir des recommandations compréhensibles.

---

# 5. ⚙️ Fonctionnalités

## 5.1 Tableau de bord

Le tableau de bord fournit une vue synthétique de l'activité.

Il pourra notamment afficher :

* chiffre d'affaires ;
* bénéfices estimés ;
* dépenses ;
* nombre de ventes ;
* produits les plus vendus ;
* évolution des performances ;
* niveau des stocks ;
* alertes importantes.

---

## 5.2 Gestion des produits

L'utilisateur peut gérer les informations relatives à ses produits.

### Informations possibles

* nom ;
* description ;
* catégorie ;
* prix d'achat ;
* prix de vente ;
* quantité disponible ;
* seuil d'alerte ;
* statut.

### Actions

* créer un produit ;
* consulter un produit ;
* modifier un produit ;
* supprimer un produit ;
* rechercher un produit ;
* filtrer les produits.

---

# 5.3 Gestion des stocks

Ashley permet de suivre les quantités disponibles.

Le système doit pouvoir :

* afficher les stocks ;
* enregistrer les mouvements ;
* diminuer automatiquement le stock après une vente ;
* identifier les stocks faibles ;
* identifier les stocks critiques ;
* générer des alertes.

Exemple :

```text
Produit : Riz 25 kg

Stock actuel : 8
Seuil d'alerte : 10

→ Alerte stock faible
```

---

# 5.4 Gestion des ventes

Chaque vente enregistrée doit permettre de mettre à jour automatiquement les données associées.

Processus :

```text
Nouvelle vente
      ↓
Enregistrement transaction
      ↓
Mise à jour du stock
      ↓
Calcul du chiffre d'affaires
      ↓
Calcul de la marge
      ↓
Mise à jour des statistiques
```

Les ventes doivent être historisées afin de permettre leur analyse ultérieure.

---

# 5.5 Gestion des dépenses

Ashley permet d'enregistrer les dépenses de l'entreprise.

Exemples :

* achats ;
* transport ;
* salaires ;
* communication ;
* charges ;
* autres dépenses.

Ces données sont ensuite utilisées dans les indicateurs et analyses.

---

# 5.6 Alertes

Ashley peut signaler différents événements importants.

### Types d'alertes

* stock critique ;
* stock faible ;
* baisse importante des ventes ;
* dépenses inhabituelles ;
* produits performants ;
* autres événements détectés par le système.

Les alertes doivent être compréhensibles et directement exploitables.

---

# 5.7 Rapports

Ashley pourra produire des rapports synthétiques concernant :

* les ventes ;
* les dépenses ;
* les bénéfices ;
* les produits ;
* les stocks ;
* les performances mensuelles.

---

# 6. 🤖 Copilot IA

Le **Copilot IA** constitue l'une des fonctionnalités centrales d'Ashley.

Il permet à l'utilisateur d'interagir avec les données de son entreprise en langage naturel.

### Exemple

L'utilisateur peut demander :

> « Quel est mon produit le plus rentable ? »

Ashley analyse les données disponibles et fournit une réponse compréhensible.

Autres exemples :

> « Pourquoi mes ventes ont-elles diminué cette semaine ? »

> « Quels produits dois-je réapprovisionner ? »

> « Combien ai-je vendu ce mois-ci ? »

> « Quelle est ma marge moyenne ? »

> « Quelles sont mes dépenses les plus importantes ? »

> « Fais-moi un rapport de mon activité. »

---

## Principe

Le Copilot doit fonctionner autour du cycle :

```text
Question utilisateur
        ↓
Compréhension de la demande
        ↓
Identification des données nécessaires
        ↓
Analyse des données
        ↓
Interprétation
        ↓
Réponse
        ↓
Recommandation éventuelle
```

L'IA ne doit pas simplement générer une réponse générique.

Elle doit, lorsque cela est possible, s'appuyer sur les données réelles de l'entreprise.

---

# 7. 🏗️ Architecture

Pour la première version, Ashley adopte une **architecture Web client-serveur**.

```text
┌───────────────────────────────────────────────┐
│                  NAVIGATEUR                   │
│                                               │
│              Ashley Web                       │
│              React                            │
│              UI Responsive                    │
└──────────────────────┬────────────────────────┘
                       │
                       │ HTTPS
                       │ REST API
                       ▼
┌───────────────────────────────────────────────┐
│                   BACKEND                     │
│                                               │
│                  FastAPI                      │
│                  Python                       │
│                                               │
│  Auth │ Users │ Products │ Sales │ Expenses  │
│       │ Stocks │ Reports  │ Analytics │ AI   │
└──────────────────────┬────────────────────────┘
                       │
                       │ SQL / ORM
                       ▼
┌───────────────────────────────────────────────┐
│                  PostgreSQL                   │
│                                               │
│ Users │ Companies │ Products │ Stocks         │
│ Sales │ Expenses │ Alerts │ Reports           │
└───────────────────────────────────────────────┘
                       │
                       │
                       ▼
┌───────────────────────────────────────────────┐
│                  IA LAYER                     │
│                                               │
│       API de modèles d'IA générative         │
│                                               │
│ Analyse │ Assistant │ Recommandations        │
└───────────────────────────────────────────────┘
```

---

# 8. 🛠️ Stack technique

## Frontend

* React
* JavaScript / TypeScript selon l'implémentation
* Tailwind CSS ou solution de styling équivalente
* Vite
* React Router
* gestion d'état selon les besoins

## Backend

* Python
* FastAPI
* Pydantic
* SQLAlchemy ou ORM équivalent
* API REST

## Base de données

* PostgreSQL

## Authentification

* Firebase Authentication ou mécanisme d'authentification sécurisé équivalent.

## Intelligence artificielle

* API de modèles d'IA générative.

## Infrastructure

* Cloud
* Git
* GitHub
* CI/CD

---

# 9. 📁 Structure du projet

Une organisation recommandée est :

```text
Ashley/
│
├── frontend/
│   ├── public/
│   ├── src/
│   │   ├── components/
│   │   ├── pages/
│   │   ├── layouts/
│   │   ├── hooks/
│   │   ├── services/
│   │   ├── stores/
│   │   ├── utils/
│   │   ├── assets/
│   │   ├── App.*
│   │   └── main.*
│   ├── package.json
│   └── README.md
│
├── backend/
│   ├── app/
│   │   ├── api/
│   │   ├── core/
│   │   ├── models/
│   │   ├── schemas/
│   │   ├── services/
│   │   ├── repositories/
│   │   ├── ai/
│   │   └── main.py
│   ├── tests/
│   ├── requirements.txt
│   └── README.md
│
├── database/
│   ├── migrations/
│   └── seeds/
│
├── docs/
│
├── .env.example
├── .gitignore
├── docker-compose.yml
└── README.md
```

Cette structure pourra évoluer avec le développement du projet.

---

# 10. 🧩 Modules fonctionnels

Ashley est organisé autour de plusieurs modules.

```text
Ashley
│
├── Authentification
│
├── Entreprise
│
├── Utilisateurs
│
├── Tableau de bord
│
├── Produits
│
├── Stocks
│
├── Ventes
│
├── Dépenses
│
├── Alertes
│
├── Rapports
│
├── Analytics
│
└── Copilot IA
```

---

# 11. 🗄️ Gestion des données

Les principales entités prévues sont :

```text
User
Company
Product
Stock
Sale
SaleItem
Expense
Alert
Report
AIConversation
AIMessage
```

Une entreprise peut être associée à plusieurs données opérationnelles.

Exemple :

```text
Company
   │
   ├── Products
   ├── Stocks
   ├── Sales
   ├── Expenses
   ├── Alerts
   └── Reports
```

Les relations exactes et le modèle relationnel définitif seront déterminés lors de la conception technique de la base PostgreSQL.

---

# 12. 🔐 Authentification et sécurité

La sécurité constitue une priorité du projet.

Ashley devra notamment intégrer :

* authentification sécurisée ;
* gestion des sessions ;
* contrôle des accès ;
* gestion des rôles ;
* validation des données ;
* chiffrement des communications HTTPS ;
* protection des API ;
* sauvegardes ;
* journalisation des opérations ;
* protection contre les accès non autorisés.

Les secrets et clés API ne doivent jamais être stockés directement dans le code source.

Ils doivent être fournis via des variables d'environnement ou un gestionnaire de secrets adapté.

---

# 13. 🔌 API

Le backend expose une API REST consommée par le frontend.

Exemple d'organisation :

```text
/api
│
├── /auth
├── /users
├── /companies
├── /products
├── /stocks
├── /sales
├── /expenses
├── /alerts
├── /reports
├── /analytics
└── /ai
```

Exemple :

```http
GET /api/products
```

```http
POST /api/products
```

```http
GET /api/sales
```

```http
POST /api/sales
```

```http
GET /api/dashboard
```

```http
POST /api/ai/chat
```

Les endpoints définitifs seront documentés au fur et à mesure de leur implémentation.

---

# 14. 🎨 Interface utilisateur

Ashley doit proposer une interface :

* moderne ;
* simple ;
* responsive ;
* claire ;
* rapide ;
* accessible aux utilisateurs non techniques.

L'expérience utilisateur doit privilégier la simplicité.

## Responsive Web

L'application doit fonctionner correctement sur :

* ordinateur ;
* tablette ;
* smartphone.

```text
Desktop
   ↓
Tablet
   ↓
Mobile
```

L'application reste néanmoins une **application Web** dans la première phase.

---

# 15. 🧠 Intelligence artificielle

L'IA intervient principalement dans l'analyse et l'interprétation des données.

### Cas d'utilisation

#### Analyse des ventes

Identifier :

* tendances ;
* variations ;
* produits performants ;
* périodes importantes.

#### Analyse des stocks

Identifier :

* produits proches de la rupture ;
* besoins potentiels de réapprovisionnement ;
* évolution des stocks.

#### Analyse des dépenses

Identifier :

* catégories de dépenses importantes ;
* variations ;
* dépenses inhabituelles.

#### Recommandations

Le système peut formuler des recommandations à partir des données disponibles.

---

# 16. 🚀 Installation

## Prérequis

Avant de lancer Ashley, installer :

* Git ;
* Node.js ;
* npm ;
* Python ;
* PostgreSQL.

Les versions exactes seront définies dans la documentation technique du projet.

---

## Cloner le projet

```bash
git clone <URL_DU_REPOSITORY>
cd Ashley
```

---

# 17. ⚙️ Configuration

Créer les fichiers `.env` nécessaires à partir de `.env.example`.

Exemple :

```env
DATABASE_URL=postgresql://user:password@localhost:5432/ashley

API_URL=http://localhost:8000

SECRET_KEY=your_secret_key

AI_API_KEY=your_ai_api_key
```

### Important

Ne jamais publier :

```text
.env
```

dans le dépôt Git.

Le fichier `.env` doit être présent dans `.gitignore`.

---

# 18. ▶️ Lancement du projet

## Frontend

```bash
cd frontend
npm install
npm run dev
```

Le frontend sera disponible sur l'adresse indiquée par Vite.

---

## Backend

Créer un environnement virtuel :

```bash
cd backend
python -m venv venv
```

Activer l'environnement.

### Linux / macOS

```bash
source venv/bin/activate
```

### Windows

```bash
venv\Scripts\activate
```

Installer les dépendances :

```bash
pip install -r requirements.txt
```

Lancer FastAPI :

```bash
uvicorn app.main:app --reload
```

Le backend sera généralement accessible sur :

```text
http://localhost:8000
```

La documentation automatique FastAPI pourra être accessible sur :

```text
http://localhost:8000/docs
```

---

# 19. 🧪 Tests

Le projet devra progressivement intégrer plusieurs niveaux de tests.

## Backend

* tests unitaires ;
* tests d'intégration ;
* tests API ;
* tests de sécurité.

## Frontend

* tests des composants ;
* tests des interactions ;
* tests des flux utilisateurs.

## Tests fonctionnels

Les principaux parcours devront être vérifiés :

```text
Inscription
   ↓
Connexion
   ↓
Création entreprise
   ↓
Création produit
   ↓
Stock
   ↓
Vente
   ↓
Mise à jour statistiques
   ↓
Analyse IA
```

---

# 20. ☁️ Déploiement

Ashley est conçu pour être déployé dans le cloud.

Architecture cible :

```text
                    Internet
                       │
                       ▼
                ┌─────────────┐
                │   Frontend  │
                │   React     │
                └──────┬──────┘
                       │
                       ▼
                ┌─────────────┐
                │   FastAPI   │
                │   Backend   │
                └──────┬──────┘
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
       PostgreSQL             IA API
```

Le choix définitif du fournisseur cloud sera effectué lors de la phase de déploiement.

---

# 21. 🔄 CI/CD

Le projet pourra utiliser une pipeline CI/CD pour automatiser :

* vérification du code ;
* tests ;
* linting ;
* build frontend ;
* vérification backend ;
* déploiement.

Exemple :

```text
Push Git
   ↓
CI
   ↓
Lint
   ↓
Tests
   ↓
Build
   ↓
Validation
   ↓
Deploy
```

---

# 22. 🗺️ Roadmap

## Phase 1 — Conception

Objectifs :

* définition des besoins ;
* architecture ;
* conception UX/UI ;
* modèle de données ;
* définition des API.

---

## Phase 2 — MVP Web

Développement :

* authentification ;
* gestion des entreprises ;
* produits ;
* stocks ;
* ventes ;
* dépenses ;
* tableau de bord.

---

## Phase 3 — Intelligence artificielle

Ajout :

* Copilot IA ;
* analyse des données ;
* recommandations ;
* alertes intelligentes ;
* génération de rapports.

---

## Phase 4 — Tests utilisateurs

Objectifs :

* tester avec des utilisateurs ;
* identifier les problèmes UX ;
* recueillir les retours ;
* améliorer la plateforme.

---

## Phase 5 — Déploiement pilote

Actions :

* déploiement cloud ;
* sécurisation ;
* sauvegardes ;
* monitoring ;
* documentation ;
* démonstration.

---

## Phase 6 — Évolution

À terme, Ashley pourra évoluer vers :

* comptabilité simplifiée ;
* prévisions de ventes ;
* gestion des fournisseurs ;
* gestion des clients ;
* facturation ;
* analyse financière ;
* recommandations commerciales ;
* automatisation administrative ;
* intégration de solutions de paiement ;
* application mobile.

---

# 23. 💰 Modèle économique

Le modèle économique envisagé repose sur :

**Freemium + abonnement + services professionnels**

## Offre gratuite

Elle pourra inclure :

* gestion des produits ;
* gestion basique des ventes ;
* gestion basique des stocks ;
* tableau de bord limité.

## Offre Premium

Tarification indicative :

**2 500 à 5 000 FCFA / mois**

Elle pourra inclure :

* Copilot IA ;
* analyses avancées ;
* rapports ;
* alertes intelligentes ;
* historique complet ;
* sauvegarde avancée.

## Offre Business

Tarification indicative :

**10 000 à 25 000 FCFA / mois**

Elle pourra proposer :

* plusieurs utilisateurs ;
* plusieurs boutiques ;
* gestion avancée ;
* rapports personnalisés ;
* statistiques avancées ;
* administration des utilisateurs.

> Les tarifs sont indicatifs et pourront être modifiés après validation du modèle économique.

---

# 24. 🤝 Contribution

Les contributions au projet doivent respecter les conventions techniques définies par l'équipe.

Avant toute modification importante :

1. créer une branche dédiée ;
2. effectuer les changements ;
3. ajouter ou mettre à jour les tests ;
4. vérifier le fonctionnement ;
5. documenter les changements ;
6. créer une Pull Request.

Exemple :

```bash
git checkout -b feature/gestion-produits
```

Puis :

```bash
git add .
git commit -m "feat: ajout gestion des produits"
git push origin feature/gestion-produits
```

---

# 25. 🔒 Sécurité

Toute vulnérabilité découverte doit être traitée avec sérieux.

Les principes suivants doivent être respectés :

* ne jamais exposer les secrets ;
* ne jamais stocker de mots de passe en clair ;
* valider toutes les entrées ;
* contrôler les permissions ;
* protéger les endpoints ;
* utiliser HTTPS en production ;
* limiter les accès aux données ;
* journaliser les opérations sensibles ;
* effectuer des sauvegardes ;
* maintenir les dépendances à jour.

Les données d'une entreprise doivent être isolées de celles des autres entreprises.

Le système devra donc appliquer une logique de séparation stricte des données au niveau de l'architecture et des requêtes.

---

# 26. 📄 Licence

La licence définitive du projet sera déterminée par les responsables d'Ashley.

En attendant cette décision, le projet doit être considéré comme un projet propriétaire et son code ne doit pas être redistribué sans autorisation.

---

# 27. 🌍 Vision

Ashley a pour ambition de rendre les outils numériques et l'intelligence artificielle plus accessibles aux petites entreprises.

La vision du projet repose sur une évolution simple :

```text
        Cahier
           │
           ▼
       Numérique
           │
           ▼
         Données
           │
           ▼
        Analyse
           │
           ▼
   Intelligence artificielle
           │
           ▼
        Décision
```

Ashley ne cherche donc pas uniquement à fournir un outil permettant d'enregistrer les opérations d'une entreprise.

L'ambition est de construire progressivement un **assistant numérique capable d'aider l'entrepreneur à comprendre son activité et à prendre des décisions à partir de ses propres données**.

---

# 🇨🇮 Ashley

### Les données de votre entreprise. L'intelligence pour mieux décider.

**Statut :** Projet en phase de conception / MVP
**Nom :** Ashley
**Type :** Application Web
**Cible :** TPE, PME, commerçants et entrepreneurs
**Marché initial :** Côte d'Ivoire
**Modèle :** Freemium + abonnement + services professionnels

---

## 📌 Résumé technique

| Élément          | Technologie                                              |
| ---------------- | -------------------------------------------------------- |
| Application      | Web                                                      |
| Frontend         | React                                                    |
| Backend          | Python / FastAPI                                         |
| Base de données  | PostgreSQL                                               |
| API              | REST                                                     |
| Authentification | Firebase Authentication / solution sécurisée équivalente |
| IA               | API de modèles d'IA générative                           |
| Infrastructure   | Cloud                                                    |
| Versioning       | Git / GitHub                                             |
| CI/CD            | Pipeline automatisée                                     |

---

> **Ashley transforme les données de l'entreprise en informations compréhensibles, puis en recommandations utiles à la décision.**
