# Exercise 05 — Pandas Reconciliation Drill — Reference Solution

## 1. Solution overview

A governance analyst keeps a curated **ground-truth AI inventory** (the
system-of-record used for risk reviews, EU AI Act classification, and audit
evidence). Separately, an **asset-discovery scan** sweeps the environment —
model registries, cloud accounts, SaaS admin consoles — and emits whatever it
sees. The two views disagree constantly: scans find shadow systems the
analyst has never intaked, the inventory contains systems the scan can't
observe (vendor-hosted, physical, offline), and both sides drift on
attributes like owner team and EU AI Act tier.

Reconciling the two views is a standing analyst drill because NIST AI RMF
*Map 4* and ISO/IEC 42001:2023 *Clause 7.5* both require the inventory to
remain accurate and controlled over time, not just at creation. This
solution delivers:

- Realistic sample data for both files covering every case the learner must
  handle.
- A runnable Python 3.11+ script using pandas that performs an outer merge,
  classifies each row, extracts a long-format column-diff table, and writes
  a summary + detail pair plus a Markdown exec summary.
- Decision rationale explaining join-key choice, merge type, and the
  normalisation pipeline.
- A rubric and common-mistake list a reviewer can apply to a learner's
  submission.

The learner's output is correct when (a) no row from either source is
silently dropped, (b) every row lands in exactly one of four status
categories, (c) column-level disagreements are listed per system rather
than summarised away, and (d) string normalisation happens *before* the
diff so that `"Platform"` vs `"platform "` is not reported as a mismatch.

## 2. Worked answer or implementation

### 2.1 Sample input — `inventory.csv`

The analyst-maintained ground truth. Eight rows covering the full matrix of
reconciliation cases.

```csv
system_id,system_name,owner_team,eu_ai_act_tier,hosted_externally
SYS-001,Resume Screener,Talent Acquisition,high_risk,False
SYS-002,Customer Support Copilot,Customer Experience,limited_risk,True
SYS-003,Marketing Content Generator,Growth Marketing,minimal_risk,True
SYS-004,Fraud Scoring Model,Risk Analytics,high_risk,False
SYS-005,Loan Pricing Assistant,Consumer Lending,high_risk,False
SYS-006,Internal Code Assistant,Platform Engineering,minimal_risk,True
SYS-008,Vendor Translation API,Localization,limited_risk,True
SYS-010,Clinical Triage Pilot,Clinical Operations,high_risk,False
```

### 2.2 Sample input — `discovered.csv`

The asset-discovery scan. Notice the deliberately messy whitespace and
casing — real scanners emit whatever the source system stored.

```csv
system_id,system_name,owner_team,eu_ai_act_tier,hosted_externally
SYS-001,Resume Screener,Talent Acquisition,High_Risk,False
SYS-002,Customer Support Copilot ,CX Platform,limited_risk,True
SYS-003,Marketing Content Generator,Growth Marketing,limited_risk,True
SYS-004,Fraud Scoring Model,Risk Analytics,high_risk,False
SYS-006,Internal Code Assistant,Platform Engineering,minimal_risk,True
SYS-007,Shadow Chatbot POC,Unknown,unclassified,True
SYS-008,Vendor Translation Service,Localization,limited_risk,True
SYS-009,Sales Lead Scorer,Revenue Operations,limited_risk,False
```

Case coverage against the brief:

| Case | Row(s) |
| --- | --- |
| Only in inventory | SYS-005, SYS-010 |
| Only in discovered | SYS-007, SYS-009 |
| `system_name` differs | SYS-008 (`Vendor Translation API` vs `Vendor Translation Service`) |
| `owner_team` differs | SYS-002 (`Customer Experience` vs `CX Platform`) |
| `eu_ai_act_tier` differs | SYS-003 (`minimal_risk` vs `limited_risk`) |
| Identical match (after normalisation) | SYS-001 (casing only), SYS-004, SYS-006 |

SYS-001 is important: raw strings differ (`high_risk` vs `High_Risk`) but
after enum normalisation they are the same — a correct solution treats
this as `match_identical`, not `match_with_diffs`.

### 2.3 Runnable script — `reconcile.py`

