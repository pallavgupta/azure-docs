---
title: Delete a role/policy in the Remediation dashboard in CloudKnox Permissions Management 
description: How to delete a role/policy in the Just Enough Permissions (JEP) Controller.
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

# Delete a role/policy in the Remediation dashboard

> [!IMPORTANT]
> CloudKnox Permissions Management (CloudKnox) is currently in PREVIEW.
> Some information relates to a prerelease product that may be substantially modified before it's released. Microsoft makes no warranties, express or implied, with respect to the information provided here.

This article describes how you can use the **Remediation** dashboard in CloudKnox Permissions Management (CloudKnox) to delete roles/policies for the Amazon Web Services (AWS), Microsoft Azure, or Google Cloud Platform (GCP) authorization systems. 

> [!NOTE]
> To view the **Remediation** dashboard, you must have **Viewer**, **Controller**, or **Administrator** permissions. To make changes on this tab, you must have **Controller** or **Administrator** permissions. If you don’t have these permissions, contact your system administrator.

> [!NOTE]
> Microsoft Azure uses the term *role* for what other Cloud providers call *policy*. CloudKnox automatically makes this terminology change when you select the authorization system type. In the user documentation, we use *role/policy* to refer to both.

## Delete a role/policy

1. On the CloudKnox home page, select the **Remediation** tab, and then select the **Role/Policies** subtab.
1. Select the role/policy you want to delete, and from the **Actions** column, select **Delete**.

    You can only delete a role/policy if it isn't assigned to an identity. 

    You can't delete system roles/policies.

1. On the **Preview** page, review the role/policy information to make sure you want to delete it, and then select **Submit**.

## Next steps


- For information on how to view existing roles/policies, requests, and permissions, see [View roles/policies, requests, and permission in the Remediation dashboard](cloudknox-ui-remediation.md).
- For information on how to create a role/policy, see [Create a role/policy](cloudknox-howto-create-role-policy.md).
- For information on how to clone a role/policy, see [Clone a role/policy](cloudknox-howto-clone-role-policy.md).
- For information on how to modify a role/policy, see Modify a role/policy](cloudknox-howto-modify-role-policy.md).
- To view information about roles/policies, see [View information about roles/policies](cloudknox-howto-view-role-policy.md).
- For information on how to attach and detach permissions for AWS identities, see [Attach and detach policies for AWS identities](cloudknox-howto-attach-detach-permissions.md).
- For information on how to revoke high-risk and unused tasks or assign read-only status for Azure and GCP identities, see [Revoke high-risk and unused tasks or assign read-only status for Azure and GCP identities](cloudknox-howto-revoke-task-readonly-status.md)
- For information on how to create or approve a request for permissions, see [Create or approve a request for permissions](cloudknox-howto-create-approve-privilege-request.md).
- For information on how to view information about roles/policies, see [View information about roles/policies](cloudknox-howto-view-role-policy.md)
