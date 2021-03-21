---
title: Moln och regioner för Azure Media Services v3
description: 'Den här artikeln pratar om de URL: er som används för slut punkter och kod för regioner.'
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: reference
ms.date: 10/28/2020
ms.author: inhenkel
ms.openlocfilehash: 74f30ebb766ea34603c0a30455837c47222967c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98954335"
---
# <a name="regional-code-names-and-endpoints"></a>Regionala kodnamn och slutpunkter

### <a name="region-code-name"></a>Region kod namn

När parametern **location** används i ett kommando eller en begäran måste du ange regions kod namnet som **plats** värde. För att hämta kod namnet för den region där ditt konto finns och att ditt anrop ska dirigeras till kan du köra följande rad i Azure CLI.

```azurecli-interactive
az account list-locations
```

När du har kört raden som visas ovan får du en lista över alla Azure-regioner. Navigera till den Azure-region som har det *visnings* namn du söker och använd dess *Name* -värde för parametern **location** .

För Azure-regionen Västra USA 2 (visas nedan) använder du till exempel "westus2" för **plats** parametern.

```json
   {
      "displayName": "West US 2",
      "id": "/subscriptions/00000000-23da-4fce-b59c-f6fb9513eeeb/locations/westus2",
      "latitude": "47.233",
      "longitude": "-119.852",
      "name": "westus2",
      "subscriptionId": null
    }
```

## <a name="endpoints"></a>Slutpunkter  

Följande slut punkter är viktiga att känna till när du ansluter till Media Services konton från olika nationella Azure-moln.

### <a name="global-azure"></a>Global Azure

| Tjänst | Slutpunkt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.azure.com/` |
| Autentisering | `https://login.microsoftonline.com/` |
| Token-Audience | `https://management.core.windows.net/` |

### <a name="azure-government"></a>Azure Government

| Tjänst | Slutpunkt |
| ------- | -------- |
| Azure Resource Manager |  `https://management.usgovcloudapi.net/` |
| Autentisering | `https://login.microsoftonline.us/` |
| Token-Audience | `https://management.core.usgovcloudapi.net/` |

[!INCLUDE [Widevine is not available in the GovCloud region.](./includes/widevine-not-available-govcloud.md)]

### <a name="azure-germany"></a>Azure Tyskland

> [!NOTE]
> Azure Germany-slutpunkter gäller endast för de suveräna molnen i Tyskland.

| Tjänst | Slutpunkt |
| ------- | -------- |
| Azure Resource Manager | `https://management.cloudapi.de/` |
| Autentisering | `https://login.microsoftonline.de/` |
| Token-Audience | `https://management.core.cloudapi.de/`|

### <a name="azure-china-21vianet"></a>Azure Kina 21Vianet

| Tjänst | Slutpunkt |
| ------- | -------- |
| Azure Resource Manager | `https://management.chinacloudapi.cn/` |
| Autentisering | `https://login.chinacloudapi.cn/` |
| Token-Audience |  `https://management.core.chinacloudapi.cn/` |

## <a name="see-also"></a>Se även

* [Azure-regioner](https://azure.microsoft.com/global-infrastructure/regions/)
* [Regionala kodnamn och slutpunkter](azure-regions-code-names.md)
* [Geografiska områden i Azure](https://azure.microsoft.com/global-infrastructure/geographies/)
* [Azure-platser](https://azure.microsoft.com/global-infrastructure/locations/)

## <a name="next-steps"></a>Nästa steg

[Översikt över Media Services v3](media-services-overview.md)
