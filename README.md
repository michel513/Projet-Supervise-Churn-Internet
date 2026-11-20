# Prediction du churn Internet

Projet de classification supervisée visant à estimer la probabilité qu'un client résilie son abonnement Internet dans les 30 prochains jours. Le notebook analyse des données synthétiques dans un contexte sénégalais, nettoie les anomalies, compare plusieurs modèles et propose un seuil compatible avec une contrainte métier de contact de 25 % des clients au maximum.

## Aperçu

- **Tâche :** classification binaire (`resiliation_30j`)
- **Classe positive :** 1, correspondant à une résiliation
- **Métrique principale :** Average Precision
- **Modèle sélectionné :** régression logistique
- **Résultat test :** AP 0,3605 ; ROC-AUC 0,7294 ; Brier Score 0,1325
- **Seuil retenu :** 0,24

Ces métriques proviennent du notebook exécuté fourni dans ce dépôt. Elles décrivent les données synthétiques utilisées et ne constituent pas une garantie de performance en production.

## Structure du dépôt

| Fichier | Rôle |
|---|---|
| `Projet_Churn_Internet.ipynb` | Notebook reproductible : audit, nettoyage, modélisation et recommandation |
| `Projet_Churn_Internet_executed.ipynb` | Version exécutée conservant les sorties, graphiques et tableaux |
| `donnees_nettoyees.csv` | Jeu de 1 700 observations après nettoyage et suppression des doublons |
| `journal_nettoyage.csv` | Traçabilité des règles de nettoyage |
| `tableau_capacite.csv` | Résiliations détectées selon plusieurs taux de couverture |
| `DATA_DICTIONARY.md` | Description détaillée des variables |
| `requirements.txt` | Dépendances Python minimales |

## Installation

Python 3.10 ou plus récent est recommandé.

```bash
git clone https://github.com/michel513/Projet-Supervise-Churn-Internet.git
cd Projet-Supervise-Churn-Internet

python -m venv .venv
source .venv/bin/activate
python -m pip install -U pip
python -m pip install -r requirements.txt
jupyter lab
```

Ouvrez `Projet_Churn_Internet.ipynb`, puis exécutez les cellules dans l'ordre.

## Données

Le fichier source brut `P05_churn_internet_sale.csv` n'est pas inclus dans le dépôt. Le notebook cherche d'abord ce fichier, puis utilise automatiquement `donnees_nettoyees.csv` lorsqu'il est disponible.

Le jeu nettoyé contient 1 700 lignes et 15 colonnes. La cible est déséquilibrée : 1 393 clients non résiliants et 307 résiliants, soit environ 18 % de churn.

Le nettoyage traite notamment :

- les suffixes et virgules décimales dans les montants et débits ;
- les factures négatives ou supérieures à 500 000 FCFA ;
- les débits irréalistes ;
- les scores de satisfaction hors de `[1, 5]` ;
- les variantes de casse et d'espacement dans `type_offre` ;
- les dates invalides ;
- les doublons d'identifiant client.

Les valeurs exclues ou invalidées sont documentées dans `journal_nettoyage.csv`.

## Méthodologie

1. Définition du contrat de prédiction.
2. Audit de la qualité des données.
3. Nettoyage traçable des anomalies.
4. Séparation stratifiée 60/20/20 : apprentissage, validation et test.
5. Prétraitement :
   - médiane puis standardisation des variables numériques ;
   - médiane/valeur la plus fréquente selon les colonnes puis encodage one-hot des catégories.
6. Validation croisée des modèles :
   - DummyClassifier ;
   - régression logistique ;
   - arbre de décision ;
   - forêt aléatoire.
7. Recherche du seuil sous contrainte de couverture maximale.
8. Évaluation finale sur le jeu de test et analyse des sous-groupes.

`client_id` identifie uniquement une observation et `mois_reference` est exclue du modèle pour éviter une fuite temporelle.

## Résultats métier

Sur le jeu de test :

- avec 25 % des clients contactés, le classement capture 50,8 % des résiliations ;
- le rappel au seuil 0,24 vaut 49,18 % ;
- la précision vaut 37,04 % ;
- le F1 vaut 42,25 % ;
- le taux d'alerte observé est 28,82 %, supérieur à la couverture maximale théorique de validation.

Avant déploiement, recalibrer et valider le seuil sur des données temporelles plus récentes. Les performances par région ou par offre doivent aussi être suivies pour détecter les dérives et biais potentiels.

## Limites

- Les données sont synthétiques.
- Le choix du modèle et du seuil est basé sur une seule séparation.
- Aucun jeu réellement temporel indépendant n'est fourni.
- Les sorties exécutées peuvent dépendre de versions historiques des bibliothèques.
- Les probabilités ne remplacent pas une validation métier ou statistique.

## Contribution

Les contributions sont les bienvenues : voir [CONTRIBUTING.md](CONTRIBUTING.md).

## Licence

Distribué sous licence MIT. Voir [LICENSE](LICENSE).
