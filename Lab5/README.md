+++
title = "Operations"
date = 2020-01-22T16:27:02-08:00
weight = 7
pre = "<b>7. </b>"
+++

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
{{< html.inline >}}
<table><tr><td><img src=/images/operations1.png></td></tr></table> 
{{< /html.inline >}}

2. Click on the *Subscriptions* tab and then click on the *Create Event Subscription* button.
{{< html.inline >}}
<table><tr><td><img src=../images/operations2.png></td></tr></table> 
{{< /html.inline >}}

3. Create a subscription for *any* severity *management* notification on *any cluster*.   Notice the types of event on the right you will recieve a notification for.
{{< html.inline >}}
<table><tr><td><img src=../images/operations3.png></td></tr></table>
{{< /html.inline >}}

4. Name the subscription *ClusterManagement* and click *Next*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations4.png></td></tr></table>
{{< /html.inline >}}

5. Select the *Create New Topic* tab and enter the topic name *ClusterManagement*.  Add your email address and click *Add Recipient*.  Finally, click *Create*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations5.png></td></tr></table>
{{< /html.inline >}}

6. You will recieve an email shortly.  Click on the *Confirm subscription* link in the email.
{{< html.inline >}}
<table><tr><td><img src=../images/operations6.png></td></tr></table>
{{< /html.inline >}}

7. The link should take you to a final confirmation page confirming the subscription.
{{< html.inline >}}
<table><tr><td><img src=../images/operations7.png></td></tr></table>
{{< /html.inline >}}

## Cluster Encryption
Note: This portion of the lab will take ~45 minutes to complete based on the data loaded in [LAB 2 - Creating Redshift Clusters](../lab2.html).  Please plan accordingly.

1. Navigate to your Redshift Cluster list.  Select your cluster and click on *Cluster* -> *Modify Cluster*.
```
https://console.aws.amazon.com/redshift/home?#cluster-list
```
{{< html.inline >}}
<table><tr><td><img src=../images/operations8.png></td></tr></table>
{{< /html.inline >}}

2. Select *KMS* for the database encryption and then click *Modify*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations9.png></td></tr></table>
{{< /html.inline >}}

4. Notice your cluster enters a *resizing* status.  The process of encrypting your cluster is similar to resizing your cluster using the classic resize method.  All data is read, encrypted and re-written. During this time, the cluster is still avialable for read queries, but not write queries.
{{< html.inline >}}
<table><tr><td><img src=../images/operations10.png></td></tr></table>
{{< /html.inline >}}

5. You should also receive an email notification about the cluster resize because of the event subscription we setup earlier.
{{< html.inline >}}
<table><tr><td><img src=../images/operations11.png></td></tr></table>
{{< /html.inline >}}

## Cross Region Snapshots
1. Navigate to your Redshift Cluster list.  Select your cluster and click on *Backup* -> *Configure Cross-region snapshots*.
```
https://console.aws.amazon.com/redshift/home?#cluster-list
```
{{< html.inline >}}
<table><tr><td><img src=../images/operations12.png></td></tr></table>
{{< /html.inline >}}

2. Select the *Yes* radio button to enable the copy.  Select the destination region of *us-east-2*.  Because the cluster is encrypted you must establish a grant in the other region to allow the snapshot to be re-encrypted.  Select *No* for the Existing Snapshot Copy Grant.  Name the Snapshot Copy Grant with the value *SnapshotGrant*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations13.png></td></tr></table>
{{< /html.inline >}}

3. To demonstrate the cross-region replication, initiate a manual backup.  Click on *Backup* -> *Take Snapshot*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations14.png></td></tr></table>
{{< /html.inline >}}

4. Name the snapshot *CRRBackup* and click *Create*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations15.png></td></tr></table>
{{< /html.inline >}}

5. Navigate to your list of snapshots and notice the snapshot is being created. 
```
https://console.aws.amazon.com/redshift/home?#snapshots:id=;cluster=
```
{{< html.inline >}}
<table><tr><td><img src=../images/operations16.png></td></tr></table>
{{< /html.inline >}}

6. Wait for the snapshot to finish being created.  The status will be *available*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations17.png></td></tr></table>
{{< /html.inline >}}

7. Navigate to the us-east-2 region by select *Ohio* from the region drop down, or navigate to the following link.  Notice that the snapshot is available and is in a *copying* status. 
```
https://us-east-2.console.aws.amazon.com/redshift/home?region=us-east-2#snapshots:id=;cluster=
```
{{< html.inline >}}
<table><tr><td><img src=../images/operations18.png></td></tr></table>
{{< /html.inline >}}

## Elastic Resize
Note: This portion of the lab will take ~15 minutes to complete based on the data loaded in [LAB 2 - Creating Redshift Clusters](../lab2.html).  Please plan accordingly.
1. Navigate to your Redshift Cluster list.  Select your cluster and click on *Cluster* -> *Resize*.  Note, if you don't see your cluster, you may have to change the *Region* drop-down.
{{< html.inline >}}
<table><tr><td><img src=../images/operations19.png></td></tr></table>
{{< /html.inline >}}

2. Ensure the *Elastic Resize* radio is selected.  Choose the *New number of nodes*, and click *Resize*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations20.png></td></tr></table>
{{< /html.inline >}}

3. When the resize operation begins, you'll see the Cluster Status of *prep-for-resize*.
{{< html.inline >}}
<table><tr><td><img src=../images/operations21.png></td></tr></table>
{{< /html.inline >}}

4. When the operation completes, you'll see the Cluster Status of *available* again.
{{< html.inline >}}
<table><tr><td><img src=../images/operations22.png></td></tr></table>
{{< /html.inline >}}

## Before You Leave
If you are done using your cluster, please think about decommissioning it to avoid having to pay for unused resources.
