---
title: Assign regulatory compliance standards in Microsoft Defender for Cloud
description: Learn how to assign regulatory compliance standards in Microsoft Defender for Cloud.
ms.topic: how-to
ms.date: 10/10/2023
ms.custom: ignite-2023
---

# Assign security standards


Regulatory standards and benchmarks are represented in Microsoft Defender for Cloud as [security standards](security-policy-concept.md). Each standard is an initiative defined in Azure Policy.


In Defender for Cloud you assign security standards to  specific scopes such as Azure subscriptions, AWS accounts, and GCP projects that have Defender for Cloud enabled.

Defender for Cloud continually assesses the environment-in-scope against standards. Based on assessments, it shows in-scope resources as being compliant or non-compliant with the standard, and provides remediation recommendations.

This article describes how to add regulatory compliance standards as security standards in an Azure subscriptions, AWS account, or GCP project.

## Before you start

- To add compliance standards, at least one Defender for Cloud plan must be enabled.
- You need Owner or Policy Contributor permissions to add a standard.


## Assign a standard (Azure)

1. In the Defender for Cloud portal, select **Regulatory compliance**. For each standard, you can see the subscription in which it's applied.

1. From the top of the page, select **Manage compliance policies**.

1. Select the subscription or management group on which you want to assign the security standard.

We recommend selecting the highest scope for which the standard is applicable so that compliance data is aggregated and tracked for all nested resources.

1. Select **Security policies**.

1. For the standard you want to enable, in the **Status** column, switch the toggle button to **On**.

1. If any information is needed in order to enable the standard, the **Set parameters** page appears for you to type in the information.


    :::image type="content" source="media/update-regulatory-compliance-packages/turn-standard-on.png" alt-text="Screenshot showing regulatory compliance dashboard options."  lightbox="media/update-regulatory-compliance-packages/turn-standard-on.png":::

1. From the menu at the top of the page, select **Regulatory compliance** again to go back to the regulatory compliance dashboard.


The selected standard appears in **Regulatory compliance** dashboard as enabled for the subscription.


## Assign a standard (AWS)

To assign regulatory compliance standards on AWS accounts:

1. Navigate to **Environment settings**.
1. Select the relevant AWS account.
1. Select **Security policies**.
1. In the **Standards** tab, select the three dots in the standard you want to assign > **Assign standard**.

    :::image type="content" source="media/update-regulatory-compliance-packages/assign-standard-aws-from-list.png" alt-text="Screenshot that shows where to select a standard to assign." lightbox="media/update-regulatory-compliance-packages/assign-standard-aws-from-list.png":::

1. At the prompt, select **Yes**. The standard is assigned to your AWS account.

    :::image type="content" source="media/update-regulatory-compliance-packages/assign-standard-aws.png" alt-text="Screenshot of the prompt to assign a regulatory compliance standard to the AWS account." lightbox="media/update-regulatory-compliance-packages/assign-standard-aws.png":::

The selected standard appears in **Regulatory compliance** dashboard as enabled for the account.

## Assign a standard (GCP)

To assign regulatory compliance standards on GCP projects:

1. Navigate to **Environment settings**.
1. Select the relevant GCP project.
1. Select **Security policies**.
1. In the **Standards** tab, select the three dots alongside an unassigned standard and select **Assign standard**.
1. At the prompt, select **Yes**. The standard is assigned to your GCP project.

The selected standard appears in the **Regulatory compliance** dashboard as enabled for the project.

## Next steps

- Create custom standards for [Azure](custom-security-policies.md), [AWS, and GCP](create-custom-recommendations.md).
- [Improve regulatory compliance](regulatory-compliance-dashboard.md)
