---
title: Självstudie för att konfigurera Azure Active Directory B2C med LexisNexis
titleSuffix: Azure AD B2C
description: Lär dig att integrera Azure AD B2C-autentisering med LexisNexis som är en profilerings-och identitets validerings tjänst och som används för att verifiera användar identifiering och tillhandahålla omfattande riskbedömningar baserade på användarens enhet.
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 5f408699cae9580188a3780fc8f8654eaa97c26b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97108441"
---
# <a name="tutorial-for-configuring-lexisnexis-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera LexisNexis med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar Azure AD B2C med [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). LexisNexis tillhandahåller en mängd olika lösningar. du hittar dem [här](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd). I den här självstudien ska vi gå igenom **ThreatMetrix** -lösningen från LexisNexis. ThreatMetrix är en profilerings-och identitets validerings tjänst. Den används för att verifiera användar identifiering och tillhandahålla omfattande riskbedömningar baserat på användarens enhet.

Den här integrationen tillhandahåller profilering baserat på några få delar av användar information, som tillhandahålls av användaren under registrerings flödet. ThreatMetrix bestämmer om användaren ska tillåtas att fortsätta logga in eller inte. Följande attribut beaktas i ThreatMetrix risk analys:

- E-post
- Telefonnummer
- Profilering av information som samlas in från användarens dator

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- [En Azure AD B2C klient](./tutorial-create-tenant.md) som är länkad till din Azure-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

ThreatMetrix-integreringen innehåller följande komponenter:

- Azure AD B2C – auktoriseringsservern som ansvarar för att verifiera användarens autentiseringsuppgifter, även kallat identitets leverantören

- ThreatMetrix – ThreatMetrix-tjänsten tar indata från användaren och kombinerar den med profilerings information som samlats in från användarens dator för att kontrol lera säkerheten för användar interaktionen.

- Anpassat REST API – det här API: et implementerar integreringen mellan Azure AD B2C och ThreatMetrix-tjänsten.

I följande arkitektur diagram visas implementeringen.

![skärm bild för LexisNexis-Architecture-diagram](media/partner-lexisnexis/lexisnexis-architecture-diagram.png)

|Steg | Beskrivning |
|:--------------|:-------------|
|1. | Användaren kommer till inloggnings sidan. Användaren väljer att registrera sig för att skapa ett nytt konto och ange information på sidan. Azure AD B2C samlar in användarattribut.
| 2. | Azure AD B2C anropar API: t för mellanlager och passerar användar-attributen.
| 3. | API för mellanlager samlar in användarattribut och omvandlar dem till ett format som LexisNexis-API: et kan förbruka. Skickar det sedan till LexisNexis.  
| 4. | LexisNexis förbrukar informationen och bearbetar den för att verifiera användar identifiering baserat på risk analysen. Sedan returnerar den resultatet till API: t mellan lagret.
| 5. | API för mellan lager bearbetar informationen och skickar tillbaka relevant information till Azure AD B2C.
| 6. | Azure AD B2C tar emot information tillbaka från API för mellanliggande lager. Om det visar ett felsvar visas ett fel meddelande för användaren. Om ett lyckat svar visas, autentiseras användaren och beviljas åtkomst.

## <a name="onboard-with-lexisnexis"></a>Publicera med LexisNexis

1. Kontakta [LexisNexis](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd) om du vill skapa ett LexisNexis-konto