```python
"""Reconcile analyst inventory against asset-discovery scan.

Python 3.11+, pandas >= 2.0. Run from the directory containing
inventory.csv and discovered.csv:

    python reconcile.py
"""

from __future__ import annotations

from pathlib import Path

import pandas as pd

# --- Configuration -----------------------------------------------------------

JOIN_KEY = "system_id"

# Columns we compare after the merge. Everything else (e.g. system_id itself)
# is structural and not a "diff candidate".
COMPARE_COLS: list[str] = [
    "system_name",
    "owner_team",
    "eu_ai_act_tier",
    "hosted_externally",
]

# Explicit dtypes keep "True"/"False" strings out of the bool column and stop
# system_id from being coerced to int if the CSV happens to be all-numeric.
DTYPES: dict[str, str] = {
    "system_id": "string",
    "system_name": "string",
    "owner_team": "string",
    "eu_ai_act_tier": "string",
    "hosted_externally": "boolean",
}

# Columns where we case-fold before comparing. system_name is a proper noun
# and casing is sometimes meaningful (brand names), so we only strip it.
CASEFOLD_COLS = {"owner_team", "eu_ai_act_tier"}


def _differs(a: object, b: object) -> bool:
    """True iff `a` and `b` should be reported as different.

    pandas nullable dtypes propagate `pd.NA` through comparisons, so a bare
    `a != b` returns `pd.NA` when either side is missing and raises
    `TypeError: boolean value of NA is ambiguous` in an `if`. Treat both-NA
    as equal (same missing state) and one-NA as a diff.
    """
    a_na, b_na = pd.isna(a), pd.isna(b)
    if a_na and b_na:
        return False
    if a_na or b_na:
        return True
    return a != b


# --- Load --------------------------------------------------------------------

def load(path: Path) -> pd.DataFrame:
    df = pd.read_csv(path, dtype=DTYPES)
    if df[JOIN_KEY].isna().any():
        raise ValueError(f"{path}: {JOIN_KEY} must be non-null on every row")
    if df[JOIN_KEY].duplicated().any():
        dupes = df.loc[df[JOIN_KEY].duplicated(), JOIN_KEY].tolist()
        raise ValueError(f"{path}: duplicate {JOIN_KEY} values: {dupes}")
    return df


# --- Normalise ---------------------------------------------------------------

def normalise(df: pd.DataFrame) -> pd.DataFrame:
    """Strip whitespace on every string column; case-fold enum-like columns.

    We do NOT mutate the raw values we write to column_diffs.csv — those stay
    as the source saw them so the analyst can see exactly what to fix at the
    source. The normalised copy is only used for the equality test.
    """
    out = df.copy()
    for col, dtype in DTYPES.items():
        if dtype != "string":
            continue
        out[col] = out[col].str.strip()
        if col in CASEFOLD_COLS:
            out[col] = out[col].str.casefold()
    return out


# --- Merge + classify --------------------------------------------------------

def reconcile(inv_raw: pd.DataFrame, dis_raw: pd.DataFrame) -> tuple[pd.DataFrame, pd.DataFrame]:
    inv = normalise(inv_raw)
    dis = normalise(dis_raw)

    merged = inv.merge(
        dis,
        on=JOIN_KEY,
        how="outer",
        suffixes=("_inv", "_dis"),
        indicator=True,
    )

    # --- Status classification ------------------------------------------------
    def classify(row: pd.Series) -> str:
        if row["_merge"] == "left_only":
            return "in_inventory_not_discovered"
        if row["_merge"] == "right_only":
            return "in_discovered_not_inventory"
        # Both sides present — compare normalised values.
        for col in COMPARE_COLS:
            if _differs(row[f"{col}_inv"], row[f"{col}_dis"]):
                return "match_with_diffs"
        return "match_identical"

    merged["status"] = merged.apply(classify, axis=1)

    # --- Per-column diff list (only for both-sides rows) ---------------------
    def differing_cols(row: pd.Series) -> list[str]:
        if row["status"] != "match_with_diffs":
            return []
        return [c for c in COMPARE_COLS if _differs(row[f"{c}_inv"], row[f"{c}_dis"])]

    merged["differing_columns"] = merged.apply(differing_cols, axis=1)

    # --- Summary DF (one row per system) -------------------------------------
    summary = merged[[JOIN_KEY, "status", "differing_columns"]].copy()
    summary["differing_columns"] = summary["differing_columns"].apply(
        lambda cols: ";".join(cols) if cols else ""
    )
    summary = summary.sort_values(["status", JOIN_KEY]).reset_index(drop=True)

    # --- Long-format column diffs using the RAW (un-normalised) values -------
    # Re-attach raw values so the analyst sees "High_Risk" vs "high_risk" in
    # the report, not the casefolded version. Strip the join key on both sides
    # first so that a raw ID with trailing whitespace (e.g. "SYS-002 ") joins
    # to its clean twin instead of fanning out into a separate row.
    inv_raw_keyed = inv_raw.copy()
    dis_raw_keyed = dis_raw.copy()
    inv_raw_keyed[JOIN_KEY] = inv_raw_keyed[JOIN_KEY].str.strip()
    dis_raw_keyed[JOIN_KEY] = dis_raw_keyed[JOIN_KEY].str.strip()
    raw_merged = inv_raw_keyed.merge(
        dis_raw_keyed, on=JOIN_KEY, how="outer", suffixes=("_inv", "_dis")
    )
    raw_merged = raw_merged.merge(
        merged[[JOIN_KEY, "status", "differing_columns"]], on=JOIN_KEY, how="left"
    )

    diff_rows: list[dict[str, object]] = []
    for _, row in raw_merged.iterrows():
        if row["status"] != "match_with_diffs":
            continue
        for col in row["differing_columns"]:
            diff_rows.append(
                {
                    "system_id": row[JOIN_KEY],
                    "column": col,
                    "inventory_value": row[f"{col}_inv"],
                    "discovered_value": row[f"{col}_dis"],
                }
            )
    column_diffs = pd.DataFrame(
        diff_rows, columns=["system_id", "column", "inventory_value", "discovered_value"]
    ).sort_values(["system_id", "column"]).reset_index(drop=True)

    return summary, column_diffs


# --- Reporting ---------------------------------------------------------------

def exec_summary(summary: pd.DataFrame) -> str:
    counts = summary["status"].value_counts().to_dict()
    categories = [
        "match_identical",
        "match_with_diffs",
        "in_inventory_not_discovered",
        "in_discovered_not_inventory",
    ]
    lines = ["# AI inventory reconciliation — executive summary", ""]
    lines.append("| Status | Count |")
    lines.append("| --- | ---: |")
    for cat in categories:
        lines.append(f"| {cat} | {counts.get(cat, 0)} |")
    lines.append(f"| **total** | **{len(summary)}** |")
    lines.append("")
    lines.append("## Action items")
    lines.append(
        "- `in_discovered_not_inventory`: triage as potential shadow AI; "
        "open intake tickets."
    )
    lines.append(
        "- `in_inventory_not_discovered`: confirm the system is still live "
        "or mark as retired."
    )
    lines.append(
        "- `match_with_diffs`: see column_diffs.csv and reconcile at the "
        "authoritative source."
    )
    return "\n".join(lines)


# --- Entry point -------------------------------------------------------------

def main() -> None:
    here = Path(__file__).resolve().parent
    inv_raw = load(here / "inventory.csv")
    dis_raw = load(here / "discovered.csv")

    summary, column_diffs = reconcile(inv_raw, dis_raw)

    summary.to_csv(here / "reconciliation_summary.csv", index=False)
    column_diffs.to_csv(here / "column_diffs.csv", index=False)

    print(exec_summary(summary))


if __name__ == "__main__":
    main()
```

