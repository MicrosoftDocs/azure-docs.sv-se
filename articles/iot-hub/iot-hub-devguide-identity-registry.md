---
title: Förstå Azure IoT Hub Identity-registret | Microsoft Docs
description: Guide för utvecklare – Beskrivning av registret för IoT Hub identitet och hur du använder det för att hantera dina enheter. Innehåller information om import och export av enhets identiteter i bulk.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: 2d9b0d97fa1823314f5109a1c7fc79054806c148
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93146934"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Förstå identitets registret i din IoT-hubb

Varje IoT-hubb har ett identitets register som lagrar information om de enheter och moduler som tillåts att ansluta till IoT Hub. Innan en enhet eller modul kan ansluta till en IoT-hubb måste det finnas en post för enheten eller modulen i IoT Hub: s identitets register. En enhet eller modul måste också autentisera med IoT Hub baserat på autentiseringsuppgifter som lagras i identitets registret.

Enhets-eller modul-ID som lagras i identitets registret är Skift läges känsligt.

På hög nivå är identitets registret en REST-kompatibel samling enhets-eller modul identitets resurser. När du lägger till en post i identitets registret skapar IoT Hub en uppsättning resurser per enhet, till exempel den kö som innehåller meddelanden från molnet till enheten.

Använd identitets registret när du behöver:

* Etablera enheter eller moduler som ansluter till din IoT Hub.
* Kontrol lera åtkomsten per enhet/enhet till navets enhet eller modul-riktade slut punkter.

> [!NOTE]
> * Identitets registret innehåller inte några programspecifika metadata.
> * Modulens identitet och modul är i offentlig för hands version. Funktionen nedan kommer att stödjas för modul identitet när den är allmänt tillgänglig.
>

## <a name="identity-registry-operations"></a>Identitetsregisteråtgärder

IoT Hub Identity-registret visar följande åtgärder:

* Skapa enhets-eller modul identitet
* Uppdatera enhets-eller modulens identitet
* Hämta enhets-eller modul identitet efter ID
* Ta bort enhets-eller modulens identitet
* Lista upp till 1000 identiteter
* Exportera enhets identiteter till Azure Blob Storage
* Importera enhets identiteter från Azure Blob Storage

