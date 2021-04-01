---
title: Spåra användar beteende med hjälp av Application Insights
titleSuffix: Azure AD B2C
description: Lär dig hur du aktiverar händelse loggar i Application Insights från Azure AD B2C användar resor.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 92da0b12a3119b048866eef5b18f658916595294
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101645933"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Spåra användar beteende i Azure AD B2C med Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

I Azure Active Directory B2C (Azure AD B2C) kan du skicka händelse data direkt till [Application Insights](../azure-monitor/app/app-insights-overview.md) med hjälp av Instrumentation-nyckeln som är avsedd att Azure AD B2C. Med en Application Insights teknisk profil kan du hämta detaljerade och anpassade händelse loggar för dina användar resor till:

- Få insikter om användar beteende.
- Felsök dina egna principer i utvecklingen eller i produktionen.
- Mät prestanda.
- Skapa meddelanden från Application Insights.

## <a name="overview"></a>Översikt

Om du vill aktivera anpassade händelse loggar lägger du till en Application Insights teknisk profil. I den tekniska profilen definierar du Application Insights Instrumentation-nyckeln, händelse namnet och anspråken som ska registreras. Om du vill publicera en händelse lägger du till den tekniska profilen som ett Orchestration-steg i en [användar resa](userjourneys.md).

Tänk på följande när du använder Application Insights:

