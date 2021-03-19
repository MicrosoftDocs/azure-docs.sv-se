---
title: Regioner där Video Indexer är tillgängligt – Azure
titleSuffix: Azure Media Services
description: Den här artikeln pratar om Azure-regioner där Azure Media Services Video Indexer är tillgängligt.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/14/2020
ms.author: juliako
ms.openlocfilehash: 6ebdb22f50efbefc695f9752c6e6fc333571828c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90530953"
---
# <a name="azure-regions-in-which-video-indexer-exists"></a>Azure-regioner där Video Indexer finns

Video Indexer-API: er innehåller en **plats** parameter som du bör ange till den Azure-region som anropet ska dirigeras till. Det måste vara en [Azure-region där video Indexer är tillgängligt](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services&regions=all).

## <a name="locations"></a>Platser

`location`Parametern måste tilldelas kod namnet för Azure-regionen som dess värde. Om du använder Video Indexer i förhands gransknings läge bör du ange `"trial"` värdet. `trial` är standardvärdet för `location` parametern. Annars kan du hämta kod namnet för den Azure-region som ditt konto finns i och som ditt anrop ska dirigeras till, använda Azure Portal eller köra ett [Azure CLI](/cli/azure) -kommando.

### <a name="azure-portal"></a>Azure Portal

1. Logga in på [Video Indexer](https://www.videoindexer.ai/)-webbplatsen.
1. Välj **användar konton** i det övre högra hörnet på sidan.
1. Hitta platsen för ditt konto i det övre högra hörnet.  

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/location/location1.png" alt-text="Plats":::
    
###  <a name="cli-command"></a>CLI-kommando

```azurecli-interactive
az account list-locations
```

När du har kört raden som visas ovan får du en lista över alla Azure-regioner. Navigera till den Azure-region som har det *visnings* namn du söker och använd dess *Name* -värde för parametern **location** .

För Azure-regionen Västra USA 2 (visas nedan) använder du till exempel "westus2" för **plats** parametern.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="next-steps"></a>Nästa steg

- [Anpassa språk modellen med hjälp av API: er](customize-language-model-with-api.md)
- [Anpassa varumärkes-modellen med API: er](customize-brands-model-with-api.md)
- [Anpassa person modellen med hjälp av API: er](customize-person-model-with-api.md)
