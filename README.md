# Verif-ordonnance
# Verif-ordonnance
# OrdoVerif – vérification d’ordonnances (Python + Flask)

## Prérequis
- Python 3.9+
- Tesseract + langue française
- poppler (pdf2image)

## Installation
```bash
git clone https://github.com/VOTRE-PSEUDO/ordo-verif-py.git
cd ordo-verif-py
python -m venv venv
source venv/bin/activate  # Windows : venv\Scripts\activate
pip install -r requirements.txt
```

## Lancement
```bash
python app.py
```
Ouvrir http://localhost:5000

## Déploiement
Push sur GitHub → Render / Railway → build automatique (plan gratuit).
