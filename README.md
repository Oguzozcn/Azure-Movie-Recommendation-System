# Azure Movie Recommendation System

### Why I am doing this?: 
- I am studying azure services due preparation of DP-203 (Azure Data Engineering Exam) and also investigate how cloud services builts systems from scratch. I am taking notes also for future purposes and also people who have less experience can see and understand how processes works in a simple step by step guide with schema that makes them understand bigger picture.

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

- Create an Azure Databricks by connecting it to your resource group. Choose the standard pricing tier, which will create a workspace. Sign in to the new window that pops out and create a new notebook with the language as Python. Remember that running notebooks means running clusters and will result in charges.

- In the Compute tab, create a cluster and name it. Choose the standard option that uses multiple nodes, but remember that only one user can work at the same time in standard option.

- Enable autoscaling and set the minimum worker to 1 and maximum worker to 3. This will increase the workers if the load increases. Select terminate after 60 minutes of inactivity.

- Mount your landing zone container in the Workspace. Remember to attach your cluster to the notebook; otherwise, the notebook will not run. Azure Databricks and Azure Storage need to connect, and we do that by mounting.

- Go to Azure App Registration and create a new registration. In the Certification and Secrets panel, create a new certificate and new client secret with an expiration date. Copy your value and secret ID and store them somewhere safe (locally).

- Go to Azure Key Vault and create a new key vault. Inside the vault, store the secrets. In the Secrets tab, generate or create a secret, name it, and copy the value. This key vault will be used whenever you want to use the app registration key that you created.

- To use the app registration, you need three things: client secret, application (client) ID, and directory (tenant) ID. Create these three in Key Vault. Remember that even the owner cannot see the value after. Use step 1 to fill it.

#### 4. Azure Databricks and Mounting (Mounting Notebook):

- Manually create scope by go to https://<databricks-instance>#secrets/createScope. This URL is case sensitive; scope in createScope must be uppercase. More information from databricks website [here](https://learn.microsoft.com/en-us/azure/databricks/security/secrets/secret-scopes).
 
- From azure key vault >> properties copy Vault URL and Resource ID and save it. Fill parts in Databricks notebook.
 
- List the files to verify.
  
#### 5. Azure Databricks and Mounting (Movie_Lens_small Notebook):

- Gathering the data: Specify the file paths for two CSV files, 'ratings.csv' and 'movies.csv', which will be used as input data for further analysis.

- Databricks connecting: Used the "%fs" command to connect to the underlying Databricks cluster and list the files under the '/mnt/Files/' directory.

- Creating dataframes: Created two PySpark DataFrames, 'movies_df' and 'movies_with_genres_df', by reading the 'movies.csv' file using the 'com.databricks.spark.csv' format. It also defines the schemas for these DataFrames using the StructType and StructField classes.

- Inspecting Dataframes: Displayed the first 4 rows of each of the DataFrames created in the previous step using the show() method.

- Transforming the Dataframes: Transformed the 'movies_df' DataFrame by adding a new 'year' column based on a regular expression match for the year in the movie title. The resulting DataFrame is named 'movies_with_year_df'.

-  Global Popularity: Computeds the average rating and count of ratings for each movie using the 'groupBy' and 'agg' methods of the 'ratings_df' DataFrame. It then joins the resulting DataFrame with the 'movies_df' DataFrame to get the names of the movies. Finally, it filters the movies that have at least 508 ratings and displays them in descending order of average rating using the 'filter' and 'orderBy' methods of the resulting DataFrame.

Summary of it is to find movie recommendations, we need to analyze the ratings data. We used a structured data frame and cast it to access it quickly. Next, we joined the movie names with average ratings using a join method, dropping one ID in the process. Now we have a data frame with movie IDs, titles, and average ratings. We also used collaborative filtering and the ALS method for matrix factorization. We set the user column to user ID, the item column to movie ID, and the rating column to the ratings given by users. We then split the ratings data frame into training, validation, and test sets and used the ALS fit method to train the model on the training data. Finally, we evaluated the model on the validation and test data sets to see how well it performs.


#### 5. Azure Data Factory and Logic Apps (Schema):
 
 
- Create your own datafactory related with your resource group.

- In overview there is open data factory studio, go author from left menu. There are pipeline, dataset and dataflow tabs will be shown. Create a pipeline M_R_pl lets say.

- Validate files by get metadata of the files(movie file, movie schema, ratings file, ratings schema) (Create new dataset >> Azure Blob Storage >> DelimitedText) (Linked Services means adress of the file, New >> authentication method account key >> storage account (yourlandingzone))
 
- Get if activity, connect all metadata to if condition branch, if schema matches copy data function and source is movie data meaning file path is rawdata/Movie/movies.csv. If False do copy data same. Defined Sink each of them if statement matches(true) sink into validated and if not sink into rejected.
 
 - In If schema matches true statement get databricks notebook module, in azure databricks tab select workspace and select existing cluster with access token. (Acess token you can take from databricks >> user settings >> generate new token.) Notebook modele settings tab browse notebook path >> Movies_Lens_small.
 
 - Select web and drag down two of it next to if condition. Name them success email and fail email. Inside setting URL set by azure logic apps (we are going to use it to schedule and send emails)
 
- Create a logic app, on resource group and publish on workflow. Then create a trigger, select schedule.
 
- Run the pipeline and it will email.
 
 
 















#### Citation 

 Note: I changed all .csv files since they still didnt email me free usage licence. So now movie names shown as follow, Movie_1, Movie_2.
```
F. Maxwell Harper and Joseph A. Konstan. 2015. The MovieLens Datasets: History and Context. ACM Transactions on Interactive Intelligent Systems (TiiS) 5, 4: 19:1–19:19. <https://doi.org/10.1145/2827872>
```
