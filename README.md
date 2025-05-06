# Implementing Medallion Architecture in Microsoft Fabric Lakehouse

## Overview
This lab demonstrates how to implement a complete medallion architecture (bronze-silver-gold) in Microsoft Fabric using a lakehouse and notebooks. You'll process sales data through each layer, applying transformations and creating a star schema in the gold layer.

## Prerequisites
- Microsoft Fabric trial
- Web browser access
- Sample data files (2019.csv, 2020.csv, 2021.csv)

## Lab Architecture

```
Bronze Layer (raw data) → Silver Layer (cleaned data) → Gold Layer (modeled star schema)
```

## Key Steps

### 1. Environment Setup
1. Create Fabric workspace with capacity
2. Enable Data model editing preview
3. Create "Sales" lakehouse

### 2. Bronze Layer Implementation
1. Create `bronze` folder
2. Upload CSV files (2019.csv, 2020.csv, 2021.csv)

### 3. Silver Layer Transformations

```python

# Schema definition and data quality

orderSchema = StructType([

    StructField("SalesOrderNumber", StringType()),

    StructField("SalesOrderLineNumber", IntegerType()),

    # ... other fields ...

])

df = (df

    .withColumn("FileName", input_file_name())

    .withColumn("IsFlagged", when(col("OrderDate") < '2019-08-01', True))

    .withColumn("CustomerName", when(col("CustomerName").isNull(), "Unknown"))

)
```

### 4. Gold Layer Modeling

```python

# Create dimension tables

DeltaTable.createIfNotExists(spark) \

    .tableName("sales.dimdate_gold") \

    .addColumn("OrderDate", DateType()) \

    # ... other columns ...

# Create fact table

DeltaTable.createIfNotExists(spark) \

    .tableName("sales.factsales_gold") \

    .addColumn("CustomerID", LongType()) \

    # ... other columns ...

```

### 5. Semantic Model Creation
1. Create new semantic model "Sales_Gold"
2. Include all gold tables:
   - dimdate_gold
   - dimcustomer_gold
   - dimproduct_gold
   - factsales_gold
3. Define relationships between tables

## Key Features Demonstrated
- Delta table operations (create, merge/upsert)
- Data quality enforcement
- Star schema modeling
- Incremental data processing
- SQL endpoint for exploration
- Semantic model creation

## Clean Up
1. Navigate to workspace settings
2. Select "Remove this workspace"

# 👤 Author >> Sefa Öztürk

IT Trainee | Azure Data Engineer in progress

📇 LinkedIn: https://www.linkedin.com/in/sefa-ozturk1972
