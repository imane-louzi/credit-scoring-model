# Scoring de Crédit — Prédiction du Risque de Défaut

## Contexte
Ce projet vise à prédire la probabilité qu'un emprunteur fasse défaut sur un crédit dans les 2 années suivantes, à partir de ses caractéristiques financières et comportementales. Il s'inscrit dans la continuité de mon expérience en analyse de risque de crédit (modèles PD/LGD, backtesting, IFRS9) acquise chez Carrefour Banque et Assurance.

## Données
Dataset public "Give Me Some Credit" (Kaggle, 2011) :
- 150 000 emprunteurs
- 10 variables explicatives (taux d'utilisation du crédit renouvelable, ratio d'endettement, revenu mensuel, âge, historique de retards de paiement, nombre de crédits ouverts, etc.)
- 1 variable cible : défaut de paiement dans les 2 ans (6,7% de défauts — dataset déséquilibré, cohérent avec la réalité du risque de crédit)

## Méthodologie
1. **Préparation des données** : traitement des valeurs manquantes (médiane pour le revenu, 0 pour les personnes à charge), split train/test stratifié (70/30)
2. **Modélisation** : comparaison de deux approches
   - Régression logistique (avec standardisation des variables et pondération des classes pour gérer le déséquilibre)
   - Random Forest (100 arbres)
3. **Évaluation** : AUC-ROC comme métrique principale (standard en scoring de crédit), complétée par precision/recall
4. **Interprétation** : analyse de l'importance des variables (feature importance) du modèle retenu

## Résultats
| Modèle | AUC |
|---|---|
| Régression logistique | 0.80 |
| Random Forest | **0.83** |

Le Random Forest a été retenu pour sa meilleure performance prédictive.

**Variables les plus prédictives** : le taux d'utilisation du crédit renouvelable (27%) domine largement, suivi du ratio d'endettement, de l'âge et du revenu mensuel. L'historique de retards de paiement (30-59, 60-89, 90+ jours) représente collectivement environ 21% de l'importance — confirmant que le comportement de paiement passé est un des meilleurs prédicteurs du risque futur, davantage que les caractéristiques socio-démographiques (nombre de personnes à charge, prêts immobiliers).

## Technologies utilisées
Python (pandas, numpy, scikit-learn), Google Colab

