---
title: 'Självstudie: skicka enhets data via transparent Gateway – Machine Learning på Azure IoT Edge'
description: Den här självstudien visar hur du kan använda din utvecklings dator som en simulerad IoT Edge enhet för att skicka data till IoT Hub genom att gå igenom en enhet som kon figurer ATS som en transparent Gateway.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 718f4c0b41400ae2e35b88820b2d4154dab23111
ms.sourcegitcommit: 2bab7c1cd1792ec389a488c6190e4d90f8ca503b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88272832"
---
# <a name="tutorial-send-data-via-transparent-gateway"></a>Självstudie: skicka data via transparent Gateway

> [!NOTE]
> Den här artikeln ingår i en serie för självstudier om hur du använder Azure Machine Learning på IoT Edge. Om du har kommit till den här artikeln direkt rekommenderar vi att du börjar med den [första artikeln](tutorial-machine-learning-edge-01-intro.md) i serien för bästa möjliga resultat.

I den här artikeln använder vi den virtuella utvecklings datorn igen som en simulerad enhet. I stället för att skicka data direkt till IoT Hub skickar enheten data till den IoT Edge enheten som kon figurer ATS som en transparent Gateway.

Vi övervakar IoT Edge enhetens funktion medan den simulerade enheten skickar data. När enheten har körts klart tittar vi på data i vårt lagrings konto för att kontrol lera att allt fungerade som förväntat.

Det här steget utförs vanligt vis av en moln-eller enhets utvecklare.

## <a name="review-device-harness"></a>Granska enhetens nät

Återanvänd [DeviceHarness-projektet](tutorial-machine-learning-edge-03-generate-data.md) för att simulera den underordnade enheten (eller löv). För att ansluta till den transparenta gatewayen krävs ytterligare två saker:

* Registrera certifikatet så att den underordnade IoT-enheten litar på den certifikat utfärdare som används av den IoT Edge körningen. I vårt fall är den underordnade enheten den virtuella utvecklings datorn.
* Lägg till det fullständigt kvalificerade domän namnet (FQDN) för Edge gateway i enhets anslutnings strängen.

Titta på koden för att se hur dessa två objekt implementeras.

1. Öppna Visual Studio Code på din utvecklings dator.

1. Använd öppna **fil**-  >  **mapp...** för att öppna C: \\ Source \\ IoTEdgeAndMlSample \\ DeviceHarness.

1. Titta på metoden InstallCertificate () i Program.cs.

1. Observera att om koden hittar certifikat Sök vägen anropas metoden CertificateManager. InstallCACert för att installera certifikatet på datorn.

1. Titta nu på GetIotHubDevice-metoden i TurbofanDevice-klassen.

1. När användaren anger FQDN för gatewayen med alternativet "-g" skickas värdet till den här metoden som `gatewayFqdn` variabeln, som läggs till i enhets anslutnings strängen.

   ```csharp
   connectionString = $"{connectionString};GatewayHostName={gatewayFqdn.ToLower()}";
   ```

## <a name="build-and-run-leaf-device"></a>Skapa och kör löv enhet

1. Skapa projektet med DeviceHarness-projektet fortfarande öppet i Visual Studio Code. Välj **Kör skapa uppgift** på **Terminal** -menyn och välj **skapa**.

1. Hitta det fullständigt kvalificerade domän namnet (FQDN) för din Edge-Gateway genom att gå till din IoT Edge-enhet (Linux VM) i Azure Portal och kopiera värdet för **DNS-namn** från översikts sidan.

1. Starta din IoT-enhet (Linux VM) om den inte redan körs.

1. Öppna Visual Studio Code Terminal. På menyn **Terminal** väljer du **ny terminal** och kör följande kommando och ersätter `<edge_device_fqdn>` med DNS-namnet som du kopierade från IoT Edge enhet (Linux VM):

   ```cmd
   dotnet run -- --gateway-host-name "<edge_device_fqdn>" --certificate C:\edgecertificates\certs\azure-iot-test-only.root.ca.cert.pem --max-devices 1
   ```

1. Programmet försöker installera certifikatet på din utvecklings dator. När det gör det accepterar du säkerhets varningen.

