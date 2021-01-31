---
title: Spåra användar beteende med Application Insights
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
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218561"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Spåra användar beteende i Azure Active Directory B2C att använda Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) stöder sändning av händelse data direkt till [Application Insights](../azure-monitor/app/app-insights-overview.md) med hjälp av Instrumentation-nyckeln som är avsedd att Azure AD B2C. Med en Application Insights teknisk profil kan du hämta detaljerade och anpassade händelse loggar för dina användar resor till:

* Få insikter om användar beteende.
* Felsök dina egna principer i utvecklingen eller i produktionen.
* Mät prestanda.
* Skapa meddelanden från Application Insights.

## <a name="overview"></a>Översikt

Om du vill aktivera anpassade händelse loggar lägger du till en Application Insights teknisk profil. I den tekniska profilen definierar du Application Insights Instrumentation-nyckel, händelse namn och anspråk att registrera. Om du vill publicera en händelse läggs den tekniska profilen till som ett Orchestration-steg i en [användar resa](userjourneys.md).

Tänk på följande när du använder Application Insights:

- Det finns en kort fördröjning, vanligt vis mindre än fem minuter, innan nya loggar är tillgängliga i Application Insights.
- Med Azure AD B2C kan du välja vilka anspråk som ska registreras. Ta inte med anspråk med person uppgifter.
- Om du vill registrera en användarsession kan händelser vara enhetliga genom att använda ett korrelations-ID. 
- Anropa den Application Insights tekniska profilen direkt från en [användar resa](userjourneys.md) eller [under transporter](subjourneys.md). Använd inte Application Insights teknisk profil som en [teknisk validerings profil](validation-technical-profile.md).

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Skapa en Application Insights-resurs

När du använder Application Insights med Azure AD B2C behöver du bara skapa en resurs och hämta Instrumentation-nyckeln. Mer information finns i [skapa en Application Insights resurs](../azure-monitor/app/create-new-resource.md)

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrol lera att du använder den katalog som innehåller din Azure-prenumeration genom att välja filtret **katalog + prenumeration** på den översta menyn och välja den katalog som innehåller din prenumeration. Den här klienten är inte din Azure AD B2C klient.
3. Välj **skapa en resurs** i det övre vänstra hörnet av Azure Portal och Sök sedan efter och välj **Application Insights**.
4. Klicka på **Skapa**.
5. Ange ett **namn** för resursen.
6. För **program typ** väljer du **ASP.NET-webbprogram**.
7. För **resurs grupp** väljer du en befintlig grupp eller anger ett namn för en ny grupp.
8. Klicka på **Skapa**.
4. När du har skapat Application Insights-resursen öppnar du den, expanderar **Essentials** och kopierar Instrumentation-nyckeln.

