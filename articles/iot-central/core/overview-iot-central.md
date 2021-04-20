---
title: Beskrivning av Azure IoT Central | Microsoft Docs
description: Azure IoT Central är en IoT-programplattform som förenklar skapandet av IoT-lösningar och hjälper till att minska belastningen och kostnaden för IoT-hanteringsåtgärder och utveckling. Den här artikeln innehåller en översikt över funktionerna i Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 04/19/2021
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc, contperf-fy21q2
ms.openlocfilehash: 88f59c1b3fc1014cef5035845f1f2e8616bea908
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739916"
---
# <a name="what-is-azure-iot-central"></a>Vad är Azure IoT Central?

IoT Central är en IoT-programplattform som minskar belastningen och kostnaderna för att utveckla, hantera och underhålla IoT-lösningar i företagsklass. Om du väljer att skapa med IoT Central får du möjlighet att fokusera tid, pengar och energi på att omvandla din verksamhet med IoT-data, i stället för att bara underhålla och uppdatera en komplex och ständigt växande IoT-infrastruktur.

Med webbgränssnittet kan du snabbt ansluta enheter, övervaka enhetsförhållanden, skapa regler och hantera miljontals enheter och deras data under hela livscykeln. Dessutom kan du agera på enhetsinsikter genom att utöka IoT-intelligens till verksamhetsapplikationer.

I den här artikeln beskrivs följande IoT Central:

- De vanliga användarroller som är associerade med ett projekt.
- hur du skapar ditt program
- hur du ansluter dina enheter till ditt program
- hur du hanterar ditt program.
- Azure IoT Edge funktioner i IoT Central.
- Ansluta dina Azure IoT Edge till ditt program.

## <a name="user-roles"></a>Användarroller

Dokumentationen IoT Central fyra användarroller som interagerar med ett IoT Central program:

- En _lösningsbyggare_ ansvarar [för](quick-deploy-iot-central.md)att skapa ett [program,](quick-configure-rules.md)konfigurera regler och åtgärder, definiera [integreringar](howto-export-data.md)med andra tjänster och ytterligare anpassa programmet för operatörer och enhetsutvecklare.
- En _operatör_ [hanterar de enheter som](howto-manage-devices.md) är anslutna till programmet.
- En _administratör_ ansvarar för administrativa uppgifter som att hantera [användarroller och behörigheter](howto-administer.md) i programmet.
- En _enhetsutvecklare_ [skapar den kod som körs på en enhet eller](concepts-telemetry-properties-commands.md) en IoT Edge [som](concepts-iot-edge.md) är ansluten till ditt program.

## <a name="create-your-iot-central-application"></a>Skapa ditt IoT Central program

Du kan snabbt distribuera ett nytt IoT Central och sedan anpassa det efter dina specifika krav. Börja med en allmän _programmall_ eller med någon av de branschfokuserade programmallarna:

- [Retail](../retail/overview-iot-central-retail.md)
- [Energi](../energy/overview-iot-central-energy.md)
- [Myndigheter](../government/overview-iot-central-government.md)
- [Sjukvård](../healthcare/overview-iot-central-healthcare.md).

I [snabbstarten Skapa ett](quick-deploy-iot-central.md) nytt program finns en genomgång av hur du skapar ditt första program.

## <a name="connect-devices"></a>Ansluta enheter

När du har skapat ditt program är det första steget att skapa och ansluta enheter. Varje enhet som är ansluten IoT Central använder en _enhetsmall_. En enhetsmall är skissen som definierar egenskaper och beteende för en typ av enhet, till exempel:

- Telemetri som den skickar. Exempel på detta är temperatur och luftfuktighet. Telemetri är strömmande data.
- företagsegenskaper som en operatör kan ändra Exempel är en kundadress och ett datum för senaste service.
- enhetsegenskaper som anges av en enhet och som är skrivskyddade i programmet. Till exempel tillståndet för en valve som antingen öppen eller avstängd.
- Egenskaper, som en operatör anger, som avgör enhetens beteende. Till exempel en måltemperatur för enheten.
- Kommandon som en operatör kan anropa och som körs på en enhet. Till exempel ett kommando för att fjärrstarta om en enhet.

