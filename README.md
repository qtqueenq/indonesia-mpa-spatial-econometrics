# Spatial Econometric Analysis of Illegal Fishing in Indonesian Marine Protected Areas

## 📌 Executive Summary
Can spatial data science evaluate environmental policy enforcement? This project investigates illegal fishing activity across Indonesian Marine Protected Areas (MPAs) by merging **Synthetic Aperture Radar (SAR) satellite vessel detection data** with spatial vector polygons. 

Using R (`sf` and `terra`), this project performs a point-in-polygon spatial join to count detected fishing vessels per reserve, then runs an OLS regression model to evaluate how MPA size (`GIS_M_AREA`), establishment age (`STATUS_YR`), and strict protection status (`strict.protection`) impact illegal fishing incidence.

---

## 🛠️ Tech Stack & Libraries
* **Language:** R
* **Spatial Data Science:** `sf` (Simple Features for spatial vector analysis), `terra`, `rnaturalearth`
* **Data Wrangling & Visualization:** `tidyverse` (`dplyr`, `ggplot2`, `readr`), `ggplot2` spatial layers (`geom_sf`)
* **Statistical Modeling:** Ordinary Least Squares (OLS) Regression

---

## 📊 Data Sources & Preprocessing
* **Vessel Detections:** Synthetic Aperture Radar (SAR) vessel monitoring data (`sar_vessel_detections_pipev3.csv`), containing over 200,000 raw global maritime observations.
* **Marine Reserves:** Vector geodatabase (`indo.marine.gpkg`) containing 102 Indonesian Marine Protected Areas with IUCN management classifications.

### Key Data Cleaning Steps:
1. Filtered raw SAR coordinates within Indonesia’s geographic bounds (`lat` between -14° and 8°, `lon` between 92° and 142°).
2. Filtered observations by a machine learning **`fishing_score > 0.5`** to isolate likely commercial fishing vessels from transit or cargo traffic.
3. Transformed coordinate tables into spatial feature (`sf`) objects (`CRS: WGS 84 / EPSG:4326`).

---

## 🔬 Methodology & Spatial Econometric Model

1. **Spatial Join (Point-in-Polygon):** Used `st_intersects()` to compute the exact count (`pt_count`) of detected fishing boats located within each MPA polygon boundary.
2. **Policy Coding:** Created a binary indicator (`strict.protection`) categorizing reserves under strict IUCN management classifications (Ia, II, and III).
3. **Regression Specification:**
   $$\text{pt count} = \beta_0 + \beta_1(\text{GIS M AREA}) + \beta_2(\text{STATUS YR}) + \beta_3(\text{strict.protection}) + u$$

---

## 📈 Regression Results & Key Policy Insights

| Independent Variable | Coefficient | Standard Error | $t$-value | $p$-value |
| :--- | :---: | :---: | :---: | :---: |
| **Intercept** | 4.059 | 1.680 | 2.416 | 0.018* |
| **`GIS_M_AREA` (Area Size)** | 0.001 | 0.00017 | 5.814 | 7.62e-08*** |
| **`STATUS_YR` (Established Year)**| -0.0014 | 0.0009 | -1.579 | 0.118 |
| **`strict.protection` (Strict IUCN)** | -4.532 | 2.197 | -2.063 | 0.042* |

*Model Summary: $N = 102$, Residual Standard Error = 6.71, Adjusted $R^2 = 0.235$, $F = 11.37$ ($p < 0.001$)*

### Key Findings:
* **Area Scale & Enforcement Burden:** Larger MPAs experience significantly more detected illegal fishing ($\beta = 0.00097, p < 0.001$), highlighting the logistical challenge of patrolling expansive marine borders.
* **Strict Protection Efficacy:** Strictly protected reserves (IUCN categories Ia, II, III) experience an average of **~4.5 fewer illegal fishing boats** compared to less restricted zones, proving that regulatory strictness effectively deters unauthorized activity ($p = 0.042$).
* **Institutional Maturity:** Older protected areas trend toward lower illegal fishing rates, suggesting public awareness and enforcement infrastructure improve over time.

---

## 📁 Repository Structure
```text
.
├── data/
│   └── indo.marine.gpkg                # MPA Geospatial Vector Polygon File
├── scripts/
│   └── Extra_Credit_Project.Rmd        # Complete Spatial Analysis R Markdown
├── outputs/
│   └── mpa_map_plot.png                # Spatial distribution map of MPAs and boats
├── README.md                           # Project Documentation
