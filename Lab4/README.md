# LAB 4 - Querying S3 Data Lake using Redshift Spectrum 
Now you will learn how to setup your Amazon Redshift Cluster to query Data on S3 Data Lake with Redshift Spectrum.  

In this lab, you will use external tables to query data stored in Amazon S3 using parquet file format. The external tables definition are created and kept in AWS Glue catalog. In this Lab you will learn how to create database catalog in Glue, create crawler jobs that will detect tables and partitions based on data stored in S3. Later you will query data on S3 using external tables via Amazon Redshift Spectrum. 


## Contents
  - [Create external database and schema](#create-external-database-and-schema)
  - [Create clawler Job using AWS Glue](#create-clawler-job-using-aws-glue)
  - [Running the crawler Job](#running-the-crawler-Job)
  - [Querying Data on S3 using Redshift Spectrum](#querying-data-on-s3-using-redshift-spectrum)
  - [After you finish all the Labs](#after-you-finish-all-the-labs)

## Create external database and schema

You will now create the external database in AWS Glue and the external schema in Amazon Redshift that will make the external tables visible for queries when using Amazon Redshift Spectrum. 

Log in to the AWS Console. On AWS console main page, go to Services and select AWS Glue or type Glue in the search field. Choose AWS Glue from the search. 

1. On AWS Glue console choose `Databases` on the left-hand side and choose `Add Database` option.  
2. Type a database name in the `Database name` field and choose `Create`  

**Important** Take note of the database name you chose as it will be used in a later section. 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/gluedatabase.jpg "Database Name")


## Create clawler Job using AWS Glue

After creating the database in AWS Glue, you will now create a crawler job that will scan an S3 bucket where the parquet files are stored and then create external tables automatically for you with correct properties. Later you will query these external tables in Amazon Redshift Spectrum. The integration with Data Lake on S3 will allow you to query data on tables stored either on Amazon Redshift or S3 using Amazon Redshift Spectrun through external tables. 

While still in the AWS Glue console, choose `Crawlers` on the left-hand side and then **`Add crawler`**


![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/addcrawlerJob.jpg "Add Crawler")

Specify the **`Crawler Name`**

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/crawlername.jpg "Crawler Name")

On Specify crawler source type, choose `Data Stores` and click next. 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/crawlersource.jpg "Crawler Source")

On **`Add a data store`**  select S3 as your data store, select the option **`Specified path in another account`**, and use the following path in the **`Include path`** field. s3://reinvent-hass/historical-parquet. and **`Choose next`** 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/crawlerdatastore.jpg "Crawler Data Store")

In the **`Add another data store`** leave the option **`No`** selected and choose next: 

In the **`Choose an IAM role`** choose the option **`Create an IAM role`**. Specicy the role name in the field and choose `Next`.

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/glueIAMRole.jpg "Create IAM role")

On **`Create a schedule for this crawler`** choose the option **`Run on demand`** and than **`Next`**

In **`Configure the crawler's output`**, choose the Database created in the previous step and leave the other options default. Choose **`Next`** 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/crawlerOutput.jpg "See IAM roles option")

In the Review all the steps, choose **`Finish`**


## Running the crawler Job

Now it is time to run the crawler Job so that AWS Glue can scan the files on S3 and detect the tables automactically.

Select the Job name using the check box and choose **`Run Crawler`**. 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/runcrawler.jpg "Run crawler Job")

Wait a few minutes for the crawler to read the files and build the external tables that will be used later with Amazon Redshift Spectrum. 

Notice that two tables have been added by the crawler in the database 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/addedtables.jpg "Added Tables")

Choose Tables on the left-hand side and review the tables added. Hit the refresh button in case they don’t show-up. 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/checktables.jpg "Check added Tables")

Choose one of the tables to see the schema definition and properties. 

On the same screen on right top corner, click **`View Partitions`** button to view the partitions detected automatically by the crawler.  
Notice the `year` and `month` matches with the S3 partitioning using the fields `l_yearmonth` and `o_l_yearmonth` values year and month on tables `lineitem` and `order`. The partitioning will help reduce the number of files Redshift Spectrum has to scan when querying the data on S3. 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/partitions.jpg "Review Partitions")


