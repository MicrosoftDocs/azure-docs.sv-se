---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som fören klar skapandet av IoT-lösningar och hjälper till att minska belastningen och kostnaden för IoT-hanterings åtgärder och utveckling. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 11/23/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 6f7b24c711d99b1127ee77a920b305acb114d20e
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505135"
---
# <a name="what-is-azure-iot-central"></a>Vad är Azure IoT Central?

IoT Central är en IoT-programplattform som minskar belastningen och kostnaderna för att utveckla, hantera och underhålla IoT-lösningar i företagsklass. Genom att välja att skapa med IoT Central får du möjlighet att fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt växande IoT-infrastruktur.

Med webb gränssnittet kan du övervaka enhets villkor, skapa regler och hantera miljon tals enheter och deras data under deras livs cykel. Dessutom kan du agera på enhets insikter genom att utöka IoT Intelligence till branschspecifika program.

Den här artikeln beskriver IoT Central:

- De vanliga användar roller som är associerade med ett projekt.
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.
- Azure IoT Edge funktioner i IoT Central.
- Så här ansluter du dina Azure IoT Edge runtime-baserade enheter till ditt program.

## <a name="user-roles"></a>Användarroller

IoT Central-dokumentationen refererar till fyra användar roller som interagerar med ett IoT Central-program:

- En _Solution Builder_ ansvarar för att [skapa ett program](quick-deploy-iot-central.md), [Konfigurera regler och åtgärder](quick-configure-rules.md), [definiera integreringar med andra tjänster](howto-export-data.md)och ytterligare anpassa programmet för operatörer och enhets utvecklare.
- En _operatör_ [hanterar enheterna](howto-manage-devices.md) som är anslutna till programmet.
- En _administratör_ ansvarar för administrativa uppgifter, till exempel hantering av [användar roller och behörigheter](howto-administer.md) i programmet.
- En _enhets utvecklare_ [skapar den kod som körs på en enhet](concepts-telemetry-properties-commands.md) eller [IoT Edge modul](concepts-iot-edge.md) som är ansluten till ditt program.

## <a name="create-your-iot-central-application"></a>Skapa ditt IoT Central program

Som Solution Builder använder du IoT Central för att skapa en anpassad IoT-lösning för molnet som är värd för din organisation. En anpassad IoT-lösning består vanligtvis av:

- ett molnbaserat program som tar emot telemetri från dina enheter och låter dig hantera dessa enheter.
- flera enheter som kör anpassad kod är anslutna till ditt molnbaserade program.

Du kan snabbt distribuera ett nytt IoT Central-program och sedan anpassa det efter dina specifika krav i webbläsaren. Du kan börja med en allmän _Programmall_ eller med en av de branschspecifika Programmallarna för [detalj handel](../retail/overview-iot-central-retail.md), [energi](../energy/overview-iot-central-energy.md), [myndigheter](../government/overview-iot-central-government.md)eller [hälso vård](../healthcare/overview-iot-central-healthcare.md).

Som Solution Builder använder du webbaserade verktyg för att skapa en _enhets mall_ för de enheter som ansluter till ditt program. En enhets mall är den skiss som definierar egenskaperna och beteendet för en typ av enhet, till exempel:

- Telemetri som skickas. Exempel är temperatur och fuktighet. Telemetri är strömmande data.
- företagsegenskaper som en operatör kan ändra Exempel innefattar en kund adress och senaste service datum.
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet. Till exempel status för en ventil som antingen öppen eller avstängning.
- Egenskaper, som en operator anger, som avgör enhetens beteende. Till exempel en mål temperatur för enheten.
- Kommandon som en operatör kan anropa, som körs på en enhet. Till exempel ett kommando för att fjärrstarta en enhet.

Den här [enhets mal len](howto-set-up-template.md) innehåller:

- En _enhets modell_ som beskriver de funktioner som en enhet ska implementera. Enhets funktionerna är:

  - Den telemetri som den strömmar till IoT Central.
  - De skrivskyddade egenskaperna som används för att rapportera tillstånd till IoT Central.
  - De skrivbara egenskaperna som tas emot från IoT Central för att ange enhets tillstånd.
  - Kommandona som anropas från IoT Central.

- Moln egenskaper som inte lagras på enheten.
- Anpassningar, instrument paneler och formulär som är en del av ditt IoT Central-program.

### <a name="create-device-templates"></a>Skapa mallar för enheter

Som Solution Builder har du flera alternativ för att skapa enhets mallar:

- Utforma enhets mal len i IoT Central och implementera sedan dess enhets modell i enhets koden.
- Skapa en enhets modell med Visual Studio Code och publicera modellen till en lagrings plats. Implementera din enhets kod från modellen och Anslut enheten till IoT Central-programmet. IoT Central hittar enhets modellen från lagrings platsen och skapar en enkel enhets mall åt dig.
- Skapa en enhets modell med Visual Studio Code. Implementera din enhets kod från modellen. Importera enhets modellen manuellt till ditt IoT Central program och Lägg sedan till eventuella moln egenskaper, anpassningar och instrument paneler som ditt IoT Central program behöver.

Som Solution Builder kan du använda IoT Central för att generera kod för test enheter för att validera dina enhets mallar.

Om du är en enhets utvecklare, se [Översikt över IoT Central enhets utveckling](./overview-iot-central-developer.md) för att få en introduktion till att implementera enheter som använder dessa enhetsspecifika mallar.

### <a name="customize-the-ui"></a>Anpassa användargränssnittet

Som Solution Builder kan du också anpassa IoT Central programmets användar gränssnitt för de operatörer som ansvarar för den dagliga användningen av programmet. Anpassningar som en Solution Builder kan göra är:

