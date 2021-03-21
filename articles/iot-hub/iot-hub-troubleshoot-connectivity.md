---
title: Övervaka och Felsök från kopplingar med Azure IoT Hub
description: Lär dig att övervaka och felsöka vanliga fel med enhets anslutning för Azure IoT Hub
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 8bd20e3c7207c75e87a2132fca89906885de2676
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579261"
---
# <a name="monitor-diagnose-and-troubleshoot-disconnects-with-azure-iot-hub"></a>Övervaka, diagnostisera och Felsök från koppling med Azure IoT Hub

Anslutnings problem för IoT-enheter kan vara svåra att felsöka eftersom det finns många möjliga fel punkter. Program logik, fysiska nätverk, protokoll, maskin vara, IoT Hub och andra moln tjänster kan orsaka problem. Det är viktigt att kunna identifiera och hitta källan till ett problem. En IoT-lösning i skala kan dock ha tusentals enheter, så det är inte praktiskt att kontrol lera enskilda enheter manuellt. IoT Hub integreras med två Azure-tjänster som hjälper dig att:

* **Azure Monitor** För att hjälpa dig att identifiera, diagnostisera och felsöka problemen i stor skala, använder du övervaknings funktionerna IoT Hub tillhandahåller Azure Monitor. Detta omfattar att ställa in aviseringar som utlöser meddelanden och åtgärder när från kopplingar sker och hur du konfigurerar loggar som du kan använda för att identifiera de villkor som orsakade från koppling.

* **Azure Event Grid** Använd Azure Event Grid för att prenumerera på enhets anslutning och koppla från händelser som avsänts av IoT Hub för att koppla från en kritisk infrastruktur och per enhet.

I båda fallen är dessa funktioner begränsade till vad IoT Hub kan se, så vi rekommenderar också att du följer de rekommenderade metoderna för att övervaka dina enheter och andra Azure-tjänster.

## <a name="event-grid-vs-azure-monitor"></a>Event Grid jämfört med Azure Monitor

Event Grid tillhandahåller en övervaknings lösning med låg latens per enhet som du kan använda för att spåra enhets anslutningar för kritiska enheter och infrastruktur. Azure Monitor tillhandahåller ett mått, *anslutna enheter*, som du kan använda för att övervaka antalet enheter som är anslutna till din IoT Hub och utlösa en avisering när antalet sjunker under ett statiskt tröskelvärde.

Tänk på följande när du bestämmer om du ska använda Event Grid eller Azure Monitor för ett visst scenario:

* Aviserings fördröjning: IoT Hub anslutnings händelser levereras mycket snabbare genom Event Grid. Detta gör Event Grid ett bättre alternativ för scenarier där snabb meddelande är önskvärt.

* Meddelanden per enhet: Event Grid ger möjlighet att spåra anslutningar och koppla från enskilda enheter. Detta gör Event Grid ett bättre alternativ för scenarier där du behöver övervaka anslutningarna för kritiska enheter.

* Förenklad installation: Azure Monitor mått aviseringar ger en förenklad installations miljö som inte kräver integrering med andra tjänster för att leverera aviseringar via e-post, SMS, röst meddelanden och andra aviseringar.  Med Event Grid måste du integrera med andra Azure-tjänster för att leverera meddelanden. Båda tjänsterna kan integreras med andra tjänster för att utlösa mer komplexa åtgärder.

På grund av sina funktioner för låg latens, per enhet, för produktions miljöer rekommenderar vi starkt att du använder Event Grid för att övervaka anslutningar. Valet är inte exklusivt, du kan använda både Azure Monitor metriska aviseringar och Event Grid. Oavsett vad du väljer för att spåra från koppling kommer du troligen att använda Azure Monitor resurs loggar för att felsöka orsakerna till att oväntad enhet kopplas från. I följande avsnitt beskrivs var och en av dessa alternativ i detalj.

## <a name="event-grid-monitor-device-connect-and-disconnect-events"></a>Event Grid: övervaka händelser för enhets anslutning och från koppling

