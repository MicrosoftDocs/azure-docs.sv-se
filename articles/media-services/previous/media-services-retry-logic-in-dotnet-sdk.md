---
title: Omprövnings logik i Media Services SDK för .NET | Microsoft Docs
description: Avsnittet ger en översikt över omprövnings logik i Media Services SDK för .NET.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 527b61a6-c862-4bd8-bcbc-b9aea1ffdee3
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: fe37c9f7727535a292b029c342aa0e85ce0a86ec
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644123"
---
# <a name="retry-logic-in-the-media-services-sdk-for-net"></a>Omprövnings logik i Media Services SDK för .NET

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

När du arbetar med Microsoft Azure tjänster kan tillfälliga fel uppstå. Om ett tillfälligt fel inträffar, i de flesta fall, efter några försök lyckas åtgärden. Media Services SDK för .NET implementerar omprövnings logiken för att hantera tillfälliga fel som är kopplade till undantag och fel som orsakas av webb förfrågningar, köra frågor, Spara ändringar och lagrings åtgärder.  Som standard kör Media Services SDK för .NET fyra återförsök innan undantaget för ditt program återskapas. Koden i programmet måste sedan hantera detta undantag korrekt.  

 Följande är en kort rikt linje för förfrågnings-, lagrings-, fråge-och SaveChanges-principer:  

* Lagrings principen används för Blob Storage-åtgärder (uppladdning eller hämtning av till gångs filer).  
* Principen för webb principer används för allmänna webb begär Anden (till exempel för att hämta en autentiseringstoken och matcha användarens kluster slut punkt).  
* Frågeprincip används för att köra frågor mot entiteter från REST (till exempel mediaContext. assets. Where (...)).  
* SaveChanges-principen används för att göra allt som ändrar data i tjänsten (till exempel skapa en entitet som uppdaterar en entitet, som anropar en tjänst funktion för en åtgärd).  
  
  Det här avsnittet innehåller undantags typer och felkoder som hanteras av logiken Media Services SDK för .NET-omförsök.  

## <a name="exception-types"></a>Undantags typer
I följande tabell beskrivs undantag som Media Services SDK för .NET hanterar eller inte hanterar för vissa åtgärder som kan orsaka tillfälliga fel.  

| Undantag | Webb förfrågan | Storage | Fråga | SaveChanges |
| --- | --- | --- | --- | --- |
| WebException<br/>Mer information finns i avsnittet [Webexceptions status koder](media-services-retry-logic-in-dotnet-sdk.md#WebExceptionStatus) . |Ja |Ja |Ja |Ja |
| DataServiceClientException<br/> Mer information finns i [status koder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Inga |Ja |Ja |Ja |
| DataServiceQueryException<br/> Mer information finns i [status koder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Inga |Ja |Ja |Ja |
| DataServiceRequestException<br/> Mer information finns i [status koder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Inga |Ja |Ja |Ja |
| DataServiceTransportException |Inga |Inga |Ja |Ja |
| TimeoutException |Ja |Ja |Ja |Inga |
| SocketException |Ja |Ja |Ja |Ja |
| StorageException |Inga |Ja |Inga |Inga |
| IOException |Inga |Ja |Inga |Inga |

### <a name="webexception-status-codes"></a><a name="WebExceptionStatus"></a> Webexceptions status koder
I följande tabell visas för vilka webexceptions-felkoder som logiken för omförsök implementeras. [WebExceptionStatus](/dotnet/api/system.net.webexceptionstatus) -uppräkningen definierar status koderna.  

| Status | Webb förfrågan | Storage | Fråga | SaveChanges |
| --- | --- | --- | --- | --- |
| ConnectFailure |Ja |Ja |Ja |Ja |
| NameResolutionFailure |Ja |Ja |Ja |Ja |
| ProxyNameResolutionFailure |Ja |Ja |Ja |Ja |
| SendFailure |Ja |Ja |Ja |Ja |
| PipelineFailure |Ja |Ja |Ja |Inga |
| ConnectionClosed |Ja |Ja |Ja |Inga |
| KeepAliveFailure |Ja |Ja |Ja |Inga |
| UnknownError |Ja |Ja |Ja |Inga |
| ReceiveFailure |Ja |Ja |Ja |Inga |
| RequestCanceled |Ja |Ja |Ja |Inga |
| Tidsgräns |Ja |Ja |Ja |Inga |
| ProtocolError <br/>Återförsöket för ProtocolError kontrol leras av HTTP-status kod hantering. Mer information finns i [status koder för HTTP-fel](media-services-retry-logic-in-dotnet-sdk.md#HTTPStatusCode). |Ja |Ja |Ja |Ja |

### <a name="http-error-status-codes"></a><a name="HTTPStatusCode"></a> Status koder för HTTP-fel
När fråge-och SaveChanges-åtgärder returnerar DataServiceClientException, DataServiceQueryException eller DataServiceQueryException returneras status koden för HTTP-fel i egenskapen StatusCode.  I följande tabell visas för vilka felkoder som logiken för omförsök implementeras.  

| Status | Webb förfrågan | Storage | Fråga | SaveChanges |
| --- | --- | --- | --- | --- |
| 401 |Inga |Ja |Inga |Inga |
| 403 |Inga |Ja<br/>Hantering av återförsök med längre vänte tid. |Inga |Inga |
| 408 |Ja |Ja |Ja |Ja |
| 429 |Ja |Ja |Ja |Ja |
| 500 |Ja |Ja |Ja |Inga |
| 502 |Ja |Ja |Ja |Inga |
| 503 |Ja |Ja |Ja |Ja |
| 504 |Ja |Ja |Ja |Inga |

Om du vill ta en titt på den faktiska implementeringen av Media Services SDK för .NET-omprövnings logik, se [Azure-SDK-för-Media-Services](https://github.com/Azure/azure-sdk-for-media-services/tree/dev/src/net/Client/TransientFaultHandling).

## <a name="next-steps"></a>Nästa steg
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
