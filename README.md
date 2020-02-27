# Redshift Day at AWS Loft Workshop

## Goals
Amazon Redshift is cloud-based modern data warehouse designed to deliver fast query performance at lowest cost possible. In this session, you will deploy a Redshift Cluster from scratch, create the table schemas using the proper distribution style so that queries can take advantage of Massive Parallel processing using multiple nodes and slices. You will load data from files stored in the S3 data lake and execute queries in your cluster. Aditionally, you will take a hands-on approach to show you how to mine your Amazon S3 data lake using open data formats with Redshift Spectrum, a feature of Redshift, without the need for unnecessary data movement. This enables you to analyze data across your data warehouse and data lake, together, with a single service.

## Labs
|Lab  |Lab Description |
|---- | ----|
|[1 - Launching a Redshift Clusters in your account using Cloud Formation](Lab1/README.md)                 | You will learn how to provision an Amazon Redshift Cluster using a CloudFormation template|
|[2 - Creating Tables and Loading Data into Amazon Redshift](Lab2/README.md)           |Learn how to create tables and Load data into Amazon Redshift cluster.|
|[3 - Querying local tables in Amazon Redshift](Lab3/README.md)        |You learn how to retrieve table metadata to check distribution style, run queries in local tables, and how to identify slow performance queries|
|[4 - Querying S3 Data Lake using Redshift Spectrum](Lab4/README.md)      | You learn how to query data in your data warehouse and exabytes of data in your S3 data lake, using Redshift Spectrum |
|[5 - Operations](Lab5/README.md)      | Step through some common operations a Redshift Administrator may have to do to maintain their Redhshift environment including, Cluster Encryption, Snapshots, and Elastic Resize |
|[6 - Remove AWS resources created for the Lab](/cleanresources.md)      | Make sure you remove all the resources created at the end of the labs to avoid unexpected charges |