2. Skapa en LexisNexis-princip som uppfyller dina krav. Använd dokumentationen som finns [här](https://risk.lexisnexis.com/products/threatmetrix/?utm_source=bingads&utm_medium=ppc&utm_campaign=SEM%7CLNRS%7CUS%7CEN%7CTMX%7CBR%7CBing&utm_term=threat%20metrix&utm_network=o&utm_device=c&msclkid=1e85e32ec18c1ae9bbc1bc2998e026bd).

>[!NOTE]
> Namnet på principen kommer att användas senare.

När ett konto har skapats får du den information du behöver för API-konfiguration. I följande avsnitt beskrivs processen.

## <a name="configure-azure-ad-b2c-with-lexisnexis"></a>Konfigurera Azure AD B2C med LexisNexis

### <a name="part-1---deploy-the-api"></a>Del 1 – distribuera API: et

Distribuera den tillhandahållna [API-koden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/Api) till en Azure-tjänst. Du kan publicera koden från Visual Studio genom att följa dessa [anvisningar](/visualstudio/deployment/quickstart-deploy-to-azure).

>[!NOTE]
>Du behöver URL: en för den distribuerade tjänsten för att konfigurera Azure AD med nödvändiga inställningar.

### <a name="part-2---configure-the-api"></a>Del 2 – Konfigurera API: et

Program inställningar kan [konfigureras i App Service i Azure](../app-service/configure-common.md#configure-app-settings).  Med den här metoden kan du konfigurera inställningar på ett säkert sätt utan att kontrol lera dem i en lagrings plats. Du måste ange följande inställningar för REST-API: et:

| Programinställningar | Källa | Kommentarer |
| :-------- | :------------| :-----------|
|ThreatMetrix: URL | Konfiguration av ThreatMetrix-konto |     |
|ThreatMetrix: OrgId | Konfiguration av ThreatMetrix-konto |     |
|ThreatMetrix: ApiKey |Konfiguration av ThreatMetrix-konto|  |
|ThreatMetrix: princip | Princip namn som skapats i ThreatMetrix | |
| BasicAuth:ApiUsername |Definiera ett användar namn för API: et| Användar namnet kommer att användas i Azure AD B2C-konfigurationen
| BasicAuth:ApiPassword | Definiera ett lösen ord för API: et | Lösen ord kommer att användas i Azure AD B2C-konfigurationen

### <a name="part-3---deploy-the-ui"></a>Del 3 – distribuera användar gränssnittet

Den här lösningen använder anpassade UI-mallar som läses in av Azure AD B2C. Dessa UI-mallar utför profilering som skickas direkt till ThreatMetrix-tjänsten.

Se de här [anvisningarna](./customize-ui-with-html.md#custom-page-content-walkthrough) för att distribuera de inkluderade [UI-filerna](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template) till ett Blob Storage-konto. Anvisningarna är att konfigurera ett Blob Storage-konto, konfigurera CORS och aktivera offentlig åtkomst.

Användar gränssnittet baseras på den [blå mallen för havs](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/ui-template/ocean_blue). Alla länkar i användar gränssnittet bör uppdateras för att referera till den distribuerade platsen. I mappen UI söker du efter och ersätter https://yourblobstorage/blobcontainer med den distribuerade platsen.

### <a name="part-4---create-api-policy-keys"></a>Del 4 – Skapa API-principinställningar

Referera till det här [dokumentet](./secure-rest-api.md#add-rest-api-username-and-password-policy-keys) och skapa två princip nycklar – ett för API-användarnamnet och ett för det API-lösenord som du definierade ovan.

Exempel principen använder följande nyckel namn:

- B2C_1A_RestApiUsername

- B2C_1A_RestApiPassword

### <a name="part-5---update-the-api-url"></a>Del 5 – uppdatera API-URL: en

I den angivna [TrustFrameworkExtensions-principen](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)letar du upp den tekniska profilen med namnet `Rest-LexisNexus-SessionQuery` och uppdaterar `ServiceUrl` Metadataobjektet med platsen för det API som distribuerades ovan.

### <a name="part-6---update-ui-url"></a>Del 6 – uppdatera UI-webbadressen

I den angivna [TrustFrameworkExtensions-principen](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/ThreatMetrix/policy/TrustFrameworkExtensions.xml)gör du en sökning och ersätter för att söka efter https://yourblobstorage/blobcontainer/ den plats som UI-filerna distribueras till.

>[!NOTE]
> Som bästa praxis rekommenderar vi att kunderna lägger till medgivande meddelanden på sidan samling av attribut. Meddela användarna att informationen kommer att skickas till tjänster från tredje part för identitets verifiering.

### <a name="part-7---configure-the-azure-ad-b2c-policy"></a>Del 7 – konfigurera principen för Azure AD B2C

Se det här [dokumentet](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) för att hämta [Start paket för lokala konton](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) och konfigurera [principen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/ThreatMetrix/policy) för Azure AD B2C klient.

>[!NOTE]
>Uppdatera de angivna principerna så att de relaterar till din angivna klient.

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

7. ThreatMetrix pussl visas när du har angett **Fortsätt**.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](./custom-policy-overview.md)

- [Kom igång med anpassade principer i Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
