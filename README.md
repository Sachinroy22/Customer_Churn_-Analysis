# Customer Churn Data Analysis Dashboard

## 📌 Project Overview

This project is an end-to-end **Customer Churn Data Analysis** solution designed to clean customer data, perform analytical transformations, store the prepared data in MySQL, run SQL-based business analysis, and present the results through an interactive **Power BI dashboard**.

The project follows this pipeline:

**Excel / Raw Churn Data → Python (Pandas) → Data Cleaning → Feature Engineering → Clean CSV → MySQL → SQL Analysis → Power BI Dashboard**

---

## 🎯 Business Objective

Customer churn directly affects recurring revenue and customer retention. The objective of this project is to:

- Measure the overall customer churn rate.
- Identify the number of churned and retained customers.
- Analyze churn across contract and subscription types.
- Analyze churn by geography, internet service, payment method, and senior-citizen status.
- Understand customer tenure and monthly-charge patterns.
- Calculate customer value using monthly charges and tenure.
- Identify high-value customers.
- Provide an interactive dashboard for business-oriented exploration.

---

## 🛠️ Technologies Used

| Technology | Purpose |
|---|---|
| Python | Data preparation and transformation |
| Pandas | Data cleaning and feature engineering |
| NumPy | Conditional transformations and numerical operations |
| Jupyter Notebook | Data analysis workflow |
| MySQL | Structured storage and SQL analysis |
| SQL | Business queries and aggregations |
| SQLAlchemy | Loading the cleaned dataset into MySQL |
| PyMySQL | MySQL connectivity |
| Power BI | Interactive dashboard and visualization |
| Excel / CSV | Source and cleaned data formats |

---

## 🔄 Project Workflow

### 1. Data Loading

The raw churn dataset is loaded into Python using Pandas.

```python
df = pd.read_excel("Churn_Unclean_Project.xlsx")
```

Initial inspection includes:

```python
df.head()
df.info()
df.describe()
df.shape
```

### 2. Data Cleaning

The notebook performs:

- Missing-value checks.
- Duplicate detection and removal.
- Replacement of `N/A`, `NULL`, empty strings, and spaces with missing values.
- Removal of unnecessary spaces.
- Text standardization.
- Churn-value standardization.
- Numeric type conversion.
- Removal of invalid ages outside 18–100.
- Removal of negative monthly and total charges.
- Date conversion.
- Filling selected missing values.

Example:

```python
df.drop_duplicates(inplace=True)

df.replace(["N/A", "NULL", "", " "], np.nan, inplace=True)

df = df.apply(
    lambda x: x.str.strip() if x.dtype == "object" else x
)
```

---

## 🧮 Feature Engineering

### Customer Value

```python
df["Customer_Value"] = (
    df["Monthly_Charges"] * df["Tenure_Months"]
)
```

A simple customer-value metric based on monthly charges and tenure.

### Monthly Revenue

```python
df["Monthly_Revenue"] = df["Monthly_Charges"]
```

### Tenure Group

Customers are grouped into:

- `0-12 month`
- `13-24 month`
- `25-48 month`
- `49-72 month`

### Senior Flag

```python
df["Senior_Flag"] = np.where(
    df["Age"] >= 60,
    "Senior",
    "Adult"
)
```

### Churn Flag

```python
df["Churn_Flag"] = df["Churn"].map({
    "Yes": 1,
    "No": 0
})
```

---

## 📊 Clean Dataset

The processed dataset is exported as:

```text
Clean_Churn_Data.csv
```

The current uploaded version contains **492 rows and 23 columns**.

Important fields include:

- `Customer_ID`
- `Customer_Name`
- `Gender`
- `Age`
- `State`
- `City`
- `Tenure_Months`
- `Subscription_Type`
- `Monthly_Charges`
- `Total_Charges`
- `Contract_Type`
- `Payment_Method`
- `Internet_Service`
- `Tech_Support`
- `Senior_Citizen`
- `Dependents`
- `Churn`
- `Last_Interaction_Date`
- `Customer_Value`
- `Monthly_Revenue`
- `Tenure_Group`
- `Senior_Flag`
- `Churn_Flag`

### Current CSV Snapshot

| Metric | Value |
|---|---:|
| Rows | 492 |
| Columns | 23 |
| Customers with known churn status | 490 |
| Churned customers | 116 |
| Retained customers | 374 |
| Churn rate among known statuses | 23.67% |
| Total Revenue / Total Charges | 24,779,081.88 |
| Average Monthly Charges | 1,359.22 |
| Average Tenure | 36.06 months |

