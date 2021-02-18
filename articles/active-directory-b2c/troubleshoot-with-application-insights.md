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
ms.date: 10/16/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4a68b492bad4ac091b4600c9ec81ac0de27cc05
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100572896"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Samla in Azure Active Directory B2C loggar med Application Insights

Den här artikeln innehåller steg för att samla in loggar från Active Directory B2C (Azure AD B2C) så att du kan diagnostisera problem med dina anpassade principer. Application Insights är ett sätt att diagnostisera undantag och visualisera problem med program prestanda. Azure AD B2C innehåller en funktion för att skicka data till Application Insights.

De detaljerade aktivitets loggarna som beskrivs här ska **bara** aktive ras under utvecklingen av dina anpassade principer.

> [!WARNING]
> Ange inte `DeploymentMode` `Development` i produktions miljöer. Loggar samlar in alla anspråk som skickas till och från identitets leverantörer. Du när utvecklaren antar ansvar för alla personliga data som samlas in i dina Application Insights loggar. Dessa detaljerade loggar samlas endast in när principen placeras i **utvecklarläge**.

## <a name="set-up-application-insights"></a>Konfigurera Application Insights

Om du inte redan har en, skapar du en instans av Application Insights i din prenumeration.

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

1. Lägg till en `<UserJourneyBehaviors>` underordnad nod till noden om den inte redan finns `<RelyingParty>` . Den måste finnas omedelbart efter `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
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

| Söka i data | Description |
|---------------------|--------------------|
`traces` | Se alla loggar som genererats av Azure AD B2C |
`traces | where timestamp > ago(1d)` | Se alla loggar som genererats av Azure AD B2C den senaste dagen

Posterna kan vara långa. Exportera till CSV för en närmare titt.

Mer information om frågor finns i [Översikt över logg frågor i Azure Monitor](../azure-monitor/logs/log-query-overview.md).

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

Communityn har utvecklat ett användar resa visnings program för att hjälpa identitets utvecklare. Den läser från din Application Insights instans och ger en välstrukturerad vy över användar resans händelser. Du får käll koden och distribuerar den i din egen lösning.

Användarens körnings spelare stöds inte av Microsoft och görs tillgänglig enbart i befintligt skick.

Du hittar den version av visnings programmet som läser händelser från Application Insights på GitHub, här:

[Azure-samples/Active-Directory-B2C-Advanced-policys](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
