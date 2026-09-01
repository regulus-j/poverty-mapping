# Poverty Mapping in Zamboanga City

An end-to-end geospatial machine learning system for estimating poverty at a fine spatial scale across Zamboanga City, Philippines.

The project combines socioeconomic indicators with satellite imagery, nighttime lights, population data, OpenStreetMap features, terrain, rainfall, and other geospatial variables to produce poverty estimates at approximately **1 km² grid-cell resolution**.

> **Research project:** Poverty Mapping in Zamboanga City with Socioeconomic and Geospatial Analysis

## Overview

Official poverty statistics are commonly reported at administrative levels such as municipalities, cities, or barangays. This makes it difficult to identify smaller areas where poverty may be concentrated.

This project addresses that problem through **small-area poverty mapping**. Barangay-level socioeconomic information is combined with remotely sensed and geospatial predictors, then disaggregated to a regular grid covering Zamboanga City.

The workflow includes:

1. Collecting socioeconomic and geospatial datasets
2. Preprocessing and harmonizing spatial data
3. Generating remote-sensing and spatial features
4. Performing feature selection and multicollinearity analysis
5. Training machine learning models
6. Applying spatial validation
7. Disaggregating estimates to 1 km² grid cells
8. Producing maps and analysis outputs

## Research Objective

The primary objective is to estimate the spatial distribution of poverty across Zamboanga City at a substantially finer resolution than the available official socioeconomic statistics.

The system focuses on identifying relationships between poverty and observable geographic proxies such as:

- Vegetation
- Built-up development
- Surface water
- Nighttime illumination
- Population distribution
- Building and road density
- Accessibility to services
- Terrain
- Rainfall
- Spatial texture and land-cover characteristics

## Key Features

- **1 km² spatial grid** covering Zamboanga City
- **100+ barangay boundaries** used as administrative reference units
- **70+ geospatial and remote-sensing features**
- Satellite-derived environmental indicators
- Nighttime-light features
- Population and infrastructure proxies
- Spatial feature engineering
- Machine learning-based poverty estimation
- Spatial cross-validation
- Fine-scale poverty visualization
- Reproducible geospatial processing pipeline

## Methodology

### Spatial Framework

The study area is divided into approximately **1 km × 1 km grid cells**.

The grid is aligned to **UTM Zone 51N (EPSG:32651)** to provide a consistent projected coordinate system for spatial operations and distance-based calculations.

### Data Sources

The project integrates multiple datasets, including:

| Dataset | Purpose |
|---|---|
| PSA socioeconomic indicators | Poverty-related target information |
| Sentinel-2 | Optical and land-surface features |
| Landsat 8/9 | Additional spectral information |
| VIIRS DNB | Nighttime-light / economic activity proxies |
| MODIS NDVI | Vegetation characteristics |
| CHIRPS | Rainfall |
| SRTM DEM | Elevation and terrain |
| WorldPop | Population distribution |
| OpenStreetMap | Roads, buildings, services, and infrastructure |
| DSWD / DHS spatial data | Supporting socioeconomic and geographic information |

## Feature Engineering

The feature engineering pipeline generates both direct and derived spatial predictors.

Examples include:

### Spectral / Environmental Features

- NDVI
- NDBI
- NDWI
- SAVI
- Elevation
- Rainfall

### Nighttime-Light Features

- Mean nighttime radiance
- Local nighttime-light statistics
- Spatially aggregated nighttime-light features
- CNN-derived nighttime-light representations

### Built Environment

- Building density
- Road density
- Infrastructure density
- Distance to services
- Accessibility indicators

### Texture Features

Texture-based features are derived from remotely sensed imagery using GLCM-style statistics to capture spatial patterns that may not be represented by simple spectral averages.

### Spatial Features

The pipeline also supports:

- K-nearest-neighbor spatial imputation
- Multi-scale neighborhood aggregates
- Spatial trend features
- Local statistics
- Interaction features

Multiple neighborhood radii can be used to capture geographic context at different scales.

## Machine Learning

Several models were investigated during development, including:

- Random Forest
- CatBoost
- LightGBM
- Convolutional Neural Networks
- EfficientNetV2-based feature extraction

### Random Forest Baseline

