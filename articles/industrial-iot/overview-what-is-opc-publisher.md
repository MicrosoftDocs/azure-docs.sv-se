---
title: Microsoft OPC Publisher
description: Den här artikeln innehåller en översikt över OPC Publisher Edge-modulen.
author: v-condav
ms.author: jemorina
ms.service: industrial-iot
ms.topic: conceptual
ms.date: 3/22/2021
ms.openlocfilehash: 6df39c93e9bcfca522ac61a863c87269216cc592
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816178"
---
# <a name="what-is-the-opc-publisher"></a>Vad är OPC Publisher?

OPC Publisher är en Microsoft-produkt med fullständigt stöd som överbryggar avståndet mellan industriella tillgångar och Microsoft Azure molnet. Det gör den genom att ansluta OPC UA-aktiverade tillgångar eller programvara för industriella anslutningar till Microsoft Azure molnet. Den publicerar telemetridata som samlas in för att Azure IoT Hub i olika format, inklusive IEC62541 OPC UA PubSub-standardformat (från version 2.6 och senare). OPC Publisher körs på Azure IoT Edge som en modul eller på vanlig Docker som en container. Eftersom den använder plattformsoberoende .NET-körning körs den inbyggt i både Linux och Windows 10.

OPC Publisher är en referensimplementering som visar hur du:

- Anslut till befintliga OPC UA-servrar.
- Publicera JSON-kodade telemetridata från OPC UA-servrar i OPC UA Pub/Sub-format med hjälp av en JSON-nyttolast till en Azure IoT Hub.

Du kan använda något av de transportprotokoll som Azure IoT Hub klient-SDK stöder, till exempel HTTPS, AMQP och MQTT.

Referensimplementering omfattar följande.

- En OPC *UA-klient* för att ansluta till befintliga OPC UA-servrar som du har i nätverket.
- En OPC *UA-server* på port 62222 som du kan använda för att hantera vad som publiceras och erbjuder IoT Hub direktmetoder för att göra samma sak.

Du kan ladda ned [OPC Publisher-referensimplementering](https://github.com/Azure/iot-edge-opc-publisher) från GitHub.

Programmet implementeras med hjälp av .NET Core-teknik och kan köras på valfri plattform som stöds av .NET Core.

## <a name="what-does-the-opc-publisher-do"></a>Vad gör OPC Publisher?

OPC Publisher implementerar omprövningslogik för att upprätta anslutningar till slutpunkter som inte svarar på ett visst antal keep alive-begäranden. Till exempel om en OPC UA-server slutar svara på grund av ett strömavbrott.

För varje distinkt publiceringsintervall till en OPC UA-server skapar programmet en separat prenumeration där alla noder med det här publiceringsintervallet uppdateras.

OPC Publisher stöder batchbearbetning av data som skickas till IoT Hub för att minska nätverksbelastningen. Den här batchbearbetningen skickar ett paket IoT Hub endast om den konfigurerade paketstorleken har uppnåtts.

Det här programmet använder OPC Foundation OPC UA-referensstacken som NuGet-paket. Licensvillkoren [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) finns i .

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig vad OPC Publisher är kan du komma igång genom att distribuera den:

> [!div class="nextstepaction"]
> [Distribuera OPC Publisher i fristående läge](tutorial-publisher-deploy-opc-publisher-standalone.md)
