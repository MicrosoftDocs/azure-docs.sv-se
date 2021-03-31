---
title: Förstå Azure IoT Hub-jobb | Microsoft Docs
description: Guide för utvecklare – schemalägga jobb som ska köras på flera enheter som är anslutna till din IoT Hub. Jobb kan uppdatera Taggar och önskade egenskaper och anropa direkta metoder på flera enheter.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2019
ms.custom: mqtt
ms.openlocfilehash: 5c14e8cfcbf8df86b0f71d6b12025594d2e648c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "81730113"
---
# <a name="schedule-jobs-on-multiple-devices"></a>Schemalägga jobb på flera enheter

Azure IoT Hub möjliggör ett antal bygg stenar som [enhets dubbla egenskaper och taggar](iot-hub-devguide-device-twins.md) och [direkta metoder](iot-hub-devguide-direct-methods.md). Server dels appar gör det vanligt att enhets administratörer och operatörer kan uppdatera och interagera med IoT-enheter i bulk och vid en schemalagd tidpunkt. Jobb kör enhetens dubbla uppdateringar och direkta metoder mot en uppsättning enheter vid en schemalagd tidpunkt. En operatör skulle till exempel kunna använda en backend-app som initierar och spårar ett jobb för att starta om en uppsättning enheter i byggnad 43 och våning 3 vid en tidpunkt som inte skulle störa driften av byggnaden.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Överväg att använda jobb när du behöver schemalägga och spåra förloppet för följande aktiviteter på en uppsättning enheter:

* Uppdatera önskade egenskaper
* Uppdatera Taggar
* Anropa direkt metoder

## <a name="job-lifecycle"></a>Jobb livs cykel

Jobb initieras av lösningens Server del och underhålls av IoT Hub. Du kan starta ett jobb via en tjänst som är riktad till en URI ( `PUT https://<iot hub>/jobs/v2/<jobID>?api-version=2018-06-30` ) och fråga efter förloppet för ett jobb som körs via en service FACING URI ( `GET https://<iot hub>/jobs/v2/<jobID?api-version=2018-06-30` ). Kör en jobb fråga om du vill uppdatera statusen för jobb som körs när ett jobb har initierats.

> [!NOTE]
> När du startar ett jobb kan egenskaps namn och värden bara innehålla US ASCII-utskrivbar alfanumerisk, förutom i följande uppsättning: `$ ( ) < > @ , ; : \ " / [ ] ? = { } SP HT`

## <a name="jobs-to-execute-direct-methods"></a>Jobb för att köra direkta metoder

Följande fragment visar information om HTTPS 1,1-begäran för att köra en [direkt metod](iot-hub-devguide-direct-methods.md) på en uppsättning enheter med ett jobb:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "<methodName>",
        "payload": <payload>,
        "responseTimeoutInSeconds": methodTimeoutInSeconds
    },
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

Frågevillkor kan också finnas på ett enda enhets-ID eller en lista med enhets-ID: n som visas i följande exempel:

```
"queryCondition" = "deviceId = 'MyDevice1'"
"queryCondition" = "deviceId IN ['MyDevice1','MyDevice2']"
"queryCondition" = "deviceId IN ['MyDevice1']"
```

[IoT Hub frågespråk](iot-hub-devguide-query-language.md) täcker IoT Hub frågespråket i ytterligare information.

I följande kodfragment visas begäran och svaret för ett jobb som är schemalagt att anropa en direkt metod med namnet testMethod på alla enheter på Contoso-Hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job01?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=68iv------------------------------------v8Hxalg%3D&se=1556849884&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 317