1. När du uppmanas att ange anslutnings strängen för IoT Hub klickar du på ellipsen (**...**) på panelen Azure IoT Hub-enheter och väljer **Kopiera IoT Hub anslutnings sträng**. Klistra in värdet i terminalen.

1. Utdata visas som:

   ```output
   Found existing device: Client_001
   Using device connection string: HostName=<your hub>.azure-devices.net;DeviceId=Client_001;SharedAccessKey=xxxxxxx; GatewayHostName=iotedge-xxxxxx.<region>.cloudapp.azure.com
   Device: 1 Message count: 50
   Device: 1 Message count: 100
   Device: 1 Message count: 150
   Device: 1 Message count: 200
   Device: 1 Message count: 250
   ```

   Observera att tillägget "GatewayHostName" läggs till i enhets anslutnings strängen, vilket gör att enheten kommunicerar via IoT Hub via den IoT Edge transparenta gatewayen.

## <a name="check-output"></a>Kontrol lera utdata

### <a name="iot-edge-device-output"></a>IoT Edge enhets utdata

Utdata från avroFileWriter-modulen kan observeras enkelt genom att titta på den IoT Edge enheten.

1. SSH i din IoT Edge virtuella dator.

1. Sök efter filer som skrivs till disk.

   ```bash
   find /data/avrofiles -type f
   ```

1. Kommandots utdata ser ut som i följande exempel:

   ```output
   /data/avrofiles/2019/4/18/22/10.avro
   ```

   Du kan ha fler än en fil beroende på tidpunkten för körningen.

1. Var uppmärksam på tidsstämplar. AvroFileWriter-modulen överför filerna till molnet när den senaste ändrings tiden är mer än 10 minuter tidigare (se MODIFIERAd \_ fil-timeout i \_ Uploader.py i avroFileWriter-modulen).

1. När 10 minuter har förflutit ska modulen Ladda upp filerna. Om överföringen lyckas tas filerna bort från disken.

### <a name="azure-storage"></a>Azure Storage

Vi kan se resultatet av vår löv enhet som skickar data genom att titta på de lagrings konton där vi förväntar sig att data ska vidarebefordras.

1. Öppna Visual Studio Code på utvecklings datorn.

1. I panelen "AZURE STORAGE" i fönstret utforska navigerar du i trädet för att hitta ditt lagrings konto.

1. Expandera noden **BLOB-behållare** .

1. Från arbetet som vi gjorde i föregående del av själv studie kursen förväntar vi dig att **ruldata** -behållaren ska innehålla meddelanden med RUL. Expandera noden **ruldata** .

1. Du kommer att se en eller flera BLOB-filer som heter: `<IoT Hub Name>/<partition>/<year>/<month>/<day>/<hour>/<minute>` .

1. Högerklicka på en av filerna och välj **Ladda ned BLOB** för att spara filen på din utvecklings dator.

1. Expandera sedan **uploadturbofanfiles** -noden. I föregående artikel anger vi den här platsen som mål för filer som laddats upp av avroFileWriter-modulen.

1. Högerklicka på filerna och välj **Hämta BLOB** för att spara den på din utvecklings dator.

### <a name="read-avro-file-contents"></a>Läsa innehållet i Avro-filen

Vi har inkluderat ett enkelt kommando rads verktyg för att läsa en Avro-fil och returnera en JSON-sträng för meddelandena i filen. I det här avsnittet ska vi installera och köra det.

1. Öppna en Terminal i Visual Studio Code (**Terminal**  >  -**ny terminal**).

1. Installera hubavroreader:

   ```cmd
   pip install c:\source\IoTEdgeAndMlSample\HubAvroReader
   ```

1. Använd hubavroreader för att läsa Avro-filen som du laddade ned från **ruldata**.

   ```cmd
   hubavroreader <avro file with ath> | more
   ```

