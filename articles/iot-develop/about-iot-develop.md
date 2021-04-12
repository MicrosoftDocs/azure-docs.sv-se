---
title: Introduktion till Azure IoT-enheter och program utveckling
description: Lär dig hur du använder Azure IoT för att göra inbäddade enhets utveckling och bygga enhets aktiverade moln program.
author: ryanwinter
ms.author: rywinter
ms.service: iot-develop
ms.topic: overview
ms.date: 01/11/2021
ms.openlocfilehash: dd4e53eebe6593db457798f009d3d05ddcbd77b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100655028"
---
# <a name="what-is-azure-iot-device-and-application-development"></a>Vad är Azure IoT-enheter och program utveckling?

Azure IoT är en samling hanterade och plattforms tjänster som ansluter, övervakar och styr dina IoT-enheter. Azure IoT erbjuder utvecklare en omfattande uppsättning alternativ. Alternativen inkluderar enhets plattformar, stöd för moln tjänster, SDK: er och verktyg för att skapa enhets aktiverade moln program.

Den här artikeln visar flera viktiga överväganden för utvecklare som vill komma igång med Azure IoT. Dessa begrepp orienterar dig, som en IoT-enhets utvecklare, till dina Azure IoT-alternativ och hur du börjar. Mer specifikt visar artikeln dessa begrepp:
- [Förstå enhets utvecklings roller](#device-development-roles)
- [Välja maskin vara](#choosing-your-hardware)
- [Välja en SDK](#choosing-an-sdk)
- [Välja anslutnings alternativ](#selecting-connection-options)

## <a name="device-development-roles"></a>Enhets utvecklings roller
I den här artikeln beskrivs två vanliga roller som du kan följa i enhets utvecklare. Som används här är en roll en samling relaterade utvecklings uppgifter. Det är användbart att förstå vilken typ av utvecklings roll som du arbetar i just nu. Din roll påverkar många utvecklings alternativ som du gör.

* **Utveckling av enhets program:** Anpassar sig efter moderna utvecklings metoder, riktar in dig på många av de högre order språken och körs på ett allmänt operativ system, till exempel Windows eller Linux.

* **Inbäddad enhets utveckling:** Beskriver resurser som är begränsade till utvecklings mål resurser. En resurs begränsad enhet används ofta för att minska kostnader per enhet, energi förbrukning eller enhets storlek. Dessa enheter har direkt kontroll över maskin varu plattformen som de körs på.

### <a name="device-application-development"></a>Utveckling av enhets program
Utvecklare av enhets program anpassar befintliga enheter för att ansluta till molnet och integrera dem i sina IoT-lösningar. Dessa enheter har stöd för större språk, till exempel C# eller python, och har ofta stöd för ett stabilt allmänt operativ system, till exempel Windows eller Linux. Vanliga mål enheter är datorer, behållare, Raspberry-Pis och mobila enheter. 

I stället för att utveckla begränsade enheter i stor skala fokuserar dessa utvecklare på att aktivera ett speciellt IoT-scenario som krävs av deras moln lösning. Några av dessa utvecklare kommer också att arbeta med begränsade enheter för sin moln lösning. För utvecklare som arbetar med begränsade enheter, se [inbäddad enhets utvecklings](#embedded-device-development) väg nedan.

> [!TIP]
> Kom igång genom att se de [obegränsade enhets-SDK: erna](about-iot-sdks.md#unconstrained-device-sdks) .

### <a name="embedded-device-development"></a>Utveckling av inbäddade enheter
Inbäddade utvecklings mål begränsade enheter med begränsat minne och bearbetning. Begränsade enheter begränsar vad som kan uppnås jämfört med en traditionell utvecklings plattform.

Inbäddade enheter använder vanligt vis ett operativ system i real tid (återställnings tider) eller inget operativ system alls. Inbäddade enheter har fullständig kontroll över sin maskin vara, på grund av bristen på ett allmänt användnings system. Detta faktum gör inbäddade enheter till ett bra val för real tids system.

De aktuella inbäddade SDK: erna riktar sig mot språket **C** . De inbäddade SDK: erna ger antingen inget operativ system eller stöd för Azure återställnings tider. De är utformade med inbäddade mål i åtanke. Design överväganden omfattar behovet av ett minimalt utrymme och en icke-minnes tilldelnings design.

Om enheten kan köra ett allmänt operativ system rekommenderar vi att du följer [enhets programmets utvecklings](#device-application-development) Sök väg. Det ger en mer omfattande uppsättning utvecklings alternativ.

> [!TIP]
> Se de [begränsade enhets-SDK: erna](about-iot-sdks.md#constrained-device-sdks) för att komma igång.

## <a name="choosing-your-hardware"></a>Välja maskin vara
Azure IoT-enheter är de grundläggande Bygg stenarna i en IoT-lösning och är ansvariga för att kunna iaktta och interagera med deras miljö. Det finns många olika typer av IoT-enheter och det är bra att förstå vilka typer av enheter som finns och hur dessa kan påverka utvecklings processen.

Mer information om skillnaden mellan enhets typer som beskrivs i den här artikeln finns i [om IoT-enhets typer](concepts-iot-device-types.md).

## <a name="choosing-an-sdk"></a>Välja en SDK
Som Azure IoT-utvecklare har du en mängd olika enhets-SDK: er och Azure-tjänst-SDK: er för att hjälpa dig att skapa enhets aktiverade moln program. SDK: er effektiviserar din utvecklings ansträngning och fören klar komplexiteten med att ansluta och hantera enheter. 

Som anges i avsnittet [enhets utvecklings roller](#device-development-roles) finns tre typer av IoT-SDK: er för enhets utveckling:
- Inbäddade enhets-SDK: er (för begränsade enheter)
- Enhets-SDK: er (för att använda högre ordnings språk för att ansluta befintliga enheter till IoT-program)
- Tjänst-SDK: er (för att skapa Azure IoT-lösningar som ansluter enheter till tjänster)

Mer information om hur du väljer en Azure IoT-enhet eller service SDK finns i [Översikt över SDK: er för Azure IoT-enheter](about-iot-sdks.md).

## <a name="selecting-connection-options"></a>Välja anslutnings alternativ
Ett viktigt steg i utvecklings processen är att välja den uppsättning alternativ som du använder för att ansluta och hantera dina enheter. Det finns två viktiga aspekter att tänka på:
- Välja en IoT-programplattform som värd för dina enheter. För Azure IoT innebär detta att du väljer IoT Hub eller IoT Central.
- Välj utvecklarverktyg för att hjälpa dig att ansluta, hantera och övervaka enheter.

Mer information om hur du väljer en plattform och verktyg för program finns i [Översikt: anslutnings alternativ för Azure IoT Device-utvecklare](concepts-overview-connection-options.md).

## <a name="next-steps"></a>Nästa steg
Välj en av följande snabb starts serier som är mest relevanta för din utvecklings roll. De här artiklarna demonstrerar grunderna för att skapa ett Azure IoT-program för att hantera enheter, använda ett SDK, ansluta en enhet och skicka telemetri.  
- För utveckling av enhets program:  [snabb start: skicka telemetri från en enhet till Azure IoT Central](quickstart-send-telemetry-python.md)
- För inbäddad enhets utveckling: [komma igång med Azure IoT Embedded-enhets utveckling](quickstart-device-development.md)
