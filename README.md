# HexFlow: Demand Forecasting and Supply Repositioning

HexFlow forecasts taxi demand by H3 geographic cell and 15-minute time interval, then recommends where idle vehicles should reposition before demand increases. The project is designed around NYC Taxi and Limousine Commission trip data, weather data, and calendar/event features.

## Project goals

- Forecast completed or requested rides for each H3 hex and 15-minute slot.
- Compare a LightGBM model with a seasonal-naive baseline using WMAPE.
- Convert demand forecasts into practical, capacity-aware repositioning recommendations.
- Simulate the effect of recommendations on rider wait time with a fixed fleet.

## Expected results

The finished project should report forecast quality by hex, time of day, and borough; visualize demand hotspots; and quantify the fleet simulation outcome, including p50 and p90 rider wait time.

## Repository layout

```text
.
├── README.md
├── requirements.txt                 # Python package versions
├── .gitignore                       # Ignore data, models, environments, and secrets
├── configs/
│   ├── data_paths.yaml              # Dataset locations and date ranges
│   ├── model_config.yaml            # LightGBM and validation settings
│   └── simulation_config.yaml       # Fleet and repositioning assumptions
├── data/
│   ├── raw/                         # Downloaded TLC, weather, and event source files
│   ├── interim/                     # Cleaned but not final intermediate datasets
│   ├── processed/                   # Model-ready hex-time feature tables
│   └── external/                    # Reference lookup files, such as H3 boundaries
├── docs/
│   ├── data_dictionary.md           # Feature and source definitions
│   ├── methodology.md               # Modeling and simulation decisions
│   └── results.md                   # Final metrics, charts, and conclusions
├── notebooks/
│   ├── 01_data_exploration.ipynb    # Raw-trip exploration and quality checks
│   ├── 02_feature_engineering.ipynb # Feature validation
│   └── 03_model_evaluation.ipynb    # Metrics, errors, and visualizations
├── src/
│   ├── data/
│   │   ├── download_data.py         # Source-data download entry point
│   │   ├── preprocess_trips.py      # Trip cleaning and H3 aggregation
│   │   └── build_features.py        # Lag, rolling, weather, and event features
│   ├── models/
│   │   ├── train.py                 # Model-training entry point
│   │   ├── evaluate.py              # WMAPE and baseline comparison
│   │   └── predict.py               # Forecast generation
│   ├── optimization/
│   │   ├── reposition.py            # Greedy vehicle-repositioning heuristic
│   │   └── simulate.py              # Fleet and wait-time simulation
│   └── visualization/
│       └── plots.py                 # Reusable maps and performance charts
├── models/                          # Serialized trained models; excluded from Git
├── outputs/
│   ├── figures/                     # Saved charts and maps
│   ├── forecasts/                   # Demand forecast tables
│   └── simulation/                  # Repositioning recommendations and metrics
└── tests/
    ├── test_features.py             # Feature-generation checks
    ├── test_metrics.py              # Metric calculation checks
    └── test_repositioning.py        # Supply-allocation constraints
```

## Data sources

- NYC TLC trip-record data for trip volume, locations, and times.
- H3 for spatial indexing and neighborhood aggregation.
- Historical weather observations and a documented event/calendar dataset.

Raw data must not be committed. Store it under `data/raw/` and document the source URL, extraction date, license, and relevant columns in `docs/data_dictionary.md`.

## Modeling workflow

1. Normalize trip timestamps and map pickup coordinates or taxi zones to an H3 resolution chosen for city-scale forecasting.
2. Aggregate rides to hex-time cells and build time, lag, rolling, weather, and event features.
3. Split the data chronologically, train the baseline and LightGBM models, and compare WMAPE by period and geography.
4. Use forecasts as demand estimates in the repositioning heuristic.
5. Simulate a fixed fleet and publish wait-time and coverage results in `docs/results.md`.

## Reproducibility checklist

- Pin dependencies in `requirements.txt`.
- Keep all run settings in `configs/` rather than notebooks.
- Record seeds, training periods, H3 resolution, and feature definitions.
- Do not commit raw data, trained model binaries, local environment files, or API keys.

## Future deliverables

- A short technical report in `docs/methodology.md`.
- Forecast and allocation visualizations in `outputs/figures/`.
- A Streamlit dashboard in `app.py` only after the modeling pipeline is stable.
