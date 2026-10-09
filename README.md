# Oklahoma Child Support Guideline Schedule, 1999 vs. 2024

**90-819 Python Programming II, Final Project**
Mary Welch · Carnegie Mellon University, Heinz College · Fall 2026

This repository holds the code and raw source files for a project on how the Oklahoma child support guideline schedule (43 O.S. § 119), unchanged in nominal terms since 1999, now applies to Oklahoma families across the income distribution. All source files were retrieved on September 25, 2026.

## Repository layout

```
.
├── README.md
├── <notebook>.ipynb
└── data_raw/
    ├── oscn_119_current_2026-09-25.pdf
    ├── oscn_119_superseded_1999_2026-09-25.pdf
    ├── okdhs_pub_01-38_rev2013_2026-09-25.pdf
    ├── okdhs_quadrennial_review_page_2026-09-25.pdf
    └── usa_00001.xml
```

Two files the notebook needs are not in this repository: the IPUMS microdata (`usa_00001.csv.gz`, see below) and a BLS API key (`bls_api_key.txt`). To run the notebook, place both in the repository's top-level folder.

## Source files in `data_raw/`

| File | Description | Source |
|---|---|---|
| `oscn_119_current_2026-09-25.pdf` | 43 O.S. § 119, current text (Laws 2000, c. 345, emerg. eff. June 6, 2000). | [OSCN](https://www.oscn.net/applications/oscn/DeliverDocument.asp?CiteID=71841) |
| `oscn_119_superseded_1999_2026-09-25.pdf` | 43 O.S. § 119, superseded text (Laws 1999, c. 422, eff. Nov. 1, 1999; superseded June 6, 2000). | [OSCN](https://www.oscn.net/applications/oscn/DeliverDocument.asp?citeid=445151) |
| `okdhs_pub_01-38_rev2013_2026-09-25.pdf` | OKDHS Publication 01-38, *Guideline Schedule: Age and Wage Tables* (revised 6/2013; schedule stamped eff. June 6, 2000). Primary source for the analysis table. | [OKDHS](https://oklahoma.gov/content/dam/ok/en/okdhs/documents/okdhs-publication-library/01-38.pdf) |
| `okdhs_quadrennial_review_page_2026-09-25.pdf` | Saved copy of the OKDHS quadrennial review page for the 2026 review by the Oklahoma Senate Judiciary Committee. The page describes a DHS review presentation, but the presentation is not posted. | [OKDHS](https://oklahoma.gov/okdhs/services/child-support-services/quadrennialreview.html) |
| `usa_00001.xml` | Codebook (DDI) for IPUMS USA extract #1. | IPUMS USA (see below) |

## Known discrepancies in the schedule

The dollar amounts are the same in all three schedule sources except for three cells. In each case the DHS value is the one that rises smoothly with income.

| Combined monthly income | Children | OSCN 1999 | OSCN current | DHS 01-38 |
|---|---|---|---|---|
| $7,800 / $7,850 | Two | 1,281 / 1,287 | 1,287 / 1,281 (swapped) | 1,281 / 1,287 |
| $11,300 | Three | 1,898 | 1,898 | 1,989 |
| $14,250 | Three | 2,240 | 2,240 | 2,241 |

## Sources not stored as files

### Consumer Price Index (BLS)

Pulled from the BLS Public Data API v2.

- Primary: CPI-U, U.S. city average, all items (`CUUR0000SA0`)
- Sensitivity check: CPI-U, South region (`CUUR0300SA0`)

A saved snapshot will be added after the first pull.

### Census microdata (IPUMS USA)

IPUMS USA extract #1, submitted and completed 2026-09-25:

- Samples: 2000 Census 5% sample and 2024 ACS 1-year sample
- Restriction: Oklahoma only (`STATEFIP` = 40)
- 28 harmonized variables
- 213,062 person records (173,843 in 2000; 39,219 in 2024)
- Format: person-level (rectangular) CSV

The codebook (`usa_00001.xml`) is in `data_raw/`. IPUMS terms prohibit redistributing the microdata file (`usa_00001.csv.gz`), so it is stored locally and not committed here. The extract can be rebuilt from the codebook's sample and variable list at [usa.ipums.org](https://usa.ipums.org).

**Citation:** Steven Ruggles, Sarah Flood, Matthew Sobek, Daniel Backman, Grace Cooper, Julia A. Rivera Drew, Stephanie Richards, Renae Rodgers, Jonathan Schroeder, and Kari C.W. Williams. *IPUMS USA: Version 16.0* [dataset]. Minneapolis, MN: IPUMS, 2025. https://doi.org/10.18128/D010.V16.0

### Context (not data)

[Video of the May 4, 2026 Senate Judiciary hearing](https://sg001-harmony.sliq.net/00282/Harmony/en/PowerBrowser/PowerBrowserV3/20260504/-1/81302?startposition=20260504110332&mediaEndTime=20260504111332&viewMode=3) on the quadrennial review.
