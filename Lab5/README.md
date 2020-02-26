In this lab, we step through some common operations a Redshift Administrator may have to do to maintain their Redhshift environment.

## Contents
* [Before You Begin](#before-you-begin)
* [Event Subscriptions](#event-subscriptions)
* [Cluster Encryption](#cluster-encryption)
* [Cross Region Snapshots](#cross-region-snapshots)
* [Elastic Resize](#elastic-resize)
* [Before You Leave](#before-you-leave)

## Before You Begin
This lab assumes you have launched a Redshift cluster.  If you have not launched a cluster, see [LAB 1 - Creating Redshift Clusters](../lab1.html).

## Event Subscriptions
1. Navigate to your Redshift Events page.  Notice the *Events* involved with creating the cluster.  
```
https://console.aws.amazon.com/redshift/home?#events:cluster=
``` 
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations1.png "Event Subscription")

2. Click on the *Subscriptions* tab and then click on the *Create Event Subscription* button.

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations2.png "Event Subscription2")

3. Create a subscription for *any* severity *management* notification on *any cluster*.   Notice the types of event on the right you will recieve a notification for.

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations3.png "Event Subscription3")

4. Name the subscription *ClusterManagement* and click *Next*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations4.png "Event Subscription4")

5. Select the *Create New Topic* tab and enter the topic name *ClusterManagement*.  Add your email address and click *Add Recipient*.  Finally, click *Create*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations5.png "Event Subscription5")

6. You will recieve an email shortly.  Click on the *Confirm subscription* link in the email.

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations6.png "Event Subscription6")

7. The link should take you to a final confirmation page confirming the subscription.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations7.png "Event Subscription7")

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

5. You should also receive an email notification about the cluster resize because of the event subscription we setup earlier.

![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations11.png "Cluster Encryption 4")

## Cross Region Snapshots
1. Navigate to your Redshift Cluster list.  Select your cluster and click on *Backup* -> *Configure Cross-region snapshots*.
```
https://console.aws.amazon.com/redshift/home?#cluster-list
```
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations12.png "Cross Region Snapshots")

2. Select the *Yes* radio button to enable the copy.  Select the destination region of *us-east-2*.  Because the cluster is encrypted you must establish a grant in the other region to allow the snapshot to be re-encrypted.  Select *No* for the Existing Snapshot Copy Grant.  Name the Snapshot Copy Grant with the value *SnapshotGrant*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations13.png "Cross Region Snapshots2")

3. To demonstrate the cross-region replication, initiate a manual backup.  Click on *Backup* -> *Take Snapshot*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations14.png "Cross Region Snapshot3")

4. Name the snapshot *CRRBackup* and click *Create*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations15.png "Cross Region Snapshots4")

5. Navigate to your list of snapshots and notice the snapshot is being created. 
```
https://console.aws.amazon.com/redshift/home?#snapshots:id=;cluster=
```
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations16.png "Cross Region Snapshots5")

6. Wait for the snapshot to finish being created.  The status will be *available*.
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations17.png "Cross Region Snapshots6")

7. Navigate to the us-east-2 region by select *Ohio* from the region drop down, or navigate to the following link.  Notice that the snapshot is available and is in a *copying* status. 
```
https://us-east-2.console.aws.amazon.com/redshift/home?region=us-east-2#snapshots:id=;cluster=
```
![alt text](https://github.com/andrehass/RedshiftWorkshop/blob/master/Images/operations18.png "Cross Region Snapshots7")

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
