---
title: Övervaka diagnostikloggar via Azure Monitor
description: Den här artikeln visar hur du dirigerar och visar diagnostikloggar via Azure Monitor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3093d20fbe0a1ded68e34e816b7fc865a237ad92
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100576319"
---
# <a name="monitor-media-services-diagnostic-logs"></a>Övervaka Media Services diagnostikloggar

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med [Azure Monitor](../../azure-monitor/overview.md) kan du övervaka mått och diagnostikloggar som hjälper dig att förstå hur dina program presterar. Detaljerad beskrivning av den här funktionen och se varför du vill använda Azure Media Services mått och diagnostikloggar finns i [övervaka Media Services statistik och diagnostikloggar](media-services-metrics-diagnostic-logs.md).

Den här artikeln visar hur du dirigerar data till lagrings kontot och sedan visar data.

## <a name="prerequisites"></a>Förutsättningar

- [Skapa ett Media Services-konto](./create-account-howto.md).
- Granska  [övervaknings Media Services statistik och diagnostikloggar](media-services-metrics-diagnostic-logs.md).

## <a name="route-data-to-the-storage-account-using-the-portal"></a>Dirigera data till lagrings kontot med hjälp av portalen

1. Logga in på Azure Portal på https://portal.azure.com.
1. Navigera till ditt Media Services-konto i och klicka på **diagnostikinställningar** under **övervaka**. Här ser du en lista över alla resurser i prenumerationen som producerar övervakningsdata via Azure Monitor.

    ![Skärm bild som visar diagnostikinställningar i avsnittet övervakning.](media/media-services-diagnostic-logs/logs01.png)

1. Klicka på **Lägg till diagnostisk inställning**.

   En resursdiagnostikinställning är en definition av *vilka* övervakningsdata som ska dirigeras från en viss resurs och *vart* dessa övervakningsdata ska dirigeras.

1. I avsnittet som visas ger du inställningen ett **namn** och kryssar i rutan för att **Arkivera till ett lagringskonto**.

    Välj det lagrings konto som du vill skicka loggar till och tryck på **OK**.
1. Markera alla rutor under **Logg** och **Mått**. Beroende på resurstyp kanske du bara ser ett av alternativen. De här kryssrutorna styr vilka kategorier av för resurstypen tillgängliga logg- och måttdata som skickas till den valda målplatsen, i det här fallet ett lagringskonto.

   ![Avsnittet diagnostikinställningar](media/media-services-diagnostic-logs/logs02.png)
1. Ställ in reglaget **Bevarande (dagar)** till 30. Det här reglaget anger hur många dagar som övervakningsdata behålls på lagringskontot. Azure Monitor avlägsnar automatiskt data som är äldre än det angivna antalet dagar. Om du ställer in noll dagar lagras data utan tidsbegränsning.
1. Klicka på **Spara**.

Nu flödar övervakningsdata från resursen till lagringskontot.

## <a name="route-data-to-the-storage-account-using-the-azure-cli"></a>Dirigera data till lagrings kontot med hjälp av Azure CLI

Om du vill aktivera lagring av diagnostikloggar i ett lagrings konto kör du följande `az monitor diagnostic-settings` Azure CLI-kommando:

```azurecli-interactive
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Exempel:

```azurecli-interactive
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>Visa data i lagrings kontot med hjälp av portalen

Om du har följt föregående steg har data börjat flöda till lagringskontot.

Du kan behöva vänta upp till fem minuter innan händelsen visas på lagringskontot.

1. I portalen navigerar du till avsnittet **Lagringskonton** genom att söka i navigeringsfältet till vänster.
1. Identifiera det lagringskonto som du skapade i föregående avsnitt och klicka på det.
1. Klicka på **blobbar**, sedan på behållaren med etiketten **Insights-logs-keydeliveryrequests**. Det här är den behållare som innehåller dina loggar. Övervaknings data är indelade i behållare efter resurs-ID, sedan efter datum och tid.
1. Navigera till filen PT1H.json genom att klicka på containrarna för resurs-ID, datum och tid. Klicka på filen PT1H.json och på **Hämta**.

 Du kan nu visa JSON-händelsen som lagrats i lagringskontot.

### <a name="examples-of-pt1hjson"></a>Exempel på PT1H.jspå

#### <a name="clear-key-delivery-log"></a>Rensa nyckel leverans logg

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

#### <a name="widevine-encrypted-key-delivery-log"></a>Widevine-krypterad nyckel leverans logg

```json
{
  "time": "2019-05-20T23:15:22.7088747Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 69,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
      "nbf": "1558392430",
      "exp": "1558396330",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "westus2",
  "properties": {
    "requestId": "49613dd2-16aa-4595-a6e0-4e68beae6d37",
    "keyType": "Widevine",
    "keyId": "0092d23a-0a42-4c5f-838e-6d1bbc6346f8",
    "policyName": "DRMContentKeyPolicy",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="see-also"></a>Se även

* [Azure Monitor-statistik](../../azure-monitor/data-platform.md)
* [Azure Monitor diagnostikloggar](../../azure-monitor/essentials/platform-logs-overview.md)
* [Så här samlar du in och använder loggdata från dina Azure-resurser](../../azure-monitor/essentials/platform-logs-overview.md)

## <a name="next-steps"></a>Nästa steg

[Övervaka mått](media-services-metrics-howto.md)
