---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831215"
---
Om du planerar att fortsätta med ytterligare IoT Plug and Play-artiklar kan du behålla och återanvända de resurser som du använde i den här artikeln. Annars kan du ta bort de resurser som du skapade i den här artikeln för att undvika ytterligare kostnader.

Du kan ta bort både Hub och den registrerade enheten samtidigt genom att ta bort hela resurs gruppen med följande Azure CLI-kommando. Använd inte det här kommandot om resurserna delar en resurs grupp med andra resurser som du vill behålla.

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

Om du bara vill ta bort IoT-hubben kör du följande kommando med hjälp av Azure CLI:

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

Om du bara vill ta bort enhets identiteten som du registrerade i IoT Hub kör du följande kommando med hjälp av Azure CLI:

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Du kanske också vill ta bort de klonade exempelfilerna från utvecklings datorn.
