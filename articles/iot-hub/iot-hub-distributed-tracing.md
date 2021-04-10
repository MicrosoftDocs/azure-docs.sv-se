---
title: 'Lägg till korrelations-ID: n i IoT-meddelanden med distribuerad spårning (för)'
description: Lär dig hur du använder funktionen för distribuerad spårning för att spåra IoT-meddelanden i de Azure-tjänster som används av din lösning.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: fca5c1a0aee6e62890d734e3233177549d932672
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643669"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>Spåra Azure IoT-meddelanden från enhet till moln med distribuerad spårning (för hands version)

Microsoft Azure IoT Hub stöder för närvarande distribuerad spårning som en [förhands gransknings funktion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

IoT Hub är en av de första Azure-tjänsterna som stöder distribuerad spårning. Eftersom fler Azure-tjänster stöder distribuerad spårning kan du spåra IoT-meddelanden i de Azure-tjänster som ingår i din lösning. En bakgrund för distribuerad spårning finns i [Distributed tracing](../azure-monitor/app/distributed-tracing.md).

Genom att aktivera distribuerad spårning för IoT Hub kan du:

- Övervaka flödet av varje meddelande noggrant genom IoT Hub med hjälp av [spårnings kontext](https://github.com/w3c/trace-context). Den här spårnings kontexten innehåller korrelations-ID: n som gör att du kan korrelera händelser från en komponent med händelser från en annan komponent. Den kan användas för en delmängd eller alla IoT-enhets meddelanden med [enhets](iot-hub-devguide-device-twins.md)-till-enhet.
- Logga automatiskt spårnings kontexten till [Azure Monitor loggar](monitor-iot-hub.md).
- Mäta och förstå meddelande flöde och svars tid från enheter till IoT Hub och dirigera slut punkter.
- Börja fundera över hur du vill implementera distribuerad spårning för icke-Azure-tjänster i din IoT-lösning.

I den här artikeln använder du [Azure IoT-enhetens SDK för C](iot-hub-device-sdk-c-intro.md) med distribuerad spårning. Stöd för distribuerad spårning pågår fortfarande för de andra SDK: erna.

## <a name="prerequisites"></a>Förutsättningar

- För hands versionen av Distributed tracing stöds för närvarande bara för IoT-hubbar som skapats i följande regioner:

  - **Europa, norra**
  - **Sydostasien**
  - **USA, västra 2**

- Den här artikeln förutsätter att du är van att skicka telemetri-meddelanden till IoT Hub. Kontrol lera att du har slutfört åtgärden för att [Skicka telemetri C](quickstart-send-telemetry-c.md).

- Registrera en enhet med IoT-hubben (steg tillgängliga i varje snabb start) och anteckna anslutnings strängen.

- Installera den senaste versionen av [Git](https://git-scm.com/download/).

## <a name="configure-iot-hub"></a>Konfigurera IoT Hub

I det här avsnittet konfigurerar du en IoT Hub att logga distribuerade tracing-attribut (korrelations-ID: n och tidsstämplar).

1. Navigera till din IoT Hub i [Azure Portal](https://portal.azure.com/).

1. Rulla ned till avsnittet **övervakning** i den vänstra rutan för din IoT-hubb och klicka på **diagnostikinställningar**.

1. Klicka på **Lägg till diagnostisk inställning**.

1. I fältet **namn** anger du ett namn för en ny diagnostisk inställning. Till exempel **DistributedTracingSettings**.

1. Välj ett eller flera av följande alternativ som avgör var loggningen ska skickas:

    - **Arkivera till ett lagrings konto**: Konfigurera ett lagrings konto som innehåller loggnings informationen.
    - **Strömma till en Event Hub**: Konfigurera en händelsehubben som innehåller loggnings informationen.
    - **Skicka till Log Analytics**: Konfigurera en Log Analytics-arbetsyta som innehåller loggnings informationen.

1. I avsnittet **logg** väljer du de åtgärder som du vill ha loggnings information för.

    Se till att inkludera **DistributedTracing** och konfigurera en **kvarhållning** för hur många dagar du vill att loggningen ska behållas. Logg kvarhållning påverkar lagrings kostnader.

    ![Skärm bild som visar var DistributedTracing-kategorin är för IoT Diagnostic-inställningar](./media/iot-hub-distributed-tracing/diag-logs.png)

1. Klicka på **Spara** för den nya inställningen.

1. Valfritt Om du vill se meddelanden flödet till olika platser ställer du in [routningsregler till minst två olika slut punkter](iot-hub-devguide-messages-d2c.md).

När loggningen är aktive rad, IoT Hub registrerar en logg när ett meddelande som innehåller giltiga spårnings egenskaper påträffas i någon av följande situationer:

- Meddelandena anländer till IoT Hubs Gateway.
- Meddelandet bearbetas av IoT Hub.
- Meddelandet dirigeras till anpassade slut punkter. Routning måste vara aktiverat.

Mer information om dessa loggar och deras scheman finns i [övervaka IoT Hub](monitor-iot-hub.md) och [distribuerad spårning i IoT Hub resurs loggar](monitor-iot-hub-reference.md#distributed-tracing-preview).

## <a name="set-up-device"></a>Konfigurera enhet

I det här avsnittet förbereder du en utvecklings miljö för användning med [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c). Sedan ändrar du ett av exemplen för att aktivera distribuerad spårning på enhetens telemetri-meddelanden.

Dessa anvisningar är till för att bygga exemplet på Windows. Andra miljöer finns i avsnittet [om att kompilera c SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile) eller [färdigförpackade c SDK för plattformsspecifik utveckling](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development).

### <a name="clone-the-source-code-and-initialize"></a>Klona käll koden och initiera

1. Installera [arbets belastningen "Skriv bords utveckling med C++"](/cpp/build/vscpp-step-0-installation?view=vs-2019&preserve-view=true) för Visual Studio 2019. Visual Studio 2017 och 2015 stöds också.

1. Installera [cmake](https://cmake.org/). Kontrol lera att det finns i `PATH` genom att skriva `cmake -version` från en kommando tolk.

1. Öppna en kommandotolk eller Git Bash-gränssnittet. Kör följande kommandon för att klona den senaste versionen av [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub-lagringsplatsen:

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Den här åtgärden kan förväntas ta flera minuter att slutföra.

1. Skapa en `cmake`-underkatalog i rotkatalogen på git-lagringsplatsen och navigera till den mappen. Kör följande kommandon från `azure-iot-sdk-c` katalogen:

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    Om `cmake` du inte hittar din C++-kompilator kan du få build-fel när du kör kommandot ovan. Om det händer ska du försöka köra det här kommandot i [kommandotolken i Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs). 

    När bygget är klart ser de sista utdataraderna ut ungefär som följande utdata:

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>Redigera exemplet skicka telemetri för att aktivera distribuerad spårning

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">Hämta exemplet på GitHub</a>

1. Använd en redigerare för att öppna `azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` käll filen.

1. Hitta deklarationen för konstanten `connectionString`:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    Ersätt värdet för `connectionString` konstanten med enhets anslutnings strängen som du antecknade i avsnittet [Registrera en enhet](./quickstart-send-telemetry-c.md#register-a-device) i snabb starten för att [Skicka telemetri C](./quickstart-send-telemetry-c.md).

1. Ändra `MESSAGE_COUNT` definiera till `5000` :

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. Hitta kodraden som anropar `IoTHubDeviceClient_LL_SetConnectionStatusCallback` för att registrera en callback-funktion för anslutnings status innan loopen för att skicka meddelande. Lägg till kod under raden så som visas nedan för att anropa `IoTHubDeviceClient_LL_EnablePolicyConfiguration` aktivering av distribuerad spårning för enheten:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration`Funktionen aktiverar principer för vissa IoTHub-funktioner som kon figurer ATS via [enhets dubbla](./iot-hub-devguide-device-twins.md). När `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` har Aktiver ATS med kodraden ovan visar spårnings beteendet för enheten distribuerade spårnings ändringar som görs på enheten.

1. Om du vill hålla exempel appen igång utan att använda upp hela din kvot lägger du till en fördröjning i slutet av slingan skicka meddelande:

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>Kompilera och köra

1. Navigera till *iothub_ll_telemetry_sample* projekt katalog från cmake-katalogen ( `azure-iot-sdk-c/cmake` ) som du skapade tidigare och kompilera exemplet:

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. Kör appen. Enheten skickar telemetri som stöder distribuerad spårning.

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. Se till att appen körs. Observera att meddelandet skickas till IoT Hub genom att titta i konsol fönstret.

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>Lösning för klienter från tredje part

Det är **inte enkelt** att förhandsgranska Distributed tracing-funktionen utan att använda C SDK. Därför rekommenderas inte den här metoden.

Först måste du implementera alla primitiva IoT Hub-protokoll i dina meddelanden genom att följa utvecklings guiden [skapa och läsa IoT Hub meddelanden](iot-hub-devguide-messages-construct.md). Redigera sedan protokoll egenskaperna i MQTT/AMQP-meddelanden som ska läggas till `tracestate` som **system egenskap**. Mer specifikt:

* För MQTT lägger du till i `%24.tracestate=timestamp%3d1539243209` meddelande ämnet, där `1539243209` ska ersättas med skapande tiden för meddelandet i formatet för Unix-tidsstämpel. Som exempel kan du se implementeringen [i C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761)
* För AMQP lägger du till `key("tracestate")` och `value("timestamp=1539243209")` som meddelande anteckning. En referens implementering finns [här](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527).

Om du vill kontrol lera procent andelen meddelanden som innehåller den här egenskapen implementerar du logik för att lyssna på molnbaserade händelser, till exempel dubbla uppdateringar.

## <a name="update-sampling-options"></a>Uppdatera alternativ för sampling 

Om du vill ändra procent andelen meddelanden som ska spåras från molnet måste du uppdatera enheten med dubbla. Du kan utföra detta på flera sätt, inklusive JSON-redigeraren i portalen och IoT Hub service SDK. Följande underavsnitt innehåller exempel.

### <a name="update-using-the-portal"></a>Uppdatera med portalen

1. Navigera till din IoT Hub i [Azure Portal](https://portal.azure.com/)och klicka sedan på **IoT-enheter**.

1. Klicka på din enhet.

1. Leta efter **Aktivera distribuerad spårning (för hands version)** och välj sedan **Aktivera**.

    ![Aktivera distribuerad spårning i Azure Portal](./media/iot-hub-distributed-tracing/azure-portal.png)

1. Välj en **samplings frekvens** mellan 0% och 100%.

1. Klicka på **Spara**.

1. Vänta några sekunder och klicka på **Uppdatera**, sedan visas en ikon för synkronisering med en bock om enheten har godkänts av enheten.

1. Gå tillbaka till konsol fönstret för appen telemetri-meddelande. Meddelanden som skickas med `tracestate` i program egenskaperna visas.

    ![Spårnings status](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. Valfritt Ändra samplings frekvensen till ett annat värde och observera den ändring i frekvensen som meddelanden innehåller `tracestate` i program egenskaperna.

### <a name="update-using-azure-iot-hub-for-vs-code"></a>Uppdatera med Azure IoT Hub for VS Code

1. Installera VS Code och installera den senaste versionen av Azure IoT Hub for VS Code [härifrån.](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)

1. Öppna VS Code och [konfigurera IoT Hub anslutnings sträng](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites).

1. Expandera enheten och Sök efter **Distributed tracing-inställning (för hands version)**. Under det klickar du på **Uppdatera Distributed tracing Setting (för hands version)** av undernoden.

    ![Aktivera distribuerad spårning i Azure IoT Hub-tillägget](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. I popup-fönstret väljer du **Aktivera** och trycker sedan på RETUR för att bekräfta 100 som samplings frekvens.

    ![Uppdatera exempel läge](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![Uppdatera samplings frekvens](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>Mass uppdatering för flera enheter

Om du vill uppdatera den distribuerade spårnings samplings konfigurationen för flera enheter använder du [Automatisk enhets konfiguration](./iot-hub-automatic-device-management.md). Se till att du följer det här dubbla schemat:

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| Elementnamn | Krävs | Typ | Beskrivning |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | Ja | Integer | Två läges värden stöds för närvarande för att aktivera och inaktivera sampling. `1` är på och `2` är avstängd. |
| `sampling_rate` | Ja | Integer | Det här värdet är en procent andel. Endast värden från `0` till `100` (inklusive) tillåts.  |

## <a name="query-and-visualize"></a>Fråga och visualisera

Om du vill se alla spår som loggats av en IoT Hub frågar du logg lagret som du valde i diagnostikinställningar. Det här avsnittet går igenom ett par olika alternativ.

### <a name="query-using-log-analytics"></a>Fråga med Log Analytics

Om du har konfigurerat [Log Analytics med resurs loggar](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage)kan du fråga genom att leta efter loggar i `DistributedTracing` kategorin. Den här frågan visar till exempel alla spår som loggats:

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Exempel på loggar som visas i Log Analytics:

| TimeGenerated | OperationName | Kategori | Nivå | CorrelationId | DurationMs | Egenskaper |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633 Z | DiagnosticIoTHubD2C | DistributedTracing | Information | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId": "AZ3166", "messageSize": "96", "callerLocalTimeUtc": "2018-02-22T03:27:28.633 Z", "calleeLocalTimeUtc": "2018-02-22T03:27:28.687 Z"} |
| 2018-02-22T03:28:38.633 Z | DiagnosticIoTHubIngress | DistributedTracing | Information | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled": "false", "parentSpanId": "0144d2590aacd909"} |
| 2018-02-22T03:28:48.633 Z | DiagnosticIoTHubEgress | DistributedTracing | Information | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType": "EventHub", "endpointName": "myEventHub", "parentSpanId": "0144d2590aacd909"} |

Information om de olika typerna av loggar finns i [Azure IoT Hub distribuerade spårnings loggar](monitor-iot-hub-reference.md#distributed-tracing-preview).

### <a name="application-map"></a>Programkarta

För att visualisera flödet av IoT-meddelanden ställer du in exempel appen för program kartan. Exempel appen skickar de distribuerade spårnings loggarna till [program kartan](../azure-monitor/app/app-map.md) med hjälp av en Azure-funktion och en Event Hub.

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">Hämta exemplet på GitHub</a>

Den här bilden nedan visar distribuerad spårning i app-kartor med tre Dirigerings slut punkter:

![IoT-distribuerad spårning i app Map](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Förstå Azure IoT Distributed tracing

### <a name="context"></a>Kontext

Många IoT-lösningar, inklusive vår egen [referens arkitektur](/azure/architecture/reference-architectures/iot) (endast engelska), följer i allmänhet en variant av [mikrotjänstens arkitektur](/azure/architecture/microservices/). När en IoT-lösning växer mer komplex får du ett dussin tals eller fler mikrotjänster. Dessa mikrotjänster kanske inte kommer från Azure. Att ta reda på var IoT-meddelanden släpps eller saktas ned kan bli utmanande. Du har till exempel en IoT-lösning som använder 5 olika Azure-tjänster och 1500 aktiva enheter. Varje enhet skickar 10 enhet-till-moln-meddelanden per sekund (totalt 15 000 meddelanden per sekund), men du märker att din webbapp bara ser 10 000 meddelanden/sekund. Var är problemet? Hur hittar du orsaken?

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>Distribuerat spårnings mönster i arkitektur för mikrotjänster

För att rekonstruera flödet av ett IoT-meddelande mellan olika tjänster bör varje tjänst sprida ett *korrelations-ID* som unikt identifierar meddelandet. När du har samlat in ett centraliserat system kan du se meddelande flödet med korrelations-ID. Den här metoden kallas för [distribuerat spårnings mönster](/azure/architecture/microservices/logging-monitoring#distributed-tracing).

För att ge stöd till ett större införande för distribuerad spårning bidrar Microsoft till [W3C: standard förslag för distribuerad spårning](https://w3c.github.io/trace-context/).

### <a name="iot-hub-support"></a>IoT Hub support

När den har Aktiver ATS följer stöd för distribuerad spårning för IoT Hub följande flöde:

1. Ett meddelande genereras på IoT-enheten.
1. IoT-enheten bestämmer (med hjälp av molnet) att det här meddelandet ska tilldelas med en spårnings kontext.
1. SDK lägger till en `tracestate` i meddelande egenskapen som innehåller tidsstämpeln för att skapa meddelanden.
1. IoT-enheten skickar meddelandet till IoT Hub.
1. Meddelandet anländer till IoT Hub Gateway.
1. IoT Hub letar efter `tracestate` i meddelande egenskaperna och kontrollerar om det har rätt format.
1. I så fall genererar IoT Hub ett globalt unikt `trace-id` för meddelandet, a `span-id` för "hopp" och loggar dem i [IoT Hub distribuerade spårnings loggar](monitor-iot-hub-reference.md#distributed-tracing-preview) under åtgärden `DiagnosticIoTHubD2C` .
1. När meddelande bearbetningen har slutförts genererar IoT Hub en annan `span-id` och loggar den tillsammans med det befintliga `trace-id` under åtgärden `DiagnosticIoTHubIngress` .
1. Om routning har Aktiver ATS för meddelandet IoT Hub skriver den till den anpassade slut punkten och loggar en annan `span-id` med samma `trace-id` under kategori `DiagnosticIoTHubEgress` .
1. Stegen ovan upprepas för varje meddelande som genereras.

## <a name="public-preview-limits-and-considerations"></a>Allmänna begränsningar och överväganden för för hands version

- Förslag för W3C trace context standard är ett arbets utkast som standard.
- För närvarande är det enda utvecklings språk som stöds av klient-SDK: n C.
- Den dubbla kapaciteten från moln till enhet är inte tillgänglig för [IoT Hub Basic-nivån](iot-hub-scaling.md#basic-and-standard-tiers). IoT Hub kommer dock fortfarande att logga till Azure Monitor om det ser en korrekt sammansatt spårnings kontext rubrik.
- För att säkerställa en effektiv åtgärd ger IoT Hub en begränsning för den loggnings frekvens som kan uppstå som en del av den distribuerade spårningen.

## <a name="next-steps"></a>Nästa steg

- Mer information om det allmänna mönstret för spårning i mikrotjänster finns i mönster för [mikrotjänst arkitektur: distribuerad spårning](https://microservices.io/patterns/observability/distributed-tracing.html).
- Information om hur du konfigurerar konfiguration för att tillämpa distribuerade spårnings inställningar på ett stort antal enheter finns i [Konfigurera och övervaka IoT-enheter i stor skala](./iot-hub-automatic-device-management.md).
- Mer information om Azure Monitor finns i [Vad är Azure Monitor?](../azure-monitor/overview.md).
- Mer information om hur du använder Azure Monitor med IoT HUb finns i [övervaka IoT Hub](monitor-iot-hub.md)