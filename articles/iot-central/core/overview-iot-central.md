---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: IoT Central är en värdindelade IoT-appplattform som är säker, kan skalas med dig när verksamheten växer och integreras med dina befintliga företagsappar. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: vishwam
ms.author: vishwams
ms.date: 04/16/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 1ff8243f9f4d070592ae6dc99e7c33472a6a90de
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600450"
---
# <a name="what-is-azure-iot-central"></a>Vad är Azure IoT Central?

IoT Central är en värdindelade IoT-appplattform som är säker, kan skalas med dig när verksamheten växer och integreras med dina befintliga företagsappar. Om du väljer att skapa med IoT Central får du möjlighet att fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt växande IoT-infrastruktur.

IoT Central kan du snabbt ansluta enheter, övervaka enhetsförhållanden, skapa regler och hantera miljontals enheter och deras data under hela livscykeln. Dessutom kan du agera på enhetsinsikter genom att utöka IoT-intelligens till verksamhetsapplikationer.

## <a name="create-your-iot-central-application"></a>Skapa ditt IoT Central program

Du kan snabbt skapa ett IoT Central program och sedan anpassa det efter dina unika krav. Du kan antingen börja med _en_ allmän programmall eller med någon av de branschfokuserade programmallarna [för Detaljhandel,](../retail/overview-iot-central-retail.md) [Energi,](../energy/overview-iot-central-energy.md) [Myndigheter](../government/overview-iot-central-government.md)eller [Hälsovård.](../healthcare/overview-iot-central-healthcare.md)

Se [snabbstarten skapa ett](quick-deploy-iot-central.md) nytt program för en genomgång av hur du skapar ditt första program.

## <a name="connect-your-devices"></a>Anslut dina enheter
När du har skapat ditt program är det första steget att ansluta dina enheter. Se [översikten över enhetsutveckling](./overview-iot-central-developer.md) för en introduktion till att ansluta enheter till IoT Central program.

### <a name="device-templates"></a>Enhetsmallar

Enheter i IoT Central är associerade med en _enhetsmall_. En enhetsmall liknar en skiss: den definierar egenskaper och beteenden för dina enheter, till exempel:

- Telemetri, som representerar mätningar från sensorer, till exempel temperatur eller luftfuktighet.
- Egenskaper, som representerar en enhets beständiga tillstånd. Exempel är tillstånd för en kylande pump eller måltemperatur för en enhet. Du kan deklarera egenskaper som skrivskyddade eller skrivbara. Endast enheter kan uppdatera värdet för en skrivskyddade egenskap. En operatör kan ange värdet för en skrivbar egenskap som ska skickas till en enhet.
- Kommandon, åtgärder som kan utlösas på en enhet, till exempel ett kommando för att fjärrstarta om en enhet.
- Molnegenskaper, som är enhetsmetadata som ska lagras i IoT Central program, till exempel kundadress eller senaste servicedatum.

Mer information [finns i artikeln Skapa en](howto-set-up-template.md) enhetsmall.

### <a name="customize-the-ui"></a>Anpassa användargränssnittet

Du kan anpassa IoT Central för de operatörer som ansvarar för den dagliga användningen av programmet, till exempel:

- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare


## <a name="manage-your-devices"></a>Hantera dina enheter


Med alla IoT-lösningar som är utformade för att fungera i stor skala är det viktigt med en strukturerad metod för enhetshantering. Det räcker inte att bara ansluta dina enheter till molnet, du måste hålla enheterna anslutna och felfria.

Du kan [hantera enheterna med ditt](howto-manage-devices.md) IoT Central för att utföra uppgifter som:

- Övervaka enheterna.
- felsöka och åtgärda problem med enheter
- Utföra massuppdateringar på enheter.

### <a name="dashboards"></a>Instrumentpaneler

Inbyggda [instrumentpaneler ger](./howto-set-up-template.md#generate-default-views) ett anpassningsbart användargränssnitt för att övervaka enhetens hälsotillstånd och telemetri. Börja med en förbyggd instrumentpanel i en [programmall eller](howto-use-app-templates.md) skapa egna instrumentpaneler som skräddarsytts efter dina operatörers behov. Du kan dela instrumentpaneler med alla användare i ditt program eller hålla dem privata.

### <a name="rules-and-actions"></a>Regler och åtgärder

Skapa [anpassade regler](tutorial-create-telemetry-rules.md) baserat på enhetens tillstånd och telemetri för att identifiera enheter som behöver uppmärksammas. Konfigurera åtgärder för att meddela rätt personer och se till att korrigerande åtgärder vidtas inom rimlig tid.

### <a name="jobs"></a>Jobb

[Med](howto-run-a-job.md) jobb kan du tillämpa enstaka uppdateringar eller massuppdateringar på enheter genom att ange egenskaper eller anropa kommandon.

### <a name="analytics"></a>Analys
[Analytics](howto-create-analytics.md) visar omfattande funktioner för att analysera historiska trender och korrelera olika telemetrier från dina enheter.

## <a name="integrate-with-other-services"></a>Integrera med andra tjänster

Som programplattform kan IoT Central omvandla dina IoT-data till affärsinsikter som ger användbara resultat. [Regler,](./tutorial-create-telemetry-rules.md) [dataexport](./howto-export-data.md)och [offentliga REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) exempel på hur du kan integrera IoT Central med affärsprogram:

![Hur IoT Central kan transformera dina IoT-data](media/overview-iot-central/transform.png)

Du kan generera affärsinsikter, till exempel fastställa trender för maskineffektivitet eller förutsäga framtida energiförbrukning på fabriksgolvet, genom att skapa anpassade analyspipelines för att bearbeta telemetri från dina enheter och lagra resultaten. Konfigurera dataexporter i IoT Central program för att exportera data till andra tjänster där du kan analysera, lagra och visualisera dem med önskade verktyg.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Skapa anpassade IoT-lösningar och integreringar med REST-API:er

Skapa IoT-lösningar som:

- Mobilappar som kan fjärrstyra och konfigurera enheter.
- Anpassade integreringar som gör det möjligt för befintliga verksamhetsapplikationer att interagera med dina IoT-enheter och -data.
- Enhetshanteringsprogram för enhetsmodellering, registrering, hantering och dataåtkomst.

## <a name="administer-your-application"></a>Administrera ditt program

IoT Central är helt värd för Microsoft, vilket minskar administrationen av hanteringen av dina program. Administratörer hanterar åtkomst till ditt program med [användarroller och behörigheter](howto-administer.md).

## <a name="pricing"></a>Priser

Du kan skapa ett IoT Central med en kostnadsfri utvärderingsversion på 7 dagar eller använda en standardprisplan.

- Program som du skapar med *den kostnadsfria* planen är kostnadsfria i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem till att använda en standardprisplan när som helst innan de upphör att gälla.
- Program som du skapar *med standardplanen* debiteras per enhet. Du kan välja **prisplanen Standard 0,** **Standard 1** eller **Standard 2** där de första två enheterna är kostnadsfria. Läs mer om [IoT Central prissättning.](https://aka.ms/iotcentral-pricing)

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Central här är några förslag på nästa steg:

- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- Om du är enhetsutvecklare och vill fördjupa dig i kod kan du skapa och ansluta ett klientprogram [till ditt Azure IoT Central program](./tutorial-connect-device.md).
