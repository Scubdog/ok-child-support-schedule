# Data Dictionary

All files in `data_processed/` are written by `Final_Project_revised.ipynb`. Rerunning the notebook
from the top regenerates every one of them from the files in `data_raw/` plus the IPUMS extract.

Conventions used throughout:

- **Monthly dollars.** Every income and support amount is per month, matching the schedule in
  43 O.S. § 119. Census annual incomes are divided by 12.
- **Nominal dollars** unless a column name says otherwise. The schedule's dollar amounts have not
  changed since 1999, so the same schedule applies to both years.
- **Year labels.** The 2000 Census asked about income in 1999, so records with `YEAR` = 2000
  describe 1999 incomes. Tables and charts label them 1999.
- **Shares** are stored as decimals (0.15 means 15%).

---

## Raw files (`data_raw/`)

| File | Source | What it holds |
|---|---|---|
| `okdhs_pub_01-38_rev2013_2026-09-25.pdf` | Oklahoma Human Services, Pub. 01-38 (rev. 2013), downloaded Sept. 25, 2026 | The child support guideline schedule, used as the primary table to parse |
| `oscn_119_current_2026-09-25.pdf` | Oklahoma State Courts Network, 43 O.S. § 119, current text | Statutory schedule, used to verify the DHS table |
| `oscn_119_superseded_1999_2026-09-25.pdf` | Oklahoma State Courts Network, 43 O.S. § 119 as enacted by Laws 1999 c. 422 | The 1999 schedule, used to confirm the dollar amounts have not changed |
| `okdhs_quadrennial_review_page_2026-09-25.pdf` | Oklahoma Human Services, guideline review web page (saved as PDF) | Documents the state's review process; context only, not parsed |
| `bls_cpi_snapshot_2026-10-09.json` | U.S. Bureau of Labor Statistics Public Data API v2, pulled Oct. 9, 2026 | Raw API response for CPI-U series CUUR0000SA0 and CUUR0300SA0 |
| `usa_00001.xml` | IPUMS USA | Codebook (DDI) for the microdata extract |

**Not in this repository:** `usa_00001.csv.gz`, the IPUMS USA extract (2000 Census 5% sample and
2024 ACS 1-year sample, Oklahoma, 28 variables, 213,062 person records). IPUMS terms of use do not
allow redistribution. To reproduce it, request an extract at usa.ipums.org with the samples and
variables listed in `usa_00001.xml`, and save it to the project folder under that name.

---

## `schedule_long.csv`

The guideline schedule in long format: one row per income bracket and number of children.
1,734 rows (289 income brackets × 6 child counts).

| Column | Type | Units | Description |
|---|---|---|---|
| `combined_monthly_income` | integer | dollars per month | Lower bound of the income bracket. A family's amount comes from the highest row equal to or below its income. Rows run from 50 to 15,000, with a gap between 50 and 650 (the table has no rows in that range). |
| `total_support_amount` | integer | dollars per month | Presumptive total child support obligation of both parents combined, before adjustments for child care, health insurance, or parenting time |
| `n_children` | integer | count, 1 to 6 | Number of children; 6 means six or more |
| `support_share` | float | decimal | `total_support_amount` / `combined_monthly_income` |

Source: OKDHS Pub. 01-38, checked cell by cell against both OSCN versions of § 119.

---

## `cpi_annual.csv`

Annual average Consumer Price Index for All Urban Consumers (CPI-U), all items, not seasonally
adjusted. One row per year, 1999 to 2024 (26 rows).

| Column | Type | Units | Description |
|---|---|---|---|
| `year` | integer | calendar year | Year of the annual average (BLS period M13) |
| `CUUR0000SA0` | float | index, 1982-84 = 100 | CPI-U, U.S. city average |
| `CUUR0300SA0` | float | index, 1982-84 = 100 | CPI-U, South region (used as a robustness check) |

The deflator used throughout is 2024 CPI / 1999 CPI for the U.S. series (1.883). The South series
gives 1.884.

---

## `families_married.csv` (not in this repository)

One row per married couple living together with at least one own child under 18. This file is a
record-level derivative of the IPUMS microdata, so it is excluded from the repository for the same
reason as the extract. The notebook rebuilds it.

