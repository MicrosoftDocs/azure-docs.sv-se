---
title: Postman FHIR-server i Azure – Azure API för FHIR
description: 'I den här självstudien får vi gå igenom de steg som krävs för att använda Postman för att få åtkomst till en FHIR-Server. Postman är användbar för fel sökning av program som har åtkomst till API: er.'
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/01/2021
ms.openlocfilehash: aa0b18b701c573d4b2542359cb45b2d7694e78bd
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103019530"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Få åtkomst till Azure API för FHIR med Postman

Ett klient program har åtkomst till ett FHIR-API via en [REST API](https://www.hl7.org/fhir/http.html). Du kanske också vill interagera direkt med FHIR-servern när du skapar program, till exempel för fel sökning. I den här självstudien får vi gå igenom de steg som krävs för att använda [Postman](https://www.getpostman.com/) för att få åtkomst till en FHIR-Server. Postman är ett verktyg som ofta används för fel sökning när du skapar program som har åtkomst till API: er.

## <a name="prerequisites"></a>Förutsättningar

- En FHIR-slutpunkt i Azure. Du kan ställa in detta med hjälp av det hanterade Azure-API: t för FHIR eller FHIR-servern med öppen källkod för Azure. Konfigurera det hanterade Azure-API: t för FHIR med hjälp av [Azure Portal](fhir-paas-portal-quickstart.md), [POWERSHELL](fhir-paas-powershell-quickstart.md)eller [Azure CLI](fhir-paas-cli-quickstart.md).
- Ett  [klient program](register-confidential-azure-ad-client-app.md) som du ska använda för att få åtkomst till FHIR-tjänsten.
- Du har beviljat behörigheter, till exempel "FHIR data Contributor" till klient programmet för att få åtkomst till FHIR-tjänsten. Mer information finns i [Konfigurera Azure RBAC för FHIR](configure-azure-rbac.md)
- Postman installerat. Du kan hämta den från [https://www.getpostman.com](https://www.getpostman.com)

## <a name="fhir-server-and-authentication-details"></a>Information om FHIR-Server och-autentisering

Följande information behövs för att kunna använda Postman:

- FHIR-serverns URL, till exempel `https://MYACCOUNT.azurehealthcareapis.com`
- Identitets leverantören `Authority` för FHIR-servern, till exempel `https://login.microsoftonline.com/{TENANT-ID}`
- Den konfigurerade `audience` . Detta är vanligt vis URL: en för FHIR-servern, t. ex. `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` eller bara `https://azurehealthcareapis.com` .
- `client_id`(Eller program-ID) för det [klient program](register-confidential-azure-ad-client-app.md) som du ska använda för att få åtkomst till FHIR-tjänsten.
- `client_secret`(Eller program hemligheten) för klient programmet.

Slutligen bör du kontrol lera att `https://www.getpostman.com/oauth2/callback` är en registrerad svars-URL för klient programmet.

## <a name="connect-to-fhir-server"></a>Anslut till FHIR-Server

Använd Postman och gör en `GET` begäran till `https://fhir-server-url/metadata` :

![Funktions sats för Postman-metadata](media/tutorial-postman/postman-metadata.png)

URL: en för metadata för Azure API för FHIR är `https://MYACCOUNT.azurehealthcareapis.com/metadata` . I det här exemplet är URL: en för FHIR-servern `https://fhirdocsmsft.azurewebsites.net` och serverns kapacitets meddelande tillgänglig på `https://fhirdocsmsft.azurewebsites.net/metadata` . Den här slut punkten bör vara tillgänglig utan autentisering.

Om du försöker komma åt begränsade resurser bör du få svaret "autentisering misslyckades":

![Autentiseringen misslyckades](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Hämta en åtkomsttoken

Om du vill hämta en giltig åtkomsttoken väljer du "auktorisering" och väljer typ "OAuth 2,0":

![Ange OAuth 2,0](media/tutorial-postman/postman-select-oauth2.png)

Tryck på "Hämta ny åtkomsttoken" och en dialog ruta visas:

![Begär ny åtkomsttoken](media/tutorial-postman/postman-request-token.png)

Du behöver lite information:

| Fält                 | Exempelvärde                                                                                                   | Kommentar                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Token Name (Tokennamn)            | TOKEn                                                                                                         | Ett namn som du väljer          |
| Grant Type (Typ av beviljande)            | Auktoriseringskod                                                                                              |                            |
| Callback URL (Webbadress för återanrop)          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Auth URL (Auktoriseringswebbadress)              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` är `https://MYACCOUNT.azurehealthcareapis.com` för Azure API för FHIR |
| Access Token URL (Webbadress för åtkomsttoken)      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| Klient-ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | Program-ID             |
| Client Secret (Klienthemlighet)         | `XXXXXXXX`                                                                                                        | Hemlig klient nyckel          |
| Omfång | `<Leave Blank>` |
| Stat                 | `1234`                                                                                                            |                            |
| Klientautentisering | Skicka klientautentiseringsuppgifter i brödtext                                                                                 |                 

Besök-token för begäran och du kommer att guidas genom Azure Active Directory-autentiseringsschemat och en token returneras till Postman. Om du stöter på problem öppnar du Postman-konsolen (från meny alternativet "View->Visa Postman-konsolen").

Rulla ned på skärmen returnerad token och tryck på Använd token:

![Använd token](media/tutorial-postman/postman-use-token.png)

Token ska nu vara ifylld i fältet åtkomsttoken och du kan välja tokens från "tillgängliga tokens". Om du "skickar" igen för att upprepa `Patient` resurss ökningen, ska du få en status `200 OK` :

![200 OK](media/tutorial-postman/postman-200-OK.png)

I det här fallet finns det inga patienter i databasen och sökningen är tom.

Om du inspekterar åtkomsttoken med ett verktyg som [https://jwt.ms](https://jwt.ms) , bör du se innehåll som:

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

I fel söknings situationer kontrollerar du att du har rätt mål grupp ( `aud` anspråk) och är en bra plats att starta. Om din token kommer från rätt utfärdare ( `iss` anspråk) och har rätt mål grupp ( `aud` anspråk), men du fortfarande inte har åtkomst till FHIR-API: et, är det troligt att användaren eller tjänstens huvud namn ( `oid` anspråk) inte har åtkomst till FHIR-dataplanen. Vi rekommenderar att du [använder rollbaserad åtkomst kontroll i Azure (Azure RBAC)](configure-azure-rbac.md) för att tilldela användare data plan roller. Om du använder en extern, sekundär Azure Active Directory-klient för ditt data plan måste du [Konfigurera lokala RBAC-tilldelningar](configure-local-rbac.md).

Det är också möjligt att [Hämta en token för Azure API för FHIR med hjälp av Azure CLI](get-healthcare-apis-access-token-cli.md). Om du använder en token som hämtats med Azure CLI bör du använda autentiseringstypen "Bearer token" och klistra in token direkt.

## <a name="inserting-a-patient"></a>Lägga till en patient

Nu när du har en giltig åtkomsttoken. Du kan infoga en ny patient. Växla till metod "POST" och Lägg till följande JSON-dokument i bröd texten i begäran:

[!code-json[](../samples/sample-patient.json)]

Tryck på "Skicka" så bör du se att patienten har skapats:

![Skärm bild som visar att patienten har skapats.](media/tutorial-postman/postman-patient-created.png)

Om du upprepar patients ökningen bör du nu se patient posten:

![Patient skapad](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du till gång till en FHIR-API med Postman. Läs om de API-funktioner som stöds i avsnittet funktioner som stöds.
 
>[!div class="nextstepaction"]
>[Funktioner som stöds](fhir-features-supported.md)