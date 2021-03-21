---
title: Självstudie för att konfigurera Azure Active Directory B2C med Onfido
titleSuffix: Azure AD B2C
description: Lär dig att integrera Azure AD B2C-autentisering med Onfido för dokument-ID och verifiering av ansikts biometrik
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/03/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 46c6eac80ddbff73d99e05c070e66aa1700da174
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928638"
---
# <a name="tutorial-for-configuring-onfido-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera Onfido med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar Azure AD B2C med [Onfido](https://onfido.com/). Onfido är ett dokument-ID och en verifierings app för ansikts biometrik. Det gör det möjligt för företag att uppfylla *kunskaper om dina kund* -och identitets krav i real tid. Onfido använder sofistikerade AI-baserade identitets verifiering, som först verifierar ett foto-ID och sedan matchar det mot deras ansikts biometrik. Den här lösningen binder en digital identitet till sin verkliga person och ger en säker onboarding-upplevelse och minskar bedrägerier.

I det här exemplet ansluter vi Onfido-tjänsten i registrerings-eller inloggnings flödet för att verifiera identiteten. Välgrundade beslut om vilken produkt och tjänst som användaren har åtkomst till görs baserat på Onfido resultat.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- [En Azure AD B2C klient](./tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

- Ett [utvärderings konto](https://onfido.com/signup/)för Onfido.

## <a name="scenario-description"></a>Scenariobeskrivning

Onfido-integreringen innehåller följande komponenter:

- Azure AD B2C klient – auktoriseringsservern som ansvarar för att verifiera användarens autentiseringsuppgifter baserat på anpassade principer som definierats i klient organisationen. Det kallas även för identitets leverantören. Den är värd för Onfido-klient programmet som samlar in användar dokumenten och skickar dem till Onfido-API-tjänsten.

- Onfido-klient – ett konfigurerbart verktyg för att konfigurera JavaScript-klient för dokument som distribueras på andra webb sidor. Samlar in dokumenten och utför preliminära kontroller som dokument storlek och kvalitet.

- Mellanliggande REST API – ger slut punkter för den Azure AD B2C klienten som kommunicerar med Onfido-API-tjänsten, hanterar data bearbetning och följer säkerhets kraven för båda.

- Onfido-API-tjänst – backend-tjänsten som tillhandahålls av Onfido, som sparar och verifierar dokumenten som tillhandahålls av användaren.

I följande arkitektur diagram visas implementeringen.

![skärm bild för onfido-Architecture-diagram](media/partner-onfido/onfido-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | Användaren kommer till inloggnings sidan. Användaren registrerar sig för att skapa ett nytt konto och ange information på sidan. Azure AD B2C samlar in användarattribut. Onfido-klient som finns i Azure AD B2C söker preliminärt efter användar informationen.
| 2. | Azure AD B2C anropar API: t för mellanlager och passerar användar-attributen.
| 3. | API för mellanlager samlar in användarattribut och omvandlar dem till ett format som Onfido-API: et kan förbruka. Skickar det sedan till Onfido.
| 4. | Onfido förbrukar informationen och bearbetar den för att verifiera användar identifiering. Sedan returnerar den resultatet till API: t mellan lagret.
| 5. | API för mellan lager bearbetar informationen och skickar tillbaka relevant information i rätt JSON-format till Azure AD B2C.
| 6. | Azure AD B2C tar emot information tillbaka från API för mellanliggande lager. Om det visar ett felsvar visas ett fel meddelande för användaren. Om det visar sig ett lyckat svar, autentiseras och skrivs användaren till katalogen.

## <a name="onboard-with-onfido"></a>Publicera med Onfido

1. Kontakta [Onfido](https://onfido.com/signup/)om du vill skapa ett Onfido-konto.

2. När ett konto har skapats skapar du en [API-nyckel](https://documentation.onfido.com/). Live-nycklar är fakturerbara, men du kan använda [sandbox-nycklar för att testa](https://documentation.onfido.com/?javascript#sandbox-and-live-differences) lösningen. Sandbox-nycklarna ger samma resultat struktur som Live-nycklar, men resultaten är alltid fördefinierade. Dokument bearbetas eller sparas inte.

>[!NOTE]
> Du kommer att behöva nyckeln senare.

Mer information om Onfido finns i [API-dokumentationen för Onfido](https://documentation.onfido.com) och [Onfido Developer Hub](https://developers.onfido.com).  

## <a name="configure-azure-ad-b2c-with-onfido"></a>Konfigurera Azure AD B2C med Onfido

### <a name="part-1---deploy-the-api"></a>Del 1 – distribuera API: et

- Distribuera den tillhandahållna [API-koden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/API/Onfido.Api) till en Azure-tjänst. Du kan publicera koden från Visual Studio genom att följa dessa [anvisningar](/visualstudio/deployment/quickstart-deploy-to-azure).
- Set-CORS, Lägg till **tillåtet ursprung** som https://{your_tenant_name}. b2clogin. com

>[!NOTE]
>Du behöver URL: en för den distribuerade tjänsten för att konfigurera Azure AD med nödvändiga inställningar.

#### <a name="adding-sensitive-configuration-settings"></a>Lägga till känsliga konfigurations inställningar

Program inställningar kan konfigureras i [App Service i Azure](../app-service/configure-common.md#configure-app-settings). App Service gör det möjligt att konfigurera inställningar på ett säkert sätt utan att kontrol lera dem i en lagrings plats. REST-API: et behöver följande inställningar:

| Namn på program inställning | Källa | Kommentarer |
|:-------------------------|:-------|:-------|
|OnfidoSettings: AuthToken| Onfido-konto |

### <a name="part-2---deploy-the-ui"></a>Del 2 – distribuera användar gränssnittet

#### <a name="configure-your-storage-location"></a>Konfigurera din lagrings plats

1. Konfigurera en [Blob Storage-behållare i ditt lagrings konto](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)

2. Lagra UI-filerna från [mappen UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI) till BLOB-behållaren.

3. Tillåt CORS-åtkomst till lagrings behållare som du skapade genom att följa dessa anvisningar:

   a. Gå till **Inställningar**  > **tillåtet ursprung**, ange `https://{your_tenant_name}.b2clogin.com` . Ersätt ditt-Tenant-namn med namnet på din Azure AD B2C-klient. Till exempel https://fabrikam.b2clogin.com . Använd små bokstäver när du anger ditt klient namn.

   b. För **tillåtna metoder** väljer du `GET` och `PUT` .

   c. Välj **Spara**.

#### <a name="update-ui-files"></a>Uppdatera UI-filer

1. I UI-filerna går du till mappen [ **ocean_blue**](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/UI/ocean_blue)

2. Öppna varje HTML-fil.

3. Sök efter och ersätt {Your-UI-BLOB-container-URL} med URL: en till den plats där dina UI **ocean_blue**-, **förd**-och **till gångar** -mappar finns

4. Sök och ersätt {din-mellanliggande API-URL} med URL: en för mellanliggande API app service.

#### <a name="upload-your-files"></a>Ladda upp filer

1. Lagra UI-filerna från mappen UI till BLOB-behållaren.

2. Använd [Azure Storage Explorer](../virtual-machines/disks-use-storage-explorer-managed-disks.md) för att hantera dina filer och åtkomst behörigheter.

### <a name="part-3---configure-azure-ad-b2c"></a>Del 3 – Konfigurera Azure AD B2C

#### <a name="replace-the-configuration-values"></a>Ersätt konfigurations värden

I de angivna [anpassade principerna](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/OnFido-Combined/Policies)söker du efter följande plats hållare och ersätter med motsvarande värden från din instans.

| Platshållare | Ersätt med värde | Exempel  |
|:---------------|:----------------|:-------------------|
| {your_tenant_name}  | Ditt klient kort namn |  "yourtenant" från yourtenant.onmicrosoft.com |
| {your_tenantID} | TenantID för din Azure AD B2C-klient | 01234567-89ab-cdef-0123-456789abcdef           |
| {your_tenant_IdentityExperienceFramework_appid}        | App-ID för IdentityExperienceFramework-appen som kon figurer ATS i Azure AD B2C-klienten      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | App-ID för ProxyIdentityExperienceFramework-appen som kon figurer ATS i Azure AD B2C-klienten | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | App-ID för klientens lagrings program                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | Objekt-ID för klientens lagrings program                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_app_insights_instrumentation_key} | Instrumentation-nyckel för App Insights-instansen *| 01234567-89ab-cdef-0123-456789abcdef|
|{your_ui_file_base_url}| URL till den plats där dina UI **ocean_blue**-, **Dist**-och **assets** -mappar finns | https://yourstorage.blob.core.windows.net/UI/|
| {your_app_service_URL}                                 | URL för den app service som du har konfigurerat                                             | `https://yourapp.azurewebsites.net`          |

* App Insights kan finnas i en annan klient. Det här är valfritt. Ta bort motsvarande TechnicalProfiles och OrchestrationSteps om det inte behövs.

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Del 4 – konfigurera principen för Azure AD B2C

Se det här [dokumentet](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) för instruktioner om hur du konfigurerar Azure AD B2C-klienten och konfigurerar principer.

>[!NOTE]
> Som bästa praxis rekommenderar vi att kunderna lägger till medgivande meddelanden på sidan samling av attribut. Meddela användarna att informationen kommer att skickas till tjänster från tredje part för identitets verifiering.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten och under principer väljer du **identitets miljö ramverk**.

2. Välj din tidigare skapade **SignUpSignIn**.

3. Välj **Kör användar flöde** och välj inställningarna:

   a. **Program**: Välj den registrerade appen (EXEMPLET är JWT)

   b. **Svars-URL**: Välj **omdirigerings-URL**

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto

5. Onfido-tjänsten anropas under flödet efter att användarattribut har skapats. Om flödet är ofullständigt, kontrollerar du att användaren inte har sparats i katalogen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
