# Workshop Health Insurance Plan (WHIP) code system

WHIP is a fictional provincial health plan made for the workshop. It follows the structure of OHIP billing: a fee code for the service and a diagnostic code for the reason. None of these codes or descriptions come from the OHIP Schedule of Benefits or the OHIP diagnostic code list. Every code uses a `WF-` (fee) or `WD-` (diagnostic) prefix, so none of them can be mistaken for a real one.

| File | Contents |
|---|---|
| `whip-codes.csv` | All fee and diagnostic codes in one file. Upload this one as code system `WHIP`, version `2026-11`, so a single Construe call returns both kinds of code. |
| `whip-fee-codes.csv` | Fee codes only (26 rows). |
| `whip-diagnostic-codes.csv` | Diagnostic codes only (41 rows). |

Columns: `code`, `description`, `definition` and `code_type` (`fee` or `diagnostic`). On upload, set `code_col` to `code`, `desc_col` to `description` and `defn_col` to `definition`. Construe does not use `code_type`; it is there for teams that want to split fee and diagnostic codes in their own pipeline.

The diagnostic codes cover every active problem in `patient-panel.csv`. The upload request is in [../walkthrough.md](../walkthrough.md).
