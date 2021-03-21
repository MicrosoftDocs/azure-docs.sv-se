---
title: Ladda upp filer från enheter till Azure IoT Hub med python | Microsoft Docs
description: Ladda upp filer från en enhet till molnet med hjälp av Azure IoT-enhetens SDK för python. Överförda filer lagras i en BLOB-behållare för Azure Storage.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 03/31/2020
ms.author: robinsh
ms.custom: mqtt, devx-track-python
ms.openlocfilehash: 77d51b2c839a64567838fa4d6308d203a6bb8b82
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501149"
---
# <a name="upload-files-from-your-device-to-the-cloud-with-iot-hub-python"></a>Ladda upp filer från enheten till molnet med IoT Hub (python)

[!INCLUDE [iot-hub-file-upload-language-selector](../../includes/iot-hub-file-upload-language-selector.md)]

Den här artikeln visar hur du använder [fil överförings funktionerna i IoT Hub](iot-hub-devguide-file-upload.md) för att ladda upp en fil till [Azure Blob Storage](../storage/index.yml). Självstudien visar hur du:

* Tillhandahålla en lagrings behållare på ett säkert sätt för att ladda upp en fil.

* Använd python-klienten för att ladda upp en fil via din IoT Hub.

[Genom att skicka telemetri från en enhet till en IoT Hub](quickstart-send-telemetry-python.md) -snabb start demonstreras de grundläggande meddelande funktionerna från enhet till moln i IoT Hub. I vissa fall kan du dock inte enkelt mappa de data som enheterna skickar till de relativt små enhets-till-moln-meddelanden som IoT Hub accepterar. När du behöver använda filer från en enhet kan du fortfarande använda säkerhet och tillförlitlighet för IoT Hub.

I slutet av den här självstudien kör du python-konsol programmet:

* **FileUpload.py**, som laddar upp en fil till lagringen med python-enhets-SDK: n.

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

[!INCLUDE [iot-hub-include-x509-ca-signed-file-upload-support-note](../../includes/iot-hub-include-x509-ca-signed-file-upload-support-note.md)]

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [iot-hub-include-python-v2-async-installation-notes](../../includes/iot-hub-include-python-v2-async-installation-notes.md)]

* Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här artikeln använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [iot-hub-associate-storage](../../includes/iot-hub-associate-storage.md)]

## <a name="upload-a-file-from-a-device-app"></a>Ladda upp en fil från en enhets app

I det här avsnittet skapar du Device-appen för att ladda upp en fil till IoT Hub.

1. Kör följande kommando i kommando tolken för att installera paketet **Azure-IoT-Device** . Du använder det här paketet för att koordinera fil uppladdning med IoT Hub.

    ```cmd/sh
    pip install azure-iot-device
    ```

