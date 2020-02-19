# LAB 2 - Creating Tables and Loading Data into Amazon Redshift
In this Lab you will create the tables in the Amazon Redshift Cluster using the proper distribution keys for fact and dimensions tables that will allow you to get the most of Massive Paralallel processing system in Amazon Redshift.  

These tables are based on TPC-H star schema, commonly used for database/data warehouse benchmarking. In the next steps in this Lab, you  will use COPY command to load approximately 10GB worth of data from a S3 bucket. At the end of this Lab, you should expect 7 tables created in your Redshift cluster.

## Contents
  - [Creating Tables](#creating-tables)
  - [Loading Data into Amazon Redshift](#loading-data-into-amazon-redshift)
  

## Creating Tables

The tables **lineitem** and **orders** contains the largest number of rows. 

•	`customer`   
•	`lineitem`  
•	`nation`  
•	`orders`  
•	`part`  
•	`partsupp`  
•	`supplier`  


Please refer to the link provided below to download the script that will be used to create the table definition for this exercise.  

Access the tables.sql file using the following S3 link. 
[Table Definition - tables.sql](https://s3.amazonaws.com/reinvent-hass/code/tables.sql)


Open the file `tables.sql` using your query editor of preference. The majority of client tools provide query editor and the ability to submit queries or scripts to Redshift. After opening the file, examine the `CREATE TABLE` commands; you should see distribution style key for tables `lineitem`, `orders`, `part`, and `partsup`. For all the remaining tables, the distribution style will be set to ALL as they are small dimmensions tables. It means that tables defined with a distribution key will have a specific collumn set as the distribution key so the data can be hashed and distributed across all nodes and slices in the Amazon Redshift Cluster. The distribution ALL means there will be a copy of the table in all the nodes. The reason for choosing distribution ALL for small dimention tables is to avoid data movement between the nodes. When you join a fact distributed by key and dimmention tables with distribution style ALL, the query processing will happen local at the node. 

Copy the CREATE TABLE commands and execute one at the time using the client tool of your preference. You can also use the Query Editor available in the Amazon Redshift console. 


## Loading Data into Amazon Redshift

You are now ready to load data into your Amazon Redshift cluster. We will load approxemately 10GB worth of data from a S3. The bucket is in US-EAST-1 region.

Please refer to the following link provided below to download the script that will be used to load data into Redshift using the COPY command. 
Access the copy.sql file using the following s3 link. 
[COPY Command - copy.sql](https://s3.amazonaws.com/reinvent-hass/code/copy.sql)

After downloading the `copy.sql` file, open the file using the client tool of your choice and execute the copy statements against your Redshift cluster. 

**`Important step`**, replace 'iam_role' with the IAM role assigned to your Redshift cluster in all the COPY commands in your script. (see COPY command example bellow) 

```sql
COPY nation FROM 's3://reinvent-hass/redshiftdata/nation/nation_'
iam_role 'arn:aws:iam::xxxxxxxxxxxx:role/MyRedshiftRole'
gzip delimiter '|'
IGNOREHEADER 1;
```
If you need instructions on how to retrieve the iam_role assigned to your redshift cluster, please refer to [`Redshift IAM Roles`](https://github.com/andrehass/RedshiftWorkshop/blob/master/IAM-role.md) section. 

**Load times and # of rows**  

•	customer ==>         aprox 1 minute – 15M rows  
•	lineitem ==>         aprox  10 Minutes, 250M rows  
•	nation;  ==>         N/A  
•	orders;  ==>         aprox 5 minutes, 58M rows   
•	part;    ==>         aprox 1 minute, 20M rows  
•	partsupp; ==>        aprox 3 minutes, 80M rows  
•	supplier; ==>        aprox 20 seconds, 1M rows  
•	region;   ==>        5 records  

Execute each COPY command individually. Tables `lineitem` and `orders` will take longer. 
You can monitor the load status by either using AWS Console or running a query on **`STV_LOAD_STATE`** table. 

Connect to the Redshift Cluster using a different session while your COPY command executes to check the COPY command state. 

```sql
/* Check load state */
select query, slice ,bytes_loaded, bytes_to_load, lines, num_files,  pct_complete from stv_load_state;
```

| slice | bytes_loaded | bytes_to_load | pct_complete 
|-------|--------------|---------------|--------------
|     2 |            0 |             0 |            0
|     3 |     12840898 |      39104640 |           32
(2 rows)

When you finish loading the data into Amazon Redshift, execute the following command to check the tables in Redshift. Look for the columns `diststyle` for the distribution style defined for your table. 

```sql
select "table", encoded, diststyle, sortkey1, skew_sortkey1, skew_rows
from svv_table_info
order by 1;
```

You now can move to the Lab 3. 

**Important** If you leave earlier or don't have plans to complete the remaining Labs. Make sure you delete the resources created in your acoount to avoid any unespected charges. Please refer to [Remove CloudFormation Stack](https://github.com/andrehass/RedshiftWorkshop/blob/master/cleanresources.md) for instructions on how to remove the CloudFormation Stack. 


