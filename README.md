# Backtesting d'une stratégie de suivi de tendance (croisement de moyennes mobiles)

Test d'une stratégie de suivi de tendance (croisement de moyennes mobiles 50/200 jours) sur un portefeuille équipondéré de trois valeurs du CAC 40 (LVMH, TotalEnergies, Sanofi), comparée à une détention passive (buy & hold) sur 7 ans de données réelles (octobre 2019 à septembre 2026).

![Capital cumulé du portefeuille](capital_cumule_portefeuille.png)

## Le projet

Une stratégie de trading qui « gagne » sur un backtest ne veut rien dire si on ne comprend pas pourquoi elle gagne ou perd, et dans quelles conditions de marché. Ce projet implémente une règle classique de suivi de tendance : rester investi quand la moyenne mobile 50 jours est au-dessus de la moyenne mobile 200 jours (« golden cross »), passer en cash sinon (« death cross »). La règle est testée telle quelle, sans ajuster les paramètres a posteriori.

Les données commencent en janvier 2019, mais la stratégie et le buy & hold sont comparés à partir du 14 octobre 2019, premier jour où la moyenne mobile 200 jours existe. Avant cette date, la stratégie n'a pas de signal : l'inclure reviendrait à la mettre en cash pendant neuf mois de hausse sans qu'elle ait rien décidé.

## Résultats clés

| | CAGR stratégie | CAGR buy & hold | Sharpe stratégie | Sharpe buy & hold | Max drawdown stratégie | Max drawdown buy & hold |
|---|---|---|---|---|---|---|
| LVMH | -2,33 % | 2,40 % | 0,00 | 0,23 | -60,40 % | -52,61 % |
| TotalEnergies | 11,43 % | 14,59 % | 0,63 | 0,61 | -27,04 % | -56,79 % |
| Sanofi | -0,79 % | 2,21 % | 0,05 | 0,21 | -29,83 % | -27,80 % |
| **Portefeuille équipondéré** | **3,76 %** | **8,26 %** | **0,34** | **0,50** | **-26,29 %** | **-37,17 %** |

**Sur ce panier, la stratégie ne bat pas le buy & hold**, ni en rendement, ni en rendement ajusté du risque (Sharpe 0,34 contre 0,50). Elle réduit la volatilité (13,6 % contre 19,8 %) et le drawdown maximal (-26 % contre -37 %), mais le rendement abandonné est trop important. Sur TotalEnergies, elle divise le drawdown par deux pour un Sharpe équivalent.

## D'où vient l'écart

| Année | Stratégie | Buy & hold | Écart | Part investie |
|---|---|---|---|---|
| 2019 (dès le 14/10) | +6,6 % | +8,6 % | -2,0 pts | 86 % |
| 2020 | -5,5 % | -1,5 % | -4,0 pts | 55 % |
| 2021 | +27,4 % | +33,2 % | -5,8 pts | 89 % |
| 2022 | +9,1 % | +15,3 % | -6,2 pts | 74 % |
| 2023 | +3,6 % | +9,9 % | -6,3 pts | 76 % |
| 2024 | -1,2 % | -3,0 % | +1,8 pt | 53 % |
| 2025 | -9,9 % | +4,2 % | -14,1 pts | 24 % |
| 2026 (au 25/09) | +0,6 % | -3,9 % | +4,5 pts | 46 % |

- **En marché haussier (2021-2023)**, la stratégie n'est jamais investie à 100 % et abandonne environ 6 points par an.
- **En marché sans tendance (2025)**, elle se fait piéger par des faux signaux : en cash pendant la hausse de janvier, investie juste avant la baisse de mars-avril, de nouveau en cash pendant le rebond de l'automne. Cette année coûte à elle seule 14 points.
- **Dans une chute franche (krach Covid)**, elle protège : voir ci-dessous.

## Le cas du krach Covid

![Zoom sur le krach Covid](zoom_covid.png)

Entre novembre 2019 et décembre 2020, la pire baisse de la stratégie est de -24 %, contre -37 % pour le buy & hold : elle a commencé à réduire son exposition début mars 2020. En contrepartie, elle se réinvestit lentement et ne capte qu'une partie du rebond. Sur la fenêtre, elle finit à -0,1 % contre +5,1 %. Le krach illustre le compromis de la stratégie, mais il n'explique qu'une petite partie de l'écart total.

![Capital cumulé par action](capital_cumule_par_action.png)

Le drawdown de -60 % de la stratégie sur LVMH ne vient pas du Covid : il s'étale d'avril 2023 à mars 2026, pendant la longue baisse du titre, où la stratégie est rentrée plusieurs fois à contretemps.

## Fichiers

| Fichier | Description |
|---|---|
| `Backtesting_Momentum_LVMH_TTE_SAN.ipynb` | Notebook Python complet (données, signal, métriques, décomposition par année, graphiques) |
| `Backtesting_Momentum_Trading.docx` | Dossier méthodologique |
| `capital_cumule_portefeuille.png`, `capital_cumule_par_action.png`, `zoom_covid.png` | Graphiques générés par le notebook |
| `prix_actions.csv` | Prix de clôture ajustés |
| `metriques_par_action.csv`, `metriques_portefeuille.csv`, `performance_par_annee.csv` | Résultats exportés |

## Outils

Python (pandas, numpy, yfinance, matplotlib), Google Colab.

## Limites et pistes d'amélioration

- **Échantillon très petit** : 7 à 12 changements de position par action en sept ans, une seule période, trois titres. Le résultat décrit ce qui s'est passé ; il ne permet pas de conclure statistiquement sur la règle elle-même.
- **Trois actions seulement** : le résultat dépend fortement de LVMH, un tiers du portefeuille. Un panier plus large ou un indice lisserait les faux signaux individuels.
- **Cash rémunéré à 0 %** : un taux sans risque réel améliorerait la stratégie, en cash 39 % du temps en moyenne.
- **Frais de transaction** : testés à 0,1 % par transaction, ils font passer le rendement annuel de 3,76 % à 3,62 %. La stratégie trade trop peu pour qu'ils pèsent.
- **Paramètres 50/200 non optimisés**, volontairement, pour éviter le sur-ajustement. Un autre réglage, ou un filtre de confirmation du signal, pourrait donner un résultat différent sur cette période.

---
*Projet réalisé dans le cadre d'une recherche de stage/alternance en finance de marché (risk management, front office).*
