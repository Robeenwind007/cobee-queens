# Cobee Queens — v2.1.2

## Référence génétique femelle (souche ♀) automatique sur les ventes

La souche ♀ (F0) d'une reine vendue est désormais récupérée automatiquement à partir de la production qui lui a attribué son numéro. Plus besoin de la ressaisir.

### Modifications

- **Nouvelle fonction utilitaire** `getSoucheByQueen(numéroReine)` : parcourt `DB.productions` et renvoie la `souche` de la production contenant ce numéro.
- **Formulaire « Nouvelle Vente / Modifier vente »** : ajout d'un champ **Souche ♀ (référence génétique femelle)** en lecture seule, mis à jour automatiquement à l'ouverture du formulaire et à chaque changement du sélecteur N° reine (`onchange="updateVenteSouche()"`).
- **Onglet Ventes (liste)** : la souche ♀ s'affiche en chip à côté du numéro de reine pour chaque vente (ex. `♀ Apinov 040`).
- **Recherche de reine (résumé)** : la ligne « Origine » (qui s'appuyait sur un champ `vente.origine` non alimenté) est remplacée par « **Souche ♀** » calculée dynamiquement via `getSoucheByQueen(vente.reine)`.

### Note technique

La souche n'est pas dénormalisée dans l'objet `vente` : elle est recalculée à la volée à partir des productions. Conséquence : si une production est modifiée a posteriori (correction de souche), toutes les ventes des reines de cette production reflètent immédiatement le changement, sans migration de données.

### Version

- `splash-version` : `v 2.1.1` → `v 2.1.2`
