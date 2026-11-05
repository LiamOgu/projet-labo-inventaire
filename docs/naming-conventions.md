# Conventions de nommage – Projet d'inventaire

Stack : **Python / FastAPI** (backend), **TypeScript** (frontend), **PostgreSQL** (base de données), hébergé sur **GitHub**.

Ce document fixe les règles de nommage du projet : dépôt, branches, commits, pull requests, issues, backend, base de données, API, frontend et variables d'environnement. L'objectif est un code cohérent et un historique lisible.

**Règle d'or :** en cas de doute, on suit la convention déjà présente dans le code et on propose toute évolution via une issue, plutôt que de mélanger les styles.

## 1. Langue

- **Code** (variables, fonctions, classes, tables, colonnes, routes) : en **anglais**.
- **Commits, issues, PR, documentation** : une seule langue pour tout le projet, l'anglais
- Le vocabulaire métier est unifié (voir le glossaire de la section 2) : on ne mélange pas `item`, `article` et `product` pour désigner la même chose.

## 2. Glossaire métier

Un terme = un seul nom partout (code, base, API, interface).

| Concept | Nom dans le code | Table | À éviter |
| --- | --- | --- | --- |
| Produit / article | `Product` | `products` | `Item`, `Article` |
| Référence unique | `sku` | `products.sku` | `ref`, `code` |
| Catégorie | `Category` | `categories` | `Type`, `Family` |
| Entrepôt | `Warehouse` | `warehouses` | `Depot`, `Store` |
| Emplacement dans un entrepôt | `Location` | `locations` | `Place`, `Bin` |
| Fournisseur | `Supplier` | `suppliers` | `Vendor` |
| Niveau de stock | `StockLevel` | `stock_levels` | `Inventory` (trop ambigu) |
| Mouvement de stock | `StockMovement` | `stock_movements` | `Transaction` |
| Utilisateur | `User` | `users` | `Account` |

Le terme `inventory` désigne le **projet** dans son ensemble, pas une entité.

## 3. Dépôt GitHub

- Nom en **kebab-case**, minuscules, sans accents : `inventory-management`.
- Deux approches possibles, à choisir une fois :
  - **Monorepo** : un seul dépôt contenant `backend/` et `frontend/`.
  - **Dépôts séparés** : `inventory-api` et `inventory-web`.

### Structure recommandée (monorepo)

```
inventory-management/
├── backend/
│   ├── app/
│   │   ├── api/            # routeurs FastAPI
│   │   ├── core/           # config, sécurité
│   │   ├── models/         # modèles SQLAlchemy
│   │   ├── schemas/        # schémas Pydantic
│   │   ├── services/       # logique métier
│   │   ├── repositories/   # accès aux données
│   │   └── main.py
│   ├── alembic/            # migrations
│   ├── tests/
│   └── pyproject.toml
├── frontend/
│   ├── src/
│   └── package.json
├── docs/
├── .github/
└── README.md
```

## 4. Branches

| Branche | Rôle |
| --- | --- |
| `main` | Code stable et déployable. Protégée, aucun push direct. |
| `develop` | (optionnelle) Intégration des fonctionnalités avant release. |

Branches temporaires, format : `type/numéro-issue-description-courte`

| Type | Usage | Exemple |
| --- | --- | --- |
| `feature/` | Nouvelle fonctionnalité | `feature/12-ajout-mouvement-stock` |
| `fix/` | Correction de bug | `fix/27-stock-negatif` |
| `hotfix/` | Correction urgente en production | `hotfix/31-erreur-login` |
| `refactor/` | Refactorisation sans changement fonctionnel | `refactor/40-extraction-stock-service` |
| `db/` | Migration ou évolution de schéma | `db/45-table-suppliers` |
| `docs/` | Documentation | `docs/8-guide-installation` |
| `test/` | Tests | `test/52-tests-produits` |
| `chore/` | Maintenance, dépendances | `chore/60-maj-fastapi` |
| `ci/` | Pipelines GitHub Actions | `ci/64-workflow-lint` |

Règles : kebab-case, minuscules, sans accents, 5 mots maximum dans la description, une branche = un sujet, suppression après merge.

## 5. Commits

On suit **Conventional Commits** : `type(portée): description`

