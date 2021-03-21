---
title: Arkitektur koncept i Azure IoT Central | Microsoft Docs
description: Den här artikeln beskriver viktiga begrepp som rör arkitekturen i Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: c2d5310d1a664aa2e22d4241d8066e41d9c82bd1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97796728"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-arkitektur

Den här artikeln ger en översikt över Microsoft Azure IoT Central-arkitekturen.

![Arkitektur på översta nivån](media/concepts-architecture/architecture.png)

## <a name="devices"></a>Enheter

Enheter utbyter data med ditt Azure IoT Central-program. En enhet kan:

- Skicka mått som telemetri.
- Synkronisera inställningar med ditt program.

I Azure IoT Central används en enhetsmall för att ange de data som en enhet kan utbyta med ditt program. Mer information om enhetsspecifika finns i hantering av [metadata](#metadata-management).

Mer information om hur enheter ansluter till ditt Azure IoT Central-program finns i [enhets anslutning](concepts-get-connected.md).

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-enheter

Enheter som har skapats med [Azure IoT-SDK](https://github.com/Azure/azure-iot-sdks): er kan också ansluta [Azure IoT Edge enheter](../../iot-edge/about-iot-edge.md) till ett IoT Central-program. Med IoT Edge kan du köra Cloud Intelligence och anpassad logik direkt på IoT-enheter som hanteras av IoT Central. Med IoT Edge runtime kan du:

- Installerar och uppdaterar arbetsbelastningar på enheten.
- Behåll IoT Edge säkerhets standarder på enheten.
- Säkerställer att IoT Edge-moduler alltid körs.
- Rapporterar modulens hälsa till molnet för fjärrövervakning.
- Hanterar kommunikationen mellan nedströms lövenheter och en IoT Edge-enhet, mellan modulerna på en IoT Edge-enhet, och mellan en IoT Edge-enhet och molnet.

![Azure IoT Central med Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central aktiverar följande funktioner för IoT Edge enheter:

- Enhets mallar för att beskriva funktionerna i en IoT Edge-enhet, till exempel:
  - Distributions Manifestets överförings funktion, som hjälper dig att hantera ett manifest för en flotta av enheter.
  - Moduler som körs på den IoT Edge enheten.
  - Telemetri varje modul skickar.
  - Egenskaperna varje modul rapporterar.
  - De kommandon som varje modul svarar på.
  - Relationerna mellan en IoT Edge gateway-enhet och en underordnad enhet.
  - Moln egenskaper som inte lagras på den IoT Edge enheten.
  - Anpassningar, instrument paneler och formulär som är en del av ditt IoT Central-program.

  Mer information finns i artikeln [anslut Azure IoT Edge enheter till en Azure IoT Central-program](./concepts-iot-edge.md) .

- Möjlighet att etablera IoT Edge enheter i skala med Azure IoT Device Provisioning-tjänsten
- Regler och åtgärder.
- Anpassade instrument paneler och analyser.
- Kontinuerlig data export av telemetri från IoT Edge enheter.

### <a name="iot-edge-device-types"></a>IoT Edge enhets typer

IoT Central klassificerar IoT Edge enhets typer på följande sätt:

- Löv enheter. En IoT Edge enhet kan ha underordnade löv enheter, men enheterna är inte etablerade i IoT Central.
- Gateway-enheter med underordnade enheter. Både gateway-enheter och underordnade enheter tillhandahålls i IoT Central

![IoT Central med IoT Edge översikt](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge mönster

IoT Central stöder följande IoT Edge enhets mönster:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge som löv enhet

![IoT Edge som löv enhet](./media/concepts-architecture/edgeasleafdevice.png)

IoT Edges enheten tillhandahålls i IoT Central och eventuella efterföljande enheter och deras telemetri visas som kommer från den IoT Edge enheten. Underordnade enheter som är anslutna till IoT Edge enheten är inte etablerade i IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge gateway-enhet ansluten till underordnade enheter med identitet

![IoT Edge med underordnad enhets identitet](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

IoT Edges enheten har etablerats i IoT Central och underordnade enheter som är anslutna till IoT Edge enheten. Körnings stöd för etablering av underordnade enheter via gatewayen stöds inte för närvarande.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge gateway-enhet ansluten till underordnade enheter med identiteten som tillhandahålls av IoT Edge Gateway

![IoT Edge med underordnad enhet utan identitet](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

IoT Edges enheten har etablerats i IoT Central och underordnade enheter som är anslutna till IoT Edge enheten. Stöd för körning av gateway som ger identitet till underordnade enheter och etablering av underordnade enheter stöds inte för närvarande. Om du använder en egen modul för identitets översättning kan IoT Central stödja det här mönstret.

## <a name="cloud-gateway"></a>Cloud Gateway

Azure IoT Central använder Azure IoT Hub som en molnbaserad gateway som aktiverar enhets anslutningen. IoT Hub aktiverar:

- Data inmatning i skala i molnet.
- Enhets hantering.
- Skydda enhets anslutningen.

Mer information om IoT Hub finns i [Azure IoT Hub](../../iot-hub/index.yml).

Mer information om enhets anslutning i Azure IoT Central finns i [enhets anslutning](concepts-get-connected.md).

## <a name="data-stores"></a>Datalager

Azure IoT Central lagrar program data i molnet. Program data som lagras innehåller:

- Enhets mallar.
- Enhets identiteter.
- Metadata för enheten.
- Användar-och roll data.

Azure IoT Central använder ett tids serie lager för Mät data som skickas från dina enheter. Tids serie data från enheter som används av Analytics-tjänsten.

## <a name="analytics"></a>Analys

Analytics-tjänsten ansvarar för att skapa anpassade rapporterings data som visas i programmet. En operatör kan [Anpassa den analys](howto-create-analytics.md) som visas i programmet. Analytics-tjänsten bygger på [Azure Time Series Insights](https://azure.microsoft.com/services/time-series-insights/) och bearbetar Mät data som skickas från dina enheter.

## <a name="rules-and-actions"></a>Regler och åtgärder

[Regler och åtgärder](tutorial-create-telemetry-rules.md) fungerar nära varandra för att automatisera uppgifter i programmet. Ett verktyg kan definiera regler baserat på enhets telemetri, till exempel temperaturen som överskrider ett definierat tröskelvärde. Azure IoT Central använder en Stream-processor för att fastställa när regel villkoren är uppfyllda. När ett regel villkor uppfylls utlöser den en åtgärd som definierats av verktyget. En åtgärd kan till exempel skicka ett e-postmeddelande för att meddela en tekniker om att temperaturen i en enhet är för hög.

## <a name="metadata-management"></a>Hantering av metadata

I ett Azure IoT Central-program definierar enhets mallarna beteende och funktion för enhets typer. En mall för kyl skåps enhet anger till exempel vilken telemetri ett kyl skåp skickar till ditt program.

![Arkitektur för mall](media/concepts-architecture/template-architecture.png)

I en mall för IoT Central [enhet](concepts-device-templates.md) innehåller:

- En **enhets modell** för att ange funktioner för en enhet, till exempel den telemetri som skickas, egenskaper som definierar enhetens tillstånd och de kommandon som enheten svarar på. Enhetens kapacitet är indelat i ett eller flera gränssnitt.
- **Moln egenskaper** ange egenskaper IoT Central arkiv för en enhet. Dessa egenskaper lagras endast i IoT Central och skickas aldrig till en enhet.
- **Vyer** anger vilka instrument paneler och formulär som Builder skapar för att låta operatören övervaka och hantera enheterna.
- **Anpassningar** gör att verktyget åsidosätter vissa av definitionerna i enhets modellen för att göra dem mer relevanta för det IoT Central programmet.

Ett program kan ha en eller flera simulerade och riktiga enheter som baseras på varje enhets mall.

## <a name="data-export"></a>Dataexport

I ett Azure IoT Central-program kan du [kontinuerligt exportera dina data](howto-export-data.md) till dina egna Azure Event Hubs-och Azure Service Bus-instanser. Du kan också regelbundet exportera dina data till ditt Azure Blob Storage-konto. IoT Central kan exportera mått, enheter och mallar för enheter.

## <a name="batch-device-updates"></a>Uppdateringar av batch-enhet

I ett Azure IoT Central-program kan du [skapa och köra jobb](howto-run-a-job.md) för att hantera anslutna enheter. Med de här jobben kan du göra Mass uppdateringar av enhets egenskaper eller inställningar eller köra kommandon. Du kan till exempel skapa ett jobb för att öka fläkt hastigheten för flera kylda Vending-datorer.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

Alla IoT Central-program har sitt eget inbyggda RBAC-system. En [administratör kan definiera åtkomst regler](howto-manage-users-roles.md) för ett Azure IoT Central-program med en av de fördefinierade rollerna eller genom att skapa en anpassad roll. Roller avgör vilka delar av programmet som en användare har åtkomst till och vilka åtgärder de kan utföra.

## <a name="security"></a>Säkerhet

Säkerhetsfunktionerna i Azure IoT Central inkluderar:

- Data krypteras under överföring och i vila.
- Autentisering tillhandahålls antingen av Azure Active Directory eller Microsoft-konto. Autentisering med två faktorer stöds.
- Fullständig klient isolering.
- Säkerhet på enhets nivå.

## <a name="ui-shell"></a>UI-gränssnitt

UI Shell är ett modernt, överdrivet HTML5 webbläsarbaserat program.
En administratör kan anpassa programmets användar gränssnitt genom att använda anpassade teman och ändra hjälp länkar så att de pekar på dina egna anpassade hjälp resurser. Mer information om hur du anpassar gränssnittet finns i artikeln [Anpassa Azure IoT Central UI](howto-customize-ui.md) .

En operatör kan skapa anpassade instrument paneler för program. Du kan ha flera instrument paneler som visar olika data och växlar mellan dem.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om arkitekturen i Azure IoT Central, är det föreslagna nästa steg att lära dig om [enhets anslutning](concepts-get-connected.md) i Azure IoT Central.