---
title: Självstudie för att konfigurera Azure Active Directory B2C med Microsoft Dynamics 365-bedrägeriskydd
titleSuffix: Azure AD B2C
description: Självstudie för att Azure Active Directory B2C med Microsoft Dynamics 365 Bedrägeriskydd för att identifiera riskfyllda och bedrägliga konton
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8c9d760ed888eb194ad8f282f180a634e3c09538
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587824"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Självstudie: Konfigurera Bedrägeriskydd i Microsoft Dynamics 365 med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar [Microsoft Dynamics 365 Bedrägeriskydd](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) med Azure Active Directory (AD) B2C.

Microsoft DFP ger klienter möjlighet att bedöma om risken för försök att skapa nya konton och försök att logga in i klientens ekosystem är bedräglig. Microsoft DFP-utvärdering kan användas av kunden för att blockera eller utmana misstänkta försök att skapa nya falska konton eller kompromettera befintliga konton. Kontoskydd omfattar artificiell intelligens som ger fingeravtryck för enheter, API:er för riskbedömning i realtid, regel- och listupplevelse för att optimera riskstrategi enligt klientens affärsbehov och ett styrkort för att övervaka effektiviteten och trenderna för bedrägeriskydd i klientens ekosystem.

I det här exemplet integrerar vi funktionerna för kontoskydd i Microsoft DFP med ett Azure AD B2C användarflöde. Tjänsten fingeravtryckar externt varje inloggnings- eller registreringsförsök och ser efter eventuella tidigare eller aktuella misstänkta beteenden. Azure AD B2C en beslutsslutpunkt från Microsoft DFP, som returnerar ett resultat baserat på allt tidigare och nuvarande beteende från den identifierade användaren och även de anpassade regler som anges i Microsoft DFP-tjänsten. Azure AD B2C fattar ett godkännandebeslut baserat på det här resultatet och skickar tillbaka det till Microsoft DFP.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure-prenumeration. Om du inte har någon prenumeration kan du skaffa ett [kostnadsfritt konto.](https://azure.microsoft.com/free/)

- En [Azure AD B2C klientorganisation](./tutorial-create-tenant.md). Klientorganisationen är länkad till din Azure-prenumeration.

- Skaffa en Microsoft [](https://dynamics.microsoft.com/pricing/#Sales)DFP-prenumeration . Du kan även konfigurera en [utvärderingsversion av](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) klienten.

## <a name="scenario-description"></a>Scenariobeskrivning

Microsoft DFP-integrering innehåller följande komponenter:

- **Azure AD B2C:** Autentiserar användaren och fungerar som en klient för Microsoft DFP. Är värd för ett fingeravtrycksskript som samlar in identifierings- och diagnostikdata för varje användare som kör en målprincip. Senare blockerar eller står inför inloggnings- eller inloggningsförsök om Microsoft DFP hittar dem misstänkta.

- **Anpassad apptjänst:** En webbapp som har två syften.

  - Visar HTML-sidor som ska Identity Experience Framework i användargränssnittet. Ansvarig för inbäddning av Microsoft Dynamics 365-fingeravtrycksskriptet.

  - En API-kontrollant med RESTful-slutpunkter som ansluter Microsoft DFP till Azure AD B2C. Hanterar databehandling, struktur och följer säkerhetskraven för båda.

- **Microsoft** DFP-fingeravtryckstjänst: Dynamiskt inbäddat skript som loggar enhettelemetri och självinfogade användarinformation för att skapa ett unikt identifierbart fingeravtryck som användaren kan använda senare i beslutsprocessen.

- **Microsoft DFP API-slutpunkter:** Ger beslutsresultatet och godkänner en slutlig status som återspeglar den åtgärd som utförs av klientprogrammet. Azure AD B2C kommunicerar inte direkt med slutpunkterna på grund av olika krav på säkerhet och API-nyttolast, utan använder i stället apptjänsten som mellannivå.

Följande arkitekturdiagram visar implementeringen.

![Bild som visar ett diagram över arkitekturen för bedrägeriskydd i Microsoft Dynamics365](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Steg | Description |
|:-----| :-----------|
| 1. | Användaren kommer till en inloggningssida. Användarna väljer registrering för att skapa ett nytt konto och anger information på sidan. Azure AD B2C samlar in användarattribut.
| 2. | Azure AD B2C anropar API:et i mellanlagret och skickar vidare användarattributen.
| 3. | API:et i mellanlagret samlar in användarattribut och omvandlar det till ett format som Microsoft DFP API kan använda. Sedan skickar den till Microsoft DFP API.
| 4. | När Microsoft DFP API använder informationen och bearbetar den returneras resultatet till API:et i mitten.
| 5. | API:et för mellanlagret bearbetar informationen och skickar tillbaka relevant information till Azure AD B2C.
| 6. | Azure AD B2C tar emot information från API:et för mellanskikt. Om ett felsvar visas visas ett felmeddelande för användaren. Om svaret Lyckades visas autentiseras användaren och skrivs till katalogen.

## <a name="set-up-the-solution"></a>Konfigurera lösningen

1. [Skapa ett Facebook-program](./identity-provider-facebook.md#create-a-facebook-application) som konfigurerats för att tillåta federation att Azure AD B2C.
2. [Lägg till Facebook-hemligheten](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key) som du skapade Identity Experience Framework en principnyckel.

## <a name="configure-your-application-under-microsoft-dfp"></a>Konfigurera ditt program under Microsoft DFP

[Konfigurera Din Azure AD-klientorganisation](/dynamics365/fraud-protection/integrate-real-time-api) att använda Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Distribuera till webbappen

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementera fingeravtryck för Microsoft DFP-tjänsten

[Microsoft DFP-enhetsavtryck](/dynamics365/fraud-protection/device-fingerprinting) är ett krav för Microsoft DFP-kontoskydd.

>[!NOTE]
>Förutom att Azure AD B2C användargränssnittssidor kan kunden även implementera fingeravtryckstjänsten i appkoden för mer omfattande enhetsprofilering. Fingeravtryckstjänsten i appkoden ingår inte i det här exemplet.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Distribuera API Azure AD B2C koden

Distribuera den [tillhandahållna API-koden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) till en Azure-tjänst. Koden kan [publiceras från Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure).

Konfigurera CORS, lägg till tillåten **ursprung**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Du behöver senare URL:en för den distribuerade tjänsten för att konfigurera Azure AD med de nödvändiga inställningarna.

Mer [information finns i App Service-dokumentationen.](../app-service/app-service-web-tutorial-rest-api.md)

### <a name="add-context-dependent-configuration-settings"></a>Lägga till kontextberoende konfigurationsinställningar

Konfigurera programinställningarna i [App Service i Azure](../app-service/configure-common.md#configure-app-settings). Detta gör att inställningarna kan konfigureras på ett säkert sätt utan att de checkas in på en lagringsplats. Rest-API:et behöver följande inställningar:

| Programinställningar | Källa | Kommentarer |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Microsoft DFP-konfiguration |     |
|FraudProtectionSettings:DevicePrintingCustomerId | Kund-ID:t för din Microsoft-enhet med fingeravtryck |     |
| FraudProtectionSettings:ApiBaseUrl |  Din bas-URL från Microsoft DFP-portalen   | Ta bort "-int" för att anropa produktions-API:et i stället|
|  TokenProviderConfig: Resurs  | Din bas-URL – https://api.dfp.dynamics-int.com     | Ta bort "-int" för att anropa produktions-API:et i stället|
|   TokenProviderConfig:ClientId       |Id för Din Azure AD-klientapp för bedrägeriskyddsförsäljare      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | Din Azure AD-klientorganisationsutfärdare för bedrägeriskyddsföretaget |
| TokenProviderConfig:CertificateThumbprint* | Tumavtrycket för certifikatet som ska användas för autentisering mot din återförsäljares Azure AD-klientapp |
| TokenProviderConfig:ClientSecret* | Hemligheten för din azure AD-klientapp för återförsäljare | Rekommenderas att du använder en hemlighetshanterare |

*Ange endast 1 av de 2 markerade parametrarna beroende på om du autentiserar med ett certifikat eller en hemlighet, till exempel ett lösenord.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C konfiguration

### <a name="replace-the-configuration-values"></a>Ersätt konfigurationsvärdena

I de [anpassade principerna](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)hittar du följande platshållare och ersätter dem med motsvarande värden från din instans.

| Platshållare | Ersätt med | Kommentarer |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Kortnamnet för din klientorganisation |  "yourtenant" från yourtenant.onmicrosoft.com   |
|{your_tenantId} | Klientorganisations-ID för Azure AD B2C klientorganisation |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   App-ID för identityExperienceFramework-appen som konfigurerats i din Azure AD B2C klientorganisation    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  App-ID för ProxyIdentityExperienceFramework-appen som konfigurerats i din Azure AD B2C klientorganisation      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  App-ID för klientorganisationens lagringsprogram   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | Objekt-ID för klientorganisationens lagringsprogram    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Instrumenteringsnyckel för din App Insights-instans*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Slutpunkt i apptjänsten som UI-filerna används från    | `https://yourapp.azurewebsites.net/B2CUI/GetUIPage`   |
|   {your_app_service_url}  | Webbadressen till din apptjänst    |  `https://yourapp.azurewebsites.net`  |
|   {your-facebook-app-id}  |  App-ID för Facebook-appen som du konfigurerade för federation med Azure AD B2C   | 000000000000000   |
|  {your-facebook-app-secret}   |  Namnet på den principnyckel som du har sparat Facebooks apphemlighet som   | B2C_1A_FacebookAppSecret   |

*App Insights kan finnas i en annan klientorganisation. Det här är valfritt. Ta bort motsvarande TechnicalProfiles och OrechestrationSteps om det inte behövs.

### <a name="call-microsoft-dfp-label-api"></a>Anropa Microsoft DFP-etikett-API

Kunder måste implementera [etikett-API: et](/dynamics365/fraud-protection/integrate-ap-api). Mer [information finns i Microsoft DFP API.](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0)

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

Värdet för userID måste vara samma som värdet i motsvarande Azure AD B2C -konfigurationsvärde (ObjectID).

>[!NOTE]
>Lägg till meddelande om medgivande på attributsamlingssidan. Meddela att användarnas telemetri- och användaridentitetsinformation registreras i kontoskyddssyfte.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurera Azure AD B2C princip

1. Gå till [Azure AD B2C princip](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) i mappen Principer.

2. Följ det [här dokumentet](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack) för att ladda [ned LocalAccounts-startpaketet](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Konfigurera principen för den Azure AD B2C klientorganisationen.

>[!NOTE]
>Uppdatera de principer som tillhandahålls för att relatera till din specifika klientorganisation.

## <a name="test-the-user-flow"></a>Testa användarflödet

1. Öppna klientorganisationen Azure AD B2C och under Principer väljer **du Identity Experience Framework**.

2. Välj den **signUpSignIn** som du skapade tidigare.

3. Välj **Kör användarflöde** och välj inställningarna:

   a. **Program:** Välj den registrerade appen (exemplet är JWT)

   b. **Svars-URL:** välj **omdirigerings-URL:en**

   c. Välj **Kör användarflöde.**

4. Gå igenom inloggningsflödet och skapa ett konto

5. Microsoft DFP-tjänsten anropas under flödet när användarattributet har skapats. Om flödet är ofullständigt kontrollerar du att användaren inte har sparats i katalogen.

>[!NOTE]
>Uppdatera regler direkt i Microsoft DFP-portalen om du använder [Microsoft DFP-regelmotorn](/dynamics365/fraud-protection/rules).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Microsoft DFP-exempel](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
