---
title: Hantera slutpunkter för direktuppspelning
description: Den här artikeln visar hur du hanterar strömnings slut punkter med Azure Media Services v3.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli, devx-track-csharp
ms.openlocfilehash: 6cc48de93affb8873bff1264dd012d2a7933e99f
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106108191"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Hantera slut punkter för direkt uppspelning med Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När ditt Media Services-konto skapas, läggs en **standard** [slut punkt för direkt uppspelning](streaming-endpoint-concept.md) till på ditt konto i **stoppat** tillstånd. Om du vill börja strömma ditt innehåll och dra nytta av [dynamisk paketering](encode-dynamic-packaging-concept.md) och [dynamisk kryptering](drm-content-protection-concept.md), måste den strömmande slut punkten från vilken du vill strömma innehåll vara i **Kör** tillstånd.

Den här artikeln visar hur du kör kommandot [Start](/rest/api/media/streamingendpoints/start) på slut punkten för direkt uppspelning med olika tekniker. 
 
> [!NOTE]
> Du faktureras bara när slut punkten för direkt uppspelning är i körnings läge.
    
## <a name="prerequisites"></a>Förutsättningar

Beakta 

* [Media Services begrepp](concepts-overview.md)
* [Koncept för strömnings slut punkt](streaming-endpoint-concept.md)
* [Dynamisk paketering](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>Använda REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Mer information finns i: 

* Referens dokumentationen för att [starta en StreamingEndpoint](/rest/api/media/streamingendpoints/start) -referens.
* Att starta en slut punkt för direkt uppspelning är en asynkron åtgärd. 

    Information om hur du övervakar långvariga åtgärder finns i [långvariga åtgärder](media-services-apis-overview.md).
* Den här [Postman-samlingen](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) innehåller exempel på flera rest-åtgärder, inklusive hur du startar en slut punkt för direkt uppspelning.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen 
 
1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Gå till ditt Azure Media Services-konto.
1. I det vänstra fönstret väljer du  **slut punkter för direkt uppspelning**.
1. Välj den slut punkt för direkt uppspelning som du vill starta och välj sedan **Starta**.

## <a name="use-the-azure-cli"></a>Använda Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Mer information finns i [AZ AMS streaming-Endpoint start](/cli/azure/ams/streaming-endpoint#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Använda SDK:er

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Se det fullständiga [Java Code-exemplet](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Se det fullständiga [.NET-kod exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Nästa steg

* [Media Services v3 OpenAPI-specifikation (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Slut punkts åtgärder för direkt uppspelning](/rest/api/media/streamingendpoints)
