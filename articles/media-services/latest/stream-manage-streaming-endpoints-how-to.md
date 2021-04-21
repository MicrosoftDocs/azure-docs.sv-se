---
title: Hantera slutpunkter för direktuppspelning
description: Den här artikeln visar hur du hanterar slutpunkter för direktuppspelning med Azure Media Services v3.
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
ms.openlocfilehash: 2b442edc537ec64b12df215a18ab017ee47becff
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791741"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Hantera slutpunkter för direktuppspelning med Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När ditt Media Services-konto skapas läggs en **standardslutpunkt** [för](stream-streaming-endpoint-concept.md) direktuppspelning till i ditt konto med tillståndet **Stoppad.** Om du vill starta direktuppspelning av [](drm-content-protection-concept.md)innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill strömma innehåll från ha tillståndet **Körs.** [](encode-dynamic-packaging-concept.md)

Den här artikeln visar hur du kör [startkommandot på](/rest/api/media/streamingendpoints/start) slutpunkten för direktuppspelning med hjälp av olika tekniker. 
 
> [!NOTE]
> Du debiteras bara när slutpunkten för direktuppspelning körs.
    
## <a name="prerequisites"></a>Förutsättningar

Recension: 

* [Media Services begrepp](concepts-overview.md)
* [Begrepp för slutpunkter för direktuppspelning](stream-streaming-endpoint-concept.md)
* [Dynamisk paketering](encode-dynamic-packaging-concept.md)

## <a name="use-rest"></a>Använda REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Mer information finns i: 

* Starta [en StreamingEndpoint-referensdokumentation.](/rest/api/media/streamingendpoints/start)
* Att starta en slutpunkt för direktuppspelning är en asynkron åtgärd. 

    Information om hur du övervakar långvariga åtgärder finns [i Långvariga åtgärder.](media-services-apis-overview.md)
* Den [här Postman-samlingen](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) innehåller exempel på flera REST-åtgärder, inklusive hur du startar en slutpunkt för direktuppspelning.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen 
 
1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Gå till ditt Azure Media Services konto.
1. I den vänstra rutan väljer du **Strömningsslutpunkter.**
1. Välj den slutpunkt för direktuppspelning som du vill starta och välj sedan **Starta.**

## <a name="use-the-azure-cli"></a>Använda Azure CLI

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Mer information finns i [az ams streaming-endpoint start](/cli/azure/ams/streaming-endpoint#az_ams_streaming_endpointstart).

## <a name="use-sdks"></a>Använda SDK:er

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Se det fullständiga [Java-kodexe exemplet](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

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

Se det fullständiga [.NET-kodexe exemplet](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Streaming/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Nästa steg

* [Media Services v3 OpenAPI Specification (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Åtgärder för slutpunkter för direktuppspelning](/rest/api/media/streamingendpoints)
