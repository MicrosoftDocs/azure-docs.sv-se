---
title: Förstå Azure IoT Hub fil uppladdning | Microsoft Docs
description: Guide för utvecklare – Använd fil uppladdnings funktionen i IoT Hub för att hantera uppladdning av filer från en enhet till en Azure Storage BLOB-behållare.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/07/2018
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: d106021d90304a06ea7c08494d626511bb903df0
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553047"
---
# <a name="upload-files-with-iot-hub"></a>Ladda upp filer med IoT Hub

Som beskrivs i artikeln [IoT Hub slut punkter](iot-hub-devguide-endpoints.md) kan en enhet starta en fil uppladdning genom att skicka ett meddelande via en enhets slut punkt (**/Devices/{deviceId}/Files**). När en enhet meddelar IoT Hub att överföringen har slutförts skickar IoT Hub ett meddelande om fil överföring via **/Messages/servicebound/filenotifications** -slutpunktens slut punkt.

I stället för att koppla samman meddelanden via IoT Hub fungerar IoT Hub istället som en dispatcher till ett associerat Azure Storage konto. En enhet begär en lagrings-token från IoT Hub som är speciell för den fil som enheten vill ladda upp. Enheten använder SAS-URI: n för att ladda upp filen till lagringen och när överföringen är klar skickar enheten ett meddelande om slut för ande till IoT Hub. IoT Hub kontrollerar att fil uppladdningen är klar och lägger till ett meddelande om fil överföring i meddelande slut punkten för servicen.

Innan du laddar upp en fil till IoT Hub från en enhet måste du konfigurera hubben genom att [associera ett Azure Storage](iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub) konto till det.

