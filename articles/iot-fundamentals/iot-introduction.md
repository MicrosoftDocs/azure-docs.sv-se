---
title: Introduktion till Sakernas Internet (IoT) i Azure
description: Introduktionen förklarar grunderna i Azure IoT och IoT-tjänster, och innehåller exempel som kan illustrera användningen av IoT.
author: dominicbetts
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: overview
ms.date: 01/15/2020
ms.author: dobett
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f1053062302c9a00cf49cee1cd8de5ca6652b745
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167710"
---
# <a name="what-is-azure-internet-of-things-iot"></a>Vad är Azure Sakernas Internet (IoT)?

Azure Sakernas Internet (IoT) är en samling Microsoft-hanterade molntjänster som ansluter, övervakar och styr miljontals IoT-resurser. I enklare termer består en IoT-lösning av en eller flera IoT-enheter som kommunicerar med en eller flera Server dels tjänster som finns i molnet. 

## <a name="iot-devices"></a>IoT-enheter

En IoT-enhet består vanligt vis av en kretskort med exponerade sensorer som använder WiFi för att ansluta till Internet. Exempel:

* En tryck sensor på en fjärran sluten olje pump.
* Temperatur-och fuktighets sensorer i en luft Konditionerings enhet.
* En accelerometer i en hiss.
* Närvaro sensorer i ett rum.

Det finns en mängd olika enheter som är tillgängliga från olika tillverkare för att bygga din lösning. En lista över enheter som har certifierats för att fungera med Azure IoT Hub finns i [enhets katalogen för Azure Certified för IoT](https://devicecatalog.azure.com). För prototyper kan du använda enheter som till exempel en [MXChip IoT-DevKit](https://microsoft.github.io/azure-iot-developer-kit/) eller en [Raspberry Pi](https://www.raspberrypi.org/). Devkit har inbyggda sensorer för temperatur, tryck, fuktighet och en Gyroscope, accelerometer och magnetometer. Med Raspberry Pi kan du koppla flera olika typer av sensorer. 

Microsoft tillhandahåller [enhets-SDK](../iot-hub/iot-hub-devguide-sdks.md) : er med öppen källkod som du kan använda för att skapa appar som körs på dina enheter. Dessa [SDK: er fören klar och påskyndar](https://azure.microsoft.com/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) utvecklingen av IoT-lösningar.

## <a name="communication"></a>Kommunikation

Vanligt vis skickar IoT-enheter telemetri från sensorer till backend-tjänster i molnet. Andra typer av kommunikation är dock möjligt, till exempel en backend-tjänst som skickar kommandon till dina enheter. Här följer några exempel på enhets-till-moln-och moln-till-enhet-kommunikation:

* En bilkylnings Truck skickar temperatur var 5: e minut till en IoT Hub. 

* Backend-tjänsten skickar ett kommando till en enhet för att ändra den frekvens som den skickar telemetri till för att hjälpa till att diagnostisera ett problem. 

* En enhet skickar aviseringar baserat på de värden som läses från dess sensorer. Till exempel en enhet som övervakar en batch reaktor i en kemisk anläggning, skickar en avisering när temperaturen överskrider ett visst värde.

* Dina enheter skickar information som ska visas på en instrument panel för visning av mänskliga operatörer. Till exempel kan ett kontroll rum i en fin justering Visa temperatur, tryck och Flow-volymer i varje rörledning, vilket gör det möjligt för operatörer att övervaka anläggningen. 

[IoT-enhetens SDK](../iot-hub/iot-hub-devguide-sdks.md) : er och IoT Hub stödja vanliga [kommunikations protokoll](../iot-hub/iot-hub-devguide-protocols.md) som http, MQTT och AMQP.

IoT-enheter har olika egenskaper jämfört med andra klienter, till exempel webbläsare och mobilappar. Enhets-SDK: er hjälper dig att lösa utmaningarna med att ansluta enheter på ett säkert och tillförlitligt sätt till Server dels tjänsten.  IoT-enheter är mer specifikt:

* Ofta inbyggda system utan mänskliga operatörer (till skillnad från en telefon).
* Kan distribueras på avlägsna platser, där fysisk åtkomst är dyr.
* Kan i vissa fall enbart nås via lösningens serverdel.
* Kan ha begränsade ström- och bearbetningsresurser.
* Kan ha oregelbunden, långsam eller dyr nätverksanslutning.
* Kan behöva använda privatägda, anpassade eller branschspecifika programprotokoll.

## <a name="back-end-services"></a>Serverdelstjänster 

I en IoT-lösning tillhandahåller Server dels tjänsten funktioner som:

* Ta emot stora mängder telemetri från dina enheter och bestämma hur telemetridata bearbetas och lagras.
* Analysera telemetri för att ge insikter, oavsett om de skickas i realtid eller i efterhand.
* Skicka kommandon från molnet till en specifik enhet. 
* Konfigurera enheter och kontrol lera vilka enheter som kan ansluta till din infrastruktur.
* Kontrol lera status för enheterna och övervakning av deras aktiviteter.
* Hantering av den inbyggda program varan på dina enheter.

I en lösning för fjärrövervakning för en olje pump Station, använder till exempel Server delen telemetri från pumpar för att identifiera avvikande beteende. När backend-tjänsten identifierar en avvikelse kan den automatiskt skicka tillbaka ett kommando till enheten för att vidta en lämplig åtgärd. Den här processen skapar en automatisk feedback-loop mellan enheten och molnet som ökar effektiviteten för lösningen dramatiskt.

## <a name="azure-iot-examples"></a>Exempel på Azure IoT

I verkliga exempel på hur organisationer använder Azure IoT, se tekniska fallstudier från [Microsoft för IoT](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured). 

En detaljerad beskrivning av IoT-arkitekturen finns i [Microsoft Azure IoT Reference Architecture](/azure/architecture/reference-architectures/iot) (Referensarkitektur för Microsoft Azure IoT).

## <a name="next-steps"></a>Nästa steg

För verkliga affärsfall och vilken arkitektur som använts finns i avsnittet om [Microsoft Azure IoT:s tekniska fallstudier](https://microsoft.github.io/techcasestudies/#technology=IoT&sortBy=featured).

Vissa exempelprojekt som du kan prova med en IoT DevKit finns i [IoT DevKit-katalogen](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/). 

En mer omfattande förklaring av de olika tjänsterna och hur de används finns i [Azure IoT-tjänster och-tekniker](iot-services-and-technologies.md).