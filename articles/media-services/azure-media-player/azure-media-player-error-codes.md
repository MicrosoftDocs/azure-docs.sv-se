---
title: Azure Media Player felkoder
description: En fel kods referens för Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: error-reference
ms.date: 04/20/2020
ms.openlocfilehash: 89f063ab50cfd880552e80a77ba798e4d06de5b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709588"
---
# <a name="error-codes"></a>Felkoder #

När uppspelningen inte kan starta eller har stoppats kommer en fel händelse att utlösas och `error()` funktionen returnerar en kod och ett valfritt meddelande som hjälper appens utvecklare att få mer information. `error().message` är inte meddelandet som visas för användaren.  Meddelandet som visas för användaren baseras på `error().code` bits 27-20, se tabellen nedan.

```javascript

    var myPlayer = amp('vid1');
    myPlayer.addEventListener('error', function() {
        var errorDetails = myPlayer.error();
        var code = errorDetails.code;
        var message = errorDetails.message;
    }
```

## <a name="error-codes-bits-31-28-4-bits"></a>Felkoder, bitar [31-28] (4 bitar) ##

Beskriv fel arean.

- 0 – Okänt
- 1 – AMP
- 2-AzureHtml5JS
- 3 – blixt
- 4 – Silverlight
- 5-HTML5
- 6-Html5FairPlayHLS

## <a name="error-codes-bits-27-0-28-bits"></a>Felkoder, bitar [27-0] (28 bitar) ##

Beskriv informationen om felet, BITS 27-20 tillhandahåller en hög nivå, och BITS 19-0 ger mer information om tillgängligt.


| amp. errorCode. ändrat | Koder, BITS [27-0] (28 bitar) | Beskrivning |
|---|---:|---|
| **MEDIA_ERR_ABORTED fel intervall (0x0100000-0x01FFFFF)** | | |
| abortedErrUnknown | 0x0100000 | Allmänt abort-fel |
| abortedErrNotImplemented | 0x0100001 | Abort-fel, har inte implementerats |
| abortedErrHttpMixedContentBlocked | 0x0100002 | Abort-fel, blandat innehåll blockerades – vanligt vis inträffar när en `http://` data ström läses in från en `https://` sida |
| **Start värde för MEDIA_ERR_NETWORK fel (0x0200000-0x02FFFFF)** | | |
| networkErrUnknown | 0x0200000 | Allmänt nätverks fel |
| networkErrHttpBadUrlFormat | 0x0200190 | Http 400-fel svar |
| networkErrHttpUserAuthRequired | 0x0200191 | Http 401-fel svar |
| networkErrHttpUserForbidden | 0x0200193 | Http 403-fel svar |
| networkErrHttpUrlNotFound | 0x0200194 | Http 404-fel svar |
| networkErrHttpNotAllowed | 0x0200195 | Http 405-fel svar |
| networkErrHttpGone | 0x020019A | Http 410-fel svar |
| networkErrHttpPreconditionFailed | 0x020019C | Http 412-fel svar |
| networkErrHttpInternalServerFailure | 0x02001F4 | Http 500-fel svar
| networkErrHttpBadGateway | 0x02001F6 | Http 502-fel svar |
| networkErrHttpServiceUnavailable | 0x02001F7 | Http 503-fel svar |
| networkErrHttpGatewayTimeout | 0x02001F8 | Http 504-fel svar |
| networkErrTimeout | 0x0200258 | Timeout-fel för nätverk
| networkErrErr | 0x0200259 | Fel svar för nätverks anslutning |
| **MEDIA_ERR_DECODE fel (0x0300000-0x03FFFFF)** | | |
| decodeErrUnknown | 0x0300000 | Generiskt avkodnings fel |
| **MEDIA_ERR_SRC_NOT_SUPPORTED fel (0x0400000-0x04FFFFF)** | | |
| srcErrUnknown | 0x0400000 | Allmän källa stöds inte |
| srcErrParsePresentation | 0x0400001 | Fel när presentationen skulle parsas |
| srcErrParseSegment | 0x0400002 | Segment parse-fel |
| srcErrUnsupportedPresentation | 0x0400003 | Presentationen stöds inte |
| srcErrInvalidSegment | 0x0400004 | Ogiltigt segment |
| **Start värde för MEDIA_ERR_ENCRYPTED fel (0x0500000-0x05FFFFF)** | | |
| encryptErrUnknown | 0x0500000 | Allmänt krypterat fel | 
| encryptErrDecrypterNotFound | 0x0500001 | Decrypter hittades inte |
| encryptErrDecrypterInit | 0x0500002 | Decrypter initierings fel |
| encryptErrDecrypterNotSupported | 0x0500003 | Decrypter stöds inte |
| encryptErrKeyAcquire | 0x0500004 | Nyckel hämtningen misslyckades |
| encryptErrDecryption | 0x0500005 | Dekryptering av segment misslyckades |
| encryptErrLicenseAcquire | 0x0500006 | Licens hämtningen misslyckades |
| **Start värde för SRC_PLAYER_MISMATCH fel (0x0600000-0x06FFFFF)** | | |
| srcPlayerMismatchUnknown | 0x0600000 | Allmän ingen matchande teknisk spelare för att spela upp källan |
| srcPlayerMismatchFlashNotInstalled | 0x0600001 |Flash-plugin-programmet är inte installerat, om det är installerat kan källan spelas upp. *Eller* Flash 30 är installerat och spelar upp AES-innehåll.  Om så är fallet kan du prova med en annan webbläsare. Flash 30 stöds inte idag den 7 juni. Se [kända problem](azure-media-player-known-issues.md) för mer information. Obs: om 0x00600003, både Flash och Silverlight inte installeras, om det anges i techOrder.|
| srcPlayerMismatchSilverlightNotInstalled | 0x0600002 | Silverlight-plugin-programmet installeras inte, om det är installerat kan det hända att källan spelas upp. Obs: om 0x00600003, både Flash och Silverlight inte installeras, om det anges i techOrder. |
| | 0x00600003 | Både Flash och Silverlight är inte installerade, om de anges i techOrder. |
| **Okända fel (0x0FF00000)** | | |
| errUnknown | 0xFF00000 | Okända fel |