### 2.4 Expected output

Given the sample data above, `reconciliation_summary.csv` looks like this:

| system_id | status | differing_columns |
| --- | --- | --- |
| SYS-007 | in_discovered_not_inventory | |
| SYS-009 | in_discovered_not_inventory | |
| SYS-005 | in_inventory_not_discovered | |
| SYS-010 | in_inventory_not_discovered | |
| SYS-001 | match_identical | |
| SYS-004 | match_identical | |
| SYS-006 | match_identical | |
| SYS-002 | match_with_diffs | owner_team |
| SYS-003 | match_with_diffs | eu_ai_act_tier |
| SYS-008 | match_with_diffs | system_name |

`column_diffs.csv`:

| system_id | column | inventory_value | discovered_value |
| --- | --- | --- | --- |
| SYS-002 | owner_team | Customer Experience | CX Platform |
| SYS-003 | eu_ai_act_tier | minimal_risk | limited_risk |
| SYS-008 | system_name | Vendor Translation API | Vendor Translation Service |

Printed Markdown exec summary:

```markdown
# AI inventory reconciliation — executive summary

| Status | Count |
| --- | ---: |
| match_identical | 3 |
| match_with_diffs | 3 |
| in_inventory_not_discovered | 2 |
| in_discovered_not_inventory | 2 |
| **total** | **10** |

## Action items
- `in_discovered_not_inventory`: triage as potential shadow AI; open intake tickets.
- `in_inventory_not_discovered`: confirm the system is still live or mark as retired.
- `match_with_diffs`: see column_diffs.csv and reconcile at the authoritative source.
```

Note: SYS-001 lands in `match_identical` because `High_Risk` and `high_risk`
normalise to the same casefolded value. Without the normalisation step it
would (incorrectly) be reported as a mismatch.

### 2.5 Decision rationale

