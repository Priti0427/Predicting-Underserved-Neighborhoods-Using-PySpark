# Assessing Public Transit Accessibility in Philadelphia and Predicting Underserved Neighborhoods Using PySpark

## Introduction

Public transportation accessibility is a major factor in urban mobility, economic opportunity, and social equity. In cities where transit services are unevenly distributed, some neighborhoods experience limited access to reliable transportation. These areas are often referred to as **transit deserts**.

This project analyzes public transit accessibility across **159 neighborhoods in Philadelphia** using **SEPTA GTFS transit data** and **Philadelphia neighborhood boundary data**. The workflow uses **PySpark** for scalable distributed processing of millions of transit records, engineers neighborhood-level accessibility features, computes a composite accessibility score, and trains machine learning models to identify and predict underserved areas.

The project also explores deployment in a cloud-based distributed environment using **AWS EMR** and **Amazon S3**.

---

## Objectives

This project aims to answer the following questions:

1. Which Philadelphia neighborhoods have the lowest transit accessibility?
2. Which transit-related factors most strongly influence accessibility?
3. Can machine learning models accurately predict accessibility scores using spatial and service-based features?

---

## Dataset Description

### 1. SEPTA GTFS Transit Data
The General Transit Feed Specification (GTFS) data provides detailed information about public transportation services.

Main files used:
- `stops.txt` – transit stop locations
- `stop_times.txt` – arrival and departure schedules
- `trips.txt` – trip definitions
- `routes.txt` – transit route details
- `calendar.txt` – service schedule information

Approximate dataset sizes:
- `stop_times.txt` – **2,733,692 rows**
- `trips.txt` – **46,202 rows**
- `routes.txt` – **182 rows**
- `stops.txt` – **13,855 rows**

### 2. Philadelphia Neighborhood Boundaries
Neighborhood boundary data was loaded in **GeoJSON** format and contains polygon boundaries for **159 Philadelphia neighborhoods**.

---

## Technology Stack

- **Programming:** Python 3.x
- **Distributed Processing:** PySpark 3.5.0
- **Cloud Platform:** AWS EMR, Amazon S3
- **Development Environment:** Google Colab
- **Machine Learning:** PySpark MLlib
- **Visualization:** Folium, Matplotlib, Seaborn
- **Geospatial Processing:** GeoPandas, Shapely, PyProj

---

## Project Workflow

### 1. Data Collection
- Download SEPTA GTFS transit data
- Extract bus and rail GTFS zip files
- Download Philadelphia neighborhood boundary GeoJSON data

### 2. Data Loading
- Load bus and rail transit files into PySpark DataFrames
- Combine bus and rail stops into a unified transit stop dataset
- Load neighborhood polygons using GeoPandas

### 3. Geospatial Preparation
- Reproject neighborhood boundaries for accurate centroid calculation
- Compute neighborhood centroids
- Convert centroids back to latitude and longitude for distance calculations

### 4. Distance Computation
- Perform a **cross join** between neighborhoods and transit stops
- Calculate Haversine distance between each neighborhood centroid and each stop
- Generate a large neighborhood-stop distance dataset for analysis

### 5. Feature Engineering
Neighborhood-level features are generated using spatial and service-based analysis.

### 6. Accessibility Scoring
A composite accessibility score is calculated using normalized transit indicators.

### 7. Machine Learning
Two regression models are trained to predict accessibility scores:
- Random Forest Regressor
- Gradient Boosted Trees Regressor

### 8. Visualization
Create maps and charts to highlight spatial disparities in transit coverage.

### 9. Cloud Deployment
Test scalability of the pipeline using AWS EMR and Amazon S3.

---

## Feature Engineering

The following features were generated for each neighborhood:

- `stops_within_500m` – Number of transit stops within 500 meters
- `stops_within_1km` – Number of transit stops within 1 kilometer
- `stops_within_1500m` – Number of transit stops within 1.5 kilometers
- `min_distance_to_stop_km` – Distance to the nearest transit stop
- `bus_stops_within_1km` – Number of bus stops within 1 kilometer
- `rail_stops_within_1km` – Number of rail stops within 1 kilometer
- `unique_routes_within_1km` – Number of distinct transit routes within 1 kilometer
- `total_trips_within_1km` – Total daily trips available within 1 kilometer
- `avg_trips_per_stop` – Average service frequency per stop
- `distance_to_center_city_km` – Distance from neighborhood centroid to Center City Philadelphia

The distance computation involved approximately **2.2 million neighborhood-stop comparisons**.

---

## Accessibility Score

A composite accessibility score was computed using a weighted combination of normalized features.

### Formula

Accessibility Score =  
`0.25 * Stop Density + 0.25 * Route Diversity + 0.25 * Trip Frequency + 0.15 * Proximity to Center City`

Where:
- Higher stop density improves accessibility
- More route diversity improves accessibility
- Higher trip frequency improves accessibility
- Lower distance to Center City improves accessibility
- Lower distance to nearest stop improves accessibility

The final score ranges from **0 to 1**, where higher values indicate better transit accessibility.

---

## Machine Learning Approach

### Models Used
- **Random Forest Regressor**
- **Gradient Boosted Trees Regressor**

### Preprocessing Pipeline
- `VectorAssembler` to combine input features
- `StandardScaler` to normalize features
- Train-test split: **80% training / 20% testing**

### Input Features for Modeling
- `stops_within_500m`
- `stops_within_1km`
- `stops_within_1500m`
- `min_distance_to_stop_km`
- `bus_stops_within_1km`
- `rail_stops_within_1km`
- `unique_routes_within_1km`
- `total_trips_within_1km`
- `avg_trips_per_stop`
- `distance_to_center_city_km`

### Target Variable
- `accessibility_score`

---

## Model Results

### Random Forest Performance
- **RMSE:** 0.0263
- **MAE:** 0.0202
- **R²:** 0.9711

### Gradient Boosted Trees Performance
- **RMSE:** 0.0376
- **MAE:** 0.0294
- **R²:** 0.9410

### Conclusion
The **Random Forest model** performed better than Gradient Boosted Trees and explained approximately **97.1% of the variance** in accessibility scores.

---

## Feature Importance

The most important features in predicting accessibility were:

- **Stops within 1 km** – 23.3%
- **Bus stops within 1 km** – 19.5%
- **Total trips within 1 km** – 19.5%
- **Stops within 1.5 km** – 14.6%

This indicates that **stop density** and **service frequency** are the strongest drivers of neighborhood transit accessibility.

---

## Visualizations

This project includes the following visualizations:

1. **Transit stop density heatmap**
2. **Choropleth map of accessibility scores**
3. **Top 15 underserved neighborhoods bar chart**
4. **Feature correlation matrix**
5. **Prediction vs actual comparison plots**

These visualizations help reveal strong spatial disparities between central and peripheral neighborhoods in Philadelphia.

---

## AWS EMR Deployment

To demonstrate cloud scalability, the pipeline was deployed on **Amazon EMR**.

### Cluster Configuration
- **EMR Version:** emr-7.0.0
- **Master Node:** m5.xlarge
- **Core Nodes:** 2 × m5.large
- **Applications:** Spark, Hadoop
- **Storage:** Amazon S3

This setup was intended to support large-scale distributed analysis and future expansion to larger or multi-city datasets.

---

## Project Structure

```bash
.
├── Cloud_Computing_Project.ipynb
├── Final report.docx.pdf
├── septa_gtfs/
│   ├── bus/
│   └── rail/
├── philadelphia_neighborhoods.geojson
├── accessibility_results.csv
├── model_predictions.csv
└── README.md