```
feat(products): ajoute la création de produit
fix(stock): empêche un stock négatif lors d'une sortie
db(migrations): ajoute la table stock_movements
refactor(api): extrait la pagination dans une dépendance
docs(readme): décrit le lancement avec docker compose
chore(deps): met à jour pydantic
```

### Types autorisés

`feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`, plus `db` pour les migrations.

### Portées conseillées

- **Backend** : `products`, `categories`, `warehouses`, `suppliers`, `stock`, `auth`, `users`, `api`, `core`, `migrations`.
- **Frontend** : `ui`, `pages`, `hooks`, `store`, `api-client`, `routing`.
- **Transverse** : `deps`, `config`, `docker`, `ci`.

### Règles de rédaction

- Description à l'**impératif présent**, sans majuscule initiale ni point final.
- 72 caractères maximum sur la première ligne.
- Corps (facultatif) : explique le *pourquoi*, séparé du titre par une ligne vide.
- Changement incompatible : `!` après le type (`feat(api)!: renomme /items en /products`) et un pied `BREAKING CHANGE:`.
- Référence d'issue en pied : `Closes #12`.

## 6. Pull Requests

- **Titre** : même format qu'un commit, ex. `feat(stock): ajoute les mouvements d'entrée et de sortie`.
- **Description** : contexte, changements principaux, comment tester, issue liée (`Closes #12`), captures d'écran si l'interface change.
- Une PR = un sujet, idéalement moins de 400 lignes modifiées.
- Utiliser le statut *Draft* tant que la PR n'est pas prête.
- Merge en **squash and merge** : le titre de la PR devient le message du commit sur `main`.
- Une PR qui contient une migration le mentionne explicitement dans sa description.

## 7. Issues et labels

- **Titre** court et descriptif : `Ajouter la pagination sur la liste des produits`, `Erreur 500 à la création d'un mouvement de stock`.
- Templates d'issue : bug, fonctionnalité, question.

Labels au format `catégorie: valeur`, en minuscules :

| Catégorie | Exemples |
| --- | --- |
| Type | `type: bug`, `type: feature`, `type: docs`, `type: refactor` |
| Priorité | `priority: high`, `priority: medium`, `priority: low` |
| Périmètre | `scope: backend`, `scope: frontend`, `scope: database`, `scope: infra` |
| Statut | `status: blocked`, `status: in progress`, `status: needs review` |
| Accueil | `good first issue`, `help wanted` |

## 8. Versions et tags

- **SemVer** : `MAJEUR.MINEUR.CORRECTIF`.
- Tags préfixés par `v` : `v1.0.0`, `v1.3.2`. Pré-versions : `v2.0.0-beta.1`.
- MAJEUR : rupture de compatibilité (API ou schéma). MINEUR : ajout compatible. CORRECTIF : correction.
- Chaque release GitHub contient un changelog généré depuis les Conventional Commits.

## 9. Backend Python / FastAPI

Base : **PEP 8**.

### Fichiers et modules

| Élément | Convention | Exemple |
| --- | --- | --- |
| Modules et fichiers | `snake_case`, minuscules | `stock_service.py` |
| Packages (dossiers) | `snake_case`, un mot si possible | `repositories` |
| Tests | préfixe `test_` | `test_stock_service.py` |

### Code

| Élément | Convention | Exemple |
| --- | --- | --- |
| Variables, fonctions, méthodes | `snake_case` | `get_product_by_id()` |
| Classes | `PascalCase` | `StockMovementService` |
| Constantes | `SCREAMING_SNAKE_CASE` | `MAX_PAGE_SIZE` |
| Membres privés | préfixe `_` | `_compute_total()` |
| Booléens | préfixe `is_`, `has_`, `can_` | `is_active`, `has_stock` |
| Types génériques | `PascalCase` court | `T`, `ModelT` |

- Fonctions nommées avec un **verbe** : `create_product`, `adjust_stock`, `list_suppliers`.
- Annotations de type obligatoires sur les fonctions publiques (vérifiées par **mypy**).
- Pas d'abréviations obscures : `quantity` plutôt que `qty` ou `q`.

### Modèles SQLAlchemy

- Classe au **singulier**, `PascalCase` : `Product`, `StockMovement`.
- `__tablename__` au **pluriel**, `snake_case` : `products`, `stock_movements`.
- Une classe par fichier ou un fichier par domaine : `models/product.py`.

