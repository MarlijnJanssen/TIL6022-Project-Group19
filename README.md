# TIL6022-Project-Group19
Python Programming Project Group 19 - 2026

**Authors:** Sofie Knevel, Louise Nanninga, Feline Mooren, Marlijn Janssen, Julia Koenen

## What this project is about

In this project we look at whether people who live closer to a train station own fewer cars. We do this for all Dutch neighbourhoods in 2023. We also check if this relation is different for major and minor stations, and if it changes when a station has a lot of train disruptions.

**Main question:** Is there an association between distance to the nearest railway station and household car ownership in Dutch neighbourhoods (2023), and to what extent does this association vary by station type and disruption frequency (2021–2023)?

Sub-questions:

- **SQ1:** How were car ownership per household, distance to the nearest station and train disruptions spread across Dutch neighbourhoods in 2023?
- **SQ2:** What is the baseline relation between distance to the nearest station and car ownership, when we take income, urbanisation, household size and density into account?
- **SQ3:** Is this relation different for neighbourhoods near a major station compared to a minor station?
- **SQ4:** Is the relation weaker near stations with many disruptions?

## Repository structure

```
TIL6022-Project-Group19/
├── README.md
├── .gitignore
├── project_proposal.ipynb                     # project proposal (+ PDF export)
├── project_proposal.pdf
├── project_template (1).ipynb                 # final report notebook
├── Phase1_Phase2_data_cleaning.ipynb          # load and clean raw data
├── Phase3_stations_koppelen.ipynb             # link each neighbourhood to its nearest station
├── Phase4_storingen.ipynb                     # count disruptions per station
├── Phase5_mastertabel.ipynb                   # combine everything in one master table
├── Phase6_exploratory_analysis_and_maps.ipynb # maps and charts (SQ1)
├── Phase7_regression.ipynb                    # Phase 7: regressions (SQ2, SQ3, SQ4)
└── data/
    ├── raw/            # raw data (the .gpkg map is not on GitHub, see below)
    └── cleaned_data/   # made by the notebooks, not on GitHub
```

All notebooks use paths relative to the repository folder (`data/raw` and `data/cleaned_data`), so open Jupyter in the main folder of the repo.

## Data

We use five datasets. All of them are open data.