## Querying Data on S3 using Redshift Spectrum

Open the Redshift Client tool of your choice you installed in the previous steps. 

You will now create the external schema on Redshift that will associate with the tables discoverd by the `AWS Glue Crawler` executed in the previous step. The external tables properties are kept in the AWS Glue catalog and it is integrated with Amazon Redshift Spectrum. 

 Execute the following query to return the external schemas and objects. We haven't created the external schema yet. The external schema creation will associate Amazon Redshift with the external catalog in AWS Glue. The result expected should be 0 rows.

```SQL
/* Query will return external tables */ 
SELECT s.schemaname, databasename, tablename, location, input_format FROM SVV_EXTERNAL_SCHEMAS AS S
JOIN SVV_EXTERNAL_TABLES AS T ON S.schemaname = T.schemaname;
```

To create the external tables, first you will need to create an external schema that will reference the tables discovered by the crawlers in AWS Glue. Execute the following command to create the external schema that will reference the external tables.  

```SQL
/* Create External Schema and reference Glue Database */
create external schema spectrum
from data catalog
database '<your-glue-database-name>'
iam_role '<redshift-role-assigned-to-your-cluster>'
``` 

**Important** for the `database` parameter make sure you type the same database name you defined when you created the database on AWS Glue. If you are unsure, go back to AWS Console, AWS Glue and choose Databases to confirm the database name. 

*** iam_role *** You will need the role arn with permission to access the files on S3. Use the steps bellow to retrieve the role arn assigned for the Redshift Cluster. 

Replace the parameters `database` and `iam_role` with the values from your enviroment.