För att övervaka enhets anslutning och från koppling av händelser i produktion, rekommenderar vi att du prenumererar på [ **DeviceConnected** -och **DeviceDisconnected** -händelser](iot-hub-event-grid.md#event-types) i Event Grid för att utlösa aviseringar och övervaka status för enhets anslutning. Event Grid ger mycket lägre händelse latens än Azure Monitor och du kan övervaka per enhet, i stället för det totala antalet anslutna enheter. Dessa faktorer gör Event Grid den bästa metoden för övervakning av kritiska enheter och infrastruktur.

När du använder Event Grid för att övervaka eller utlösa aviseringar på enheten kopplar från, kontrollerar du att du bygger på ett sätt att filtrera bort de periodiska från kopplingar på grund av att en SAS-token förnyas på enheter som använder Azure IoT SDK: er. Läs mer i [MQTT enhets kopplings beteende med Azure IoT SDK](#mqtt-device-disconnect-behavior-with-azure-iot-sdks): er.

I följande avsnitt kan du läsa mer om hur du övervakar anslutnings händelser för enheter med Event Grid:

* En översikt över hur du använder Event Grid med IoT Hub finns i [reagera på att IoT Hub händelser med event Grid](iot-hub-event-grid.md). Var särskilt uppmärksam på avsnittet [begränsningar för enhet ansluten och frånkopplad enhet](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) .

* En själv studie kurs om att beställa anslutnings händelser för enheter finns i [beställa enhets anslutnings händelser från Azure IoT Hub med Azure Cosmos DB](iot-hub-how-to-order-connection-state-events.md).

* En själv studie kurs om att skicka e-postaviseringar finns i [skicka e-postaviseringar om Azure IoT Hub händelser med event Grid och Logic Apps](../event-grid/publish-iot-hub-events-to-logic-apps.md) i Event Grid-dokumentationen.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: dirigera anslutnings händelser till loggar

IoT Hub genererar kontinuerligt resurs loggar för flera åtgärds kategorier. Om du vill samla in dessa loggdata måste du skapa en diagnostisk inställning för att dirigera den till ett mål där den kan analyseras eller arkiveras. Ett sådant mål är Azure Monitor loggar via en Log Analytics arbets yta ([Se prissättning](https://azure.microsoft.com/pricing/details/log-analytics/)), där du kan analysera data med Kusto-frågor.

[Kategorin resurs loggar](monitor-iot-hub-reference.md#connections) för IoT Hub resurs genererar åtgärder och fel som måste utföras med enhets anslutningar. Följande skärm bild visar en diagnostisk inställning för att dirigera dessa loggar till en Log Analytics arbets yta:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Rekommenderad inställning för att skicka anslutnings loggar till Log Analytics-arbetsyta.":::

Vi rekommenderar att du skapar en diagnostisk inställning så tidigt som möjligt när du har skapat din IoT-hubb, eftersom IoT Hub alltid skapar resurs loggar, samlas de inte in av Azure Monitor förrän du dirigerar dem till ett mål.

Mer information om routning av loggar till ett mål finns i [samling och routning](monitor-iot-hub.md#collection-and-routing). Detaljerade anvisningar om hur du skapar en diagnostisk inställning finns i [själv studie kursen använda mått och loggar](tutorial-use-metrics-and-diags.md).

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnect-at-scale"></a>Azure Monitor: Konfigurera mått varningar för enhets från koppling i skala

Du kan ställa in aviseringar baserat på vilka plattforms mått som genereras av IoT Hub. Med mått aviseringar kan du meddela individer att ett villkor för ränta har inträffat och även utlösa åtgärder som kan svara på det villkoret automatiskt.

Måttet [*anslutna enheter (förhands granskning)*](monitor-iot-hub-reference.md#device-metrics) visar hur många enheter som är anslutna till din IoT Hub. Du kan skapa aviseringar för att utlösa om detta mått sjunker under ett tröskelvärde:

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="Varnings logik inställningar för anslutna enhets mått.":::

Du kan använda Mät regler för mått för att övervaka enheter från kopplings avvikelser i skala. Det vill säga när ett stort antal enheter från en oväntad anslutning bryts. När en sådan förekomst identifieras kan du titta på loggarna för att felsöka problemet. För att övervaka att varje enhet kopplas från och kopplas från för kritiska enheter. Du måste dock använda Event Grid. Event Grid ger också en mer real tids upplevelse än Azures mått.

Mer information om aviseringar med IoT Hub finns [i aviseringar i övervaka IoT Hub](monitor-iot-hub.md#alerts). En genom gång av hur du skapar aviseringar i IoT Hub finns i [själv studie kursen använda mått och loggar](tutorial-use-metrics-and-diags.md). En mer detaljerad översikt över aviseringar finns i [Översikt över aviseringar i Microsoft Azure](../azure-monitor/alerts/alerts-overview.md) i Azure Monitor-dokumentationen.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: Använd loggar för att lösa anslutnings fel

När du identifierar enhets kopplingar, oavsett om det är med Azure Monitor metriska aviseringar eller med Event Grid, kan du använda loggar för att hjälpa till att felsöka orsaken. I det här avsnittet beskrivs hur du söker efter vanliga problem i Azure Monitor loggar. Stegen nedan förutsätter att du redan har skapat en [diagnostisk inställning](#azure-monitor-route-connection-events-to-logs) för att skicka IoT Hub anslutnings loggar till en Log Analytics arbets yta.

När du har skapat en diagnostisk inställning för att dirigera IoT Hub resurs loggar till Azure Monitor loggar följer du stegen nedan för att visa loggarna i Azure Portal.

1. Navigera till din IoT Hub i [Azure Portal](https://portal.azure.com).

1. Under **övervakning** i den vänstra rutan i IoT Hub väljer du **loggar**.

1. Om du vill isolera anslutnings fel loggar för IoT Hub anger du följande fråga i Frågeredigeraren och väljer sedan **Kör**:

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. Om det finns resultat kan du söka efter `OperationName` , `ResultType` (felkod) och `ResultDescription` (fel meddelande) för att få mer information om felet.

   ![Exempel på fel logg](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

När du har identifierat felet följer du rikt linjerna för problemlösning för att få hjälp med de vanligaste felen:

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>MQTT enhet från kopplings beteende med Azure IoT SDK: er

SDK: er för Azure IoT-enheter kopplar från IoT Hub och ansluter sedan igen när de förnyar SAS-token över MQTT-protokollet (och MQTT över WebSockets). I loggar visas detta som en informations enhet från koppling och anslutnings händelser som ibland åtföljs av fel händelser.

Som standard är livs längd-token 60 minuter för alla SDK: er; Det kan dock ändras av utvecklare i några av SDK: erna. I följande tabell sammanfattas token livs längd, token förnyelse och förnyelse av token för var och en av SDK: erna:

| SDK | Livs längd för token | Förnya token | Förnyelse beteende |
|-----|----------|---------------------|---------|
| .NET | 60 minuter, konfigurerbart | 85% av livs längd, konfigurerbar | SDK ansluter och kopplar från med token livs längd plus en Grace-period på 10 minuter. Informations händelser och fel som genereras i loggar. |
| Java | 60 minuter, konfigurerbart | 85% av livs längd, kan inte konfigureras | SDK ansluter och kopplar från med token livs längd plus en Grace-period på 10 minuter. Informations händelser och fel som genereras i loggar. |
| Node.js | 60 minuter, konfigurerbart | konfigurerbara | SDK ansluter och kopplar från vid förnyelse av token. Endast informations händelser genereras i loggar. |
| Python | 60 minuter, kan inte konfigureras | -- | SDK ansluter och kopplar från med token livs längd. |

Följande skärm bilder visar hur token förnyas i Azure Monitor loggar för olika SDK: er. Livs längd och förnyelse tröskeln för token har ändrats från sina standardinställningar som anges.

* .NET-enhets-SDK med en livs längd på 1200 sekund (20 minuter) och förnyelse inställd på 90% av livs längd. från kopplingar sker var 30: e minut:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text="Fel beteende vid förnyelse av token över MQTT i Azure Monitor loggar med .NET SDK.":::

* Java SDK med en livs längd på 300 sekund (5 minut) och standard 85% av livs längd-förnyelsen. Från kopplingar sker var 15: e minut:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Fel beteende vid förnyelse av token över MQTT i Azure Monitor loggar med Java SDK.":::

* Node SDK med en livs längd på 300 sekund (5 minut) och token förnyelse inställt på 3 minuter. Från kopplingar sker vid förnyelse av token. Det finns inte heller några fel, endast informations anslutningar/från kopplings händelser genereras:

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Fel beteende vid förnyelse av token över MQTT i Azure Monitor loggar med Node SDK.":::

Följande fråga användes för att samla in resultaten. Frågan extraherar SDK-namn och version från egenskaps uppsättningen; Mer information finns [i SDK-version i IoT Hub loggar](monitor-iot-hub.md#sdk-version-in-iot-hub-logs).

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

Som en IoT-lösnings utvecklare eller-operatör måste du vara medveten om det här beteendet för att kunna tolka anslutnings-/från kopplings händelser och relaterade fel i loggarna. Om du vill ändra token-livs längd eller förnyelse beteendet för enheter kontrollerar du om enheten implementerar en enhets inställning eller en enhets metod som gör det möjligt.

Om du övervakar enhets anslutningar med Händelsehubben, se till att du bygger på ett sätt att filtrera bort periodiska från kopplingar på grund av förnyelse av SAS-token. till exempel, genom att inte utlösa åtgärder baserat på från koppling så länge händelsen från koppling följs av en anslutnings händelse inom ett visst tidsintervall.

> [!NOTE]
> IoT Hub stöder endast en aktiv MQTT-anslutning per enhet. Alla nya MQTT-anslutningar på uppdrag av samma enhets-ID gör att IoT Hub släppa den befintliga anslutningen.
>
> 400027 ConnectionForcefullyClosedOnNewConnection kommer att loggas in i IoT Hub loggar

## <a name="i-tried-the-steps-but-they-didnt-work"></a>Jag försökte utföra stegen, men de fungerade inte

Om föregående steg inte hjälper kan du prova:

* Om du har åtkomst till de problematiska enheterna, antingen fysiskt eller via fjärr anslutning (som SSH), följer du [fel söknings guiden på enhets sidan](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices) för att fortsätta fel sökningen.

* Kontrol lera att enheterna är **aktiverade** i Azure Portal > din iot Hub > IoT-enheter.

* Om enheten använder MQTT-protokoll kontrollerar du att port 8883 är öppen. Mer information finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

* Få hjälp från [Microsoft Q&en fråge sida för Azure IoT Hub](/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub)eller [Azure-support](https://azure.microsoft.com/support/options/).

Du kan förbättra dokumentationen för alla genom att lämna en kommentar i avsnittet feedback nedan om den här guiden inte hjälper dig.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du löser tillfälliga problem finns i [hantering av tillfälliga fel](/azure/architecture/best-practices/transient-faults).

* Mer information om Azure IoT SDK och hur du hanterar återförsök finns i [hantera anslutningar och Reliable Messaging med hjälp av Azure IoT Hub-enhets-SDK](iot-hub-reliability-features-in-sdks.md#connection-and-retry): er.