> **Important:** The supplied Power BI dashboard screenshot shows 445 total customers, 105 churn customers, 340 retained customers, a 23.60% churn rate, 22.16M total revenue, 1.36K average monthly charges, and 35.95 average tenure. The current `Clean_Churn_Data.csv` contains 492 rows and 2 records with missing `Churn` values. Therefore, the screenshot and current CSV are not an exact snapshot of the same data state. Refresh/rebuild the Power BI model from the current clean dataset before publishing final metrics.

---

## 🗄️ MySQL Integration

The cleaned data is loaded into MySQL using SQLAlchemy.

Database:

```text
churndb
```

Table:

```text
customer_churn
```

Example:

```python
from sqlalchemy import create_engine

engine = create_engine(
    "mysql+pymysql://<username>:<password>@localhost/churndb"
)

df.to_sql(
    name="customer_churn",
    con=engine,
    if_exists="replace",
    index=False
)
```

### Security Recommendation

Never commit database passwords to GitHub. Use environment variables or a local configuration file excluded through `.gitignore`.

---

## 🔎 SQL Analysis

The project includes SQL analysis for:

1. Total customers.
2. Total churn customers.
3. Churn rate.
4. Average monthly charges.
5. Average tenure.
6. Churn by contract type.
7. Customers by internet service.
8. Churn by state.
9. Customers by payment method.
10. Customers by subscription type.
11. Highest-revenue states.
12. Average charges by contract type.
13. Senior-citizen churn.
14. Top 10 high-value customers.
15. Customers without technical support.

Example:

```sql
SELECT
    ROUND(
        SUM(CASE WHEN Churn = 'Yes' THEN 1 ELSE 0 END) * 100
        / COUNT(*),
        2
    ) AS Churn_Rate
FROM customer_churn;
```

---

## 📈 Power BI Dashboard

The dashboard provides an interactive view of customer churn and revenue.

### KPI Cards

- Total Customers
- Churn Customers
- Retained Customers
- Churn Rate
- Total Revenue
- Average Monthly Charges
- Average Tenure

### Dashboard Visuals

- Churn by Contract Type
- Churn by Subscription Type
- Churn by State
- Monthly Charges vs Churn
- Revenue by State
- Internet Service Distribution
- Payment Method Distribution
- Senior Citizen vs Churn
- Tenure Group vs Churn
- Customer Value by Subscription

### Slicers

- State
- City
- Contract Type
- Subscription Type
- Internet Service
- Payment Method
- Senior Citizen
- Churn
- Tenure Group
- Last Interaction Date

---

## 📁 Recommended Repository Structure

```text
Customer-Churn-Analysis/
│
├── data/
│   ├── Clean_Churn_Data.csv
│   └── Churn_Unclean_Project.xlsx
│
├── notebooks/
│   └── Customer_Churn_Analysis.ipynb
│
├── sql/
│   └── Customer_Churn_Analysis.sql
│
├── dashboard/
│   └── Customer_Churn_Analysis.pbix
│
├── documentation/
│   └── Complete_Project_Flow.docx
│
├── screenshots/
│   └── Customer_Churn_Analysis_Dashboard.png
│
├── README.md
└── .gitignore
```

---

## 🚀 How to Run

### 1. Install dependencies

```bash
pip install pandas numpy openpyxl sqlalchemy pymysql jupyter
```

### 2. Open the notebook

```bash
jupyter notebook
```

Open:

```text
Customer_Churn_Analysis.ipynb
```

Run it from top to bottom.

### 3. Generate the clean dataset

The notebook exports:

```text
Clean_Churn_Data.csv
```

### 4. Create the MySQL database

```sql
CREATE DATABASE churndb;
```

Update the SQLAlchemy connection with your own MySQL credentials.

### 5. Load the data into MySQL

Run the MySQL export section of the notebook.

The table created is:

```text
customer_churn
```

### 6. Run SQL analysis

Open:

```text
Customer_Churn_Analysis sql.sql
```

Run the queries against `customer_churn`.

### 7. Build / refresh Power BI

Connect Power BI to the cleaned dataset or MySQL table and recreate/refresh the documented KPIs, visuals, and slicers.

---

## 👨‍💻 Skills Demonstrated

**Data Analytics:** Data Cleaning, EDA, Feature Engineering, KPI Analysis, Business Insights

**Python:** Pandas, NumPy, Jupyter Notebook

**Database:** MySQL, SQL, SQLAlchemy

**Visualization:** Power BI, KPI Cards, Bar Charts, Donut Charts, Maps, Scatter Plots, Slicers

**Data Preparation:** Missing Value Handling, Duplicate Removal, Type Conversion, Data Standardization
