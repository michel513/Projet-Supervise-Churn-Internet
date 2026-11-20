# Dictionnaire des données

Le jeu nettoyé contient une ligne par client retenu. Les montants sont exprimés en FCFA.

| Variable | Type | Description | Valeurs ou plage |
|---|---|---|---|
| `client_id` | Texte | Identifiant anonymisé du client | `CLIxxxxx` |
| `mois_reference` | Date/texte | Période d'observation | Date attendue AAAA-MM-JJ ; exclue de la modélisation |
| `region` | Catégorie | Région sénégalaise | Dakar, Kaolack, Saint-Louis, Thies, Ziguinchor |
| `type_offre` | Catégorie | Type d'abonnement Internet | Fibre, ADSL, 4G_Fixe |
| `anciennete_mois` | Numérique | Ancienneté du client | Mois |
| `facture_moy_fcfa` | Numérique | Facture mensuelle moyenne | FCFA ; valeurs invalides converties en NaN |
| `retards_paiement_6m` | Entier | Nombre de retards sur les 6 derniers mois | 0 ou plus |
| `incidents_reseau_30j` | Entier | Nombre d'incidents réseau sur 30 jours | 0 ou plus |
| `debit_moy_mbps` | Numérique | Débit moyen observé | Mbps ; valeurs invalides converties en NaN |
| `tickets_support_90j` | Entier | Nombre de tickets support sur 90 jours | 0 ou plus |
| `satisfaction_score` | Numérique | Score de satisfaction | Plage attendue 1 à 5 ; hors plage converti en NaN |
| `usage_go_30j` | Numérique | Volume de données utilisé | Go sur 30 jours |
| `changement_tarif_3m` | Catégorie | Changement de tarif récent | Oui / Non |
| `mode_paiement` | Catégorie | Mode de paiement principal | Carte, Prelevement, OrangeMoney, Wave |
| `resiliation_30j` | Binaire | Variable cible | 0 = pas de résiliation, 1 = résiliation |

## Règles de confidentialité

- Le jeu est synthétique et ne contient aucune donnée personnelle réelle connue.
- `client_id` ne doit jamais être utilisé comme prédicteur.
- Toute nouvelle donnée réelle devra être pseudonymisée, documentée et soumise aux règles de conservation adaptées à son contexte.

## Variables exclues du modèle

- `client_id` : identifiant sans valeur prédictive fiable.
- `mois_reference` : risque de fuite temporelle.
