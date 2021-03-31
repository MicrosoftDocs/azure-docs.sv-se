---
title: Reagera på Blob Storage module-händelser – Azure Event Grid IoT Edge | Microsoft Docs
description: Reagera på Blob Storage module-händelser
author: arduppal
manager: brymat
ms.author: arduppal
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 230e158a970f8c815b1575403c013e30749124c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96005069"
---
# <a name="tutorial-react-to-blob-storage-events-on-iot-edge-preview"></a>Självstudie: reagera på Blob Storage händelser på IoT Edge (förhands granskning)
Den här artikeln visar hur du distribuerar Azure Blob Storage i IoT-modulen, som fungerar som en Event Grid utgivare för att skicka händelser vid skapande av BLOB och blob-borttagning till Event Grid.  

En översikt över Azure-Blob Storage på IoT Edge finns i [Azure Blob Storage på IoT Edge](../../iot-edge/how-to-store-data-blob.md) och dess funktioner.

> [!WARNING]
> Azure Blob Storage på IoT Edge-integrering med Event Grid är i för hands version

För att kunna slutföra den här självstudien behöver du:

* **Azure-prenumeration** – skapa ett [kostnads fritt konto](https://azure.microsoft.com/free) om du inte redan har ett. 
* **Azure IoT Hub-och IoT Edge-enhet** – Följ stegen i snabb starten för [Linux](../../iot-edge/quickstart-linux.md) -eller [Windows-enheter](../../iot-edge/quickstart.md) om du inte redan har en.

## <a name="deploy-event-grid-iot-edge-module"></a>Distribuera Event Grid IoT Edge modul

Det finns flera sätt att distribuera moduler till en IoT Edge-enhet och alla fungerar Azure Event Grid på IoT Edge. I den här artikeln beskrivs stegen för att distribuera Event Grid på IoT Edge från Azure Portal.

>[!NOTE]
> I den här självstudien ska du distribuera Event Grid-modulen utan persistence. Det innebär att alla ämnen och prenumerationer som du skapar i den här självstudien tas bort när du distribuerar om modulen. Mer information om hur du konfigurerar persistence finns i följande artiklar: [sparat tillstånd i Linux](persist-state-linux.md) eller [sparat tillstånd i Windows](persist-state-windows.md). För produktions arbets belastningar rekommenderar vi att du installerar Event Grid-modulen med persistence.


### <a name="select-your-iot-edge-device"></a>Välj din IoT Edge enhet

1. Logga in på [Azure-portalen](https://portal.azure.com)
1. Navigera till din IoT Hub.
1. Välj **IoT Edge** på menyn i avsnittet **Automatisk enhets hantering** . 
1. Klicka på mål enhetens ID i listan över enheter
1. Välj **Ange moduler**. Låt sidan vara öppen. Du kommer att fortsätta med stegen i nästa avsnitt.

### <a name="configure-a-deployment-manifest"></a>Konfigurera ett distributions manifest

Ett distributions manifest är ett JSON-dokument som beskriver vilka moduler som ska distribueras, hur data flödar mellan moduler och önskade egenskaper för modulen. Azure Portal har en guide som vägleder dig genom att skapa ett distributions manifest i stället för att skapa JSON-dokumentet manuellt.  Det finns tre steg: **Lägg till moduler**, **Ange vägar** och **Granska distribution**.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **distributions moduler** väljer du **Lägg till**
1. Från typer av moduler i list rutan väljer du **IoT Edge modul**
1. Ange namn, avbildning, behållarens skapande alternativ för behållaren:

   * **Namn**: eventgridmodule
   * **Bild-URI**: `mcr.microsoft.com/azure-event-grid/iotedge:latest`
   * **Alternativ för att skapa behållare**:

    ```json
        {
          "Env": [
           "inbound__serverAuth__tlsPolicy=enabled",
           "inbound__clientAuth__clientCert__enabled=false"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
    ```    

 1. Klicka på **Spara**
 1. Fortsätt till nästa avsnitt för att lägga till modulen Azure Event Grid-prenumerant innan du distribuerar dem tillsammans.

    >[!IMPORTANT]
    > I den här självstudien får du lära dig att distribuera Event Grid-modulen för att tillåta både HTTP/HTTPs-begäranden, klientautentisering inaktive rad. För produktions arbets belastningar rekommenderar vi att du bara aktiverar HTTPs-förfrågningar och-prenumeranter med klientautentisering aktive rad. Mer information om hur du konfigurerar Event Grid modul på ett säkert sätt finns i [säkerhet och autentisering](security-authentication.md).
    

## <a name="deploy-event-grid-subscriber-iot-edge-module"></a>Distribuera Event Grid Subscriber IoT Edge-modulen

I det här avsnittet visas hur du distribuerar en annan IoT-modul som fungerar som en händelse hanterare som du kan leverera händelser till.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **distributions moduler** väljer du **Lägg till** igen. 
1. Från typer av moduler i list rutan väljer du **IoT Edge modul**
1. Ange namn, avbildning och behållar skapande alternativ för behållaren:

   * **Namn**: prenumerant
   * **Bild-URI**: `mcr.microsoft.com/azure-event-grid/iotedge-samplesubscriber:latest`
   * **Alternativ för att skapa behållare**: ingen
1. Klicka på **Spara**
1. Fortsätt till nästa avsnitt för att lägga till Azure Blob Storage-modulen

## <a name="deploy-azure-blob-storage-module"></a>Distribuera Azure Blob Storage-modulen

I det här avsnittet visas hur du distribuerar Azure Blob Storage-modulen, som fungerar som en Event Grid publicering av BLOB som skapats och tagits bort av Publisher.

### <a name="add-modules"></a>Lägga till moduler

1. I avsnittet **distributions moduler** väljer du **Lägg till**
2. Från typer av moduler i list rutan väljer du **IoT Edge modul**
3. Ange namn, avbildning och behållar skapande alternativ för behållaren:

   * **Namn**: azureblobstorageoniotedge
   * **Bild-URI**: MCR.Microsoft.com/Azure-Blob-Storage:Latest
   * **Alternativ för att skapa behållare**:

   ```json
       {
         "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>",
           "EVENTGRID_ENDPOINT=http://<event grid module name>:5888"
         ],
         "HostConfig":{
           "Binds":[
               "<storage mount>"
           ],
           "PortBindings":{
             "11002/tcp":[{"HostPort":"11002"}]
           }
         }
       }
   ```

   > [!IMPORTANT]
   > - Blob Storage-modulen kan publicera händelser med både HTTPS och HTTP. 
   > - Om du har aktiverat den klientbaserade autentiseringen för EventGrid, se till att uppdatera värdet för EVENTGRID_ENDPOINT att tillåta https, så här: `EVENTGRID_ENDPOINT=https://<event grid module name>:4438` .
   > - Lägg också till en annan miljö variabel `AllowUnknownCertificateAuthority=true` till ovanstående JSON. När du pratar med EventGrid över HTTPS tillåter **AllowUnknownCertificateAuthority** lagrings modulen att lita på självsignerade EventGrid-servercertifikat.

4. Uppdatera JSON som du kopierade med följande information:

   - Ersätt `<your storage account name>` med ett namn som du kan komma ihåg. Konto namn ska vara 3 till 24 tecken långa, med gemener och siffror. Inga blank steg.

   - Ersätt `<your storage account key>` med en 64 byte-base64-nyckel. Du kan generera en nyckel med verktyg som [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Du använder dessa autentiseringsuppgifter för att få åtkomst till blob-lagringen från andra moduler.

   - Ersätt `<event grid module name>` med namnet på din event Grid-modul.
   - Ersätt `<storage mount>` enligt behållar operativ systemet.
     - För Linux-behållare, **min-volym:/blobroot**
     - För Windows-behållare,**min-volym: C:/BlobRoot**

5. Klicka på **Spara**
6. Klicka på **Nästa** för att fortsätta till avsnittet vägar

    > [!NOTE]
    > Om du använder en virtuell Azure-dator som gräns enhet lägger du till en regel för inkommande port för att tillåta inkommande trafik på de värd portar som används i den här självstudien: 4438, 5888, 8080 och 11002. Anvisningar om hur du lägger till regeln finns i [så här öppnar du portar till en virtuell dator](../../virtual-machines/windows/nsg-quickstart-portal.md).

### <a name="setup-routes"></a>Installations vägar

Behåll standard vägarna och välj **Nästa** för att fortsätta till gransknings avsnittet

### <a name="review-deployment"></a>Granska distribution

1. I avsnittet granska visas JSON-distributions manifestet som skapades utifrån dina val i föregående avsnitt. Bekräfta att du ser följande fyra moduler: **$edgeAgent**, **$edgeHub**, **eventgridmodule**, **prenumeranter** och **azureblobstorageoniotedge** som alla distribueras.
2. Granska distributions informationen och välj sedan **Skicka**.

## <a name="verify-your-deployment"></a>Verifiera distributionen

1. När du har skickat distributionen återgår du till IoT Edge sida i IoT Hub.
2. Välj den **IoT Edge enhet** som du har för distributionen för att öppna informationen.
3. I enhets informationen kontrollerar du att modulerna eventgridmodule, prenumeranter och azureblobstorageoniotedge anges som båda **anges i distributionen** och **rapporteras av enheten**.

   Det kan ta en stund innan modulen har startats på enheten och sedan rapporteras tillbaka till IoT Hub. Uppdatera sidan om du vill se en uppdaterad status.

## <a name="publish-blobcreated-and-blobdeleted-events"></a>Publicera BlobCreated-och BlobDeleted-händelser

1. I den här modulen skapas automatiskt ämnet **MicrosoftStorage**. Kontrol lera att den finns
    ```sh
    curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview
    ```

    Exempel på utdata:

    ```json
        [
          {
            "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage",
            "name": "MicrosoftStorage",
            "type": "Microsoft.EventGrid/topics",
            "properties": {
              "endpoint": "https://<edge-vm-ip>:4438/topics/MicrosoftStorage/events?api-version=2019-01-01-preview",
              "inputSchema": "EventGridSchema"
            }
          }
        ]
    ```

    > [!IMPORTANT]
    > - För HTTPS-flödet, om klientautentisering är aktive rad via SAS-nyckel, ska den SAS-nyckel som anges ovan läggas till som en rubrik. Därför kommer spiral förfrågan att: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`
    > - Om klientautentisering är aktive rad via certifikat för HTTPS-flödet blir förfrågan om att: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage?api-version=2019-01-01-preview`

2. Prenumeranter kan registrera sig för händelser som publiceras i ett ämne. Om du vill ta emot en händelse måste du skapa en Event Grid prenumeration för **MicrosoftStorage** -avsnittet.
    1. Skapa blobsubscription.jsmed följande innehåll. Mer information om nytto lasten finns i vår [API-dokumentation](api.md)

       ```json
        {
          "properties": {
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       >[!NOTE]
       > Egenskapen **endpointType** anger att prenumeranten är en **webhook**.  **EndpointUrl** anger URL: en där prenumeranten lyssnar efter händelser. URL: en motsvarar Azure Function-exemplet som du distribuerade tidigare.

    2. Kör följande kommando för att skapa en prenumeration för ämnet. Bekräfta att du ser HTTP-statuskoden är `200 OK` .

       ```sh
       curl -k -H "Content-Type: application/json" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       > [!IMPORTANT]
       > - För HTTPS-flödet, om klientautentisering är aktive rad via SAS-nyckel, ska den SAS-nyckel som anges ovan läggas till som en rubrik. Därför kommer spiral förfrågan att: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview` 
       > - Om klientautentisering är aktive rad via certifikat för HTTPS-flödet blir förfrågan om att:`curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X PUT -g -d @blobsubscription.json https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

    3. Kör följande kommando för att kontrol lera att prenumerationen har skapats. HTTP-statuskod på 200 OK ska returneras.

       ```sh
       curl -k -H "Content-Type: application/json" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview
       ```

       Exempel på utdata:

       ```json
        {
          "id": "/iotHubs/eg-iot-edge-hub/devices/eg-edge-device/modules/eventgridmodule/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5",
          "type": "Microsoft.EventGrid/eventSubscriptions",
          "name": "sampleSubscription5",
          "properties": {
            "Topic": "MicrosoftStorage",
            "destination": {
              "endpointType": "WebHook",
              "properties": {
                "endpointUrl": "https://subscriber:4430"
              }
            }
          }
        }
       ```

       > [!IMPORTANT]
       > - För HTTPS-flödet, om klientautentisering är aktive rad via SAS-nyckel, ska den SAS-nyckel som anges ovan läggas till som en rubrik. Därför kommer spiral förfrågan att: `curl -k -H "Content-Type: application/json" -H "aeg-sas-key: <your SAS key>" -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`
       > - Om klientautentisering är aktive rad via certifikat för HTTPS-flödet blir förfrågan om att: `curl -k -H "Content-Type: application/json" --cert <certificate file> --key <certificate private key file> -X GET -g https://<your-edge-device-public-ip-here>:4438/topics/MicrosoftStorage/eventSubscriptions/sampleSubscription5?api-version=2019-01-01-preview`

3. Ladda ned [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) och [Anslut den till din lokala lagrings plats](../../iot-edge/how-to-store-data-blob.md#connect-to-your-local-storage-with-azure-storage-explorer)

## <a name="verify-event-delivery"></a>Verifiera händelse leverans

### <a name="verify-blobcreated-event-delivery"></a>Verifiera leverans av BlobCreated-händelser

1. Ladda upp filer som block blobbar till den lokala lagringen från Azure Storage Explorer, så publicerar modulen automatiskt skapa händelser. 
2. Kolla in prenumeranternas loggar för att skapa händelse. Följ stegen för att [Verifiera händelse leveransen](pub-sub-events-webhook-local.md#verify-event-delivery)

    Exempel på utdata:

    ```json
            Received Event:
            {
              "id": "d278f2aa-2558-41aa-816b-e6d8cc8fa140",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobCreated",
              "eventTime": "2019-10-01T21:35:17.7219554Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "PutBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "ef1c387b-4c3c-4ac0-8e04-ff73c859bfdc",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

### <a name="verify-blobdeleted-event-delivery"></a>Verifiera leverans av BlobDeleted-händelser

1. Ta bort blobbar från den lokala lagringen med hjälp av Azure Storage Explorer och modulen publicerar automatiskt ta bort händelser. 
2. Kolla in prenumeranterna för att ta bort händelser. Följ stegen för att [Verifiera händelse leveransen](pub-sub-events-webhook-local.md#verify-event-delivery)

    Exempel på utdata:
    
    ```json
            Received Event:
            {
              "id": "ac669b6f-8b0a-41f3-a6be-812a3ce6ac6d",
              "topic": "MicrosoftStorage",
              "subject": "/blobServices/default/containers/cont1/blobs/Team.jpg",
              "eventType": "Microsoft.Storage.BlobDeleted",
              "eventTime": "2019-10-01T21:36:09.2562941Z",
              "dataVersion": "1.0",
              "metadataVersion": "1",
              "data": {
                "api": "DeleteBlob",
                "clientRequestId": "00000000-0000-0000-0000-000000000000",
                "requestId": "2996bbfb-c819-4d02-92b1-c468cc67d8c6",
                "eTag": "0x8D746B740DA21FB",
                "url": "http://azureblobstorageoniotedge:11002/myaccount/cont1/Team.jpg",
                "contentType": "image/jpeg",
                "contentLength": 858129,
                "blobType": "BlockBlob"
              }
            }
    ```

Grattis! Du har slutfört självstudien. I följande avsnitt finns information om händelse egenskaperna.

### <a name="event-properties"></a>Händelse egenskaper

Här är en lista över de händelse egenskaper som stöds och deras typer och beskrivningar. 

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| ämne | sträng | Fullständig resurs Sök väg till händelse källan. Det går inte att skriva till det här fältet. Event Grid ger det här värdet. |
| Ämne | sträng | Utgivardefinierad sökväg till händelseobjektet. |
| Händelsetyp | sträng | En av de registrerade händelsetyperna för den här händelsekällan. |
| Händelsetid | sträng | Tiden då händelsen genereras baserat på providerns UTC-tid. |
| id | sträng | Unikt ID för händelsen. |
| data | objekt | Händelse data för Blob Storage. |
| Dataversion | sträng | Dataobjektets schemaversion. Utgivaren definierar schemaversion. |
| Metadataversion | sträng | Schemaversionen av händelsens metadata. Event Grid definierar schemat för de översta egenskaperna. Event Grid ger det här värdet. |

Data-objektet har följande egenskaper:

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| api | sträng | Den åtgärd som utlöste händelsen. Det kan vara något av följande värden: <ul><li>BlobCreated-tillåtna värden är: `PutBlob` och `PutBlockList`</li><li>BlobDeleted-tillåtna värden är `DeleteBlob` `DeleteAfterUpload` och `AutoDelete` . <p>`DeleteAfterUpload`Händelsen genereras när BLOB tas bort automatiskt eftersom den önskade deleteAfterUpload-egenskapen har angetts till true. </p><p>`AutoDelete` händelsen genereras när blobben tas bort automatiskt eftersom det önskade egenskap svärdet för deleteAfterMinutes har upphört att gälla.</p></li></ul>|
| clientRequestId | sträng | ett ID för begäran som tillhandahållits för Storage API-åtgärden. Detta ID kan användas för att korrelera Azure Storage diagnostikloggar med hjälp av fältet "client-Request-ID" i loggarna och kan tillhandahållas i klient begär Anden med hjälp av huvudet "x-MS-client-Request-ID". Mer information finns i [logg format](/rest/api/storageservices/storage-analytics-log-format). |
| requestId | sträng | Service-genererat förfrågnings-ID för Storage API-åtgärden. Kan användas för att korrelera Azure Storage diagnostikloggar som använder fältet "Request-ID-huvud" i loggarna och returneras från initiering av API-anrop i huvudet "x-MS-Request-ID". Se [logg format](/rest/api/storageservices/storage-analytics-log-format). |
| eTag | sträng | Det värde som du kan använda för att utföra åtgärder villkorligt. |
| Innehålls | sträng | Den innehålls typ som angetts för blobben. |
| contentLength | heltal | Storleken på blobben i byte. |
| blobType | sträng | Typ av BLOB. Giltiga värden är antingen "BlockBlob" eller "PageBlob". |
| url | sträng | Sökvägen till blobben. <br>Om klienten använder en BLOB-REST API, har URL: en följande struktur: *\<storage-account-name\> . blob.Core.Windows.net/ \<container-name\> / \<file-name\>*. <br>Om klienten använder en Data Lake Storage REST API har URL: en följande struktur: *\<storage-account-name\> . DFS.Core.Windows.net/ \<file-system-name\> / \<file-name\>*. |


## <a name="next-steps"></a>Nästa steg

Se följande artiklar i Blob Storage-dokumentationen:

- [Filtrera Blob Storage händelser](../../storage/blobs/storage-blob-event-overview.md#filtering-events)
- [Rekommenderade metoder för att konsumera Blob Storage händelser](../../storage/blobs/storage-blob-event-overview.md#practices-for-consuming-events)

I den här självstudien publicerade du händelser genom att skapa eller ta bort blobbar i ett Azure-Blob Storage. Se de andra självstudierna för att lära dig hur du vidarebefordrar händelser till molnet (Azure Event Hub eller Azure IoT Hub): 

- [Vidarekoppla händelser till Azure Event Grid](forward-events-event-grid-cloud.md)
- [Vidarekoppla händelser till Azure IoT Hub](forward-events-iothub.md)
