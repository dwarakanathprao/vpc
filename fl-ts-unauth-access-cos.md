---

copyright:
  years: 2020, 2021
lastupdated: "2021-02-11"

keywords: is.flow-log-collector.00002E

subcollection: vpc

---

{:tsSymptoms: .tsSymptoms}
{:tsCauses: .tsCauses}
{:tsResolve: .tsResolve}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:support: data-reuse='support'}
{:codeblock: .codeblock}
{:pre: .pre}
{:note:.note}
{:important: .important}
{:troubleshoot: data-hd-content-type='troubleshoot'}

# Why isn't the flow log collector authorized to publish data to the COS bucket?
{: #fl-ts-error-unauth-access-cos}
{: troubleshoot}
{: support}

A flow log collector requires a {{site.data.keyword.cos_full_notm}} (COS) bucket to be defined and accessible. If you see the error log with message ID `is.flow-log-collector.00002E`, the COS bucket is not accessible. The flow log collector cannot publish data to the bucket. 
{:shortdesc}

Take action within the next 24 hours to correct this problem, or your data will be lost. 
{: important}

The flow log collector is not authorized to publish data to the COS bucket: 
   `is.flow-log-collector.00002E: Unauthorized access to Cloud Object Storage bucket <BucketName>`
{: tsSymptoms}

The COS bucket does not have the correct access to allow the flow log collector to publish data.
{: tsCauses}

Check that there is an authorization defined between the flow log collector and the COS bucket. If not, add one so that the flow log collector can access the bucket.
{: tsResolve}

To define an authorization, follow these steps:

1. In the {{site.data.keyword.cloud_notm}} console, click **Manage** &gt; **Access (IAM)**.
1. Select **Authorizations** from the navigation pane.
1. Click **Create**.
1. For **Source service**:
   * Select **VPC Infrastructure Services**. Then, select **Services based on attributes**.
   * Select **Resource type**. Then, select **Flow Logs for VPC**.
   * Select **Source resource instance** and choose an option.
1. For **Target service**:
   * Select **Cloud Object Storage**. Then, select **Services based on attributes**.
   * Select **Service instance** and choose an option.
1. For **Service access**, select the **Writer** role.
1. Click **Authorize**.

For more information, see [Creating a flow log collector](/docs/vpc?topic=vpc-ordering-flow-log-collector).
{: note}
