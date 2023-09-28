# Azure Tokyo Olympics Data Engineering project

## Table of Contents

- [Introduction](#introduction)
- [Architecture of the Data Pipeline](#architecture-of-the-data-pipeline)
- [Code and Services utilized](#code-and-services-utilized)

## Introdution


## Architecture of the Data Pipeline

(image)

The raw data will be extracted from its source (Kaggle website) using Data Factory in order to load/ingest it into our Data Lake Storage location (Raw) further on. This data loading will be the result of a pipeline created within the Data Factory.

Once the data is ready to be conveyed, the ETL orchestration will mainly occur within Azure Databricks environment. We will elaborate some code in Apache Spark and so get it loaded into another Data Lake Storage location(Transformed).

Next, we will run some SQL functions and analytics queries in Azure Synapse analytics so that we can produce some dashboards and reports later on using Power BI.




## Code and Services utilized