### Schémas Pydantic

Nom de la forme `<Entité><Rôle>` :

| Rôle | Suffixe | Exemple |
| --- | --- | --- |
| Création (entrée) | `Create` | `ProductCreate` |
| Mise à jour (entrée) | `Update` | `ProductUpdate` |
| Lecture (sortie) | `Read` | `ProductRead` |
| Liste paginée | `Page` | `ProductPage` |
| Filtres de recherche | `Filter` | `ProductFilter` |

### Routeurs et endpoints FastAPI

| Élément | Convention | Exemple |
| --- | --- | --- |
| Fichier de routeur | pluriel de la ressource | `api/products.py` |
| Variable du routeur | `router` | `router = APIRouter(prefix="/products", tags=["products"])` |
| Fonction de route | verbe + ressource | `list_products`, `get_product`, `create_product`, `update_product`, `delete_product` |
| Dépendances | préfixe `get_` | `get_db`, `get_current_user` |
| `operation_id` | identique au nom de la fonction | `create_product` |

### Services et dépôts

- Services : `<Entité>Service` dans `services/<entité>_service.py`.
- Dépôts : `<Entité>Repository` dans `repositories/<entité>_repository.py`.
- Exceptions métier : suffixe `Error`, ex. `InsufficientStockError`, `ProductNotFoundError`.

## 10. API REST

- Ressources au **pluriel**, en minuscules, kebab-case : `/products`, `/stock-movements`.
- Pas de verbe dans l'URL : `POST /stock-movements`, pas `/createMovement`.
- Versionnage dans l'URL : `/api/v1/products`.
- Ressource imbriquée seulement si la relation est forte : `/warehouses/{warehouse_id}/locations`.
- Paramètres de chemin et de requête en `snake_case` : `product_id`, `page_size`, `sort_by`.
- **Champs JSON en `snake_case`** (comportement par défaut de FastAPI/Pydantic) : `unit_price`, `created_at`. Le frontend les convertit en camelCase dans sa couche client (voir section 12).
- Pagination : `page`, `page_size`, `total` ; tri : `sort_by`, `order` (`asc` | `desc`).

| Action | Méthode et route | Fonction |
| --- | --- | --- |
| Lister | `GET /products` | `list_products` |
| Lire | `GET /products/{product_id}` | `get_product` |
| Créer | `POST /products` | `create_product` |
| Modifier | `PATCH /products/{product_id}` | `update_product` |
| Supprimer | `DELETE /products/{product_id}` | `delete_product` |

## 11. Base de données PostgreSQL

### Tables et colonnes

- Tout en **`snake_case`**, minuscules, sans accents, sans guillemets nécessaires.
- Tables au **pluriel** : `products`, `stock_levels`.
- Table de liaison : `<table1>_<table2>`, ex. `products_suppliers`.
- Clé primaire : `id`.
- Clé étrangère : `<entité_singulier>_id`, ex. `product_id`, `warehouse_id`.
- Booléens : `is_active`, `is_deleted`.
- Dates et heures : `created_at`, `updated_at`, `deleted_at` (type `timestamptz`).
- Quantités et montants : noms explicites avec unité si utile, ex. `quantity`, `unit_price`, `weight_kg`.
- Énumérations : valeurs en minuscules, ex. `movement_type` ∈ `in`, `out`, `adjustment`.

### Objets nommés

| Objet | Convention | Exemple |
| --- | --- | --- |
| Clé primaire | `pk_<table>` | `pk_products` |
| Clé étrangère | `fk_<table>_<colonne>_<table_cible>` | `fk_stock_levels_product_id_products` |
| Contrainte unique | `uq_<table>_<colonne>` | `uq_products_sku` |
| Contrainte de vérification | `ck_<table>_<règle>` | `ck_stock_levels_quantity_non_negative` |
| Index | `ix_<table>_<colonne>` | `ix_stock_movements_product_id` |
| Type enum | `<nom>_enum` | `movement_type_enum` |
| Vue | préfixe `v_` | `v_low_stock_products` |

Ces noms sont imposés via la `naming_convention` de SQLAlchemy `MetaData`, pour que **Alembic** génère toujours des noms prévisibles.

### Migrations Alembic

