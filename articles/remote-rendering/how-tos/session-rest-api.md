---
title: Session Management-REST API
description: Beskriver hur du hanterar sessioner
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4e65655f1809c6badc50e39a2a5e932516ef99d2
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509849"
---
# <a name="use-the-session-management-rest-api"></a>Använda REST API:et för sessionshantering

Om du vill använda funktioner för Azure-fjärrrendering måste du skapa en *session*. Varje session motsvarar en virtuell dator (VM) som allokeras i Azure och väntar på att en klient enhet ska ansluta. När en enhet ansluter återger den virtuella datorn de begärda data och ger resultatet som en video ström. När sessionen skapas väljer du vilken typ av server som du vill köra på, vilket fastställer prissättningen. När sessionen inte behövs längre bör den stoppas. Om den inte stoppas manuellt stängs den av automatiskt när sessionens *låne tid* upphör att gälla.

Vi tillhandahåller ett PowerShell-skript i [databasen arr samples](https://github.com/Azure/azure-remote-rendering) i mappen *scripts* , som kallas *RenderingSession.ps1*, som visar användningen av vår tjänst. Skriptet och dess konfiguration beskrivs här: exempel på [PowerShell-skript](../samples/powershell-example-scripts.md)

> [!TIP]
> PowerShell-kommandona som visas på den här sidan är avsedda att komplettera varandra. Om du kör alla skript i följd i samma PowerShell-kommandotolk, kommer de att bygga ovanpå varandra.

## <a name="regions"></a>Regioner

Se [listan över tillgängliga regioner](../reference/regions.md) för bas-URL: er för att skicka begär anden till.

För exempel skripten nedan valde vi regionen *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Exempel skript: Välj en slut punkt

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Konton

Om du inte har ett konto för fjärrrendering [skapar du ett](create-an-account.md). Varje resurs identifieras av en *accountId*, som används i API: erna för sessioner.

### <a name="example-script-set-accountid-and-accountkey"></a>Exempel skript: Ange accountId och accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Vanliga begärandehuvuden

* *Authorization* -huvudet måste ha värdet " `Bearer TOKEN` ", där " `TOKEN` " är den autentiseringstoken [som returneras av Secure token service](tokens.md).

### <a name="example-script-request-a-token"></a>Exempel skript: begär en token

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Vanliga svars rubriker

* *MS-ka* -huvudet kan användas av produkt teamet för att spåra samtalet i tjänsten.

## <a name="create-a-session"></a>Skapa en session

Det här kommandot skapar en session. Den returnerar ID: t för den nya sessionen. Du behöver sessions-ID för alla andra kommandon.

| URI | Metod |
|-----------|:-----------|
| /v1/Accounts/*accountId*/sessions/Create | POST |

**Brödtext i begäran:**

* maxLeaseTime (TimeSpan): ett timeout-värde när sessionen tas ur bruk automatiskt
* modeller (matris): URL: er för till gångs behållare till preload
* storlek (sträng): Server storleken som ska konfigureras ([**"standard"**](../reference/vm-sizes.md) eller [**"Premium"**](../reference/vm-sizes.md)). Visa [begränsade storleks begränsningar](../reference/limits.md#overall-number-of-polygons).

**Registrera**

| Statuskod | JSON-nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 202 | -sessionId: GUID | Klart |

### <a name="example-script-create-a-session"></a>Exempel skript: skapa en session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Exempel skript: Store sessionId

Svaret från begäran ovan innehåller ett **SessionID**som du behöver för alla uppföljnings begär Anden.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Uppdatera en session

Detta kommando uppdaterar en sessions parametrar. För närvarande kan du bara utöka låne tiden för en session.

> [!IMPORTANT]
> Låne tiden anges alltid som en total tid sedan sessionen börjar. Det innebär att om du har skapat en session med en låne tid på en timme och du vill utöka dess låne tid för en annan timme måste du uppdatera dess maxLeaseTime till två timmar.

| URI | Metod |
|-----------|:-----------|
| /v1/Accounts/*accountID*/sessions/*SessionID* | 9.0a |

**Brödtext i begäran:**

* maxLeaseTime (TimeSpan): ett timeout-värde när sessionen tas ur bruk automatiskt

**Registrera**

| Statuskod | JSON-nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 200 | | Klart |

### <a name="example-script-update-a-session"></a>Exempel skript: uppdatera en session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Hämta aktiva sessioner

Det här kommandot returnerar en lista över aktiva sessioner.

| URI | Metod |
|-----------|:-----------|
| /v1/Accounts/*accountId*/sessions | GET |

**Registrera**

| Statuskod | JSON-nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 200 | -sessioner: matris med sessions egenskaper | i avsnittet Hämta egenskaper för session finns en beskrivning av sessionens egenskaper |

### <a name="example-script-query-active-sessions"></a>Exempel skript: fråga aktiva sessioner

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Hämta egenskaper för sessioner

Det här kommandot returnerar information om en session, till exempel dess VM-värdnamn.

| URI | Metod |
|-----------|:-----------|
| /v1/Accounts/*accountId*/sessions/*SessionID*/Properties | GET |

**Registrera**

| Statuskod | JSON-nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 200 | -meddelande: sträng<br/>-sessionElapsedTime: TimeSpan<br/>-sessionHostname: sträng<br/>-sessionId: sträng<br/>-sessionMaxLeaseTime: TimeSpan<br/>-sessionSize: Enum<br/>-sessionStatus: Enum | räkna upp sessionStatus {Start, Ready, stoppad, stoppad, upphört, fel}<br/>Om statusen är "fel" eller "utgången", innehåller meddelandet mer information |

### <a name="example-script-get-session-properties"></a>Exempel skript: Hämta egenskaper för session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Stoppa en session

Det här kommandot stoppar en session. Den allokerade virtuella datorn kommer att återtas strax efter.

| URI | Metod |
|-----------|:-----------|
| /v1/Accounts/*accountId*/sessions/*SessionID* | DELETE |

**Registrera**

| Statuskod | JSON-nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 204 | | Klart |

### <a name="example-script-stop-a-session"></a>Exempel skript: stoppa en session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Exempel på utdata:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Nästa steg

* [PowerShell-exempelskript](../samples/powershell-example-scripts.md)
