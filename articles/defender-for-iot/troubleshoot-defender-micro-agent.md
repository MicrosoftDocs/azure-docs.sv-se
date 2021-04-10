---
title: Fel sökning av Defender IoT Micro-agent (för hands version)
description: Lär dig hur du hanterar oväntade eller oförutsedda fel.
ms.date: 1/24/2021
ms.topic: reference
ms.openlocfilehash: 51550a4d3e5042fed7cadc4eac10a0074e954f19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782460"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Fel sökning av Defender IoT Micro-agent (för hands version)

I händelse av oväntade eller oförutsedda fel kan du försöka lösa problemen med följande fel söknings metoder. Du kan också kontakta produkt teamet för Azure Defender för IoT om du behöver hjälp.   

## <a name="service-status"></a>Tjänststatus 

Så här visar du status för tjänsten: 

1. Kör följande kommando

    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```

1. Kontrol lera att tjänsten är stabil genom att se till att den är `active` och att drift tiden i processen är lämplig.

    :::image type="content" source="media/troubleshooting/active-running.png" alt-text="Se till att tjänsten är stabil genom att kontrol lera att den är aktiv och att drift tiden är lämplig.":::

Om tjänsten är listad som `inactive` använder du följande kommando för att starta tjänsten:

```azurecli
systemctl start defender-iot-micro-agent.service 
```

Du vet att tjänsten kraschar om process drift tiden är för kort. För att lösa det här problemet måste du granska loggarna.

## <a name="review-logs"></a>Granska loggar 

Använd följande kommando för att kontrol lera att tjänsten Defender IoT Micro Agent körs med rot privilegier.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Kontrol lera att tjänsten Defender för IoT Micro Agent körs med rot privilegier.":::

Använd följande kommando för att visa loggarna:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

Om du vill starta om tjänsten använder du följande kommando: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Nästa steg

Ta en titt på [funktions stödet och dra tillbaka](edge-security-module-deprecation.md).
