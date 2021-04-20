---
title: Arkitekturbegrepp i Azure IoT Central | Microsoft Docs
description: Den här artikeln introducerar viktiga begrepp som rör arkitekturen för Azure IoT Central
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: bcda4ca252101ed1505f71a1b5f9fe9a0d8d16b9
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728400"
---
# <a name="azure-iot-central-architecture"></a>Azure IoT Central-arkitektur

Den här artikeln innehåller en översikt över nyckelbegreppen i Azure IoT Central arkitektur.

## <a name="devices"></a>Enheter

Enheter utbyter data med ditt Azure IoT Central program. En enhet kan:

- Skicka mått, till exempel telemetri.
- Synkronisera inställningar med ditt program.

I Azure IoT Central används en enhetsmall för att ange de data som en enhet kan utbyta med ditt program. Mer information om enhetsmallar finns i [Enhetsmallar.](concepts-device-templates.md)

Mer information om hur enheter ansluter till ditt Azure IoT Central finns i [Enhetsanslutning.](concepts-get-connected.md)

## <a name="azure-iot-edge-devices"></a>Azure IoT Edge-enheter

Samt enheter som skapats med [Azure IoT-SDK:er](https://github.com/Azure/azure-iot-sdks)kan du även [ansluta Azure IoT Edge enheter](../../iot-edge/about-iot-edge.md) till ett IoT Central program. IoT Edge kan du köra molnintelligens och anpassad logik direkt på IoT-enheter som hanteras av IoT Central. Med IoT Edge runtime kan du:

- Installerar och uppdaterar arbetsbelastningar på enheten.
- Underhålla IoT Edge säkerhetsstandarder på enheten.
- Säkerställer att IoT Edge-moduler alltid körs.
- Rapporterar modulens hälsa till molnet för fjärrövervakning.
- Hanterar kommunikationen mellan nedströms lövenheter och en IoT Edge-enhet, mellan modulerna på en IoT Edge-enhet, och mellan en IoT Edge-enhet och molnet.

![Azure IoT Central med Azure IoT Edge](./media/concepts-architecture/iotedge.png)

IoT Central aktiverar följande funktioner för för IoT Edge enheter:

- Enhetsmallar för att beskriva funktionerna i en IoT Edge enhet, till exempel:
  - Kapacitet för överföring av distributionsmanifest som hjälper dig att hantera ett manifest för en vagnpark med enheter.
  - Moduler som körs på IoT Edge enhet.
  - Den telemetri som varje modul skickar.
  - Egenskaperna som varje modul rapporterar.
  - Kommandona som varje modul svarar på.
  - Relationerna mellan en IoT Edge gatewayenhet och nedströmsenhet.
  - Molnegenskaper som inte lagras på den IoT Edge enheten.
  - Anpassningar, instrumentpaneler och formulär som ingår i ditt IoT Central program.

  Mer information finns i artikeln Connect Azure IoT Edge devices to an Azure IoT Central application (Ansluta [Azure IoT Edge till ett Azure IoT Central program).](./concepts-iot-edge.md)

- Möjligheten att etablera IoT Edge i stor skala med hjälp av Azure IoT-enhetsetableringstjänsten
- Regler och åtgärder.
- Anpassade instrumentpaneler och analyser.
- Kontinuerlig dataexport av telemetri från IoT Edge enheter.

### <a name="iot-edge-device-types"></a>IoT Edge enhetstyper

IoT Central klassificerar IoT Edge enhetstyper enligt följande:

- Lövenheter. En IoT Edge enhet kan ha nedströms lövenheter, men dessa enheter etableras inte i IoT Central.
- Gateway-enheter med nedströmsenheter. Både gatewayenheten och nedströmsenheter etableras i IoT Central

![IoT Central med IoT Edge översikt](./media/concepts-architecture/gatewayedge.png)

### <a name="iot-edge-patterns"></a>IoT Edge mönster

IoT Central stöder följande IoT Edge enhetsmönster:

#### <a name="iot-edge-as-leaf-device"></a>IoT Edge som lövenhet

![IoT Edge som lövenhet](./media/concepts-architecture/edgeasleafdevice.png)

Enheten IoT Edge etableras i IoT Central nedströmsenheter och deras telemetri representeras som kommer från den IoT Edge enheten. Nedströmsenheter som är anslutna IoT Edge enheten etableras inte i IoT Central.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity"></a>IoT Edge gateway-enhet som är ansluten till underordnade enheter med identitet

![IoT Edge med underordnad enhetsidentitet](./media/concepts-architecture/edgewithdownstreamdeviceidentity.png)

Den IoT Edge enheten etableras i IoT Central tillsammans med nedströmsenheter som är anslutna IoT Edge enheten. Stöd för körning för etablering av nedströmsenheter via gatewayen stöds inte för närvarande.

#### <a name="iot-edge-gateway-device-connected-to-downstream-devices-with-identity-provided-by-the-iot-edge-gateway"></a>IoT Edge gateway-enhet som är ansluten till underordnade enheter med identitet som tillhandahålls av IoT Edge gatewayen

![IoT Edge nedströmsenhet utan identitet](./media/concepts-architecture/edgewithoutdownstreamdeviceidentity.png)

Den IoT Edge enheten etableras i IoT Central tillsammans med nedströmsenheter som är anslutna IoT Edge enheten. Stöd för körning av gateway som tillhandahåller identitet till nedströmsenheter och etablering av nedströmsenheter stöds inte för närvarande. Om du tar med din egen modul för identitetsöversättning kan IoT Central stödja det här mönstret.

## <a name="cloud-gateway"></a>Molngateway

Azure IoT Central använder Azure IoT Hub som en molngateway som möjliggör enhetsanslutning. IoT Hub aktiverar:

- Datainmatning i stor skala i molnet.
- Enhetshantering.
- Säker enhetsanslutning.

Mer information om IoT Hub finns i [Azure IoT Hub](../../iot-hub/index.yml).

Mer information om enhetsanslutningar i Azure IoT Central finns i [Enhetsanslutning.](concepts-get-connected.md)

## <a name="data-stores"></a>Datalager

Azure IoT Central lagrar programdata i molnet. Programdata som lagras omfattar:

- Enhetsmallar.
- Enhetsidentiteter.
- Enhetsmetadata.
- Användar- och rolldata.

Azure IoT Central använder en tidsserielager för de måttdata som skickas från dina enheter. Tidsseriedata från enheter som används av analystjänsten.

## <a name="data-export"></a>Dataexport

I ett Azure IoT Central program kan du [kontinuerligt exportera dina data](howto-export-data.md) till dina Azure Event Hubs och Azure Service Bus instanser. Du kan också regelbundet exportera dina data till ditt Azure Blob Storage-konto. IoT Central kan exportera mått, enheter och enhetsmallar.

## <a name="batch-device-updates"></a>Batch-enhetsuppdateringar

I ett Azure IoT Central program kan du skapa [och köra jobb för att](howto-run-a-job.md) hantera anslutna enheter. Med de här jobben kan du massuppdateringar av enhetsegenskaper eller enhetsinställningar eller köra kommandon. Du kan till exempel skapa ett jobb för att öka fläkthastigheten för flera kylautomater.

## <a name="role-based-access-control-rbac"></a>Rollbaserad åtkomstkontroll (RBAC)

Varje IoT Central har ett eget inbyggt RBAC-system. En [administratör kan definiera åtkomstregler för](howto-manage-users-roles.md) ett Azure IoT Central med någon av de fördefinierade rollerna eller genom att skapa en anpassad roll. Roller avgör vilka områden i programmet som en användare har åtkomst till och vilka åtgärder de kan utföra.

## <a name="security"></a>Säkerhet

Säkerhetsfunktionerna i Azure IoT Central omfattar:

- Data krypteras under överföring och i vila.
- Autentisering tillhandahålls antingen av ett Azure Active Directory eller ett Microsoft-konto. Tvåfaktorautentisering stöds.
- Fullständig klientisolering.
- Säkerhet på enhetsnivå.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om arkitekturen för Azure IoT Central är nästa steg att lära dig mer om [enhetsanslutningar](concepts-get-connected.md) i Azure IoT Central.