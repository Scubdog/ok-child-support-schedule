# Bracket Creep in Family Law: Oklahoma's Frozen Child Support Schedule, 1999–2024

**90-819 Python Programming II, Final Project**
Mary Welch · Carnegie Mellon University, Heinz College · Fall 2026

Oklahoma's child support guideline schedule (43 O.S. § 119) has not changed in nominal terms since 1999. This project measures what that freeze has done to the share of parental income the schedule assigns across the income distribution, and to the share of Oklahoma families it covers at all.

**Report:** `Final_Project_revised.html` (rendered with Quarto from the notebook; download and open in a browser).
**Code and analysis:** `Final_Project_revised.ipynb`.
**Data dictionary:** [`DATA_DICTIONARY.md`](DATA_DICTIONARY.md).

## Repository layout

```
.
├── README.md
├── DATA_DICTIONARY.md
├── Final_Project_revised.ipynb      notebook: all code, outputs, and report text
├── Final_Project_revised.html       rendered report
├── data_raw/
│   ├── oscn_119_current_2026-09-25.pdf
│   ├── oscn_119_superseded_1999_2026-09-25.pdf
│   ├── okdhs_pub_01-38_rev2013_2026-09-25.pdf
│   ├── okdhs_quadrennial_review_page_2026-09-25.pdf
│   ├── bls_cpi_snapshot_2026-10-09.json
│   └── usa_00001.xml
└── data_processed/
    ├── schedule_long.csv
    ├── cpi_annual.csv
    ├── income_percentiles.csv
    ├── schedule_indexed_2024.csv
    ├── share_area_by_percentile.csv
    └── top_of_schedule_values.csv
```

Every file in `data_processed/` is written by the notebook. Column definitions, units, and sources are in [`DATA_DICTIONARY.md`](DATA_DICTIONARY.md).

## Reproducing the analysis

Three files the notebook needs are not in this repository:

| File | Why it is excluded | How to get it |
|---|---|---|
| `usa_00001.csv.gz` | IPUMS terms prohibit redistributing microdata | Rebuild the extract at [usa.ipums.org](https://usa.ipums.org) from the samples and variables in `data_raw/usa_00001.xml` |
| `bls_api_key.txt` | Personal API key | Register free at [data.bls.gov/registrationEngine](https://data.bls.gov/registrationEngine/) and save the key as a one-line text file |
| `data_processed/families_married.csv` | Record-level derivative of the IPUMS microdata | Written by the notebook when it runs |

Steps:

1. Place `usa_00001.csv.gz` and `bls_api_key.txt` in the repository's top-level folder.
2. Install Python 3 with `pandas`, `numpy`, `matplotlib`, `scipy`, `pdfplumber`, `requests`, and Jupyter.
3. Open `Final_Project_revised.ipynb` and run all cells in order. The bootstrap cells take under a minute.
4. Optional: install [Quarto](https://quarto.org) and run `quarto render Final_Project_revised.ipynb` to rebuild the report from the saved outputs.

The BLS figures can drift slightly if BLS revises the series; the raw response used for the report is saved in `data_raw/bls_cpi_snapshot_2026-10-09.json`.

## Source files in `data_raw/`

| File | Description | Source |
|---|---|---|
| `oscn_119_current_2026-09-25.pdf` | 43 O.S. § 119, current text (Laws 2000, c. 345, emerg. eff. June 6, 2000). | [OSCN](https://www.oscn.net/applications/oscn/DeliverDocument.asp?CiteID=71841) |
| `oscn_119_superseded_1999_2026-09-25.pdf` | 43 O.S. § 119, superseded text (Laws 1999, c. 422, eff. Nov. 1, 1999; superseded June 6, 2000). | [OSCN](https://www.oscn.net/applications/oscn/DeliverDocument.asp?citeid=445151) |
| `okdhs_pub_01-38_rev2013_2026-09-25.pdf` | OKDHS Publication 01-38, *Guideline Schedule: Age and Wage Tables* (revised 6/2013; schedule stamped eff. June 6, 2000). Primary source for the analysis table. | [OKDHS](https://oklahoma.gov/content/dam/ok/en/okdhs/documents/okdhs-publication-library/01-38.pdf) |
| `okdhs_quadrennial_review_page_2026-09-25.pdf` | Saved copy of the OKDHS quadrennial review page for the 2026 review by the Oklahoma Senate Judiciary Committee. The page describes a DHS review presentation, but the presentation is not posted. | [OKDHS](https://oklahoma.gov/okdhs/services/child-support-services/quadrennialreview.html) |
| `bls_cpi_snapshot_2026-10-09.json` | Raw BLS Public Data API v2 response for CPI-U series `CUUR0000SA0` (U.S. city average, primary deflator) and `CUUR0300SA0` (South region, sensitivity check), retrieved Oct. 9, 2026. | [BLS](https://www.bls.gov/developers/) |
| `usa_00001.xml` | Codebook (DDI) for IPUMS USA extract #1. | IPUMS USA (see below) |

All PDFs were retrieved on September 25, 2026.

## Known discrepancies in the schedule

The schedule has 289 income rows for each number of children: $50, then $650 to $15,000 in $50 steps. The dollar amounts are the same in all three schedule sources except for three cells. In each case the DHS value is the one that rises smoothly with income, and the notebook uses DHS.

| Combined monthly income | Children | OSCN 1999 | OSCN current | DHS 01-38 |
|---|---|---|---|---|
| $7,800 / $7,850 | Two | 1,281 / 1,287 | 1,287 / 1,281 (swapped) | 1,281 / 1,287 |
| $11,300 | Three | 1,898 | 1,898 | 1,989 |
| $14,250 | Three | 2,240 | 2,240 | 2,241 |

## Census microdata (IPUMS USA)

IPUMS USA extract #1, submitted and completed 2026-09-25:

- Samples: 2000 Census 5% sample (1999 income) and 2024 ACS 1-year sample
- Restriction: Oklahoma only (`STATEFIP` = 40)
- 28 harmonized variables
- 213,062 person records (173,843 in 2000; 39,219 in 2024)
- Format: person-level (rectangular) CSV

Analysis sample: married couples living with at least one own child under 18, 17,249 family records for 1999 and 2,935 for 2024.

**Citation:** Steven Ruggles, Sarah Flood, Matthew Sobek, Daniel Backman, Grace Cooper, Julia A. Rivera Drew, Stephanie Richards, Renae Rodgers, Jonathan Schroeder, and Kari C.W. Williams. *IPUMS USA: Version 16.0* [dataset]. Minneapolis, MN: IPUMS, 2025. https://doi.org/10.18128/D010.V16.0

## Context (not data)

[Video of the May 4, 2026 Senate Judiciary hearing](https://sg001-harmony.sliq.net/00282/Harmony/en/PowerBrowser/PowerBrowserV3/20260504/-1/81302?startposition=20260504110332&mediaEndTime=20260504111332&viewMode=3) on the quadrennial review.

## Use of generative AI

Built with Claude (Anthropic) under the course's Tier 4 policy. The report's final section describes how.
