# Azure Tokyo Olympics Data Engineering project

## Table of Contents

- [Introduction](#introduction)
- [Architecture of the Data Pipeline](#architecture-of-the-data-pipeline)
- [Code and Services utilized](#code-and-services-utilized)
- [Storage Account setting up](#storage-account-setting-up)

## Introdution


## Architecture of the Data Pipeline

(image)

The raw data will be extracted from its source (Kaggle website) using Data Factory in order to load/ingest it into our Data Lake Storage location (Raw) further on. This data loading will be the result of a pipeline created within the Data Factory.

Once the data is ready to be conveyed, the ETL orchestration will mainly occur within Azure Databricks environment. We will elaborate some code in Apache Spark and so get it loaded into another Data Lake Storage location(Transformed).

Next, we will run some SQL functions and analytics queries in Azure Synapse analytics so that we can produce some dashboards and reports later on using Power BI.




## Code and Services utilized




## Storage Account setting up

In order to start using Data Lake and Data Factory - amongst many other Azure services - we need first to create a `Storage Account`.


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/447aca01-32c9-4a7e-b5a6-572ce946338a)


When creating it, besides creating a new `Resource Group`, we need to make sure to Enable the `Hierarchical namespace` (see above) so that all the objects stored inside the containers will be kept in hierarchical data format. This HDF format has the same nature as the data stored in any local and conventional machine.


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/35ebb05d-34b8-4143-a269-caac1aa6e66b)


Once the SA is deployed, we can also access to our new `Resource Group` wherein we can see all the resources available. Note that in the image above, besides de Storage Account, we already created the rest of the services to be used (Data Factory, Databricks and Synapse Analytics)

### Data Lake

Since we have already our Storage Acount all set up, we are ready to create a new `Container` to store our raw data in the Data Lake. 


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/dcef6881-ee50-4f7b-a0b9-a4e040dbb1fc)


Inside this container, we will create 2 new `Directories`. The function of these will be to store our raw and transformed data.


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/725e8723-3192-4f0e-82bc-6a6ecd85b375)



### Data Factory

When creating our Data Factory, we will select the same `Resource Group` and create a new instance based on our closest region. This will enable the resources available in this integration service.

Next, we will begin ingesting the data stored inside the raw-data container.

[Back to Table of Contents](#table-of-contents)

