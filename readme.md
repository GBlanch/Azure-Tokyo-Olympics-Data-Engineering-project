# Azure Tokyo Olympics Data Engineering project

work in progress
&nbsp;    
&nbsp;    

## Table of Contents

- [Introduction](#introduction)
- [Architecture of the Data Pipeline](#architecture-of-the-data-pipeline)
- [Code and Services utilized](#code-and-services-utilized)
- [Storage Account and Resource Group](#storage-account-and-resource-group)
- [Data Lake Storage](#data-lake-storage)
- [Data Factory Ingestion Pipeline](#data-factory-ingestion-pipeline)
- [Azure Databricks Transformation](#azure-databricks-transformation)
- [Synapse Analytics](#synapse-analytics)
&nbsp;    
&nbsp;

## Introduction

&nbsp;    
&nbsp;    
## Architecture of the Data Pipeline


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/6da6ca7c-1650-4371-8118-576bbb9f2fee)


The raw data will be dowmloaded from [Kaggle datasets](https://www.kaggle.com/datasets/arjunprasadsarkhel/2021-olympics-in-tokyo/data) in `xls` format and then saved as `csv` files into [`this`](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/tree/main/data/raw) github directory. 

Prior to starting with any of the Azure Tools or Services, we will neeed to create a new `Storage Account` and `Resource Group`.

The next step will be to create and use `Data Factory` service in order to load/ingest this raw data into the 'raw' directory which will be located in our `Data Lake Storage`. This data ingestion will be the result of the pipeline to be created within the Data Factory.

Once the data in this 'raw' directory is ready to be transformed, we will use `Azure Databricks` platform to write and run some Spark code. 
Before doing so, and in order to connect `Databricks` to the 'transformed' directory with our Data Lake, we will need to create and set up a new `workspace`, `cluster`, `credentials app`, `client secret` and a `new role assignment` into our Access Control (IAM). 
By the end of this stage, the transformed data will be loaded into the 'transformed' directory in our `Data Lake Storage`.

Finally, we will create a new `workspace` for `Synapse Analytics` so that we can create a new `Lake database`. In this Lake database, we will have available all the databases which are also stored in the previously mentioned 'transformed' directory in our `Data Lake Storage`. We will also be able to create a SQL script with some queries so that we can have a quick glance at some visualizations and analytics about these datasets.


## Code and Services utilized

For this project, we will use all the services from the same provider, even for the ETL pipeline. That is, from raw and flat file/object storage to the execution of the analytics, we will be using `Microsoft Azure`. 

All the services to be utilized were mentioned within the previous section [Architecture of the Data Pipeline](#architecture-of-the-data-pipeline).

[Back to Table of Contents](#table-of-contents)
&nbsp;    
&nbsp;    


## Storage Account and Resource Group

In order to start using Data Lake and Data Factory - amongst many other Azure services - we need first to create a `Storage Account`.


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/447aca01-32c9-4a7e-b5a6-572ce946338a)


When creating it, besides creating a new `Resource Group`, we need to make sure to Enable the `Hierarchical namespace` (see above) so that all the objects stored inside the containers will be kept in hierarchical data format. This HDF format has the same nature as the data stored in any local and conventional machine.


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/35ebb05d-34b8-4143-a269-caac1aa6e66b)


Once the SA is deployed, we can also access to our new `Resource Group` wherein we can see all the resources available. Note that in the image above, besides de Storage Account, we already created the rest of the services to be used (Data Factory, Databricks and Synapse Analytics)

[Back to Table of Contents](#table-of-contents)
&nbsp;    
&nbsp;    

## Data Lake Storage 

Since we have already our Storage Account all set up, we are ready to create a new `Container` to store our data in the `Data Lake`. 


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/e20a8b8a-4e66-4372-b6ff-2ab53b89ef7a)



Inside this container, we will create 2 new `Directories`. The function of these will be to store our raw and transformed data.


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/347ae75d-e74a-4329-813e-10f3346b8af8)



[Back to Table of Contents](#table-of-contents)
&nbsp;    
&nbsp;    


## Data Factory Ingestion Pipeline

When creating our Data Factory, we will select the same `Resource Group` and create a new instance based on our closest region. This will enable the resources available in this integration service.

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/f600c2ea-aba3-4be5-85ea-d612a634c1b2)

Once the deployment for this service is finalized, we are ready to launch Data Factory and so begin ingesting the data from its external source (github repo) into our target location (Data Lake raw-data container)

Therefore we create a new pipeline to start our ingestion pipeline process. We can therefore add `Copy Data` blocks which will be configured further on.

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


We will repeat the same process for the other 4 csv flat files. Once they're all set, we are ready to validated and debug.

Then the ingestion pipeline results as follows:


<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/c56bda83-1c9f-4513-bbc4-f0550d5767e3"  width="100%" height="100%">


And so we can find all these datasets inside our raw-data directory we created before in our Container.

<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/f4dfc6e3-ccdb-460a-a8ca-782365a1e476)"  width="80%" height="80%">

Therefore, after finalizing the ingestion and storage of the data, we can move onto the Transformation stage with Azure Databricks service

[Back to Table of Contents](#table-of-contents)
&nbsp;    
&nbsp;    

## Azure Databricks Transformation

Likewise when we set up our Data Factory service, we will use the same `Resource Group` that we created when setting up our `Storage Account` at the beginning of this project:

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/df4b6961-c4cc-412f-adb4-25a694066210)

Once we launch the workspace, we create a `Compute` so that we can run our Spark code.

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/9a7b185e-b138-477b-b253-e6400c872027)

When cresting our `Cluster`, we will select `Single Node` as we won't be needing to use multiple machines or much resources to run our Transformation code.

Before writing any Spark code, we need to register an app in order to get some credentials. This step will allow us to go through the authentication process in order to mount the Data Lake storage to the Data Factory.


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/3da3891a-29b1-4b1c-aad1-28fe70fe068c)


Once our app is created and we have its `Application (client) ID` and `Directory (tenant) ID`, we next create a `Secret ID` and its `Secret Key` by creating a new `Client Secret`.

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/a3dbd61e-ca50-4fc7-a681-c36be19ffc72)

Once we have these 3 credentials, it is highly recommended to store them into a Key Vault. This would minimize the chances for our credentials to be exposed/leaked through our code.

Moving foward, we can proceed now to write the `Apache Spark script` in which we will create our `configuration format`(a.k.a. config) and `mounting point`, as they are shown below respectively:

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/2cedd341-9e4d-4b3e-a9bd-44c6bc72ceaf)

The last step to configure prior to start our transformation code, is to explicitly give access to the app to handle - or even read -any kind of object from the selected container. 

We will do this through the IAM framework within the container we created before. We will select `Storage Blob Data Contributor` in the section `Add role assignment`

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/73f9be87-fbdd-4b06-b133-5414289826d6)


Once we have done all this, we will be able to mount the data lake shown below into this new location/mounting point `/mnt/tokyoolympic`, this is to say:

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/e8e12ada-1f7e-48a0-9fc4-74a2b4a29eb0)



