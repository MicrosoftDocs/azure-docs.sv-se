---
title: Hantera Azure CDN caching-princip i Azure Media Services | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar Azure CDN caching-princip i Azure Media Services.
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: 5807d38e22d8cecf40b5ad4262f9e4662b77ec4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92779143"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Hantera Azure CDN caching-princip i Azure Media Services
Azure Media Services tillhandahåller HTTP-baserad anpassad direkt uppspelning och progressiv nedladdning. HTTP-baserad strömning är mycket skalbar med fördelarna med cachelagring i proxy-och CDN-lager samt cachelagring på klient sidan. Slut punkter för direkt uppspelning tillhandahåller generella strömnings funktioner och även konfiguration för HTTP-cache-huvuden. Slut punkter för direkt uppspelning anger HTTP-cache-Control: max-ålder och Expires-huvuden. Du kan få mer information om HTTP-cacheposter från [w3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Standard rubriker för cachelagring
Som standard använder streaming-slut punkter 3 dagars cache-rubriker för strömmande data på begäran (faktiska medie fragment/segment) och manifest (spelnings lista). För direkt uppspelning tillämpar slut punkter för direkt uppspelning 3 dagars cache-huvuden för data (faktiska medie fragment/segment) och 2 sekunder cache-huvud för manifest (spelnings lista) begär Anden. När Live-programmet vänder sig till på begäran (Live Archive), gäller att strömmade cacheposter på begäran.

## <a name="azure-cdn-integration"></a>Azure CDN-integrering
Azure Media Services tillhandahåller [integrerat CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) för strömnings slut punkter. Cache-Control-huvuden används på samma sätt som slut punkter för direkt uppspelning till CDN-aktiverade slut punkter för direkt uppspelning. Azure CDN använder konfigurations slut punktens konfigurerade cacheinställningar för att definiera livs längden för de internt cachelagrade objekten och använder det här värdet för att ange begärandehuvuden för leverans. När du använder CDN-aktiverade slut punkter för direkt uppspelning rekommenderar vi inte att du anger små cache-värden. Genom att ange små värden minskar du prestanda och minskar fördelarna med CDN. Det går inte att ange cache-huvuden som är mindre än 600 sekunder för CDN-aktiverade slut punkter för direkt uppspelning.

> [!IMPORTANT]
>Azure Media Services har fullständig integrering med Azure CDN. Med ett enda klick kan du integrera alla tillgängliga Azure CDN-leverantörer till din slut punkt för direkt uppspelning, inklusive standard-och Premium produkter. Mer information finns i det här [meddelandet](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Data avgifter från slut punkten för direkt uppspelning till CDN inaktive ras bara om CDN har Aktiver ATS för API: er för direkt uppspelning eller användning av Azure Portals slut punkt. Manuell integrering eller när du skapar en CDN-slutpunkt med CDN-API: er eller Portal avsnittet inaktiverar inte data kostnader.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Konfigurera cache-rubriker med Azure Media Services
Du kan använda Azure Portal-eller Azure Media Services-API: er för att konfigurera cache-huvudets värden.

1. Information om hur du konfigurerar cacheposter med hjälp av Azure Portal finns i avsnittet [Hantera slut punkter för direkt uppspelning](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) konfigurera slut punkten för direkt uppspelning.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK, [StreamingEndpointCacheControl-egenskaper](/dotnet/api/microsoft.windowsazure.mediaservices.client.streamingendpointcachecontrol).

## <a name="cache-configuration-precedence-order"></a>Prioritetsordning för cache-konfiguration
1. Azure Media Services konfigurerade cache-värdet åsidosätter standardvärdet.
2. Om det inte finns någon manuell konfiguration gäller standardvärdena.
3. Som standard gäller 2 sekunders cache-huvuden för Live streaming-manifestet (spelnings lista) oavsett om Azure Media eller Azure Storage konfiguration och åsidosätter det här värdet inte är tillgängligt.