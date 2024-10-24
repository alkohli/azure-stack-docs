---
title: Known issues for Azure Stack HCI version 22H2
description: Read about the known issues for Azure Stack HCI version 22H2
author: alkohli
ms.topic: how-to
ms.date: 10/08/2022
ms.author: alkohli
ms.reviewer: alkohli
---

# Known issues for Azure Stack HCI version 22H2 

> Applies to: Azure Stack HCI, version 22H2 

This article identifies the critical open issues and the resolved issues for the Azure Stack HCI, version 22H2 release. 

The release notes are continuously updated, and as critical issues requiring a workaround are discovered, they're added. Before you deploy your Azure Stack HCI, carefully review the information contained in the release notes.

This article applies to the Azure Stack HCI, version 22H2 preview release, which maps to software version number **10.2208.0.29**. The Azure Stack HCI, version 21H2 existing software deployments can be updated to this release. This release also supports brand new software installation using a deployment tool. For more information, see [What's new in 22H2](whats-new.md).

Here are the known issues for Azure Stack HCI, version 22H2:

|#|Feature|Issue|Workaround|
|-|------|------|----------|
|1|Deployment |Failure in ECE – *Set-RoleDefinition: Can't find the element ‘NodeDefinition’ for the role NC*|Make sure that a DVD isn't inserted in the physical machine or mounted via the Baseboard Management Controller (BMC).|
|2|Deployment |Only the first host can be the staging server.|There's no workaround for this issue in this preview release.|
|3|Deployment |Some of the Learn more links aren't functional in the deployment tool.|These links will be functional in a future release.|
|4|Deployment |During deployment, an error is seen in Windows Admin Center: Remote Exception “GetCredential” with “1”.|Reboot the staging server and run the bootstrap script again. Make sure that the Azure credentials for the subscription haven't expired and are correct.|
|5|Deployment |Renaming the network adapter in the deployment tool and using the **Back** and **Next** buttons causes it to hang.|There's no workaround for this is in the preview release.|
|6|Deployment |Deployment UI does not show the actual progress even after the staging server has restarted. |Refresh the browser once. For the remainder of the deployment, the page will refresh automatically.|
|7|Arc VM and AKS-HCI workload deployment |In this release, Windows Defender App Control (WDAC) is enabled by default on Azure Stack HCI servers. The following post-deployment scenarios are affected: <br>- Windows Admin Center in Azure portal won't work because WDAC is enabled by default.  <br>- If you are deploying Arc VM or AKS-HCI workloads, you would see an error while importing Arc Resource Bridge PowerShell module. |The workaround is to switch WDAC policy mode to `Audit` instead of '`Enforce`. For more information, see [Switch WDAC policy mode](./concepts/security-windows-defender-application-control.md). <br> After the workload deployment is complete, you can run the cmdlet to switch WDAC policy mode back to the default enforced mode.|
|8|Deployment |If a cluster node does not show as connected in the Azure portal, this can be owing to the Arc for server registration failing to complete.  | Disable WDAC on your system to allow the automatic retry logic to complete the Arc for server registration. For more information, see [Switch WDAC policy mode](./concepts/security-windows-defender-application-control.md#switching-between-wdac-policy-modes).|
|9|Deployment |In this release, regardless of which region you select, the cluster is always deployed in East US.||
|10|Deployment |If you have an Azure policy assigned to the Azure subscription that enforces tags, the deployment fails.||