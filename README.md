# Station proximity, rail reliability and household car ownership in Dutch neighbourhoods

TIL6022 Python Programming, Group 19, 2026

**Authors:** Sofie Knevel, Louise Nanninga, Feline Mooren, Marlijn Janssen, Julia Koenen

## What this project is about

In this project we look at whether people who live closer to a train station own fewer cars. We do this for all Dutch neighbourhoods in 2023. We also check if this relation is different for major and minor stations, and if it changes when many trains at the nearest station are cancelled or delayed.

**Main question:** Is there an association between distance to the nearest railway station and household car ownership in Dutch neighbourhoods (2023), and to what extent does this association vary by station type and by the share of disrupted trains at the nearest station (2023)?

Sub-questions:

- **SQ1:** How were car ownership per household, distance to the nearest station and the share of disrupted trains spread across Dutch neighbourhoods in 2023?
- **SQ2:** What is the baseline relation between distance to the nearest station and car ownership, when we take income, urbanisation, household size and density into account?
- **SQ3:** Is this relation different for neighbourhoods near a major station compared to a minor station?
- **SQ4:** Is the relation weaker near stations with a high share of disrupted trains (cancelled or 5+ minutes late)?

## Repository structure

```
TIL6022-Project-Group19/
├── README.md
├── .gitignore
├── project_proposal.ipynb                     # project proposal (+ PDF export)
├── project_proposal.pdf
├── project_template (2).ipynb                 # final report notebook
├── project_template (2).pdf                   # PDF export of the final report
└── Python project Til6022/
    ├── Phase1_Phase2_data_cleaning.ipynb          # load and clean raw data
    ├── Phase3_stations_koppelen.ipynb             # link each neighbourhood to its nearest station
    ├── Phase4_storingen.ipynb                     # reliability per station (share of disrupted trains)
    ├── Phase5_mastertabel.ipynb                   # combine everything in one master table
    ├── Phase6_exploratory_analysis_and_maps.ipynb # maps and charts (SQ1)
    ├── Phase7_regression_analysis.ipynb           # regressions (SQ2, SQ3, SQ4)
    └── data/
        ├── raw/              # raw data (the .gpkg map is not on GitHub, see below)
        │   └── treinarchief/ # train archive 2023 (not on GitHub, see below)
        └── cleaned_data/     # made by the notebooks, not on GitHub
```

All notebooks use paths relative to the folder `Python project Til6022` (`data/raw` and `data/cleaned_data`). VS Code runs a notebook from its own folder, so this works automatically. If you use Jupyter in the browser, start it in the folder `Python project Til6022`.

## Data

We use six datasets. All of them are open data.