Alla dessa åtgärder kan använda optimistisk samtidighet, enligt vad som anges i [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> Det enda sättet att hämta alla identiteter i en IoT Hub-identitets register är att använda [export](iot-hub-devguide-identity-registry.md#import-and-export-device-identities) funktionen.

Ett IoT Hub identitets register:

* Innehåller inte några metadata för programmet.
* Kan nås som en ord lista med hjälp av **deviceId** eller **moduleId** som nyckel.
* Stöder inte lättfattliga programspecifika-frågor.

En IoT-lösning har vanligt vis en separat lösning – specifik lagring som innehåller programspecifika metadata. Den lösnings bara butiken i en smart byggnads lösning skulle till exempel kunna registrera det rum där en temperatur sensor distribueras.

> [!IMPORTANT]
> Använd bara identitets registret för enhets hantering och etablerings åtgärder. Åtgärder med hög genom strömning vid körning bör inte vara beroende av att utföra åtgärder i identitets registret. Om du till exempel kontrollerar anslutnings status för en enhet innan du skickar ett kommando är det inte ett mönster som stöds. Se till att kontrol lera [begränsnings taxan](iot-hub-devguide-quotas-throttling.md) för identitets registret och mönstret för [enhetens pulsslag](iot-hub-devguide-identity-registry.md#device-heartbeat) .

## <a name="disable-devices"></a>Inaktivera enheter

Du kan inaktivera enheter genom att uppdatera egenskapen **status** för en identitet i identitets registret. Normalt använder du den här egenskapen i två scenarier:

* Under en etablerings process. Mer information finns i [enhets etablering](iot-hub-devguide-identity-registry.md#device-provisioning).

* Om du av någon anledning tror att en enhet är komprometterad eller har blivit obehörig.

Den här funktionen är inte tillgänglig för moduler.

## <a name="import-and-export-device-identities"></a>Importera och exportera enhets identiteter

Använd asynkrona åtgärder på [slut punkten för IoT Hub Resource Provider](iot-hub-devguide-endpoints.md) för att exportera enhets identiteter i bulk från en IoT Hub identitets register. Exporter är långvariga jobb som använder en BLOB-behållare som levereras av kund för att spara enhets identitets data som läses från identitets registret.

Använd asynkrona åtgärder på [slut punkten för IoT Hub Resource Provider](iot-hub-devguide-endpoints.md) för att importera enhets identiteter i bulk till ett identitets register för IoT Hub. Importer är långvariga jobb som använder data i en BLOB-behållare som levereras av kund för att skriva enhets identitets data i identitets registret.

Mer information om import-och export-API: er finns i [IoT Hub Resource Provider REST-API: er](/rest/api/iothub/iothubresource). Mer information om hur du kör import-och export jobb finns i [Mass hantering av IoT Hub enhets identiteter](iot-hub-bulk-identity-mgmt.md).

Enhets identiteter kan också exporteras och importeras från en IoT Hub via tjänst-API: et via [REST API](/rest/api/iothub/service/jobs/createimportexportjob) eller någon av IoT Hub [tjänst-SDK](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks): er.

## <a name="device-provisioning"></a>Enhets etablering

Enhets data som en specifik IoT-lösning lagrar beror på de särskilda kraven för lösningen. Men som minst måste en lösning lagra enhets identiteter och nycklar. Azure IoT Hub innehåller ett identitets register som kan lagra värden för varje enhet, till exempel ID: n, autentiseringsinställningar och status koder. En lösning kan använda andra Azure-tjänster, till exempel Table Storage, Blob Storage eller Cosmos DB för att lagra ytterligare enhets data.

*Enhets etablering* är en process för att lägga till den ursprungliga enhets informationen i butikerna i din lösning. Om du vill aktivera en ny enhet för att ansluta till navet måste du lägga till ett enhets-ID och nycklar i IoT Hub identitets registret. Som en del av etablerings processen kan du behöva initiera enhetsspecifika data i andra lösnings lager. Du kan också använda Azure-IoT Hub Device Provisioning Service för att möjliggöra Zero Touch-etablering, just-in-Time-etablering till en eller flera IoT-hubbar utan mänsklig inblandning. Mer information finns i dokumentationen för [etablerings tjänsten](https://azure.microsoft.com/documentation/services/iot-dps).

## <a name="device-heartbeat"></a>Enhetens pulsslag

IoT Hub Identity-registret innehåller ett fält med namnet **connectionState**. Använd bara fältet **connectionState** vid utveckling och fel sökning. IoT-lösningar bör inte fråga fältet vid körning. Fråga till exempel inte fältet **connectionState** för att kontrol lera om en enhet är ansluten innan du skickar ett meddelande från molnet till enheten eller ett SMS. Vi rekommenderar att du prenumererar på [händelsen **frånkopplad enhet**](iot-hub-event-grid.md#event-types) på Event Grid för att få aviseringar och övervaka enhetens anslutnings tillstånd. I den här [självstudien](iot-hub-how-to-order-connection-state-events.md) får du lära dig hur du integrerar enhet anslutna och avkopplade händelser från IoT Hub i din IoT-lösning.

Om din IoT-lösning behöver veta om en enhet är ansluten, kan du implementera *pulsslags mönstret*.
I pulsslags mönstret skickar enheten enhet-till-moln-meddelanden minst en gång varje fast tids period (till exempel minst en gång i timmen). Det innebär att även om en enhet inte har några data att skicka, skickar den fortfarande ett tomt meddelande från enhet till molnet (vanligt vis med en egenskap som identifierar den som ett pulsslag). På tjänst sidan upprätthåller lösningen en karta med det sista pulsslag som tagits emot för varje enhet. Om lösningen inte tar emot ett pulsslags meddelande inom den förväntade tiden från enheten förutsätts det att det är problem med enheten.

En mer komplex implementering kan omfatta information från [Azure Monitor](../azure-monitor/index.yml) och [Azure Resource Health](../service-health/resource-health-overview.md) för att identifiera enheter som försöker ansluta eller kommunicera men som inte fungerar. Mer information finns i [övervaka IoT Hub](monitor-iot-hub.md) och [kontrol lera IoT Hub resurs hälsa](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health). När du implementerar pulsslags mönstret måste du kontrol lera [IoT Hub kvoter och begränsningar](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Om en IoT-lösning endast använder anslutnings tillstånd för att avgöra om meddelanden från moln till enhet ska skickas, och meddelanden inte skickas till stora uppsättningar av enheter, bör du överväga att använda det enklare *korta förfallo tids* mönstret. Det här mönstret uppnår samma resultat som att underhålla ett register över enhetens anslutnings tillstånd med pulsslags mönstret, medan det är mer effektivt. Om du begär meddelande bekräftelser kan IoT Hub meddela dig om vilka enheter som kan ta emot meddelanden och vilka som inte är det.

## <a name="device-and-module-lifecycle-notifications"></a>Livs cykel meddelanden för enhet och modul

IoT Hub kan meddela din IoT-lösning när en identitet skapas eller tas bort genom att skicka livs cykel meddelanden. För att göra det måste IoT-lösningen skapa en väg och ange data källan som lika med *DeviceLifecycleEvents* eller *ModuleLifecycleEvents*. Som standard skickas inga livs cykel meddelanden, det vill säga inte finns några sådana vägar i förväg. Aviserings meddelandet innehåller egenskaper och brödtext.

Egenskaper: meddelande system egenskaper föregås av `$` symbolen.

Meddelande för enhet:

| Name | Värde |
| --- | --- |
|$content-typ | application/json |
|$iothub-enqueuedtime |  Tid när meddelandet skickades |
|$iothub-meddelande källa | deviceLifecycleEvents |
|$content kodning | UTF-8 |
|opType | **createDeviceIdentity** eller **deleteDeviceIdentity** |
|hubName | Namn på IoT Hub |
|deviceId | ID för enheten |
|operationTimestamp | ISO8601 tidsstämpel för åtgärd |
|iothub – meddelande schema | deviceLifecycleNotification |

Brödtext: det här avsnittet är i JSON-format och representerar den dubbla av den skapade enhets identiteten. Exempel:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```
Aviserings meddelande för modul:

| Name | Värde |
| --- | --- |
$content-typ | application/json |
$iothub-enqueuedtime |  Tid när meddelandet skickades |
$iothub-meddelande källa | moduleLifecycleEvents |
$content kodning | UTF-8 |
opType | **createModuleIdentity** eller **deleteModuleIdentity** |
hubName | Namn på IoT Hub |
moduleId | ID för modulen |
operationTimestamp | ISO8601 tidsstämpel för åtgärd |
iothub – meddelande schema | moduleLifecycleNotification |

Brödtext: det här avsnittet är i JSON-format och representerar den dubbla av den skapade modulens identitet. Exempel:

```json
{
    "deviceId":"11576-ailn-test-0-67333793211",
    "moduleId":"tempSensor",
    "etag":"AAAAAAAAAAE=",
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2016-02-30T16:24:48.789Z"
            },
            "$version": 1
        }
    }
}
```

## <a name="device-identity-properties"></a>Egenskaper för enhets identitet

Enhets identiteter visas som JSON-dokument med följande egenskaper:

| Egenskap | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |krävs, skrivskyddad på uppdateringar |En Skift läges känslig sträng (upp till 128 tecken) av ASCII 7-bitars alfanumeriska tecken plus särskilda specialtecken: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |krävs, skrivskyddad |En IoT Hub-genererad, SKIFT läges känslig sträng som är upp till 128 tecken lång. Det här värdet används för att särskilja enheter med samma **deviceId**, när de har tagits bort och återskapas. |
| etag |krävs, skrivskyddad |En sträng som representerar en svag ETag för enhets identiteten per [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Factor |valfri |Ett sammansatt objekt som innehåller autentiseringsinformation och säkerhets material. |
| auth. symkey |valfri |Ett sammansatt objekt som innehåller en primär och en sekundär nyckel, lagrad i base64-format. |
| status |krävs |En åtkomst indikator. Kan **aktive ras** eller **inaktive** ras. Om **aktive rad** tillåts enheten att ansluta. Om den är **inaktive rad** kan den här enheten inte komma åt någon enhets slut punkt. |
| statusReason |valfri |En 128 tecken lång sträng som lagrar orsaken till enhetens identitets status. Alla UTF-8-tecken tillåts. |
| statusUpdateTime |skrivskyddad |En temporal indikator som visar datum och tid för senaste status uppdateringen. |
| connectionState |skrivskyddad |Ett fält som visar anslutnings status: antingen **ansluten** eller **frånkopplad**. Det här fältet representerar IoT Hub visning av enhetens anslutnings status. **Viktigt**: det här fältet ska endast användas för utveckling/fel sökning. Anslutnings statusen uppdateras bara för enheter som använder MQTT eller AMQP. Dessutom baseras den på protokoll nivå pingar (MQTT-pingar eller AMQP-ping) och kan ha en maximal fördröjning på bara 5 minuter. Därför kan det finnas falska positiva identifieringar, till exempel enheter som rapporter ATS som anslutna, men som är frånkopplade. |
| connectionStateUpdatedTime |skrivskyddad |En temporal indikator som visar datum och tidpunkt då anslutnings statusen uppdaterades. |
| lastActivityTime |skrivskyddad |En temporal indikator som visar datum och tid då enheten anslöt, togs emot eller skickat ett meddelande. Den här egenskapen är konsekvent konsekvent, men kan fördröjas upp till 5 till 10 minuter. Därför bör den inte användas i produktions scenarier. |

> [!NOTE]
> Anslutnings status kan bara representera IoT Hub visning av anslutningens status. Uppdateringar av det här tillståndet kan bli fördröjda, beroende på nätverks förhållanden och konfigurationer.

> [!NOTE]
> För närvarande har enhets-SDK: erna inte stöd för användning av `+` and- `#` tecknen i **deviceId**.

## <a name="module-identity-properties"></a>Egenskaper för modulens identitet

Modul identiteter representeras som JSON-dokument med följande egenskaper:

| Egenskap | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |krävs, skrivskyddad på uppdateringar |En Skift läges känslig sträng (upp till 128 tecken) av ASCII 7-bitars alfanumeriska tecken plus särskilda specialtecken: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |krävs, skrivskyddad på uppdateringar |En Skift läges känslig sträng (upp till 128 tecken) av ASCII 7-bitars alfanumeriska tecken plus särskilda specialtecken: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |krävs, skrivskyddad |En IoT Hub-genererad, SKIFT läges känslig sträng som är upp till 128 tecken lång. Det här värdet används för att särskilja enheter med samma **deviceId**, när de har tagits bort och återskapas. |
| etag |krävs, skrivskyddad |En sträng som representerar en svag ETag för enhets identiteten per [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Factor |valfri |Ett sammansatt objekt som innehåller autentiseringsinformation och säkerhets material. |
| auth. symkey |valfri |Ett sammansatt objekt som innehåller en primär och en sekundär nyckel, lagrad i base64-format. |
| status |krävs |En åtkomst indikator. Kan **aktive ras** eller **inaktive** ras. Om **aktive rad** tillåts enheten att ansluta. Om den är **inaktive rad** kan den här enheten inte komma åt någon enhets slut punkt. |
| statusReason |valfri |En 128 tecken lång sträng som lagrar orsaken till enhetens identitets status. Alla UTF-8-tecken tillåts. |
| statusUpdateTime |skrivskyddad |En temporal indikator som visar datum och tid för senaste status uppdateringen. |
| connectionState |skrivskyddad |Ett fält som visar anslutnings status: antingen **ansluten** eller **frånkopplad**. Det här fältet representerar IoT Hub visning av enhetens anslutnings status. **Viktigt**: det här fältet ska endast användas för utveckling/fel sökning. Anslutnings statusen uppdateras bara för enheter som använder MQTT eller AMQP. Dessutom baseras den på protokoll nivå pingar (MQTT-pingar eller AMQP-ping) och kan ha en maximal fördröjning på bara 5 minuter. Därför kan det finnas falska positiva identifieringar, till exempel enheter som rapporter ATS som anslutna, men som är frånkopplade. |
| connectionStateUpdatedTime |skrivskyddad |En temporal indikator som visar datum och tidpunkt då anslutnings statusen uppdaterades. |
| lastActivityTime |skrivskyddad |En temporal indikator som visar datum och tid då enheten anslöt, togs emot eller skickat ett meddelande. |

> [!NOTE]
> För närvarande har enhets-SDK: erna inte stöd för användning av `+` and- `#` tecknen i **DeviceID** -och **moduleId**.

## <a name="additional-reference-material"></a>Ytterligare referens material

Andra referens ämnen i IoT Hub Developer Guide är:

* [IoT Hub slut punkter](iot-hub-devguide-endpoints.md) beskriver de olika slut punkter som varje IoT-hubb visar för körnings-och hanterings åtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter och begränsnings beteenden som gäller för tjänsten IoT Hub.

* [Azure IoT-enhet och tjänst-SDK](iot-hub-devguide-sdks.md) : er listar de olika språk-SDK: er som du kan använda när du utvecklar både enhets-och tjänst program som samverkar IoT Hub med

* [IoT Hub frågespråk](iot-hub-devguide-query-language.md) beskriver frågespråket som du kan använda för att hämta information från IoT Hub om din enhets dubblare och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder IoT Hub identitets registret kanske du är intresse rad av följande avsnitt om IoT Hubs guide:

* [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

* [Använd enheten för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)

* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill testa några av de begrepp som beskrivs i den här artikeln kan du läsa följande IoT Hub själv studie kurs:

* [Kom igång med Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Om du vill utforska med hjälp av IoT Hub Device Provisioning Service för att aktivera Zero-Touch, just-in-Time-etablering, se: 

* [Azure IoT Hub Device Provisioning-tjänst](https://azure.microsoft.com/documentation/services/iot-dps)