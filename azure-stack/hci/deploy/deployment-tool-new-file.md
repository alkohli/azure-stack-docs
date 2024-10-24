---
title: Deploy Azure Stack HCI version 22H2 (preview) interactively
description: Learn how to deploy Azure Stack HCI version 22H2 (preview) interactively using a new configuration file
author: dansisson
ms.topic: how-to
ms.date: 09/22/2022
ms.author: v-dansisson
ms.reviewer: alkohli
---

# Deploy Azure Stack HCI version 22H2 interactively (preview) 

> Applies to: Azure Stack HCI, version 22H2 

After you've successfully installed the operating system, you're ready to set up and run the deployment tool. This method of deployment leads you through a guided, UI experience to create a configuration (answer) file interactively that is saved.

You can deploy both single-node and multi-node clusters using this procedure.

> [!IMPORTANT]
 > Please review the [Terms of Use](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) and agree to the terms before you deploy this solution.

## Prerequisites

Before you begin, make sure you've done the following:

- Satisfy the [prerequisites](deployment-tool-prerequisites.md).
- Complete the [deployment checklist](deployment-tool-checklist.md).
- Prepare your [Active Directory](deployment-tool-active-directory.md) environment.
- [Install version 22H2 OS](deployment-tool-install-os.md) on each server.


## Set up the deployment tool

> [!NOTE]
> You need to install and set up the deployment tool only on the first server in the cluster.


1. In Windows Admin Center, select the first server listed for the cluster to act as a staging server during deployment.

1. Sign in to the staging server using local administrative credentials.

1. Copy content from the *Cloud* folder you downloaded previously to any drive other than the C:\ drive.

1. Run PowerShell as administrator.

1. Run the following command to install the deployment tool:

   ```PowerShell
    .\BootstrapCloudDeploymentTool.ps1 
    ```

    This step takes several minutes to complete.


## Run the deployment tool

This procedure collects deployment parameters from you interactively and saves them to a configuration file as you step through the deployment UI. You deploy single-node and multi-node clusters similarly.

If you want to use an existing configuration file you have previously created, see [Deploy using a configuration file](deployment-tool-existing-file.md).

1. Open a web browser from a computer that has network connectivity to the staging server.

1. In the URL field, enter *https://your_staging-server-IP-address*.

1. Accept the security warning displayed by your browser - this is shown because we’re using a self-signed certificate.

1. Authenticate using the local administrator credentials of your staging server.

1. In the deployment UI, on the **Get started deploying Azure Stack** page, select **Create a new config file**, then select either **One server** or **Multiple servers** as applicable for your deployment.

      :::image type="content" source="media/deployment-tool/new-file/deploy-new-get-started.png" alt-text="Screenshot of the Deployment Get Started page." lightbox="media/deployment-tool/new-file/deploy-new-get-started.png":::

