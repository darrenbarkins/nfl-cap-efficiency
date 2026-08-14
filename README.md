# NFL Roster Construction & Cap Efficiency

Analyzing whether *how* NFL teams allocate salary cap space across position groups predicts winning better than *how much* they spend in total — using 12 seasons of roster, contract, and game-result data (2013–2024).

## The question

Front offices don't just decide how much to spend — they decide *where* to spend it. This project tests whether a team's cap allocation *pattern* across position groups (QB, OL, WR, LB, etc.) carries meaningful signal about team success beyond total cap spend alone.

## Key finding

Cap allocation pattern shows a small but measurable relationship with win percentage beyond total spend: adding position-group allocation to a simple total-spend model improves R² from **0.012 to 0.040**. This is a modest effect — the majority of variance in team success comes from factors outside cap strategy (coaching, player evaluation accuracy, injuries, scheme fit) — but it's a real, statistically detectable one.

**Tight end investment** showed the strongest positive association with wins holding total spend constant; **linebacker, offensive line, and defensive line** investment showed the strongest negative associations. These are population-level tendencies, not rules — individual teams regularly defy them. The 2015 Carolina Panthers (15-1, +219 point differential) invested heavily in linebacker, the position group with the most negative league-wide coefficient, because Luke Kuechly was worth the exception.

## Case study: offensive line spend vs. outcome

| Team | Season | % Cap on OL | Win % | Point Diff |
|---|---|---|---|---|
| Cleveland Browns | 2017 | 30.8% | 0.000 (0-16) | -176 |
| Carolina Panthers | 2015 | 12.8% | 0.895 (15-1) | +219 |

High OL investment alone didn't save the 2017 Browns from one of the worst seasons in NFL history. Low OL investment didn't stop the 2015 Panthers from one of the best. Spend is not destiny — allocation strategy interacts with talent evaluation, scheme, and other roster decisions in ways a single position group's cap share can't capture on its own.

## Methodology

1. **Data sources**: Player rosters, contract values (AAV, guaranteed money), and game results pulled via `nfl_data_py` (nflverse ecosystem), 2013–2024.
2. **Leakage-safe contract matching**: Contracts are only matched to a season if signed on or before that season, preventing future contract information from leaking into past-season analysis.
3. **Position grouping**: Individual positions rolled up into 9 standard groups (QB, RB, WR, TE, OL, DL, LB, CB_S, ST). Cornerbacks and safeties are combined into a single group (`CB_S`) because roster data uses a generic "DB" label for a large share of defensive backs that doesn't reliably distinguish the two positions — combining them avoids guessing at a split the source data doesn't support.
4. **Team-level aggregation**: Player-level contract values rolled up to team + position group + season, then converted to % of total team cap to isolate allocation *strategy* from total spend.
5. **Team code standardization**: Relocated franchises (OAK→LV, SD→LAC, STL→LA) mapped to consistent codes to avoid duplicate team entries across the panel.
6. **Regression**: Linear regression comparing win_pct explained by total cap spend alone vs. total spend + position-group allocation %. One position group (special teams) excluded as baseline to avoid multicollinearity, since allocation percentages sum to 1 across each team-season.

## Data notes & limitations

- **CB_S combination**: Noted above — a source data limitation, not an analytical choice made for convenience.
- **R² is intentionally reported honestly, not oversold**: the model explains a small share of variance in wins. Cap allocation is one input into team success among many, and this project doesn't claim otherwise.
- **Correlation, not causation**: position groups with negative coefficients aren't "bad investments" — teams may overspend at a position *because* they're compensating for a weakness, meaning the causal arrow could run in either direction.

## Tech stack

Python · pandas · scikit-learn · `nfl_data_py` · Tableau

## Related projects

- [NFL Player Value Engine](https://github.com/darrenbarkins/nfl-value-engine) — player-level AAV prediction via XGBoost
- [NFL Game Outcome Prediction](https://github.com/darrenbarkins/nfl-game-outcome-prediction) — four-model outcome classifier benchmarked against the Vegas spread
