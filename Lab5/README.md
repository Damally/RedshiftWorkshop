In this lab, we step through some common operations a Redshift Administrator may have to do to maintain their Redhshift environment.

## Contents
* [Before You Begin](#before-you-begin)
* [Cluster Encryption](#cluster-encryption)
* [Elastic Resize](#elastic-resize)
* [Before You Leave](#before-you-leave)

## Before You Begin
This lab assumes you have launched a Redshift cluster.  If you have not launched a cluster, see [LAB 1 - Creating Redshift Clusters](../lab1.html).


## Cluster Encryption
Note: This portion of the lab will take ~45 minutes to complete based on the data loaded in [LAB 2 - Creating Redshift Clusters](../lab2.html).  Please plan accordingly.

1. Navigate to your Redshift Cluster list.  Select your cluster and click on *Cluster* -> *Modify Cluster*.
```
https://console.aws.amazon.com/redshift/home?#cluster-list
```
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations8.png "Cluster Encryption 1")

2. Select *KMS* for the database encryption and then click *Modify*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations9.png "Cluster Encryption 2")

4. Notice your cluster enters a *resizing* status.  The process of encrypting your cluster is similar to resizing your cluster using the classic resize method.  All data is read, encrypted and re-written. During this time, the cluster is still avialable for read queries, but not write queries.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations10.png "Cluster Encryption 3")


## Elastic Resize
Note: This portion of the lab will take ~15 minutes to complete based on the data loaded in [LAB 2 - Creating Redshift Clusters](../lab2.html).  Please plan accordingly.
1. Navigate to your Redshift Cluster list.  Select your cluster and click on *Cluster* -> *Resize*.  Note, if you don't see your cluster, you may have to change the *Region* drop-down.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations19.png "Elastic Resize 1")

2. Ensure the *Elastic Resize* radio is selected.  Choose the *New number of nodes*, and click *Resize*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations20.png "Elastic Resize 2")

3. When the resize operation begins, you'll see the Cluster Status of *prep-for-resize*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations21.png "Elastic Resize 3")

4. When the operation completes, you'll see the Cluster Status of *available* again.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations22.png "Elastic Resize 4")

## Before You Leave
If you are done using your cluster, please think about decommissioning it to avoid having to pay for unused resources.
