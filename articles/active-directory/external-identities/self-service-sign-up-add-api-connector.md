---
title: Lägg till API-kopplingar till självbetjänings registrerings flöden – Azure AD
description: Konfigurera ett webb-API som ska användas i ett användar flöde.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26403c20d7f3274e8f3f2dcae479f72e9a7e3354
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807028"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Lägga till en API-anslutning till ett användar flöde

Om du vill använda en [API-anslutning](api-connectors-overview.md)skapar du först API-anslutningen och aktiverar den sedan i ett användar flöde.

> [!IMPORTANT]
>Från och med den **4 januari 2021** är Google [inaktuell WebView-inloggning support](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Om du använder Google Federation eller självbetjänings registrering med Gmail bör du [testa dina verksamhetsbaserade interna program för kompatibilitet](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="create-an-api-connector"></a>Skapa en API-anslutning

1. Logga in till [Azure-portalen](https://portal.azure.com/) som Azure AD-administratör.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **alla API-kopplingar (förhands granskning)** och välj sedan **ny API-anslutning**.

   ![Lägg till en ny API-anslutning](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Ange ett visnings namn för anropet. Du kan till exempel **kontrol lera godkännande statusen**.
6. Ange **slut punkts-URL** för API-anropet.
7. Ange autentiseringsinformation för API: et.

   - Det finns för närvarande endast stöd för grundläggande autentisering. Om du vill använda ett API utan grundläggande autentisering i utvecklings syfte anger du bara ett **användar namn** och **lösen ord** som ditt API kan ignorera. För användning med en Azure-funktion med en API-nyckel kan du inkludera koden som en frågeparameter i **slut punktens URL** (t. ex. `https://contoso.azurewebsites.net/api/endpoint?code=0123456789` ).

   ![Konfigurera en ny API-anslutning](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. Välj **Spara**.

> [!IMPORTANT]
> Tidigare var du tvungen att konfigurera vilka användarattribut som ska skickas till API: et ("anspråk att skicka") och vilka användarattribut som ska accepteras från API: et ("anspråk att ta emot"). Nu skickas alla användarattribut som standard om de har ett värde och det finns ett användar-attribut som kan returneras av API: et i ett fortsättnings svar.

## <a name="the-request-sent-to-your-api"></a>Begäran skickades till ditt API
En API-anslutning materialiseras som en **http post** -begäran och skickar användarattribut ("anspråk") som nyckel/värde-par i en JSON-text. Attributen serialiseras på samma sätt som [Microsoft Graph](/graph/api/resources/user#properties) användar egenskaper. 

**Exempelbegäran**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

Endast användar egenskaper och anpassade attribut som anges i **Azure Active Directory**  >  **externa identiteter** för  >  **anpassade** användarattribut är tillgängliga att skickas i begäran.

Anpassade attribut finns i **extension_ \<extensions-app-id> _AttributeNames**  formatet i katalogen. Ditt API bör förvänta sig att ta emot anspråk i samma serialiserade format. Mer information om anpassade attribut finns i [definiera anpassade attribut för](user-flow-add-custom-attributes.md)självbetjänings registrerings flöden.

Dessutom skickas anspråk som är **lokala för användar gränssnittet (ui_locales)** som standard i alla begär Anden. Den innehåller en användares språkvariant (er) som kon figurer ATS på deras enhet som kan användas av API: et för att returnera internationella svar.

> [!IMPORTANT]
> Om ett anspråk att skicka inte har något värde när API-slutpunkten anropas skickas inte anspråket till API: et. Ditt API bör vara utformat för att uttryckligen söka efter det värde som förväntas.

> [!TIP] 
> [**identiteter (' Identities ')**](/graph/api/resources/objectidentity) och e- **postadressen (e-post)** -anspråk kan användas av ditt API för att identifiera en användare innan de har ett konto i din klient organisation. "Identities"-anspråk skickas när en användare autentiseras med en identitetsprovider som Google eller Facebook. "e-post" skickas alltid.

## <a name="enable-the-api-connector-in-a-user-flow"></a>Aktivera API-anslutningen i ett användar flöde

Följ dessa steg om du vill lägga till en API-anslutning till ett självbetjänings registrerings användar flöde.

1. Logga in till [Azure-portalen](https://portal.azure.com/) som Azure AD-administratör.
2. Under **Azure-tjänster** väljer du **Azure Active Directory**.
3. På den vänstra menyn väljer du **externa identiteter**.
4. Välj **användar flöden (förhands granskning)** och välj sedan det användar flöde som du vill lägga till API-kopplingen till.
5. Välj **API-kopplingar** och välj sedan de API-slutpunkter som du vill anropa i följande steg i användar flödet:

   - **När du har loggat in med en identitets leverantör**
   - **Innan du skapar användaren**

   ![Lägga till API: er i användar flödet](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Välj **Spara**.

## <a name="after-signing-in-with-an-identity-provider"></a>När du har loggat in med en identitets leverantör

En API-anslutning i det här steget i registrerings processen anropas omedelbart när användaren autentiseras med en identitetsprovider (Google, Facebook, Azure AD). Det här steget föregår ***sidan för attribut samling***, som är det formulär som visas för användaren att samla in användarattribut. 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exempel förfrågan skickades till API: et i det här steget
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

En API-anslutning i det här steget i registrerings processen anropas efter sidan Attribute Collection, om en sådan ingår. Det här steget anropas alltid innan ett användar konto skapas i Azure AD. 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>Exempel förfrågan skickades till API: et i det här steget

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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
| version                                            | Sträng            | Ja      | API-versionen.                                                                                                                                                                                                                                                                |
| åtgärd                                             | Sträng            | Ja      | Värdet måste vara `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Inga       | Värden kan lagras i katalogen om de har valts som ett **anspråk att ta emot** i API-anslutningens konfiguration **och användarattribut** för ett användar flöde. Värdena kan returneras i token om de väljs som ett **program anspråk**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Inga       | Det returnerade anspråket behöver inte innehålla `_<extensions-app-id>_` . Värdena lagras i katalogen om de valts som ett **anspråk att ta emot** i API-kopplingens konfiguration och **användarattribut** för ett användar flöde. Det går inte att skicka anpassade attribut tillbaka i token. |

### <a name="example-of-a-blocking-response"></a>Exempel på ett blockerande svar

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| Parameter   | Typ   | Obligatorisk | Beskrivning                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | Sträng | Ja      | API-versionen.                                                    |
| åtgärd      | Sträng | Ja      | Värdet måste vara `ShowBlockPage`                                              |
| userMessage | Sträng | Ja      | Meddelande som ska visas för användaren.                                            |
| kod        | Sträng | Inga       | Felkod. Kan användas för fel söknings syfte. Visas inte för användaren. |

**Slut användar upplevelse med ett blockerande svar**

![Exempel på block sida](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Exempel på ett verifierings fel svar

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| Parameter   | Typ    | Obligatorisk | Beskrivning                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | Sträng  | Ja      | API-versionen.                                                    |
| åtgärd      | Sträng  | Ja      | Värdet måste vara `ValidationError` .                                           |
| status      | Integer | Ja      | Måste vara `400` ett värde för ett ValidationError-svar.                        |
| userMessage | Sträng  | Ja      | Meddelande som ska visas för användaren.                                            |
| kod        | Sträng  | Inga       | Felkod. Kan användas för fel söknings syfte. Visas inte för användaren. |

**Slut användar upplevelse med ett verifierings fel svar**

![Exempel på validerings sida](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Metod tips och fel sökning

### <a name="using-serverless-cloud-functions"></a>Använda Server lös moln funktioner
Funktioner utan server, t. ex. HTTP-utlösare i Azure Functions, ger ett enkelt sätt att skapa API-slutpunkter att använda med API-anslutningen. Du kan använda funktionen för Server lös molnet till [exempel](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)utföra validerings logik och begränsa registreringen till vissa domäner. Moln funktionen utan server kan också anropa och anropa andra webb-API: er, användar lager och andra moln tjänster för mer komplexa scenarier.

### <a name="best-practices"></a>Rekommenderade metoder
Se till att:
* Ditt API följer API-begäran och svars avtalen enligt beskrivningen ovan. 
* **Slut punkts-URL: en** för API-anslutningen pekar på rätt API-slutpunkt.
* Ditt API söker uttryckligen efter null-värden för mottagna anspråk.
* Ditt API svarar så snabbt som möjligt för att säkerställa en flytande användar upplevelse.
    * Om du använder en server lös funktion eller en skalbar webb tjänst använder du en värd plan som behåller API: t "vakna" eller "varm". För Azure Functions rekommenderar vi att du använder [Premium planen](../../azure-functions/functions-premium-plan.md). 


### <a name="use-logging"></a>Använd loggning
I allmänhet är det bra att använda de loggnings verktyg som har Aktiver ATS av webb-API-tjänsten, som [Application Insights](../../azure-functions/functions-monitoring.md), för att övervaka ditt API för oväntade felkoder, undantag och dåliga prestanda.
* Övervaka HTTP-statuskod som inte är HTTP 200 eller 400.
* En HTTP-statuskod på 401 eller 403 indikerar vanligt vis att det är ett problem med autentiseringen. Dubbel kontrol lera ditt API: s Authentication Layer och motsvarande konfiguration i API-anslutningen.
* Använd mer aggressiva loggnings nivåer (t. ex. "spårning" eller "Felsök") i utvecklingen vid behov.
* Övervaka ditt API för långa svars tider.

## <a name="next-steps"></a>Nästa steg
<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- Lär dig hur du [lägger till ett anpassat godkännande arbets flöde till självbetjänings registrering](self-service-sign-up-add-approvals.md)
- Kom igång med våra [snabb starts exempel för Azure Function](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->