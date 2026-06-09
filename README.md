# Data-Driven-Bed-Capacity-Patient-flow-Optimisation-for-Hospital-X

*Power BI Interactive Dashboard*

This repository contains the analytical workflow, data model, and documentation for the **Hospital X Inpatient Utilisation & Bed Capacity Dashboard**, an interactive Power BI report designed to support hospital operations, capacity planning, and patient‑flow optimisation.

---

## 🏥 **1. Overview**

The dashboard provides a data‑driven view of inpatient activity at **Hospital X**, focusing on:

- Admission patterns  
- Length of Stay (LOS)  
- Bed‑day utilisation  
- Annual bed requirements  
- Specialty‑level performance  
- Patient‑flow insights  

The report is fully interactive and supports year‑based filtering through a dedicated **DimDate** table.

---

## 📊 **2. Key Features**

### **2.1 Total Admissions**
Displays the number of inpatient episodes within the selected period.

### **2.2 Average Length of Stay (LOS)**
Measures the average number of days patients remain admitted.

### **2.3 Total Bed Days**
Calculated as:

```
Total Bed Days = SUM(Admissions[Length of Stay])
```

Represents total occupied bed‑days.

### **2.4 Beds Required (Annualised)**
Estimates the average number of beds needed per day:

\[
\text{Beds Required} = \frac{\text{Total Bed Days}}{\text{Number of Days in Selected Year}}
\]

This dynamically adjusts based on slicer selection.

### **2.5 Admission Source Breakdown**
Shows where demand originates (ED, GP, transfers, etc.).

### **2.6 LOS Distribution**
Highlights high‑impact specialties contributing to bed pressure.

---

## 🗂️ **3. Data Model**

### **Fact Table: Admissions**
Contains:
- AdmissionDate  
- DischargeDate  
- ModifiedDate  
- LengthOfStay  

### **Dimension Table: DimDate**
Contains:
- Date  
- Year  
- Month  
- Quarter  

### **Relationships**
| From | To | Status | Purpose |
|------|-----|--------|----------|
| DimDate[Date] | Admissions[AdmissionDate] | **Active** | Drives slicers & time intelligence |
| DimDate[Date] | Admissions[DischargeDate] | Inactive | Used only via `USERELATIONSHIP` |
| DimDate[Date] | Admissions[ModifiedDate] | Inactive | Optional for audit/metadata |

This ensures correct filtering and avoids ambiguous date relationships.

<img width="2011" height="988" alt="image" src="https://github.com/user-attachments/assets/a44997aa-c58d-4c0f-92e9-594f3aaaa23f" />


## 🎛️ **4. Interactivity**

### **Year Slicer**
Selecting a year filters:
- Admissions  
- LOS  
- Bed Days  
- Beds Required  

If no year is selected, the dashboard displays **multi‑year averages**.

### **Hover Tooltips**
Provide contextual insights such as:
- LOS per specialty  
- Admission source contribution  
- Bed‑day impact  

---

## 🧭 **5. Interpretation Guide**

### **High Bed Requirements**
May indicate:
- High admission volume  
- Long LOS  
- Inefficient discharge processes  
- Seasonal surges  

### **High LOS**
Often linked to:
- Delayed diagnostics  
- Complex case mix  
- Discharge bottlenecks  

### **Admission Source Trends**
Help identify:
- High‑pressure entry points  
- Opportunities for community‑based interventions  

---

# 🧩 **6. Technical Appendix**

This section documents the **DAX measures**, **data modelling decisions**, and **logic** used in the dashboard.

---

## 📐 **6.1 Calendar Table (DimDate)**

Created using:

```DAX
DimDate =
CALENDAR(
    MIN(Admissions[AdmissionDate]),
    MAX(Admissions[DischargeDate])
)
```

Additional columns:

```DAX
Year = YEAR(DimDate[Date])
Month = FORMAT(DimDate[Date], "MMMM")
Quarter = "Q" & FORMAT(DimDate[Date], "Q")
```


## 📏 **6.2 Core Measures**

### **Total Bed Days**
```DAX
Total Bed Days =
SUM(Admissions[Length of Stay])
```

### **Beds Required (Dynamic)**
```DAX
Beds Required =
DIVIDE(
    [Total Bed Days],
    DISTINCTCOUNT(DimDate[Date])
)
```

### **Beds Required at 85% Occupancy**
```DAX
Beds Required (85%) =
DIVIDE(
    [Total Bed Days],
    DISTINCTCOUNT(DimDate[Date]) * 0.85
)
```



## 🔄 **6.3 Discharge‑Based Measures (Using Inactive Relationship)**

Example:

```DAX
Discharges by Year =
CALCULATE(
    COUNTROWS(Admissions),
    USERELATIONSHIP(DimDate[Date], Admissions[DischargeDate])
)
```



## 🧮 **6.4 Length of Stay Calculation**

```DAX
Length of Stay =
DATEDIFF(Admissions[AdmissionDate], Admissions[DischargeDate], DAY)
```



## 🧱 **6.5 Data Modelling Rationale**

- **AdmissionDate** is the active relationship because it is the industry‑standard anchor for inpatient episodes.
- **DischargeDate** and **ModifiedDate** remain inactive to avoid ambiguous filtering.
- DimDate ensures:
  - Correct year filtering  
  - Leap‑year handling  
  - Accurate time intelligence  



# 📄 **7. Live Dashboard**

The interactive Power BI report is available here:

👉 **Hospital X Dashboard**  
[https://app.powerbi.com/view?r=eyJrIjoiNGMxYWI0ZDYtNjViZS00Zjg0LWJlYTYtZWZlODA5Y2FjMzBmIiwidCI6ImI4MDRmYTYxLTUwYTctNDM3My1iOGNmLTI3YzQyNDcwZTYyNSIsImMiOjZ9](https://app.powerbi.com/view?r=eyJrIjoiNGMxYWI0ZDYtNjViZS00Zjg0LWJlYTYtZWZlODA5Y2FjMzBmIiwidCI6ImI4MDRmYTYxLTUwYTctNDM3My1iOGNmLTI3YzQyNDcwZTYyNSIsImMiOjZ9)

