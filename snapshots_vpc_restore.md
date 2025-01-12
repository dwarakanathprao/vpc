---

copyright:
  years: 2021
lastupdated: "2021-03-05"

keywords: snapshots, virtual private cloud, boot volume, data volume, volume, data storage, virtual server instance, instance
subcollection: vpc

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:important: .important}
{:screen: .screen}
{:pre: .pre}
{:table: .aria-labeledby="caption"}
{:note: .note}
{:beta: .beta}

# Restoring a volume from a snapshot (Beta)
{: #snapshots-vpc-restore}

Now that you created a snapshot, how do you use it? By restoring from a snapshot, you create a new, fully-provisioned boot or data volume. Restoring from a snapshot of a boot volume creates a new boot volume that you can use when you provision a new instance. Restoring from a snapshot of a data volume creates a secondary volume that's attached to the instance.
{:shortdesc}

This beta feature is available only to accounts with special approval.
{:beta}

## About restoring a volume from a snapshot
{: #snapshots-vpc-restore-concepts}

Restoring a volume from a snapshot creates a new volume. The new volume has the same properties of the original volume. If you restore from a bootable snapshot, you create a boot volume. Similarly, you can create a new data volume from a snapshot of a data volume. The volume you create from the snapshot uses the same volume [profile](docs/vpc?topic=vpc-block-storage-profiles) and contains the same data and meta-data as the original volume. If the source volume used [customer-managed encryption](/docs/vpc?topic=vpc-vpc-encryption-about#vpc-customer-managed-encryption), the volume created from the snapshot inherits that encryption.

You restore a volume from a [virtual server instance when you provision a new instance](#snapshots-vpc-restore-vol-UI). You can also [restore a volume from an existing instance](#snapshots-vpc-create-from-vol-ui). The snapshot must be in a _stable_ state before you can restore a volume from it.

When you create a new instance, you can select a snapshot for the operating system. From there, you select a _bootable snapshot_, a snapshot taken of a boot volume. This creates a new boot volume for that instance.

You can create and attach a data volume to the new instance during instance provisioning or from an existing instance. During the create and attach process, you select a snapshot of the volume to restore the volume. A new volume is created and attached to the instance as secondary storage.

You can't simultaneously restore a boot and data volume.

You can also create and attach a boot volume in the same manner on existing instances. It appears in the list of block storage volumes as a boot volume.

When you select a snapshot as the source for the volume, the service immediately creates the new volume. The volume appears as _pending_ while it's being created. After the volume is hydrated (that is, fully provisioned), it contains all the data and metadata of the original volume. It retains the same capacity and IOPS performance. You can then use the boot volume to start a new instance or write data to the new attached data volume.

## Create a volume from a snapshot when you provision a new instance by using the UI
{: #snapshots-vpc-restore-vol-UI}

Follow these steps to create a boot and data volume from snapshots when you provision a new virtual server instance.

1. In the [{{site.data.keyword.cloud_notm}} console ![External link icon](../icons/launch-glyph.svg "External link icon")](https://{DomainName}/vpc-ext), go to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VPC Infrastructure > Compute > Virtual server instances**. 

   Be sure to select VPC infrastructure from the Menu icon.   
   {: tip}

1. Click **Create** and provision your new instance with the information from the table in [Creating virtual server instances by using the UI](/docs/vpc?topic=vpc-creating-virtual-servers). 

1. For the operating system, click the tab for **Snaphots**. The most recent bootable snapshot is listed. 

1. Optionally, to select a different bootable snapshot and create a boot volume, click **Change**.

1. From the list of snapshots, select a bootable snapshot for your instance's operating system and click **Save**.

  This action imports snapshot data to the boot volume. The new boot volume is created from the snapshot and is listed under Boot Volume on the instance provisioning page. 

1. To create a data volume, under Data Volumes, click **Create**. A side panel displays for creating a new data volume.

1. Select **Import from snapshot**. Expand the list to select a data snapshot. The most recent data snapshot is selected by default. Optionally, select a different snapshot from the list. The snapshot contains the properties of the original source volume, including the IOPS tier and encryption. 

1. Optionally, change the size of the volume.

1. Click **Save**. The new data volume is created from the snapshot and attached to the instance, shown in the Data volume list. 

1. Optionally, to see the API POST request for restoring the volume, click **Get sample API call**. Information displays for the call, similar to the example in [this section](#snapshots-vpc-restore-API).

1. When you're finished provisioning your new instance, click **Create virtual server instance**. The new instance appears in the list of virtual server instances.

After the instance is created, you can click on the instance name to see the instance details. The boot volume you restored from a snapshot is listed under **Storage volumes**. The camera icon indicates the volume was created from a snapshot.

## Create a volume from a snapshot from an existing virtual server instance
{: #snapshots-vpc-create-from-vol-ui}

You can also create a snapshot from the list of virtual server instances.

1. In the [{{site.data.keyword.cloud_notm}} console ![External link icon](../icons/launch-glyph.svg "External link icon")](https://{DomainName}/vpc-ext), go to **Menu icon ![Menu icon](../icons/icon_hamburger.svg) > VPC Infrastructure > Compute > Virtual server instances**. 

1. From the list, click on the name of an instance. The instance must be in a _Running_ state.

1. On the Instance details page, scroll to the list of Storage volumes and click **Attach volumes**. A side panel opens for you to define the volume attachment.

1. From the Attach data volume panel, expand the list of Block volumes and select **Create a data volume**.

1. Select **Import from snapshot**. Expand the Snapshot list and select a snapshot. You can select either a bootable snapshot or a snapshot of a data volume.

1. Optionally, increase the size of the volume within the specified range.

1. Click **Save**. The side panel closes and messages indicate that the restored volume is being attached to the instance. 

The new volume appears in the list of Storage volumes. Hover over the camera icon to see the name of the snapshot from which it was created. 

## Create a volume from a snapshot by using the CLI
{: #snapshots-vpc-restore-CLI}

Use the CLI to create a boot or data volume from a snapshot.

### Before you begin
{: #snapshots-vpc-restore-prereq-CLI}

Make sure that you downloaded, installed, and initialized the following CLI plug-ins:

* {{site.data.keyword.cloud_notm}} CLI
* The infrastructure-service plug-in

For more information, see the [CLI Reference](/docs/vpc?topic=vpc-infrastructure-cli-plugin-vpc-reference).
   
After you install the vpc-infrastructure plug-in, set the target to generation 2 by running the command `ibmcloud is target --gen 2`.
{:important}

Make sure that you [created an {{site.data.keyword.vpc_short}}](/docs/vpc?topic=vpc-getting-started).

### Create a boot volume from a snapshot
{: #snapshots-vpc-restore-boot-CLI}

Specify the `source_snapshot` parameter and snapshot ID when using the `instance-create` command to create a new instance.

Example:

```
bmcloud is instance-create my-instance-restore1 ea002578-ff10-41fe-9652-e63f7e0e3cba us-south-1 bx2-2x8 ba11a6f2-6c17-4fee-a4b5-5c016fe64376 --boot-volume
'{
   "name":"boot-from-snapshot1",
   "volume":{
      "name":"boot-from-snapshot1",
      "profile":{
         "name":"general-purpose"
      },
      "source_snapshot":{
         "id":"d857c69f-d795-46ac-85e4-f26ca3001033"
      }
   }
}'
```
{:codeblock}

```
Creating instance my-instance-restore1 in resource group under account VP 01 as user rtuser1@mycompany.com...

ID               7101_eded6dcd-4f3c-4e79-a0cb-00f7c72f38cd
Name             my-instance-restore1
CRN              crn:v1:staging:public:is:us-south-1:a/2d1bace7b46e4815a81e52c6ffeba5cf
                 ::instance:7101_eded6dcd-4f3c-4e79-a0cb-00f7c72f38cd
Status           pending
Profile          bx2-2x8
Architecture     amd64
vCPUs            2
Memory           8
Network(Gbps)    4
Image            ID                                         Name
                 6f153c4d-6a9a-496d-8063-5c39932f6ded       ibm-centos-7-6-minimal-amd64-2
VPC              ID                                         Name
                 ea002578-ff10-41fe-9652-e63f7e0e3cba       my-vpc
Zone             us-south-1
Resource group   ID                                         Name
                 cdc21b72d4f557b195de988b175e3d81           Default

Created          2020-11-17T17:03:30+08:00
Boot volume      ID                                   Name                  Attachment ID                            Attachment name
                 0651dacb-4589-4147-86b3-a77544598f93 boot-from-snapshot1                  7101-abf9dd2b-9d5d-41f1-849d-55a8ab580ddb                            boot-from-snapshot1

```
{:screen}

### Create a data volume from a snapshot
{: #snapshots-vpc-restore-data-CLI}

Specify the `source_snapshot` parameter and snapshot ID in the volume attachment.

Example:

```
bmcloud is instance-create my-instance-restore1 ea002578-ff10-41fe-9652-e63f7e0e3cba us-south-1 bx2-2x8 ba11a6f2-6c17-4fee-a4b5-5c016fe64376 --volume-attach
'{
   "name":"datavol-from-snapshot",
   "volume":{
      "name":"datavol-from-snapshot",
      "profile":{
         "name":"general-purpose"
      },
      "source_snapshot":{
         "id":"6daaaa39-3d81-4d1d-81f8-e1f6a14f97f3"
      }
   }
}'
.
.
.
```
{:codeblock}

## Restore a volume from a snapshot from the API
{: #snapshots-vpc-restore-API}

To restore a boot volume from a bootable snapshot using the snapshot ID, use this call:

```
POST /v1/instances boot_volume_attachments {snapshot_id}
```
{:pre}

To restore a data volume from a snapshot and attach it at boot time, use this call:

```
POST /v1/instances volume_attachments {snapshot_id}
```
{:pre}

To restore a data volume based based on the snapshot ID and attach it to an instance by instance ID, use this call:

```
POST /v1/instances/{instanceID} volume_attachments {snapshot_id}
```
{:pre}

This example API request creates a new instance and restores a data volume from a snapshot. Notice that `source_snapshot` is listed for the `volume_attachments` parameter. If the restored volume were a boot volume, it would be listed under `boot_volume_attachment`. 

```
curl -X POST \
"$vpc_api_endpoint/v1/instances?version=2020-10-12&generation=2" \
-H "Authorization: $iam_token" \
-H "Content-Type: application/json" \
-d '{
      "name": "my-server-name",
      "zone": {
        "name": "us-south-1"
      },
      "vpc": {
        "id": "r134-3b717398-de15-45a2-8922-100f982e87e3"
      },
          "profile": {
        "name": "cx2-2x4"
      },
      "primary_network_interface": {
        "name": "region3130example-net1",
        "subnet": {
          "id": ""
        }
      },
    "image": {
       "id": "r134-6f153a5d-6a9a-496d-8063-5c39932f6ded"
      },   
     "volume_attachments": [{
        "volume": {
          "source_snapshot": {
              "id": "r130-2ab1d4e1-8bf7-4431-b4ac-6dbe2e72a810"
            }
        }
      }],
    "resource_group": {
        "id": "a342dbfb-3ea7-48d1-96e8-2825ec5feab4"
    }
}'
```
{:codeblock}

## Next Steps
{: #snapshots_vpc_restore_next_steps}

[Create](/docs/vpc?topic=vpc-snapshots-vpc-create) more snapshots or [manage](/docs/vpc?topic=vpc-snapshots-vpc-manage) existing snapshots.
