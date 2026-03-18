# 🐝 Cobee Queens — v1.1

Application PWA de gestion de production de reines d'abeilles.  
Fonctionne sur iPhone (Safari) et Mac (Chrome / Edge).

---

## Déploiement

### GitHub Pages
1. Placer `index.html` à la racine d'un dépôt GitHub
2. Activer **Settings → Pages → Deploy from branch → main / root**
3. L'app est accessible à `https://<user>.github.io/<repo>/`

### iPhone — Ajouter à l'écran d'accueil
1. Ouvrir l'URL dans **Safari**
2. Icône partage ↑ → **Sur l'écran d'accueil**
3. L'app se lance en plein écran sans barre Safari

### Mac — Installer comme app
1. Ouvrir l'URL dans **Chrome** ou **Edge**
2. Menu ⋮ → **Installer Cobee Queens**
3. L'app apparaît dans le Dock

---

## Fonctionnalités v1.1

| Écran | Description |
|---|---|
| 📋 Productions | Liste des greffages par année, avec ratios couveuse/introduites |
| 🔍 Recherche | Recherche d'une reine par année + numéro — affiche production & vente |
| 💰 Ventes | Liste des ventes, ajout rapide |
| 📊 Statistiques | Vue d'ensemble filtrée par année, performances par souche ♀, ventes par qualité |
| ⚙️ Paramètres | Gestion des listes (races, souches, qualités, numéros), sauvegarde JSON |

---

## Données

Les données sont stockées en **localStorage** du navigateur.

- Clé active : `cobeequeen_v2`
- Clé backup : `cobeequeen_backup_v2`

### Sauvegarde & Restauration
- **Exporter** : Paramètres → Exporter la sauvegarde → fichier `.json`
- **Restaurer** : Paramètres → Importer & Restaurer → sélectionner le `.json`

> ⚠️ Vider le cache du navigateur supprime les données. Pensez à exporter régulièrement.

---

## Import depuis Excel (migration initiale)

Un fichier `cobee_import.json` peut être généré depuis un fichier Excel avec deux onglets :

| Onglet | Colonnes attendues |
|---|---|
| **Reines** | Souche ⚦, Type, Souche F0 ♀, Date Greffage, Starter/Éleveuses, Implantées, Mises en couveuse, Introduites, N° Reine |
| **Ventes** | Acquéreur, Date vente, Prix de vente, Qualité, Origine, Numéro de reine |

Le numéro de reine est formaté `AA-NN` (ex : `25-01` pour la reine n°1 de 2025).

---

## Format JSON de sauvegarde

```json
{
  "_app": "Cobee Queens",
  "_version": "1.0",
  "productions": [
    {
      "id": "uuid",
      "pere": "BK6986",
      "type": "Buckfast",
      "souche": "Apinov 040",
      "dateGreffage": "2025-03-31",
      "starter": "155",
      "implantees": 105,
      "couveuse": 41,
      "introduites": 21,
      "queens": ["25-01", "25-02"],
      "notes": ""
    }
  ],
  "ventes": [
    {
      "id": "uuid",
      "acquereur": "Dupont Jean",
      "date": "2025-05-10",
      "prix": 40.0,
      "qualite": "Très bonne",
      "origine": "Buckfast",
      "reine": "25-01"
    }
  ],
  "settings": {
    "types": ["Buckfast", "Carnica"],
    "souches": ["Apinov 040"],
    "peres": ["BK6986"],
    "qualites": ["Bon démarrage", "Bonne ponte", "Très bonne", "Excellente", "Bombasse"],
    "prixDefaut": 40,
    "queens": ["25-01", "25-02"]
  }
}
```

---

## Historique des versions

| Version | Date | Changements |
|---|---|---|
| v1.1 | Mars 2026 | Écran Recherche de reine (par année + numéro), filtre par année dans Statistiques, bouton Nouveau greffage homogène, scroll corrigé, import Excel initial |
| v1.0 | 2025 | Version initiale — productions, ventes, statistiques, paramètres |