1. On step 1.1 **Provide registration details**, enter the following details to authenticate your cluster with Azure:

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-1-registration-details.png" alt-text="Screenshot of the Deployment step 1.1 Provide registration details page." lightbox="media/deployment-tool/new-file/deploy-new-step-1-registration-details.png":::

    1. Select the **Azure Cloud** to be used. In this release, only Azure public cloud is supported.
    
    1. Copy the authentication code.
    
    1. Select **login**. A new browser window opens. Enter the code that you copied earlier and then provide your Azure credentials. Multi-factor authentication (MFA) is supported. 

    1. Go back to the deployment screen and provide the Azure registration details.

    1. From the dropdown, select the **Azure Active Directory ID** or the tenant ID.

    1. Select the associated subscription. This subscription is used to create the cluster resource, register it with Azure Arc and set up billing.

        > [!NOTE]
        > Make sure that you are a [user access administrator](/azure/role-based-access-control/built-in-roles#user-access-administrator) on this subscription. This will allow you to manage access to Azure resources, specifically to Arc-enable each server of an Azure Stack HCI cluster.

    1. Select an existing **Azure resource group** from the dropdown to associate with the cluster resource. To create a new resource group, leave the field empty.

    1. Select an **Azure region** from the dropdown or leave the field empty to use the default.


1. On step **1.2 Configure privacy**, set the privacy settings as they apply to your organization.

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-1-privacy.png" alt-text="Screenshot of the Deployment step 1.1 Configure privacy page." lightbox="media/deployment-tool/new-file/deploy-new-step-1-privacy.png":::

1. On **step 1.3 Add servers**, follow these steps:

    1. Provide the local administrator credentials. Make sure that the local administrator credentials are identical across all the servers.

    1. Enter the IP address of each server. Add the servers in the right sequence, beginning with the first server.

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-1-add-servers.png" alt-text="Screenshot of the Deployment step 1.2 Add servers page." lightbox="media/deployment-tool/new-file/deploy-new-step-1-add-servers.png":::

1. On Step **1.4 Set cluster security**, select **Recommended security settings** to use the recommended default settings:

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-1-security-default.png" alt-text="Screenshot of the Deployment step 1.4 Set cluster security default page." lightbox="media/deployment-tool/new-file/deploy-new-step-1-security-default.png":::

    or select **Customized security settings:**

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-1-security-custom.png" alt-text="Screenshot of the Deployment step 1.4 Set cluster security custom page." lightbox="media/deployment-tool/new-file/deploy-new-step-1-security-custom.png":::

    For more information on the individual security settings, see:
    - [App control (WDAC) enforced on host](/windows/security/threat-protection/windows-defender-application-control/wdac-and-applocker-overview#windows-defender-application-control).
    - [BitLocker for OS boot volume and BitLocker for data volumes](/windows/security/information-protection/bitlocker/bitlocker-overview).
    - [SMB Signing Default instance](/troubleshoot/windows-server/networking/overview-server-message-block-signing).
    - [SMB Encryption E-W Cluster traffic](/windows-server/storage/file-server/smb-security#smb-encryption).

1. On **step 1.5 Specify Active Directory details**, enter the following:
    1. Provide your **Active Directory domain** name. For example, this would be in `Contoso.com` format.
    1. Enter the **Computer name prefix** used when you prepared the Active Directory.
    1. For **OU**, provide the full name of the organizational unit (including the domain controllers) that was created for the deployment. For example, the name would be `"OU=Hci001,DC=contoso,DC=com"`.
    1. Provide the **username** and the **password** for the deployment user account that was created during [Prepare the Active Directory](deployment-tool-active-directory.md) step.

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-1-join-domain.png" alt-text="Screenshot of the Deployment step 1.3 Join a domain page." lightbox="media/deployment-tool/new-file/deploy-new-step-1-join-domain.png":::


1. On step **2 Networking**, consult with your network administrator to ensure you enter the correct network details.

1. On step **2.1 Check network adapters**, consult with your network administrator to ensure you enter the correct network details. 

    If all the network adapters do not show up and if you have not excluded those, select **Show hidden adapters**. You may also need to check the cabling and the link speeds. While the network interfaces can have identical speeds across the nodes of the cluster, any low speed switch connections could lead to  difference in the overall speed. 

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-2-network-adapters.png" alt-text="Screenshot of the Deployment step 2.1 network adapters page." lightbox="media/deployment-tool/new-file/deploy-new-step-2-network-adapters.png":::

1. On step **2.2 Define network intents**, consult with your network administrator to ensure you enter the correct network details.

    When defining the network intents, for this preview release, only the following two sets of network intents are supported.

    - one *Management + Compute* intent, one storage intent.
    - one fully converged intent that maps to *Management + Compute + Storage* intent.
    
    The networking intent should match how you've cabled your system. For this release, see the [Validated deployment network patterns](./deployment-tool-introduction.md#validated-configurations)

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-2-network-intents.png" alt-text="Screenshot of the Deployment step 2.2 network intents page." lightbox="media/deployment-tool/new-file/deploy-new-step-2-network-intents.png":::

1. On step **2.3 Provide storage network details**, consult with your network administrator to ensure you enter the correct network details.

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-2-network-storage.png" alt-text="Screenshot of the Deployment step 2.3 storage network page." lightbox="media/deployment-tool/new-file/deploy-new-step-2-network-storage.png":::

1. On step **2.4 Provide management network details**, enter the following input after consulting your network administrator:

    1. In this release, only the static IPs can be assigned and DHCP isn't supported.
    1. For the **Starting IP** and the **Ending IP** range, provide a minimum of 6 free, contiguous IPv4 addresses. This range excludes your host IPs. These IPs are used for infrastructure services such as clustering.
    1. Provide the **Subnet mask** and **Default gateway** for the network.
    1. Provide the IPv4 address of the **DNS servers** in your environment. DNS servers are required because they're used when your server attempts to communicate with Azure or to resolve your server by name in your network. The DNS server you configure must be able to resolve the Active Directory domain.
        
        For high availability, we recommend that you configure a preferred and an alternate DNS server.
    1. Use the **Advanced SDN settings** when Software defined networking (SDN) is enabled.

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-2-network-management.png" alt-text="Screenshot of the Deployment step 2.4 management network page." lightbox="media/deployment-tool/new-file/deploy-new-step-2-network-management.png":::


1. On step **3.1 Create cluster**, enter the cluster name. This must be the cluster name you used when preparing Active Directory.

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-3-create-cluster.png" alt-text="Screenshot of the Deployment step 3.1 create cluster page." lightbox="media/deployment-tool/new-file/deploy-new-step-3-create-cluster.png":::

1. On step **4.1 Set up cluster storage**, select **Set up with empty drives**.

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-4-storage.png" alt-text="Screenshot of the Deployment step 4.1 cluster storage page." lightbox="media/deployment-tool/new-file/deploy-new-step-4-storage.png":::

1. On step **5.1 Add services**, no changes are needed. Optional services are slated for upcoming releases. VM services are enabled by default. Select **Next** to continue.

    :::image type="content" source="media/deployment-tool/new-file/deployment-step-5-add-services.png" alt-text="Screenshot of the Deployment step 5.1 Add services page." lightbox="media/deployment-tool/new-file/deployment-step-5-add-services.png":::


1. On step **5.2 Set Arc management details**, accept the default region for deployment and virtual switch used by Azure Arc. These Azure Arc resources are used to create and manage resources on your cluster via the Azure portal. Select **Next** to continue.

    :::image type="content" source="media/deployment-tool/new-file/deploy-new-step-5-arc.png" alt-text="Screenshot of the Deployment step 5.2 Arc management page." lightbox="media/deployment-tool/new-file/deploy-new-step-5-arc.png":::

1. On step **6.1 Deploy the cluster**, select **Download the config file for your deployment**, and then select **Deploy to start the deployment**.

    > [!IMPORTANT]
    > The staging server restarts after the deployment starts.

    :::image type="content" source="media/deployment-tool/new-file/deployment-step-6-deploy-cluster.png" alt-text="Screenshot of the Deployment step 6.1 deploy cluster page." lightbox="media/deployment-tool/new-file/deployment-step-6-deploy-cluster.png":::

1. It can take up to 3 hours for deployment to complete. You can monitor your deployment progress in near real time. 

    :::image type="content" source="media/deployment-tool/new-file/deployment-progress.png" alt-text="Screenshot of the Monitor deployment page." lightbox="media/deployment-tool/new-file/deployment-progress.png":::

    > [!NOTE]
    > When you start the deployment, the page may not show actual progress even after the staging server has restarted. Refresh the page once using the browser refresh and then the page will automatically refresh for the remainder of the deployment.

## Next steps

- [Validate deployment](deployment-tool-validate.md).
- If needed, [troubleshoot deployment](deployment-tool-troubleshoot.md).
