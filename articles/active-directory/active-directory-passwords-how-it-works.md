---
title: 'How it works? Azure AD SSPR | Microsoft Docs'
description: Azure AD self-service password reset deep dive
services: active-directory
keywords: 
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: femila
ms.reviewer: sahenry

ms.assetid: 618c5908-5bf6-4f0d-bf88-5168dfb28a88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2017
ms.author: joflore
ms.custom: it-pro

---
# Self-service password reset in Azure AD deep dive

How does SSPR work? What does that option mean in the interface? Continue reading to find out more about Azure AD self-service password reset.

## How does the password reset portal work

When a user navigates to the password reset portal, a workflow is kicked off to determine:

   * How should the page be localized?
   * Is the user account valid?
   * What organization does the user belong to?
   * Where the user’s password is managed?
   * Is the user licensed to use the feature?


Read through the following steps to learn about the logic behind the password reset page.

1. User clicks the Can’t access your account link or goes directly to [https://aka.ms/sspr](https://passwordreset.microsoftonline.com).
2. Based on the browser locale the experience is rendered in the appropriate language. The password reset experience is localized into the same languages as Office 365 supports.
3. User enters a user ID and passes a captcha.
4. Azure AD verifies if the user is able to use this feature by doing the following checks:
   * Checks that the user has this feature enabled and an Azure AD license assigned.
     * If the user does not have this feature enabled or a license assigned, the user is asked to contact their administrator to reset their password.
   * Checks that the user has the right challenge data defined on their account in accordance with administrator policy.
     * If policy requires only one challenge, then it is ensured that the user has the appropriate data defined for at least one of the challenges enabled by the administrator policy.
       * If the user is not configured, then the user is advised to contact their administrator to reset their password.
     * If the policy requires two challenges, then it is ensured that the user has the appropriate data defined for at least two of the challenges enabled by the administrator policy.
       * If the user is not configured, then we the user is advised to contact their administrator to reset their password.
   * Checks if the user’s password is managed on premises (federated or password hash synchronized).
     * If writeback is deployed and the user’s password is managed on premises, then the user is allowed to proceed to authenticate and reset their password.
     * If writeback is not deployed and the user’s password is managed on premises, then the user is asked to contact their administrator to reset their password.
5. If it is determined that the user is able to successfully reset their password, then the user is guided through the reset process.

## Authentication methods

If Self-Service Password Reset (SSPR) is enabled, you must select at least one of the following options for authentication methods. Sometimes you may hear these options referred to as gates. We highly recommend choosing at least two authentication methods so that your users have more flexibility.

* Email
* Mobile Phone
* Office Phone
* Security Questions

![Authentication][Authentication]

### What fields are used in the directory for authentication data

* Office Phone corresponds to Office Phone
    * Users are unable to set this field themselves it must be defined by an administrator
* Mobile Phone corresponds to either Authentication Phone (not publicly visible) or Mobile Phone (publicly visible)
    * The service looks for Authentication Phone first, then falls back to Mobile Phone if not present
* Alternate Email Address corresponds to either Authentication Email (not publicly visible) or Alternate Email
    * The service looks for Authentication Email first, then fails back to Alternate Email

By default, only the cloud attributes Office Phone and Mobile Phone are synchronized to your cloud directory from your on-premises directory for authentication data.

Users can only reset their password if they have data present in the authentication methods that the administrator has enabled and requires.

If users do not want their mobile phone number to be visible in the directory but would still like to use it for password reset, administrators should not populate it in the directory and the user should then populate their **Authentication Phone** attribute via the [password reset registration portal](http://aka.ms/ssprsetup). Administrators can see this information in the user's profile but it is not published elsewhere.

### Number of authentication methods required

This option determines the minimum number of the available authentication methods or gates a user must go through to reset or unlock their password and can be set to either 1 or 2.

Users can choose to supply more authentication methods if they are enabled by the administrator.

If a user does not have the minimum required methods registered, they see an error page that directs them to request an administrator to reset their password.

#### Changing authentication methods

If you start with a policy that has only one authentication method required for reset or unlock registered and you change that to two what happens?

| Number of methods registered | Number of methods required | Result |
| :---: | :---: | :---: |
| 1 or more | 1 | **Able** to reset or unlock |
| 1 | 2 | **Unable** to reset or unlock |
| 2 or more | 2 | **Able** to reset or unlock |

If you change the types of authentication methods that a user can use you may inadvertently stop users from being able to use SSPR if they do not have the minimum amount of data available.

Example: 
1. Original policy configured with 2 authentication methods required using only office phone and security questions. 
2. Administrator changes the policy to no longer use security questions but allow the use of mobile phone and alternate email.
3. Users without mobile phone and alternate email fields populated can't reset their passwords.

### How secure are my security questions

If you use security questions, we recommend them in use with another method as they can be less secure than other methods since some people may know the answers to another user's questions.

> [!NOTE] 
> Security questions are stored privately and securely on a user object in the directory and can only be answered by users during registration. There is no way for an administrator to read or modify a user's questions or answers.
>

### Security question localization

All predefined questions that follow are localized into the full set of Office 365 languages based on the user's browser locale.

* In what city did you meet your first spouse/partner?
* In what city did your parents meet?
* In what city does your nearest sibling live?
* In what city was your father born?
* In what city was your first job?
* In what city was your mother born?
* What city were you in on New Year's 2000?
* What is the last name of your favorite teacher in high * school?
* What is the name of a college you applied to but didn't attend?
* What is the name of the place in which you held your first wedding reception?
* What is your father's middle name?
* What is your favorite food?
* What is your maternal grandmother's first and last name?
* What is your mother's middle name?
* What is your oldest sibling's birthday month and year? (e.g. November 1985)
* What is your oldest sibling's middle name?
* What is your paternal grandfather's first and last name?
* What is your youngest sibling's middle name?
* What school did you attend for sixth grade?
* What was the first and last name of your childhood best friend?
* What was the first and last name of your first significant other?
* What was the last name of your favorite grade school teacher?
* What was the make and model of your first car or motorcycle?
* What was the name of the first school you attended?
* What was the name of the hospital in which you were born?
* What was the name of the street of your first childhood home?
* What was the name of your childhood hero?
* What was the name of your favorite stuffed animal?
* What was the name of your first pet?
* What was your childhood nickname?
* What was your favorite sport in high school?
* What was your first job?
* What were the last four digits of your childhood telephone number?
* When you were young, what did you want to be when you grew up?
* Who is the most famous person you have ever met?

### Custom security questions

Custom security questions are not localized for different locales. All custom questions are displayed in the same language they are entered in the administrative user interface even if the user's browser locale is different. If you need localized questions, please use the predefined questions.

The maximum length of a custom security question is 200 characters.

### Security question requirements

* Minimum answer character limit is 3 characters
* Maximum answer character limit is 40 characters
* Users may not answer the same question more than one time
* Users may not provide the same answer to more than one question
* Any character set may be used to define questions and answers including Unicode characters
* The number of questions defined must be greater than or equal to the number of questions required to register

## Registration

### Require users to register when signing in

Enabling this option requires a user who is enabled for password reset to complete the password reset registration if they login to applications using Azure AD to sign in like those that follow:

* Office 365
* Azure portal
* Access Panel
* Federated applications
* Custom applications using Azure AD

When this is disabled users can still manually register their contact information by visiting [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup) or by clicking the **register for password reset** link under the profile tab in the access panel.

> [!NOTE]
> Users can dismiss the password reset registration portal by clicking cancel or closing the window but are prompted each time they login until they complete registration.
>
> This will not break the user's connection if they are alreay signed in.

### Number of days before users are asked to reconfirm their authentication information

This option determines the period of time between setting and reconfirming authentication information and is only available if you enable the **require users to register when signing in** option.

Valid values are 0-730 days with 0 meaning never ask users to reconfirm their authentication information

## Notifications

### Notify users on password resets

If this option is set to yes, then the user who is resetting their password receives an email notifying them that their password has been changed via the SSPR portal to their primary and alternate email addresses on file in Azure AD. No one else is notified of this reset event.

### Notify all admins when other admins reset their passwords

If this option is set to yes, then **all administrators** receive an email to their primary email address on file in Azure AD notifying them that another administrator has changed their password using SSPR.

Example: There are four administrators in an environment. Administrator "A" resets their password using SSPR. Administrators B, C, and D receive an email alerting them of this happening.

## On-premises integration

If you have installed, configured, and enabled Azure AD Connect, you will have the following additional options for on-premises integrations. If these options are greyed-out then writeback has not been properly configured see [Configuring password writeback](active-directory-passwords-writeback.md#configuring-password-writeback) for more information.

### Write back passwords to your on-premises directory

Controls whether or not password writeback is enabled for this directory and, if writeback is on, indicates the status of the on-premises writeback service. This is useful if you want to temporarily disable the password writeback without reconfiguring Azure AD Connect.

* If the switch is set to yes, then writeback is enabled, and federated and password hash synchronized users are able to reset their passwords.
* If the switch is set to no, then writeback is disabled, and federated and password hash synchronized users are not be able to reset their passwords.

### Allow users to unlock accounts without resetting their password

Designates whether or not users who visit the password reset portal should be given the option to unlock their on-premises Active Directory accounts without resetting their password. By default, Azure AD unlocks accounts when performing a password reset, this setting allows you to separate those two operations. 

* If set to “yes”, then users are given the option to reset their password and unlock the account, or to unlock without resetting the password.
* If set to “no”, then users are only be able to perform a combined password reset and account unlock operation.

## How does password reset work for B2B users?
Password reset and change are fully supported with all B2B configurations. The following three cases are supported for B2B user password reset.

1. **Users from a partner org with an existing Azure AD tenant** - If the organization you are partnering with has an existing Azure AD tenant, we **respect whatever password reset policies are enabled in that tenant**. For password reset to work, the partner organization just needs to make sure Azure AD SSPR is enabled, which is no additional charge for O365 customers, and can be enabled by following the steps in our [Getting Started with Password Management](https://azure.microsoft.com/documentation/articles/active-directory-passwords-getting-started/#enable-users-to-reset-or-change-their-aad-passwords) guide.
2. **Users who signed up using [self-service sign-up](active-directory-self-service-signup.md)** - If the organization you are partnering with used the [self-service sign-up](active-directory-self-service-signup.md) feature to get into a tenant, we let them reset with the email they registered.
3. **B2B users** - Any new B2B users created using the new [Azure AD B2B capabilities](active-directory-b2b-what-is-azure-ad-b2b.md) will also be able to reset their passwords with the email they registered during the invite process.

To test this scenario, go to http://passwordreset.microsoftonline.com with one of these partner users. As long as they have an alternate email or authentication email defined, password reset works as expected.

> [!NOTE]
> Microsoft accounts that have been granted guest access to your Azure AD tenant such as those from Hotmail.com, Outlook.com, or other personal email addresses are not able to use Azure AD SSPR and will need to reset their password using the information found in the article [When you can't sign in to your Microsoft account](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).

## Next steps

The following links provide additional information regarding password reset using Azure AD

* [How do I complete a successful rollout of SSPR?](active-directory-passwords-best-practices.md)
* [Reset or change your password](active-directory-passwords-update-your-own-password.md).
* [Register for self-service password reset](active-directory-passwords-reset-register.md).
* [Do you have a Licensing question?](active-directory-passwords-licensing.md)
* [What data is used by SSPR and what data should you populate for your users?](active-directory-passwords-data.md)
* [What authentication methods are available to users?](active-directory-passwords-how-it-works.md#authentication-methods)
* [What are the policy options with SSPR?](active-directory-passwords-policy.md)
* [What is password writeback and why do I care about it?](active-directory-passwords-writeback.md)
* [How do I report on activity in SSPR?](active-directory-passwords-reporting.md)
* [What are all of the options in SSPR and what do they mean?](active-directory-passwords-how-it-works.md)
* [I think something is broken. How do I troubleshoot SSPR?](active-directory-passwords-troubleshoot.md)
* [I have a question that was not covered somewhere else](active-directory-passwords-faq.md)

[Authentication]: ./media/active-directory-passwords-how-it-works/sspr-authentication-methods.png "Azure AD authentication methods available and quantity required"