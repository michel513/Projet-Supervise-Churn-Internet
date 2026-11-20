# Contribuer

Merci de vouloir améliorer ce projet.

## Mise en place

```bash
python -m venv .venv
source .venv/bin/activate
python -m pip install -U pip
python -m pip install -r requirements.txt
```

## Types de contributions

- corrections du notebook ou de sa documentation ;
- amélioration traçable du nettoyage ;
- nouveaux tests ou validations temporelles ;
- comparaison de modèles ;
- correction des anomalies ou clarification des métriques.

## Règles de contribution

1. Décrire clairement le problème ou l'amélioration dans une issue.
2. Créer une branche dédiée.
3. Conserver les données synthétiques hors de tout dépôt contenant des informations personnelles.
4. Documenter toute nouvelle règle de nettoyage dans `journal_nettoyage.csv`.
5. Mettre à jour `DATA_DICTIONARY.md` lors d'une modification de schéma.
6. Exécuter le notebook jusqu'à la fin avant de proposer une modification.
7. Ne jamais modifier une métrique ni un résultat sans conserver les sorties reproductibles associées.

## Format des modifications

- Python conforme aux conventions courantes PEP 8.
- Noms de variables explicites.
- Pas de chemin absolu propre à une machine.
- Pas de secret, credential ou donnée réelle dans les notebooks.

## Rapport de bogue

Merci d'inclure :

- la version de Python ;
- les versions de pandas, NumPy et scikit-learn ;
- les cellules concernées ;
- le message d'erreur complet ;
- les étapes minimales pour reproduire le problème.
