---
title: REST API-felkoder – Azure Key Vault
description: De här fel koderna kan returneras av en åtgärd på en Azure Key Vault-webbtjänst.
keywords: ''
services: machine-learning
author: msmbaldwin
ms.custom: seodec18
ms.author: mbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 12/16/2019
ms.openlocfilehash: 30b7e34f2a791cfd8dec1a6d8e81d706fa07939f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91631230"
---
# <a name="azure-key-vault-rest-api-error-codes"></a>Fel koder för Azure Key Vault REST API
 
Följande felkoder kan returneras av en åtgärd på en Azure Key Vault-webbtjänst.
 
## <a name="http-401-unauthenticated-request"></a>HTTP 401: oautentiserad begäran

401 innebär att begäran inte autentiseras för Key Vault. 

En begäran autentiseras om:

- Nyckel valvet känner till identiteten för anroparen. särskilt
- Anroparen kan försöka komma åt Key Vault-resurser. 

Det finns flera olika orsaker till varför en begäran kan returnera 401.

### <a name="no-authentication-token-attached-to-the-request"></a>Ingen autentiseringstoken är kopplad till begäran. 

Här är ett exempel på begäran om att ange värdet för en hemlighet:

``` 
PUT https://putreqexample.vault.azure.net//secrets/DatabaseRotatingPassword?api-version=7.0 HTTP/1.1
x-ms-client-request-id: 03d275a2-52a4-4bed-82c8-6fe15165affb
accept-language: en-US
Authorization: Bearer     eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSIsImtpZCI6Im5iQ3dXMTF3M1hrQi14VWFYd0tSU0xqTUhHUSJ9.eyJhdWQiOiJodHRwczovL3ZhdWx0LmF6dXJlLm5ldCIsImlzcyI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpYXQiOjE1NDg2OTc1MTMsIm5iZiI6MTU0ODY5NzUxMywiZXhwIjoxNTQ4NzAxNDEzLCJhaW8iOiI0MkpnWUhoODVqaVBnZHF5ZlRGZE5TdHY3bGUvQkFBPSIsImFwcGlkIjoiZmFkN2Q1YjMtNjlkNi00YjQ4LTkyNTktOGQxMjEyNGUxY2YxIiwiYXBwaWRhY3IiOiIxIiwiaWRwIjoiaHR0cHM6Ly9zdHMud2luZG93cy5uZXQvNzJmOTg4YmYtODZmMS00MWFmLTkxYWItMmQ3Y2QwMTFkYjQ3LyIsIm9pZCI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInN1YiI6IjM5NzVhZWVkLTdkMDgtNDUzYi1iNmY0LTQ0NWYzMjY5ODA5MSIsInRpZCI6IjcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0NyIsInV0aSI6IjItZ3JoUmtlSWs2QmVZLUxuNDJtQUEiLCJ2ZXIiOiIxLjAifQ.fgubiz1MKqTJTXI8dHIV7t9Fle6FdHrkaGYKcBeVRX1WtLVuk1QVxzIFDlZKLXJ7QPNs0KWpeiWQI9IWIRK-8wO38yCqKTfDlfHOiNWGOpkKddlG729KFqakVf2w0GPyGPFCONRDAR5wjQarN9Bt8I8YbHwZQz_M1hztlnv-Lmsk1jBmech9ujD9-lTMBmSfFFbHcqquev119V7sneI-zxBZLf8C0pIDkaXf1t8y6Xr8CUJDMdlWLslCf3pBCNIOy65_TyGvy4Z4AJryTPBarNBPwOkNAtjCfZ4BDc2KqUZM5QN_VK4foP64sVzUL6mSr0Gh7lQJIL5b1qIpJxjxyQ
User-Agent: FxVersion/4.7.3324.0 OSName/Windows OSVersion/6.2.9200.0 Microsoft.Azure.KeyVault.KeyVaultClient/3.0.3.0
Content-Type: application/json; charset=utf-8
Host: putreqexample.vault.azure.net
Content-Length: 31

{
   "value": "m*gBJ7$Zuoz)"
}
```

