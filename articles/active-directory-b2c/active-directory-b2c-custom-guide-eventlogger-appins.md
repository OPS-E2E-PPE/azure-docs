---
title: How to track user behavior using events in Application Insights from Azure AD B2C | Microsoft Docs
description: Step-by-step guide to enable event logs in Application Insights from Azure AD B2C user journeys using custom policies - PREVIEW
services: active-directory-b2c
documentationcenter: dev-center-name
author: rojasja
manager: mtillman


ms.service: active-directory-b2c
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 3/15/2018
ms.author: joroja

---
# Tracking user behavior inside Azure AD B2C journeys using Application Insights

Azure Active Directory B2C works well with Azure Application Insights.  They provide detailed and customized event logs for your custom created user journeys.  This guide shows how to get started so you can: 
* gain insights on user behavior
* troubleshoot your own policies in development or in production
* measure performance
* create notifications from Application Insights

## How it works
A new provider has been added to Azure AD B2C's Identity Experience Framework: `Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0`.  It sends event data directly to Application Insights using the Instrumentation Key provided to Azure AD B2C.  A technical profile uses this provider to define an event from B2C.  The profile specifies the name of the event, the claims that will be recorded, and the instrumentation key.  To post an event, the technical profile is then added as am `orchestration step` or as a `validation technical profile` in a custom user journey.  The events can be unified by Application Insights using a correlation ID to record a user session.  Application Insights makes the event and session available within seconds and presents many visualization, export, and analytical tools.



## Prerequisites
Complete the steps in the [Getting started with custom policies](active-directory-b2c-get-started-custom.md).  This guide assumes that the custom policy starter pack is being used.  However it is not required.



## Step 1. Create an Application Insights resource and get the Instrumentation Key

