---

copyright:
  years: 2020
lastupdated: "2020-10-30"

keywords: anti-spoofing, source destination check, ip spoofing

subcollection: vpc

---


{:shortdesc: .shortdesc}
{:new_window: target="_blank"}
{:codeblock: .codeblock}
{:pre: .pre}
{:screen: .screen}
{:term: .term}
{:tip: .tip}
{:note: .note}
{:important: .important}
{:deprecated: .deprecated}
{:external: target="_blank" .external}
{:table: .aria-labeledby="caption"}
{:generic: data-hd-programlang="generic"}
{:download: .download}
{:DomainName: data-hd-keyref="DomainName"}


# About IP spoofing checks
{: #ip-spoofing-about}

{{site.data.keyword.cloud}} Virtual Private Cloud (VPC) includes a source/destination check on each network interface of a virtual service instance to ensure that traffic coming from that network interface includes appropriate addressing.
{:shortdesc}

Disabling source/destination checks allows traffic to pass through the network interface, instead of terminating at the network interface. If you are using the instance as a "next hop", the instance's network interfaces must allow IP spoofing. For example, if you are using a custom load balancer instance, you must set `allow_ip_spoofing` for traffic to reach the instance.

Traffic can be dropped at two points in the check: 
- Incoming traffic is checked to make sure it is addressed to the selected network interface. Traffic is dropped if its destination address does not match the selected network interface address.

- Outgoing traffic is checked to verify that the content comes from the selected network interface address. Traffic from the selected network network interface is dropped if its source address does not match the selected network interface address.

![Figure showing unsuccessful traffic flow to and from a virtual server instance](images/as-deny.png "Figure showing unsuccessful traffic flow to and from a virtual server instance"){: caption="Figure 1. Unsuccessful source/destination check" caption-side="top"}

![Figure showing successful traffic flow to and from a virtual server instance](images/as-allow.png "Figure showing successful traffic flow to and from a virtual server instance"){: caption="Figure 2. Successful source/destination check" caption-side="top"}

Only operators with **IP Spoofing Operator** Identity and Access Management (IAM) privileges can enable or disable the source/destination check on the interfaces within a VPC. Ingress and egress source/destination checks are enabled by default.

## Enabling source/destination checks
{: #ip-spoofing-enable-check}

After a virtual server instance is created, a network administrator with the **IP Spoofing Operator** role in IAM can update the network interface to enable or disable the source/destination check. 

For information about IAM permissions, see [Required permissions](/docs/vpc?topic=vpc-resource-authorizations-required-for-api-and-cli-calls).

## Understanding the risks
{: #ip-spoofing-risks}

When you disable the source/destination check on your network interface, consider the potential security risks that are involved. Anyone with the **IP Spoofing Operator** role not only has permission to enable virtual network appliances, but they also can configure an instance to send traffic on behalf of another instance. This increases the chance of situations where the platform might be attacked due to the action of an uneducated or malicious user. 

Be cautious when assigning the **IP Spoofing Operator** role to users.
{:important}

## Alerting for IP spoofing events
{:#ip-spoofing-alerts}

Any time IP spoofing is modified on a network interface, an Activity Tracker log is generated.

For more information, see the [Getting started tutorial](/docs/Activity-Tracker-with-LogDNA?topic=Activity-Tracker-with-LogDNA-getting-started) for Activity Tracker with LogDNA.
For information about setting up alerts, see [Managing alerts](/docs/Activity-Tracker-with-LogDNA?topic=Activity-Tracker-with-LogDNA-alerts).