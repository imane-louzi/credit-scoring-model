# Research log — scoring de crédit piloté par agents

AUC baseline (features d'origine, Random Forest) : **0.8343**

| Feature | AUC | Gain | Verdict | Risque de fuite | Raison |
|---|---|---|---|---|---|
| total_past_due | 0.8415 | +0.0072 | accepté | faible | Historique de retards agrégé, connu au moment de la décision, gain modeste et plausible vu le déséquilibre de classe. |
| income_per_dependent | 0.8403 | +0.0060 | accepté | faible | Revenu et nombre de personnes à charge tous deux disponibles à la demande, gain compatible avec la variabilité attendue. |
| real_estate_loan_ratio | 0.8387 | +0.0045 | accepté | faible | Ratio structurel de l'endettement, gain modéré jugé statistiquement plausible sur 150k observations. |
| severe_delinquency_flag | 0.8390 | +0.0047 | **rejeté** | faible | Cohérence métier claire mais gain jugé trop marginal pour être fiable sans test de significativité — risque de sur-ajustement. |

## Détail par hypothèse

### total_past_due
- **Formule** : `NumberOfTime30-59DaysPastDueNotWorse + NumberOfTime60-89DaysPastDueNotWorse + NumberOfTimes90DaysLate`
- **Justification initiale** : agrège l'historique complet de retards de paiement, capture la fréquence globale des incidents de remboursement.
- **AUC** : 0.8415 (gain +0.0072 vs baseline)
- **Verdict de l'agent critique** : accepté — "La feature ne présente pas de risque de fuite de données, son amélioration de performance est plausible compte tenu du déséquilibre de classe, et elle a une interprétation métier claire liée à l'historique de retard de paiement."

### income_per_dependent
- **Formule** : `MonthlyIncome / (NumberOfDependents + 1)`
- **Justification initiale** : mesure le revenu disponible par personne à charge, indicateur de capacité de remboursement ajusté aux obligations familiales.
- **AUC** : 0.8403 (gain +0.0060 vs baseline)
- **Verdict de l'agent critique** : accepté — "Aucun indice de fuite de données, le gain d'AUC est faible mais plausible, et la feature est économiquement pertinente ; le modèle reste acceptable."

### real_estate_loan_ratio
- **Formule** : `NumberRealEstateLoansOrLines / (NumberOfOpenCreditLinesAndLoans + 1)`
- **Justification initiale** : proportion de crédits immobiliers dans le portefeuille total, reflète la stabilité de l'endettement.
- **AUC** : 0.8387 (gain +0.0045 vs baseline)
- **Verdict de l'agent critique** : accepté — "La feature est économiquement cohérente (répartition de l'exposition de la dette) et le gain de performance est modéré, ce qui évite le sur-ajustement flagrant."

### severe_delinquency_flag — REJETÉ
- **Formule** : `1 if NumberOfTimes90DaysLate > 0 else 0`
- **Justification initiale** : indicateur binaire de défaut sévère passé, signal fort de risque de récidive.
- **AUC** : 0.8390 (gain +0.0047 vs baseline)
- **Verdict de l'agent critique** : **rejeté** — "Malgré une cohérence métier claire, le gain d'AUC insignifiant soulève des doutes sur l'apport réel de la feature. Risque de sur-ajustement ou de détérioration des performances généralisées."

## Ce que cette boucle a permis de détecter

Sur les 4 features proposées par l'agent Hypothèses, 3 ont été validées et 1 a été rejetée : `severe_delinquency_flag`, dont le gain d'AUC (+0.0047) était jugé trop marginal pour être distingué du bruit statistique, malgré une justification métier a priori solide. Ce résultat est notable car cette feature est en grande partie redondante avec `total_past_due` (les deux dérivent de `NumberOfTimes90DaysLate`) — l'agent critique a signalé une valeur ajoutée insuffisante là où l'intuition initiale suggérait une feature "évidemment" utile. Sans cette étape de critique systématique, cette feature aurait probablement été gardée sur la seule base de sa cohérence métier.
