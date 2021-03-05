---
title: Skalning av Azure-IoT Hub | Microsoft Docs
description: Så här skalar du din IoT Hub till att stödja dina förväntade meddelande data flöde och önskade funktioner. Innehåller en sammanfattning av det data flöde som stöds för varje nivå och alternativ för horisontell partitionering.
author: wesmc7777
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: wesmc
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: Operations'
ms.openlocfilehash: e786a323cfb1ebacdf7e6693e3ca4299f10aadb3
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216045"
---
# <a name="choose-the-right-iot-hub-tier-for-your-solution"></a>Välja rätt IoT Hub-nivå för din lösning

Varje IoT-lösning är annorlunda, så Azure IoT Hub erbjuder flera alternativ baserat på priser och skala. Den här artikeln är avsedd att hjälpa dig att utvärdera dina IoT Hub behov. För pris information om IoT Hub-nivåer, se [IoT Hub prissättning](https://azure.microsoft.com/pricing/details/iot-hub).

Om du vill bestämma vilken IoT Hub nivå som passar din lösning, kan du ställa in två frågor:

**Vilka funktioner planerar jag att använda?**

Azure IoT Hub erbjuder två nivåer, Basic och standard, som skiljer sig åt i antalet funktioner som de stöder. Om din IoT-lösning är baserad på att samla in data från enheter och analysera den centralt, är Basic-nivån förmodligen rätt för dig. Om du vill använda mer avancerade konfigurationer för att styra IoT-enheter via fjärr anslutning eller distribuera några av dina arbets belastningar på enheterna själva bör du tänka på standard nivån. En detaljerad översikt över vilka funktioner som ingår på varje nivå finns i nivån [Basic och standard](#basic-and-standard-tiers).

**Hur mycket data planerar jag att flytta varje dag?**

Varje IoT Hub nivå är tillgänglig i tre storlekar, baserat på hur mycket data flöde de kan hantera under en och samma dag. De här storlekarna identifieras numeriskt som 1, 2 och 3. Till exempel kan varje enhet på en nivå 1 IoT Hub hantera 400 000 meddelanden per dag, medan en enhet på nivå 3 kan hantera 300 000 000. Om du vill ha mer information om rikt linjerna för data fortsätter du till [meddelande data flöde](#message-throughput).

## <a name="basic-and-standard-tiers"></a>Basic-och standard-nivåer

Standard nivån för IoT Hub aktiverar alla funktioner och krävs för alla IoT-lösningar som vill använda funktionerna i dubbelriktad kommunikation. Nivån Basic möjliggör en delmängd av funktionerna och är avsedd för IoT-lösningar som bara behöver enkelriktad kommunikation från enheter till molnet. Båda nivåerna har samma funktioner för säkerhet och autentisering.

Det går bara att välja en typ av [utgåva](https://azure.microsoft.com/pricing/details/iot-hub/) inom en nivå per IoT Hub. Du kan till exempel skapa en IoT Hub med flera enheter S1, men inte med en blandning av enheter från olika versioner, till exempel S1 och S2.

| Funktion | Basic-nivå | Kostnads fri/standard-nivå |
| ---------- | ---------- | ------------- |
| [Telemetri från enhet till moln](iot-hub-devguide-messaging.md) | Ja | Ja |
| [Identitet per enhet](iot-hub-devguide-identity-registry.md) | Ja | Ja |
| [Meddelanderoutning,](iot-hub-devguide-messages-read-custom.md) [meddelande anrikning](iot-hub-message-enrichments-overview.md)och [Event Grid-integrering](iot-hub-event-grid.md) | Ja | Ja |
| [HTTP-, AMQP-och MQTT-protokoll](iot-hub-devguide-protocols.md) | Ja | Ja |
| [Device Provisioning Service](../iot-dps/about-iot-dps.md) | Ja | Ja |
| [Övervakning och diagnostik](monitor-iot-hub.md) | Ja | Ja |
| [Meddelanden från moln till enhet](iot-hub-devguide-c2d-guidance.md) |   | Yes |
| [Enhets uppflätade](iot-hub-devguide-device-twins.md), [modulens sammanflätade](iot-hub-devguide-module-twins.md)och [enhets hantering](iot-hub-device-management-overview.md) |   | Yes |
| [Enhets strömmar (förhands granskning)](iot-hub-device-streams-overview.md) |   | Yes |
| [Azure IoT Edge](../iot-edge/about-iot-edge.md) |   | Yes |
| [IoT Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) |   | Yes |

IoT Hub erbjuder också en kostnads fri nivå som är avsedd för testning och utvärdering. Den har alla funktioner i standard-nivån, men begränsade meddelande ersättningar. Du kan inte uppgradera från den kostnads fria nivån till antingen Basic eller standard.

## <a name="partitions"></a>Partitioner

Azure IoT-hubbar innehåller många kärn komponenter i [azure Event Hubs](../event-hubs/event-hubs-features.md), inklusive [partitioner](../event-hubs/event-hubs-features.md#partitions). Händelse strömmar för IoT Hub fylls i allmänhet med inkommande telemetridata som rapporteras av olika IoT-enheter. Partitionering av händelse strömmen används för att minska de innehåll som inträffar när den samtidigt läses och skrivs till händelse strömmar.

Du kan välja partitions gräns när IoT Hub skapas och inte kan ändras. Den maximala gränsen för partition för Basic-nivå IoT Hub och standard nivån IoT Hub är 32. De flesta IoT-hubbar behöver bara 4 partitioner. Mer information om hur du fastställer partitionerna finns i Event Hubs vanliga frågor och svar [hur många partitioner behöver jag?](../event-hubs/event-hubs-faq.md#how-many-partitions-do-i-need)

## <a name="tier-upgrade"></a>Uppgradering av nivå

När du har skapat din IoT-hubb kan du uppgradera från Basic-nivån till standard-nivån utan att avbryta dina befintliga åtgärder. Mer information finns i [så här uppgraderar du IoT Hub](iot-hub-upgrade.md).

Konfigurationen av partitionen förblir oförändrad när du migrerar från Basic-nivån till standard nivån.

> [!NOTE]
> Den kostnads fria nivån stöder inte uppgradering till Basic eller standard.

## <a name="iot-hub-rest-apis"></a>IoT Hub REST-API: er

Skillnaden mellan de funktioner som stöds mellan nivåerna Basic och standard i IoT Hub innebär att vissa API-anrop inte fungerar med Basic-nivå nav. Följande tabell visar vilka API: er som är tillgängliga:

| API | Basic-nivå | Kostnads fri/standard-nivå |
| --- | ---------- | ------------- |
| [Ta bort enhet](/javascript/api/azure-iot-digitaltwins-service/registrymanager#deletedevice-string--models-registrymanagerdeletedeviceoptionalparams-) | Ja | Ja |
| [Hämta enhet](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-getdevice) | Ja | Ja |
| [Ta bort modul](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletemodule) | Ja | Ja |
| [Hämta modul](/java/api/com.microsoft.azure.sdk.iot.service.registrymanager.getmodule) | Ja | Ja |
| [Hämta register statistik](/javascript/api/azure-iot-digitaltwins-service/registrymanager#getdevicestatistics-msrest-requestoptionsbase-) | Ja | Ja |
| [Hämta tjänste statistik](/javascript/api/azure-iot-digitaltwins-service/registrymanager#getservicestatistics-msrest-requestoptionsbase-) | Ja | Ja |
| [Skapa eller uppdatera enhet](/javascript/api/azure-iot-digitaltwins-service/registrymanager#createorupdatedevice-string--device--servicecallback-device--) | Ja | Ja |
| [Skapa eller uppdatera modul](/javascript/api/azure-iot-digitaltwins-service/registrymanager#createorupdatemodule-string--string--module--models-registrymanagercreateorupdatemoduleoptionalparams-) | Ja | Ja |
| [Fråga IoT Hub](/dotnet/api/microsoft.azure.devices.registrymanager?view=azure-dotnet) | Ja | Ja |
| [Skapa SAS-URI för fil uppladdning](/rest/api/iothub/device/createfileuploadsasuri) | Ja | Ja |
| [Ta emot meddelande om enhets bindning](/rest/api/iothub/device/receivedeviceboundnotification) | Ja | Ja |
| [Skicka enhets händelse](/rest/api/iothub/device/senddeviceevent) | Ja | Ja |
| Händelse för att skicka modul | Endast AMQP och MQTT | Endast AMQP och MQTT |
| [Uppdatera fil överförings status](/rest/api/iothub/device/updatefileuploadstatus) | Ja | Ja |
| [Åtgärd för Mass enhet](/javascript/api/azure-iot-digitaltwins-service/registrymanager#bulkdevicecrud-exportimportdevice----msrest-requestoptionsbase-) | Ja, förutom IoT Edge funktioner | Yes |
| [Avbryt import av export jobb](/rest/api/iothub/service/jobs/cancelimportexportjob) | Ja | Ja |
| [Skapa import export jobb](/rest/api/iothub/service/jobs/createimportexportjob) | Ja | Ja |
| [Hämta import export jobb](/rest/api/iothub/service/jobs/getimportexportjob) | Ja | Ja |
| [Hämta import export jobb](/rest/api/iothub/service/jobs/getimportexportjobs) | Ja | Ja |
| [Rensa kommando kön](/javascript/api/azure-iot-digitaltwins-service/registrymanager#purgecommandqueue-string--msrest-requestoptionsbase-) |   | Yes |
| [Hämta enhetens dubbla](/java/api/com.microsoft.azure.sdk.iot.device.deviceclient.getdevicetwin) |   | Yes |
| [Hämta modul, dubbla](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-getmoduletwin) |   | Yes |
| [Anropa enhets metod](./iot-hub-devguide-direct-methods.md) |   | Yes |
| [Uppdatera enhet, dubbla](./iot-hub-devguide-device-twins.md) |   | Yes |
| [Uppdatera modul, dubbla](/azure/iot-hub/iot-c-sdk-ref/iothub-devicetwin-h/iothubdevicetwin-updatemoduletwin) |   | Yes |
| [Överge meddelande för enhets bindning](/rest/api/iothub/device/abandondeviceboundnotification) |   | Yes |
| [Slutför meddelande om enhets bindning](/rest/api/iothub/device/completedeviceboundnotification) |   | Yes |
| [Avbryt jobb](/rest/api/media/jobs/canceljob) |   | Yes |
| [Skapa jobb](/rest/api/media/jobs/create) |   | Yes |
| [Hämta jobb](/java/api/com.microsoft.azure.sdk.iot.service.jobs.jobclient.getjob) |   | Yes |
| [Fråga efter jobb](/javascript/api/azure-iot-digitaltwins-service/jobclient#queryjobs-jobclientqueryjobsoptionalparams--servicecallback-queryresult--) |   | Yes |

## <a name="message-throughput"></a>Meddelande data flöde

Det bästa sättet att ändra storlek på en IoT Hub lösning är att utvärdera trafiken per enhet. Ta särskilt hänsyn till det högsta data flöde som krävs för följande kategorier av åtgärder:

* Meddelanden från enheten till molnet
* Meddelanden från moln till enhet
* Identitetsregisteråtgärder

Trafiken mäts för din IoT-hubb per enhet. När du skapar en IoT-hubb väljer du dess nivå och utgåva och anger antalet tillgängliga enheter. Du kan köpa upp till 200 enheter för B1, B2, S1 eller S2 Edition, eller upp till 10 enheter för B3-eller S3-versionen. När din IoT Hub har skapats kan du ändra antalet enheter som är tillgängliga i dess utgåva, uppgradera eller nedgradera mellan versioner inom dess nivå (B1 till B2) eller uppgradera från Basic till standard-nivån (B1 till S1) utan att avbryta dina befintliga åtgärder. Mer information finns i [så här uppgraderar du IoT Hub](iot-hub-upgrade.md).  

Som exempel på varje nivås trafik egenskaper följer enhets-till-moln-meddelanden dessa rikt linjer för data flöde:

| Nivå utgåva | Varaktigt data flöde | Varaktig överförings takt |
| --- | --- | --- |
| B1, S1 |Upp till 1111 KB/minut per enhet<br/>(1,5 GB/dag/enhet) |Medelvärde för 278 meddelanden/minut per enhet<br/>(400 000 meddelanden/dag per enhet) |
| B2, S2 |Upp till 16 MB/minut per enhet<br/>(22,8 GB/dag/enhet) |Medelvärde för 4 167 meddelanden/minut per enhet<br/>(6 000 000 meddelanden/dag per enhet) |
| B3, S3 |Upp till 814 MB/minut per enhet<br/>(1144,4 GB/dag/enhet) |Medelvärde för 208 333 meddelanden/minut per enhet<br/>(300 000 000 meddelanden/dag per enhet) |

Data flöde från enhet till moln är bara ett av de mått du behöver tänka på när du skapar en IoT-lösning. Mer utförlig information finns i [IoT Hub kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

### <a name="identity-registry-operation-throughput"></a>Data flöde för identitets register åtgärd

IoT Hub identitets register åtgärder ska inte vara körnings åtgärder, eftersom de huvudsakligen är relaterade till enhets etablering.

För vissa prestanda siffror i burst, se [IoT Hub kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

## <a name="auto-scale"></a>Automatisk skalning

Om du närmar dig den tillåtna meddelande gränsen på din IoT-hubb kan du använda de här [stegen för att automatiskt skala](https://azure.microsoft.com/resources/samples/iot-hub-dotnet-autoscale/) för att öka en IoT Hub enhet på samma IoT Hub nivå.

## <a name="next-steps"></a>Nästa steg

* Mer information om IoT Hub funktioner och prestanda information finns i [IoT Hub priser](https://azure.microsoft.com/pricing/details/iot-hub) eller [IoT Hub kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

* Om du vill ändra IoT Hub nivån följer du stegen i [Uppgradera IoT Hub](iot-hub-upgrade.md).