- definiera layouten för egenskaper och inställningar i en enhetsmall
- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare
- konfigurera anpassad analys för att kunna utforska tidsseriedata från anslutna enheter.

## <a name="manage-your-devices"></a>Hantera dina enheter

Som operatör använder du IoT Central-programmet för att [Hantera enheterna](howto-manage-devices.md) i din IoT Central-lösning. Operatörer utför uppgifter som:

- övervaka enheter som är anslutna till programmet
- felsöka och åtgärda problem med enheter
- etablera nya enheter.

Som Solution Builder kan du [definiera anpassade regler och åtgärder](howto-configure-rules.md) som körs via data strömning från anslutna enheter. En operatör kan aktivera eller inaktivera dessa regler på enhetsnivå för att kunna styra och automatisera uppgifter i programmet.

Med en IoT-lösning som är utformad för att användas i stor skala är det viktigt med en strukturerad metod för enhets hantering. Det räcker inte bara att ansluta dina enheter till molnet, du måste hålla enheterna anslutna och felfria. En operatör kan använda följande IoT Central funktioner för att hantera dina enheter under hela programmets livs cykel:

### <a name="dashboards"></a>Instrumentpaneler

Inbyggda [instrument paneler](./howto-set-up-template.md#generate-default-views) ger ett anpassningsbart gränssnitt för övervakning av enhetens hälso tillstånd och telemetri. Börja med en fördefinierad instrument panel i en [Programmall](howto-use-app-templates.md) eller skapa egna instrument paneler som är anpassade efter dina operatörers behov. Du kan dela instrument paneler med alla användare i ditt program eller hålla dem privata.

### <a name="rules-and-actions"></a>Regler och åtgärder

Bygg [anpassade regler](tutorial-create-telemetry-rules.md) baserat på enhetens tillstånd och telemetri för att identifiera vilka enheter som behöver åtgärdas. Konfigurera åtgärder för att meddela rätt personer och se till att korrigerande åtgärder vidtas i rätt tid.

### <a name="jobs"></a>Jobb

Med [jobb](howto-run-a-job.md) kan du tillämpa enstaka eller Mass uppdateringar av enheter genom att ange egenskaper eller anropa kommandon.

## <a name="integrate-with-other-services"></a>Integrera med andra tjänster

Som en program plattform kan IoT Central omvandla dina IoT-data till affärs insikter som enhets åtgärds resultat. [Regler](./tutorial-create-telemetry-rules.md), [data export](./howto-export-data.md)och de [offentliga REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) är exempel på hur du kan integrera IoT Central med branschspecifika program:

![Hur IoT Central kan transformera dina IoT-data](media/overview-iot-central/transform.png)

Du kan generera affärs insikter, till exempel för att fastställa maskin effektivitets trender eller förutsäga framtida energi förbrukning på en fabriks plan, genom att skapa anpassade analys pipelines för att bearbeta telemetri från dina enheter och lagra resultatet. Konfigurera data exporter i ditt IoT Central program för att exportera telemetri, ändringar av enhets egenskaper och enhets mal len ändras till andra tjänster där du kan analysera, lagra och visualisera data med dina önskade verktyg.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Bygg anpassade IoT-lösningar och integreringar med REST-API: er

Bygg IoT-lösningar som:

- Mobile Companion-appar som kan fjärrkonfigurera och kontrol lera enheter.
- Anpassade integreringar som gör det möjligt för befintliga branschspecifika program att interagera med dina IoT-enheter och-data.
- Enhets hanterings program för enhets modellering, onboarding, hantering och data åtkomst.

## <a name="administer-your-application"></a>Administrera ditt program

IoT Central program är helt värdar för Microsoft, vilket minskar administrations kostnaderna för att hantera dina program. Administratörer hanterar åtkomst till ditt program med [användar roller och behörigheter](howto-administer.md).

## <a name="pricing"></a>Priser

Du kan skapa IoT Central program med en kostnads fri utvärderings version på 7 dagar eller använda en standard pris plan.

- Program som du skapar med hjälp av den *kostnads fria* planen är kostnads fria i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem till att använda en standard pris plan när som helst innan de upphör att gälla.
- Program som du skapar med *standard* planen debiteras per enhet. du kan välja antingen standard pris avtal för **0**, **standard 1** eller **Standard 2** med de två första enheterna som är kostnads fria. Läs mer om [IoT Central prissättning](https://aka.ms/iotcentral-pricing).

## <a name="quotas"></a>Kvoter

Varje Azure-prenumeration har standard kvoter som kan påverka omfånget för din IoT-lösning. IoT Central begränsar för närvarande antalet program som du kan distribuera i en prenumeration på 10. Kontakta [Microsoft-supporten](https://azure.microsoft.com/support/options/)om du behöver öka den här gränsen.

## <a name="known-issues"></a>Kända problem

- Kontinuerlig data export har inte stöd för Avro-formatet (inkompatibilitet).
- Interjson stöds inte för närvarande.
- Kart panelen stöds inte för närvarande.
- Mat ris schema typer stöds inte.
- Endast C-enhetens SDK och Node.js enhets-och tjänst-SDK: er stöds.
- IoT Central är för närvarande tillgängligt på platserna USA, Europa, Asien och stillahavsområdet, Australien, Storbritannien och Japan.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Central finns några förslag på nästa steg:

- Om du är en enhets utvecklare och vill gå in i viss kod, är det föreslagna nästa steg att [skapa och ansluta ett klient program till ditt Azure IoT Central-program](./tutorial-connect-device.md).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
