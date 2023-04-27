# Azure Movie Recommendation System

This project involves creating a movie recommendation system using Azure services, which includes the following steps:


1. Set up an Azure storage account and upload your movie data files from your local laptop to the account.

2. Connect your Azure Databricks account to your Azure storage account so that you can access your movie data files.

3. Before building your recommendation system, ensure that your data is clean and has a proper schema. You can use Azure Data Factory or Azure Databricks for this purpose.

4. Use Spark code in Azure Databricks to create your recommendation system, using techniques like collaborative filtering or content-based filtering.

5. Create a pipeline in Azure Data Factory that runs your Spark job and sends the output to your Azure storage account.

6. If the pipeline runs successfully, send an email to the user with the recommended movies. If the pipeline fails, send an email to the appropriate team notifying them of the failure.


![](https://github.com/Oguzozcn/Azure-Movie-Recommendation-System/blob/main/systemdiagram.png)


## Full Guidance


#### 1. Resource Group: 
Inside Azure services create a resource group to hold all the resources for your project. This is like a container for all the resources you'll create in Azure. You can give it a name and choose a location that's nearest to your company. Note that resource group = resource container and this is the place everything going to be held for Azure Movie Recommendation System.

#### 2. Storage Account: 
Create a storage account to store your movie data files. Make sure you link this storage account to your resource group. There are different types of replication available in Azure, including geo-redundant storage and locally-redundant storage. Geo-redundant storage means your data is replicated in a different geography, while locally-redundant storage means your data is replicated within the same region. Enable hierarchical namespace, you're allowing for the creation of directories and subdirectories in your storage account, similar to how folders are organized on a local computer. This can make it easier to manage and access your files. You can also create containers within your storage account to group your files together, and use a schema to ensure that your files have the proper format and structure.


![](https://github.com/Oguzozcn/Azure-Movie-Recommendation-System/blob/main/Storage%20account.png)


#### 3. Azure Databricks and Mounting (Setup and Key Vault): 

a) Create an Azure Databricks by connecting it to your resource group. Choose the standard pricing tier, which will create a workspace. Sign in to the new window that pops out and create a new notebook with the language as Python. Remember that running notebooks means running clusters and will result in charges.

b) In the Compute tab, create a cluster and name it. Choose the standard option that uses multiple nodes, but remember that only one user can work at the same time in standard option.

c) Enable autoscaling and set the minimum worker to 1 and maximum worker to 3. This will increase the workers if the load increases. Select terminate after 60 minutes of inactivity.

d) Mount your landing zone container in the Workspace. Remember to attach your cluster to the notebook; otherwise, the notebook will not run. Azure Databricks and Azure Storage need to connect, and we do that by mounting.

e) Go to Azure App Registration and create a new registration. In the Certification and Secrets panel, create a new certificate and new client secret with an expiration date. Copy your value and secret ID and store them somewhere safe (locally).

f) Go to Azure Key Vault and create a new key vault. Inside the vault, store the secrets. In the Secrets tab, generate or create a secret, name it, and copy the value. This key vault will be used whenever you want to use the app registration key that you created.

g) To use the app registration, you need three things: client secret, application (client) ID, and directory (tenant) ID. Create these three in Key Vault. Remember that even the owner cannot see the value after. Use step 1 to fill it.

#### 4. Azure Databricks and Mounting (Mounting Notebook):

a) Manually create scope by go to https://<databricks-instance>#secrets/createScope. This URL is case sensitive; scope in createScope must be uppercase. More information from databricks website [here](https://learn.microsoft.com/en-us/azure/databricks/security/secrets/secret-scopes).
 
b) From azure key vault >> properties copy Vault URL and Resource ID and save it. Fill parts in Databricks notebook.
 
c) List the files to verify.
  
#### 5. Azure Databricks and Mounting (Movie_Lens_small Notebook):























#### Citation 
```
F. Maxwell Harper and Joseph A. Konstan. 2015. The MovieLens Datasets: History and Context. ACM Transactions on Interactive Intelligent Systems (TiiS) 5, 4: 19:1–19:19. <https://doi.org/10.1145/2827872>
```
