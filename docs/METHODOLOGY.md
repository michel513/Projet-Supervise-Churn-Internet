# Méthodologie

## Objectif de prédiction

Le projet estime, pour chaque client, une probabilité de résiliation d'un
abonnement Internet dans les 30 jours suivant l'observation. La cible
`resiliation_30j` vaut `1` pour une résiliation et `0` dans le cas contraire.
Le cas d'usage simulé est l'aide au ciblage d'actions de fidélisation, avec une
capacité opérationnelle de contact limitée à 25 % des clients.

Les données sont synthétiques et représentent un opérateur fictif dans un
contexte sénégalais. Les performances décrites ici ne sont donc pas
transférables telles quelles à une clientèle réelle.

## Données et préparation

Le fichier brut contient 1 725 lignes et 15 colonnes. Après nettoyage et
suppression des doublons d'identifiant, le jeu utilisé par le modèle contient
1 700 observations :

- 1 393 observations de classe 0 ;
- 307 observations de classe 1, soit environ 18,1 %.

Les étapes de préparation documentées dans `journal_nettoyage.csv` comprennent :

1. Retirer les suffixes `FCFA` et `Mbps`, convertir les virgules décimales et
   convertir les colonnes numériques.
2. Remplacer par des valeurs manquantes les factures négatives ou supérieures
   à 500 000 FCFA et les débits d'au moins 500 Mbps.
3. Remplacer par des valeurs manquantes les scores de satisfaction hors de
   l'intervalle `[1, 5]`.
4. Normaliser les variantes de casse et d'espacement de `type_offre`.
5. Repérer les dates invalides de `mois_reference`. Cette variable est exclue
   du modèle.
6. Supprimer les observations dupliquées selon `client_id`, en conservant la
   première occurrence.

Les valeurs manquantes restantes sont imputées dans le pipeline de
prétraitement, plutôt que par une transformation calculée avant la séparation
des jeux.

## Variables du modèle

La cible est `resiliation_30j`. Les variables prédictives sont les 12 autres
variables métier décrivant la région, l'offre, l'ancienneté, la facturation,
les retards de paiement, les incidents réseau, le débit, les tickets support,
la satisfaction, l'usage, les changements de tarif et le mode de paiement.

`client_id` est écarté car c'est un identifiant. `mois_reference` est écarté
afin de ne pas apprendre un signal temporel artificiel. La liste détaillée des
colonnes est dans [`DATA_DICTIONARY.md`](DATA_DICTIONARY.md).

## Séparation des données

La séparation est stratifiée et reproductible avec `random_state=42` :

| Jeu | Taille | Classe 0 | Classe 1 |
|---|---:|---:|---:|
| Entraînement | 1 020 | 835 | 185 |
| Validation | 340 | 279 | 61 |
| Test | 340 | 279 | 61 |

Le jeu d'entraînement sert à comparer les modèles, le jeu de validation à
choisir le seuil de décision, et le jeu de test à l'évaluation finale. La
séparation est aléatoire et non chronologique : elle ne mesure donc pas la
robustesse à une dérive dans le temps.

## Prétraitement et modèles

Le prétraitement est intégré au pipeline scikit-learn afin que les
transformations soient ajustées sur les données d'entraînement de chaque pli :

- variables numériques : imputation par la médiane, puis standardisation ;
- variables catégorielles : imputation par la modalité la plus fréquente, puis
  encodage one-hot avec la première modalité retirée et les catégories inconnues
  ignorées.

Quatre modèles sont comparés sur les mêmes plis de validation croisée
stratifiée (5 plis, mélange des observations, graine 42) avec l'Average
Precision :

| Modèle | AP moyenne | Écart-type |
|---|---:|---:|
| Dummy (classe la plus fréquente) | 0,1814 | 0,0000 |
| Régression logistique | 0,3993 | 0,0231 |
| Arbre de décision | 0,2065 | 0,0139 |
| Forêt aléatoire (100 arbres) | 0,3488 | 0,0228 |

Les sorties de validation croisée indiquent la régression logistique comme
meilleur modèle selon le critère annoncé. Certaines légendes de graphiques et
le texte de conclusion du notebook exécuté mentionnent toutefois la forêt
aléatoire. Cette incohérence de présentation doit être corrigée lors d'une
prochaine exécution contrôlée ; les résultats numériques ci-dessus sont
retranscrits des sorties enregistrées.

## Choix du seuil

La sélection du seuil est faite sur les probabilités du jeu de validation.
Le notebook examine les seuils de 0,01 à 0,99 par pas de 0,01, conserve ceux
qui signalent au plus 25 % des clients, puis choisit parmi eux celui ayant le
rappel le plus élevé.

Le seuil enregistré est `0,24`. Sur la validation, il produit :

- un taux d'alerte de 23,82 % ;
- un rappel de 49,18 % ;
- une précision de 37,04 % ;
- un F1 de 42,25 %.

La contrainte est une contrainte de sélection sur validation, pas une garantie
de taux d'alerte identique sur des données nouvelles. Sur le jeu de test, le
taux observé avec le même seuil est 28,82 %.

## Évaluation enregistrée

Sur le jeu de test, les sorties du notebook indiquent :

| Mesure | Résultat |
|---|---:|
| Average Precision | 0,3605 |
| ROC-AUC | 0,7294 |
| Brier Score | 0,1325 |
| Taux d'alerte au seuil 0,24 | 28,82 % |

Le tableau de capacité classe aussi les clients du jeu de test par probabilité
décroissante. Dans cette analyse de rang, contacter les 25 % les mieux classés
capture 31 des 61 résiliations, soit 50,8 %. Cette mesure de capacité est
distincte de l'application du seuil fixe de 0,24.

Le notebook examine par ailleurs les faux négatifs et calcule les performances
par région et type d'offre. Ces analyses reposent sur de petits sous-groupes :
elles sont descriptives et ne suffisent pas à établir l'équité ou la stabilité
du modèle.

## Reproductibilité et limites

- Installer les dépendances listées dans `requirements.txt`, ouvrir
  `Projet_Churn_Internet.ipynb` et exécuter les cellules dans l'ordre.
- Le notebook fixe la graine aléatoire à 42. Les versions des dépendances ne
  sont pas verrouillées ; des versions différentes peuvent modifier les
  sorties.
- Le jeu brut utilisé lors de l'exécution enregistrée n'est pas inclus. Le
  notebook principal utilise le jeu nettoyé fourni comme solution de repli.
- Le jeu est synthétique, la séparation est aléatoire et aucun test temporel
  indépendant n'est disponible.
- Une AP supérieure à la prévalence de la classe positive indique un meilleur
  classement que la baseline dans cet échantillon, mais ne garantit ni
  l'efficacité d'une action de fidélisation ni un lien causal.
- Avant tout usage réel, il faut valider le schéma et les règles de nettoyage,
  évaluer sur une période future, recalibrer les probabilités et le seuil, et
  suivre les performances et les impacts par sous-groupe.