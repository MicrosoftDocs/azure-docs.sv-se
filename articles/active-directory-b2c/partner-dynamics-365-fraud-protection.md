---
title: Självstudie för att konfigurera Azure Active Directory B2C med Microsoft Dynamics 365 bedrägeri skydd
titleSuffix: Azure AD B2C
description: Självstudie för att konfigurera Azure Active Directory B2C med Microsoft Dynamics 365 bedrägeri skydd för att identifiera riskfyllda och bedrägligt konto
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 9483b0a8829c042a7cf8d516c6007cbbf14a97ac
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105639750"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Självstudie: Konfigurera Microsoft Dynamics 365 bedrägeri skydd med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar [Microsoft Dynamics 365 bedrägeri skydd](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) med Azure Active Directory (AD) B2C.

Microsoft DFP ger klienter möjlighet att utvärdera om risken för försök att skapa nya konton och försöker logga in på klientens eko system är bedräglig. Microsoft DFP-utvärderingen kan användas av kunden för att blockera eller utmana misstänkta försök att skapa nya falska konton eller kompromettera befintliga konton. Konto skydd omfattar artificiell intelligens som har hälsostyrt enhets finger avtryck, API: er för riskbedömning, regel-och list erfarenhet i real tid för att optimera risk strategin enligt klientens affärs behov och ett styrkort för att övervaka bedrägerier och trender i klientens eko system.

I det här exemplet kommer vi att integrera konto skydds funktionerna i Microsoft DFP med ett Azure AD B2C användar flöde. Tjänsten finger avtryck externt varje inloggnings-eller registrerings försök och titta efter eventuella tidigare eller här misstänkta beteenden. Azure AD B2C anropar en besluts slut punkt från Microsoft DFP, som returnerar ett resultat baserat på alla tidigare och befintliga beteenden från den identifierade användaren, och även de anpassade reglerna som anges i Microsoft DFP-tjänsten. Azure AD B2C fattar ett godkännande beslut baserat på det här resultatet och skickar samma tillbaka till Microsoft DFP.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](./tutorial-create-tenant.md). Klienten är länkad till din Azure-prenumeration.

