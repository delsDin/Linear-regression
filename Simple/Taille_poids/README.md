# Évaluation complète — Régression linéaire v3

**Dataset :** SOCR Height/Weight · 25 000 observations · split 80/20 · lr=0.01 · tol=1e-9

---

## 1. Performances globales

| Métrique       | Valeur     | Détail              |
|----------------|------------|---------------------|
| R² — Train     | **0.2509** | 20 000 obs          |
| R² — Test      | **0.2606** | Sklearn : 0.2606    |
| RMSE — Test    | **10.12 lbs** | MAE : 8.03 lbs   |
| MAPE — Test    | **6.44%**  | Erreur relative     |

---

## 2. Courbe d'apprentissage

Le coût décroît régulièrement de **0.498 à 0.375** — aucune oscillation, aucun plateau prématuré.  
Convergence atteinte à l'**itération 733**.

---

## 3. Droite de régression

Équation obtenue sur les données test (5 000 pts) :

```
ŷ = 3.07x − 81.61
```

---

## 4. Analyse des résidus (test)

- Résidus centrés sur 0 (moyenne = **−0.04 lbs**)
- Dispersion homogène — absence de biais systématique

---

## 5. Tests statistiques

| Test                        | Résultat              | Détail                          |
|-----------------------------|-----------------------|---------------------------------|
| Normalité des résidus       | ✅ Non rejetée        | Shapiro p=0.40 · KS p=0.62     |
| Autocorrélation (Durbin-Watson) | ✅ **1.9975**     | Idéal ≈ 2 — aucune autocorrélation |
| Significativité de la pente | ✅ t = **81.8**       | p ≈ 0 · IC 95% : [2.996, 3.143] |
| Homoscédasticité            | ⚠️ Légère            | r = −0.04 · p = 0.004          |

---

## 6. Évaluation par critère

### ✅ Normalisation & absence de fuite de données — `10/10`
Z-score calculé uniquement sur le train, appliqué ensuite au test. Dénormalisation algébrique correcte. Solution identique à sklearn au 4e décimal.

### ✅ Convergence réelle — `10/10`
Converge à l'itération 733 avec tol=1e-9. Aucune convergence prématurée. La courbe de coût est monotone décroissante sans oscillation.

### ✅ Évaluation complète (train/test + métriques) — `10/10`
Split 80/20 propre, R², RMSE, MAE calculés sur les deux jeux. Courbe d'apprentissage + analyse des résidus présentes. Faible écart train/test (+0.01) = pas de surapprentissage.

### ✅ Robustesse de `predict()` — `10/10`
Validation de type (`TypeError`) et de plage (`ValueError`) avec messages clairs. Testée sur 3 cas valides et 3 cas invalides. Aucune prédiction silencieuse hors plage.

### ⚠️ Homoscédasticité légèrement non respectée — `7/10`
La corrélation |résidus| ~ x est faible (r=−0.04) mais statistiquement significative (p=0.004). La variance des résidus n'est pas parfaitement constante — attendu pour une régression simple sur ce type de données physiologiques.

### ℹ️ R² plafonné à 0.26 — limite du dataset
Ce n'est pas un défaut du code. La corrélation de Pearson height/weight est r=0.50 — la régression linéaire simple atteint son maximum théorique (R² ≤ r² = 0.25). Seul l'ajout de variables (âge, sexe, etc.) permettrait d'aller plus loin.

---

## Note finale

```
9.5 / 10
```

Modèle correct sur tous les plans techniques. Le seul point mineur est une légère hétéroscédasticité, inhérente à la nature du dataset et non à l'implémentation. Le plafond de R²=0.26 est une limite du problème, pas du modèle.