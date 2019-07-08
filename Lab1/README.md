# LAB 1 - Launching a Redshift Clusters in your account using Cloud Formation. 
In this lab you will launch a new Redshift Cluster, Install the client tool of your preference and setup connectivity to stablish a connectivity with Amazon Redshift Cluster.

## Contents
  - [Prerequisites](#Prerequisites)
  - [Cloud Formation](#Cloud-Formation)
  - [Launching Redshift Cluster](#Launching-Redshift-Cluster)
  - [Installing client tool](#Installing-client-tool)
  - [Connecting to your Redshift Cluster](#Connecting-to-your-Redshift-Cluster)


## Prerequisites
In this exercise, we will launch a Redshift Cluster in your account. Using a Cloud Formation template provided in the link bellow. For better performance and avoid cost transfer between Amazon Redshift cluster and S3, make sure you are in the US-EAST-1 region.


## Cloud Formation
In the Cloudformation template provided, we will launch a Redshift Cluster in your account. We will also need to create resources in your account that are required for security and be able to access the cluster from a public endpoint. 

Here are the following resources we will create in your account
* Amazon Redshift Cluster with 2 dc.xlarge nodes  
* VPC and Subnet 
* Security Groups for Amazon Redshift access 
* Internet Gateway to make Amazon Redshift accessible publicy 
* Route Table with a route to the internet
* IAM Role that will be attached to your Amazon Redshift Cluster 
  

## Launching Redshift Cluster

Go ahead and use Cloudformation link provided to launch a Redshift Cluster in your account. You should be able see the initial `Create Stack` page 

[![Launch](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=RedshiftDay&templateURL=https://s3.amazonaws.com/reinvent-hass/code/redshiftTemplate.json)  

On Specify stack details, provide a MasterUserName and MasterUserPassword of your choice. Leave all the other parameters unchanged and choose next. 

![Cloud Formation](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/CloudFormationParameter1.jpg "Cloud Formation Template")

In the `Configure stack options`, Click Next 

In the Review, check to acknowledge the creation of IAM resources and click Create Stack. Wait a few minutes for the cluster to become available.

![Cloud Formation Acknowledgment](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/CloudFormationAck.jpg "Cloud Formation Acknowledgment")


## Installing client tool

You will need to install a client tool to be able to connect on the Redshift Cluster. The following client tools are suggested for this Lab. Please feel free to use any tool you prefer. If you are familiar with Postgres command line client tool (psql), you can also use as your client. 



* [DB Beaver](https://dbeaver.io/download/)
* [WorkbenchJ](https://www.sql-workbench.eu/downloads.html) 
* [Aginity - Windows Only](https://www.aginity.com/main/workbench-for-amazon-redshift/)


 You also can query the data in your Amazon Redshift cluster directly from your AWS Management console, using the new `Query Editor`. 

 ![Query Editor](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/queryeditor.jpg "Query Editor")

## Connecting to your Redshift Cluster

On AWS console main page, go to Services and select Amazon Redshift. Alternatively, type Redshift in the search field and choose Amazon Redshift when you see in the results returned.
In the Redshift dashboard, choose Clusters and you should see your cluster recently created listed and the current status. Choose your cluster to access Redshift cluster details and connections information. 

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/Redshift_WS_Console.jpg "Logo Title Text 1")

Copy the endpoint details and save it to connect on the cluster and run queries. The endpoint will look similar to this one `redshiftday.xxxxxxxxxx.us-east-1.redshift.amazonaws.com:5439`

You may need the JDBC URL depending on the tool you are using to connect to the Redshift Cluster. Scroll down on the same page and look for JDBC URL.  

JDBC URL will look similar to the JDBC URK bellow; 
`jdbc:redshift://redshiftday.xxxxxxxxxx.us-east-1.redshift.amazonaws.com:5439/dev`

Use the connection information you just captured to access your cluster. 

Credentials to log into the Redshift cluster.  

**Username:** `user name` (user name you defined in the CloudFormation step.)  

**Password:**  `password` (password you defined in the CloudFormation step.)


You should now connect to your Amazon Redshift Cluster. You now can move to the Lab 2. 

**Important** If you leave earlier or don't have plans to complete the remaining Labs. Make sure you delete the resources created in your acoount to avoid any unespected charges. Please refer to `Remove CloudFormation Stack`  for instructions on how to remove the CloudFormation Stack. 

