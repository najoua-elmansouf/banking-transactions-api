# Banking Transactions API (FastAPI)

## Description

API REST développée avec **FastAPI** permettant l’analyse de transactions bancaires, incluant :

- Filtrage et pagination des transactions  
- Détection de fraude (scoring simplifié)  
- Calcul de statistiques et agrégations  
- Profil et classement des clients  
- Diagnostic du système  

Le projet respecte une architecture propre avec :

- Routers (endpoints FastAPI)
- Services internes (logique métier séparée)
- Tests unitaires (pytest)
- Tests de features (unittest)
- CI/CD avec GitHub Actions
- Packaging Python (wheel & sdist)


## Installation

Créer un environnement virtuel :

```bash
python -m venv .venv
```

Activer :

### Windows (PowerShell)

```powershell
.venv\Scripts\activate
```

Installer les dépendances :

```bash
pip install -r requirements.txt
```

Lancer l’application :

```bash
uvicorn app.main:app --reload
```

Swagger est disponible ici :

```
http://127.0.0.1:8000/docs
```

## Architecture du projet

```
app/
├── core/
│   └── dataset.py
├── routers/
│   ├── transactions.py
│   ├── stats.py
│   ├── fraud.py
│   ├── customers.py
│   └── system.py
├── services/
│   ├── transactions_service.py
│   ├── stats_service.py
│   ├── fraud_detection_service.py
│   ├── customer_service.py
│   └── system_service.py
└── main.py
```

Les routers délèguent la logique métier aux services internes afin de respecter la séparation des responsabilités.


## Gestion des données

### Stratégie des datasets

Le projet utilise deux configurations de dataset :

1. Dataset complet (développement local)
2. Dataset réduit (CI / tests automatisés)

Cela permet :

- Exécution rapide en CI  
- Respect des limites GitHub 
- Dépôt Git propre et léger  
- Tests reproductibles  


### Dataset en développement local

Pour le développement, les fichiers Kaggle doivent être placés dans :

```
data/
```

Fichiers requis :

```
data/
├── transactions_data.csv
├── users_data.csv
├── cards_data.csv
├── train_fraud_labels.json
├── mcc_codes.json
```

Ces fichiers sont exclus du dépôt Git car :

- Certains dépassent 100 MB
- Ils ralentiraient le clone du projet
- Ils ne sont pas nécessaires aux tests CI

### Dataset utilisé en CI

Un dataset réduit est fourni dans :

```
tests/data/
```

Il est utilisé par GitHub Actions 

### Variable d’environnement DATA_DIR

Le dossier des données est contrôlé par la variable :

```
DATA_DIR
```

#### Comportement par défaut

Si aucune variable n’est définie :

```
DATA_DIR = "data"
```

#### Configuration CI (GitHub Actions)

```yaml
env:
  DATA_DIR: tests/data
```

### Lancement local sous Windows

```powershell
$env:DATA_DIR="data"
uvicorn app.main:app --reload
```

## Tests

### Pytest (tests unitaires + couverture)

```bash
pytest --cov=app --cov-report=term-missing --cov-fail-under=85
```

Exigence minimale : 85%  
Couverture actuelle : ≥ 87%

![coverage](docs/images/coverage.png)


### Tests features (unittest)

```bash
python -m unittest discover -s tests_unittest -p "test_*.py"
```

---

## Qualité du code

### Lint (PEP8)

```bash
flake8 app
```

### Typage statique

```bash
mypy app
```
![flake-mypy](docs/images/flake-mypy.png)


Toutes les fonctions sont typées.

## Packaging

Build avec :

```bash
python -m build
```

Les fichiers générés se trouvent dans :

```
dist/
```

## Intégration Continue (CI/CD)

Le pipeline GitHub Actions exécute :

- Installation des dépendances  
- flake8  
- mypy  
- pytest + couverture  
- unittest  
- Build du package  

