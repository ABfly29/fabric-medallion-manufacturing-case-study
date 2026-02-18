# 🏭 Fabric Medallion Manufacturing Data Platform  
### Enterprise Lakehouse Implementation using Microsoft Fabric

---

## 📌 Executive Summary

This project implements a structured Lakehouse data platform using **Microsoft Fabric** and **Medallion Architecture (Bronze → Silver)**.

The solution models a multi-location manufacturing enterprise handling:

- Raw Material Procurement  
- Production Operations  
- Inventory Movements  
- Sales & Invoicing  
- Collections & Accounts Receivable  
- Customer Credit Monitoring  

The objective was to design a governed, scalable, analytics-ready data platform while solving real-world data engineering challenges in the transformation layer.

---

# 🏗 Architecture Overview

    ┌────────────────────────────────────┐
    │ GitHub (Source Control & Raw Data)│
    └────────────────────────────────────┘
                      ↓
    ┌────────────────────────────────────┐
    │ Bronze Layer (Raw CSV - Files)    │
    └────────────────────────────────────┘
                      ↓
    ┌────────────────────────────────────┐
    │ Fabric Notebooks (PySpark)        │
    └────────────────────────────────────┘
                      ↓
    ┌────────────────────────────────────┐
    │ Silver Layer (Parquet Tables)     │
    └────────────────────────────────────┘
                      ↓
    ┌────────────────────────────────────┐
    │ Power BI Semantic Model           │
    └────────────────────────────────────┘
                      ↓
    ┌────────────────────────────────────┐
    │ Executive Dashboard               │
    └────────────────────────────────────┘


---

# 🥉 Bronze Layer – Raw Zone

## **Purpose**
Immutable ingestion layer preserving source data integrity.

### **Characteristics**

- Raw CSV ingestion from GitHub  
- No transformations applied  
- Schema preserved as received  
- Stored in Lakehouse Files  
- Acts as audit and traceability layer  

### **Bronze Entities**

- production_data  
- raw_material_purchase  
- sales_orders  
- collections  
- customer_master  
- inventory_transactions  

---

# 🥈 Silver Layer – Curated & Structured Zone

## **Purpose**
Enforce data quality, apply business logic, and prepare analytics-ready datasets.

The Silver layer represents the core engineering intelligence of the platform.

---

# 🔎 Data Engineering Problems Solved

---

## 1️⃣ Inconsistent Data Types

### **Problem**
- Dates stored as text or integers  
- Monetary fields stored as strings  
- Numeric precision inconsistencies  

### **Solution**
- Explicit type casting using PySpark  
- Date normalization using format-specific parsing  
- Decimal precision enforcement (`Decimal(18,2)`)  

---

## 2️⃣ Invalid Transaction Records

### **Problem**
- UnitsSold ≤ 0  
- Negative payment amounts  
- RejectedQty > ActualQty  
- Inventory rows with both InQty and OutQty populated  

### **Solution**
- Business validation rules implemented  
- Invalid records captured instead of silently dropped  
- Structured filtering before Silver persistence  

---

## 3️⃣ Date Conversion Failures

### **Problem**
- Multiple date formats causing NULL conversions  
- Spark default parsing failures  

### **Solution**
- Explicit date parsing using `to_date(column, format)`  
- Structured conversion before date arithmetic  
- Reliable DueDate and DaysToCollect calculations  

---

## 4️⃣ Duplicate Primary Keys

### **Problem**
- Duplicate business identifiers  
- Risk of inflated joins and incorrect aggregations  

### **Solution**
- Deterministic deduplication logic  
- Primary key enforcement at Silver level  

---

## 5️⃣ Missing Analytical Metrics

### **Problem**
Raw data lacked derived business measures required for reporting.

### **Solution**

### **Sales**
- NetUnitPrice  
- InvoiceAmount  
- TaxAmount  
- TotalInvoiceAmount  
- DueDate  
- HighDiscountFlag  

### **Production**
- GoodQty  
- MaterialUsedKG  
- ProductionVarianceQty  
- OverProductionFlag  

### **Collections**
- DaysToCollect  
- IsDelayed  
- InvoiceStatus  

All calculations were moved into the engineering layer to prevent duplication in BI tools.

---

## 6️⃣ Lack of Governance & Observability

### **Problem**
Invalid records were previously dropped without trace.

### **Solution**
A centralized `silver_rejected_records` table was implemented containing:

- TableName  
- RecordID  
- IssueType  
- RejectedTimestamp  

This enables:

- Auditability  
- Data quality monitoring  
- Root cause analysis  
- Governance maturity  

---

## 7️⃣ Dimensional Modeling Limitations

### **Problem**
Customer master lacked analytical structure.

### **Solution**
- Surrogate key generation  
- SCD Type 2-ready columns (EffectiveFrom, EffectiveTo, IsCurrent)  
- Null handling (`RiskCategory → 'Unknown'`)  
- CreditLimit precision enforcement  

---

# 🧠 Silver Layer Capabilities

The curated layer now provides:

- Clean and structured transactional tables  
- Enforced business rules  
- Cross-table referential alignment  
- Surrogate key readiness  
- Centralized data validation logging  
- Analytics-ready Parquet datasets  

### **Silver Entities**

- silver_production  
- silver_raw_material_purchase  
- silver_sales  
- silver_collections  
- silver_customer  
- silver_inventory_transactions  
- silver_rejected_records  

---

# 📊 Analytics Layer

Silver tables are consumed directly by Power BI:

- Relationships defined using surrogate keys  
- DAX measures implemented in semantic layer  
- KPI logic separated from transformation logic  

### **KPI Domains Modeled**

- Production performance  
- Revenue & tax analysis  
- Collection delay monitoring  
- Customer credit utilization  
- Inventory flow analysis  

---

# 🛠 Technology Stack

- Microsoft Fabric Lakehouse  
- PySpark (Notebook-based transformations)  
- Parquet storage format  
- Power BI Semantic Modeling  
- GitHub (Version control)  
- Medallion Architecture  

---

# 🚀 Architectural Strengths Demonstrated

- Structured Medallion implementation  
- Business-rule-driven transformation logic  
- Cross-entity enrichment  
- Data governance modeling  
- Surrogate key design  
- Clean separation between engineering and reporting  
- Enterprise-aligned Lakehouse architecture  

---

# 📌 Professional Summary

This implementation demonstrates:

- Ownership of transformation logic at the data engineering layer  
- Governance-first architecture design  
- Lakehouse modeling discipline  
- Business-to-technical translation capability  
- Structured, scalable data platform design  