- **Outer merge, not inner.** An inner merge would silently drop systems
  that appear in only one source — exactly the rows we most need to see. A
  left or right merge would hide one side's uniques. Only outer preserves
  every input row, which is the minimum bar for a reconciliation tool.
- **Join on `system_id`, not `system_name`.** Names are editable, get
  rebranded, differ by casing and whitespace, and are not guaranteed unique.
  `system_id` is an immutable surrogate key assigned at intake; it is the
  only stable anchor between the two views. Treating the name column as
  joinable would conflate "we renamed it" with "we discovered a new
  system".
- **Status column is separate from the diff list.** A single system has one
  *status* (where does it exist?) but zero-to-many *column diffs* (which
  attributes disagree?). Mixing them collapses actionable detail. The
  summary tells leadership "we have 3 systems with discrepancies"; the
  detail tells the responsible engineer "fix the owner_team on SYS-002 in
  the discovery source".
- **Normalise before comparing, keep raw values in the report.** Whitespace
  and enum casing cause false positives when comparing, but the analyst
  fixing the mismatch needs to see the original values — otherwise they
  can't tell whether to fix the inventory or the discovery source.
  `system_name` is stripped but not case-folded because brand capitalisation
  can be deliberate; `owner_team` and `eu_ai_act_tier` are both stripped and
  case-folded because they are effectively enums.
- **Explicit dtypes on load.** Without `dtype=` pandas infers types per
  file. If `inventory.csv` happens to have `True`/`False` strings while
  `discovered.csv` has them as unquoted booleans, every `hosted_externally`
  value would register as a diff. Declaring `hosted_externally` as
  `boolean` on both sides forces a consistent representation.
- **Duplicate and null checks on the join key.** A duplicate `system_id` on
  either side causes a silent fan-out in the merge — row counts balloon,
  totals stop matching inputs. Failing loudly at load time is cheaper than
  debugging a wrong summary later.
- **Deterministic sort order.** The summary sorts by `(status, system_id)`
  and the detail sorts by `(system_id, column)` so the same inputs always
  produce byte-identical outputs — important when the file is diffed in git
  or re-run inside a CI job for drift tracking (RMF *Measure 3*).

## 3. Validation steps

1. **Row-conservation check.** Let `|I|` = rows in `inventory.csv`, `|D|` =
   rows in `discovered.csv`, `|S|` = rows in `reconciliation_summary.csv`.
   Confirm `|S| == |I ∪ D|` on `system_id`. With the sample data that is
   `8 + 8 − 6 = 10`. If `|S|` is smaller, an inner or left merge has
   silently dropped rows.
2. **Status partitioning.** Every row in the summary has exactly one
   status; the four category counts sum to the total. Run:
   ```python
   assert summary["status"].isin({
       "match_identical", "match_with_diffs",
       "in_inventory_not_discovered", "in_discovered_not_inventory",
   }).all()
   assert summary["status"].value_counts().sum() == len(summary)
   ```
3. **Diff-list consistency.** Every `match_with_diffs` row in the summary
   has at least one entry in `column_diffs.csv`, and no other status does.
   ```python
   diffed = set(summary.loc[summary["status"] == "match_with_diffs", "system_id"])
   assert diffed == set(column_diffs["system_id"].unique())
   ```
4. **Normalisation actually ran.** Add a row to `discovered.csv` where
   `owner_team` differs only in trailing whitespace from the inventory.
   Re-run the script. That row must appear as `match_identical`, not
   `match_with_diffs`. If it mismatches, `normalise()` is being skipped or
   the normalised frame is not what's being compared.
5. **Run the script on the sample data and confirm it produces exactly
   three files-worth of output**: `reconciliation_summary.csv` with 10
   rows, `column_diffs.csv` with 3 rows, and a printed Markdown exec
   summary whose status counts match `3 / 3 / 2 / 2`.
6. **Determinism.** Run the script twice with no input change; both output
   files must be byte-identical (`diff -q` returns nothing). Non-determinism
   usually means a missing `sort_values` on either output.

## 4. Rubric or review checklist

Score each criterion out of 3; a passing submission averages ≥ 2.0 and has
no 0s on the correctness rows.

