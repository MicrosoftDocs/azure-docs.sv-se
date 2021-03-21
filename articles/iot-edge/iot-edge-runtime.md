---
title: Lär dig hur körningen hanterar enheter – Azure IoT Edge | Microsoft Docs
description: Lär dig hur IoT Edge runtime hanterar moduler, säkerhet, kommunikation och rapportering på dina enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 74cfe4ba3c92d8d96dd196ef6f612b9ed7c0da9d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496260"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Förstå Azure IoT Edge Runtime och dess arkitektur

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

IoT Edge-körningen är en samling program som omvandlar en enhet till en IoT Edge-enhet. IoT Edges körnings komponenter gör det möjligt för IoT Edge enheter att ta emot kod som ska köras vid gränsen och förmedla resultatet.

IoT Edge runtime ansvarar för följande funktioner på IoT Edge enheter:

* Installerar och uppdaterar arbetsbelastningar på enheten.

* Upprätthåller Azure IoT Edge-säkerhetsstandarder på enheten.

* Se till att [IoT Edge moduler](iot-edge-modules.md) alltid körs.

* Rapporterar modulens hälsa till molnet för fjärrövervakning.

* Hantera kommunikation mellan underordnade enheter och IoT Edge enheter.

* Hantera kommunikation mellan moduler på en IoT Edge enhet.

* Hantera kommunikationen mellan en IoT Edge enhet och molnet.
<!-- 1.2.0 -->
::: moniker range=">=iotedge-2020-11"
* Hantera kommunikation mellan IoT Edge enheter.
::: moniker-end

![Körningen kommunicerar insikter och modulens hälso tillstånd för att IoT Hub](./media/iot-edge-runtime/Pipeline.png)

Ansvars områdena för IoT Edge runtime finns i två kategorier: kommunikation och modul hantering. Dessa två roller utförs av två komponenter som ingår i IoT Edge Runtime. *IoT Edge agent* distribuerar och övervakar modulerna, medan *IoT Edge Hub* ansvarar för kommunikation.

Både IoT Edge-agenten och IoT Edge Hub är moduler, precis som andra moduler som körs på en IoT Edge enhet. De kallas ibland för *körnings moduler*.

## <a name="iot-edge-agent"></a>IoT Edge agent

IoT Edge agenten är en av två moduler som utgör Azure IoT Edge Runtime. De är ansvariga för att instansiera moduler, se till att de fortsätter att köras och rapportera status för modulerna tillbaka till IoT Hub. Den här konfigurations informationen skrivs som en egenskap för den IoT Edge agent-modulen.

[IoT Edge Security daemon](iot-edge-security-manager.md) startar IoT Edge agent vid enhets start. Agenten hämtar sin modul från IoT Hub och kontrollerar distributions manifestet. Distributions manifestet är en JSON-fil som deklarerar moduler som behöver startas.

Varje objekt i distributions manifestet innehåller detaljerad information om en modul och används av IoT Edge-agenten för att kontrol lera modulens livs cykel. Mer information om alla egenskaper som används av IoT Edge agent för att kontrol lera moduler finns i avsnittet om [egenskaperna för IoT Edge-agenten och IoT Edge Hub-modulen](module-edgeagent-edgehub.md).

IoT Edge agenten skickar körnings svar till IoT Hub. Här är en lista över möjliga svar:
  
* 200 – OK
* 400-distributions konfigurationen är felaktig eller ogiltig.
* 417 – enheten har ingen distributions konfigurations uppsättning.
* 412-schema versionen i distributions konfigurationen är ogiltig.
* 406 – den IoT Edge enheten är offline eller skickar inte status rapporter.
* 500 – ett fel uppstod i IoT Edge Runtime.

Mer information om hur du skapar distributions manifest finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

### <a name="security"></a>Säkerhet

IoT Edge agenten spelar en viktig roll i en IoT Edge enhets säkerhet. Den utför till exempel åtgärder som att verifiera en moduls avbildning innan den startas.

