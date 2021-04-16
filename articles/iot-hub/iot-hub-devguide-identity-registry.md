---
title: Förstå Azure IoT Hub identitetsregistret | Microsoft Docs
description: Utvecklarguide – beskrivning av IoT Hub identitetsregistret och hur du använder det för att hantera dina enheter. Innehåller information om massimport och export av enhetsidentiteter.
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
ms.openlocfilehash: 42def04db63d81bdb3eff8098daa8c75924bffec
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107502087"
---
# <a name="understand-the-identity-registry-in-your-iot-hub"></a>Förstå identitetsregistret i din IoT-hubb

Varje IoT-hubb har ett identitetsregister som lagrar information om de enheter och moduler som tillåts ansluta till IoT Hub. Innan en enhet eller modul kan ansluta till en IoT-hubb måste det finnas en post för enheten eller modulen i IoT-hubbens identitetsregister. En enhet eller modul måste också autentiseras med IoT-hubben baserat på autentiseringsuppgifter som lagras i identitetsregistret.

Enhets- eller modul-ID:t som lagras i identitetsregistret är ärendekänsligt.

På hög nivå är identitetsregistret en REST-kompatibel samling enhets- eller modulidentitetsresurser. När du lägger till en post i identitetsregistret IoT Hub en uppsättning resurser per enhet, till exempel den kö som innehåller meddelanden från molnet till enheten.

Använd identitetsregistret när du behöver:

* Etablera enheter eller moduler som ansluter till din IoT-hubb.
* Kontrollera åtkomst per enhet/per modul till hubbens enhets- eller modulriktade slutpunkter.

> [!NOTE]
> * Identitetsregistret innehåller inga programspecifika metadata.
> * Modulidentitet och modultvilling finns i offentlig förhandsversion. Nedanstående funktion kommer att stödjas på modulidentitet när den är allmänt tillgänglig.
>

## <a name="identity-registry-operations"></a>Identitetsregisteråtgärder

Följande IoT Hub identitetsregistret exponerar följande åtgärder:

* Skapa enhets- eller modulidentitet
* Uppdatera enhets- eller modulidentitet
* Hämta enhets- eller modulidentitet efter ID
* Ta bort enhets- eller modulidentitet
* Lista upp till 1 000 identiteter
* Exportera enhetsidentiteter till Azure Blob Storage
* Importera enhetsidentiteter från Azure Blob Storage

