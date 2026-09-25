# 🏦 Commonwealth Bank Cyber Security Job Simulation — Fraud Detection Dashboard

## 📋 Project Overview
Completed Commonwealth Bank's Cyber Security job simulation on Forage, acting as a
cyber security generalist supporting the bank's fraud team. Built a Splunk dashboard
to analyze 200 simulated customer transactions and surface fraud patterns across
category, age group, gender, month, and merchant — helping the team identify
suspicious activity patterns for further investigation.

## 🛠️ Tools & Setup
- Splunk Enterprise (local install, 60-day trial)
- Dataset: 200 simulated transactions with fields for step (month), customer, age
  group, gender, postcode, merchant, category, amount, and fraud flag
- Imported CSV via Splunk's Add Data workflow, indexed with current time

## ⚙️ Dashboard Build
Used Splunk's Search Processing Language (SPL) to build 10 panels across the
transaction overview and fraud analysis. `eval` and `case()` were used throughout
to convert coded fields (age bracket, month) into readable labels, and `replace()`
to clean up formatting artifacts in the source text fields.

**Transaction overview**
```spl
sourcetype="fraud_detection.csv" | eval category=replace(category,"'","") | top category

sourcetype="fraud_detection.csv" | eval fraud_status=case(fraud=0,"Not Fraud", fraud=1,"Fraud") | stats count by fraud_status

sourcetype="fraud_detection.csv" | eval age_group=case(age=0,"≤18", age=1,"19-25", age=2,"26-35", age=3,"36-45", age=4,"46-55", age=5,"56-65") | top age_group

sourcetype="fraud_detection.csv" | eval merchant=replace(merchant,"'","") | top merchant limit=10
```

**Fraud analysis**
```spl
sourcetype="fraud_detection.csv" fraud="1" | eval category=replace(category,"'","") | stats count by category | sort -count

sourcetype="fraud_detection.csv" fraud="1" | eval month=case(step=0,"May", step=1,"June", step=2,"July", step=3,"August") | stats count by month

sourcetype="fraud_detection.csv" fraud="1" | eval gender=replace(gender,"'","") | stats count by gender

sourcetype="fraud_detection.csv" fraud="1" | eval age_group=case(age=0,"≤18", age=1,"19-25", age=2,"26-35", age=3,"36-45", age=4,"46-55", age=5,"56-65") | stats count by age_group

sourcetype="fraud_detection.csv" fraud="1" | eval gender=replace(gender,"'",""), category=replace(category,"'","") | stats count by gender, category | sort -count

sourcetype="fraud_detection.csv" fraud="1" | eval age_group=case(age=0,"≤18", age=1,"19-25", age=2,"26-35", age=3,"36-45", age=4,"46-55", age=5,"56-65"), merchant=replace(merchant,"'","") | stats count by age_group, merchant | sort -count
```

## 🔍 Key Findings
- 92 of 200 transactions (46%) were flagged as fraudulent
- Fraud was heavily concentrated in the transportation category (84 of 92 cases)
- **Female customers accounted for the most fraudulent transactions (49 vs. 35 for
  male customers), overwhelmingly in the transportation category** — directly
  answering which gender/category combination poses the highest risk
- **The 19–25 age group had the highest fraud count (36 of 38 cases directed at a
  single merchant, M348934600)** — a strong signal for targeted monitoring of that
  merchant relationship for younger customers
- Fraud volume stayed fairly consistent month over month (19–27 cases per month),
  suggesting no strong seasonal trend in this dataset

## 💡 Key Learnings
- Practical experience with SIEM-style log/data analysis and dashboard building in
  Splunk
- Writing SPL queries to filter, aggregate, and cross-tabulate data (`stats`,
  `top`, `eval`, `case()`, `replace()`)
- Choosing the right visualization for the data shape — single-dimension counts as
  bar charts, two-dimension breakdowns as tables, trends over time as line charts
- Translating raw transactional data into visual insights a fraud analytics team
  could act on

## 📁 Files
- `Fraud_Detection_Dashboard.pdf` — exported dashboard
- `prepared_data.csv` — source dataset
