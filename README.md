# Data Contracts Framework

Ce projet fournit un cadre pour la gestion des contrats de données, en mettant l'accent sur la qualité, la gouvernance et l'évolution des données dans un environnement Data Mesh.

## Introduction

Les data contracts sont des accords formels qui définissent les attentes et les responsabilités autour des données. Ils garantissent la qualité et la fiabilité des données à chaque étape de leur cycle de vie.

## Installation

Pour installer les dépendances nécessaires, exécutez :

```bash
pip install -r requirements.txt
```

## Utilisation

Pour démarrer l'application Streamlit, utilisez la commande suivante :

```bash
streamlit run streamlit/app.py
```

## Exemples de Contrats

- [Contrat de base pour les événements clients](contracts/customer-domain/customer_events.yaml)
- [Contrat avancé pour les profils clients](contracts/customer-domain/customer_profile_events.yaml)
- [Contrat pour les profils clients](contracts/customer-domain/customer_profile.yaml)
- [Contrat pour les événements de commande](contracts/customer-domain/order_events.yaml)

## Documentation

Consultez les articles pour plus de détails sur la mise en œuvre et la gouvernance des data contracts :

### Français
1. [Fondamentaux des Data Contracts](docs/fr/articles/01-fondamentaux.md)
2. [Structure et Gouvernance](docs/fr/articles/02-structure-et-gouvernance.md)
3. [Workflow et Versioning](docs/fr/articles/03-workflow-et-versioning.md)
4. [Patterns d'Architecture](docs/fr/articles/04-patterns-architecture.md)
5. [Mise en Œuvre Pratique](docs/fr/articles/05-implementation.md)
6. [Cycle de Vie](docs/fr/articles/06-cycle-de-vie.md)
7. [Gouvernance et Adoption](docs/fr/articles/07-gouvernance-et-adoption.md)

### English
1. [Fundamentals of Data Contracts](docs/en/articles/01-fundamentals.md)
2. [Structure and Governance](docs/en/articles/02-structure-and-governance.md)
3. [Workflow and Versioning](docs/en/articles/03-workflow-and-versioning.md)
4. [Architecture Patterns](docs/en/articles/04-architecture-patterns.md)
5. [Implementation](docs/en/articles/05-implementation.md)
6. [Lifecycle](docs/en/articles/06-lifecycle.md)
7. [Governance and Adoption](docs/en/articles/07-governance-and-adoption.md)

## Structure du Projet

```
data-contracts-framework/
├── contracts/                    # Définitions des data contracts
│   ├── customer-domain/         # Contracts liés au domaine client
│   ├── templates/              # Templates de contracts
│   └── shared/                 # Types et définitions partagés
├── sql/                        # Implémentations SQL
│   ├── bronze/                # Couche d'ingestion
│   ├── silver/                # Couche de transformation
│   ├── gold/                  # Couche business
│   └── monitoring/            # Vues de monitoring
├── governance/                 # Outils de gouvernance
│   ├── workflow.py            # Gestion des workflows
│   └── coe_config.yaml        # Configuration du CoE
├── scripts/                    # Scripts utilitaires
│   └── generate_sample_data.py # Générateur de données
├── validation/                 # Tests et validations
│   ├── contract_tests.py      # Tests des contracts
│   └── version_migration.py   # Gestion des migrations
└── docs/                      # Documentation
    └── articles/              # Articles détaillés
```

## Démarrage Rapide

### Démo Docker

La façon la plus simple d'essayer le framework est d'utiliser la démo Docker :

```bash
# Build the image
docker build -t data-contracts-demo .

# Run the container
docker run -p 8501:8501 -p 8000:8000 data-contracts-demo
```

Puis ouvrez : http://localhost:8501

La démo illustre plusieurs concepts clés du framework :

1. **Architecture Médaillon**
   - Couche Bronze : ingestion des événements clients bruts (100 000 événements)
   - Couche Silver : 
     * Version 1 : normalisation basique des événements
     * Version 2 : enrichissement avec géolocalisation et segmentation
   - Couche Gold : vue métier consolidée avec profils clients et métriques d'engagement

2. **Contract Registry Pattern**
   - API REST (port 8000) exposant les endpoints :
     * GET /contracts/{contract_id} : récupération d'un contrat
     * GET /contracts/{contract_id}/versions : historique des versions
     * GET /contracts/{contract_id}/dependencies : dépendances du contrat
   - Interface Streamlit permettant de :
     * Visualiser la structure des contrats
     * Comparer les versions (diff visuel)
     * Explorer les dépendances entre contrats

3. **Circuit Breaker Pattern**
   - Démonstration interactive avec :
     * Taux d'échec configurable (40% par défaut)
     * Seuil de déclenchement : 3 échecs consécutifs
     * Période de reset : 60 secondes
   - États observables :
     * Fermé : fonctionnement normal
     * Ouvert : arrêt temporaire après échecs
     * Semi-ouvert : phase de test de reprise
   - Métriques en temps réel :
     * Nombre d'échecs
     * Temps depuis le dernier échec
     * État actuel du circuit

4. **Monitoring Proactif**
   - Métriques opérationnelles :
     * Latence (ms) des requêtes
     * Taux d'erreur (%)
     * Débit (requêtes/sec)
   - Visualisations temps réel :
     * Graphiques d'évolution
     * Seuils d'alerte
     * Tendances
   - Simulation de scénarios :
     * Dégradation progressive
     * Pics de charge
     * Récupération après incident

Fonctionnalités de la Démo :
- Exploration des données à travers les couches (Bronze → Silver → Gold)
- Comparaison des versions de schémas
- Métriques et visualisations interactives
- Tests de résilience avec le Circuit Breaker
- Monitoring des performances en temps réel

### Installation Manuelle

1. Installation:
```bash
git clone https://github.com/pybonnefoy/data-contracts-framework.git
cd data-contracts-framework
python -m venv venv
source venv/bin/activate  # or venv\Scripts\activate on Windows
pip install -r requirements.txt
```

2. Générer des données de test:
```bash
python scripts/generate_sample_data.py
```

3. Explorez les exemples de contrats dans `contracts/`

4. Lisez la documentation:
   - Commencez par [Fondamentaux](docs/fr/articles/01-fondamentaux.md)
   - Consultez les exemples d'implémentation dans `contracts/` et `sql/`
   - Suivez les tutoriels dans la documentation

## Gouvernance

Le framework inclut une structure de gouvernance complète :

- Rôles et responsabilités définis
- Workflows de validation
- Métriques de suivi
- Programme de formation

Consultez [Gouvernance et Adoption](docs/fr/articles/07-gouvernance-et-adoption.md) pour plus de détails.

## Tests

```bash
# Exécuter les tests
python -m pytest validation/

# Vérifier la compatibilité des contracts
python validation/version_migration.py check
```

## Monitoring

Le framework inclut des vues SQL pour le monitoring :
- Utilisation des contracts
- Qualité des données
- Métriques de performance
- Alertes de versioning

## Auteur

Pierre-Yves Bonnefoy - Olexya

## Remerciements

Je tiens à remercier chaleureusement tous les contributeurs et relecteurs qui m'ont accompagné dans l'écriture de cette série d'articles :

- Stéphane Heckel
- Jean-Georges Perrin
- Gaëlle SERET
- Najate BOUAD

Leurs précieux retours, suggestions et expertises ont été déterminants dans la qualité finale de ce travail.