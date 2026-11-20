# Changelog

Les changements notables du projet sont documentés dans ce fichier.
Le format s'inspire de [Keep a Changelog](https://keepachangelog.com/fr/1.1.0/).
En l'absence de version publiée, les changements sont regroupés sous `Unreleased`.

## [Unreleased]

### Ajouté

- Documentation de la méthodologie, des données et des contributions.
- Documentation du projet, de son installation, de ses résultats et de ses limites.
- Licence MIT.

### Modifié

- Le notebook principal peut utiliser le fichier nettoyé fourni dans le dépôt
  si le jeu de données brut n'est pas disponible localement.

### Données et résultats de référence

- Jeu nettoyé : 1 700 observations.
- Modèles comparés avec une validation croisée stratifiée à cinq plis.
- Le meilleur score d'Average Precision observé en validation croisée est celui
  de la régression logistique (0,3993 ± 0,0231).
- Évaluation de test enregistrée : AP 0,3605, ROC-AUC 0,7294 et Brier Score
  0,1325.

Ces résultats sont ceux des données synthétiques et des sorties actuellement
conservées dans le notebook exécuté. Ils ne constituent pas une version de
modèle déployée ni une promesse de performance en production.