{
    "jobId": "job01",
    "type": "scheduleDeviceMethod",
    "cloudToDeviceMethod": {
        "methodName": "testMethod",
        "payload": {},
        "responseTimeoutInSeconds": 30
    },
    "queryCondition": "*", 
    "startTime": "2019-05-04T15:53:00.077Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 65
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 01:46:18 GMT

{"jobId":"job01","type":"scheduleDeviceMethod","status":"queued"}
```

## <a name="jobs-to-update-device-twin-properties"></a>Jobb för att uppdatera enhetens dubbla egenskaper

Följande fragment visar information om HTTPS 1,1-begäran om att uppdatera enhetens dubbla egenskaper med ett jobb:

```
PUT /jobs/v2/<jobId>?api-version=2018-06-30

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8

{
    "jobId": "<jobId>",
    "type": "scheduleUpdateTwin",
    "updateTwin": <patch>                 // Valid JSON object
    "queryCondition": "<queryOrDevices>", // query condition
    "startTime": <jobStartTime>,          // as an ISO-8601 date string
    "maxExecutionTimeInSeconds": <maxExecutionTimeInSeconds>
}
```

> [!NOTE]
> Egenskapen *updateTwin* kräver en giltig etag-matchning. till exempel `etag="*"` .

Följande fragment visar begäran och svar för ett jobb som har schemalagts att uppdatera enhetens dubbla egenskaper för test-Device på Contoso-Hub-1:

```
PUT https://contoso-hub-1.azure-devices.net/jobs/v2/job02?api-version=2018-06-30 HTTP/1.1
Authorization: SharedAccessSignature sr=contoso-hub-1.azure-devices.net&sig=BN0U-------------------------------------RuA%3D&se=1556925787&skn=iothubowner
Content-Type: application/json; charset=utf-8
Host: contoso-hub-1.azure-devices.net
Content-Length: 339

{
    "jobId": "job02",
    "type": "scheduleUpdateTwin",
    "updateTwin": {
      "properties": {
        "desired": {
          "test1": "value1"
        }
      },
     "etag": "*"
     },
    "queryCondition": "deviceId = 'test-device'",
    "startTime": "2019-05-08T12:19:56.868Z",
    "maxExecutionTimeInSeconds": 20
}

HTTP/1.1 200 OK
Content-Length: 63
Content-Type: application/json; charset=utf-8
Vary: Origin
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 03 May 2019 22:45:13 GMT

{"jobId":"job02","type":"scheduleUpdateTwin","status":"queued"}
```

## <a name="querying-for-progress-on-jobs"></a>Fråga efter förlopp för jobb

Följande fragment visar information om HTTPS 1,1-begäran för att fråga efter jobb:

```
GET /jobs/v2/query?api-version=2018-06-30[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]

Authorization: <config.sharedAccessSignature>
Content-Type: application/json; charset=utf-8
```

ContinuationToken tillhandahålls från svaret.

Du kan fråga efter jobb körnings statusen på varje enhet med hjälp av [IoT Hub frågespråk för enhets dubbla, jobb och](iot-hub-devguide-query-language.md)meddelanderoutning.

## <a name="jobs-properties"></a>Jobb egenskaper

I följande lista visas egenskaper och motsvarande beskrivningar, som du kan använda när du frågar efter jobb eller jobb resultat.

| Egenskap | Beskrivning |
| --- | --- |
| **jobId** |ID för programbegäran för jobbet. |
| **/St** |Den angivna start tiden för programmet (ISO-8601) för jobbet. |
| **Slut** |IoT Hub det angivna datumet (ISO-8601) för när jobbet har slutförts. Endast giltigt efter att jobbet har nått status ' slutfört '. |
| **bastyp** |Typer av jobb: |
| | **scheduleUpdateTwin**: ett jobb som används för att uppdatera en uppsättning av önskade egenskaper eller taggar. |
| | **scheduleDeviceMethod**: ett jobb som används för att anropa en enhets metod på en uppsättning enheter. |
| **statusfältet** |Jobbets aktuella tillstånd. Möjliga värden för status: |
| | **väntar**: schemalagd och väntar på att hämtas av jobb tjänsten. |
| | **schemalagt**: schemalagd för en gång i framtiden. |
| | **Kör**: aktivt jobb pågår. |
| | **avbruten**: jobbet har avbrutits. |
| | **misslyckades**: det gick inte att utföra jobbet. |
| | **slutfört**: jobbet har slutförts. |
| **deviceJobStatistics** |Statistik om jobb körningen. |
| | **deviceJobStatistics** -egenskaper: |
| | **deviceJobStatistics. deviceCount**: antal enheter i jobbet. |
| | **deviceJobStatistics. failedCount**: antalet enheter där jobbet misslyckades. |
| | **deviceJobStatistics. succeededCount**: antalet enheter där jobbet lyckades. |
| | **deviceJobStatistics. runningCount**: antal enheter som kör jobbet för tillfället. |
| | **deviceJobStatistics. pendingCount**: antal enheter som väntar på att köra jobbet. |

### <a name="additional-reference-material"></a>Ytterligare referens material

Andra referens ämnen i IoT Hub Developer Guide är:

* [IoT Hub slut punkter](iot-hub-devguide-endpoints.md) beskriver de olika slut punkter som varje IoT-hubb visar för körnings-och hanterings åtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter som gäller för den IoT Hub tjänsten och begränsnings beteendet som kan förväntas när du använder tjänsten.

* [Azure IoT-enhet och tjänst-SDK](iot-hub-devguide-sdks.md) : er listar de olika språk-SDK: er som du kan använda när du utvecklar både enhets-och tjänst program som samverkar IoT Hub med

* [IoT Hub frågespråk för enhets dubbla, jobb och meddelanderoutning](iot-hub-devguide-query-language.md) beskriver IoT Hub frågespråk. Använd det här frågespråket för att hämta information från IoT Hub om din enhets dubblare och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Om du vill testa några av de begrepp som beskrivs i den här artikeln kan du läsa följande IoT Hub själv studie kurs:

* [Schemalägga och sända jobb](iot-hub-node-node-schedule-jobs.md)