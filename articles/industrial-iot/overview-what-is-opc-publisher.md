---
title: Microsoft OPC Publisher
description: Den här artikeln innehåller en översikt över Edge-modulen OPC.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 3a44bdbadfe6ecd86a1b98fb7002f2d75c23bb6a
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800541"
---
# <a name="what-is-the-opc-publisher"></a>Vad är OPC-utgivaren?

OPC Publisher är en Microsoft-produkt som stöds fullt ut, som har utvecklats i öppet, som förenar mellanrummet mellan industriella till gångar och Microsoft Azure molnet. Det gör det genom att ansluta till OPC UA-aktiverade till gångar eller industriella anslutnings program och publicera telemetridata till Azure IoT Hub i olika format, inklusive IEC62541 OPC UA PubSub standardformat (från version 2,6 och senare).

Den körs på Azure IoT Edge som en modul eller på en enkel Docker som en behållare. Eftersom den använder sig av .NET-plattforms oberoende körning körs den också internt i Linux och Windows 10.

OPC Publisher är en referens implementering som visar hur du:

- Anslut till befintliga OPC UA-servrar.
- Publicera JSON-kodade telemetridata från OPC UA-servrar i OPC UA pub/sub-format med en JSON-nyttolast till Azure IoT Hub.

Du kan använda något av de transport protokoll som Azure IoT Hub-klientens SDK stöder: HTTPS, AMQP och MQTT.

Referens implementeringen innehåller:

- En OPC UA- *klient* för att ansluta till befintliga OPC UA-servrar som du har i nätverket.
- En OPC UA- *Server* på port 62222 som du kan använda för att hantera vad som publiceras och erbjuder IoT Hub direkta metoder för att göra samma sak.

Du kan ladda ned [referens implementeringen OPC Publisher](https://github.com/Azure/iot-edge-opc-publisher) från GitHub.

Programmet implementeras med hjälp av .NET Core Technology och kan köras på alla plattformar som stöds av .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>Vad gör OPC-utgivaren?

OPC Publisher implementerar omprövnings logik för att upprätta anslutningar till slut punkter som inte svarar på ett visst antal Keep Alive-begäranden. Om en OPC UA-server till exempel slutar svara på grund av strömavbrott.

För varje distinkt publicerings intervall till en OPC UA-Server skapar programmet en separat prenumeration där alla noder med publicerings intervallet uppdateras.

OPC Publisher stöder batchbearbetning av data som skickas till IoT Hub för att minska belastningen på nätverket. Den här batchen skickar ett paket till IoT Hub endast om den konfigurerade paket storleken har nåtts.

I det här programmet används OPC Foundation OPC UA-referens stack som NuGet-paket. Se [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) för licens villkoren.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig vad OPC-utgivaren är kan du komma igång genom att distribuera den:

> [!div class="nextstepaction"]
> [Distribuera OPC Publisher i fristående läge](tutorial-publisher-deploy-opc-publisher-standalone.md)