Enheten kan sedan [initiera en uppladdning](iot-hub-devguide-file-upload.md#initialize-a-file-upload) och sedan [meddela IoT Hub](iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload) när överföringen är klar. Om en enhet meddelar IoT Hub att uppladdningen är klar, kan tjänsten generera ett [meddelande](iot-hub-devguide-file-upload.md#file-upload-notifications).

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

### <a name="when-to-use"></a>När du ska använda detta

Använd fil uppladdning för att skicka mediefiler och stora telemetri-batchar som laddas upp av tillfälligt anslutna enheter eller komprimerade för att spara bandbredd.

Se [rikt linjer för kommunikation mellan enheter om du](iot-hub-devguide-d2c-guidance.md) är tveksam mellan att använda rapporterade egenskaper, meddelanden från enhet till moln eller fil uppladdning.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associera ett Azure Storage-konto med IoT Hub

Om du vill använda fil uppladdnings funktionen måste du först länka ett Azure Storage-konto till IoT Hub. Du kan slutföra den här uppgiften antingen via Azure Portal eller via programmering via [REST-API: er för IoT Hub Resource Provider](/rest/api/iothub/iothubresource). När du har associerat ett Azure Storage-konto med ditt IoT Hub returnerar tjänsten en SAS-URI till en enhet när enheten startar en fil överförings förfrågan.

[Ladda upp filer från enheten till molnet med IoT Hub](iot-hub-csharp-csharp-file-upload.md) instruktions guider ger en fullständig genom gång av fil överförings processen. De här instruktions guiderna visar hur du använder Azure Portal för att koppla ett lagrings konto till en IoT-hubb.

> [!NOTE]
> [Azure IoT SDK](iot-hub-devguide-sdks.md) : er hanterar automatiskt signatur-URI för delad åtkomst, laddar upp filen och meddelar IoT Hub om en slutförd uppladdning. Om en brand vägg blockerar åtkomsten till Blob Storage-slutpunkten men tillåter åtkomst till IoT Hub slut punkten, Miss lyckas fil överförings processen och följande fel meddelande visas för IoT C#-enhetens SDK:
>
> `---> System.Net.Http.HttpRequestException: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond`
>
> För att fil uppladdnings funktionen ska fungera måste åtkomst till både IoT Hub-slutpunkten och Blob Storage-slutpunkten vara tillgänglig för enheten.
> 


## <a name="initialize-a-file-upload"></a>Initiera en fil uppladdning
IoT Hub har en slut punkt för enheter för att begära en SAS-URI för lagring för att ladda upp en fil. För att starta fil överförings processen skickar enheten en POST-begäran till `{iot hub}.azure-devices.net/devices/{deviceId}/files` med följande JSON-text:

```json
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

IoT Hub returnerar följande data, som enheten använder för att ladda upp filen:

```json
{
    "correlationId": "somecorrelationid",
    "hostName": "yourstorageaccount.blob.core.windows.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Föråldrad: initiera en fil uppladdning med GET

> [!NOTE]
> I det här avsnittet beskrivs inaktuella funktioner för att få en SAS-URI från IoT Hub. Använd metoden POST som beskrivits tidigare.

IoT Hub har två REST-slutpunkter som stöder fil uppladdning, en för att hämta SAS-URI för lagring och den andra för att meddela IoT-hubben för en slutförd uppladdning. Enheten startar fil överförings processen genom att skicka en GET till IoT Hub på `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}` . IoT Hub returnerar:

* En SAS-URI som är unik för den fil som ska överföras.

* Ett korrelations-ID som ska användas när överföringen är klar.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Meddela IoT Hub om en slutförd fil uppladdning

Enheten överför filen till lagringen med hjälp av Azure Storage SDK: er. När uppladdningen är klar skickar enheten en POST-begäran till `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` med följande JSON-brödtext:

```json
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

Värdet för `isSuccess` är ett booleskt värde som anger om filen har överförts. Status koden för `statusCode` är statusen för överföring av filen till lagringen och `statusDescription` motsvarar `statusCode` .

## <a name="reference-topics"></a>Referens ämnen:

Följande referens avsnitt innehåller mer information om hur du laddar upp filer från en enhet.

## <a name="file-upload-notifications"></a>Fil överförings meddelanden

Om en enhet meddelar IoT Hub att överföringen är klar IoT Hub generera ett meddelande. Det här meddelandet innehåller filens namn och lagrings plats.

Som förklaras i [slut punkter](iot-hub-devguide-endpoints.md)IoT Hub skickar fil överförings meddelanden via en service Facing Endpoint (**/Messages/servicebound/fileuploadnotifications**) som meddelanden. De mottagna semantikarna för fil överförings meddelanden är desamma som för meddelanden från moln till enhet och har samma [livs cykel för meddelande](iot-hub-devguide-messages-c2d.md#the-cloud-to-device-message-life-cycle). Varje meddelande som hämtas från fil överförings meddelande slut punkten är en JSON-post med följande egenskaper:

| Egenskap | Beskrivning |
| --- | --- |
| EnqueuedTimeUtc |Tidsstämpel som anger när meddelandet skapades. |
| DeviceId |**DeviceID** för enheten som överförde filen. |
| BlobUri |URI för den överförda filen. |
| BlobName |Namnet på den överförda filen. |
| LastUpdatedTime |Tidsstämpel som anger när filen senast uppdaterades. |
| BlobSizeInBytes |Storleken på den överförda filen. |

**Exempel**. Det här exemplet visar bröd texten i ett meddelande om fil överföring.

```json
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Konfigurations alternativ för fil överförings meddelanden

Varje IoT-hubb har följande konfigurations alternativ för fil överförings meddelanden:

| Egenskap | Beskrivning | Intervall och standard |
| --- | --- | --- |
| **enableFileUploadNotifications** |Anger om fil överförings meddelanden skrivs till slut punkten för fil meddelanden. |Booleska. Standard: sant. |
| **fileNotifications. ttlAsIso8601** |Standard-TTL för fil överförings meddelanden. |ISO_8601 intervall upp till 48H (minst 1 minut). Standard: 1 timme. |
| **fileNotifications. lockDuration** |Lås varaktighet för meddelande kön för fil överföring. |5 till 300 sekunder (minst 5 sekunder). Standard: 60 sekunder. |
| **fileNotifications. maxDeliveryCount** |Maximalt antal leveranser för meddelande kön för fil överföring. |1 till 100. Standard: 100. |

Du kan ställa in dessa egenskaper på IoT-hubben med hjälp av Azure Portal, Azure CLI eller PowerShell. Mer information finns i avsnitten under [Konfigurera fil uppladdning](iot-hub-configure-file-upload.md).

## <a name="additional-reference-material"></a>Ytterligare referens material

Andra referens ämnen i IoT Hub Developer Guide är:

* [IoT Hub slut punkter](iot-hub-devguide-endpoints.md) beskriver de olika IoT Hub-slutpunkterna för körnings-och hanterings åtgärder.

* [Begränsning och kvoter](iot-hub-devguide-quotas-throttling.md) beskriver de kvoter och begränsnings beteenden som gäller för tjänsten IoT Hub.

* [Azure IoT-enhet och tjänst-SDK](iot-hub-devguide-sdks.md) : er listar de olika språk-SDK: er som du kan använda när du utvecklar både enhets-och tjänst program som samverkar IoT Hub med

* [IoT Hub frågespråk](iot-hub-devguide-query-language.md) beskriver frågespråket som du kan använda för att hämta information från IoT Hub om din enhets dubblare och jobb.

* [IoT Hub MQTT-support](iot-hub-mqtt-support.md) innehåller mer information om IoT Hub-stöd för MQTT-protokollet.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du laddar upp filer från enheter med hjälp av IoT Hub kanske du är intresse rad av följande avsnitt om IoT Hub-utvecklarverktyg:

* [Hantera enhets identiteter i IoT Hub](iot-hub-devguide-identity-registry.md)

* [Styra åtkomst till IoT Hub](iot-hub-devguide-security.md)

* [Använd enheten för att synkronisera tillstånd och konfigurationer](iot-hub-devguide-device-twins.md)

* [Anropa en direkt metod på en enhet](iot-hub-devguide-direct-methods.md)

* [Schemalägga jobb på flera enheter](iot-hub-devguide-jobs.md)

Om du vill testa några av de begrepp som beskrivs i den här artikeln kan du läsa följande IoT Hub själv studie kurs:

* [Ladda upp filer från enheter till molnet med IoT Hub](iot-hub-csharp-csharp-file-upload.md)