![Application Insights översikt och Instrumentation-nyckel](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definiera anspråk

Ett anspråk ger tillfällig lagring av data under en Azure AD B2C princip körning. [Anspråks schema](claimsschema.md) är den plats där du deklarerar dina anspråk.

1. Öppna tilläggs filen för principen. Till exempel <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Sök efter [BuildingBlocks](buildingblocks.md) -elementet. Om elementet inte finns lägger du till det.
1. Leta upp [ClaimsSchema](claimsschema.md) -elementet. Om elementet inte finns lägger du till det.
1. Lägg till följande anspråk i **ClaimsSchema** -elementet. 

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

Tekniska profiler kan betraktas som funktioner i den anpassade principen. I den här tabellen definieras de tekniska profiler som används för att öppna en session och publicera händelser. Lösningen använder metoden för att [Inkludera tekniska profiler](technicalprofiles.md#include-technical-profile) . När en teknisk profil innehåller en annan teknisk profil för att ändra inställningar eller lägga till nya funktioner.

| Teknisk profil | Uppgift |
| ----------------- | -----|
| AppInsights-Common | Den vanliga tekniska profilen med den gemensamma uppsättningen konfiguration. Inklusive Application Insights Instrumentation-nyckel, insamling av anspråk att registrera och utvecklarläge. Följande tekniska profiler innehåller den vanliga tekniska profilen och lägger till fler anspråk, till exempel händelse namnet. |
| AppInsights-SignInRequest | Registrerar en `SignInRequest` händelse med en uppsättning anspråk när en inloggnings förfrågan har mottagits. |
| AppInsights-UserSignUp | Registrerar en `UserSignUp` händelse när användaren utlöser inloggnings alternativet i en inloggnings-eller inloggnings resa. |
| AppInsights-SignInComplete | Registrerar en `SignInComplete` händelse när en autentisering har slutförts, när en token har skickats till det förlitande part programmet. |

Lägg till profilerna i *TrustFrameworkExtensions.xml* -filen från start paketet. Lägg till dessa element i **ClaimsProviders** -elementet:

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
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

Anropa `AppInsights-SignInRequest` som dirigerings steg 2 för att spåra att en begäran om inloggning/registrering har tagits emot:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Omedelbart *före* `SendClaims` Orchestration-steget lägger du till ett nytt steg som anropar `AppInsights-UserSignup` . Den utlöses när användaren väljer knappen Registrera i en resa för registrering/inloggning.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
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

Direkt efter `SendClaims` Orchestration-steget anropa `AppInsights-SignInComplete` . Det här steget visar att resan har slutförts.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> När du har lagt till de nya stegen för dirigering kan du numrera om stegen sekventiellt utan att hoppa över heltal från 1 till N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Ladda upp filen, kör principen och Visa händelser

Spara och ladda upp *TrustFrameworkExtensions.xml* -filen. Anropa sedan den förlitande part principen från programmet eller Använd **Kör nu** i Azure Portal. Vänta en minut, så kommer dina händelser att vara tillgängliga i Application Insights.

1. Öppna **Application Insights** resursen i Azure Active Directory klient organisationen.
2. Välj **användning** och välj sedan **händelser**.
3. Anges **under** till **sista timmen** och **med** **3 minuter**.  Du kan behöva välja **Uppdatera** för att visa resultatet.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Samla in mer data

Du kanske vill registrera fler anspråk för att passa dina affärs behov. Om du vill lägga till ett anspråk måste du först [definiera ett anspråk](#define-claims)och sedan lägga till anspråket i insamlingen av indatamängden. Anspråk som du lägger till i *AppInsights – vanliga* tekniska profiler visas i alla händelser. Anspråk som du lägger till i en speciell teknisk profil visas endast i den händelsen. Det angivna anspråks elementet innehåller följande attribut:

- **ClaimTypeReferenceId** – är en referens till en anspråks typ. 
- **PartnerClaimType** – är namnet på den egenskap som visas i Azure Insights. Använd syntaxen för `{property:NAME}` , där `NAME` är egenskapen som läggs till i händelsen.
- **Standardvärde** – ett fördefinierat värde som ska registreras, t. ex. händelse namn. Ett anspråk som används i användar resan, till exempel namnet på identitets leverantören. Om anspråket är tomt används standardvärdet. `identityProvider`Anspråket anges till exempel av de tekniska profilerna för federationen, t. ex. Facebook. Om anspråket är tomt, anger det att användaren loggar in med ett lokalt konto. Standardvärdet är alltså inställt på *Local*. Du kan också registrera en [anspråks lösare](claim-resolver-overview.md) med ett sammanhangsbaserad värde, till exempel program-ID eller ANVÄNDAREns IP-adress.

### <a name="manipulating-claims"></a>Manipulera anspråk

Du kan använda [omvandlingar av inmatade anspråk](custom-policy-trust-frameworks.md#manipulating-your-claims) för att ändra inskickade anspråk eller generera nya innan du skickar till Application Insights. I följande exempel innehåller den tekniska profilen transformeringen av *CheckIsAdmin* -ingångs anspråk. 

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

Om du vill lägga till en händelse skapar du en ny teknisk profil som innehåller den *AppInsights-vanliga* tekniska profilen. Lägg sedan till den tekniska profilen som Orchestration-steg i [användar resan](custom-policy-trust-frameworks.md#orchestration-steps). Använd [villkoret](userjourneys.md#preconditions) för att utlösa händelsen när du vill. Rapportera till exempel händelsen endast när användare kör via MFA.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Nu när du har en teknisk profil lägger du till händelsen i användar resan. Numrera sedan om stegen sekventiellt utan att hoppa över heltal från 1 till N.

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

Om du vill aktivera utvecklarläge går du till *AppInsights-gemensam* teknisk profil och ändrar `DeveloperMode` metadata till `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Inaktivera telemetri

Om du vill inaktivera insikts loggar för program i *AppInsights-common* Technical Profile, ändrar du `DisableTelemetry` metadata till `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar anpassade KPI-instrumentpaneler med hjälp av Azure Application insikter](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end