| # | Criterion | 3 — strong | 2 — acceptable | 1 — weak | 0 — fail |
| --- | --- | --- | --- | --- | --- |
| 1 | **Merge correctness** | Outer merge on `system_id`; every input row survives to the summary. | Outer merge but join key is suboptimal (e.g., composite including name). | Left or right merge — loses one side's uniques. | Inner merge — loses all uniques. |
| 2 | **Explicit dtype handling** | `dtype=` on `read_csv` covers every column including the boolean. | Dtypes declared but miss the boolean, risking string-vs-bool diffs. | Dtypes only on the join key. | No dtypes; relies on pandas inference. |
| 3 | **Normalisation before diffing** | Whitespace stripped on all string cols; enum-like cols case-folded; raw values preserved in the report. | Stripped and case-folded but raw values lost (report shows normalised strings). | Only whitespace stripped, or only casefold applied. | No normalisation — SYS-001 reports as a mismatch. |
| 4 | **Summary vs detail separation** | `reconciliation_summary.csv` is one row per system; `column_diffs.csv` is long-format `(system_id, column, inventory_value, discovered_value)`. | Summary and detail separated but detail is wide (one column per diff). | Single combined file. | No per-column detail at all — only status counts. |
| 5 | **No silent drop of rows** | Duplicate / null checks on the join key fail loudly at load time; row-conservation holds. | Row-conservation holds but no explicit guard against dup/null keys. | Row-conservation off by a small amount (e.g., NaN keys folded). | Rows silently dropped and count mismatch unnoticed. |
| 6 | **Deterministic sort order** | Both outputs sorted on stable, documented keys; two runs produce byte-identical files. | Summary sorted, detail not (or vice versa). | Sort depends on dict/hash insertion order. | No sort; row order varies between runs. |
| 7 | **Actionable exec summary** | Markdown table with the four categories named, totals, and an explicit next-action per category. | Markdown table with counts only, no action guidance. | Plain text counts, no Markdown. | No exec summary printed. |

Binary deck-level checks (all must pass):

- [ ] Script runs end-to-end on Python 3.11+ with pandas ≥ 2.0 and no
      warnings about dtype inference.
- [ ] `reconciliation_summary.csv` + `column_diffs.csv` are both written.
- [ ] Every value in the `status` column is one of the four sanctioned
      categories.
- [ ] Re-running produces byte-identical output files.

## 5. Common mistakes

- **Inner merge "for simplicity".** Using `how="inner"` quietly deletes
  every system that exists on only one side — which is the single most
  important class of finding in a reconciliation. The symptom is a summary
  whose row count is smaller than either input. Always `how="outer"`, and
  verify row conservation (validation step 1).
- **Joining on `system_name`.** Names get retitled, renamed by the
  discovery tool, or arrive with trailing whitespace. A rename then
  appears as "the old system vanished and a new one appeared" instead of
  "SYS-008 was renamed". The join key must be the immutable surrogate id.
- **Comparing strings without normalising.** `"High_Risk"` vs `"high_risk"`
  or `"Platform "` vs `"Platform"` will register as a diff and swamp the
  real signal. Strip whitespace on every string column, and case-fold the
  enum-like ones, *before* the equality test.
- **Reporting counts without listing mismatches.** A summary that says
  "3 systems have discrepancies" without `column_diffs.csv` is not
  actionable — the engineer who has to fix the drift needs the specific
  `(system_id, column, inventory_value, discovered_value)` tuples.
- **One giant file instead of summary + detail.** Smashing status and
  per-column diffs into a single wide file (one column per possible diff)
  explodes with each new attribute and is unreadable above ~5 compare
  columns. Keep summary short and detail long.
- **Ignoring type coercion so `"True"` vs `True` register as a diff.** If
  one CSV loads `hosted_externally` as string and the other as bool, every
  row becomes a false positive. Declare `dtype={"hosted_externally":
  "boolean"}` on both loads so the comparison is bool-vs-bool.

## 6. References

- NIST, *AI Risk Management Framework (AI RMF 1.0)*, January 2023 — Map 1
  (context and inventory scope) and Map 4 (components of the AI system are
  mapped and documented, including third-party assets). The reconciliation
  drill operationalises Map 4's requirement that the inventory account for
  *all* components, not just the ones intake has seen. <https://www.nist.gov/itl/ai-risk-management-framework>
- NIST, *AI RMF 1.0*, Measure 3 — mechanisms for tracking identified AI
  risks over time. Reconciliation is the measurement that detects
  inventory drift between scans. <https://www.nist.gov/itl/ai-risk-management-framework>
- ISO/IEC 42001:2023, *Information technology — Artificial intelligence —
  Management system*, Clause 7.5 *Documented information* — requires that
  documented information (including the AI system inventory) is controlled,
  kept current, and reviewable. A standing reconciliation job is one of the
  controls that evidences Clause 7.5 compliance.
- pandas user guide, *merge, join, concatenate and compare* section —
  semantics of `how="outer"`, the `indicator=True` flag used to produce the
  `_merge` column, and the `suffixes=` convention for overlapping columns.
