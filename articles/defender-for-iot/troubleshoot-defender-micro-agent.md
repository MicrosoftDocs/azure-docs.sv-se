---
title: Fel sökning av Defender IoT Micro-agent (för hands version)
description: Lär dig hur du hanterar oväntade eller oförutsedda fel.
ms.date: 4/5/2021
ms.topic: reference
ms.openlocfilehash: 3d52c4093c01d7e449c68b1c8143249b51f7061a
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011427"
---
# <a name="defender-iot-micro-agent-troubleshooting-preview"></a>Fel sökning av Defender IoT Micro-agent (för hands version)

Om ett oväntat fel inträffar kan du använda de här fel söknings metoderna i ett försök att lösa problemet. Du kan också kontakta produkt teamet för Azure Defender för IoT om du behöver hjälp.   

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

Du vet att tjänsten kraschar om process drift tiden är mindre än två minuter. För att lösa det här problemet måste du [Granska loggarna](#review-the-logs).

## <a name="validate-micro-agent-root-privileges"></a>Verifiera rot privilegier för Micro agent

Använd följande kommando för att kontrol lera att tjänsten Defender IoT Micro Agent körs med rot privilegier.

```azurecli
ps -aux | grep " defender-iot-micro-agent"
```

:::image type="content" source="media/troubleshooting/root-privileges.png" alt-text="Kontrol lera att tjänsten Defender för IoT Micro Agent körs med rot privilegier.":::
## <a name="review-the-logs"></a>Granska loggarna 

Använd följande kommando för att granska loggarna:  

```azurecli
sudo journalctl -u defender-iot-micro-agent | tail -n 200 
```

### <a name="quick-log-review"></a>Snabb loggs granskning

Om det uppstår ett problem när Micro-agenten körs kan du köra Micro-agenten i ett tillfälligt tillstånd, vilket gör att du kan visa loggarna med följande kommando:

```azurecli
sudo systectl stop defender-iot-micro-agent
cd /var/defender_iot_micro_agent/
sudo ./defender_iot_micro_agent
```

## <a name="restart-the-service"></a>Starta om tjänsten

Om du vill starta om tjänsten använder du följande kommando: 

```azurecli
sudo systemctl restart defender-iot-micro-agent  
```

## <a name="next-steps"></a>Nästa steg

Ta en titt på [funktions stödet och dra tillbaka](edge-security-module-deprecation.md).