The baseline Random Forest model uses parameters such as:

```text
n_estimators = 200
max_depth = 12
random_state = 42
```

### CatBoost

CatBoost is used for nonlinear tabular prediction and spatial poverty disaggregation.

The project also experimented with conservative configurations to reduce overfitting, including lower tree depth, smaller learning rates, and stronger L2 regularization.

### CNN / EfficientNetV2

An EfficientNetV2 model is used to extract learned representations from nighttime-light imagery.

These CNN-derived features can then be incorporated into the tabular geospatial modeling pipeline.

## Spatial Validation

Because neighboring grid cells are spatially correlated, conventional random train/test splitting can produce overly optimistic performance estimates.

The project therefore uses **spatial cross-validation**, including contiguous spatial groups, to better evaluate how models perform when predicting geographically distinct areas.

Evaluation metrics include:

- **R²**
- **MAE**
- **RMSE**

## Disaggregation Approach

The target socioeconomic information is available at a coarser administrative level than the desired prediction grid.

The project therefore separates:

**Target information**

from

**Fine-scale predictors**

The machine learning pipeline uses environmental, infrastructural, demographic, and remotely sensed variables to estimate the spatial distribution of the socioeconomic target within administrative areas.

A dasymetric mapping approach is used to allocate coarse-area information using spatially varying auxiliary information such as:

- Population
- Nighttime lights
- Building density
- Accessibility
- Other geospatial predictors

This allows the resulting estimates to preserve the broader administrative-level information while producing a more detailed spatial surface.

## Project Architecture

At a high level, the system follows this pipeline:

```text
                    ┌─────────────────────┐
                    │   Raw Data Sources  │
                    └──────────┬──────────┘
                               │
             ┌─────────────────┼─────────────────┐
             │                 │                 │
             ▼                 ▼                 ▼
       Socioeconomic       Satellite        Geospatial
          Data               Data              Data
             │                 │                 │
             └─────────────────┼─────────────────┘
                               ▼
                    ┌─────────────────────┐
                    │ Data Preprocessing  │
                    └──────────┬──────────┘
                               ▼
                    ┌─────────────────────┐
                    │ Feature Engineering  │
                    └──────────┬──────────┘
                               ▼
                    ┌─────────────────────┐
                    │ Feature Selection    │
                    └──────────┬──────────┘
                               ▼
                    ┌─────────────────────┐
                    │ ML Model Training    │
                    └──────────┬──────────┘
                               ▼
                    ┌─────────────────────┐
                    │ Spatial Validation   │
                    └──────────┬──────────┘
                               ▼
                    ┌─────────────────────┐
                    │ Spatial Disaggregation│
                    └──────────┬──────────┘
                               ▼
                    ┌─────────────────────┐
                    │ Poverty Map Outputs  │
                    └─────────────────────┘
```

## Technology Stack

### Programming

- Python 3.11
- SQL where applicable

### Data Science

- NumPy
- pandas
- scikit-learn
- CatBoost
- LightGBM
- TensorFlow / Keras

### Geospatial

- GeoPandas
- Rasterio
- geemap
- Google Earth Engine
- QGIS

### Infrastructure

- Docker
- Git / GitHub

## Repository Structure

The exact repository structure may evolve during development, but the project is organized around the following logical components:

```text
poverty-mapping/
├── assets/              # Input datasets and derived assets
├── data/                # Processed data where applicable
├── notebooks/           # Exploratory analysis and experiments
├── scripts/             # Data processing and modeling scripts
├── models/              # Trained model artifacts
├── output/              # Generated predictions and geospatial outputs
├── frontend/            # Application interface, if enabled
├── backend/             # Backend/API components, if enabled
├── requirements.txt     # Python dependencies
└── README.md
```

## Installation

Clone the repository:

```bash
git clone https://github.com/regulus-j/poverty-mapping.git
cd poverty-mapping
```

Create a Python environment:

```bash
conda create -n povertymapping python=3.11
conda activate povertymapping
```

Install the Python dependencies:

```bash
pip install -r requirements.txt
```

For a reproducible containerized environment, use the project's Docker configuration if available:

```bash
docker build -t poverty-mapping .
```