Mer information om säkerhets ramverket Azure IoT Edge finns i [IoT Edge Security Manager](iot-edge-security-manager.md).

## <a name="iot-edge-hub"></a>IoT Edge hubb

IoT Edge Hub är den andra modulen som utgör Azure IoT Edge Runtime. Den fungerar som en lokal Proxy för IoT Hub genom att exponera samma protokoll slut punkter som IoT Hub. Den här konsekvensen innebär att klienter kan ansluta till IoT Edge runtime precis som de skulle IoT Hub.

IoT Edge hubben är inte en fullständig version av IoT Hub som körs lokalt. IoT Edge Hub delegerar vissa uppgifter till IoT Hub. IoT Edge Hub hämtar till exempel automatiskt auktoriseringsinformation från IoT Hub på den första anslutningen så att en enhet kan ansluta. När den första anslutningen har upprättats cachelagras auktoriseringsinformationen lokalt av IoT Edge Hub. Framtida anslutningar från den enheten är auktoriserade utan att du behöver hämta auktoriseringsinformation från molnet igen.

### <a name="cloud-communication"></a>Moln kommunikation

För att minska bandbredden som IoT Edge-lösningen använder optimerar IoT Edge Hub hur många faktiska anslutningar som görs till molnet. IoT Edge hub tar logiska anslutningar från moduler eller underordnade enheter och kombinerar dem för en enda fysisk anslutning till molnet. Informationen om den här processen är transparent för resten av lösningen. Klienterna tror att de har sin egen anslutning till molnet även om de skickas över samma anslutning. IoT Edge hubben kan antingen använda AMQP-eller MQTT-protokollet för att kommunicera överordnade med molnet, oberoende av protokoll som används av underordnade enheter. IoT Edge Hub stöder för närvarande bara att kombinera logiska anslutningar till en enda fysisk anslutning genom att använda AMQP som överordnat protokoll och dess funktioner för multiplexering. AMQP är standard protokollet för överordnad.

![IoT Edge Hub är en gateway mellan fysiska enheter och IoT Hub](./media/iot-edge-runtime/gateway-communication.png)

IoT Edge Hub kan avgöra om det är anslutet till IoT Hub. Om anslutningen bryts sparar IoT Edge hubben meddelanden eller dubbla uppdateringar lokalt. När en anslutning har återupprättats synkroniseras alla data. Den plats som används för den här tillfälliga cachen bestäms av en egenskap hos den IoT Edge hubbens modul, dubbla. Storleken på cachen är inte begränsad och kommer att växa så länge enheten har lagrings kapacitet. Mer information finns i [offline-funktioner](offline-capabilities.md).

<!-- <1.1> -->
::: moniker range="iotedge-2018-06"

### <a name="module-communication"></a>Modul kommunikation

IoT Edge hubb underlättar modulen kommunikation. Om du använder IoT Edge hubben som en meddelande utjämning lagras moduler oberoende av varandra. Moduler behöver bara ange de indata som de accepterar meddelanden och de utdata som de skriver meddelanden till. En lösnings utvecklare kan sy ihop dessa indata och utdata, så att modulerna bearbetar data i den ordning som är speciell för lösningen.

![IoT Edge hubb underlättar kommunikation mellan moduler och moduler](./media/iot-edge-runtime/module-endpoints.png)

För att skicka data till IoT Edge Hub anropar en modul metoden SendEventAsync. Det första argumentet anger på vilka utdata som meddelandet ska skickas. Följande pseudocode skickar ett meddelande på **output1**:

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

Om du vill ta emot ett meddelande registrerar du ett återanrop som bearbetar meddelanden som kommer i en speciell Indatatyp. Följande pseudocode registrerar funktionen messageProcessor som ska användas för bearbetning av alla meddelanden som tas emot på **INPUT1**:

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

