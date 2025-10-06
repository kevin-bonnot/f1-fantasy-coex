# F1 Fantasy Backend Roadmap

## Vision

Construire un écosystème backend modulaire pour une application Fantasy F1. À terme il comprendra :

1. **API .NET 9 Web** – point d'entrée public pour les apps web/mobile.
2. **Back-office Blazor (.NET 9)** – interface d'administration et tâches de gestion de données.
3. **FastAPI Python** – collecte et normalisation des données FastF1.
4. **Base PostgreSQL** – stockage des résultats, des entités Fantasy et des journaux d'import.

## Architecture cible (v1)

```
FastF1 -> FastAPI (Python) -> PostgreSQL <- .NET 9 Web API
                                       ^
                                       |
                                 Blazor Admin
```

* La FastAPI fournit des endpoints `/results`, `/sessions`, `/drivers`, etc. consumés par le back-office.
* Le back-office Blazor offre des actions pour lancer des imports et gérer le référentiel (écuries, pilotes, circuits, utilisateurs fantasy).
* L'API Web .NET expose des endpoints publics (lecture seule dans un premier temps) adaptés aux applications clientes.

## Étapes de mise en place

1. **Initialisation des projets**
   - Créer une solution .NET (`src/dotnet/F1Fantasy.sln`) contenant deux projets :
     - `F1Fantasy.Api` (ASP.NET Core Minimal API)
     - `F1Fantasy.BackOffice` (Blazor Server)
   - Créer l'environnement Python (`src/python/fastf1-service`).
   - Définir les fichiers de configuration (`.editorconfig`, `.gitignore`, `Directory.Build.props`).

2. **Modélisation des données**
   - Schéma PostgreSQL initial :
     - `races`, `sessions`, `drivers`, `teams`, `results`, `lap_times`, `fantasy_users`, `fantasy_lineups`, `imports`.
   - Utiliser EF Core dans l'API .NET et SQLAlchemy dans FastAPI pour partager la source de vérité.

3. **Services d'import**
   - FastAPI expose un endpoint `/ingest/latest` qui déclenche la récupération FastF1 (qualification + course).
   - Back-office consomme cet endpoint et persiste les données via l'API .NET ou directement via EF Core (selon stratégie retenue).

4. **API publique (phase 1)**
   - Endpoints read-only :
     - `GET /races`, `GET /races/{id}`, `GET /races/{id}/results`
     - `GET /drivers`, `GET /drivers/{id}`
   - Authentification à prévoir (API Keys / OAuth) mais non prioritaire pour MVP.

5. **Observabilité & Qualité**
   - Tests unitaires (xUnit pour .NET, pytest pour FastAPI).
   - Documentation OpenAPI pour les deux APIs.
   - Monitoring : logs structurés + métriques Prometheus (optionnel).

## Outils & dépendances

| Composant | Langage | Principales libs |
|-----------|---------|------------------|
| API publique | .NET 9 | ASP.NET Core Minimal API, EF Core, Npgsql |
| Back-office | .NET 9 | Blazor Server, MudBlazor (UI), Identity (auth) |
| Service FastAPI | Python 3.12 | FastAPI, fastf1, SQLAlchemy, Pydantic |
| Base de données | PostgreSQL 16 | Extensions `uuid-ossp`, `pgcrypto` |

## Flux de développement

1. **Base de données** : décrire le schéma via EF Core Migrations + Alembic (optionnel) pour garder la parité.
2. **CI/CD** : GitHub Actions avec jobs séparés (.NET, Python) et vérifications de lint/test.
3. **Conteneurisation** : docker-compose pour orchestrer Postgres + services.

## Prochaines actions

- [ ] Initialiser la solution .NET avec les deux projets.
- [ ] Générer la structure FastAPI et configurer FastF1.
- [ ] Ajouter docker-compose (Postgres + adminer + services).
- [ ] Définir un modèle de données partagé et écrire les premières migrations.