- Det finns en kort fördröjning, vanligt vis mindre än fem minuter, innan nya loggar är tillgängliga i Application Insights.
- Med Azure AD B2C kan du välja vilka anspråk som ska registreras. Ta inte med anspråk med person uppgifter.
- Om du vill registrera en användarsession kan du använda ett korrelations-ID för att förena händelser.
- Ring Application Insights teknisk profil direkt från en [användar resa](userjourneys.md) eller [under resa](subjourneys.md). Använd inte en Application Insights teknisk profil som en [teknisk verifierings profil](validation-technical-profile.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

När du använder Application Insights med Azure AD B2C behöver du bara skapa en resurs och hämta Instrumentation-nyckeln. Mer information finns i [skapa en Application Insights resurs](../azure-monitor/app/create-new-resource.md).

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Kontrol lera att du använder den katalog som har din Azure-prenumeration. Välj **katalog + prenumerations** filter på den översta menyn och välj den katalog som innehåller din Azure-prenumeration. Den här klienten är inte din Azure AD B2C klient.
1. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Application Insights**.
1. Välj **Skapa**.
1. I **namn** anger du ett namn för resursen.
1. För **program typ** väljer du **ASP.NET-webbprogram**.
1. För **resurs grupp** väljer du en befintlig grupp eller anger ett namn för en ny grupp.
1. Välj **Skapa**.
1. Öppna den nya Application Insights-resursen, expandera **Essentials** och kopiera Instrumentation-tangenten.

![Skärm bild som visar Instrumentation-tangenten på fliken Application Insights översikt.](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definiera anspråk

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C princip körning. Du deklarerar dina anspråk i [ClaimsSchema-elementet](claimsschema.md).

1. Öppna tilläggs filen för principen. Filen kan se ut ungefär så här `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** .
1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om du inte ser elementet lägger du till det.
1. Hitta **ClaimsSchema** -elementet. Om du inte ser elementet lägger du till det.
1. Lägg till följande anspråk i **ClaimsSchema** -elementet:

   ```xml
   <ClaimType Id="EventType">
     <DisplayName>Event type</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="EventTimestamp">
     <DisplayName>Event timestamp</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="PolicyId">
     <DisplayName>Policy Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="Culture">
     <DisplayName>Culture ID</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="CorrelationId">
     <DisplayName>Correlation Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="federatedUser">
     <DisplayName>Federated user</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   <ClaimType Id="parsedDomain">
     <DisplayName>Domain name</DisplayName>
     <DataType>string</DataType>
     <UserHelpText>The domain portion of the email address.</UserHelpText>
   </ClaimType>
   <ClaimType Id="userInLocalDirectory">
     <DisplayName>userInLocalDirectory</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   ```

## <a name="add-new-technical-profiles"></a>Lägg till nya tekniska profiler

Tekniska profiler kan betraktas som funktioner i den anpassade principen. Dessa funktioner använder metoden för att [Inkludera tekniska profiler](technicalprofiles.md#include-technical-profile) , där en teknisk profil innehåller en annan teknisk profil och ändringar av inställningar eller nya funktioner. I följande tabell definieras de tekniska profiler som används för att öppna en session och publicera händelser.

| Teknisk profil | Uppgift |
| ----------------- | -----|
| AppInsights-Common | Den vanliga tekniska profilen med typisk konfiguration. Den innehåller Application Insights Instrumentation-nyckeln, en samling anspråk att spela in och utvecklarläge. De andra tekniska profilerna omfattar den vanliga tekniska profilen och lägger till fler anspråk, till exempel händelse namnet. |
| AppInsights-SignInRequest | Registrerar en **SignInRequest** -händelse med en uppsättning anspråk när en inloggnings förfrågan har mottagits. |
| AppInsights-UserSignUp | Registrerar en **UserSignUp** -händelse när användaren utlöser registrerings alternativet vid en registrering eller inloggnings resa. |
| AppInsights-SignInComplete | Registrerar en **SignInComplete** -händelse vid lyckad autentisering när en token har skickats till det förlitande part programmet. |

Öppna *TrustFrameworkExtensions.xml* -filen från start paketet. Lägg till de tekniska profilerna i **ClaimsProvider** -elementet:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Ändra Instrumentation-nyckeln i den `AppInsights-Common` tekniska profilen till det GUID som Application Insights resursen tillhandahåller.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Lägg till de tekniska profilerna som Orchestration-steg

Lägg till nya Dirigerings steg som refererar till de tekniska profilerna.

> [!IMPORTANT]
> När du har lagt till de nya stegen för dirigering kan du numrera om stegen sekventiellt utan att hoppa över heltal från 1 till N.

1. Anropa `AppInsights-SignInRequest` som det andra Orchestration-steget. Det här steget spårar att en registrerings-eller inloggnings förfrågan har mottagits.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Innan du `SendClaims` dirigerar steget lägger du till ett nytt steg som anropar `AppInsights-UserSignup` . Den utlöses när användaren väljer registrerings knappen i en registrerings-eller inloggnings resa.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
   <OrchestrationStep Order="8" Type="ClaimsExchange">
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
       <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. Efter `SendClaims` Orchestration-steget anropar du `AppInsights-SignInComplete` . Det här steget visar att resan har slutförts.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>Ladda upp filen, kör principen och Visa händelser

Spara och ladda upp *TrustFrameworkExtensions.xml* -filen. Anropa sedan den förlitande part principen från programmet eller Använd **Kör nu** i Azure Portal. Vänta tills dina händelser är tillgängliga i Application Insights.

1. Öppna **Application Insights** resursen i Azure Active Directory klient organisationen.
1. Välj **användning** och välj sedan **händelser**.
1. Anges **under** till **sista timmen** och **med** **3 minuter**. Du kan behöva uppdatera fönstret för att se resultatet.

![Skärm bild som visar Application Insights händelse statistik.](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Samla in mer data

Du kanske vill registrera fler anspråk för att passa dina affärs behov. Om du vill lägga till ett anspråk måste du först [definiera ett anspråk](#define-claims)och sedan lägga till anspråket i insamlingen av indatamängden. Anspråk som du lägger till i **AppInsights – vanliga** tekniska profiler visas i alla händelser. Anspråk som du lägger till i en speciell teknisk profil visas endast i händelsen. Det angivna anspråks elementet innehåller följande attribut:

- **ClaimTypeReferenceId** är referensen till en anspråks typ.
- **PartnerClaimType** är namnet på den egenskap som visas i Azure Insights. Använd syntaxen för `{property:NAME}` , där `NAME` är en egenskap som läggs till i händelsen.
- **DefaultValue** är ett fördefinierat värde som ska registreras, till exempel ett händelse namn. Om ett anspråk som används i användar resan är tomt används standardvärdet. `identityProvider`Anspråket anges till exempel av de tekniska profilerna för federationen, t. ex. Facebook. Om anspråket är tomt anger det att användaren har loggat in med ett lokalt konto. Standardvärdet är alltså inställt på **Local**. Du kan också registrera en [anspråks lösare](claim-resolver-overview.md) med ett sammanhangsbaserad värde, till exempel program-ID eller ANVÄNDAREns IP-adress.

### <a name="manipulate-claims"></a>Manipulera anspråk

Du kan använda [omvandlingar av inmatade anspråk](custom-policy-trust-frameworks.md#manipulating-your-claims) för att ändra inskickade anspråk eller generera nya innan du skickar dem till Application Insights. I följande exempel innehåller den tekniska profilen omvandlingen av inloggade `CheckIsAdmin` anspråk.

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Lägg till händelser

Om du vill lägga till en händelse skapar du en ny teknisk profil som innehåller den `AppInsights-Common` tekniska profilen. Lägg sedan till den nya tekniska profilen som ett Orchestration-steg i [användar resan](custom-policy-trust-frameworks.md#orchestration-steps). Använd [villkors](userjourneys.md#preconditions) elementet för att utlösa händelsen när du är klar. Rapportera till exempel händelsen endast när användare kör via multifaktorautentisering.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>Kom ihåg att numrera om Dirigerings stegen i tur och ordning när du lägger till en händelse i användar resan.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Aktivera utvecklarläge

När du använder Application Insights för att definiera händelser, kan du ange om utvecklarläge är aktiverat. Utvecklarläge styr hur händelser buffras. I en utvecklings miljö med minimal händelse volym aktiverar du utvecklarläge om du vill att händelser skickas direkt till Application Insights. Standardvärdet är `false`. Aktivera inte utvecklarläge i produktions miljöer.

Om du vill aktivera utvecklarläge ändrar du `DeveloperMode` metadata till `true` i den `AppInsights-Common` tekniska profilen:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Inaktivera telemetri

Om du vill inaktivera Application Insights loggar ändrar du `DisableTelemetry` metadata till `true` i den `AppInsights-Common` tekniska profilen:

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [skapar anpassade KPI-instrumentpaneler med hjälp av Azure Application insikter](../azure-monitor/app/tutorial-app-dashboards.md).

::: zone-end