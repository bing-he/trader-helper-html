# Spring Basis Overlay Outputs (Forward)

- Season: Summer
- History window: 2026-06-05 to 2026-08-05 (latest two months of available prices)
- Forward point date: 2026-09-01
- Mark source file: ForwardMarks.csv
- Parent-group figures written: 17
- Flagged marks written to plot labels: 22
- Components without selected marks: 28

## Plot Logic

- Each subplot shows daily basis history (`component - Henry`) for the latest two months of available prices on or before the forward point date.
- Each component's selected mark is shown as a star on the final-date vertical line.
- Forward marks that fall outside the tight history-based y-axis are clipped to the top/bottom edge and drawn with an X marker instead of expanding the y-axis.
- Only flagged stars are text-labeled directly on the figure.
- Each parent-group figure uses one shared y-axis across all its panels: recent 1-month grouped history `ymin` to `ymax`.
- **Forward + Balmo overlay:** when `mark_source` is `forward`, each panel also draws **Balmo** marks from `balmo.csv` as a short **dashed horizontal segment** ending a day before month-end (same calendar window as the portable subgroup chart), with an end-point marker. Flagged Balmo labels appear to the **right** of that segment when a companion Balmo outlier report is supplied.
- Balmo segment reference month follows **`date.today()`** for the end-of-month window (not the history-end date).

## Files

- `summary.csv`: one row per parent-group figure
- `manifest.csv`: merged assignment, history, forward mark, and flag detail
- `missing_forward_marks.csv`: components with no forward mark
- `plot_flag_manifest.csv`: components whose stars are text-labeled
- `component_volatility.csv`: recent history stats by component
- `group_correlation_outlier_report.csv`: full outlier report copied in from Step2b
- `component_flag_reference.csv`: compact per-component explanation for the final flag
- `flag_reference.md`: threshold reference for Strong outlier / Outlier / Watch

One PNG is written per parent group.