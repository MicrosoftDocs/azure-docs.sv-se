---
title: IDology-integrering med Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Lär dig hur du integrerar en exempel-IDology online i Azure AD B2C med. IDology är en identitets verifiering och en kontroll leverantör med flera lösningar.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2b6001932b6f53a60eda76b6136611a10011391a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928689"
---
# <a name="tutorial-for-configuring-idology-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera IDology med Azure Active Directory B2C 

I den här självstudien ger vi vägledning om hur du integrerar Azure AD B2C med [IDology](https://www.idology.com/solutions/). IDology är en identitets verifiering och en kontroll leverantör med flera lösningar. I det här exemplet kommer vi att behandla ExpectID-lösningen från IDology.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* [En Azure AD B2C klient](tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

IDology-integreringen innehåller följande komponenter:

- Azure AD B2C – den auktoriserade server som ansvarar för att verifiera användarens autentiseringsuppgifter. Det kallas även för identitets leverantören.
- IDology – IDology-tjänsten tar emot indata från användaren och verifierar användarens identitet.
- Anpassat REST API – det här API: et implementerar integreringen mellan Azure AD och IDology-tjänsten.

I följande arkitektur diagram visas implementeringen.

![Diagram över IDology-arkitektur](media/partner-idology/idology-architecture-diagram.png)

| Steg | Beskrivning |
|------|------|
|1     | En användare kommer till inloggnings sidan. |
|2     | Användaren väljer alternativet registrera för att skapa ett nytt konto och anger information på sidan. Azure AD B2C samlar in användarattribut. |
|3     | Azure AD B2C anropar API: t för mellanlager och passerar användar-attributen. |
|4     | API för mellanlager samlar in användarattribut och omvandlar dem till ett format som IDOlogy-API: n kan använda. Sedan skickar den informationen till IDology. |
|5     | IDology förbrukar informationen och bearbetar den, och returnerar sedan resultatet till API: t mellan lager. |
|6     | API: n för mellanlager bearbetar informationen och skickar relevant information tillbaka till Azure AD B2C. |
|7     | Azure AD B2C tar emot information tillbaka från API för mellanliggande lager. Om det visar ett **felsvar visas ett fel** meddelande för användaren. Om det visar sig ett **lyckat** svar, autentiseras och skrivs användaren till katalogen. |
|      |      |

> [!NOTE]
> Azure AD B2C kan också be kunden utföra stegvis autentisering, men det här scenariot omfattas inte av den här självstudien.

## <a name="onboard-with-idology"></a>Publicera med IDology

1. IDology tillhandahåller en mängd olika lösningar, som du hittar [här](https://www.idology.com/solutions/). För det här exemplet använder vi ExpectID.

2. Kontakta [IDology](https://www.idology.com/request-a-demo/microsoft-integration-signup/)om du vill skapa ett IDology-konto.

3. När ett konto har skapats får du den information du behöver för API-konfiguration. I följande avsnitt beskrivs processen.

## <a name="integrate-with-azure-ad-b2c"></a>Integrera med Azure AD B2C

### <a name="part-1---deploy-the-api"></a>Del 1 – distribuera API: et

Distribuera den tillhandahållna [API-koden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/Api) till en Azure-tjänst. Du kan publicera koden från Visual Studio genom att följa dessa [anvisningar](/visualstudio/deployment/quickstart-deploy-to-azure).

Du behöver URL: en för den distribuerade tjänsten för att konfigurera Azure AD med nödvändiga inställningar.

### <a name="part-2---configure-the-api"></a>Del 2 – Konfigurera API: et 

Program inställningar kan [konfigureras i App Service i Azure](../app-service/configure-common.md#configure-app-settings). Med den här metoden kan du konfigurera inställningar på ett säkert sätt utan att kontrol lera dem i en lagrings plats. Du måste ange följande inställningar för REST-API: et:

| Programinställningar | Källa | Kommentarer |
| :-------- | :------------| :-----------|
|IdologySettings:ApiUsername | Konfiguration av IDology-konto |     |
|IdologySettings:ApiPassword | Konfiguration av IDology-konto |     |
|WebApiSettings:ApiUsername |Definiera ett användar namn för API: et| Används i ExtId-konfigurationen |
|WebApiSettings:ApiPassword | Definiera ett lösen ord för API: et | Används i ExtId-konfigurationen

### <a name="part-3---create-api-policy-keys"></a>Del 3 – skapa API-principinställningar

Följ det här [dokumentet](secure-rest-api.md#add-rest-api-username-and-password-policy-keys) för att skapa två princip nycklar: en för API-användarnamnet och en för API-lösenordet som du definierade ovan.

Exempel principen använder följande nyckel namn:

* B2C_1A_RestApiUsername
* B2C_1A_RestApiPassword

### <a name="part-4---configure-the-azure-ad-b2c-policy"></a>Del 4 – konfigurera principen för Azure AD B2C

1. Följ det här [dokumentet](custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) för att ladda ned [LocalAccounts start paket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) och konfigurera principen för Azure AD B2C klient. Följ anvisningarna tills du är klar med avsnittet **testa den anpassade principen** .

2. Hämta de två exempel principerna [här](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/IDology/policy).

3. Uppdatera de två exempel principerna:

   1. Öppna båda principerna:

      1. I avsnittet `Idology-ExpectId-API` uppdaterar du `ServiceUrl` Metadataobjektet med platsen för det API som distribuerades ovan.

      1. Ersätt `yourtenant` med namnet på din Azure AD B2C-klient.
      Om namnet på din Azure AD B2C klient organisation till exempel  `contosotenant` , ersätter du alla instanser av  `yourtenant.onmicrosoft.com`   med `contosotenant.onmicrosoft.com` .

   1. Öppna filen TrustFrameworkExtensions.xml:

      1. Hitta elementet  `<TechnicalProfile Id="login-NonInteractive">` . Ersätt båda instanserna av  `IdentityExperienceFrameworkAppId`   med program-ID: t för det IdentityExperienceFramework-program som du skapade tidigare.

      1. Ersätt båda instanserna av  `ProxyIdentityExperienceFrameworkAppId`   med program-ID: t för det ProxyIdentityExperienceFramework-program som du skapade tidigare.

4. Ersätt SignInorSignUp.xml och TrustFrameworkExtensions.xml tidigare överförts till Azure AD B2C i steg 1 med de två uppdaterade exempel principerna.

> [!NOTE]
> Som bästa praxis rekommenderar vi att kunderna lägger till medgivande meddelanden på sidan samling av attribut. Meddela användarna att information kommer att skickas till tjänster från tredje part för identitets verifiering.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C klient organisation och välj **användar flöden** under **principer**.

2. Välj ditt tidigare skapade **användar flöde**.

3. Välj **Kör användar flöde** och välj inställningarna:

   1. **Program** – Välj den registrerade appen (EXEMPLET är JWT).

   1. **Svars-URL** – Välj **omdirigerings-URL**: en.

   1. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto.

5. Logga ut.

6. Gå igenom inloggnings flödet.

7. IDology-pusslet visas när du har angett **Fortsätt**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](custom-policy-get-started.md?tabs=applications)
