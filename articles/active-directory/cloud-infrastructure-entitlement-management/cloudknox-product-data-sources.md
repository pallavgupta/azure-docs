---
title: View and configure settings for data collection from your authorization system in CloudKnox Permissions Management
description: How to view and configure settings for collecting data from your authorization system in CloudKnox Permissions Management.
services: active-directory
author: mtillman
manager: karenh444
ms.service: active-directory
ms.subservice: ciem
ms.workload: identity
ms.topic: how-to
ms.date: 02/23/2022
ms.author: mtillman
---

# View and configure settings for data collection 

> [!IMPORTANT]
> CloudKnox Permissions Management (CloudKnox) is currently in PREVIEW.
> Some information relates to a prerelease product that may be substantially modified before it's released. Microsoft makes no warranties, express or implied, with respect to the information provided here.


You can use the **Data Collectors** dashboard in CloudKnox Permissions Management (CloudKnox) to view and configure settings for collecting data from your authorization systems. It also provides information about the status of the data collection.

## Access and view data sources

1. To access your data sources, in the CloudKnox home page, select **Settings** (the gear icon). Then select the **Data Collectors** tab.

1. On the **Data Collectors** dashboard, select your authorization system type: 

    - **AWS** for Amazon Web Services.
    - **Azure** for Microsoft Azure.
    - **GCP** for Google Cloud Platform.

1. To display specific information about an account:

    1. Enter the following information:

        - **Uploaded on**: Select **All** accounts, **Online** accounts, or **Offline** accounts.
        - **Transformed on**: Select **All** accounts, **Online** accounts, or **Offline** accounts.
        - **Search**: Enter an ID or Internet Protocol (IP) address to find a specific account.

    1. Select **Apply** to display the results.

        Select **Reset Filter** to discard your settings.

1. The following information displays:

    - **ID**: The unique identification number for the data collector.
    - **Data types**: Displays the data types that are collected:
         - **Entitlements**: The permissions of all identities and resources for all the configured authorization systems.
    - **Recently uploaded on**: Displays whether the entitlement data is being collected. 

        The status displays *ONLINE* if the data collection has no errors and *OFFLINE* if there are errors.
    - **Recently transformed on**: Displays whether the entitlement data is being processed.

        The status displays *ONLINE* if the data processing has no errors and *OFFLINE* if there are errors. 
    - The **Tenant ID**.
    - The **Tenant name**.

## Modify a data collector   

1. Select the ellipses **(...)** at the end of the row in the table.
1. Select **Edit Configuration**. 

    The **CloudKnox Onboarding - Summary** box displays.

1. Select **Edit** (the pencil icon) for each field you want to change. 
1. Select **Verify now & save**.

    To verify your changes later, select **Save & verify later**.

    When your changes are saved, the following message displays: **Successfully updated configuration.**
    
## Delete a data collector   

1. Select the ellipses **(...)** at the end of the row in the table.
1. Select **Delete Configuration**. 

    The **CloudKnox Onboarding - Summary** box displays.
1. Select **Delete**.
1. Check your email for a one time password (OTP) code, and enter it in **Enter OTP**. 

    If you don't receive an OTP, select **Resend OTP**.

    The following message displays: **Successfully deleted configuration.**

## Start collecting data from an authorization system   

1. Select the **Authorization Systems** tab, and then select your authorization system type.
1. Select the ellipses **(...)** at the end of the row in the table.
1. Select **Collect Data**.

    A message displays to confirm data collection has started. 

## Stop collecting data from an authorization system   

1. Select the ellipses **(...)** at the end of the row in the table.
1. To delete your authorization system, select **Delete**. 

    The **Validate OTP To Delete Authorization System** box displays.

1. Enter the OTP code
1. Select **Verify**.

## Next steps

- For information about viewing an inventory of created resources and licensing information for your authorization system, see [Display an inventory of  created resources and licenses for your authorization system](cloudknox-product-data-inventory.md)