Mer information om klassen ModuleClient och dess kommunikations metoder finns i API-referensen för ditt prioriterade SDK-språk: [C#](/dotnet/api/microsoft.azure.devices.client.moduleclient), [C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient), [Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)eller [Node.js](/javascript/api/azure-iot-device/moduleclient).

Lösnings utvecklaren ansvarar för att ange regler som avgör hur IoT Edge Hub skickar meddelanden mellan moduler. Routningsregler definieras i molnet och flyttas ned till IoT Edge Hub i sin modul. Samma syntax för IoT Hub vägar används för att definiera vägar mellan moduler i Azure IoT Edge. Mer information finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md).

![Vägar mellan moduler går till IoT Edge hubb](./media/iot-edge-runtime/module-endpoints-routing.png)
::: moniker-end

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

### <a name="local-communication"></a>Lokal kommunikation

IoT Edge hubb underlättar lokal kommunikation. Den aktiverar kommunikation från enhet till modul, modul-till-modul, enhet-till-enhet genom att sammanföra meddelanden om att hantera enheter och moduler oberoende av varandra.

>[!NOTE]
> MQTT Broker-funktionen finns i en offentlig för hands version med IoT Edge version 1,2. Den måste aktive ras explicit.

IoT Edge Hub stöder två Utjämnings metoder:

1. Funktionerna för meddelanderoutning [som stöds av IoT Hub](../iot-hub/iot-hub-devguide-messages-d2c.md) och,
2. En general-purpose MQTT-Broker som uppfyller [MQTT standard v 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html)

#### <a name="using-routing"></a>Använda Routning

Den första Broker-mekanismen utnyttjar samma routningsfunktioner som IoT Hub för att ange hur meddelanden ska skickas mellan enheter eller moduler. De första enheterna eller modulerna anger vilka indata som de accepterar meddelanden och de utdata som de skriver meddelanden till. Sedan kan en lösnings utvecklare dirigera meddelanden mellan en källa, t. ex. utdata och ett mål, t. ex. indata, med potentiella filter.

![Vägar mellan moduler går till IoT Edge hubb](./media/iot-edge-runtime/module-endpoints-routing.png)

Routning kan användas av enheter eller moduler som skapats med Azure IoT-enhetens SDK: er antingen via AMQP eller MQTT-protokollet. Alla meddelande IoT Hub primitiver, t. ex. telemetri, direkta metoder, C2D, dubbla, stöds men kommunikation över användardefinierade ämnen stöds inte.

Mer information om vägar finns i [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md)

#### <a name="using-the-mqtt-broker"></a>Använda MQTT-Broker