Varje [enhetsmall](howto-set-up-template.md) innehåller:

- En _enhetsmodell_ som beskriver de funktioner som en enhet ska implementera. Enhetens funktioner är:

  - Telemetrin strömmas till IoT Central.
  - De skrivskyddade egenskaper som används för att rapportera tillstånd för att IoT Central.
  - De skrivbara egenskaper som den tar emot från IoT Central att ange enhetstillstånd.
  - Kommandona som anropas från IoT Central.

- Molnegenskaper som inte lagras på enheten.
- Anpassningar, instrumentpaneler och formulär som ingår i ditt IoT Central program.

Du har flera alternativ för att skapa enhetsmallar:

- Utforma enhetsmallen i IoT Central och implementera sedan dess enhetsmodell i enhetskoden.
- Skapa en enhetsmodell med Visual Studio kod och publicera modellen på en lagringsplats. Implementera enhetskoden från modellen och anslut enheten till ditt IoT Central program. IoT Central hittar enhetsmodellen från lagringsplatsen och skapar en enkel enhetsmall åt dig.
- Skapa en enhetsmodell med Visual Studio kod. Implementera din enhetskod från modellen. Importera enhetsmodellen manuellt till ditt IoT Central program och lägg sedan till eventuella molnegenskaper, anpassningar och instrumentpaneler som IoT Central programbehov.

I [snabbstarten Lägg till en](quick-create-simulated-device.md) simulerad enhet finns en genomgång av hur du skapar och ansluter din första enhet.

### <a name="customize-the-ui"></a>Anpassa användargränssnittet

Du kan också anpassa IoT Central för de operatörer som ansvarar för den dagliga användningen av programmet. Du kan bland annat göra följande anpassningar:

- konfigurera anpassade instrumentpaneler som hjälper operatörerna att få insikter och lösa problem snabbare
- konfigurera anpassad analys för att kunna utforska tidsseriedata från anslutna enheter.
- definiera layouten för egenskaper och inställningar i en enhetsmall

## <a name="manage-your-devices"></a>Hantera dina enheter

Som operatör använder du IoT Central för att [hantera enheterna i din](howto-manage-devices.md) IoT Central lösning. Operatörer utför uppgifter som:

- övervaka enheter som är anslutna till programmet
- felsöka och åtgärda problem med enheter
- etablera nya enheter.

Du kan [definiera anpassade regler och åtgärder som fungerar](howto-configure-rules.md) över dataströmning från anslutna enheter. En operatör kan aktivera eller inaktivera dessa regler på enhetsnivå för att kunna styra och automatisera uppgifter i programmet.

Precis som med alla IoT-lösningar som är utformade för att fungera i stor skala är det viktigt med en strukturerad metod för enhetshantering. Det räcker inte att bara ansluta dina enheter till molnet, du måste hålla dina enheter anslutna och felfria. Använd följande IoT Central för att hantera dina enheter under programmets livscykel:

### <a name="dashboards"></a>Instrumentpaneler