1. Kör följande kommando i kommando tolken för att installera [**Azure. Storage. blob**](https://pypi.org/project/azure-storage-blob/) -paketet. Du använder det här paketet för att utföra fil uppladdning.

    ```cmd/sh
    pip install azure.storage.blob
    ```

1. Skapa en test fil som du överför till Blob Storage.

1. Med hjälp av en text redigerare skapar du en **FileUpload.py** -fil i arbetsmappen.

1. Lägg till följande- `import` instruktioner och variabler i början av **FileUpload.py** -filen.

    ```python
    import os
    import asyncio
    from azure.iot.device.aio import IoTHubDeviceClient
    from azure.core.exceptions import AzureError
    from azure.storage.blob import BlobClient

    CONNECTION_STRING = "[Device Connection String]"
    PATH_TO_FILE = r"[Full path to local file]"
    ```

1. Ersätt `[Device Connection String]` med anslutnings strängen för din IoT Hub-enhet i filen. Ersätt `[Full path to local file]` med sökvägen till test filen som du skapade eller en fil på enheten som du vill ladda upp.

1. Skapa en funktion för att överföra filen till Blob Storage:

    ```python
    async def store_blob(blob_info, file_name):
        try:
            sas_url = "https://{}/{}/{}{}".format(
                blob_info["hostName"],
                blob_info["containerName"],
                blob_info["blobName"],
                blob_info["sasToken"]
            )

            print("\nUploading file: {} to Azure Storage as blob: {} in container {}\n".format(file_name, blob_info["blobName"], blob_info["containerName"]))

            # Upload the specified file
            with BlobClient.from_blob_url(sas_url) as blob_client:
                with open(file_name, "rb") as f:
                    result = blob_client.upload_blob(f, overwrite=True)
                    return (True, result)

        except FileNotFoundError as ex:
            # catch file not found and add an HTTP status code to return in notification to IoT Hub
            ex.status_code = 404
            return (False, ex)

        except AzureError as ex:
            # catch Azure errors that might result from the upload operation
            return (False, ex)
    ```

    Den här funktionen tolkar *blob_infos* strukturen som har skickats till den för att skapa en URL som används för att initiera en [Azure. Storage. blob. BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient). Sedan laddar den upp din fil till Azure Blob Storage med hjälp av den här klienten.

1. Lägg till följande kod för att ansluta klienten och ladda upp filen:

    ```python
    async def main():
        try:
            print ( "IoT Hub file upload sample, press Ctrl-C to exit" )

            conn_str = CONNECTION_STRING
            file_name = PATH_TO_FILE
            blob_name = os.path.basename(file_name)

            device_client = IoTHubDeviceClient.create_from_connection_string(conn_str)

            # Connect the client
            await device_client.connect()

            # Get the storage info for the blob
            storage_info = await device_client.get_storage_info_for_blob(blob_name)

            # Upload to blob
            success, result = await store_blob(storage_info, file_name)

            if success == True:
                print("Upload succeeded. Result is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], True, 200, "OK: {}".format(file_name)
                )

            else :
                # If the upload was not successful, the result is the exception object
                print("Upload failed. Exception is: \n") 
                print(result)
                print()

                await device_client.notify_blob_upload_status(
                    storage_info["correlationId"], False, result.status_code, str(result)
                )

        except Exception as ex:
            print("\nException:")
            print(ex)

        except KeyboardInterrupt:
            print ( "\nIoTHubDeviceClient sample stopped" )

        finally:
            # Finally, disconnect the client
            await device_client.disconnect()


    if __name__ == "__main__":
        asyncio.run(main())
        #loop = asyncio.get_event_loop()
        #loop.run_until_complete(main())
        #loop.close()
    ```

    Den här koden skapar en asynkron **IoTHubDeviceClient** och använder följande API: er för att hantera fil uppladdning med IoT Hub:

    * **get_storage_info_for_blob** hämtar information från din IoT-hubb om det länkade lagrings kontot som du skapade tidigare. Den här informationen inkluderar värdnamn, behållar namn, BLOB-namn och en SAS-token. Lagrings informationen skickas till **store_blob** funktion (som skapats i föregående steg), så att **BlobClient** i funktionen kan autentiseras med Azure Storage. Metoden **get_storage_info_for_blob** returnerar även en correlation_id som används i **notify_blob_upload_status** -metoden. Correlation_id är IoT Hub hur du markerar vilken blob som du arbetar med.

    * **notify_blob_upload_status** meddelar IoT Hub om status för din Blob Storage-åtgärd. Du skickar det correlation_id som erhålls av **get_storage_info_for_blob** -metoden. Den används av IoT Hub för att meddela alla tjänster som kan lyssna efter ett meddelande om statusen för fil överförings aktiviteten.

1. Spara och Stäng filen **UploadFile.py** .

## <a name="run-the-application"></a>Kör programmet

Nu är du redo att köra programmet.

1. Kör följande kommando i en kommando tolk i arbetsmappen:

    ```cmd/sh
    python FileUpload.py
    ```

2. Följande skärm bild visar utdata från **fileupload** -appen:

    ![Utdata från den simulerade enhets appen](./media/iot-hub-python-python-file-upload/run-device-app.png)

3. Du kan använda portalen för att visa den överförda filen i den lagrings behållare som du konfigurerade:

    ![Överförd fil](./media/iot-hub-python-python-file-upload/view-blob.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder fil överförings funktionerna i IoT Hub för att förenkla fil överföringar från enheter. Du kan fortsätta att utforska funktionerna och scenarierna i IoT Hub med följande artiklar:

* [Skapa en IoT Hub program mässigt](iot-hub-rm-template-powershell.md)

* [Introduktion till C SDK](iot-hub-device-sdk-c-intro.md)

* [SDK:er för Azure IoT](iot-hub-devguide-sdks.md)

Lär dig mer om Azure Blob Storage med följande länkar:

* [Dokumentation om Azure Blob Storage](../storage/blobs/index.yml)

* [Dokumentation om Azure Blob Storage for python API](/python/api/overview/azure/storage-blob-readme)