## <a name="user-error-messages"></a>Användar fel meddelanden ##

Det användar meddelande som visas baseras på felkodens BITS 27-20.

- MEDIA_ERR_ABORTED (1)-"du avbröt videouppspelningen"
- MEDIA_ERR_NETWORK (2)-"ett nätverks fel orsakade att nedladdningen av videon lyckades."
- MEDIA_ERR_DECODE (3)-"videouppspelningen avbröts på grund av ett skadat problem eller på grund av att de funktioner som används i webbläsaren inte kunde användas."
- MEDIA_ERR_SRC_NOT_SUPPORTED (4)-"Det gick inte att läsa in videon, antingen på grund av att servern eller nätverket misslyckades eller på grund av att formatet inte stöds."
- MEDIA_ERR_ENCRYPTED (5)-"videon är krypterad och vi har inte de nycklar som krävs för att dekryptera den."
- SRC_PLAYER_MISMATCH (6)-"ingen kompatibel källa hittades för den här videon."
- MEDIA_ERR_UNKNOWN (0xFF)-"ett okänt fel inträffade."

## <a name="examples"></a>Exempel ##

### <a name="0x10600001"></a>0x10600001 ##

"Ingen kompatibel källa hittades för den här videon." visas för slutanvändaren.

Det finns ingen teknisk spelare som kan spela de begärda källorna, men om Flash-plugin-programmet är installerat är det troligt att en källa kan spelas upp.  

### <a name="0x20200194"></a>0x20200194 ###

"Ett nätverks fel orsakade att nedladdningen av videon lyckades." visas för slutanvändaren.

AzureHtml5JS kunde inte spelas upp från ett HTTP 404-svar.

### <a name="categorizing-errors"></a>Kategorisering av fel ###

```javascript
    if(myPlayer.error().code & amp.errorCode.abortedErrStart) {
        // MEDIA_ERR_ABORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.networkErrStart) {
        // MEDIA_ERR_NETWORK errors
    }
    else if(myPlayer.error().code & amp.errorCode.decodeErrStart) {
        // MEDIA_ERR_DECODE errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcErrStart) {
        // MEDIA_ERR_SRC_NOT_SUPPORTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.encryptErrStart) {
        // MEDIA_ERR_ENCRYPTED errors
    }
    else if(myPlayer.error().code & amp.errorCode.srcPlayerMismatchStart) {
        // SRC_PLAYER_MISMATCH errors
    }
    else {
        // unknown errors
    }
```

### <a name="catching-a-specific-error"></a>Fånga ett speciellt fel ###

Följande kod fångar bara 404 fel:

```javascript
    if(myPlayer.error().code & amp.errorCode.networkErrHttpUrlNotFound) {
        // all http 404 errors
    }
```

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)