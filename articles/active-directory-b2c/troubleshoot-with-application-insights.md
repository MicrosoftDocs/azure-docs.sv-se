---
title: Felsöka anpassade principer med Application Insights
titleSuffix: Azure AD B2C
description: Konfigurera Application Insights för att spåra körningen av dina anpassade principer.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074bffb8614be1f71ba1956fd5a238bc19354c58
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028751"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Samla in Azure Active Directory B2C loggar med Application Insights

Den här artikeln innehåller steg för att samla in loggar från Active Directory B2C (Azure AD B2C) så att du kan diagnostisera problem med dina anpassade principer. I Application Insights finns olika sätt att diagnostisera undantag och visualisera prestandaproblem i programmet. Azure AD B2C har en funktion för att skicka data till Application Insights.

De detaljerade aktivitets loggarna som beskrivs här ska **bara** aktive ras under utvecklingen av dina anpassade principer.

> [!WARNING]
> Ange inte `DeploymentMode` `Development` i produktions miljöer. Loggar samlar in alla anspråk som skickas till och från identitets leverantörer. Du när utvecklaren antar ansvar för alla personliga data som samlas in i dina Application Insights loggar. Dessa detaljerade loggar samlas endast in när principen placeras i **utvecklarläge**.

## <a name="set-up-application-insights"></a>Konfigurera Application Insights

Om du inte redan har en, skapar du en instans av Application Insights i din prenumeration.