Not sure how to access the IAM role associated with your Amazon Redshift Cluster?  Access to get the steps on how to retrieve the IAM role associated with your Amazon Redshift Cluster. Please access [`Redshift IAM Roles`](https://github.com/andrehass/RedshiftWorkshop/blob/master/IAM-role.md)



Now you have schema that references the tables discovered by the AWS Glue crawler in the previous steps. Execute the following query again to see the tables and schema. 

```SQL
/* Query will return external tables */
SELECT s.schemaname, databasename, tablename, location, input_format FROM SVV_EXTERNAL_SCHEMAS AS S
JOIN SVV_EXTERNAL_TABLES AS T ON S.schemaname = T.schemaname;
```

The files for the external tables `orders` and `lineitem` are stored using Hive style partitioning on a year/month date field. Run the following query to see the partition detected by the crawler. Notice the fields location and values match 

```SQL 
/* Query partitions for the external tables */ 
SELECT schemaname, tablename, values, location FROM 
SVV_EXTERNAL_PARTITIONS;
``` 

Now you will Query data using Redshift Spectrum and join with tables stored localy in Amazon Redshift. 

The most common use for Redshift Spectrum is when customers have cold and warm data and they need to offload the cold data to S3. Cold data stored in s3, can be queried using external table through Redshift Spectrum. Whereas the data stored local in Redshift only contain frequently accessed warm data, used by users and for daily reports.  

In this Lab your Redshift Cluster stores data `between 1996-01 and 1998-08` year/month. The cold data has been offloaded to S3 and contains cold/historical data from `1992-01 through 1995-12`. You will be able to query the cold data via Redshift Spectrum. 

The data stored on S3 uses parquet file format partitioned by month and year on the fields o_yearmonth, l_yearmonth for the tables orders and lineitem respectively. S3 stores historical data from 1992-01 through 1995-12. 


Run the query bellow to confirm that are no data before 1996-01 for tables orders and lineitem  stored in Amazon Redshift. It should return zero rows.

```SQL
/* Table orders */ 
select count(*), o_yearmonth from orders
GROUP BY o_yearmonth 
HAVING o_yearmonth < '1996-01'
ORDER BY o_yearmonth;

/* Table lineitem */ 
select count(*), l_yearmonth from lineitem
GROUP BY l_yearmonth 
HAVING l_yearmonth < '1996-01'
ORDER BY l_yearmonth;

```
Now execute the same query using Amazon Redshift Spectrum Tables. Please notice that there is a schema called spectrum in front of the table name. This is the external schema name you created earlier to access external tables. It is expected to return values as the historical date is stored in S3. Redshift Spectrum is being used to query data on Data lake.

```SQL
/* Spectrum orders table */
select count(*), o_yearmonth from spectrum.orders
GROUP BY o_yearmonth 
HAVING o_yearmonth < '1996-01'
ORDER BY o_yearmonth; 

/* Spectrum lineitem table */
select count(*), l_yearmonth from spectrum.lineitem
GROUP BY l_yearmonth 
HAVING l_yearmonth < '1996-01'
ORDER BY l_yearmonth;
```

Now you will run a Query on the external table **`lineitem`** to get historical data on S3. That means any data with month and year before 1996-01 is stored on S3 and will be returned using external tables. 

Query to compute the revenue based on discount applied. 

```SQL
SELECT (l_extendedprice * l_discount) as revenue
FROM spectrum.lineitem
   WHERE l_yearmonth <= '1993-01'
AND l_discount between 0.04 - 0.01 and 0.04 + 0.01
   AND l_quantity < 24;
```

Now for the next Query, you will retrieve results using Redshift Spectrum and join with local tables stored in Redshift(dimensions).  
The tables stored in Redshift Cluster are; **nation**, **customer** and **supplier**. Tables **orders** and **lineitem** have most current data stored in Redshift Cluster and is using distribution style `key` so that they data is distributed across the nodes/slices.   Historical data with the dates before 1996-01 is stored on S3 using parquet format. Remember that you will be able to access this data through external tables in schema `spectrum`; **orders** and **lineitem**. 

You will now create a view object with an UNION between local tables in Amazon Redshift and external tables in Redshift Spectrum.   
This approach allows the users submit a query using the `View object` instead of writing the SQL statement to join both tables.  
Using the `View` you will be able to access orders and lineitem data from both tables in Amazon Redshift and S3 Data Lake with Redshift Spectrum at the same time. In addition Views defined  with `schema binding` option allows you to change your view without checking for   objects dependencies for the SQL statements specified in the view. It gives you flexibility to add or remove collumns for your queries on S3. 

```SQL
/* Create view to access data stored in orders table in Redshift and Redshift Spectrum */
CREATE VIEW vw_orders as 
select o_orderkey, o_custkey, o_orderstatus, o_totalprice, o_orderdate, o_orderpriority, 
o_shippriority, o_yearmonth FROM public.orders
UNION ALL
select o_orderkey, o_custkey, o_orderstatus, o_totalprice, CAST(o_orderdate AS date) as o_orderdate, o_orderpriority,
o_shippriority, o_yearmonth from spectrum.orders 
with no schema binding;

/* Create view to access data stored in lineitem table in Redshift and Redshift Spectrum */
CREATE VIEW vw_lineitem as 
select  l_orderkey, l_partkey, l_suppkey,l_linenumber, l_quantity, l_discount, l_tax, l_returnflag, l_extendedprice, l_linestatus, l_shipdate, l_yearmonth, l_commitdate, l_receiptdate
FROM public.lineitem 
UNION ALL
select  l_orderkey, l_partkey, l_suppkey,l_linenumber, l_quantity , l_discount, l_tax,  l_returnflag, l_extendedprice, l_linestatus, CAST(l_shipdate AS date) as l_shipdate,  l_yearmonth, CAST(l_commitdate AS date) as l_commitdate, CAST(l_receiptdate AS date) as l_receiptdate 
FROM spectrum.lineitem 
with no schema binding;
```

Now execute the query provided to access data in S3 with Redshift Spectrum and join with local tables in Amazon Redshift seamlessly using the views you just created. Please have some patience, the query execution should take approximately 3 minutes to execute. Notice the query is using the views `vw_lineitem` and `vw_orders` instead of the tables. Both views query data using tables in Redshift and external tables using Redshift Spectrum. Depending on the date parameters passed in the query, it will retrieve data from either S3 Data Lake or Amazon Redshift. 

```SQL
select
	supp_nation,
	cust_nation,
	l_year,
	sum(volume) as revenue
from
	(
		select
			n1.n_name as supp_nation,
			n2.n_name as cust_nation,
			extract(year from l_shipdate) as l_year,
			l_extendedprice * (1 - l_discount) as volume
		from
			supplier,
			vw_lineitem,
			vw_orders,
			customer,
			nation n1,
			nation n2
		where
			s_suppkey = l_suppkey
			and o_orderkey = l_orderkey
			and c_custkey = o_custkey
			and s_nationkey = n1.n_nationkey
			and c_nationkey = n2.n_nationkey
			and (
				(n1.n_name = 'INDIA' and n2.n_name = 'INDONESIA')
				or (n1.n_name = 'INDONESIA' and n2.n_name = 'INDIA')
			)
			and l_shipdate between date '1995-01-01' and date '1996-12-31'
	) as shipping
group by
	supp_nation,
	cust_nation,
	l_year
order by
	supp_nation,
	cust_nation,
	l_year;
```


The following query will access tables **orders** and **lineitem** using Amazon Redshift Spectrum to access the data lake on S3 and join with a customer table stored localy on Amazon Redshift. You can use either the view or directedly access the external table. Just make sure to reference the table with the schema spectrum. Notice the schema spectrum is specified before the table names. 

```SQL
/* 12 */
select
	l_orderkey,
	sum(l_extendedprice * (1 - l_discount)) as revenue,
	o_orderdate,
	o_shippriority
from
	customer,
	spectrum.orders,
	spectrum.lineitem
where
	c_mktsegment = 'HOUSEHOLD'
	and c_custkey = o_custkey
	and l_orderkey = o_orderkey
	and o_orderdate < date '1995-03-02'
	and l_shipdate > date '1995-03-02'
group by
	l_orderkey,
	o_orderdate,
	o_shippriority
order by
	revenue desc,
	o_orderdate
limit 10;
```

Lastly, you will execute a query againt table `lineitem` using a parameter field that has the data paritioned on S3. Redshift Spectrum will make the use of partitioning on S3 to limit the number of scanned files on S3  

```SQL
select
	l_returnflag,
	l_linestatus,
	sum(l_quantity) as sum_qty,
	sum(l_extendedprice) as sum_base_price,
	sum(l_extendedprice * (1 - l_discount))::decimal(38,2) as sum_disc_price,
	sum(l_extendedprice::decimal(38,2) * (1 - l_discount) * (1 + l_tax))::decimal(38,2) as sum_charge,
	avg(l_quantity) as avg_qty,
	avg(l_extendedprice) as avg_price,
	avg(l_discount) as avg_disc,
	count(*) as count_order
from
	spectrum.lineitem
where
	l_yearmonth < '1994-06'
group by
	l_returnflag,
	l_linestatus
order by
	l_returnflag,
	l_linestatus;
```

Now you will run the **EXPLAIN** command to retrieve the execution plan for the query. Notice that there is a filter by the partition defined in the predicate. < '1994-06'

```SQL
EXPLAIN (select
	l_returnflag,
	l_linestatus,
	sum(l_quantity) as sum_qty,
	sum(l_extendedprice) as sum_base_price,
	sum(l_extendedprice * (1 - l_discount))::decimal(38,2) as sum_disc_price,
	sum(l_extendedprice::decimal(38,2) * (1 - l_discount) * (1 + l_tax))::decimal(38,2) as sum_charge,
	avg(l_quantity) as avg_qty,
	avg(l_extendedprice) as avg_price,
	avg(l_discount) as avg_disc,
	count(*) as count_order
from
	spectrum.lineitem
where
	l_yearmonth < '1994-06'
group by
	l_returnflag,
	l_linestatus
order by
	l_returnflag,
	l_linestatus);
```
You should see the following steps in the plan. The parameter provided is making use of the partitions on S3 and scaning the files that are based on the parameter provided, in this case < '1994-06'

->  XN Seq Scan PartitionInfo of spectrum.lineitem  (cost=0.00..12.50 rows=334 width=0)
		Filter: ((l_yearmonth)::text < '1994-06'::text)

# After you are done with the Labs

Delete all the resources created during the execution of this lab to avoid unexpected charged to your account. 

Pleafe refer to the following location to get instructions on how to remove AWS resources created during this lab. 

[Removing AWS resources created for the Lab.](https://github.com/andrehass/RedshiftWorkshop/blob/master/cleanresources.md)

