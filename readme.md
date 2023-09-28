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

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/f600c2ea-aba3-4be5-85ea-d612a634c1b2)

Once the deployment for this service is finalized, we are ready to launch Data Factory and so begin ingesting the data from its external source (github repo) into our target location (Data Lake raw-data container)

Therefore we create a new pipeline to start our ETL process. We can therefore add `Copy Data` blocks which will be configured further on.

<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/c6d4c159-1bd2-41a5-b81a-4e405bb45638"  width="60%" height="60%">

Inside these blocks/instances, we mainly want to configure the tabs `Source` and `Sink`.

For the `Source` tab, we first create a new `Source Dataset`. 

Since our raw data is access through our HTTP server and it's stored in csv file format, we will select the HTTP and csv file formats when being asked by location, structure and data type in this step. Next, we need to create a `Linked service` within this new Dataset. Here is where we will pass the [`raw URL`](https://raw.githubusercontent.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/main/data/raw/Coaches.csv) from our git repository.

<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/a56e247c-80f3-4d45-8801-6a71d3ce8638"  width="60%" height="60%">

When the `Linked service` is already created we make sure the `Frist row as header` option is checked to avoid future data cleaning/transformation, as shown below:


<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/4ff5b4f6-da7e-4583-b51c-39596f620ff3"  width="60%" height="60%">

Next, we will set up our `Sink`. This step will call for creating a new `Sink dataset` so that we can connect to Azure Data Lake Storage. We are using the same `Storage account` we created before.


<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/584a7ca8-77ed-4435-8a5c-a5f39f51d56a"  width="60%" height="60%">


Once the `Linked Services` from our Data Factory to our Data Lake Storage is created, we will define the destination file path. We can browse to navigate into our raw-data directory we created before inside our Container. We finally create a name for the csv file to be stored and select `None` for the `Import schema` option.

<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/8b16915a-8d19-4ec8-902a-44b4edbb4da1"  width="60%" height="60%">


We will repeat the same proces for  the other 4 csv flat files. Once they're all set, we are ready to validated and debug.

Then the ETL pipeline results as follows:


<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/c56bda83-1c9f-4513-bbc4-f0550d5767e3"  width="100%" height="100%">


And we can find all these datasets inside our raw-data directory we created before in our Container.

<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/f4dfc6e3-ccdb-460a-a8ca-782365a1e476)"  width="100%" height="100%">


[Back to Table of Contents](#table-of-contents)