Inbyggda [instrumentpaneler ger](./howto-set-up-template.md#generate-default-views) ett anpassningsbart användargränssnitt för att övervaka enhetens hälsotillstånd och telemetri. Börja med en förbyggd instrumentpanel i en [programmall eller](howto-use-app-templates.md) skapa egna instrumentpaneler som skräddarsytts efter dina operatörers behov. Du kan dela instrumentpaneler med alla användare i ditt program eller hålla dem privata.

### <a name="rules-and-actions"></a>Regler och åtgärder

Skapa [anpassade regler](tutorial-create-telemetry-rules.md) baserat på enhetens tillstånd och telemetri för att identifiera enheter som behöver uppmärksammas. Konfigurera åtgärder för att meddela rätt personer och se till att korrigerande åtgärder vidtas inom rimlig tid.

### <a name="jobs"></a>Jobb

[Med](howto-run-a-job.md) jobb kan du tillämpa enstaka uppdateringar eller massuppdateringar på enheter genom att ange egenskaper eller anropa kommandon.

## <a name="integrate-with-other-services"></a>Integrera med andra tjänster

Som programplattform kan IoT Central omvandla dina IoT-data till affärsinsikter som ger användbara resultat. [Regler,](./tutorial-create-telemetry-rules.md) [dataexport](./howto-export-data.md)och [offentliga REST API](/learn/modules/manage-iot-central-apps-with-rest-api/) exempel på hur du kan integrera IoT Central med affärsprogram:

![Hur IoT Central kan transformera dina IoT-data](media/overview-iot-central/transform.png)

Du kan generera affärsinsikter, till exempel fastställa trender för maskineffektivitet eller förutsäga framtida energiförbrukning på fabriksgolvet, genom att skapa anpassade analyspipelines för att bearbeta telemetri från dina enheter och lagra resultatet. Konfigurera dataexporter i ditt IoT Central-program för att exportera telemetri, enhetsegenskapsändringar och enhetsmalländringar till andra tjänster där du kan analysera, lagra och visualisera data med önskade verktyg.

### <a name="build-custom-iot-solutions-and-integrations-with-the-rest-apis"></a>Skapa anpassade IoT-lösningar och integreringar med REST-API:er

Skapa IoT-lösningar som:

- Mobilappar som kan fjärrstyra enheter.
- Anpassade integreringar som gör det möjligt för befintliga verksamhetsapplikationer att interagera med dina IoT-enheter och -data.
- Enhetshanteringsprogram för enhetsmodellering, registrering, hantering och dataåtkomst.

## <a name="administer-your-application"></a>Administrera ditt program

IoT Central är helt värdar hos Microsoft, vilket minskar administrationen för att hantera dina program. Administratörer hanterar åtkomst till ditt program med [användarroller och behörigheter](howto-administer.md).

## <a name="pricing"></a>Priser

Du kan skapa IoT Central med en 7-dagars kostnadsfri utvärderingsversion eller använda en standardprisplan.

- Program som du skapar med *den kostnadsfria* planen är kostnadsfria i sju dagar och har stöd för upp till fem enheter. Du kan konvertera dem till att använda en standardprisplan när som helst innan de upphör att gälla.
- Program som du skapar med *standardplanen* debiteras per enhet. Du kan välja **prisplanen Standard 0,** **Standard 1** eller **Standard 2** där de första två enheterna är kostnadsfria. Läs mer om [IoT Central prissättning.](https://aka.ms/iotcentral-pricing)

## <a name="quotas"></a>Kvoter

Varje Azure-prenumeration har standardkvoter som kan påverka omfånget för din IoT-lösning. För närvarande IoT Central antalet program som du kan distribuera i en prenumeration till 10. Kontakta Microsofts support om du behöver öka [den här gränsen.](https://azure.microsoft.com/support/options/)

## <a name="known-issues"></a>Kända problem

- Kontinuerlig dataexport stöder inte Avro-formatet (inkompatibilitet).
- GeoJSON stöds inte för närvarande.
- Kartpanelen stöds inte för närvarande.
- Matrisschematyper stöds inte.
- Endast SDK för C-enheten och Node.js-SDK:er för enheter och tjänster stöds.
- IoT Central är för närvarande tillgängligt på USA, Europa, Asien och stillahavsområdet, Australien, Storbritannien och Japan.

## <a name="next-steps"></a>Nästa steg

Nu när du har en översikt över IoT Central här är några förslag på nästa steg:

- Om du är enhetsutvecklare och vill gå in på lite kod föreslår vi att du går vidare med att skapa och ansluta ett klientprogram till [ditt Azure IoT Central program](./tutorial-connect-device.md).
- bekanta dig med [Azure IoT Central-användargränssnittet](overview-iot-central-tour.md)
- kom igång genom att [skapa ett Azure IoT Central-program](quick-deploy-iot-central.md).
