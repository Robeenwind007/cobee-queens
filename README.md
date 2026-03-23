# 🐝 Cobee Queens — Gestion de Production de Reines

Application PWA de suivi de production et de vente de reines d'abeilles. Accessible depuis navigateur, installable sur mobile (iOS / Android) et tablette.

**Version actuelle : 2.1.0**

🔗 [Accéder à l'application](https://robeenwind007.github.io/cobee-queens/)

---

## Fonctionnalités

### 📋 Productions
- Enregistrement de chaque lot de greffage (père, type, souche, date de greffage)
- Suivi des étapes : starter → couveuse → introduction
- Calcul automatique des dates de couveuse et d'introduction
- Attribution des numéros de reines par lot
- Calcul des ratios (taux d'implantation, couveuse, introduction)
- Visualisation détaillée de chaque production
- Édition et suppression des enregistrements

### 🔍 Recherche
- Recherche de reines par numéro, souche, type ou acquéreur
- Filtre par année
- Vue consolidée productions + ventes

### 💰 Ventes
- Enregistrement des ventes (reine, acquéreur, date, prix, qualité)
- Sélection des reines disponibles (non encore vendues)
- Historique complet des ventes

### 📊 Statistiques
- Vue synthétique par campagne
- Indicateurs clés : greffages, reines produites, vendues, chiffre d'affaires

### ⚙️ Paramètres
- Configuration des souches et types disponibles
- Export manuel de sauvegarde `.json`
- Import d'un fichier de sauvegarde existant
- Synchronisation manuelle avec Supabase (🔄 Recharger depuis Supabase)

---

## Architecture technique

### Stack
- **Frontend** : HTML/CSS/JS — application single-file (`index.html`)
- **PWA** : manifest.json, icône, mode standalone iOS/Android
- **Backend** : [Supabase](https://supabase.com) (PostgreSQL)
- **Hébergement** : GitHub Pages

### Base de données Supabase

| Table | Description |
|---|---|
| `productions` | Lots de greffage et leurs reines |
| `ventes` | Ventes de reines |
| `settings` | Configuration de l'application |

### Synchronisation
- **Au démarrage** : chargement depuis Supabase, localStorage utilisé comme cache
- **Toutes les minutes** : resynchronisation automatique depuis Supabase
- **À chaque écriture** : sauvegarde immédiate en localStorage + push Supabase
- **Sauvegarde locale** : export `.json` automatique après chaque enregistrement

---

## Installation locale

```bash
git clone https://github.com/Robeenwind007/cobee-queens.git
cd cobee-queens
# Ouvrir index.html dans un navigateur
```

> Aucune dépendance npm. L'application fonctionne entièrement depuis le fichier `index.html`.

---

## Déploiement

Le déploiement est automatique via GitHub Pages sur la branche `main`.

```bash
git add index.html
git commit -m "description des modifications"
git push origin main
```

---

## Fichiers

| Fichier | Description |
|---|---|
| `index.html` | Application complète (single-file) |
| `README.md` | Documentation |
| `cobee_import.json` | Jeu de données d'import |
