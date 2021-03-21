---
title: Autentisering av säkerhets agent (för hands version)
titleSuffix: Azure Defender for IoT
description: Utför mikroagent-autentisering med två möjliga metoder.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/20/2021
ms.topic: conceptual
ms.service: azure
ms.openlocfilehash: 018da32b90c7730f82eaa5aa2cd2b5c7a64719a6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124592"
---
# <a name="micro-agent-authentication-methods-preview"></a>Autentiseringsmetoder för Micro agent (för hands version)

Det finns två alternativ för autentisering med Defender för IoT Micro-agenten: 

- Anslutningssträng 

- Certifikat 

## <a name="authentication-using-a-connection-string"></a>Autentisering med hjälp av en anslutnings sträng 

För att kunna använda en anslutnings sträng måste du lägga till en fil som använder anslutnings strängen som kodats i UTF-8 i katalogen Defender-agenten i en fil med namnet `connection_string.txt` . Exempel:

```azurecli
echo “<connection string>” > connection_string.txt 
```

När du har gjort det bör du starta om tjänsten med det här kommandot.

```azurecli
sudo systemctl restart defender-iot-micro-agent.service
``` 

## <a name="authentication-using-a-certificate"></a>Autentisering med ett certifikat 


Utföra autentisering med ett certifikat: 

1. Placera den PEM offentliga delen av ett certifikat i katalogen för Defender-agenten i en fil med namnet `certificate_public.pem` .
1. Placera den PEM privata nyckeln i katalogen för Defender-agenten i en fil med namnet `certificate_private.pem` .
1. Placera rätt anslutnings sträng i en fil med namnet `connection_string.txt` . Exempel:

    ```azurecli
    HostName=<the host name of the iot hub>;DeviceId=<the id of the device>;ModuleId=<the id of the module>;x509=true 
    ```

    Den här åtgärden anger att Defender-agenten förväntar sig att ett certifikat ska tillhandahållas för autentisering. 

1. Starta om tjänsten med följande kod: 

    ```azurecli
    sudo systemctl restart defender-iot-micro-agent.service 
    ```

## <a name="ensure-the-micro-agent-is-running-correctly"></a>Se till att Micro-agenten körs på rätt sätt 

1. Kör följande kommando: 
    ```azurecli
    systemctl status defender-iot-micro-agent.service 
    ```
1. Kontrol lera att tjänsten är stabil genom att se till att den är **aktiv** och att drift tiden för processen är lämplig: 

    :::image type="content" source="media/concept-security-agent-authentication/active.png" alt-text="Se till att tjänsten är stabil genom att se till att den är aktiv.":::

## <a name="next-steps"></a>Nästa steg

Kontrol lera [säkerhets position – CIS-benchmark](concept-security-posture.md).