> [!TIP]
> En enda instans av Application Insights kan användas för flera Azure AD B2C klienter. Sedan kan du filtrera efter klienten eller princip namnet i din fråga. Mer information finns i [loggarna i Application Insights](#see-the-logs-in-application-insights) exempel.

Följ dessa steg om du vill använda en avslutande instans av Application Insights i din prenumeration:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure-prenumeration (inte din Azure AD B2C-katalog).
1. Öppna Application Insights resurs som du skapade tidigare.
1. På sidan **Översikt** och registrera **Instrumentation-nyckeln**

Följ dessa steg om du vill skapa en instans av Application Insights i din prenumeration:

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj filtret **katalog + prenumeration** på den översta menyn och välj sedan den katalog som innehåller din Azure-prenumeration (inte din Azure AD B2C-katalog).
1. Välj **skapa en resurs** i den vänstra navigerings menyn.
1. Sök efter och välj **Application Insights** och välj sedan **skapa**.
1. Fyll i formuläret, Välj **Granska + skapa** och välj sedan **skapa**.
1. När distributionen har slutförts väljer **du gå till resurs**.
1. Under **Konfigurera** i Application Insights-menyn väljer du **Egenskaper**.
1. Registrera **Instrumentation-nyckeln** för användning i ett senare steg.

## <a name="configure-the-custom-policy"></a>Konfigurera den anpassade principen

1. Öppna den förlitande part filen (RP), till exempel *SignUpOrSignin.xml*.
1. Lägg till följande attribut i `<TrustFrameworkPolicy>` elementet:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Lägg till en `<UserJourneyBehaviors>` underordnad nod till noden om den inte redan finns `<RelyingParty>` . Den måste finnas efter `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Lägg till följande nod som underordnad till `<UserJourneyBehaviors>` elementet. Ersätt `{Your Application Insights Key}` med den Application Insights **Instrumentation-nyckel** som du registrerade tidigare.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` instruerar ApplicationInsights att påskynda Telemetrin genom bearbetnings pipelinen. Lämpligt för utveckling, men är begränsat till hög volym. I produktion ställer du in `DeveloperMode` på `false` .
    * `ClientEnabled="true"` skickar skript för ApplicationInsights på klient sidan för att spåra sid visning och fel på klient sidan. Du kan visa dessa i tabellen **browserTimings** i Application Insights-portalen. Genom att ställa in `ClientEnabled= "true"` lägger du till Application Insights i sid skriptet och du får tids inställningar för sid inläsningar och AJAX-anrop, antal, information om webb läsar undantag och AJAX-fel samt antal användare och sessioner. Det här fältet är **valfritt** och anges som `false` standard.
    * `ServerEnabled="true"` skickar den befintliga UserJourneyRecorder-JSON som en anpassad händelse till Application Insights.

    Exempel:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Överför principen.

## <a name="see-the-logs-in-application-insights"></a>Se loggarna i Application Insights

Det finns en kort fördröjning, vanligt vis mindre än fem minuter, innan du kan se nya loggar i Application Insights.

1. Öppna Application Insights resurs som du skapade i [Azure Portal](https://portal.azure.com).
1. På sidan **Översikt** väljer du **loggar**.
1. Öppna en ny flik i Application Insights.

Här är en lista över frågor som du kan använda för att visa loggarna:

| Fråga | Beskrivning |
|---------------------|--------------------|
| `traces` | Hämta alla loggar som genereras av Azure AD B2C |
| `traces | where timestamp > ago(1d)` | Hämta alla loggar som genererats av Azure AD B2C den senaste dagen.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  Hämta alla loggar med fel från de senaste två timmarna.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Hämta alla loggar som genereras av Azure AD B2C *contoso.onmicrosoft.com* -klient och användar resa är *b2c_1a_signinandup*. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| Hämta alla loggar som genereras av Azure AD B2C för ett korrelations-ID. Ersätt korrelations-ID: t med ditt korrelations-ID. | 

Posterna kan vara långa. Exportera till CSV för en närmare titt.

Mer information om frågor finns i [Översikt över logg frågor i Azure Monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-logs-in-vs-code-extension"></a>Se loggarna i VS Code-tillägget

Vi rekommenderar att du installerar [Azure AD B2C-tillägget](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) för [vs Code](https://code.visualstudio.com/). Med Azure AD B2C-tillägget ordnas loggarna efter princip namn, korrelations-ID (Application Insights visar den första siffran i korrelations-ID: t) och loggens tidsstämpel. Den här funktionen hjälper dig att hitta relevant logg baserat på den lokala tids stämplingen och se användar resan som utförd av Azure AD B2C.

> [!NOTE]
> Communityn har utvecklat vs Code-tillägget för Azure AD B2C för att hjälpa identitets utvecklare. Tillägget stöds inte av Microsoft och görs tillgängligt helt och hållet.

### <a name="set-application-insights-api-access"></a>Ange Application Insights-API-åtkomst

När du har konfigurerat Application Insights och konfigurerat den anpassade principen måste du hämta ditt Application Insights **API-ID** och skapa **API-nyckel**. Både API-ID och API-nyckeln används av Azure AD B2C-tillägget för att läsa Application Insights Events (telemetrivärden). Dina API-nycklar bör hanteras som lösen ord. Behåll IT-hemligheten.

> [!NOTE]
> Application Insights Instrumentation-nyckeln som du skapar tidigare används av Azure AD B2C för att skicka telemetrivärden till Application Insights. Du använder bara Instrumentation-nyckeln i din Azure AD B2C-princip, inte i vs Code-tillägget.

Så här hämtar du Application Insights-ID och nyckel:

1. I Azure Portal öppnar du Application Insights resursen för ditt program.
1. Välj **Inställningar** och välj sedan **API-åtkomst**.
1. Kopiera **program-ID**
1. Välj **skapa API-nyckel**
1. Markera rutan **Läs telemetri** .
1. Kopiera **nyckeln** innan du stänger bladet skapa API-nyckel och spara den på ett säkert sätt. Om du tappar bort nyckeln måste du skapa en ny.

    ![Skärm bild som visar hur du skapar API-åtkomst nyckel.](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Konfigurera Azure AD B2C VS Code-tillägg

Nu har du Azure Application Insights API-ID och nyckel, kan du konfigurera vs Code-tillägget för att läsa loggarna. Azure AD B2C VS Code-tillägg innehåller två omfång för inställningar:

- **Globala inställningar för användare** – inställningar som tillämpas globalt på valfri instans av vs Code som du öppnar.
- **Inställningar för arbets yta** – inställningar som lagras i din arbets yta och gäller endast när arbets ytan öppnas (använder vs Code **Open-mapp**).

1. Klicka på ikonen **Inställningar** i **Azure AD B2C spårnings** Utforskaren.

    ![Skärm bild som visar Välj Application Insights-inställningar.](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Ange Azure Application Insights **-ID** och **nyckel**.
1. Klicka på **Spara**

När du har sparat inställningarna visas Application Insights-loggarna i fönstret **Azure AD B2C trace (App Insights)** .

![Skärm bild av Azure AD B2C-tillägget för VSCode, som presenterar Azure Application Insights-spårningen.](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


## <a name="configure-application-insights-in-production"></a>Konfigurera Application Insights i produktion

För att förbättra produktions miljöns prestanda och förbättra användar upplevelsen är det viktigt att konfigurera principen att ignorera meddelanden som inte är viktiga. Använd följande konfiguration för att endast skicka kritiska fel meddelanden till din Application Insights. 

1. Ange `DeploymentMode` attributet för [TrustFrameworkPolicy](trustframeworkpolicy.md) till `Production` . 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. Ange `DeveloperMode` [JourneyInsights](relyingparty.md#journeyinsights) till `false` .

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. Ladda upp och testa din princip.



## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [felsöker Azure AD B2C anpassade principer](troubleshoot-custom-policies.md)