## Google Earth Engine

Some preprocessing and remote-sensing workflows rely on Google Earth Engine.

You will need an authenticated Earth Engine account with access to the datasets used by the project.

A typical Python initialization is:

```python
import ee

ee.Initialize()
```

Depending on the execution environment, Earth Engine authentication may need to be completed first.

## Running the Pipeline

A typical workflow is:

```text
1. Prepare raw datasets
2. Generate the spatial grid
3. Process satellite and geospatial datasets
4. Build the feature table
5. Perform feature selection
6. Train the machine learning model
7. Run spatial validation
8. Perform spatial disaggregation
9. Export predictions
10. Visualize the resulting poverty surface
```

Example model workflow:

```python
from catboost import CatBoostRegressor

model = CatBoostRegressor(
    iterations=300,
    learning_rate=0.02,
    depth=3,
    l2_leaf_reg=30,
    verbose=False
)

model.fit(X_train, y_train)
predictions = model.predict(X_test)
```

## Important Modeling Principle

The project maintains a strict separation between the socioeconomic target and the fine-scale predictors.

```text
Target
  └── Socioeconomic / poverty information

Predictors
  ├── Satellite imagery
  ├── Nighttime lights
  ├── Population
  ├── Buildings
  ├── Roads
  ├── Accessibility
  ├── Terrain
  ├── Rainfall
  └── Other geospatial variables
```

This separation helps reduce target leakage and keeps the disaggregation model focused on observable spatial characteristics.

## Outputs

The pipeline can produce:

- Grid-level poverty estimates
- GeoJSON / geospatial prediction layers
- Model evaluation metrics
- Feature importance results
- Spatial validation results
- Intermediate feature datasets
- Poverty maps for visualization

## Research Context

This repository supports the research project:

**Poverty Mapping in Zamboanga City with Socioeconomic and Geospatial Analysis**

The study investigates whether remotely sensed and geospatial information can be used to estimate the spatial distribution of poverty below the resolution of commonly available official statistics.

The resulting maps are intended for **research and analytical purposes**, not as a replacement for official poverty statistics or household-level assessments.

## Limitations

Several limitations should be considered when interpreting the results:

- Socioeconomic indicators are available at a coarser spatial scale than the prediction grid.
- Some socioeconomic variables are collected only periodically.
- Remote-sensing variables are proxies and do not directly measure household welfare.
- Spatial autocorrelation can affect model evaluation.
- Fine-scale estimates contain modeling uncertainty.
- Data quality and spatial coverage vary between source datasets.

## Future Improvements

Potential extensions include:

- Bayesian small-area estimation
- Fay-Herriot models with spatial random effects
- Conditional autoregressive spatial models
- Uncertainty-aware poverty maps
- Improved temporal modeling
- Higher-resolution imagery
- Additional socioeconomic indicators
- Interactive web-based mapping
- Automated model retraining
- Model explainability and uncertainty visualization

## Citation

If you use this project in academic work, please cite the associated research project:

```text
Al Badi, J. A., Regalado, J. J. (2026).
Poverty Mapping in Zamboanga City with Socioeconomic and Geospatial Analysis.
Western Mindanao State University.
```

## Disclaimer

This project is an academic and research-oriented geospatial modeling system. Predictions represent model-based estimates derived from available socioeconomic and geospatial data. They should not be interpreted as official poverty statistics or as definitive classifications of individual households.

## Author

**Jamal Al Badi**
**Jerard Regalado**


Bachelor of Computer Science  
Western Mindanao State University

J. A. Al Badi, J. J. Regalado, S. M. A. Bobon, M. O. Francisco and M. A. Lines, "Poverty Mapping in Zamboanga City using Geospatial Analysis and Ensemble Models," 2026 7th International Conference on Intelligent Communication Technologies and Virtual Mobile Networks (ICICV), Tirunelveli, India, 2026, pp. 1504-1510, doi: 10.1109/ICICV68925.2026.11554739. keywords: {Modeling;Machine learning;Cells (biology);Machining;Printing;Satellites;Urban areas;Convolutional neural networks;Random forests;Training;Poverty Mapping;Zamboanga City;Machine Learning Models;Geospatial Analysis},