Application Insights is a powerful tool. When using it with Azure AD B2C, the only requirement is to create a resource and obtain an Instrumentation Key.  Application Insights must be created in the [Azure portal.](https://portal.azure.com)

[Full documentation for Application Insights](https://docs.microsoft.com/azure/application-insights/)

1. Click on `+ Create a resource` in the Azure portal, within your subscription tenant.  This tenant is not your Azure AD B2C tenant.  
2. Search for and select `Application Insights`  
3. Create a resource of `Application Type` `ASP.NET web application` under a subscription of your preference.
4. Once created, open your Application Insights resource and note the  `Instrumentation Key` 

![Application Insights Overview and Instrumentation Key](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-key.png)

## Step 2. Add new ClaimType definitions to your Trust Framework extension file

### Open the extension file from the starter pack and add the following elements to the `<BuildingBlocks>` node.  The extensions filename is typically `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

```xml

    <ClaimsSchema>
      <ClaimType Id="EventType">
        <DisplayName>EventType</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="PolicyId">
        <DisplayName>PolicyId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="Culture">
        <DisplayName>Culture</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="CorrelationId">
        <DisplayName>CorrelationId</DisplayName>
        <DataType>string</DataType>
        <AdminHelpText />
        <UserHelpText />
      </ClaimType>
      <!--Additional claims used for passing claims to Application Insights Provider -->
      <ClaimType Id="federatedUser">
        <DisplayName>federatedUser</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
      <ClaimType Id="parsedDomain">
        <DisplayName>Parsed Domain</DisplayName>
        <DataType>string</DataType>
        <UserHelpText>The domain portion of the email address.</UserHelpText>
      </ClaimType>
      <ClaimType Id="userInLocalDirectory">
        <DisplayName>userInLocalDirectory</DisplayName>
        <DataType>boolean</DataType>
        <UserHelpText />
      </ClaimType>
    </ClaimsSchema>

```

## Step 3. Add new technical profiles that use the Application Insights provider

Technical profiles may be considered functions in Azure AD B2C's Identity Experience Framework.  Five technical profiles are defined in this example to open a session and post events.

| Technical Profile       | Task |
| ----------------------------- |:---------------------------------------------|
| AzureInsights-Common | common set of parameters to be included in  all Azure-Insights Technical Profiles     | 
| JourneyContextForInsights   | opens up the session in App Insights and sends a correlation ID |
| AzureInsights-SignInRequest     | creates a "SignIn" Event with a set of claims when a sign-in request has been received      | 
| AzureInsights-UserSignup | creates an Event called "UserSignup" when the signup option has been triggered by the user in a signup/signin journey     | 
| AzureInsights-SignInComplete | records the successful completion of an authentication when a token has been sent to the relying party application     | 

Add the profiles to the extension file from the starter pack by adding these elements to the `<ClaimsProviders>` node.  The extensions filename is typically `yourtenant.onmicrosoft.com-B2C_1A_TrustFrameworkExtensions.xml`

> [!IMPORTANT]
> Change the `Instrumentation Key` in the `ApplicationInsights-Common` technical profile to the GUID provided by your Application Insights resource.

```xml
<ClaimsProvider>
      <DisplayName>Application Insights</DisplayName>
      <TechnicalProfiles>

        <TechnicalProfile Id="JourneyContextForInsights">
          <DisplayName>Application Insights</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="CorrelationId" />
          </OutputClaims>
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInRequest">
          <InputClaims>
            <!-- 
							An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider
							to create an event with the specified value.
						-->
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-SignInComplete">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
            <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
            <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-UserSignup">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignup" />
          </InputClaims>
          <IncludeTechnicalProfile ReferenceId="AzureInsights-Common" />
        </TechnicalProfile>

        <TechnicalProfile Id="AzureInsights-Common">
          <DisplayName>Alternate Email</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
          <Metadata>
            <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
            <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
            <!-- 
							A boolean indicating whether delevoper mode is enabled. This controls how events are buffered. In a development environment
							with minimal event volume, enabling developer mode results in events being sent immediately to ApplicationInsights.
						-->
            <Item Key="DeveloperMode">false</Item>
            <!-- 
							A boolean indicating whether telemetry should be enabled or not.
						-->
            <Item Key="DisableTelemetry ">false</Item>
          </Metadata>
          <InputClaims>
            <!--
							Properties of an event are added using the syntax {property:NAME} where NAME is the name of the property being added
							to the event. DefaultValue can be either a static value or one resolved by one of the supported DefaultClaimResolvers.
						-->
            <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
            <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" />
            <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
          </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

```

## Step 4. Add the technical profiles for Application-Insights as orchestration steps in an existing User Journey.

Call `JournyeContextForInsights` as orchestration step 1

```xml
<!-- Initialize a session with Application Insights -->
<OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="JourneyContextForInsights" TechnicalProfileReferenceId="JourneyContextForInsights" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Call `Azure-Insights-SignInRequest` as orchestration step 2 to track that a sign-in/sign-up request has been received.

```xml
<!-- Track that we have received a sign in request -->
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AzureInsights-SignInRequest" />
          </ClaimsExchanges>
        </OrchestrationStep>
```

Immediately **before** the `SendClaims` orchestration step, add a new step that calls `Azure-Insights-UserSignup`. It is triggered when the signup button has been clicked in a signup/signin journey.

```xml
        <!-- Handles the user clicking the sign up link in the local account sign in page -->
        <OrchestrationStep Order="9" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
              <Value>newUser</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
              <Value>newUser</Value>
              <Value>false</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AzureInsights-UserSignup" />
          </ClaimsExchanges>
```

Immediately after the `SendClaims` orchestration step, call `Azure-Insights-SignInComplete`.   This step will reflect a successfully completed journey.

```xml
        <!-- Track that we have successfully sent a token -->
        <OrchestrationStep Order="11" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AzureInsights-SignInComplete" />
          </ClaimsExchanges>
        </OrchestrationStep>

```

> [!IMPORTANT]
> After adding the new orchestration steps, renumber the steps sequentially without skipping any integers from 1 to N


## Step 5. Upload your modified extensions file, run the policy, and view events in Application Insights

Save and upload the new trust framework extensions file.  Then, call the Relying Party policy from your application or use `Run Now` in the Azure AD B2C interface.  Within seconds, your events will be available in Application Insights.

1. Open the Application Insights resource in your Azure Active Directory tenant.
2. Click on `Events` in the `USAGE` submenu.
3. Set `During` to `Last hour` and `By` to `3 minutes`.  You may need to click `Refresh` to view results

![Application Insights USAGE-Events Blase](./media/active-directory-b2c-custom-guide-eventlogger-appins/app-ins-graphic.png)





##  NEXT STEPS

Add additional claim types and events to your user journey to fit your needs.  Here is a list of possible claims using additional claims resolvers.

### Culture-specific claims

```xml
Culture-specific Claims
            Referenced using {Culture:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="JourneyCultureDefaultClaimProcessor"/>
        public enum SupportedClaim
        {
             /// The two letter ISO code for the language i.e. en
            LanguageName
             
            /// The two letter ISO code for the region i.e. US
            RegionName,
 
            /// The RFC5646 language code i.e. en-US
            RFC5646,

            /// The LCID of language code i.e. 1033
            LCID
        }

```

### Policy-specific claims

```xml
Policy-specific Claims
Referenced using {Policy:One of the property names below}
 
        /// An enumeration of the claims supported by the <see cref="PolicyDefaultClaimProcessor"/> 
        public enum SupportedClaim
        {
            /// The trustframework tenant id
            TrustFrameworkTenantId,
  
            /// The tenant id of the relying party
            RelyingPartyTenantId,
 
            /// The policy id of the policy
            PolicyId,
 
            /// The tenant object id of the policy
            TenantObjectId
}

```

### OpenIDConnect specific claims

```xml
OpenIDConnect Specific Claims
Referenced using {OIDC:One of the property names below}
 
/// 
        /// An enumeration of the claims supported by the <see cref="OpenIdConnectDefaultClaimProcessor"/>

        public enum SupportedClaim
        {
            /// The OpenIdConnect prompt parameter
            Prompt,
 
            /// The OpenIdConnect login_hint parameter
            LoginHint,

            /// The OpenIdConnect domain_hint parameter
            DomainHint,
 
             /// The OpenIdConnect max_age parameter
            MaxAge,
 
            /// The OpenIdConnect client_id parameter
            ClientId,
 
            /// The OpenIdConnect username parameter
            Username,

            /// The OpenIdConnect password parameter
            Password,
 
            /// The OpenIdConnect resource type parameter
            Resource,
 
            /// The OpendIdConext acr_values parameter
             AuthenticationContextReferences
        }
 
```

### Non-protocol parameters included with OIDC & OAuth2 requests

```xml
Referenced using { OAUTH-KV:Querystring parameter name }
```

Any parameter name included as part of an OIDC or OAuth2 request can be mapped to a claim in the user journey.  It can then be recorded in the event. For example, the request from the application may include a query string parameter with a name of `app_session`, `loyalty_number` or `any_string`.

Sample Request from the application:
```
https://login.microsoftonline.com/sampletenant.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_signup_signin&client_id=e1d2612f-c2bc-4599-8e7b-d874eaca1ae1&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&app_session=0a2b45c&loyalty_number=1234567

```
The claims can then be added by adding an Input Claim element to the Application Insights event using:
```
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="app_session" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="loyalty_number" DefaultValue="{OAUTH-KV:loyalty_number}" />
```

### Other system claims

Some system claims must be added to the claims bag before they are available to record as events. The technical profile `SimpleUJContext` must be called as an orchestration step or a validation technical profile before these claims are available.

```xml
<ClaimsProvider>
    <DisplayName>User Journey Context Provider</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="SimpleUJContext">
                <DisplayName>User Journey Context Provide</DisplayName>
                <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.UserJourneyContextProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="IP-Address" />
                    <OutputClaim ClaimTypeReferenceId="CorrelationId" />
                    <OutputClaim ClaimTypeReferenceId="DateTimeInUtc" />
                    <OutputClaim ClaimTypeReferenceId="Build" />
                 </OutputClaims>
            </TechnicalProfile>
        </TechnicalProfiles>
</ClaimsProvider>