- Fichier : `<horodatage>_<description_snake_case>.py`, ex. `20261105_create_stock_movements_table.py`.
- Message de révision à l'impératif : `create stock movements table`, `add sku index to products`.
- Une migration = un changement logique. Jamais de modification d'une migration déjà fusionnée dans `main`.
- Chaque migration doit avoir un `downgrade()` fonctionnel.

## 12. Frontend TypeScript

Base : **ESLint** + **Prettier**** ****+**** ****R****e****a****c****t**, `strict: true` dans `tsconfig.json`.&#32;

### Fichiers et dossiers

| Élément | Convention | Exemple |
| --- | --- | --- |
| Dossiers | `kebab-case` | `stock-movements/` |
| Composants | `PascalCase` | `ProductTable.tsx` |
| Pages / routes | `PascalCase` + suffixe `Page` | `ProductListPage.tsx` |
| Hooks | `camelCase` préfixé `use` | `useProducts.ts` |
| Services d'appel API | `camelCase` + suffixe `Api` | `productsApi.ts` |
| Types et interfaces | `PascalCase`, dans `types/` | `product.types.ts` |
| Utilitaires | `camelCase` | `formatCurrency.ts` |
| Tests | nom du fichier + `.test` | `ProductTable.test.tsx` |
| Styles (si CSS modules) | nom du composant + `.module.css` | `ProductTable.module.css` |

### Code

| Élément | Convention | Exemple |
| --- | --- | --- |
| Variables, fonctions | `camelCase` | `totalQuantity`, `fetchProducts()` |
| Composants | `PascalCase` | `StockBadge` |
| Types, interfaces | `PascalCase`, sans préfixe `I` | `Product`, `StockMovement` |
| Props | `<Composant>Props` | `ProductTableProps` |
| Constantes globales | `SCREAMING_SNAKE_CASE` | `DEFAULT_PAGE_SIZE` |
| Enums et leurs valeurs | `PascalCase` | `MovementType.In` |
| Booléens | `is`, `has`, `can`, `should` | `isLoading`, `hasError` |
| Gestionnaires d'événements | `handle<Événement>` ; props `on<Événement>` | `handleSubmit`, `onSelect` |

### Correspondance avec l'API

- Les types du frontend reflètent les schémas Pydantic : `ProductRead` (backend) ↔ `Product` (frontend), `ProductCreate` ↔ `ProductCreateInput`.
- Les champs `snake_case` reçus de l'API sont convertis en `camelCase` **une seule fois**, dans la couche `api/` (ex. `unit_price` → `unitPrice`), jamais dans les composants.
- Idéalement, les types sont générés depuis le schéma OpenAPI de FastAPI plutôt que réécrits à la main.

## 13. Variables d'environnement et configuration

- Noms en `SCREAMING_SNAKE_CASE`.
- Backend : préfixe par domaine, ex. `DATABASE_URL`, `JWT_SECRET_KEY`, `JWT_EXPIRE_MINUTES`, `CORS_ALLOWED_ORIGINS`.
- Frontend (Vite) : préfixe obligatoire `VITE_`, ex. `VITE_API_BASE_URL`.
- Un fichier `.env.example` est versionné ; le vrai `.env` n'est **jamais** commité.
- Docker : services nommés `backend`, `frontend`, `db` ; volumes en `snake_case`, ex. `postgres_data`.

## 14. Tests

- **Python (pytest)** : fonctions `test_<ce_qui_est_testé>_<condition>_<résultat>`, ex. `test_adjust_stock_when_insufficient_raises_error`.
- **Frontend** : blocs `describe('ProductTable')` et `it('affiche un message si la liste est vide')`.
- Fixtures pytest : `snake_case` descriptif, ex. `db_session`, `sample_product`.

## 15. Automatisation et application des règles

| Domaine | Outil |
| --- | --- |
| TypeScript : Framework | React |
| TypeScript : lint et format | ESLint, Prettier |
| Messages de commit | commitlint (hook `pre-commit` ou `husky`) |
| Nom de branche | vérification dans un workflow GitHub Actions |
| Hooks Git | `pre-commit` |
| Intégration continue | GitHub Actions (lint, tests, build) |

## 16. Évolution du document

- Toute exception ou évolution passe par une issue `type: docs`, puis une PR modifiant ce document.
- En revue de code, ce document fait foi en cas de désaccord.