Rubriken "Authorization" är den åtkomsttoken som krävs för varje anrop till Key Vault för data Plans åtgärder. Om rubriken saknas måste svaret vara 401.

### <a name="the-token-lacks-the-correct-resource-associated-with-it"></a>Token saknar rätt resurs som är kopplad till den. 

När du begär en åtkomsttoken från Azure OAUTH-slutpunkten är en parameter med namnet "Resource" obligatorisk. Värdet är viktigt för token-providern eftersom det omfångerar token för den avsedda användningen. Resursen för **alla** tokens för att komma åt en Key Vault är *https: \/ /Vault.keyvault.net* (utan avslutande snedstreck).

### <a name="the-token-is-expired"></a>Token har upphört att gälla

Tokens är base64-kodade och värdena kan avkodas på webbplatser som [http://jwt.calebb.net](http://jwt.calebb.net) . Här är den ovanstående token som är avkodad:

```
    {
 typ: "JWT",
 alg: "RS256",
 x5t: "nbCwW11w3XkB-xUaXwKRSLjMHGQ",
 kid: "nbCwW11w3XkB-xUaXwKRSLjMHGQ"
}.
{
 aud: "https://vault.azure.net",
 iss: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 iat: 1548697513,
 nbf: 1548697513,
 exp: 1548701413,
 aio: "42JgYHh85jiPgdqyfTFdNStv7le/BAA=",
 appid: "fad7d5b3-69d6-4b48-9259-8d12124e1cf1",
 appidacr: "1",
 idp: "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
 oid: "3975aeed-7d08-453b-b6f4-445f32698091",
 sub: "3975aeed-7d08-453b-b6f4-445f32698091",
 tid: "72f988bf-86f1-41af-91ab-2d7cd011db47",
 uti: "2-grhRkeIk6BeY-Ln42mAA",
 ver: "1.0"
}.
[signature]
```

Vi kan se flera viktiga delar i denna token:

- AUD (mål grupp): token-token. Observera att detta är `https://vault.azure.net` . Denna token fungerar inte för resurser som inte uttryckligen matchar det här värdet, till exempel Graph.
- IAT (utfärdat i): antalet Tick sedan den epoken börjar när token utfärdades.
- NBF (inte före): antalet Tick sedan den epoken börjar när denna token börjar gälla.
- EXP (Expires): antalet Tick sedan den epoken börjar när denna token upphör att gälla.
- AppID (program-ID): GUID för program-ID: t som utför den här begäran.
- tid (klient-ID): GUID för klient-ID för huvudobjektet som gör den här begäran

Det är viktigt att alla värden identifieras korrekt i token för att begäran ska fungera. Om allt är korrekt kommer begäran inte att resultera i 401.

### <a name="troubleshooting-401"></a>Felsöka 401

401s bör undersökas från punkten för generering av token innan begäran görs till nyckel valvet. Normalt används kod för att begära token. När token har tagits emot skickas den till Key Vault begäran. Om koden körs lokalt kan du använda Fiddler för att avbilda begäran/svar till `https://login.microsoftonline.com` . En begäran ser ut så här:

``` 
POST https://login.microsoftonline.com/<key vault tenant ID>/oauth2/token HTTP/1.1
Accept: application/json
Content-Type: application/x-www-form-urlencoded; charset=utf-8
Host: login.microsoftonline.com
Content-Length: 192

resource=https%3A%2F%2Fvault.azure.net&client_id=<registered-app-ID>&client_secret=<registered-app-secret>&client_info=1&grant_type=client_credentials
```

Följande användar information måste vara korrekt:

- Klient-ID för Key Vault
- Resurs värde har angetts till https %3 A %2 F %2 F Vault. Azure. net (URL-kodad)
- Klient-ID
- Klienthemlighet

Se till att resten av begäran är nästan identiska.

Om du bara kan hämta svars-åtkomsttoken kan du avkoda den (som visas ovan) för att säkerställa klient-ID, klient-ID (app-ID) och resursen.

## <a name="http-403-insufficient-permissions"></a>HTTP 403: otillräcklig behörighet

HTTP 403 innebär att begäran har autentiserats (den känner till den begärda identiteten) men identiteten har inte behörighet att komma åt den begärda resursen. Det finns två orsaker:

- Det finns ingen åtkomst princip för identiteten.
- IP-adressen för den begär ande resursen är inte godkänd i nyckel valvets brand Väggs inställningar.

HTTP 403 uppstår ofta när kundens program inte använder det klient-ID som kunden tycker att det är. Det innebär vanligt vis att åtkomst principerna inte har kon figurer ATS korrekt för den faktiska anrops identiteten.

### <a name="troubleshooting-403"></a>Felsöka 403

Börja med att aktivera loggning. Instruktioner för hur du gör detta finns i [Azure Key Vault loggning](logging.md).

När loggning har Aktiver ATS kan du avgöra om 403 är på grund av åtkomst principen eller brand Väggs principen.

#### <a name="error-due-to-firewall-policy"></a>Fel på grund av brand Väggs princip

"Klient adressen (00.00.00.00) är inte auktoriserad och anroparen är inte en betrodd tjänst"

Det finns en begränsad lista över "Azure-betrodda tjänster". Azure Web Sites är **inte** en betrodd Azure-tjänst. Mer information finns i blogg inlägget [Key Vault brand Väggs åtkomst av Azure App Services](https://azidentity.azurewebsites.net/post/2019/01/03/key-vault-firewall-access-by-azure-app-services).

Du måste lägga till IP-adressen för Azure-webbplatsen i Key Vault för att den ska fungera.

Om det beror på åtkomst princip: hitta objekt-ID för begäran och se till att objekt-ID: t matchar objektet som användaren försöker tilldela åtkomst principen till. Det finns ofta flera objekt i AAD som har samma namn, så det är mycket viktigt att man väljer rätt. Genom att ta bort och lägga till åtkomst principen, är det möjligt att se om det finns flera objekt med samma namn.

Dessutom kräver de flesta åtkomst principer inte användningen av det "auktoriserade programmet" som visas i portalen. Auktoriserat program används för "autentisering av" på uppdrags nivå ", som är sällsynt. 


## <a name="http-429-too-many-requests"></a>HTTP 429: för många begär Anden

Begränsningen inträffar när antalet begär Anden överskrider det angivna Max värdet för tidsram. Om begränsningen inträffar kommer Key Vaultens svar att vara HTTP 429. Det finns angivna Max värde för typer av begär Anden som görs. Till exempel: skapandet av en HSM 2048-bitars nyckel är 5 begär Anden per 10 sekunder, men alla andra HSM-transaktioner har en gräns på 1000-begäran/10 sekunder. Det är därför viktigt att förstå vilka typer av samtal som görs när du bestämmer orsaken till begränsningen.
I allmänhet är begär anden till Key Vault begränsade till 2000 begär Anden/10 sekunder. Undantag är nyckel åtgärder, enligt beskrivningen i [Key Vault tjänst gränser](service-limits.md)

### <a name="troubleshooting-429"></a>Felsöka 429
Begränsningen arbetar med följande metoder:

- Minska antalet begär Anden som görs till Key Vault genom att fastställa om det finns mönster för en begärd resurs och försöka cachelagra dem i det anropande programmet. 

- När Key Vault begränsning sker kan du anpassa den begär ande koden så att en exponentiell backoff används för att försöka igen. Algoritmen beskrivs här: [så här begränsar du appen](overview-throttling.md#how-to-throttle-your-app-in-response-to-service-limits)

- Om antalet begär Anden inte kan minskas genom cachelagring och tidsinställda backoff inte fungerar, kan du överväga att dela upp nycklarna i flera nyckel valv. Tjänst gränsen för en enskild prenumeration är 5x den enskilda Key Vault gränsen. Om du använder fler än 5 nyckel valv bör du överväga att använda flera prenumerationer. 

Detaljerad vägledning, inklusive begäran om att öka gränserna, hittar du här: [Key Vault begränsnings vägledning](overview-throttling.md)