| Dataset | Source | File name in `data/raw/` | What we use it for |
| --- | --- | --- | --- |
| [Kerncijfers wijken en buurten 2023 (85618NED)](https://www.cbs.nl/nl-nl/cijfers/detail/85618NED) | CBS | `kwb_2023.csv` | cars per household, income, urbanisation, household size, density |
| [Nabijheid voorzieningen 2023 (85830NED)](https://www.cbs.nl/nl-nl/cijfers/detail/85830NED) | CBS | `nabijheid_2023.csv` | road distance to the nearest train station |
| [Wijk- en buurtkaart 2023](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2023) (version 3) | CBS / Kadaster | `wijkenbuurten_2023_v3.gpkg` | shapes of the neighbourhoods for the maps |
| [Railway stations](https://www.rijdendetreinen.nl/en/open-data/stations) | Rijden de Treinen | `stations-2023-09-nl.csv` | station code, type and location |
| [Train disruptions 2021–2023](https://www.rijdendetreinen.nl/en/open-data/disruptions) | Rijden de Treinen | `disruptions-2021.csv`, `disruptions-2022.csv`, `disruptions-2023.csv` | number of disruptions per station (only used to compare with the share of disrupted trains) |
| [Train archive 2023](https://www.rijdendetreinen.nl/en/open-data/train-archive) | Rijden de Treinen | `treinarchief/services-2023-01.csv` … `services-2023-12.csv` | share of trains per station that were cancelled or 5+ minutes late (our reliability measure) |

### Downloading the data

Most CSV files are already in `data/raw/`. The neighbourhood map and the train archive are too big for GitHub, so they are in the `.gitignore` and you have to download them yourself:

1. Go to the [Wijk- en buurtkaart 2023 page](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2023) on the CBS website.
2. Download the GeoPackage of **version 3** of the 2023 map.
3. Unzip it and put `wijkenbuurten_2023_v3.gpkg` in `data/raw/`. The notebooks use the layer `buurten` from this file.
4. Go to the [train archive page](https://www.rijdendetreinen.nl/en/open-data/train-archive) of Rijden de Treinen and download the 12 monthly files of 2023 (`services-2023-01` up to `services-2023-12`).
5. Unzip them and put the 12 `.csv` files in `data/raw/treinarchief/`.

If you want to download the CSV files again yourself:

- **CBS tables (kwb and nabijheid):** open the table on CBS StatLine (links above), select all regions and all variables, and download it as CSV. Save them as `kwb_2023.csv` and `nabijheid_2023.csv`. These tables contain all region levels (country, municipalities, districts and neighbourhoods); the cleaning notebook keeps only the neighbourhoods.
- **Stations:** download the station list of September 2023 from the Rijden de Treinen open data page and save it as `stations-2023-09-nl.csv`.
- **Disruptions:** download the yearly files for 2021, 2022 and 2023 from the Rijden de Treinen open data page and save them as `disruptions-<year>.csv`.

## How to run the analysis

### Packages

We used Python 3 with these packages:

```
pandas
numpy
geopandas
matplotlib
statsmodels
```

You can install them with:

```
pip install pandas numpy geopandas matplotlib statsmodels
```

### Order of the notebooks

The notebooks have to be run in this order, because every notebook uses the files that the previous one saved in `data/cleaned_data/`. The folder is made automatically by the first notebook.

| Phase | Notebook | What it does | Saves |
| --- | --- | --- | --- |
| 1 + 2 | `Phase1_Phase2_data_cleaning.ipynb` | Loads all raw data, keeps only residential neighbourhoods with complete data and removes business areas | `buurten_schoon.pkl`, `stations.pkl`, `storingen.pkl`, `buurten_kaart.gpkg` |
| 3 | `Phase3_stations_koppelen.ipynb` | Finds the nearest station for every neighbourhood (from the centre of the neighbourhood, event stations left out) and labels it as a major or minor station | `buurten_met_station.pkl` |
| 4 | `Phase4_storingen.ipynb` | Calculates the share of trains per station that were cancelled or 5+ minutes late in 2023 (train archive), and for comparison the number of disruptions per year in 2021–2023 (engineering work and foreign stations left out) | `storingen_per_station.pkl` |
| 5 | `Phase5_mastertabel.ipynb` | Combines the results of Phase 3 and 4 into one table with one row per neighbourhood, and keeps only neighbourhoods within 10 km of a station | `master.pkl` |
| 6 | `Phase6_exploratory_analysis_and_maps.ipynb` | Summary statistics, maps and charts for SQ1 | figures |
| 7 | `Phase7_regression_analysis.ipynb` | Checks for multicollinearity (correlations and VIF), three regression models (A, B, C), one for each of SQ2, SQ3 and SQ4, and two robustness checks on model A (adding the distance to the nearest major transfer station, and standard errors clustered by municipality) | model results and figures |

Phase 8 (conclusions, policy implications and limitations) is written in the report, not in code.

### Step by step

1. Clone the repository and open the folder in VS Code.
2. Activate the course environment:
   ```
   conda activate TIL6022-26
   ```
3. Make sure the packages above are installed.
4. Download the neighbourhood map and the train archive and put them in `data/raw/` (see "Downloading the data").
5. Open each notebook, choose *Restart* and then *Run All*, from Phase 1 to Phase 7. Phase 3 and Phase 4 do not depend on each other, but both need to be done before Phase 5.

Because `data/cleaned_data/` is in the `.gitignore`, you always have to run the notebooks yourself after cloning or pulling. If you change something in an earlier notebook, run all later notebooks again.

**Major and minor stations:** we count megastations, intercity hub stations and intercity stations as major stations. All other stations are minor stations. The two event stations (Rotterdam Stadion and Heerenveen IJsstadion) are not used as nearest station, because trains only stop there during events.

The final report is in `project_template (2).ipynb`. It only contains text, tables and figures; all numbers in it come from the outputs of the Phase 6 and Phase 7 notebooks.

## Notes

- **Reference year:** all CBS data is from 2023. The share of disrupted trains is also from 2023. The number of disruptions is averaged over 2021–2023, but is only used for comparison.
- **Station code `NA`:** the station Nieuw Amsterdam has the code `NA`, which pandas reads as a missing value by default. Phase 1 and Phase 4 fill this empty value with `'NA'` again (`fillna('NA')`). After cleaning there are 11,221 neighbourhoods, and 9,039 of them lie within 10 km of a station (the master table). Make sure everyone in the group runs the same version so the numbers in the report match.
- **Code style:** the code follows the structure from the lectures (imports, variables, computations, output) and mostly uses methods taught in the course. Comments are written in English.

## Main findings (short)

These are the results for the 9,039 neighbourhoods within 10 km of a station.

- Car ownership is lowest in the big cities and highest in rural areas. The stations with the highest share of disrupted trains in 2023 are Eygelshoven Markt and several stations on the line through Zeeland.
- Neighbourhoods further from a station have more cars per household (0.93 at 0–1 km, 1.35 at 8–10 km). With control variables the effect stays significant but becomes small: 0.005 extra cars per household per km (R² = 0.76). Household size, urbanisation and density matter much more. The effect stays the same in both robustness checks.
- Neighbourhoods near a major station have slightly lower car ownership (0.03 fewer cars per household with controls), but the increase with distance is the same for major and minor stations (p = 0.97).
- We find no evidence that the effect of distance is weaker near stations with a high share of disrupted trains (interaction p = 0.71). With the old measure (number of disruptions per year) the effect looked stronger, but that measure mostly shows how busy a station is (correlation 0.83 with the number of trains per day).

**In short:** living closer to a station goes together with owning fewer cars, but most of this is explained by the dense, urban character of station areas and not by the station itself. Building homes near a station will therefore not by itself lead to much lower car ownership.

## References

The full list of literature is in the final report. The datasets we used are:

- CBS. (2025). *Nabijheid voorzieningen; afstand locatie, wijk- en buurtcijfers 2023* (Table 85830NED) [Data set]. StatLine. https://www.cbs.nl/nl-nl/cijfers/detail/85830NED
- CBS. (2026). *Kerncijfers wijken en buurten 2023* (Table 85618NED) [Data set]. StatLine. https://www.cbs.nl/nl-nl/cijfers/detail/85618NED
- CBS & Kadaster. (2026). *Wijk- en buurtkaart 2023* (Version 3) [Data set]. https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2023
- Rijden de Treinen. (2026a). *Open data: Railway stations* [Data set]. https://www.rijdendetreinen.nl/en/open-data/stations
- Rijden de Treinen. (2026b). *Open data: Train disruptions* [Data set]. https://www.rijdendetreinen.nl/en/open-data/disruptions
- Rijden de Treinen. (2026c). *Open data: Train archive* [Data set]. https://www.rijdendetreinen.nl/en/open-data/train-archive