| Column | Type | Units | Description |
|---|---|---|---|
| `SAMPLE` | integer | IPUMS code | IPUMS sample identifier (200001 = 2000 Census 5%; 202401 = 2024 ACS) |
| `SERIAL` | integer | IPUMS code | Household serial number within the sample |
| `YEAR` | integer | census year | 2000 (1999 income) or 2024 |
| `pernum_a` | integer | IPUMS code | Person number of the first spouse (the lower of the two) |
| `pernum_b` | integer | IPUMS code | Person number of the other spouse (from `SPLOC`) |
| `weight` | float | persons | IPUMS person weight (`PERWT`) of the first spouse, used as the family weight |
| `income_a` | float | dollars per year | First spouse's total personal income (`INCTOT`) |
| `income_b` | float | dollars per year | Other spouse's total personal income (`INCTOT`) |
| `couple_id` | integer | | Row identifier assigned by the notebook |
| `n_children_actual` | integer | count | Own children under 18 linked to either spouse (`MOMLOC`, `POPLOC`), each counted once |
| `combined_monthly_income` | float | dollars per month | (`income_a` + `income_b`) / 12; missing if either spouse's income is missing |
| `n_children` | integer | count, 1 to 6 | `n_children_actual` capped at 6, matching the schedule's last column |
| `support_amount` | float | dollars per month | Presumptive amount from the schedule. Above 15,000, the amount at the 15,000 row (the base amount before judicial discretion, § 119(B)). Missing below 50. |
| `support_share` | float | decimal | `support_amount` / `combined_monthly_income` |
| `above_cap` | boolean | | True if `combined_monthly_income` is above 15,000 |

Filters applied before this file is written: people in households only (`GQ` = 1, 2, or 5);
`INCTOT` code 9999999 recoded to missing. The analysis sample (built in Part 5 and used by every
result) further drops families with no schedule amount: all families below 50 dollars a month,
and 2024 families below about 94 dollars a month (the indexed schedule's first row), so every
figure uses the same families.

---

## `income_percentiles.csv`

Weighted percentiles of combined monthly income for the analysis sample. 99 rows.

| Column | Type | Units | Description |
|---|---|---|---|
| `percentile` | integer | 1 to 99 | Percentile of combined income, within each year |
| `income_1999` | float | dollars per month, nominal | Combined income at that percentile, 2000 Census (1999 income) |
| `income_2024` | float | dollars per month, nominal | Combined income at that percentile, 2024 ACS |
| `growth` | float | ratio | `income_2024` / `income_1999`. Compare with the CPI deflator (1.883): above it means real growth. |

Percentiles are weighted by `weight`. Survey respondents round annual incomes, so some adjacent
percentiles share the same value.

---

## `schedule_indexed_2024.csv`

A counterfactual schedule: the 1999 schedule with every income bracket and every support amount
multiplied by the CPI deflator (1.883). Same shape as `schedule_long.csv`, 1,734 rows.

| Column | Type | Units | Description |
|---|---|---|---|
| `combined_monthly_income` | float | 2024 dollars per month | Bracket lower bound, inflated. The top bracket becomes about 28,243. |
| `n_children` | integer | count, 1 to 6 | Number of children |
| `total_support_amount` | float | 2024 dollars per month | Presumptive amount, inflated |

This is what the schedule would look like if it had been indexed to prices. It is not a proposal;
it assumes the cost of raising a child grew with general prices.

---

## `share_area_by_percentile.csv`

The area between the 1999 and 2024 share-by-percentile curves, summarized. One row per number of
children (1 and 2). Each percentile holds 1% of families, so an average across percentiles is an
average across families.

| Column | Type | Units | Description |
|---|---|---|---|
| `Children` | integer | count | Number of children |
| `Average share, 1999` | float | decimal | Average share of combined income assigned, across percentiles 1 to 99 |
| `Average share, 2024` | float | decimal | Same, 2024 |
| `Relative change` | float | decimal | (2024 average / 1999 average) − 1 |
| `Net area (points)` | float | percentage points | Average change in share assigned (2024 minus 1999) |
| `Area where share fell` | float | percentage points | Contribution of percentiles where the share fell |
| `Area where share rose` | float | percentage points | Contribution of percentiles where the share rose |
| `From below the 2024 cap` | float | percentage points | Contribution of percentiles below the 2024 cap's percentile |
| `From between the caps` | float | percentage points | Contribution of percentiles between the 2024 and 1999 caps |
| `From above the 1999 cap` | float | percentage points | Contribution of percentiles above the 1999 cap's percentile |

The three "From" columns sum to the net area, as do the "fell" and "rose" columns.

---

## `top_of_schedule_values.csv`

What the top of the schedule (the 15,000 row) was worth in 1999 and is worth now. One row per
number of children, 6 rows.

| Column | Type | Units | Description |
|---|---|---|---|
| `Children` | integer | count, 1 to 6 | Number of children |
| `1999 top amount (at 15,000)` | integer | dollars per month | Presumptive amount at the 15,000 row; unchanged since 1999 |
| `Current top, in 1999 dollars` | float | 1999 dollars per month | The top amount divided by the deflator |
| `1999 income that was assigned the current top's value` | integer | dollars per month | The 1999 bracket whose amount matches today's top in real terms |
| `Top amount to hold value (CPI), 2024 dollars` | float | 2024 dollars per month | The top amount multiplied by the deflator |
| `Top amount to hold 1999 coverage, 2024 dollars (rough)` | float | 2024 dollars per month | The top amount scaled to the cap that would keep the same share of families under the schedule as in 1999. Rounded to the nearest 100 because it rests on few high-income records. |
