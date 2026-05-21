# 🐝 Cobee Queens — v3.0.0

Application PWA de gestion de production de reines abeilles.
Déployée sur GitHub Pages · Backend Supabase · Single-file `index.html`

---

## Déploiement

**URL** : `https://robeenwind007.github.io/cobee-queens/`
**Repo** : `Robeenwind007/cobee-queens`

```bash
git add index.html README.md
git commit -m "Cobee Queens v3.0.0"
git push
```

> Le Service Worker est **désactivé** (`if (false)`) pour éviter les problèmes de cache mobile.

---

## Base de données Supabase

**Project ref** : `aatbpkuuwpoareykftpg`
**URL** : `https://aatbpkuuwpoareykftpg.supabase.co`
**Plan** : Payant · Pas de RLS · accès direct clé `anon`

### Tables

#### `productions`
| Colonne | Type | Description |
|---|---|---|
| `id` | TEXT PK | Identifiant unique |
| `date_greffage` | DATE | Date de greffage |
| `date_intro` | DATE | Date d'introduction |
| `date_couveuse` | DATE | Date de mise en couveuse |
| `type` | TEXT | Race (ex: Buckfast) |
| `souche` | TEXT | Souche femelle F0 |
| `pere` | TEXT | Souche mâle |
| `starter` | TEXT | Ruche starter/éleveuse |
| `implantees` | INT | Cellules implantées |
| `couveuse` | INT | Cellules mises en couveuse |
| `introduites` | INT | Cellules introduites |
| `queens` | TEXT[] | Numéros de reines attribués |
| `notes` | TEXT | Notes libres |

#### `ventes`
| Colonne | Type | Description |
|---|---|---|
| `id` | TEXT PK | Identifiant unique |
| `date` | DATE | Date de vente |
| `acquereur` | TEXT | Nom de l'acquéreur |
| `reine` | TEXT | Numéro de reine (ex: `26-17`) |
| `prix` | NUMERIC | Prix de vente en € |
| `qualite` | TEXT | Qualité constatée |
| `origine` | TEXT | Origine (optionnel) |

#### `reines` *(nouveau v3.0.0)*
| Colonne | Type | Description |
|---|---|---|
| `id` | TEXT PK | Identifiant unique |
| `numero` | TEXT | Numéro de reine (ex: `26-17`) |
| `production_id` | TEXT | Référence à la production |
| `type` | TEXT | Race |
| `souche` | TEXT | Souche femelle |
| `pere` | TEXT | Souche mâle |
| `date_greffage` | DATE | Date de greffage |
| `statut` | TEXT | `En stock` / `Vendue` / `DCD` |
| `qualite` | TEXT | Qualité renseignée |
| `notes` | TEXT | Notes libres |
| `date_statut` | DATE | Date du dernier changement de statut |

#### `settings`
| Colonne | Type | Description |
|---|---|---|
| `id` | TEXT PK | Valeur fixe `main` |
| `types` | TEXT[] | Races disponibles |
| `souches` | TEXT[] | Souches femelles disponibles |
| `peres` | TEXT[] | Souches mâles disponibles |
| `queens` | TEXT[] | Numéros de reines disponibles |
| `qualites` | TEXT[] | Qualités disponibles |
| `prix_defaut` | NUMERIC | Prix de vente par défaut |
| `updated_at` | TIMESTAMPTZ | Dernière mise à jour |

### SQL création table `reines`

```sql
CREATE TABLE reines (
  id TEXT PRIMARY KEY,
  numero TEXT NOT NULL,
  production_id TEXT,
  type TEXT,
  souche TEXT,
  pere TEXT,
  date_greffage DATE,
  statut TEXT DEFAULT 'En stock',
  qualite TEXT,
  notes TEXT,
  date_statut DATE
);
GRANT ALL ON reines TO anon;
```

---

## Architecture

- **Single-file** `index.html` — HTML + CSS + JS tout-en-un
- **localStorage** : cache local `cobeequeen_v2` + sauvegarde `cobeequeen_backup_v2`
- **Supabase REST API** : sync en arrière-plan, chargement au démarrage + toutes les 60s
- **Pas d'authentification** : clé `anon` directe

---

## Navigation

| Onglet | Icône | Description |
|---|---|---|
| Productions | 📋 | Historique des greffages |
| Stock | ♛ | Stock de toutes les reines |
| Ventes | 💰 | Enregistrement et historique des ventes |
| Stats | 📊 | Statistiques globales |
| Params | ⚙️ | Paramètres et sauvegarde |
| Recherche | 🔍 | Via icône dans la barre du haut |

---

## Fonctionnalités

### 📋 Productions
- Saisie d'un greffage : date, type, souche ♀ (F0), souche ⚦, starter, cellules
- Calcul automatique des dates : couveuse (+6j), introduction (+10j)
- Attribution des numéros de reines depuis la liste disponible
- **Création automatique des entrées dans le stock** à chaque attribution de numéro
- Vue accordéon groupée par année (année en cours ouverte par défaut)

