---
title: 'Självstudie för att konfigurera Azure Active Directory B2C med Experian:'
titleSuffix: Azure AD B2C
description: 'Lär dig hur du integrerar Azure AD B2C-autentisering med Experian: för identifierings verifiering och språk kontroll baserat på användarattribut för att förhindra bedrägerier.'
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 2058e5362a65b6cd5f3e5b7cb9c20ce32d020d30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928706"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera Experian: med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar Azure AD B2C med [Experian:](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration). Experian: tillhandahåller en mängd olika lösningar, som du hittar [här](https://www.experian.com/).

I det här exemplet används Experian:-integrerade digitala identiteter och **CrossCore** för bedrägeri risk plattform. CrossCore är en ID-verifierings tjänst som används för att verifiera användar identifiering. Den riskerar att analyseras baserat på flera delar av informationen som tillhandahålls av användaren under registrerings flödet. CrossCore används för att avgöra om användaren ska kunna fortsätta att logga in eller inte. Följande attribut kan användas i CrossCore risk analys:

- E-post
- IP-adress
- Förnamn
- Mellannamn
- Efternamn
- Gatuadress
- City
- Region
- Postnummer
- Land/region
- Telefonnummer

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- [En Azure AD B2C klient](./tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

Experian:-integreringen innehåller följande komponenter:

- Azure AD B2C – auktoriseringsservern som ansvarar för att verifiera användarens autentiseringsuppgifter, även kallat identitets leverantören

- Experian: – Experian:-tjänsten tar indata från användaren och verifierar användarens identitet

- Anpassat REST API – det här API: et implementerar integreringen mellan Azure AD B2C och Experian:-tjänsten.

I följande arkitektur diagram visas implementeringen.

![skärm bild för Experian:-Architecture-diagram](media/partner-experian/experian-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | Användaren kommer till inloggnings sidan. Användaren väljer att registrera sig för att skapa ett nytt konto och ange information på sidan. Azure AD B2C samlar in användarattribut.
| 2. | Azure AD B2C anropar API: t för mellanlager och passerar användar-attributen.
| 3. | API för mellanlager samlar in användarattribut och omvandlar dem till ett format som Experian:-API: et kan förbruka. Skickar det sedan till Experian:.
| 4. | Experian: förbrukar informationen och bearbetar den för att verifiera användar identifiering baserat på risk analysen. Sedan returnerar den resultatet till API: t mellan lagret.
| 5. | API för mellan lager bearbetar informationen och skickar tillbaka relevant information i rätt JSON-format till Azure AD B2C.
| 6. | Azure AD B2C tar emot information tillbaka från API för mellanliggande lager. Om det visar ett felsvar visas ett fel meddelande för användaren. Om det visar sig ett lyckat svar, autentiseras och skrivs användaren till katalogen.

## <a name="onboard-with-experian"></a>Publicera med Experian:

1. Kontakta [Experian:](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration) om du vill skapa ett Experian:-konto

2. När ett konto har skapats får du den information du behöver för API-konfiguration. I följande avsnitt beskrivs processen.

## <a name="configure-azure-ad-b2c-with-experian"></a>Konfigurera Azure AD B2C med Experian:

### <a name="part-1---deploy-the-api"></a>Del 1 – distribuera API: et

Distribuera den tillhandahållna [API-koden](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) till en Azure-tjänst. Du kan publicera koden från Visual Studio genom att följa dessa [anvisningar](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Du behöver URL: en för den distribuerade tjänsten för att konfigurera Azure AD med nödvändiga inställningar.

### <a name="part-2---deploy-the-client-certificate"></a>Del 2 – distribuera klient certifikatet

Experian:-API-anropet skyddas av ett klient certifikat. Det här klient certifikatet kommer att tillhandahållas av Experian:. Efter de instruktioner som anges i det här [dokumentet](../app-service/environment/certificates.md#private-client-certificate)måste certifikatet överföras till Azure App-tjänsten. Exempel principen använder dessa nycklar steg i processen:

- Ladda upp certifikatet

- Ange `WEBSITE_LOAD_ROOT_CERTIFICATES` nyckeln med tumavtrycket för certifikatet.

### <a name="part-3---configure-the-api"></a>Del 3 – Konfigurera API: et

Program inställningar kan [konfigureras i App Service i Azure](../app-service/configure-common.md#configure-app-settings). Med den här metoden kan du konfigurera inställningar på ett säkert sätt utan att kontrol lera dem i en lagrings plats. Du måste ange följande inställningar för REST-API: et:

| Programinställningar | Källa | Kommentarer |
| :-------- | :------------| :-----------|
|CrossCoreConfig: TenantId | Konfiguration av Experian:-konto |     |
|CrossCoreConfig:OrgCode | Konfiguration av Experian:-konto |     |
|CrossCore:ApiEndpoint |Konfiguration av Experian:-konto|  |
|CrossCore:ClientReference | Konfiguration av Experian:-konto | |
| CrossCore:ModelCode |Konfiguration av Experian:-konto|
| CrossCore:OrgCode | Konfiguration av Experian:-konto |
| CrossCore:SignatureKey  | Konfiguration av Experian:-konto |
| CrossCore: TenantId  | Konfiguration av Experian:-konto |
| CrossCore: CertificateThumbprint | Experian:-certifikat |
| BasicAuth:ApiUsername | Definiera ett användar namn för API: et | Används i ExtId-konfigurationen |
| BasicAuth:ApiPassword | Definiera ett lösen ord för API: et | Används i ExtId-konfigurationen

### <a name="part-4---create-api-policy-keys"></a>Del 4 – Skapa API-principinställningar

Referera till det här [dokumentet](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) och skapa två princip nycklar – ett för API-användarnamnet och ett för API-lösenordet som du definierade ovan för HTTP Basic-autentisering.

>[!NOTE]
>Du behöver nycklar för att konfigurera principerna senare.

### <a name="part-5---replace-the-configuration-values"></a>Del 5 – Ersätt konfigurations värdena

I de angivna [anpassade principerna](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)söker du efter följande plats hållare och ersätter med motsvarande värden från din instans

|                      Platshållare                       |                                   Ersätt med värde                                 |                   Exempel                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | Ditt klient kort namn                                                           | "yourtenant" från yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | Azure AD B2C namnet på din TrustFrameworkBase-princip                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | App-ID för IdentityExperienceFramework-appen som kon figurer ATS i Azure AD B2C-klienten      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | App-ID för ProxyIdentityExperienceFramework-appen som kon figurer ATS i Azure AD B2C-klienten | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_appid}                         | App-ID för klientens lagrings program                                      | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_tenant_extensions_app_objectid}                  | Objekt-ID för klientens lagrings program                                   | 01234567-89ab-cdef-0123-456789abcdef         |
| {your_api_username_key_name}                           | Namnet på användar namn nyckeln som du skapade [här](#part-4---create-api-policy-keys)             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | Namnet på lösen ords nyckeln som du skapade [här](#part-4---create-api-policy-keys)             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | URL för den app service som du har konfigurerat                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Del 6 – konfigurera principen för Azure AD B2C

Se det här [dokumentet](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) för instruktioner om hur du konfigurerar Azure AD B2C-klienten och konfigurerar principer.

>[!NOTE]
>Den här exempel principen baseras på [lokala konton start paket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

>[!NOTE]
> Som bästa praxis rekommenderar vi att kunderna lägger till medgivande meddelanden på sidan samling av attribut. Meddela användarna att informationen kommer att skickas till tjänster från tredje part för identitets verifiering.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten och under principer väljer du **användar flöden**.

2. Välj ditt tidigare skapade **användar flöde**.

3. Välj **Kör användar flöde** och välj inställningarna:

   a. **Program**: Välj den registrerade appen (EXEMPLET är JWT)

   b. **Svars-URL**: Välj **omdirigerings-URL**

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto

5. Logga ut

6. Gå igenom inloggnings flödet  

7. CrossCore pussl visas när du har angett **Fortsätt**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