Den andra Utjämnings metoden är baserad på en standard MQTT-Broker. MQTT är ett protokoll för Lightweight Message Transfer som garanterar optimala prestanda för resurs begränsade enheter och är en populär publicerings-och prenumerations standard. Enheter eller moduler prenumererar på ämnen för att ta emot meddelanden som publicerats av andra enheter eller moduler. IoT Edge Hub implementerar sin egen MQTT-Broker som följer [specifikationerna i MQTT version 3.1.1](https://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html).

MQTT-Broker möjliggör två ytterligare kommunikations mönster jämfört med Routning: lokal sändning och punkt-till-punkt-kommunikation. Lokal sändning är användbart när en enhet eller modul måste kunna Avisera flera andra enheter eller moduler lokalt. Med punkt-till-punkt-kommunikation kan två IoT Edge enheter eller två IoT-enheter kommunicera lokalt utan att det går att resa till molnet.

![Publicera och prenumerera lokalt med IoT Edge hubb](./media/iot-edge-runtime/local-communnication-mqtt-broker.png)

MQTT-koordinatorn kan användas av enheter eller moduler som skapats med antingen Azure IoT-enhetens SDK: er som kommunicerar via MQTT-protokollet eller MQTT-klienter. Med undantag för C2D, kan alla meddelanden IoT Hub primitiver, t. ex. telemetri, direkta metoder, dubbla metoder. IoT Hub Special ämnen som används av IoT Hub-primitiver stöds och är användardefinierade ämnen.
Det här avsnittet kan vara ett IoT Hub Special ämne eller ett användardefinierat ämne.

Till skillnad från routnings mekanismen är sortering av meddelanden endast bästa och inte garanterad och filtrering av meddelanden stöds inte av Broker. Bristen på dessa funktioner gör det dock snabbare att MQTT-Broker än routning.

Mer information om MQTT-Broker finns i [publicera och prenumerera med IoT Edge](how-to-publish-subscribe.md)

#### <a name="comparison-between-brokering-mechanisms"></a>Jämförelse mellan Utjämnings metoder

Här är de funktioner som är tillgängliga med varje mekanism för utjämning:

|Funktioner  | Routning  | MQTT-Broker  |
|---------|---------|---------|
|D2C telemetri    |     &#10004;    |         |
|Lokal telemetri     |     &#10004;    |    &#10004;     |
|DirectMethods     |    &#10004;     |    &#10004;     |
|Noble     |    &#10004;     |    &#10004;     |
|C2D för enheter     |   &#10004;      |         |
|Ordna profiler     |    &#10004;     |         |
|Filtrering     |     &#10004;    |         |
|Användardefinierade ämnen     |         |    &#10004;     |
|Enhet till enhet     |         |    &#10004;     |
|Lokal sändning     |         |    &#10004;     |
|Prestanda     |         |    &#10004;     |

### <a name="connecting-to-the-iot-edge-hub"></a>Ansluta till IoT Edge Hub

IoT Edge Hub accepterar anslutningar från enhets-eller modulens klienter, antingen via MQTT-protokollet eller AMQP-protokollet.

>[!NOTE]
> IoT Edge Hub stöder klienter som ansluter med MQTT eller AMQP. Den stöder inte klienter som använder HTTP.

När en klient ansluter till IoT Edge Hub händer följande:

1. Om Transport Layer Security (TLS) används (rekommenderas) skapas en TLS-kanal för att upprätta en krypterad kommunikation mellan klienten och IoT Edge hubben.
2. Autentiseringsinformation skickas från klienten till IoT Edge Hub för att identifiera sig själv.
3. IoT Edge Hub godkänner eller avvisar anslutningen baserat på dess auktoriseringsprincip.

#### <a name="secure-connections-tls"></a>Säkra anslutningar (TLS)

Som standard accepterar IoT Edge Hub bara anslutningar som skyddas med Transport Layer Security (TLS), t. ex. krypterade anslutningar som en tredje part inte kan dekryptera.

Om en klient ansluter på port 8883 (MQTTS) eller 5671 (AMQPS) till IoT Edge Hub måste en TLS-kanal skapas. Under TLS-handskakningen skickar IoT Edge Hub certifikat kedjan som klienten behöver verifiera. För att kunna verifiera certifikat kedjan måste rot certifikatet för IoT Edge hubben installeras som ett betrott certifikat på klienten. Om rot certifikatet inte är betrott avvisas klient biblioteket av IoT Edge hubben med ett certifikat verifierings fel.

De steg som följer för att installera det här rot certifikatet för Broker på enhets klienter beskrivs i den [transparenta gatewayen](how-to-create-transparent-gateway.md) och i [förbereda en underordnad enhets](how-to-connect-downstream-device.md#prepare-a-downstream-device) dokumentation. Moduler kan använda samma rot certifikat som IoT Edge Hub genom att använda API: t för IoT Edge daemon.

#### <a name="authentication"></a>Autentisering

IoT Edge Hub accepterar bara anslutningar från enheter eller moduler som har en IoT Hub identitet, t. ex. som har registrerats i IoT Hub och har en av de tre autentiseringsmetoderna för klientautentisering som stöds av IoT Hub för att bevisa sin identitet: [symmetriska nycklar autentisering](how-to-authenticate-downstream-device.md#symmetric-key-authentication), [x. 509 självsignerad autentisering](how-to-authenticate-downstream-device.md#x509-self-signed-authentication), [x. 509 ca-signerad autentisering](how-to-authenticate-downstream-device.md#x509-ca-signed-authentication).  Dessa IoT Hub identiteter kan verifieras lokalt av IoT Edge Hub så att anslutningar fortfarande kan göras när de är offline.

Obs!

* IoT Edge moduler har för närvarande bara stöd för symmetrisk nyckel autentisering.
* MQTT-klienter med endast lokalt användar namn och lösen ord godkänns inte av IoT Edge Hub MQTT-Broker, de måste använda IoT Hub identiteter.

#### <a name="authorization"></a>Auktorisering

När autentiseringen har autentiserats har IoT Edge Hub två sätt att auktorisera klient anslutningar:

* Genom att verifiera att en klient tillhör den uppsättning betrodda klienter som definieras i IoT Hub. Uppsättningen betrodda klienter anges genom att konfigurera överordnade/underordnade eller enhets-/modul relationer i IoT Hub. När en modul skapas i IoT Edge upprättas en förtroende relation automatiskt mellan den här modulen och dess IoT Edge-enhet. Det här är den enda verifierings modellen som stöds av mekanismen för routnings utjämning.

* Genom att konfigurera en auktoriseringsprincip. Den här auktoriseringsprincipen är ett dokument som visar alla auktoriserade klient identiteter som har åtkomst till resurser på IoT Edge hubben. Detta är den primära auktoriserings modellen som används av IoT Edge Hub MQTT-Broker, även om överordnad/underordnad och enhets-/modul relationer också kan tolkas av MQTT-Broker för IoT Hub ämnen.

### <a name="remote-configuration"></a>Fjärrkonfiguration

IoT Edge Hub styrs helt av molnet. Konfigurationen hämtas från IoT Hub via dess [modul](iot-edge-modules.md#module-twins). Den innehåller:

* Konfiguration av vägar
* Auktoriseringsprinciper
* Konfiguration av MQTT-brygga

Dessutom kan flera konfigurationer göras genom [att konfigurera miljövariabler på IoT Edge hubben](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md).
<!-- </1.2> -->
::: moniker-end

## <a name="runtime-quality-telemetry"></a>Telemetri för körnings kvalitet

IoT Edge samlar in anonym telemetri från värd körningen och systemmodulerna för att förbättra produkt kvaliteten. Den här informationen kallas telemetri för körnings kvalitet. Den insamlade Telemetrin skickas regelbundet som "enhet till molnet"-meddelanden till IoT Hub från IoT Edge agenten. Dessa meddelanden visas inte i kundens vanliga telemetri och använder inte någon meddelande kvot.

IoT Edge agenten och hubben genererar mått som du kan samla in för att förstå enhetens prestanda. En delmängd av dessa mått samlas in av den IoT Edge agenten som en del av telemetri för körnings kvalitet. Måtten som samlas in för telemetri för körnings kvalitet är märkta med taggen `ms_telemetry` . Information om alla tillgängliga mått finns i [använda inbyggda mått](how-to-access-built-in-metrics.md).

All personligt identifierbar information, till exempel enhets-och Modulnamn, tas bort före uppladdning för att säkerställa den anonyma typen av telemetri för körnings kvalitet.

IoT Edge agent samlar in Telemetrin varje timme och skickar ett meddelande till IoT Hub var 24: e timme.

Om du inte vill avanmäla körnings telemetri från dina enheter kan du göra det på två sätt:

* Ange `SendRuntimeQualityTelemetry` miljövariabeln till `false` för **edgeAgent**, eller
* Avmarkera alternativet i Azure Portal under distributionen.

## <a name="next-steps"></a>Nästa steg

* [Förstå Azure IoT Edge-moduler](iot-edge-modules.md)
* [Lär dig hur du distribuerar moduler och etablerar vägar i IoT Edge](module-composition.md)
* [Lär dig att publicera och prenumerera på IoT Edge](how-to-publish-subscribe.md)
* [Lär dig mer om IoT Edge körnings mått](how-to-access-built-in-metrics.md)
