---
title: Lägg till API-kopplingar till användar flöden (förhands granskning)
description: Konfigurera en API-anslutning som ska användas i ett användar flöde.
services: active-directory-b2c
ms.service: active-directory
ms.subservice: B2C
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.openlocfilehash: 59246c3739ad4de27e65641cc9d2154b33a6ee5e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008441"
---
# <a name="add-an-api-connector-to-a-sign-up-user-flow-preview"></a>Lägga till en API-anslutning till ett användar flöde för registrering (för hands version)

> [!IMPORTANT]
> API-kopplingar för registrering är en allmän förhands gransknings funktion i Azure AD B2C. Mer information om för hands versionerna finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

Om du vill använda en [API-anslutning](api-connectors-overview.md)skapar du först API-anslutningen och aktiverar den sedan i ett användar flöde.

## <a name="create-an-api-connector"></a>Skapa en API-anslutning

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Under **Azure-tjänster** väljer du **Azure AD B2C**.
4. Välj **API-kopplingar (förhands granskning)** och välj sedan **ny API-anslutning**.

   ![Lägg till en ny API-anslutning](./media/add-api-connector/api-connector-new.png)

5. Ange ett visnings namn för anropet. Verifiera till exempel **användar information**.
6. Ange **slut punkts-URL** för API-anropet.
7. Välj **Autentiseringstyp** och konfigurera Autentiseringsinformationen för att anropa ditt API. Se avsnittet nedan för alternativ för att skydda ditt API.

    ![Konfigurera en API-anslutning](./media/add-api-connector/api-connector-config.png)

8. Välj **Spara**.

## <a name="securing-the-api-endpoint"></a>Skydda API-slutpunkten
Du kan skydda din API-slutpunkt genom att använda antingen HTTP Basic-autentisering eller HTTPS-autentisering av klient certifikat (för hands version). I båda fallen anger du de autentiseringsuppgifter som Azure AD B2C ska använda när du anropar API-slutpunkten. API-slutpunkten kontrollerar sedan autentiseringsuppgifterna och genomför auktoriseringsbeslut.