### ♛ Stock de Reines *(nouveau v3.0.0)*
- Vue de toutes les reines numérotées avec statut, qualité, notes, origine
- **Filtre par année** : toggle `26` / `25` / … / `Toutes`
- **Filtre par statut** : En stock / Vendues / DCD / Toutes
- Statut modifiable via select : `En stock` ↔ `DCD`
  - `Vendue` est géré automatiquement par les ventes (non modifiable manuellement)
  - `DCD` exclut la reine de la liste des reines à vendre
- Qualité modifiable inline (select)
- Notes libres modifiables inline (champ texte)
- Reines vendues : affichent date, acquéreur, prix
- **Migration automatique** au premier accès si stock vide : reconstruit depuis productions + ventes existantes

### 💰 Ventes
- **Filtre par année** : toggle `26` / `25` / … / `Toutes`
- Select reine filtré sur statut `En stock` uniquement (`DCD` et `Vendue` exclus)
- Affichage inline du statut, qualité et notes de la reine sélectionnée
- **Pré-remplissage automatique de la qualité** depuis le stock
- Enregistrement → passe la reine en `Vendue` dans le stock + synchronise la qualité
- Suppression → repasse la reine en `En stock`
- Vue accordéon groupée par année

### 📊 Statistiques
- Filtre par année (pills)
- Tuiles : Productions · Reines produites · En stock · DCD (% perte) · Cellules implantées · Ratio couveuses · Introduites · Ratio introduites · Reines vendues · Taux d'écoulement · CA total · Prix moyen
- Performances par souche ♀ (ratios + CA)
- Ventes par qualité avec détail par souche

### 🔍 Recherche
- Accessible via icône 🔍 dans la barre de navigation haute
- Recherche par année (2 chiffres) + numéro
- Affiche : **badge statut** · qualité · notes · production d'origine · vente associée

### ⚙️ Paramètres
- Listes personnalisables : types de races, souches ♀, souches ⚦, qualités, numéros de reines
- Prix de vente par défaut
- Export JSON (sauvegarde complète incluant `reines`)
- Import JSON (restauration complète + push Supabase)
- Connexion Supabase (URL en lecture seule)
- Réinitialisation complète

---

## Format des numéros de reines

```
AA-NN
```
- `AA` : année 2 chiffres (ex: `26` = 2026)
- `NN` : numéro séquentiel 2+ chiffres (ex: `01`, `17`, `100`)

Exemples : `26-01` · `26-17` · `25-42`

---

## Format JSON de sauvegarde

```json
{
  "_app": "Cobee Queens",
  "_version": 2,
  "_date": "2026-05-21T10:00:00.000Z",
  "productions": [
    {
      "id": "1234567890",
      "dateGreffage": "2026-04-15",
      "dateIntro": "2026-04-25",
      "dateCouveuse": "2026-04-21",
      "type": "Buckfast",
      "souche": "Apinov 040",
      "pere": "Drone BSK-1",
      "starter": "Ruche N°12",
      "implantees": 20,
      "couveuse": 15,
      "introduites": 12,
      "queens": ["26-01", "26-02", "26-03"],
      "notes": ""
    }
  ],
  "ventes": [
    {
      "id": "1234567891",
      "date": "2026-05-10",
      "acquereur": "Dupont Jean",
      "reine": "26-01",
      "prix": 45,
      "qualite": "Bombasse"
    }
  ],
  "reines": [
    {
      "id": "reine_2601_1234567890",
      "numero": "26-01",
      "production_id": "1234567890",
      "type": "Buckfast",
      "souche": "Apinov 040",
      "pere": "Drone BSK-1",
      "dateGreffage": "2026-04-15",
      "statut": "Vendue",
      "qualite": "Bombasse",
      "notes": "",
      "date_statut": "2026-05-10"
    }
  ],
  "settings": {
    "types": ["Buckfast", "Carnica"],
    "souches": ["Apinov 040", "Apinov 159"],
    "peres": ["Drone BSK-1", "Drone BSK-2"],
    "queens": ["26-01", "26-02"],
    "qualites": ["Bon démarrage", "Bonne ponte", "Très bonne", "Excellente", "Bombasse"],
    "prixDefaut": 45
  }
}
```

---

## Historique des versions

| Version | Date | Changements |
|---|---|---|
| **v3.0.0** | Mai 2026 | Nouveau module Stock de Reines (table `reines` Supabase), statuts En stock / Vendue / DCD, filtres année dans Stock et Ventes, pré-remplissage qualité dans les ventes, info reine dans formulaire vente, reines DCD exclues des ventes, migration automatique |
| v2.1.x | Avr. 2026 | Migration Supabase compte payant, connexion Supabase dans paramètres, correction service worker |
| v2.0.0 | 2026 | Migration localStorage → Supabase, sync arrière-plan, sauvegarde JSON |
| v1.1 | Mars 2026 | Écran Recherche, filtre par année dans Stats, import Excel initial |
| v1.0 | 2025 | Version initiale : productions, ventes, statistiques, paramètres |
