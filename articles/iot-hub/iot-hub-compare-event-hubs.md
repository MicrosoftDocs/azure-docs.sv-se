---
title: Jämför Azure IoT Hub till Azure Event Hubs | Microsoft Docs
description: En jämförelse av IoT Hub och Event Hubs Azure-tjänster som markerar funktions skillnader och användnings fall. Jämförelsen omfattar protokoll, enhets hantering, övervakning och fil överföringar som stöds.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: System Architecture'
ms.openlocfilehash: 56bb179c50862f09f1b789c359db97976017bbb5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92142763"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>Ansluta IoT-enheter till Azure: IoT Hub och Event Hubs

Azure tillhandahåller tjänster som har utvecklats specifikt för olika typer av anslutningar och kommunikation för att hjälpa dig att ansluta dina data till molnets kraft. Både Azure IoT Hub och Azure Event Hubs är moln tjänster som kan mata in stora mängder data och bearbeta eller lagra data för affärs insikter. De två tjänsterna liknar de båda tjänsterna och har stöd för inmatning av data med låg latens och hög tillförlitlighet, men de är utformade för olika syfte. IoT Hub utvecklades för att hantera de unika kraven för att ansluta IoT-enheter till Azure-molnet medan Event Hubs har utformats för stor data strömning. Microsoft rekommenderar att du använder Azure IoT Hub för att ansluta IoT-enheter till Azure

Azure IoT Hub är den moln-gateway som ansluter IoT-enheter för att samla in data och driva affärs insikter och automatisering. Dessutom innehåller IoT Hub funktioner som utökningen av relationen mellan dina enheter och dina Server dels system. Dubbelriktade kommunikations funktioner innebär att när du tar emot data från enheter kan du också skicka kommandon och principer tillbaka till enheter. Du kan till exempel använda meddelanden från molnet till enheten för att uppdatera egenskaper eller aktivera enhets hanterings åtgärder. Med kommunikation från moln till enhet kan du också skicka Cloud Intelligence till dina gräns enheter med Azure IoT Edge. Den unika identiteten på enhets nivå som tillhandahålls av IoT Hub hjälper till att bättre skydda din IoT-lösning mot potentiella attacker. 

[Azure Event Hubs](../event-hubs/event-hubs-about.md) är den stora data strömnings tjänsten i Azure. Den är utformad för scenarier med högt dataflöde där kunder kan skicka miljontals begäranden per dag. Event Hubs använder en partitionerad konsumentmodell för att skala ut strömmen och är integrerad i stordata- och analystjänsterna i Azure, till exempel Databricks, Stream Analytics, ADLS och HDInsight. Med funktioner som Event Hubs avbildning och automatisk ökning, är den här tjänsten utformad för att stödja dina Big data-appar och-lösningar. Dessutom använder IoT Hub Event Hubs för vägen för telemetri flödet, så din IoT-lösning har också nytta av den fantastiska kraften i Event Hubs.

För att sammanfatta är båda lösningarna utformade för data inmatning i en enorm skala. Endast IoT Hub ger de omfattande IoT-specifika funktioner som är utformade för att maximera affärs värdet för att ansluta dina IoT-enheter till Azure-molnet.  Om din IoT-resa bara är igång, från och med IoT Hub för att stödja dina data inmatnings scenarier, garanterar du att du har direkt åtkomst till de fullständiga IoT-funktionerna när dina affärs-och teknik behov kräver det.

Följande tabell innehåller information om hur de två nivåerna av IoT Hub jämför med Event Hubs när du utvärderar dem för IoT-funktioner. Mer information om standard-och Basic-nivåerna för IoT Hub finns i [så här väljer du rätt IoT Hub nivå](iot-hub-scaling.md).

| IoT-kapacitet | IoT Hub standard nivå | IoT Hub Basic-nivå | Event Hubs |
| --- | --- | --- | --- |
| Meddelanden från enhet till moln | ![Markera][checkmark] | ![Markera][checkmark] | ![Markera][checkmark] |
| Protokoll: HTTPS, AMQP, AMQP över WebSockets | ![Markera][checkmark] | ![Markera][checkmark] | ![Markera][checkmark] |
| Protokoll: MQTT, MQTT över WebSockets | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Identitet per enhet | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Fil uppladdning från enheter | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Device Provisioning Service | ![Markera][checkmark] | ![Markera][checkmark] |  |
| Meddelanden från moln till enhet | ![Markera][checkmark] |  |  |
| Enhets-och enhets hantering | ![Markera][checkmark] |  |  |
| Enhets strömmar (förhands granskning) | ![Markera][checkmark] |  |  |
| IoT Edge | ![Markera][checkmark] |  |  |

Även om det enda användnings fallet är data inmatningen från enheten till molnet rekommenderar vi starkt att du använder IoT Hub eftersom det tillhandahåller en tjänst som är utformad för IoT-enhets anslutning. 

### <a name="next-steps"></a>Nästa steg

Mer information om funktionerna i IoT Hub finns i [IoT Hub Developer Guide](iot-hub-devguide.md).

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png