### <a name="http-basic-authentication"></a>Grundläggande HTTP-autentisering
Grundläggande HTTP-autentisering definieras i [RFC 2617](https://tools.ietf.org/html/rfc2617). Azure AD B2C skickar en HTTP-begäran med klientautentiseringsuppgifterna ( `username` och `password` ) i `Authorization` huvudet. Autentiseringsuppgifterna formateras som Base64-kodad sträng `username:password` . Ditt API kontrollerar sedan dessa värden för att avgöra om ett API-anrop eller inte ska avvisas.

### <a name="https-client-certificate-authentication-preview"></a>HTTPS-autentisering av klient certifikat (för hands version)

> [!IMPORTANT]
> Den här funktionen är en för hands version och tillhandahålls utan ett service nivå avtal. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Autentisering av klient certifikat är en ömsesidigt certifikatbaserad autentisering, där klienten tillhandahåller ett klient certifikat för servern för att bevisa sin identitet. I det här fallet kommer Azure AD B2C att använda det certifikat som du överför som en del av API-kopplingens konfiguration. Detta inträffar som en del av SSL-handskakningen. Endast tjänster som har rätt certifikat kan komma åt din REST API-tjänst. Klient certifikatet är ett digitalt X. 509-certifikat. I produktions miljöer bör den signeras av en certifikat utfärdare. 


Om du vill skapa ett certifikat kan du använda [Azure Key Vault](../key-vault/certificates/create-certificate.md), som innehåller alternativ för självsignerade certifikat och integreringar med certifikat utfärdare för signerade certifikat. Sedan kan du [Exportera certifikatet](../key-vault/certificates/how-to-export-certificate.md) och ladda upp det för användning i API Connectors-konfigurationen. Observera att lösen ord krävs endast för certifikatfiler som skyddas av ett lösen ord. Du kan också använda PowerShell [: s New-SelfSignedCertificate-cmdlet](./secure-rest-api.md#prepare-a-self-signed-certificate-optional) för att skapa ett självsignerat certifikat.

Azure App Service och Azure Functions finns i [Konfigurera ömsesidig TLS-autentisering](../app-service/app-service-web-configure-tls-mutual-auth.md) för att lära dig hur du aktiverar och validerar certifikatet från API-slutpunkten.

Vi rekommenderar att du ställer in påminnelse aviseringar när certifikatet upphör att gälla. Om du vill överföra ett nytt certifikat till en befintlig API-anslutning väljer du API-anslutaren under **API-kopplingar (för hands version)** och klickar på **överför nytt certifikat**. Det senast överförda certifikatet som inte har upphört att gälla och som har passerat start datumet används automatiskt av Azure AD B2C.

### <a name="api-key"></a>API-nyckel
Vissa tjänster använder en API-nyckel för att obfuscate åtkomst till dina HTTP-slutpunkter under utvecklingen. För [Azure Functions](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)kan du göra detta genom att inkludera `code` parametern som en frågeparameter i **slut punkts-URL:** en. Till exempel `https://contoso.azurewebsites.net/api/endpoint` <b>`?code=0123456789`</b> ). 

Detta är inte en mekanism som endast ska användas i produktion. Därför krävs alltid konfiguration för Basic-eller certifikatautentisering. Om du inte vill implementera någon autentiseringsmetod (rekommenderas inte) i utvecklings syfte kan du välja grundläggande autentisering och använda tillfälliga värden för `username` och `password` att ditt API kan ignorera när du implementerar auktoriseringen i ditt API.

## <a name="the-request-sent-to-your-api"></a>Begäran skickades till ditt API
En API-anslutning materialiseras som en **http post** -begäran och skickar användarattribut ("anspråk") som nyckel/värde-par i en JSON-text. Attributen serialiseras på samma sätt som [Microsoft Graph](/graph/api/resources/user#properties) användar egenskaper. 

**Exempelbegäran**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Endast användar egenskaper och anpassade attribut i listan **Azure AD B2C**  >  **användarattribut** är tillgängliga att skickas i begäran.

Anpassade attribut finns i **extension_ \<extensions-app-id> _CustomAttributes**  formatet i katalogen. Ditt API bör förvänta sig att ta emot anspråk i samma serialiserade format. Mer information om anpassade attribut finns [i Definiera anpassade attribut i Azure AD B2C](user-flow-custom-attributes.md).

Dessutom skickas anspråk som är **lokala för användar gränssnittet (ui_locales)** som standard i alla begär Anden. Den innehåller en användares språkvariant (er) som kon figurer ATS på deras enhet som kan användas av API: et för att returnera internationella svar.

> [!IMPORTANT]
> Om ett anspråk inte har något värde när API-slutpunkten anropas skickas inte anspråket till API: et. Ditt API bör vara utformat för att explicit kontrol lera och hantera det fall där ett anspråk inte finns i begäran.

> [!TIP] 
> [**identiteter (' Identities ')**](/graph/api/resources/objectidentity) och e- **postadressen (e-post)** -anspråk kan användas av ditt API för att identifiera en användare innan de har ett konto i din klient organisation. 

## <a name="enable-the-api-connector-in-a-user-flow"></a>Aktivera API-anslutningen i ett användar flöde

Följ dessa steg om du vill lägga till en API-anslutning till ett registrerings användar flöde.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Under **Azure-tjänster** väljer du **Azure AD B2C**.
4. Välj **användar flöden** och välj sedan det användar flöde som du vill lägga till API-kopplingen till.
5. Välj **API-kopplingar** och välj sedan de API-slutpunkter som du vill anropa i följande steg i användar flödet:

   - **När du har loggat in med en identitets leverantör**
   - **Innan du skapar användaren**

   ![Lägga till API: er i användar flödet](./media/add-api-connector/api-connectors-user-flow-select.png)

6. Välj **Spara**.

## <a name="after-signing-in-with-an-identity-provider"></a>När du har loggat in med en identitets leverantör

En API-anslutning i det här steget i registrerings processen anropas omedelbart när användaren autentiseras med en identitets leverantör (som Google, Facebook, & Azure AD). Det här steget föregår ***sidan för attribut samling***, som är det formulär som visas för användaren att samla in användarattribut. Det här steget anropas inte om en användare registreras med ett lokalt konto.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exempel förfrågan skickades till API: et i det här steget
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

De exakta anspråk som skickas till API: n beror på vilken information som identitets leverantören tillhandahåller. "e-post" skickas alltid.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Förväntade svars typer från webb-API: et i det här steget

När webb-API: et tar emot en HTTP-förfrågan från Azure AD under ett användar flöde kan det returnera följande svar:

- Fortsättnings svar
- Blockerar svar

#### <a name="continuation-response"></a>Fortsättnings svar

Ett fortsättnings svar visar att användar flödet bör fortsätta till nästa steg: sidan för attribut samling.

I ett fortsättnings svar kan API: t returnera anspråk. Om ett anspråk returneras av API: t gör kravet följande:

- Fyller i inmatade fält på sidan för attribut samling.

Se ett exempel på ett [fortsättnings svar](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Blockerar svar

Ett blockerande svar avslutar användar flödet. Det kan användas av API: et för att stoppa användar flödet genom att visa en blockera-sida för användaren. Sidan blockera visar den `userMessage` som tillhandahålls av API: et.

Se ett exempel på ett [blockerande svar](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Innan du skapar användaren

En API-anslutning i det här steget i registrerings processen anropas efter sidan Attribute Collection, om en sådan ingår. Det här steget anropas alltid innan ett användar konto skapas.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exempel förfrågan skickades till API: et i det här steget

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
De exakta anspråk som skickas till API: n beror på vilken information som samlas in från användaren eller tillhandahålls av identitets leverantören.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Förväntade svars typer från webb-API: et i det här steget

När webb-API: et tar emot en HTTP-förfrågan från Azure AD under ett användar flöde kan det returnera följande svar:

- Fortsättnings svar
- Blockerar svar
- Verifierings svar

#### <a name="continuation-response"></a>Fortsättnings svar

Ett fortsättnings svar visar att användar flödet bör fortsätta till nästa steg: skapa användaren i katalogen.

I ett fortsättnings svar kan API: t returnera anspråk. Om ett anspråk returneras av API: t gör kravet följande:

- Åsidosätter alla värden som redan har tilldelats till anspråket från sidan för attribut samling.

Se ett exempel på ett [fortsättnings svar](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Blockerar svar
Ett blockerande svar avslutar användar flödet. Det kan användas av API: et för att stoppa användar flödet genom att visa en blockera-sida för användaren. Sidan blockera visar den `userMessage` som tillhandahålls av API: et.

Se ett exempel på ett [blockerande svar](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Verifiering-fel svar
 När API: et svarar med ett verifierings fel svar finns användar flödet kvar på sidan för attribut samling och `userMessage` visas för användaren. Användaren kan sedan redigera och skicka formuläret igen. Den här typen av svar kan användas för verifiering av indatamängden.

Se ett exempel på ett [verifierings fel svar](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Exempel svar

### <a name="example-of-a-continuation-response"></a>Exempel på ett fortsättnings svar

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parameter                                          | Typ              | Obligatorisk | Beskrivning                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| åtgärd                                             | Sträng            | Ja      | Värdet måste vara `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Inga       | Returnerade värden kan skriva över värden som samlas in från en användare. De kan också returneras i token om de väljs som ett **program anspråk**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Inga       | Anspråket behöver inte innehålla något `_<extensions-app-id>_` . Returnerade värden kan skriva över värden som samlas in från en användare. De kan också returneras i token om de väljs som ett **program anspråk**.  |

### <a name="example-of-a-blocking-response"></a>Exempel på ett blockerande svar

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parameter   | Typ   | Obligatorisk | Beskrivning                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | Sträng | Ja      | API-versionen.                                                    |
| åtgärd      | Sträng | Ja      | Värdet måste vara `ShowBlockPage`                                              |
| userMessage | Sträng | Ja      | Meddelande som ska visas för användaren.                                            |

**Slut användar upplevelse med ett blockerande svar**

![Exempel på block sida](./media/add-api-connector/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Exempel på ett verifierings fel svar

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code."
}
```

| Parameter   | Typ    | Obligatorisk | Beskrivning                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | Sträng  | Ja      | Versionen av ditt API.                                                    |
| åtgärd      | Sträng  | Ja      | Värdet måste vara `ValidationError` .                                           |
| status      | Integer | Ja      | Måste vara `400` ett värde för ett ValidationError-svar.                        |
| userMessage | Sträng  | Ja      | Meddelande som ska visas för användaren.                                            |

> [!NOTE]
> HTTP-statuskoden måste vara "400" Förutom värdet "status" i bröd texten i svaret.

**Slut användar upplevelse med ett verifierings fel svar**

![Exempel på validerings sida](./media/add-api-connector/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Metod tips och fel sökning

### <a name="using-serverless-cloud-functions"></a>Använda Server lös moln funktioner
Funktioner utan server, t. ex. HTTP-utlösare i Azure Functions, ger ett enkelt sätt att skapa API-slutpunkter att använda med API-anslutningen. Du kan använda funktionen för Server lös molnet till [exempel](code-samples.md#api-connectors)utföra validerings logik och begränsa registreringen till vissa e-postdomäner. Moln funktionen utan server kan också anropa och anropa andra webb-API: er, användar lager och andra moln tjänster för mer komplexa scenarier.

### <a name="best-practices"></a>Bästa praxis
Se till att:
* Ditt API följer API-begäran och svars avtalen enligt beskrivningen ovan. 
* **Slut punkts-URL: en** för API-anslutningen pekar på rätt API-slutpunkt.
* Ditt API söker uttryckligen efter null-värden för mottagna anspråk.
* Ditt API svarar så snabbt som möjligt för att säkerställa en flytande användar upplevelse.
    * Om du använder en server lös funktion eller en skalbar webb tjänst använder du en värd plan som behåller API: t "vakna" eller "varm". i produktion. För Azure Functions rekommenderar vi att du använder [Premium-planen](../azure-functions/functions-scale.md)
 

### <a name="use-logging"></a>Använd loggning
I allmänhet är det bra att använda de loggnings verktyg som har Aktiver ATS av webb-API-tjänsten, som [Application Insights](../azure-functions/functions-monitoring.md), för att övervaka ditt API för oväntade felkoder, undantag och dåliga prestanda.
* Övervaka HTTP-statuskod som inte är HTTP 200 eller 400.
* En HTTP-statuskod på 401 eller 403 indikerar vanligt vis att det är ett problem med autentiseringen. Dubbel kontrol lera ditt API: s Authentication Layer och motsvarande konfiguration i API-anslutningen.
* Använd mer aggressiva loggnings nivåer (t. ex. "spårning" eller "Felsök") i utvecklingen vid behov.
* Övervaka ditt API för långa svars tider.

## <a name="next-steps"></a>Nästa steg
- Kom igång med våra [exempel](code-samples.md#api-connectors).
