---
author: baanders
description: ta med fil för Azure Digitals flätas – konfigurera senaste IoT-tillägget
ms.service: digital-twins
ms.topic: include
ms.date: 7/31/2020
ms.author: baanders
ms.openlocfilehash: 984739a728f6ac5e28eeb561e0d7b6ec0485ca13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "90606883"
---
Kör först det här kommandot för att se en lista över alla tillägg som du redan har installerat.

```azurecli-interactive
az extension list
```

Utdata är en matris med alla tillägg som du för närvarande har. Leta efter `"name"` fältet för varje List post om du vill se tilläggets namn.

Använd utdata för att avgöra vilka av följande kommandon som ska köras för installations programmet (du kan köra fler än en).
* Om listan innehåller `azure-iot` : du har redan tillägget. Kör det här kommandot för att kontrol lera att du har den senaste uppdateringen och att det inte finns några tillgängliga uppdateringar:

   ```azurecli-interactive
   az extension update --name azure-iot
   ```

* Om listan **inte** innehåller `azure-iot` : du måste installera tillägget. Använd det här kommandot:

    ```azurecli-interactive
    az extension add --name azure-iot
    ```

* Om listan innehåller `azure-iot-cli-ext` : det här är den äldre versionen av tillägget. Det går bara att installera en version av tillägget i taget, så du bör avinstallera det äldre tillägget. Använd det här kommandot:

   ```azurecli-interactive
   az extension remove --name azure-cli-iot-ext
   ```