# Flag Reference

- Mark source: Balmo
- History window: 2026-06-30 to 2026-08-31 (latest two months of available prices)
- Forward point date: 2026-09-01 (balmo.csv -> Final Mark)

## Main Fields

- `Forward_Robust_Z`: how far the selected mark is from the component's recent real available history
- `Own_History_Expected_Value`: the own-history expected level used for history-based comparisons
- `Own_History_Direction`: whether the forward mark is above or below the own-history expected value
- `Own_History_Range_*`: own-history expected bands based on the same robust scale used by `Forward_Robust_Z`
- `Chosen_Group_Level`: whether the peer comparison came from the subgroup, parent, or own history only
- `Chosen_Group_Peer_Implied_Final_Mark`: the peer-implied expected forward level from the chosen group model
- `Chosen_Group_Peer_Residual_Z`: deviation from the peer-implied forward mark
- `Chosen_Group_Peer_Direction`: whether the forward mark is above or below the peer-implied expected value
- `Chosen_Group_Peer_Range_*`: peer-implied expected bands widened or tightened by the chosen-group reliability
- `Chosen_Group_Reliability`: average selected historical correlation, clipped to `[0, 1]`
- `Chosen_Group_Peer_Trusted`: peer model is only trusted when selected peers >= 3 and reliability >= 0.75
- `Chosen_Group_Direction_Agree`: own-history delta and peer residual point in the same direction
- `Chosen_Group_Peer_Score_v2`: `abs(Chosen_Group_Peer_Residual_Z) * sqrt(Chosen_Group_Reliability)` when the peer model is trusted, else `0`
- `Chosen_Group_Peer_Signal`: legacy peer signal retained for reference
- `Overall_Combined_Score`: `sqrt(Forward_Abs_Z^2 + Chosen_Group_Peer_Score_v2^2)`
- `HistCal_Check_Type`: historical-check mode (`forward_transition` or `balmo_transition`)
- `HistCal_Window_Label`: date window used for balance-of-month historical comparisons
- `HistCal_Actual_Label`: latest actual date used as the Balmo transition anchor
- `HistCal_CurrentMTD_Z`: recency-weighted z-score for the actual-to-date basis anchor
- `HistCal_Mark_Mean`: recency-weighted historical mean for the matching mark window
- `HistCal_Mark_Delta`: mark minus `HistCal_Mark_Mean`
- `HistCal_Mark_Z`: recency-weighted z-score for the mark versus its matching historical calendar window
- `HistCal_Transition`: current `mark - actual_basis_anchor` transition
- `HistCal_Transition_Mean`: recency-weighted historical mean of the same calendar transition
- `HistCal_Transition_Delta`: `HistCal_Transition - HistCal_Transition_Mean` (signed gap vs history)
- `HistCal_Transition_Magnitude_Delta`: `abs(HistCal_Transition) - abs(HistCal_Transition_Mean)` (weaker/stronger gap)
- `HistCal_Transition_Degree`: qualitative weaker/stronger label (`slight`, `mild`, `moderate`, `strong`, `very strong`)
- `HistCal_Transition_Degree_Score`: `abs(HistCal_Transition_Magnitude_Delta) / component_scale_sd`
- `HistCal_Component_CurrentMonth_SD`: current-month daily basis standard deviation for the component
- `HistCal_Component_HistoricalMonth_SD`: historical same-calendar-month daily basis standard deviation for the component
- `HistCal_Transition_Degree_Scale_Source`: `current-month` or `historical same-month` volatility used for the degree score
- `HistCal_Transition_Z`: z-score for the current transition versus historical transitions
- `HistCal_Sample_Years`: number of historical years in the transition cohort
- `HistCal_Pattern`: explanation-only label (`opposite transition`, `weaker-than-history transition`, `stronger-than-history transition`, or `in-line transition`)
- `Rule_Hits`: which threshold rule(s) fired for the final flag
- `Outlier_Reasons`: plain-language explanation for the component

## Historical Pattern Check (Explanation Only)

- Uses full `PRICES.csv` basis history with recency weighting (3-year half-life).
- For Forward, compares current-month actual-to-date basis to the next-month mark using prior-year transitions.
- For Balmo, compares the latest actual basis to the balance-of-month mark using the same prior-year transition.
- Does **not** change `Group_Outlier_Flag`; it adds context in the `Historical Check` column.
- `opposite transition`: current transition direction differs from historical weighted mean
- `weaker-than-history transition`: same direction, current transition has smaller absolute magnitude than historical mean
- `stronger-than-history transition`: same direction, current transition has larger absolute magnitude than historical mean
- `in-line transition`: current transition magnitude within 0.02 of historical mean magnitude
- `HistCal_Transition_Magnitude_Delta` quantifies how much weaker/stronger (basis units)
- Degree bands for weaker/stronger transitions: slight `<0.5 sd`, mild `0.5-1.0 sd`, moderate `1.0-1.5 sd`, strong `1.5-2.0 sd`, very strong `>=2.0 sd`
- Degree scale uses each component's own current-month daily basis standard deviation when enough observations exist, otherwise historical same-month standard deviation
- Requires at least 3 historical years in the transition cohort.

## Classification Rules

- `Strong outlier`: `|Forward_Robust_Z| >= 4`, or trusted peer score >= 2.0 with direction agreement and `|Forward_Robust_Z| >= 2.5`
- `Outlier`: `|Forward_Robust_Z| >= 3`, or trusted peer score >= 2.5, or trusted peer score >= 1.5 with direction agreement and `|Forward_Robust_Z| >= 1.5`
- `Watch`: `|Forward_Robust_Z| >= 2`, or trusted peer score >= 1.25
- `In-family`: none of the above
- `No forward mark`: no `Final Mark` was available

## Peer-Model Eligibility

- Subgroup model: enough valid subgroup peers and a usable peer residual z-score
- Parent model: same idea, but only for regular parent groups
- Trusted peer evidence requires selected peers >= 3 and reliability >= 0.75
- The strongest combined peer flag also requires own-history and peer direction agreement
- Otherwise the component falls back to own-history-only evaluation or a non-trusted peer view

## Counts

- `Strong outlier`: 0
- `Outlier`: 0
- `Watch`: 0
- `In-family`: 1
- `No forward mark`: 133

## Files

- `group_correlation_outlier_report.csv`: full detailed report
- `component_flag_reference.csv`: compact per-component explanation table
- `flagged_group_outliers.csv`: only flagged rows used for quick review