- Skaffa en [prenumeration](https://dynamics.microsoft.com/pricing/#Sales)på Microsoft DFP. Du kan också konfigurera en [utvärderings klient version](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) .

## <a name="scenario-description"></a>Scenariobeskrivning

Microsoft DFP-integrering innehåller följande komponenter:

- **Azure AD B2C klient**: autentiserar användaren och fungerar som en klient av Microsoft DFP. Är värd för ett finger avtryck skript som samlar in identifierings-och diagnostikdata för varje användare som kör en mål princip. Senare blockeras eller utmanings inloggnings-eller registrerings försök om Microsoft DFP hittar dem som är misstänkta.

- **Anpassad App Service**: ett webb program som har två syfte.

  - Hanterar HTML-sidor som ska användas som användar gränssnitt för Identity Experience Framework. Ansvarig för inbäddning av skript för Microsoft Dynamics 365-finger avtryck.

  - En API-styrenhet med RESTful-slutpunkter som ansluter Microsoft DFP till Azure AD B2C. Hantera data bearbetning, struktur och följer säkerhets kraven för båda.

- **Microsoft DFP finger avtrycks tjänst**: dynamiskt inbäddat skript, som loggar enhets telemetri och självkontrollerad användar information för att skapa ett unikt identifierbart finger avtryck för användaren som ska användas senare i besluts processen.

- **API-slutpunkter för Microsoft DFP**: ger besluts resultatet och accepterar en slutgiltig status som avspeglar den åtgärd som utförs av klient programmet. Azure AD B2C kommunicerar inte med slut punkterna direkt på grund av varierande krav på säkerhet och API-nyttolast, i stället använder app service som mellanliggande.

I följande arkitektur diagram visas implementeringen.

![Bilden visar arkitektur diagrammet Microsoft dynamics365 bedrägeri skydd](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Steg | Description |
|:-----| :-----------|
| 1. | Användaren kommer till inloggnings sidan. Användare väljer registrering för att skapa ett nytt konto och ange information på sidan. Azure AD B2C samlar in användarattribut.
| 2. | Azure AD B2C anropar API: t för mellanlager och passerar användar-attributen.
| 3. | API för mellanlager samlar in användarattribut och omvandlar dem till ett format som Microsoft DFP API kan använda. Sedan skickar den till Microsoft DFP-API: et.
| 4. | När Microsoft DFP-API: n förbrukar informationen och bearbetar den, returnerar den resultatet till API: t mellan lager.
| 5. | API för mellanlager bearbetar informationen och skickar tillbaka relevant information till Azure AD B2C.
| 6. | Azure AD B2C tar emot information tillbaka från API: t mellan lager. Om det visar ett felsvar visas ett fel meddelande för användaren. Om det visar sig ett lyckat svar, autentiseras och skrivs användaren till katalogen.

## <a name="set-up-the-solution"></a>Konfigurera lösningen

1. [Skapa ett Facebook-program](./identity-provider-facebook.md#create-a-facebook-application) som är konfigurerat för att tillåta federation till Azure AD B2C.
2. [Lägg till Facebook-hemligheten](./custom-policy-get-started.md#create-the-facebook-key) som du skapade som en princip nyckel för identitets erfarenhets ramverk.

## <a name="configure-your-application-under-microsoft-dfp"></a>Konfigurera ditt program under Microsoft DFP

[Konfigurera din Azure AD-klient](/dynamics365/fraud-protection/integrate-real-time-api) så att den använder Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Distribuera till webb programmet

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementera Microsoft DFP service-finger avtryck

[Microsoft DFP-enhetens finger avtryck](/dynamics365/fraud-protection/device-fingerprinting) är ett krav för Microsoft DFP Account Protection.

>[!NOTE]
>Förutom att Azure AD B2C UI-sidor kan kunden också implementera finger avtrycks tjänsten i app Code för mer omfattande enhets profilering. Finger avtrycks tjänsten i app Code ingår inte i det här exemplet.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Distribuera Azure AD B2C API-kod

Distribuera den [tillhandahållna API-koden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) till en Azure-tjänst. Koden kan [publiceras från Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure).

Set-CORS, Lägg till **tillåtet ursprung**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Du behöver senare URL: en för den distribuerade tjänsten för att konfigurera Azure AD med nödvändiga inställningar.

Mer information finns i [App Service-dokumentationen](../app-service/app-service-web-tutorial-rest-api.md) .

### <a name="add-context-dependent-configuration-settings"></a>Lägg till Sammanhangs beroende konfigurations inställningar

Konfigurera program inställningarna i [App Service i Azure](../app-service/configure-common.md#configure-app-settings). Detta gör att inställningar kan konfigureras på ett säkert sätt utan att kontrol lera dem i en lagrings plats. REST-API: et behöver följande inställningar:

| Programinställningar | Källa | Kommentarer |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Microsoft DFP-konfiguration |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Ditt Microsoft-enhets finger avtryck kund-ID |     |
| FraudProtectionSettings:ApiBaseUrl |  Din bas-URL från Microsoft DFP-portalen   | Ta bort-int för att anropa API: et för produktion i stället|
|  TokenProviderConfig: resurs  | Din bas-URL – https://api.dfp.dynamics-int.com     | Ta bort-int för att anropa API: et för produktion i stället|
|   TokenProviderConfig: ClientId       |Ditt bedrägeri skydd för Azure AD-klientens app-ID      |       |
| TokenProviderConfig: auktoritet | https://login.microsoftonline.com/<directory_ID> | Ditt bedrägeri skydd för Azure AD-klient organisation |
| TokenProviderConfig: CertificateThumbprint * | Tumavtrycket för det certifikat som ska användas för att autentisera mot din Azure AD-klients app |
| TokenProviderConfig:ClientSecret* | Hemligheten för din handlare Azure AD-klient app | Rekommenderas att använda en hemligheter-hanterare |

* Ange bara 1 av de två markerade parametrarna beroende på om du autentiserar med ett certifikat eller en hemlighet, till exempel ett lösen ord.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C konfiguration

### <a name="replace-the-configuration-values"></a>Ersätt konfigurations värden

I de angivna [anpassade principerna](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)söker du efter följande plats hållare och ersätter dem med motsvarande värden från din instans.

| Platshållare | Ersätt med | Kommentarer |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Ditt klient kort namn |  "yourtenant" från yourtenant.onmicrosoft.com   |
|{your_tenantId} | Klient-ID för din Azure AD B2C klient |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   App-ID för IdentityExperienceFramework-appen som kon figurer ATS i Azure AD B2C-klienten    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  App-ID för ProxyIdentityExperienceFramework-appen som kon figurer ATS i Azure AD B2C-klienten      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  App-ID för klientens lagrings program   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | Objekt-ID för klientens lagrings program    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Instrumentation-nyckel för App Insights-instansen *  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Slut punkt i App Service varifrån dina UI-filer hanteras    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | URL för din app service    |  https://yourapp.azurewebsites.net  |
|   {ditt-Facebook-app-ID}  |  App-ID för Facebook-appen som du konfigurerade för federation med Azure AD B2C   | 000000000000000   |
|  {din-Facebook-app-Secret}   |  Namnet på den princip nyckel som du har sparat Facebook-appens hemlighet som   | B2C_1A_FacebookAppSecret   |

* App Insights kan finnas i en annan klient. Det här är valfritt. Ta bort motsvarande TechnicalProfiles och OrechestrationSteps om det inte behövs.

### <a name="call-microsoft-dfp-label-api"></a>Anropa Microsoft DFP etikett-API

Kunder måste [implementera etikett-API](/dynamics365/fraud-protection/integrate-ap-api). Läs mer i [Microsoft DFP-API](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) .

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

Värdet för userID måste vara samma som det i motsvarande Azure AD B2C konfigurations värde (ObjectID).

>[!NOTE]
>Lägg till medgivande meddelande till sidan för attribut samling. Meddela att användarnas telemetri och användar identitets information registreras för konto skydd.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurera principen för Azure AD B2C

1. Gå till [Azure AD B2C principen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) i mappen principer.

2. Följ det här [dokumentet](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) för att ladda ned [LocalAccounts start paket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Konfigurera principen för Azure AD B2C klient.

>[!NOTE]
>Uppdatera principerna som är relaterade till din klient organisation.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten och under principer väljer du **identitets miljö ramverk**.

2. Välj din tidigare skapade **SignUpSignIn**.

3. Välj **Kör användar flöde** och välj inställningarna:

   a. **Program**: Välj den registrerade appen (EXEMPLET är JWT)

   b. **Svars-URL**: Välj **omdirigerings-URL**

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto

5. Microsoft DFP-tjänsten anropas under flödet efter att användarattribut har skapats. Om flödet är ofullständigt kontrollerar du att användaren inte har sparats i katalogen.

>[!NOTE]
>Uppdatera regler direkt i Microsoft DFP-portalen om du använder [Microsoft DFP-regel motor](/dynamics365/fraud-protection/rules).

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Microsoft DFP-exempel](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
