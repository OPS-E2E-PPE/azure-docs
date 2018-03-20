---
title: Using language customization - Azure AD B2C| Microsoft Docs
description: Learn about customizing the language experience. 
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''

ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 02/26/2018
ms.author: davidmu

---

# Azure Active Directory B2C: Using language customization

>[!NOTE]
>This feature is in public preview.
>

Language customization allows your policy to accommodate different languages to suit your customer needs.  Microsoft provides the translations for 36 languages (see [Additional information](#additional-information)), but you can also provide your own translations for any language.  Even if your experience is only provided for a single language, you can customize any text on the pages.  

## How does language customization work?
Language customization allows you to select which languages your user journey is available in.  Once the feature is enabled, you can provide the query string parameter, ui_locales, from your application.  When you call into Azure AD B2C, we translate your page to the locale that you have indicated.  This type of configuration gives you complete control over the languages in your user journey and ignores the language settings of the customer's browser. Alternatively, you may not need that level of control over what languages your customer see.  If you don't provide a ui_locales parameter, the customer's experience is dictated by their browser's settings.  You can still control which languages your user journey is translated to by adding it as a supported language.  If a customer's browser is set to show a language you don't want to support, then the language you selected as a default in supported cultures is shown instead.

1. **ui-locales specified language** - Once you enable Language customization, your user journey is translated to the language specified here
2. **Browser requested language** - If no ui-locales was specified, it translates to the browser requested language, **if it was included in Supported languages**
3. **Policy default language** - If the browser doesn't specify a language, or it specifies one that is not supported, it translates to the policy default language

>[!NOTE]
>If you are using custom user attributes, you need to provide your own translations. See '[Customize your strings](#customize-your-strings)' for details.
>

## Support ui_locales requested languages 
Policies that were created before the general availability release of language customization will need to enable this feature first.  Policies created after will have language customization enabled by default.  By enabling 'Language customization' on a policy, you can now control the language of the user journey by adding the ui_locales parameter.
1. [Follow these steps to navigate to the B2C features page on the Azure portal.](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-app-registration#navigate-to-b2c-settings)
2. Navigate to a policy that you want to enable for translations.
3. Click **Language customization**.  
4. Click on **Enable language customization** on top.
5. Read the dialog and click 'Yes'.

## Select which languages in your user journey are enabled 
Enable a set of languages for your user journey to be translated in when the ui_locales parameter is not provided.
1. Ensure your policy has 'Language customization' enabled from previous instructions.
2. From your **Edit policy** page, select **Language customization**.
3. Select a language you would like to support.
4. In the properties pane, toggle **Enabled** to Yes.  
5. Click **Save** at the top of the properties pane.

>[!NOTE]
>If a ui_locales parameter is not provided, then the page is translated to the customer's browser language only if it is enabled
>

## Customize your strings
'Language customization' allows you to customize any string in your user journey.
1. Ensure your policy has 'Language customization' enabled from the previous instructions.
2. From your **Edit policy** page, select **Language customization**.
3. Select the language you want to customize.
4. Select the page you want to edit.
5. Click **Download defaults** (or **Download overrides** if you have previously edited this language). 

These steps give you a JSON file that you can use to start editing your strings.

### Changing any string on the page
1. Open the JSON file downloaded from previous instructions in a JSON editor.
2. Find the element you want to change.  You can find the `StringId` of the string you are looking for, or look for the `Value` you want to change.
3. Update the `Value` attribute with what you want displayed.
4. For every string you want to change, remember to toggle `Override` to **True**.
5. Save the file and upload your changes (you can find the upload control in the same place as where you downloaded the JSON file). 

>[!IMPORTANT]
>If you need to override a string, make sure to set the `Override` value to `true`.  If the value isn't changed, the entry is ignored. 
>

### Changing extension attributes
If you are looking to change the string for a custom user attribute, or want to add one to the JSON, it is in the following format:
```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Replace `<ExtensionAttribute>` with the name of your custom user attribute.  

Replace `<ExtensionAttributeValue>` with the new string to be displayed.

### Using LocalizedCollections
If you want to provide a set list of values for responses, you need to create a `LocalizedCollections`.  A `LocalizedCollections` is an array of `Name` and `Value` pairs.  The `Name` is what is displayed and the `Value` is what is returned in the claim.  To add a `LocalizedCollections`, it has the following format:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType", 
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` is the user attribute that this `LocalizedCollections` is a response to
* `Name` is the value shown to the user
* `Value` is what is returned in the claim when this option is selected

### Upload your changes
1. Once you have completed the changes to your JSON file, navigate back to your B2C tenant.
2. From your **Edit policy** page, select **Language customization**.
3. Select the language you want to provide translations.
4. Select the page you want to provide translations.
5. Click on the folder icon and select the JSON file to upload.
6. This changed is saved to your policy automatically.

## Using Page UI Customization with language customization

There are two ways to localize your HTML content.  By turning on ['Language customization'](active-directory-b2c-reference-language-customization.md).  Enabling this feature allows Azure AD B2C to forward the Open ID Connect parameter, `ui-locales`, to your endpoint.  Your content server can use this parameter to provide customized HTML pages that are language-specific.

Alternatively, we can pull content from different places based on the locale being used.  In your CORS enabled endpoint, you can set up a folder structure to host content for specific languages and we'll call the right one if you put the wildcard value, `{Culture:RFC5646}`.  For example, if I have this as my Custom Page URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
I can load my page in `fr` and when it is pulling html and css content, it will pull from:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## Custom locales

You can also add languages that Microsoft currently does not provide translations for.  You will need to provide the translations for all the strings in the policy.

1. From your **Edit policy** page, select **Language customization**.
2. Select **Add custom language** from the top of the page.
3. In the context pane that opens, identify which language you are providing translations for by entering a valid locale code.
4. For each page you can download a set of overrides for English and work on the translations.
5. Once you are done with the JSON files, you can upload them for each page.
6. Select **Enable** and your policy can now show this language for your user.
7. Remember to save you language once you have enabled it.

## Additional information

### Page UI customization labels are persisted as your first set of overrides once 'Language customization' is enabled
When you enable 'Language customization', your previous edits for labels using Page UI customization are persisted in a JSON file for English (en).  You can continue to change your labels and other strings by uploading language resources in 'Language customization'.
### Microsoft is committed to provide the most up-to-date translations for your use
We will continuously improve translations and keep them in compliance for you.  We will identify bugs and changes in global terminology and make the updates that will work seamlessly in your user journey.
### Support for right-to-left languages
We currently aren't providing support for right-to-left languages, if you require this feature please vote for this feature on [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### Social Identity provider translations
We provide the ui_locales OIDC parameter to social logins but they are not honored by some social identity providers, including Facebook and Google. 
### Browser behavior
Chrome and Firefox both request for their set language and if it is a supported language, it is displayed before the default.  Edge currently does not request a language and goes straight to the default language.

### What languages are supported?

| Language              | Language code |
|-----------------------|---------------|
| Bangla                | bn            |
| Czech                 | cs            |
| Danish                | da            |
| German                | de            |
| Greek                 | el            |
| English               | en            |
| Spanish               | es            |
| Finnish               | fi            |
| French                | fr            |
| Gujarati              | gu            |
| Hindi                 | hi            |
| Croatian              | hr            |
| Hungarian             | hu            |
| Italian               | it            |
| Japanese              | ja            |
| Kannada               | kn            |
| Korean                | ko            |
| Malayalam             | ml            |
| Marathi               | mr            |
| Malay                 | ms            |
| Norwegian Bokmal      | nb            |
| Dutch                 | nl            |
| Punjabi               | pa            |
| Polish                | pl            |
| Portuguese - Brazil   | pt-br         |
| Portuguese - Portugal | pt-pt         |
| Romanian              | ro            |
| Russian               | ru            |
| Slovak                | sk            |
| Swedish               | sv            |
| Tamil                 | ta            |
| Telugu                | te            |
| Thai                  | th            |
| Turkish               | tr            |
| Chinese - Simplified  | zh-hans       |
| Chinese - Traditional | zh-hant       |
