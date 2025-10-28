# :mortar_board: Photovoltaic System Anomaly Detection

This project focuses on anomaly detection in photovoltaic (PV) systems by combining PCA-based feature reduction, clustering techniques, LSTM time-series prediction, and density-based anomaly detection methods.

---

## :file_folder: Dataset
The dataset used in this project covers the period from **July 2023 to August 2024**, with a **sampling frequency of 15 minutes**.  
It includes measurements from PV inverters and pyranometers (solar irradiance), such as:  
- Active energy injected
- Inverter electrical parameters (voltage, current, power)  
- Solar irradiance measurements  

Data are pre-processed to handle missing values and standardized before analysis.

---

## :toolbox: Methodology

### 1️⃣ PCA + K-means
**Goal:** Group data into homogeneous clusters based on solar irradiance.  

**Steps:**  
1. **Feature selection:** Solar irradiance variables measured by pyranometers (solarimeter) are used.  
2. **Pre-processing:** Data are normalized to standardize variable scales.  
3. **Dimensionality reduction (PCA):**  
   - Reduces the number of features while preserving most of the variance.  
   - Transforms variables into principal components suitable for clustering.  
4. **Clustering (K-means):**  
   - Data are divided into clusters based on solar irradiance.  
   - Each cluster represents similar operating conditions of the PV system.  

**Outcome:** Data are organized into coherent clusters, ready for cluster-specific LSTM analysis.  

---

### 2️⃣ Long Short-Term Memory (LSTM)
**Goal:** Predict active energy injected (target variable) to detect deviations from normal behavior.  

**Steps:**  
1. **Time-series sequence creation:**  
   - Historical inverter data are transformed into temporal sequences.  
   - Target: active energy produced in each time interval.  
2. **Cluster-specific LSTM training:**  
   - An individual LSTM is trained for each cluster generated in the previous step.  
   - The network learns the normal production patterns for each operating condition.  
3. **Prediction and anomaly scoring:**  
   - Predicted values are compared with actual measurements.  
   - Measurements exceeding a predefined error threshold (empirically set) are flagged as anomalies.  

---

### 3️⃣ Interquartile Range (IQR)
**Goal:** Aggregate anomaly detections over hourly intervals to reduce false positives.  

**Steps:**  
1. **Hourly grouping:**  
   - Anomalous measurements detected by LSTM are grouped per hour.  
2. **IQR computation:**  
   - For each hour, the interquartile range of anomaly counts is calculated.  
3. **Interval-level anomaly detection:**  
   - Only hours with an unusually high number of anomalies (outside the IQR range) are flagged as anomalous intervals.  

**Outcome:** This step ensures that transient spikes or isolated errors do not trigger false alarms, focusing only on sustained abnormal behavior.  

---