And so we are all set to start writting the `transformation Spark code`. This is, we will briefly show how we read, transform and load some of the data at once. We will use the function `option()` to regard the first row as the header, as well as to cast some of the data type from string to integer. The two Spark property names to be used within this function `option()` are `header` and `inferSchema`, and they are shown below:

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/135e2379-57cb-4165-bd89-f32895a64c82)


After executing all the basic data transformation, we can even perform here in `Databricks` some other essential analytical queries such as:

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/c4bab543-bb6f-4617-a1ee-f514d04cbac8)


![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/3e7359e9-e1d9-4509-9e24-79b5885f2d1e)

For more transformation and analytic queries using `Spark Apache` , please refer to the entire script and its explanation which can be found [`here`](). 

Moving forward, we are ready to write the data into our container. The code for doing this is shown in the first image below:

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/9f81be94-3fef-47a5-ab93-de66961142ea)

And so, if we check our container, we can find our objects written/stored there:

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/7aa5bed4-e622-4691-810a-25999cabf308)

In the image above we see how `Apache Spark` stores the data in the form of different type of metadata, all of them being Binary Large Objects (blob) in this case. Was the data to be transferred really large, we could even select the number of partitions we could want to create for that dataset.

[Back to Table of Contents](#table-of-contents)
&nbsp;    
&nbsp;    

## Synapse Analytics

We start creating its workspace the same way as we did with `Azure Databricks`. We select the same `Resource Group` , `Storage Location` and `Primary ADLS` we created before, as shown below:

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/7bfdf79a-a2ac-49c3-b8d7-b09cf1a592e3)


Once these changes are deployed, we can open `Synapse Studio` ans start creating a `Data Lake` in which we will create a new database. In this new database we will create a new table and this will be linked with the same `Linked Services` we created when setting up our `Data Lake Storage` . This results as follows:



<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/e99d05bb-4620-400b-96a3-bc7cd41ac6f9"  width="80%" height="80%">


<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/ebc63d25-1674-4bd0-ba76-596a9da0f85b"  width="50%" height="50%">

When being asked for the input file or directory, we will browse to the location in which we stored the partition files we generated at the end of the [`transformation Spark code`]() in `Azure Databricks`. These being:

<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/468df478-1b04-474c-b786-d9691b480f57"  width="100%" height="100%">


Once all the tables are created and validated, we can publish them into our database:

<p align="center">
<img src="https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/d91740bf-ef1c-4208-9586-f8e15d5b82f6"  width="100%" height="100%">

We are ready now to create a new SQL script in which we can define the queries we want to visualize through the `Results` tab in form of `Tables` and `Charts`.


Average number of entries by gender for each discipine, more than 100 entries

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/2ecbaab0-41df-4c0f-abd7-a95cb189ee31)

Average number of entries by gender for each discipine, less than 100 entries

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/be689c4d-39e0-4b5b-b422-55b65c1435d8)

Medals of the Top 10th countries

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/2d287fa5-08df-4b24-b753-c81ee2a8ee4e)

Medals of the countries between 10th and 25th position

![image](https://github.com/GBlanch/Azure-Tokyo-Olympics-Data-Engineering-project/assets/136500426/dc081fef-557f-46df-aa93-8d0992124361)






[Back to Table of Contents](#table-of-contents)
&nbsp;    
&nbsp;    

