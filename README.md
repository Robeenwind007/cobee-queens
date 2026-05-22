# 🐝 Cobee Queens — v3.0.4

Application PWA de gestion de production de reines abeilles.
Déployée sur GitHub Pages · Backend Supabase · Single-file `index.html`

---

## Déploiement

**URL** : `https://robeenwind007.github.io/cobee-queens/`
**Repo** : `Robeenwind007/cobee-queens`

```bash
git add index.html README.md
git commit -m "Cobee Queens vX.X.X"
git push
```

> Le Service Worker est **désactivé** (`if (false)`) pour éviter les problèmes de cache mobile.

---

## Base de données Supabase

**Project ref** : `aatbpkuuwpoareykftpg`
**URL** : `https://aatbpkuuwpoareykftpg.supabase.co`
**Plan** : Payant · RLS désactivé sur toutes les tables · accès direct clé `anon`

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

#### `reines`
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
-- Désactiver le RLS (cohérent avec les autres tables)
ALTER TABLE reines DISABLE ROW LEVEL SECURITY;
GRANT ALL ON reines TO anon;
```

---

## Architecture

- **Single-file** `index.html` — HTML + CSS + JS tout-en-un
- **localStorage** : cache local `cobeequeen_v2` + sauvegarde `cobeequeen_backup_v2`
- **Supabase REST API** : sync en arrière-plan, chargement au démarrage + toutes les 60s
- **Pas d'authentification** : clé `anon` directe, RLS désactivé sur toutes les tables

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

### ♛ Stock de Reines
- Vue de toutes les reines numérotées avec statut, qualité, notes, origine
- **Filtre par année** : toggle `26` / `25` / … / `Toutes`
- **Filtre par statut** : En stock / Vendues / DCD / Toutes
- Statut modifiable via select : `En stock` ↔ `DCD`
  - `Vendue` géré automatiquement par les ventes
  - `DCD` exclut la reine de la liste des reines à vendre
- Qualité et notes modifiables inline
- Reines vendues : affichent date, acquéreur, prix
- **Bouton ⟳ Migration** : reconstruit le stock depuis productions + ventes existantes et pousse vers Supabase
- **Migration automatique** au premier accès si stock vide

### 💰 Ventes
- **Filtre par année** : toggle `26` / `25` / … / `Toutes`
- Select reine filtré sur statut `En stock` uniquement (`DCD` et `Vendue` exclus)
- Affichage inline du statut, qualité et notes de la reine sélectionnée
- **Pré-remplissage automatique de la qualité** depuis le stock
- Enregistrement → passe la reine en `Vendue` + synchronise la qualité
- Suppression → repasse la reine en `En stock`

### 📊 Statistiques
- Filtre par année
- Tuiles : Productions · Reines produites · En stock · DCD (% perte) · Cellules implantées · Ratios · Reines vendues · Taux d'écoulement · CA · Prix moyen
- Performances par souche ♀
- Ventes par qualité avec détail par souche

### 🔍 Recherche
- Icône 🔍 dans la barre haute
- Affiche : badge statut · qualité · notes · production d'origine · vente associée

### ⚙️ Paramètres
- Listes personnalisables : types, souches ♀, souches ⚦, qualités, numéros de reines
- Export / Import JSON (inclut `reines`)
- Réinitialisation complète

---

## Historique des versions

| Version | Date | Changements |
|---|---|---|
| **v3.0.4** | Mai 2026 | Retrait logs debug · version propre production |
| **v3.0.3** | Mai 2026 | Logs debug temporaires pour diagnostic Supabase |
| **v3.0.2** | Mai 2026 | Correction écrasement `DB.reines` par le setInterval toutes les 60s |
| **v3.0.1** | Mai 2026 | `migrateReines` async + `await Promise.all` · bouton ⟳ Migration · correction `updateReineField` statut |
| **v3.0.0** | Mai 2026 | Nouveau module Stock de Reines · table `reines` Supabase · statuts En stock / Vendue / DCD · filtres année Stock et Ventes · pré-remplissage qualité · reines DCD exclues des ventes · migration automatique |
| v2.2.0 | Mai 2026 | Onglet Stock (sans Supabase) · toggle année Productions et Ventes · icône 🔍 recherche dans nav |
| v2.1.x | Avr. 2026 | Migration Supabase compte payant · connexion dans Paramètres · correction Service Worker |
| v2.0.0 | 2026 | Migration localStorage → Supabase · sync arrière-plan · sauvegarde JSON |
| v1.1 | Mars 2026 | Écran Recherche · filtre année Stats |
| v1.0 | 2025 | Version initiale : productions, ventes, statistiques, paramètres |
