---
title: Felsöka Azure IoT-meddelanderoutning
description: Så här utför du fel sökning för Azure IoT-meddelanderoutning
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 3abff5645775d724042acba3ee2461c7cad771a7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103149672"
---
# <a name="troubleshooting-message-routing"></a>Felsöka meddelanderoutning

Den här artikeln innehåller vägledning för övervakning och fel sökning av vanliga problem och lösningar för IoT Hub [meddelanderoutning](iot-hub-devguide-messages-d2c.md).

## <a name="monitoring-message-routing"></a>Övervaka meddelanderoutning

Vi rekommenderar att du övervakar [IoT Hub mått som rör meddelanderoutning och slut punkter](monitor-iot-hub-reference.md#routing-metrics) för att ge dig en översikt över de meddelanden som skickas. Du kan också skapa en diagnostisk inställning för att skicka åtgärder för [ **vägar** i IoT Hub resurs loggar](monitor-iot-hub-reference.md#routes) till Azure Monitor loggar, Event Hubs eller Azure Storage för anpassad bearbetning. Mer information om hur du använder mått, resurs loggar och diagnostiska inställningar finns i [övervaka IoT Hub](monitor-iot-hub.md). En själv studie kurs finns i [Konfigurera och använda mått och resurs loggar med en IoT-hubb](tutorial-use-metrics-and-diags.md).

Vi rekommenderar också att du aktiverar [reserv vägen](iot-hub-devguide-messages-d2c.md#fallback-route) om du vill behålla meddelanden som inte matchar frågan på någon av vägarna. Dessa kan behållas i den [inbyggda slut punkten](iot-hub-devguide-messages-read-builtin.md) för den mängd kvarhållna dagar som kon figurer ATS.

## <a name="top-issues"></a>Främsta problem

Följande är de vanligaste problemen som observeras med meddelanderoutning. Starta fel sökningen genom att klicka på problemet för detaljerade anvisningar.

* [Meddelanden från mina enheter dirigeras inte som förväntat](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Jag slutade plötsligt hämta meddelanden vid den inbyggda Event Hubs slut punkten](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>Meddelanden från mina enheter dirigeras inte som förväntat

Du kan felsöka det här problemet genom att analysera följande.

#### <a name="the-routing-metrics-for-this-endpoint"></a>Operationsföljd måtten för den här slut punkten

Alla [IoT Hub mått som är relaterade till routning](monitor-iot-hub-reference.md#routing-metrics) har prefixet *routning*. Du kan kombinera information från flera mått för att identifiera rotor saken till problem. Använd till exempel leverans av mått **routning** för att identifiera antalet meddelanden som levererades till en slut punkt eller som släppts när de inte matchade frågorna på någon av vägarna och återställnings vägen inaktiverades. Kontrol lera värdet för **vidarebefordring av routning** för att kontrol lera om svars tiden för meddelande leverans är konstant eller ökande. En växande latens kan tyda på ett problem med en angiven slut punkt och vi rekommenderar att du kontrollerar [slut punktens tillstånd](#the-health-of-the-endpoint). Dessa operationsföljd mått har också [dimensioner](monitor-iot-hub-reference.md#metric-dimensions) som anger information om måttet, till exempel typ av slut punkt, ett särskilt slut punkts namn och en orsak till varför meddelandet inte levererades.

#### <a name="the-resource-logs-for-any-operational-issues"></a>Resurs loggarna för eventuella drift problem

Observera [resurs loggarna för **flöden**](monitor-iot-hub-reference.md#routes) för att få mer information om Routning och slut punkts [åtgärder](#operation-names) eller identifiera fel och relevant [Felkod](#common-error-codes) för att förstå problemet ytterligare. Till exempel anger **RouteEvaluationError** i loggen att vägen inte kunde utvärderas på grund av ett problem med meddelande formatet. Använd tipsen för de olika [Åtgärds namnen](#operation-names) för att undvika problemet. När en händelse loggas som ett fel innehåller loggen även mer information om varför utvärderingen misslyckades. Om åtgärds namnet till exempel är **EndpointUnhealthy**, visar [felkoden](#common-error-codes) 403004 att slut punkten hade slut på utrymme.

#### <a name="the-health-of-the-endpoint"></a>Slut punktens tillstånd

Använd REST API [Hämta slut punkts hälsa](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) för att hämta slut punktens [hälso status](iot-hub-devguide-endpoints.md#custom-endpoints) . *Hämta slut punktens hälso* -API innehåller också information om senaste gången ett meddelande skickades till slut punkten, det [senaste kända felet](#last-known-errors-for-iot-hub-routing-endpoints), senast kända fel tid och senaste gången ett sändnings försök gjordes för den här slut punkten. Använd den eventuella minskning som har angetts för det [senaste kända felet](#last-known-errors-for-iot-hub-routing-endpoints).

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Jag slutade plötsligt hämta meddelanden vid den inbyggda slut punkten

Du kan felsöka det här problemet genom att analysera följande.

#### <a name="was-a-new-route-created"></a>Har en ny väg skapats?

När en väg har skapats slutar data flöda till den inbyggda slut punkten, om inte en väg skapas till den slut punkten. För att säkerställa att meddelanden fortsätter att flöda till den inbyggda slut punkten om en ny väg läggs till, konfigurerar du en väg till *händelse* slut punkten. 

#### <a name="was-the-fallback-route-disabled"></a>Är återställnings vägen inaktive rad?

Reserv vägen skickar alla meddelanden som inte uppfyller frågevillkor på någon av de befintliga vägarna till de [inbyggda Event Hubs](iot-hub-devguide-messages-read-builtin.md) (meddelanden/händelser) som är kompatibla med [Event Hubs](../event-hubs/index.yml). Om meddelanderoutning är aktiverat kan du aktivera reserv väg funktionen. Om det inte finns några vägar till den inbyggda slut punkten och en återställnings väg är aktive rad skickas endast meddelanden som inte matchar några frågevillkor i vägar till den inbyggda slut punkten. Om alla befintliga vägar tas bort måste återställnings vägen vara aktive rad för att ta emot alla data vid den inbyggda slut punkten.

Du kan aktivera/inaktivera återställnings vägen på bladet Azure Portal->meddelande cirkulation. Du kan också använda Azure Resource Manager för [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) om du vill använda en anpassad slut punkt för återställnings väg.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>Senaste kända fel för IoT Hub routnings slut punkter

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-resource-logs"></a>Vägar resurs loggar

Här följer de åtgärds namn och felkoder som loggas i [resurs loggarna för flöden](monitor-iot-hub-reference.md#routes).

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Åtgärds namn

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Vanliga felkoder

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du kontakta Azure-experterna på [Microsoft Q&ett och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.
