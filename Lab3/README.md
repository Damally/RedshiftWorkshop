# LAB 3 - Querying local tables in Amazon Redshift



## Contents
  - [Accessing Table Metadata](#accessing-table-metadata)
  - [Distribution Styles](#distribution-styles)
  - [Quering Amazon Redshift](#quering-amazon-redshift)
  - [Query Performance and Troubleshooting](#query-performance-and-troubleshooting)

## Accessing Table Metadata

First, you will execute some queries to get table definition details such as table information and distribution style defined on the tables. Redshift is a Massive Parallel processing Data Warehouse and uses multiple nodes to process the queries depending on the distribution style selected. 


Run the following query to get details on the number of nodes and slices in your Amazon Redshift cluster. The Query will return details on number of nodes and slices in your Redshift Cluster. In this Lab, you should expect a total of 4 slices. Your Amazon Redshift cluster type is dc2.xlarge with 2 compute nodes.

```sql
SELECT * FROM STV_SLICES;
```

Run the query bellow to retrieve table definition details such as distribution style, sort key, compression enconding algorithms, and number of rows. 

```sql
SELECT "schema", "table", diststyle, sortkey1, encoded, tbl_rows FROM SVV_TABLE_INFO
WHERE "schema" = 'public';
```

## Distribution Styles 

**Even**  
The leader node distributes the rows across the slices in a round-robin fashion, regardless of the values in any particular column. EVEN distribution is appropriate when a table does not participate in joins or when there is not a clear choice between KEY distribution and ALL distribution.  

**Key**  
The rows are distributed according to the values in one column. The leader node places matching values on the same node slice. If you distribute a pair of tables on the joining keys, the leader node collocates the rows on the slices according to the values in the joining columns so that matching values from the common columns are physically stored together.  

**ALL**  
A copy of the entire table is distributed to every node. Where EVEN distribution or KEY distribution place only a portion of a table's rows on each node, ALL distribution ensures that every row is collocated for every join that the table participates in.
Query below shows the number of rows distributed across the Redshift cluster nodes and slices. For tables with distribution style key, the number of rows is distributed based on hash of the values of the column selected as key for the distribution style.  

The query below shows the number of rows distributed across the Redshift cluster nodes and slices. For tables with distribution style key, the number of rows is distributed based on hashed values of the column selected as key.

## Quering Amazon Redshift

Now you will experiment on running some some queries in Redshift accessing the data your loaded in the previous lab. 

The following query, is executed in multiple nodes/slice based on the distribution key and join the dimensions that are set to distribution ALL in every node. The Leader node compiles the query and send to compute nodes nodes for parallel processing. Each compute node node process the query based on the portion of data stored on local node and join with the dimensions.

```sql
select s_acctbal, s_name, n_name, p_partkey, p_mfgr, s_address, s_phone, s_comment
from
part, supplier, partsupp, nation, region
where
    p_partkey = ps_partkey
    and s_suppkey = ps_suppkey
    and p_size = 5
    and p_type like '%TIN'
    and s_nationkey = n_nationkey
    and n_regionkey = r_regionkey
    and r_name = 'AFRICA'
    and ps_supplycost = (
        select
            min(ps_supplycost)
                from partsupp, supplier, nation, region
                where
                    p_partkey = ps_partkey
                    and s_suppkey = ps_suppkey
                    and s_nationkey = n_nationkey
                    and n_regionkey = r_regionkey
                    and r_name = 'AFRICA')
order by
s_acctbal desc,
n_name,
s_name,
p_partkey
limit 100;
```

The following query access the table `lineitem` and process some aggregations on compute nodes. The table lineitem is using `key` distribution style, and has approximately 250M of rows. Since there is no join with additional tables, the aggregation is processed on each individual slice and the results are returned to the leader node. 

```sql
SELECT   l_returnflag, 
         l_linestatus, 
         Sum(l_quantity)                                                                     AS sum_qty,
         Sum(l_extendedprice)                                                                AS sum_base_price,
         Sum(l_extendedprice                * (1 - l_discount))::decimal(38,2)               AS sum_disc_price,
         sum(l_extendedprice::decimal(38,2) * (1 - l_discount) * (1 + l_tax))::decimal(38,2) AS sum_charge,
         avg(l_quantity)                                                                     AS avg_qty,
         avg(l_extendedprice)                                                                AS avg_price,
         avg(l_discount)                                                                     AS avg_disc,
         count(*)                                                                            AS count_order
FROM     lineitem 
WHERE    l_shipdate <= cast ( date '1998-12-01' - interval '66 days' AS date ) 
GROUP BY l_returnflag, 
         l_linestatus 
ORDER BY l_returnflag, 
         l_linestatus;
```

The following Query join tables `orders`, `lineitem` (both using distribution style key), and customers distribution style ALL. 


```sql
SELECT   l_orderkey, 
         SUM(l_extendedprice * (1 - l_discount)) AS revenue, 
         o_orderdate, 
         o_shippriority 
FROM     customer, 
         orders, 
         lineitem 
WHERE    c_mktsegment = 'HOUSEHOLD' 
AND      c_custkey = o_custkey 
AND      l_orderkey = o_orderkey 
AND      o_orderdate < DATE '1996-03-02' 
AND      l_shipdate >  DATE '1996-03-02' 
GROUP BY l_orderkey, 
         o_orderdate, 
         o_shippriority 
ORDER BY revenue DESC, 
         o_orderdate limit 10;
```

## Query Performance and Troubleshooting

Now we are going to track query execution and collect performance metrics using Amazon Redshift System tables and Views; 
There are two types of system tables: STL and STV tables.

**STL** tables are generated from logs that have been persisted to disk to provide a history of the system.  
**STV** tables are virtual tables that contain snapshots of the current system data.

In addition to System Tables there are System View and will assist your further when troubleshooting performance problemas on Amazon Redshift. There are two types of views SVL and SVV. Both system views contains a subset of data found in several of the STL and STV system tables. You will use the following approach to detect and troubleshoot slow queries in your Amazon Redshift Cluster. 

Run the query bellow to identify the top 5 queries you recently ran on your Redshift Cluster. 

```sql
select query, trim(querytxt) as sqlquery
from stl_query
order by query desc limit 5;
```

If you want to check on query execution metrics information, you can query the system table log **`STL_QUERY_METRICS`** which contains infomration such as,  number of rows processed, CPU usage, Disk I/O, and disk usage, for queries that have completed

```sql
/* Find Top Queries by Duration, Number of reads, Memory to Disk */
SELECT querytxt, MAX(CPU_TIME) AS cpu_time_micro, MAX(ROWS) AS ROWS, MAX(BLOCKS_READ)AS blocks_read, 
MAX(blocks_to_disk)as MB_to_Disk, SUM(run_time/1000000)as time_sec, SUM((run_time/1000000)/60)as time_minutes  FROM STL_QUERY AS Q 
JOIN STL_QUERY_METRICS AS M
ON Q.query = M.query 
GROUP BY querytxt
ORDER BY time_sec desc 
```

To view metrics for active queries that are currently running, see the **`STV_QUERY_METRICS`** system view instead. Notice that the difference is only in the initial three letters; STV instead of STL, therefore it is a system view. 


You can use the **`SVL_QUERY_REPORT`** system view for advanced query troubleshooting, such as identifying memory usage, data skew, disk spills as well as check for execution details on each step. 

Run the a query on **`STL_QUERY`** to identify the most recent queries you have ran and copy the query_ID for the query you want more details. You are going to use in the **`svl_query_report`** next. 

```sql
select query, trim(querytxt) as sqlquery
from stl_query
where label not in ('metrics','health')
order by query desc limit 40;
```

|query |    sqlquery
|------|--------------------------------------------------
|129 | select query, trim(querytxt) from stl_query order by query;
|128 | select node from stv_disk_read_speeds;
|127 | select system_status from stv_gui_status
|126 | select * from systable_topology order by slice
|125 | load global dict registry
(5 rows)

Now you will run the query using the viw **`svl_query_report`** view to get detailed query information for troubleshooting. Replace the query parameter with the query_ID you identified from the previous query. Chose a query that you recognize from previous executions. 

```sql
select query, segment, step, max(rows), min(rows),
case when sum(rows) > 0
then ((cast(max(rows) -min(rows) as float)*count(rows))/sum(rows))
else 0 end
from svl_query_report
where query = 279
group by query, segment, step
order by segment, step;