Alla dessa åtgärder kan använda optimistisk samtidighet, som anges i [RFC7232](https://tools.ietf.org/html/rfc7232).

> [!IMPORTANT]
> Det enda sättet att hämta alla identiteter i en IoT-hubbs identitetsregister är att använda [exportfunktionen.](iot-hub-devguide-identity-registry.md#import-and-export-device-identities)

Ett IoT Hub identitetsregister:

* Innehåller inte några programmetadata.
* Kan nås som en ordlista med hjälp av **deviceId** eller **moduleId** som nyckel.
* Stöder inte uttrycksfulla frågor.

En IoT-lösning har vanligtvis ett separat lösningsspecifikt arkiv som innehåller programspecifika metadata. Till exempel skulle det lösningsspecifika lagret i en smart bygglösning registrera rummet där en temperatursensor distribueras.

> [!IMPORTANT]
> Använd endast identitetsregistret för enhetshanterings- och etableringsåtgärder. Höga dataflödesåtgärder vid körning bör inte vara beroende av att utföra åtgärder i identitetsregistret. Det går till exempel inte att kontrollera anslutningstillståndet för en enhet innan ett kommando skickas. Se till att kontrollera [begränsningsfrekvensen för](iot-hub-devguide-quotas-throttling.md) identitetsregistret och [enhetens pulsslagsmönster.](iot-hub-devguide-identity-registry.md#device-heartbeat)

## <a name="disable-devices"></a>Inaktivera enheter

Du kan inaktivera enheter  genom att uppdatera statusegenskapen för en identitet i identitetsregistret. Normalt använder du den här egenskapen i två scenarier:

* Under en etableringsorkestreringsprocess. Mer information finns i [Enhetsetablering.](iot-hub-devguide-identity-registry.md#device-provisioning)

* Om du av någon anledning tror att en enhet har komprometterats eller har blivit obehörig.

Den här funktionen är inte tillgänglig för moduler.

## <a name="import-and-export-device-identities"></a>Importera och exportera enhetsidentiteter

Använd asynkrona åtgärder [](iot-hub-devguide-endpoints.md) på IoT Hub-resursproviderns slutpunkt för att exportera enhetsidentiteter grupp för grupp från identitetsregistret för en IoT Hub. Exporter är långvariga jobb som använder en blobcontainer från kunden för att spara enhetsidentitetsdata som läses från identitetsregistret.

Använd asynkrona åtgärder [](iot-hub-devguide-endpoints.md) på IoT Hub-resursproviderns slutpunkt för att massimport av enhetsidentiteter till en IoT-hubbs identitetsregister. Importer är långvariga jobb som använder data i en blobcontainer som tillhandahålls av kunden för att skriva enhetsidentitetsdata till identitetsregistret.

Mer information om import- och export-API:er finns i IoT Hub [rest-API:er för resursprovidern.](/rest/api/iothub/iothubresource) Mer information om hur du kör import- och exportjobb finns [i Masshantering av IoT Hub enhetsidentiteter.](iot-hub-bulk-identity-mgmt.md)

Enhetsidentiteter kan också exporteras och importeras från en IoT Hub via tjänst-API:et via antingen [REST API](/rest/api/iothub/service/jobs/createimportexportjob) eller någon av IoT Hub [Service-API:erna](./iot-hub-devguide-sdks.md#azure-iot-hub-service-sdks).

## <a name="device-provisioning"></a>Enhetsetablering

Enhetsdata som lagras i en viss IoT-lösning beror på lösningens specifika krav. Men som ett minimum måste en lösning lagra enhetsidentiteter och autentiseringsnycklar. Azure IoT Hub innehåller ett identitetsregister som kan lagra värden för varje enhet, till exempel ID:n, autentiseringsnycklar och statuskoder. En lösning kan använda andra Azure-tjänster som tabellagring, bloblagring eller Cosmos DB för att lagra ytterligare enhetsdata.

*Enhetsetablering* är processen att lägga till inledande enhetsdata till butikerna i din lösning. Om du vill göra det möjligt för en ny enhet att ansluta till hubben måste du lägga till ett enhets-ID och nycklar IoT Hub identitetsregistret. Som en del av etableringsprocessen kan du behöva initiera enhetsspecifika data i andra lösningslager. Du kan också använda Azure IoT Hub Device Provisioning Service för att aktivera zero-touch, just-in-time-etablering till en eller flera IoT-hubbar utan mänsklig inblandning. Mer information finns i dokumentationen [om etableringstjänsten.](https://azure.microsoft.com/documentation/services/iot-dps)

## <a name="device-heartbeat"></a>Enhetens pulsslag

Det IoT Hub identitetsregistret innehåller ett fält med namnet **connectionState**. Använd endast **fältet connectionState** under utveckling och felsökning. IoT-lösningar bör inte fråga fältet vid körning. Fråga till exempel inte fältet **connectionState** för att kontrollera om en enhet är ansluten innan du skickar ett moln-till-enhet-meddelande eller ett SMS. Vi rekommenderar att du prenumererar på [ **enhetens frånkopplade**](iot-hub-event-grid.md#event-types) händelse Event Grid för att få aviseringar och övervaka enhetens anslutningstillstånd. I den här [självstudien](iot-hub-how-to-order-connection-state-events.md) får du lära dig hur du integrerar enhetsanslutna och frånkopplade enhetshändelser IoT Hub i din IoT-lösning.

Om din IoT-lösning behöver veta om en enhet är ansluten kan du implementera *pulsslagsmönstret*.
I pulsslagsmönstret skickar enheten enhet-till-moln-meddelanden minst en gång varje fast tid (till exempel minst en gång i timmen). Även om en enhet inte har några data att skicka skickar den därför fortfarande ett tomt enhet-till-moln-meddelande (vanligtvis med en egenskap som identifierar den som ett pulsslag). På tjänstsidan har lösningen en karta med det senaste pulsslaget för varje enhet. Om lösningen inte får något pulsslagsmeddelande inom förväntad tid från enheten förutsätter den att det finns ett problem med enheten.

En mer komplex implementering kan innehålla information från [Azure Monitor](../azure-monitor/index.yml) [och Azure Resource Health](../service-health/resource-health-overview.md) identifiera enheter som försöker ansluta eller kommunicera men misslyckas. Mer information finns i [Övervaka IoT Hub](monitor-iot-hub.md) och Kontrollera [IoT Hub resurshälsa.](iot-hub-azure-service-health-integration.md#check-health-of-an-iot-hub-with-azure-resource-health) När du implementerar pulsslagsmönstret kontrollerar du [IoT Hub och begränsar](iot-hub-devguide-quotas-throttling.md).

> [!NOTE]
> Om en IoT-lösning endast använder anslutningstillståndet för att avgöra om meddelanden ska skickas från moln till enhet, och meddelanden inte skickas till stora uppsättningar enheter, bör du överväga att använda det enklare mönstret för kort förfallotid.  Det här mönstret uppnår samma resultat som att upprätthålla ett register för enhetsanslutningstillstånd med hjälp av pulsslagsmönstret, samtidigt som det är effektivare. Om du begär bekräftelse av meddelanden kan IoT Hub meddela dig om vilka enheter som kan ta emot meddelanden och vilka som inte är det.

## <a name="device-and-module-lifecycle-notifications"></a>Meddelanden om enhets- och modullivscykel

IoT Hub kan meddela din IoT-lösning när en enhetsidentitet skapas eller tas bort genom att skicka livscykelmeddelanden. För att göra det måste din IoT-lösning skapa en väg och ange datakällan som *DeviceLifecycleEvents*. Som standard skickas inga livscykelmeddelanden, det vill säga inga sådana vägar finns redan. Genom att skapa en väg där datakällan är lika med *DeviceLifecycleEvents* skickas livscykelhändelser för både enhetsidentiteter och modulidentiteter. Meddelandeinnehållet varierar dock beroende på om händelserna genereras för modulidentiteter eller enhetsidentiteter.  Det bör noteras att för IoT Edge-moduler skiljer sig flödet för att skapa modulidentiteter från andra moduler. För IoT Edge-moduler skickas endast skapandemeddelandet om motsvarande IoT Edge-enhet för den uppdaterade IoT Edge-modulidentiteten körs. För alla andra moduler skickas livscykelmeddelanden när modulidentiteten uppdateras på IoT Hub sidan.  Aviseringsmeddelandet innehåller egenskaper och brödtext.

Egenskaper: Meddelandesystemets egenskaper föregås av `$` symbolen .

Aviseringsmeddelande för enheten:

| Name | Värde |
| --- | --- |
|$content-typ | application/json |
|$iothub-enqueuedtime |  Tid när meddelandet skickades |
|$iothub-message-source | deviceLifecycleEvents |
|$content-kodning | utf-8 |
|opType | **createDeviceIdentity** eller **deleteDeviceIdentity** |
|hubName | Namn på IoT Hub |
|deviceId | ID för enheten |
|operationTimestamp | ISO8601-tidsstämpel för åtgärd |
|iothub-message-schema | deviceLifecycleNotification |

Brödtext: Det här avsnittet är i JSON-format och representerar tvillingen för den skapade enhetsidentiteten. Exempel:

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
Meddelande för modul:

| Name | Värde |
| --- | --- |
$content-typ | application/json |
$iothub-enqueuedtime |  Tid när meddelandet skickades |
$iothub-message-source | moduleLifecycleEvents |
$content-kodning | utf-8 |
opType | **createModuleIdentity** eller **deleteModuleIdentity** |
hubName | Namn på IoT Hub |
moduleId | ID för modulen |
operationTimestamp | ISO8601-tidsstämpel för åtgärd |
iothub-message-schema | moduleLifecycleNotification |

Brödtext: Det här avsnittet är i JSON-format och representerar tvillingen för den skapade modulidentiteten. Exempel:

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

## <a name="device-identity-properties"></a>Egenskaper för enhetsidentitet

Enhetsidentiteter representeras som JSON-dokument med följande egenskaper:

| Egenskap | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |krävs, skrivskyddade för uppdateringar |En 7-bitars alfanumerisk sträng (upp till 128 tecken) med ASCII-alfanumeriska tecken plus vissa specialtecken: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |krävs, skrivskyddade |En IoT Hub-genererad, case-känslig sträng på upp till 128 tecken. Det här värdet används för att särskilja enheter med samma **deviceId** när de har tagits bort och skapats igen. |
| Etag |krävs, skrivskyddade |En sträng som representerar en svag ETag för enhetsidentiteten enligt [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |valfri |Ett sammansatt objekt som innehåller autentiseringsinformation och säkerhetsmaterial. |
| auth.symkey |valfri |Ett sammansatt objekt som innehåller en primär och en sekundär nyckel som lagras i base64-format. |
| status |krävs |En åtkomstindikator. Kan aktiveras **eller** **inaktiveras.** Om **aktiverad** tillåts enheten att ansluta. Om **den här** enheten är inaktiverad kan den inte komma åt någon slutpunkt som är riktad mot enheten. |
| statusReason |valfri |En 128 tecken lång sträng som lagrar orsaken till enhetens identitetsstatus. Alla UTF-8 tecken tillåts. |
| statusUpdateTime |skrivskyddade |En temporal indikator som visar datum och tid för den senaste statusuppdateringen. |
| connectionState |skrivskyddade |Ett fält som anger anslutningsstatus: antingen **Ansluten** eller **Frånkopplad.** Det här fältet IoT Hub en vy över enhetens anslutningsstatus. **Viktigt!** Det här fältet ska endast användas för utveckling/felsökning. Anslutningstillståndet uppdateras endast för enheter som använder MQTT eller AMQP. Dessutom baseras den på pingar på protokollnivå (MQTT-pingar eller AMQP-pingar) och kan ha en maximal fördröjning på bara 5 minuter. Av dessa skäl kan det finnas falska positiva resultat, till exempel enheter som rapporteras som anslutna men som är frånkopplade. |
| connectionStateUpdatedTime |skrivskyddade |En temporal indikator som visar datum och senaste gången anslutningstillståndet uppdaterades. |
| lastActivityTime |skrivskyddade |En tidsindikator som visar datum och senaste gången enheten anslöts, togs emot eller skickade ett meddelande. Den här egenskapen är så småningom konsekvent, men kan fördröjas upp till 5 till 10 minuter. Därför bör den inte användas i produktionsscenarier. |

> [!NOTE]
> Anslutningstillståndet kan bara IoT Hub en vy över anslutningens status. Uppdateringar av det här tillståndet kan fördröjas, beroende på nätverksförhållanden och konfigurationer.

> [!NOTE]
> Enhets-ID:erna stöder för närvarande inte användning av `+` `#` tecknen och i **deviceId.**

## <a name="module-identity-properties"></a>Egenskaper för modulidentitet

Modulidentiteter representeras som JSON-dokument med följande egenskaper:

| Egenskap | Alternativ | Beskrivning |
| --- | --- | --- |
| deviceId |krävs, skrivskyddade för uppdateringar |En 7-bitars alfanumerisk sträng (upp till 128 tecken) med ASCII-alfanumeriska tecken plus vissa specialtecken: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| moduleId |krävs, skrivskyddade för uppdateringar |En 7-bitars alfanumerisk sträng (upp till 128 tecken) med ASCII-alfanumeriska tecken plus vissa specialtecken: `- . + % _ # * ? ! ( ) , : = @ $ '` . |
| generationId |krävs, skrivskyddade |En IoT-hubbgenererad, fallkänslig sträng på upp till 128 tecken. Det här värdet används för att särskilja enheter med samma **deviceId**, när de har tagits bort och skapats igen. |
| Etag |krävs, skrivskyddade |En sträng som representerar en svag ETag för enhetsidentiteten enligt [RFC7232](https://tools.ietf.org/html/rfc7232). |
| Auth |valfri |Ett sammansatt objekt som innehåller autentiseringsinformation och säkerhetsmaterial. |
| auth.symkey |valfri |Ett sammansatt objekt som innehåller en primär och en sekundär nyckel som lagras i base64-format. |
| status |krävs |En åtkomstindikator. Kan aktiveras **eller** **inaktiveras.** Om **aktiverad** tillåts enheten att ansluta. Om **den här** enheten är inaktiverad kan den inte komma åt någon slutpunkt som är riktad mot enheten. |
| statusReason |valfri |En 128 tecken lång sträng som lagrar orsaken till enhetens identitetsstatus. Alla UTF-8 tecken tillåts. |
| statusUpdateTime |skrivskyddade |En temporal indikator som visar datum och tid för den senaste statusuppdateringen. |
| connectionState |skrivskyddade |Ett fält som anger anslutningsstatus: antingen **Ansluten eller** **Frånkopplad.** Det här fältet IoT Hub en vy över enhetens anslutningsstatus. **Viktigt!** Det här fältet ska endast användas för utveckling/felsökning. Anslutningstillståndet uppdateras endast för enheter som använder MQTT eller AMQP. Dessutom baseras den på pingar på protokollnivå (MQTT-pingar eller AMQP-pingar) och kan ha en maximal fördröjning på bara 5 minuter. Av dessa skäl kan det finnas falska positiva resultat, till exempel enheter som rapporteras som anslutna men som är frånkopplade. |
| connectionStateUpdatedTime |skrivskyddade |En temporal indikator som visar datum och senaste gången anslutningstillståndet uppdaterades. |
| lastActivityTime |skrivskyddade |En tidsindikator som visar datum och senaste gången enheten anslöts, togs emot eller skickade ett meddelande. |

> [!NOTE]
> Enhets-ID:erna stöder för närvarande inte användning av `+` `#` tecknen och i **deviceId** och **moduleId.**

## <a name="additional-reference-material"></a>Ytterligare referensmaterial

Andra referensämnen i IoT Hub utvecklarhandbok är:

* [IoT Hub slutpunkter](iot-hub-devguide-endpoints.md) beskriver de olika slutpunkter som varje IoT-hubb exponerar för körnings- och hanteringsåtgärder.

* [Begränsning och kvoter beskriver](iot-hub-devguide-quotas-throttling.md) de kvoter och begränsningsbeteenden som gäller för IoT Hub tjänsten.

* [Azure IoT-enhets- och tjänst-SDK:er](iot-hub-devguide-sdks.md) visar de olika språk-SDK:er som du kan använda när du utvecklar både enhets- och tjänstappar som interagerar med IoT Hub.

* [IoT Hub frågespråket](iot-hub-devguide-query-language.md) beskriver det frågespråk som du kan använda för att hämta information från IoT Hub om enhetstvillingarna och jobben.

* [IoT Hub MQTT-stöd](iot-hub-mqtt-support.md) ger mer information IoT Hub stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur IoT Hub identitetsregistret kan du vara intresserad av följande avsnitt IoT Hub utvecklarhandboken:

* [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

* [Använda enhetstvillingarna för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)

* [Anropa en direktmetod på en enhet](iot-hub-devguide-direct-methods.md)

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill prova några av de begrepp som beskrivs i den här artikeln kan du läsa följande IoT Hub självstudie:

* [Kom igång med Azure IoT Hub](quickstart-send-telemetry-dotnet.md)

Om du vill utforska IoT Hub Device Provisioning Service för att aktivera zero-touch- och just-in-time-etablering kan du se: 

* [Azure IoT Hub Device Provisioning-tjänst](https://azure.microsoft.com/documentation/services/iot-dps)
