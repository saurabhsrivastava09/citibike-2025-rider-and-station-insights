# Citi Bike 2025 Data Processing

This project contains multiple Python scripts that process raw **Citi Bike 2025 trip data** into optimized, analytics-ready datasets for **Tableau dashboards**.  
The workflow is designed to handle large volumes of trip data while addressing **row limitations in free BI tools**.

---

## Project Overview

The scripts process Citi Bike’s 2025 monthly raw CSV files by:
- Merging and standardizing schemas
- Computing ride durations and travel distances
- Aggregating rider behavior and station-level metrics
- Optimizing outputs for dashboard consumption

The final datasets support two primary dashboards:
- **Rider Behavior Overview**
- **Station Network Overview**

Due to BI tool constraints, some full-detail datasets are generated but not used, and **Top-100 station filtering** is applied where necessary.

---

## Data Processing Workflow

Each script follows a consistent processing pattern:

1. Scan directories to identify all monthly CSV files  
2. Read CSVs using `low_memory=False`  
3. Reindex columns to a standard schema:
   - `ride_id`, `rideable_type`
   - `started_at`, `ended_at`
   - start/end station names and IDs
   - latitude/longitude
   - `member_casual`
4. Convert `started_at` and `ended_at` to datetime
5. Derive time features:
   - `month_year`, `month_name`
   - `year`, `month`
   - `hour_started_at`
6. Concatenate monthly data into a single DataFrame
7. Remove records with missing timestamps or station information
8. Compute ride duration:
   - `ride_duration_sec = (ended_at - started_at).total_seconds()`
   - Filter out non-positive durations
9. Calculate trip distance using the **Haversine formula**
   - Earth radius: **3959 miles**
10. Aggregate or filter data based on dashboard requirements

---

## Script Details

### Script 1: Rider Behavior Overview
Generates `citibike_2025_daily_hourly_stations.csv` for the **Rider Behavior Overview** dashboard.

- Groups by:
  - `month_year`, `month_name`
  - `hour_started_at`
  - `start_station_name`
  - `member_casual`
- Aggregates:
  - Trip counts
  - Ride duration (sum, mean, median)
  - Distance traveled (sum)
- Converts durations from seconds to minutes

---

### Script 2: Validation Checks
Used to validate aggregated results by spot-checking raw data.

- Filters data by:
  - Specific month (e.g., `202501`)
  - Hour
  - Station
- Outputs `merged_filtered.csv` to manually verify aggregation accuracy

---

### Script 3: Full Station Flows (Unused)
Creates `citibike_2025_station_flows_hourly.csv` with full start-to-end station flows.

- Includes:
  - `month_year`
  - `hour_started_at`
  - `member_casual`
- Not used due to excessive row counts exceeding BI tool limits

---

### Script 4: Combined Rider & Station Dataset (Unused)
Generates:
- `citibike_2025_cleaned_full.csv`
- `citibike_2025_daily_hourly_stations.csv`

This is the **preferred dataset** as it supports both dashboards, but it is not used due to BI tool row constraints.

---

### Script 5: Top 100 Stations
Filters data to only the **Top 100 start and end stations**.

- Outputs `citibike_2025_station_flows_TOP100.csv`
- Used for the **Station Network Overview** dashboard
- Optimized with memory-efficient data types (e.g., `int8`)

---

### Script 6: Stations Lookup
Creates a station reference table for mapping and joins.

- Extracts unique station names from `citibike_2025_cleaned_full.csv`
- Rounds latitude and longitude to 6 decimal places for deduplication
- Outputs `citibike_2025_stations_lookup.csv`

---

## Output Files

| File Name | Purpose | Used In |
|---------|--------|--------|
| `citibike_2025_cleaned_full.csv` | Fully cleaned trip-level dataset | Stations lookup input |
| `citibike_2025_daily_hourly_stations.csv` | Hourly rider behavior aggregates | Rider dashboard |
| `citibike_2025_station_flows_TOP100.csv` | Top 100 station flow data | Station dashboard |
| `citibike_2025_stations_lookup.csv` | Station latitude/longitude reference | Maps & joins |

---

## 📊 Dashboards & Visualizations

Two interactive dashboards analyze **Citi Bike 2025 trip data (January–November)**, focusing on **rider behavior patterns** and **station network dynamics**.

**Dashboards can be viewed here:**  
**[Tableau Dashboard Link – Coming Soon]**

---

### Home Page

The Home Page provides context and navigation for the analysis.

- Brief overview of Citi Bike’s evolution since 2013  
- Key guiding questions:
  - When and where are trips busiest?
  - How long and how far do riders travel?
  - Which stations dominate the network?
- Navigation links to:
  - **Rider Behavior Overview**
  - **Station Network Overview**

---

### Rider Behavior Overview

This dashboard focuses on **who is riding, when they ride, and how ride behavior differs** between members and casual users.

**Key Visuals**
- **Bar Charts**
  - Trips per month and year
  - Trips by season with member vs. casual split
- **Line Charts**
  - Hourly utilization patterns at start stations
- **Summary KPI Cards**
  - Average ride duration
  - Average distance traveled
  - Comparison by rider type

**Insights Highlighted**
- Seasonal and monthly demand trends  
- Peak riding hours and daily patterns  
- Behavioral differences between members and casual riders  

---

### Station Network Overview

This dashboard explores **where trips start and end**, highlighting the most active stations and travel flows.

**Key Visuals**
- **Dual Maps**
  - Start and end stations by month and rider type
  - Station popularity (Top stations highlighted in red)
- **Bar & Line Charts**
  - Top 10 start stations vs. end stations
  - Hourly utilization between popular station pairs
  - Most frequent station-to-station trips

**Insights Highlighted**
- Dominant stations across seasons  
- High-volume station pairs and return trips  
- Spatial density concentrated in Manhattan and Jersey City  

---

### Key Visualizations, Insights & Filters

| Visualization | Key Insights | Filters |
|--------------|------------|--------|
| Popular Start / End Stations | W 20 St & 7 Ave consistently leads; noticeable seasonal shifts | Month/Year, Member/Casual |
| Trips Between Stations | High same-station return trips; top station flows identified | Hour, Month |
| Geographic Maps | Strong trip density in Manhattan and Jersey City | Season, Rider Type |

---

## Tools & Technologies
- **Python (Pandas, NumPy)**
- **Tableau**
- **Haversine Distance Calculation**
- **Optimized CSV Outputs for BI Tools**

---

## Notes & Limitations
- Free BI tool row limits influenced dataset design
- Full-detail station flow datasets are generated but not used
- Top-100 station filtering ensures dashboard performance and usability

---

## Future Enhancements
- Migration to a BI tool without row limitations
- Incremental processing using Parquet or DuckDB
- Integration with cloud storage (S3/GCS)

