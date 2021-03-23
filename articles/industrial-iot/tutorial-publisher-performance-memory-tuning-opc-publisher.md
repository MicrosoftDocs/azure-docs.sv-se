---
title: Prestanda-och minnes inställning för Microsoft OPC Publisher
description: I den här självstudien får du lära dig hur du finjusterar prestanda och minne för OPC-utgivaren.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104787844"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Självstudie: justera OPC-utgivarens prestanda och minne

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Justera prestanda
> * Justera meddelande flödet till minnes resurserna

När du kör OPC-utgivare i produktions inställningar måste nätverks prestanda krav (data flöde och svars tid) och minnes resurser beaktas. OPC Publisher exponerar följande kommando rads parametrar som hjälper dig att uppfylla följande krav:

* Kapacitet för meddelandekö ( `mq` för version 2,5 och senare, inte tillgängligt i version 2,6, `om` för version 2,7)
* IoT Hub sändnings intervall ( `si` )
* IoT Hub meddelande storlek ( `ms` )

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>Justera IoT Hub sändnings intervall och IoT Hub meddelande storlek

`mq/om`Parametern styr den övre gränsen för kapaciteten för den interna meddelande kön. Den här kön buffrar alla meddelanden innan de skickas till IoT Hub. Standard storleken för kön är upp till 2 MB för OPC Publisher version 2,5 och under och 4000 IoT Hub-meddelanden för version 2,7 (det vill säga om inställningen för IoT Hub meddelande storleken är 256 KB, kommer storleken på kön att vara upp till 1 GB). Om OPC Publisher inte kan skicka meddelanden till IoT Hub tillräckligt snabbt, ökar antalet objekt i den här kön. Om detta inträffar under test körningar kan ett eller båda av följande utföras för att minimera:

* minska den IoT Hub sändnings intervallet ( `si` )

* öka storleken på IoT Hubs meddelandet (det `ms` högsta värdet kan vara 256 KB)

Om kön fortsätter att växa trots att `si` parametrarna och `ms` har justerats, så kommer den maximala kös kapaciteten att nås och meddelanden går förlorade. Detta beror på att både `si` `ms` parametern och har fysiska gränser och Internet anslutningen mellan OPC-utgivaren och IoT Hub inte är tillräckligt snabb för antalet meddelanden som måste skickas i ett specifikt scenario. I så fall kan du få hjälp med att konfigurera flera parallella OPC-utgivare. `mq/om`Parametern har också den största inverkan på minnes användningen av OPC-utgivaren. 

`si`Parametern TVINGAR OPC-utgivare att skicka meddelanden till IoT Hub med det angivna intervallet. Ett meddelande skickas antingen när den maximala IoT Hub meddelande storleken på 256 KB data är tillgänglig (vilket utlöser sändnings intervallet som ska återställas) eller när den angivna intervall tiden har passerat.

`ms`Parametern aktiverar batching av meddelanden som skickas till IoT Hub. I de flesta nätverks inställningar är svars tiden för att skicka ett enskilt meddelande till IoT Hub hög, jämfört med den tid det tar att överföra nytto lasten. Detta beror huvudsakligen på QoS-krav (Quality of Service), eftersom meddelanden bara bekräftas när de har bearbetats av IoT Hub). Om en fördröjning för data som kommer till IoT Hub är acceptabel, ska OPC-utgivaren konfigureras för att använda den maximala meddelande storleken på 256 KB genom att ange `ms` parametern till 0. Det är också det mest kostnads effektiva sättet att använda OPC Publisher.

Standard konfigurationen skickar data till IoT Hub var 10: e sekund ( `si=10` ) eller när 256 KB IoT Hub meddelande data är tillgängliga ( `ms=0` ). Detta lägger till en maximal fördröjning på 10 sekunder, men har låg sannolikhet för att förlora data på grund av storleken på stora meddelanden. Måttet `monitored item notifications enqueue failure`  i OPC Publisher version 2,5 och senare och `messages lost` i OPC Publisher version 2,7 visar hur många meddelanden som förlorades.

När båda `si` `ms` -och-parametrarna har angetts till 0, skickar OPC Publisher ett meddelande till IoT Hub så snart som data är tillgängliga. Detta resulterar i en genomsnittlig IoT Hub meddelande storlek på bara över 200 byte. Fördelen med den här konfigurationen är dock att OPC-utgivaren skickar data från den anslutna till gången utan fördröjning. Antalet förlorade meddelanden är högt för användnings fall där en stor mängd data måste publiceras och därför rekommenderas detta inte för dessa scenarier.

För att mäta prestanda för OPC-utgivare `di` kan parametern användas för att skriva ut prestanda mått till spårnings loggen inom det angivna intervallet (i sekunder).

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du finjusterar prestanda och minne för OPC-utgivaren kan du titta på OPC Publisher GitHub-lagringsplatsen för ytterligare resurser:

> [!div class="nextstepaction"]
> [OPC Publisher GitHub-lagringsplats](https://github.com/Azure/Industrial-IoT)