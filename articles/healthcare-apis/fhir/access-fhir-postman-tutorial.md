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
ms.date: 03/26/2021
ms.openlocfilehash: 59847f745037acec47415489cdf61d119a7807af
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936282"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Få åtkomst till Azure API för FHIR med Postman

Ett klient program kan komma åt Azure-API: et för FHIR via en [REST API](https://www.hl7.org/fhir/http.html). Om du vill skicka begär Anden, Visa svar och felsöka programmet som det skapas, använder du ett verktyg för API-testning som du väljer. I den här självstudien vägleder vi dig genom stegen för att komma åt FHIR-servern med [Postman](https://www.getpostman.com/). 

## <a name="prerequisites"></a>Förutsättningar

- En FHIR-slutpunkt i Azure. 

  Om du vill distribuera Azure API för FHIR (en hanterad tjänst) kan du använda [Azure Portal](fhir-paas-portal-quickstart.md), [POWERSHELL](fhir-paas-powershell-quickstart.md)eller [Azure CLI](fhir-paas-cli-quickstart.md).

- Ett registrerat [konfidentiellt klient program](register-confidential-azure-ad-client-app.md) för att få åtkomst till FHIR-tjänsten.
- Du har beviljat behörigheter till det konfidentiella klient programmet, till exempel "FHIR data Contributor", för att få åtkomst till FHIR-tjänsten. Mer information finns i [Konfigurera Azure RBAC för FHIR](./configure-azure-rbac.md).
- Postman installerat. 
    
  Mer information om Postman finns i [Kom igång med Postman](https://www.getpostman.com).

## <a name="fhir-server-and-authentication-details"></a>Information om FHIR-Server och-autentisering

Följande autentiseringsmetoder krävs för att använda Postman:

- Din FHIR-server-URL, till exempel `https://MYACCOUNT.azurehealthcareapis.com`

- Identitets leverantören `Authority` för FHIR-servern, till exempel `https://login.microsoftonline.com/{TENANT-ID}`

- Den konfigurerade `audience` som vanligt vis är URL: en för FHIR-servern, till exempel `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` eller `https://azurehealthcareapis.com` .

- `client_id`Program-ID: t för det [konfidentiella klient program](register-confidential-azure-ad-client-app.md) som används för att komma åt FHIR-tjänsten.

- `client_secret`Eller program hemligheten för det konfidentiella klient programmet.

Slutligen bör du kontrol lera att `https://www.getpostman.com/oauth2/callback` är en registrerad svars-URL för klient programmet.

## <a name="connect-to-fhir-server"></a>Anslut till FHIR-Server

Öppna Postman och välj sedan **Hämta** för att göra en begäran till `https://fhir-server-url/metadata` .

![Funktions sats för Postman-metadata](media/tutorial-postman/postman-metadata.png)

URL: en för metadata för Azure API för FHIR är `https://MYACCOUNT.azurehealthcareapis.com/metadata` . 

I det här exemplet är FHIR-serverns webb adress `https://fhirdocsmsft.azurewebsites.net` och serverns kapacitets meddelande tillgänglig på `https://fhirdocsmsft.azurewebsites.net/metadata` . Den här slut punkten kan nås utan autentisering.

Om du försöker komma åt begränsade resurser inträffar svaret "autentisering misslyckades".

![Autentiseringen misslyckades](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>Hämta en åtkomsttoken
Välj **Get New Access Token** (Hämta ny åtkomsttoken).

Om du vill hämta en giltig åtkomsttoken väljer du **auktorisering** och väljer **OAuth 2,0** på den nedrullningsbara menyn **typ** .

![Ange OAuth 2,0](media/tutorial-postman/postman-select-oauth2.png)

Välj **Get New Access Token** (Hämta ny åtkomsttoken).

![Begär ny åtkomsttoken](media/tutorial-postman/postman-request-token.png)

I dialog rutan **Hämta ny** åtkomsttoken anger du följande information:

| Fält                 | Exempelvärde                                                                                                   | Kommentar                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Token Name (Tokennamn)            | TOKEn                                                                                                         | Ett namn som du väljer          |
| Grant Type (Typ av beviljande)            | Auktoriseringskod                                                                                              |                            |
| Callback URL (Webbadress för återanrop)          | `https://www.getpostman.com/oauth2/callback`                                                                    |                            |
| Auth URL (Auktoriseringswebbadress)              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` är `https://MYACCOUNT.azurehealthcareapis.com` för Azure API för FHIR |
| Access Token URL (Webbadress för åtkomsttoken)      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                    |                            |
| Klient-ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                           | Program-ID             |
| Client Secret (Klienthemlighet)         | `XXXXXXXX`                                                                                                      | Hemlig klient nyckel          |
| Omfång | `<Leave Blank>` | Omfånget används inte. Det kan därför lämnas tomt.  
| Tillstånd                 | `1234`     | [State](https://learning.postman.com/docs/sending-requests/authorization/) är ett ogenomskinligt värde för att förhindra förfalskning av begäran mellan webbplatser. Det är valfritt och kan ta ett godtyckligt värde, till exempel "1234".                           |
| Klientautentisering | Skicka klientautentiseringsuppgifter i brödtext                                                                                 |                 

Välj **token för begäran** som ska guidas genom Azure Active Directory-autentiseringsschemat och en token returneras till Postman. Om det uppstår ett autentiseringsfel kan du läsa Postman-konsolen för mer information. **Obs**: Välj **Visa** på menyfliken och välj sedan **Visa Postman-konsolen**. Kortkommandot till Postman-konsolen är **Alt-Ctrl + C**.

Rulla ned för att visa skärmen returnerad token och välj sedan **Använd token**.

![Använd token](media/tutorial-postman/postman-use-token.png)

Referera till fältet **åtkomsttoken** för att visa den nyligen ifyllda token. Om du väljer **Skicka** för att upprepa `Patient` resurss ökningen returneras en **status** `200 OK` . En returnerad status `200 OK` indikerar en lyckad HTTP-begäran.

![200 OK](media/tutorial-postman/postman-200-OK.png)

I Sök exemplet för *patient* finns det inga patienter i databasen, så att Sök resultatet är tomt.

Du kan kontrol lera åtkomsttoken med hjälp av ett verktyg som [JWT.MS](https://jwt.ms). Ett exempel på innehållet visas nedan.

```json
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

I fel söknings situationer kontrollerar du att du har rätt mål grupp ( `aud` anspråk) och är en bra plats att starta. Om din token kommer från rätt utfärdare ( `iss` anspråk) och har rätt mål grupp ( `aud` anspråk), men du fortfarande inte har åtkomst till FHIR-API: et, är det troligt att användaren eller tjänstens huvud namn `oid` inte har åtkomst till FHIR-dataplanet. Vi rekommenderar att du använder [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](configure-azure-rbac.md) för att tilldela användare data plan roller. Om du använder en extern, sekundär Azure Active Directory-klient för ditt data plan måste du [Konfigurera lokal RBAC för FHIR](configure-local-rbac.md) -tilldelningar.

Det är också möjligt att hämta en token för [Azure API för FHIR med hjälp av Azure CLI](get-healthcare-apis-access-token-cli.md). Om du använder en token som hämtats med Azure CLI bör du använda token för auktorisations typen *Bearer*. Klistra in token direkt.

## <a name="inserting-a-patient"></a>Lägga till en patient

Med en giltig åtkomsttoken kan du nu infoga en ny patient. I Postman ändrar du metoden genom att välja **post** och sedan lägger du till följande JSON-dokument i bröd texten i begäran.

[!code-json[](../samples/sample-patient.json)]

Välj **Skicka** för att fastställa att patienten har skapats.

![Skärm bild som visar att patienten har skapats.](media/tutorial-postman/postman-patient-created.png)

Om du upprepar patient sökningen bör du nu se patient posten.

![Patient skapad](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du åtkomst till Azure-API: et för FHIR med Postman. Mer information om Azure API för FHIR-funktioner finns i
 
>[!div class="nextstepaction"]
>[Funktioner som stöds](fhir-features-supported.md)
