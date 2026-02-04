# 📦 Smart Logistics Delay Prediction – EDA Summary

This documentation summarizes all progress made in the Smart Logistics ML + Analytics project up to this point. The dataset simulates real-time logistics data including asset tracking, environmental sensors, and operational KPIs.

---

## ✅ Project Goals

- **Model 1**: Predict `Logistics_Delay` (0/1)
- **Model 2**: Assess **impact or cause** of delay using customer/asset features
- Create **explainable dashboards** in Power BI

---

## 📊 Dataset Overview

**Rows**: 1000  
**Target**: `Logistics_Delay` (binary)  
**Key Features:**

- Temporal: `Timestamp`
- Geospatial: `Latitude`, `Longitude`
- Operational: `Inventory_Level`, `Waiting_Time`, `Asset_Utilization`
- Customer: `User_Transaction_Amount`, `User_Purchase_Frequency`
- Environmental: `Temperature`, `Humidity`
- Status: `Shipment_Status`, `Traffic_Status`, `Logistics_Delay_Reason`
- Forecasting: `Demand_Forecast`

---

## 🧼 Preprocessing Steps

- Converted `Timestamp` to datetime
- Handled missing values (notably in `Logistics_Delay_Reason`)
- Dropped `Shipment_Status` due to data leakage (`Delayed` overlaps with target)
- Created derived features (see below)

---

## 🧠 Feature Engineering Summary

| Feature Name | Description |
|--------------|-------------|
| `Weather` | Categorical (`Cold`, `Normal`, `Hot`) from `Temperature` & `Humidity` |
| `Demand_Pressure` | = `Demand_Forecast / Inventory_Level` |
| `High_Wait_Flag` | Waiting time in top 25% |
| `Low_Util_Flag` | Utilization in bottom 25% |
| `Geo_Cluster` | KMeans clustering on lat/lon |
| `Distance_From_Center` | Haversine distance from a central depot |
| `Hour`, `Weekday`, `Peak_Hour` | Extracted from `Timestamp` |

---

## 📌 EDA Highlights

### 1. **Target Distribution**
- Class balance is moderately skewed (delay ≈ 55–60%)

### 2. **Bivariate Analysis**
- `Waiting_Time` increases delay risk (above threshold)
- `Traffic_Status = Heavy` → **100% delay rate**
- `Weather = Cold` → Higher delay risk in Cluster 1

---

## 📍 Geospatial Feature Insights

### ✅ `Geo_Cluster` (from Lat/Lon)
- Divided locations into 5 clusters
- Used as a categorical feature
- Delay rates differ across clusters (Cluster 2 ≈ 60%, Cluster 4 ≈ 50%)

### ✅ Delay Rate by `Geo_Cluster` × `Traffic_Status`

| Traffic = Heavy | Delay = 100% in all clusters |
|------------------|-----------------------------|

- Clear regional impact of traffic

### ✅ Delay Rate by `Geo_Cluster` × `Weather`

| Cluster 1 + Cold = 82% delay rate |
| Cluster 3 + Hot = 68% |
| Cluster 4 is stable under all weather |

---

## 📉 Features Dropped (Stage 1)

- `Shipment_Status` (leaks target)
- `Logistics_Delay_Reason` (only used in Stage 2)
- `User_Transaction_Amount`, `User_Purchase_Frequency`, `Asset_Utilization`  
  → **Moved to Stage 2 model for impact/cause prediction**

---

## ✅ Modeling Strategy (Confirmed)

### 🧠 Stage 1: Delay Prediction Model

- Inputs: Operational, Weather, Geo, Time features
- Output: `Logistics_Delay` + probability (risk score)

### 🔎 Stage 2: Delay Impact/Cause Model

- Inputs: `Asset_Utilization`, `User_Transaction_Amount`, `User_Purchase_Frequency`, optional `delay_risk_score`
- Trained **only on delayed rows**
- Output: Delay severity, risk impact or cause

---

## 📌 Ready for Next Steps

- [ ] Finalize feature pipeline
- [ ] Train Model 1: `Logistics_Delay` prediction
- [ ] Generate predictions + probabilities for Power BI
- [ ] Build Model 2 (optional: classify `Logistics_Delay_Reason` or delay impact)

---

_Last updated: [Insert today’s date]_