| Dataset | Source | File name in `data/raw/` | What we use it for |
| --- | --- | --- | --- |
| [Kerncijfers wijken en buurten 2023 (85618NED)](https://www.cbs.nl/nl-nl/cijfers/detail/85618NED) | CBS | `kwb_2023.csv` | cars per household, income, urbanisation, household size, density |
| [Nabijheid voorzieningen 2023 (85830NED)](https://www.cbs.nl/nl-nl/cijfers/detail/85830NED) | CBS | `nabijheid_2023.csv` | road distance to the nearest train station |
| [Wijk- en buurtkaart 2023](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2023) (version 3) | CBS / Kadaster | `wijkenbuurten_2023_v3.gpkg` | shapes of the neighbourhoods for the maps |
| [Railway stations](https://www.rijdendetreinen.nl/en/open-data/stations) | Rijden de Treinen | `stations-2023-09-nl.csv` | station code, type and location |
| [Train disruptions 2021–2023](https://www.rijdendetreinen.nl/en/open-data/disruptions) | Rijden de Treinen | `disruptions-2021.csv`, `disruptions-2022.csv`, `disruptions-2023.csv` | number of disruptions per station |

### Downloading the data

The CSV files are already in `data/raw/`. Only the neighbourhood map is too big for GitHub, so it is in the `.gitignore` and you have to download it yourself:

1. Go to the [Wijk- en buurtkaart 2023 page](https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2023) on the CBS website.
2. Download the GeoPackage of **version 3** of the 2023 map.
3. Unzip it and put `wijkenbuurten_2023_v3.gpkg` in `data/raw/`. The notebooks use the layer `buurten` from this file.

If you want to download the CSV files again yourself:

- **CBS tables (kwb and nabijheid):** open the table on CBS StatLine (links above), select all regions and all variables, and download it as CSV. Save them as `kwb_2023.csv` and `nabijheid_2023.csv`. These tables contain all region levels (country, municipalities, districts and neighbourhoods); the cleaning notebook keeps only the neighbourhoods.
- **Stations:** download the station list of September 2023 from the Rijden de Treinen open data page and save it as `stations-2023-09-nl.csv`.
- **Disruptions:** download the yearly files for 2021, 2022 and 2023 from the Rijden de Treinen open data page and save them as `disruptions-<year>.csv`.

To check if everything is in the right place, you can run the "Data availability check" at the end of `project_proposal.ipynb`. It prints OK for every file it finds.

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
| 3 | `Phase3_stations_koppelen.ipynb` | Finds the nearest station for every neighbourhood (from the centre of the neighbourhood) and labels it as a major or minor station | `buurten_met_station.pkl` |
| 4 | `Phase4_storingen.ipynb` | Counts the disruptions and disruption minutes per station per year for 2021–2023 (engineering work and foreign stations left out) | `storingen_per_station.pkl` |
| 5 | `Phase5_mastertabel.ipynb` | Combines the results of Phase 3 and 4 into one table with one row per neighbourhood | `master.pkl` |
| 6 | `Phase6_exploratory_analysis_and_maps.ipynb` | Summary statistics, maps and charts for SQ1 | figures |
| 7 | `Phase7_regression.ipynb` | Three regression models (A, B, C), one for each of SQ2, SQ3 and SQ4 | model results and figures |

Phase 8 (conclusions, policy implications and limitations) is written in the report, not in code.

### Step by step

1. Clone the repository and open the folder in VS Code.
2. Activate the course environment:
   ```
   conda activate TIL6022-26
   ```
3. Make sure the packages above are installed.
4. Download the neighbourhood map and put it in `data/raw/` (see "Downloading the data").
5. Open each notebook and choose *Run All*, from Phase 1 to Phase 7. Phase 3 and Phase 4 do not depend on each other, but both need to be done before Phase 5.

Because `data/cleaned_data/` is in the `.gitignore`, you always have to run the notebooks yourself after cloning or pulling. If you change something in an earlier notebook, run all later notebooks again.

**Major and minor stations:** we count megastations, intercity hub stations and intercity stations as major stations. All other stations are minor stations.

The final report is in `project_template (1).ipynb`.

## Notes

- **Reference year:** all CBS data is from 2023. Disruptions are averaged over 2021–2023.
- **Station code `NA`:** the station Nieuw Amsterdam has the code `NA`, which pandas reads as a missing value by default. The Phase 1 notebook uses `keep_default_na=False` to prevent this. With this setting the master table has 11,221 neighbourhoods; without it, 11,202. Make sure everyone in the group runs the same version so the numbers in the report match.
- **Code style:** the code follows the structure from the lectures (imports, variables, computations, output) and mostly uses methods taught in the course. Comments are written in English.

## Main findings (short)

- Car ownership is lowest in the big cities and highest in rural areas.
- Neighbourhoods further from a station have more cars per household (about 0.9 within 2 km, about 1.3 beyond 5 km). This effect stays significant with control variables, but it is small compared to household size, urbanisation and density.
- Neighbourhoods near a major station have slightly lower car ownership, but after adding the control variables the slope is not significantly different between major and minor stations.
- The effect of distance is stronger, not weaker, near stations with many disruptions. A likely reason is that the stations with the most disruptions are also the busiest stations with the most trains.

## References

- CBS (2026). *Kerncijfers wijken en buurten 2023* (85618NED). StatLine. https://www.cbs.nl/nl-nl/cijfers/detail/85618NED
- CBS (2025). *Nabijheid voorzieningen; afstand locatie, wijk- en buurtcijfers 2023* (85830NED). StatLine. https://www.cbs.nl/nl-nl/cijfers/detail/85830NED
- CBS & Kadaster (2026). *Wijk- en buurtkaart 2023*, version 3. https://www.cbs.nl/nl-nl/dossier/nederland-regionaal/geografische-data/wijk-en-buurtkaart-2023
- Rijden de Treinen (2026). *Open data: railway stations*. CC0. https://www.rijdendetreinen.nl/en/open-data/stations
- Rijden de Treinen (2026). *Open data: train disruptions*. CC BY 4.0. https://www.rijdendetreinen.nl/en/open-data/disruptions