1. Observera att bröd texten i meddelandet ser ut som förväntat med enhets-ID och förutsägande RUL.

   ```json
   {
       "Body": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CycleTime": 1.0,
           "PredictedRul": 170.1723693909444
       },
       "EnqueuedTimeUtc": "<time>",
       "Properties": {
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "CreationTimeUtc": "01/01/0001 00:00:00",
           "EnqueuedTimeUtc": "01/01/0001 00:00:00"
       },
       "SystemProperties": {
           "connectionAuthMethod": "{\"scope\":\"module\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
           "connectionDeviceGenerationId": "636857841798304970",
           "connectionDeviceId": "aaTurbofanEdgeDevice",
           "connectionModuleId": "turbofanRouter",
           "contentEncoding": "utf-8",
           "contentType": "application/json",
           "correlationId": "3d0bc256-b996-455c-8930-99d89d351987",
           "enqueuedTime": "<time>",
           "iotHubName": "mledgeiotwalkthroughhub"
       }
   }
   ```

1. Kör samma kommando och skicka Avro-filen som du laddade ned från **uploadturbofanfiles**.

1. Som förväntat innehåller dessa meddelanden alla sensor data och drift inställningar från det ursprungliga meddelandet. Dessa data kan användas för att förbättra RUL-modellen på vår gräns enhet.

   ```json
   {
       "Body": {
           "CycleTime": 1.0,
           "OperationalSetting1": -0.0005000000237487257,
           "OperationalSetting2": 0.00039999998989515007,
           "OperationalSetting3": 100.0,
           "PredictedRul": 170.17236328125,
           "Sensor1": 518.6699829101562,
           "Sensor10": 1.2999999523162842,
           "Sensor11": 47.29999923706055,
           "Sensor12": 522.3099975585938,
           "Sensor13": 2388.010009765625,
           "Sensor14": 8145.31982421875,
           "Sensor15": 8.424599647521973,
           "Sensor16": 0.029999999329447746,
           "Sensor17": 391.0,
           "Sensor18": 2388.0,
           "Sensor19": 100.0,
           "Sensor2": 642.3599853515625,
           "Sensor20": 39.11000061035156,
           "Sensor21": 23.353700637817383,
           "Sensor3": 1583.22998046875,
           "Sensor4": 1396.8399658203125,
           "Sensor5": 14.619999885559082,
           "Sensor6": 21.610000610351562,
           "Sensor7": 553.969970703125,
           "Sensor8": 2387.9599609375,
           "Sensor9": 9062.169921875
       },
           "ConnectionDeviceId": "Client_001",
           "CorrelationId": "70df0c98-0958-4c8f-a422-77c2a599594f",
           "CreationTimeUtc": "0001-01-01T00:00:00+00:00",
           "EnqueuedTimeUtc": "<time>"
   }
   ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att utforska de resurser som används av den här självstudien väntar du tills du är klar för att rensa de resurser som du har skapat. Annars använder du följande steg för att ta bort dem:

1. Ta bort de resurs grupper som har skapats för att lagra den virtuella dev-datorn, IoT Edge VM, IoT Hub, lagrings konto, Machine Learning-arbetsyta (och skapade resurser: container Registry, Application Insights, nyckel valv, lagrings konto).

1. Ta bort Machine Learning-projektet i [Azure Notebooks](https://notebooks.azure.com).

1. Om du har klonat lagrings platsen lokalt, Stäng alla PowerShell-eller VS Code-fönster som refererar till den lokala lagrings platsen och ta sedan bort lagrings platsen-katalogen.

1. Om du har skapat certifikat lokalt, tar du bort mappen c: \\ edgeCertificates.

## <a name="next-steps"></a>Nästa steg

I den här artikeln använde vi vår utvecklings-VM för att simulera en löv enhet som skickar sensor-och drift data till vår IoT Edge-enhet. Vi validerade att modulerna på enheten dirigerade, klassificerade, sparade och överförde data genom att undersöka real tids åtgärden hos gräns enheten och genom att titta på filerna som överförts till lagrings kontot.

Mer information finns på följande sidor:

* [Ansluta en underordnad enhet till en Azure IoT Edge-gateway](how-to-connect-downstream-device.md)
* [Lagra data på gränsen med Azure Blob Storage på IoT Edge (förhands granskning)](how-to-store-data-blob.md)
