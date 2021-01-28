---
title: Strömma videofiler med Azure Media Services CLI
description: Följ stegen i den här självstudien för att använda Azure CLI för att skapa ett nytt Azure Media Services konto, koda en fil och strömma den till Azure Media Player.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
keywords: azure media services, strömma
ms.service: media-services
ms.workload: media
ms.topic: tutorial
ms.custom: devx-track-azurecli
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: c78205d7e2b41628de9e8b92c9fa5506e82158cb
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954503"
---
# <a name="tutorial-encode-a-remote-file-based-on-url-and-stream-the-video---azure-cli"></a>Självstudie: koda en fjärrfil baserat på URL och strömma videon – Azure CLI

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Den här självstudien visar hur du enkelt kodar och direktuppspelar videor på en rad olika webbläsare och enheter genom att använda Azure Media Services och Azure CLI. Du kan ange indata-innehåll med hjälp av HTTPS-eller SAS-URL: er eller sökvägar till filer i Azure Blob Storage.

Exemplet i den här artikeln kodar innehåll som du gör tillgängliga via en HTTPS-URL. Media Services v3 stöder för närvarande inte konvertering av segment överföring via HTTPS-URL: er.

I slutet av den här självstudien kommer du att kunna strömma en video.  

![Spela upp videon](./media/stream-files-dotnet-quickstart/final-video.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-media-services-account"></a>Skapa ett Media Services-konto

Innan du kan kryptera, koda, analysera, hantera och strömma medie innehåll i Azure måste du skapa ett Media Services-konto. Kontot måste vara kopplat till ett eller flera lagrings konton.

Ditt Media Services-konto och alla tillhör ande lagrings konton måste finnas i samma Azure-prenumeration. Vi rekommenderar att du använder lagrings konton som finns på samma plats som Media Services kontot för att begränsa svars tid och kostnader för utgående data.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

```azurecli-interactive
az group create -n amsResourceGroup -l westus2
```

### <a name="create-an-azure-storage-account"></a>Skapa ett Azure Storage-konto

I det här exemplet skapar vi ett General-Purpose v2 standard LRS-konto.

Om du vill experimentera med lagringskonton använder du `--sku Standard_LRS`. När du väljer en SKU för produktion kan du överväga att använda `--sku Standard_RAGRS` , som tillhandahåller geografisk replikering för affärs kontinuitet. Mer information finns i [lagringskonton](/cli/azure/storage/account).

```azurecli-interactive
az storage account create -n amsstorageaccount --kind StorageV2 --sku Standard_LRS -l westus2 -g amsResourceGroup
```

### <a name="create-an-azure-media-services-account"></a>Skapa ett Azure Media Services-konto

```azurecli-interactive
az ams account create --n amsaccount -g amsResourceGroup --storage-account amsstorageaccount -l westus2
```

Du får ett svar så här:

```
{
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
  "location": "West US 2",
  "mediaServiceId": "8b569c2e-d648-4fcb-9035-c7fcc3aa7ddf",
  "name": "amsaccount",
  "resourceGroup": "amsResourceGroupTest",
  "storageAccounts": [
    {
      "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Storage/storageAccounts/amsstorageaccount",
      "resourceGroup": "amsResourceGroupTest",
      "type": "Primary"
    }
  ],
  "tags": null,
  "type": "Microsoft.Media/mediaservices"
}
```

## <a name="start-the-streaming-endpoint"></a>Starta slutpunkten för direktuppspelning

Följande Azure CLI-kommando startar standard **slut punkten för direkt uppspelning**.

```azurecli-interactive
az ams streaming-endpoint start  -n default -a amsaccount -g amsResourceGroup
```

Du får ett svar så här:

```
{
  "accessControl": null,
  "availabilitySetName": null,
  "cdnEnabled": true,
  "cdnProfile": "AzureMediaStreamingPlatformCdnProfile-StandardVerizon",
  "cdnProvider": "StandardVerizon",
  "created": "2019-02-06T21:58:03.604954+00:00",
  "crossSiteAccessPolicies": null,
  "customHostNames": [],
  "description": "",
  "freeTrialEndTime": "2019-02-21T22:05:31.277936+00:00",
  "hostName": "amsaccount-usw22.streaming.media.azure.net",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingendpoints/default",
  "lastModified": "2019-02-06T21:58:03.604954+00:00",
  "location": "West US 2",
  "maxCacheAge": null,
  "name": "default",
  "provisioningState": "Succeeded",
  "resourceGroup": "amsResourceGroup",
  "resourceState": "Running",
  "scaleUnits": 0,
  "tags": {},
  "type": "Microsoft.Media/mediaservices/streamingEndpoints"
}
```

Om slut punkten för direkt uppspelning redan körs visas följande meddelande:

```
(InvalidOperation) The server cannot execute the operation in its current state.
```

## <a name="create-a-transform-for-adaptive-bitrate-encoding"></a>Skapa en transformering för kodning med anpassad bit hastighet

Skapa en **transformering** för att konfigurera vanliga uppgifter för kodning eller analysering av videor. I det här exemplet har vi anpassad bit hastighets kodning. Vi skickar sedan ett jobb under transformeringen som vi skapade. Jobbet är en begäran om att Media Services att tillämpa transformeringen på det angivna video-eller ljud innehållets indata.

```azurecli-interactive
az ams transform create --name testEncodingTransform --preset AdaptiveStreaming --description 'a simple Transform for Adaptive Bitrate Encoding' -g amsResourceGroup -a amsaccount
```

Du får ett svar så här:

```
{
  "created": "2019-02-15T00:11:18.506019+00:00",
  "description": "a simple Transform for Adaptive Bitrate Encoding",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform",
  "lastModified": "2019-02-15T00:11:18.506019+00:00",
  "name": "testEncodingTransform",
  "outputs": [
    {
      "onError": "StopProcessingJob",
      "preset": {
        "odatatype": "#Microsoft.Media.BuiltInStandardEncoderPreset",
        "presetName": "AdaptiveStreaming"
      },
      "relativePriority": "Normal"
    }
  ],
  "resourceGroup": "amsResourceGroup",
  "type": "Microsoft.Media/mediaservices/transforms"
}
```

## <a name="create-an-output-asset"></a>Skapa en utdatatillgång

Skapa en utmatnings **till gång** som ska användas som kodnings jobbets utdata.

```azurecli-interactive
az ams asset create -n testOutputAssetName -a amsaccount -g amsResourceGroup
```

Du får ett svar så här:

```
{
  "alternateId": null,
  "assetId": "96427438-bbce-4a74-ba91-e38179b72f36",
  "container": null,
  "created": "2019-02-14T23:58:19.127000+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/assets/testOutputAssetName",
  "lastModified": "2019-02-14T23:58:19.127000+00:00",
  "name": "testOutputAssetName",
  "resourceGroup": "amsResourceGroup",
  "storageAccountName": "amsstorageaccount",
  "storageEncryptionFormat": "None",
  "type": "Microsoft.Media/mediaservices/assets"
}
```

## <a name="start-a-job-by-using-https-input"></a>Starta ett jobb genom att använda HTTPS-ingångar

När du skickar jobb för att bearbeta videor måste du tala om för Media Services var du hittar ingångs videon. Ett alternativ är att ange en HTTPS-URL som jobb inmatare, som du ser i det här exemplet.

När du kör `az ams job start` kan du applicera en etikett på jobbets utdata. Du kan sedan använda etiketten för att identifiera vad utmatnings till gången är för.

- Om du tilldelar ett värde till etiketten anger du "-output-assets" till "assetname = Label".
- Om du inte tilldelar ett värde till etiketten anger du "--output-assets" till "assetname =".

  Observera att vi lägger till "=" i `output-assets` .

```azurecli-interactive
az ams job start --name testJob001 --transform-name testEncodingTransform --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' --files 'Ignite-short.mp4' --output-assets testOutputAssetName= -a amsaccount -g amsResourceGroup
```

Du får ett svar så här:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

### <a name="check-status"></a>Kontrollera status

I fem minuter kontrollerar du jobbets status. Det bör vara "klart". Den är inte färdig, kontrol lera igen om några minuter. När den är färdig går du till nästa steg och skapar en **strömmande positionerare**.

```azurecli-interactive
az ams job show -a amsaccount -g amsResourceGroup -t testEncodingTransform -n testJob001
```

## <a name="create-a-streaming-locator-and-get-a-path"></a>Skapa en strömmande lokaliserare och hämta en sökväg

När kodningen är klar är nästa steg att göra videon i utdatatillgången tillgänglig för uppspelning till klienterna. Det gör du genom att först skapa en strömmande positionerare. Sedan skapar du direkt uppspelnings-URL: er som klienter kan använda.

### <a name="create-a-streaming-locator"></a>Skapa en positionerare för direktuppspelning

```azurecli-interactive
az ams streaming-locator create -n testStreamingLocator --asset-name testOutputAssetName --streaming-policy-name Predefined_ClearStreamingOnly  -g amsResourceGroup -a amsaccount 
```

Du får ett svar så här:

```
{
  "alternativeMediaId": null,
  "assetName": "output-3b6d7b1dffe9419fa104b952f7f6ab76",
  "contentKeys": [],
  "created": "2019-02-15T04:35:46.270750+00:00",
  "defaultContentKeyPolicyName": null,
  "endTime": "9999-12-31T23:59:59.999999+00:00",
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/streamingLocators/testStreamingLocator",
  "name": "testStreamingLocator",
  "resourceGroup": "amsResourceGroup",
  "startTime": null,
  "streamingLocatorId": "e01b2be1-5ea4-42ca-ae5d-7fe704a5962f",
  "streamingPolicyName": "Predefined_ClearStreamingOnly",
  "type": "Microsoft.Media/mediaservices/streamingLocators"
}
```

### <a name="get-streaming-locator-paths"></a>Hämta sökvägar för strömmande positionerare

```azurecli-interactive
az ams streaming-locator get-paths -a amsaccount -g amsResourceGroup -n testStreamingLocator
```

Du får ett svar så här:

```
{
  "downloadPaths": [],
  "streamingPaths": [
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)"
      ],
      "streamingProtocol": "Hls"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=mpd-time-csf)"
      ],
      "streamingProtocol": "Dash"
    },
    {
      "encryptionScheme": "NoEncryption",
      "paths": [
        "/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest"
      ],
      "streamingProtocol": "SmoothStreaming"
    }
  ]
}
```

Kopiera HLS-sökvägen (HTTP Live streaming). I det här fallet är det `/e01b2be1-5ea4-42ca-ae5d-7fe704a5962f/ignite.ism/manifest(format=m3u8-aapl)` .

## <a name="build-the-url"></a>Bygg URL: en

### <a name="get-the-streaming-endpoint-host-name"></a>Hämta värd namnet för direkt uppspelnings slut punkten

```azurecli-interactive
az ams streaming-endpoint list -a amsaccount -g amsResourceGroup -n default
```

Kopiera värdet `hostName`. I det här fallet är det `amsaccount-usw22.streaming.media.azure.net` .

### <a name="assemble-the-url"></a>Montera URL: en

”https://” + &lt;värde för hostName&gt; + &lt;värde för Hls-sökväg&gt;

Här är ett exempel:

`https://amsaccount-usw22.streaming.media.azure.net/7f19e783-927b-4e0a-a1c0-8a140c49856c/ignite.ism/manifest(format=m3u8-aapl)`

## <a name="test-playback-by-using-azure-media-player"></a>Testa uppspelning med Azure Media Player

> [!NOTE]
> Om en spelare finns på en HTTPS-plats måste du starta URL: en med "https".

1. Öppna en webbläsare och gå till [https://aka.ms/azuremediaplayer/](https://aka.ms/azuremediaplayer/) .
2. I rutan **URL** klistrar du in webb adressen som du skapade i föregående avsnitt. Du kan klistra in URL: en i HLS, tank streck eller smidigt format. Azure Media Player använder automatiskt ett lämpligt strömnings protokoll för uppspelning på enheten.
3. Välj **uppdaterings spelaren**.

>[!NOTE]
>Azure Media Player kan användas vid testning, men bör inte användas i en produktionsmiljö.

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna i resurs gruppen, inklusive de Media Services och lagrings konton som du skapade för den här självstudien, tar du bort resurs gruppen.

Kör följande Azure CLI-kommando:

```azurecli-interactive
az group delete --name amsResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services](media-services-overview.md)
