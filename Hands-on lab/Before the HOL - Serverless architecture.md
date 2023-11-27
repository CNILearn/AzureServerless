![Microsoft Cloud Workshops](https://github.com/Microsoft/MCW-Template-Cloud-Workshop/raw/main/Media/ms-cloud-workshop.png "Microsoft Cloud Workshops")

# Serverless architecture - Before the hands-on lab setup guide

November 2023

Information in this document, including URL and other Internet Web site references, is subject to change without notice. Unless otherwise noted, the example companies, organizations, products, domain names, e-mail addresses, logos, people, places, and events depicted herein are fictitious, and no association with any real company, organization, product, domain name, e-mail address, logo, person, place or event is intended or should be inferred. Complying with all applicable copyright laws is the responsibility of the user. Without limiting the rights under copyright, no part of this document may be reproduced, stored in or introduced into a retrieval system, or transmitted in any form or by any means (electronic, mechanical, photocopying, recording, or otherwise), or for any purpose, without the express written permission of Microsoft Corporation.

Microsoft may have patents, patent applications, trademarks, copyrights, or other intellectual property rights covering subject matter in this document. Except as expressly provided in any written license agreement from Microsoft, the furnishing of this document does not give you any license to these patents, trademarks, copyrights, or other intellectual property.

The names of manufacturers, products, or URLs are provided for informational purposes only and Microsoft makes no representations and warranties, either expressed, implied, or statutory, regarding these manufacturers or the use of the products with any Microsoft technologies. The inclusion of a manufacturer or product does not imply endorsement of Microsoft of the manufacturer or product. Links may be provided to third party sites. Such sites are not under the control of Microsoft and Microsoft is not responsible for the contents of any linked site or any link contained in a linked site, or any changes or updates to such sites. Microsoft is not responsible for webcasting or any other form of transmission received from any linked site. Microsoft is providing these links to you only as a convenience, and the inclusion of any link does not imply endorsement of Microsoft of the site or the products contained therein.

© 2021 Microsoft Corporation. All rights reserved.

Updates © 2023 CN innovation. All rights reserved.

**Contents**

- [Serverless architecture before the hands-on lab setup guide](#serverless-architecture-before-the-hands-on-lab-setup-guide)
  - [Requirements](#requirements)
  - [Before the hands-on lab](#before-the-hands-on-lab)
    - [Task 1: Create a resource group](#task-1-create-a-resource-group)
    - [Task 2: Run ARM template to provision lab resources](#task-2-run-arm-template-to-provision-lab-resources)
    - [Task 3: Add your IP address to the Cosmos DB firewall](#task-3-add-your-ip-address-to-the-cosmos-db-firewall)
    - [Task 4: Create a developer workstation](#task-4-create-a-developer-workstation)

# Serverless architecture before the hands-on lab setup guide

## Requirements

- Microsoft Azure subscription (non-Microsoft subscription)
- Office 365 account. If you do not have an Office 365 account, you can [sign up for an Office 365 trial](https://portal.office.com/Signup/MainSignup15.aspx?Dap=False&QuoteId=79a957e9-ad59-4d82-b787-a46955934171&ali=1).  
- GitHub account. You can [create a free account](https://github.com/join).

## Before the hands-on lab

**Duration**: 20 minutes

In this exercise, you set up your environment for use in the rest of the hands-on lab. You should follow all steps provided _before_ attending the hands-on lab.

> **Important**: Many Azure resources require globally unique names. Throughout these steps, the word "SUFFIX" appears as part of resource names. You should replace this with your Microsoft alias, initials, or another value to ensure uniquely named resources.

### Task 1: Create a resource group

1. In the [Azure portal](https://portal.azure.com), select **Resource groups** from the Azure services list.

   ![Resource groups is highlighted in the Azure services list.](media/azure-services-resource-groups.png "Azure services")

2. On the Resource groups blade, select **+Create**.

   ![+Add is highlighted in the toolbar on Resource groups blade.](media/resource-groups-add.png "Resource groups")

3. On the Create a resource group **Basics** tab, enter the following:

   - **Subscription**: Select the subscription you are using for this hands-on lab.
   - **Resource group**: Enter `hands-on-lab-SUFFIX` as the name of the new resource group.
   - **Region**: Select the region you are using for this hands-on lab.

   ![The values specified above are entered into the Create a resource group Basics tab.](media/create-resource-group.png "Create resource group")

4. Select **Review + Create**.

5. On the **Review + create** tab, ensure the Validation passed message is displayed and then select **Create**.

### Task 2: Run ARM template to provision lab resources

In this task, you run an Azure Resource Manager (ARM) template to create the hands-on lab's resources. In addition to creating resources, the ARM template also executes a PowerShell script on the `LabVM` to install software and configure the server. The resources created by the ARM template include:

- Azure Data Lake Storage Gen2 account
  - Blob and File services
  - Containers named `images` and `export`
- Azure Cosmos DB
  - Database named `LicensePlates`
  - Containers named `Processed` and `NeedsManualReview`
- Azure Function Apps
  - `TollBoothFunctions`
  - `TollBoothEvents`
- Application Insights
- Azure Computer Vision service
- Azure Event Grid Topic
- Azure Logic App
- Azure Key Vault plus secrets for:
  - `computerVisionApiKey`
  - `cosmosDBAuthorizationKey`
  - `dataLakeConnectionString`
  - `eventGridTopicKey`

> **Note**: You can review the steps to manually provision and configure the lab resources in the [Manual resource setup guide](Manual-resource-setup.md).

1. You are now ready to begin the ARM template deployment. To open a custom deployment screen in the Azure portal, select the Deploy to Azure button below:

   [![Deploy to azure](media/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fmicrosoft%2FMCW-Serverless-architecture%2Fmain%2FHands-on%20lab%2Flab-files%2Farm-template%2Fazure-deploy.json)

2. On the custom deployment screen, the first parameter you need to populate is the `ObjectId` associated with the account you used to log into the Azure portal. To retrieve this, select the **Cloud Shell** icon on the Azure portal toolbar to open an Azure Command Line Interface (CLI) terminal window at the bottom of your open browser window.

   ![The Cloud Shell icon is highlighted on the Azure portal toolbar.](media/azure-toolbar-cloud-shell.png "Azure toolbar")

3. In the PowerShell terminal window that opens in the Azure portal, enter the following command at the prompt:

   ```powershell
   az ad signed-in-user show --query id -o tsv
   ```

   ![At the cloud shell prompt, the az ad signed-in-user show command is entered and highlighted.](media/azure-cli-az-ad-signed-in-user-show.png "Azure CLI")

4. Execute the command and copy the output value.

   ![In the cloud shell, the output from the az ad signed-in-user show command is highlighted.](media/azure-cli-az-ad-signed-in-user-show-output.png "Azure CLI")

5. Now, on the custom deployment screen in the Azure portal, enter the following:

   - **Subscription**: Select the subscription you are using for this hands-on lab.
   - **Resource group**: Select the hands-on-lab-SUFFIX resource group from the dropdown list.
   - **Signed In User Object Id**: Paste the value you copied above from the cloud shell terminal command output.

   ![The Custom deployment blade is displayed, and the information above is entered on the Custom deployment blade.](media/azure-custom-deployment.png "Custom deployment blade")

6. Select **Review + create** to review the custom deployment.

   > **Note**: The ARM template will append a hyphen followed by a 13-digit string at the end of resource names. This suffix ensures globally unique names for resources. We will ignore that string when referring to resources throughout the lab.

7. On the Review + create blade, ensure the _Validation passed_ message is displayed and then select **Create** to begin the custom deployment.

   > **Note**: The deployment of the custom ARM template should finish in about 5 minutes.

   ![On the Review + create blade for the custom deployment, the Validation passed message is highlighted, and the Create button is highlighted.](media/azure-custom-deployment-review-create.png "Review + create custom deployment")

8. You can monitor the deployment's progress on the **Deployment** blade that opens when you start the ARM template deployment.

### Task 3: Add your IP address to the Cosmos DB firewall

1. In the [Azure portal](https://portal.azure.com), navigate to the **hands-on-lab-SUFFIX** resource group you created above.

   > You can get to the resource group by selecting **Resource groups** under **Azure services** on the Azure portal home page and then select the resource group from the list. If there are many resource groups in your Azure account, you can filter the list for **hands-on-lab** to reduce the resource groups listed.

2. On your resource group blade, select the **cosmosdb** Azure Cosmos DB account resource in the resource group's list of services available.

   ![The Azure Cosmos DB account resource is highlighted in the list of services in the resource group.](media/resource-group-cosmos-db-account.png "Resources")

3. Next, select **Firewall and virtual networks** in the left-hand navigation menu of the Cosmos DB blade.

4. Choose **Selected networks (2)**. Select **+ Add my current IP (3)** to add your IP address to the IP list under Firewall. Next, check the box next to **Accept connections from within public Azure datacenters (4)**. Checking this box enables Azure services, such as your Function Apps, to access your Azure Cosmos DB account.

    ![The checkbox is highlighted.](media/cosmos-db-firewall.png "Firewall and virtual networks")

5. Select **Save**.

### Task 4: Create a Visual Studio 2022 DevBox

If you don't have Visual Studio 2022 installed on your system, you can create an Azure DevBox with Visual Studio 2022 pre-installed. You might need an Azure subscription without a spending limit.

1. In the [Azure portal](https://portal.azure.com), create a new **Resource group** for the development machine.
2. Create a new Visual Studio 2022 Pro on Windows 11 Enterprise DevBox
