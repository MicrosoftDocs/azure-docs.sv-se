---
title: Använda protokoll buffertar med enhets simulering – Azure | Microsoft Docs
description: I den här instruktions guiden får du lära dig hur du använder protokoll-buffertar för att serialisera telemetri som skickas från lösnings acceleratorn för enhets simulering.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96852158"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serialisera telemetri med hjälp av Protocol buffrar

Protocol Buffers (protobuf) är ett format för binär serialisering för strukturerade data. Protobuf är utformat för att framhäva enkelhet och prestanda med målet att vara mindre och snabbare än XML.

Enhets simuleringen stöder **proto3** -versionen av Protocol buffer-språket.

Eftersom protobuf kräver kompilerad kod för att serialisera data, måste du bygga en anpassad version av enhets simuleringen.

Stegen i den här instruktions guiden visar hur du:

1. Förbered en utvecklings miljö
1. Ange med protobuf-formatet i en enhets modell
1. Definiera ditt protobuf-format
1. Generera protobuf-klasser
1. Testa lokalt

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här instruktions guiden behöver du:

* Visual Studio Code. Du kan hämta [Visual Studio Code för Mac, Linux och Windows](https://code.visualstudio.com/download).
* .NET Core. Du kan ladda ned [.net Core för Mac, Linux och Windows](https://www.microsoft.com/net/download).
* Postman. Du kan hämta [Postman för Mac, Windows eller Linux](https://www.getpostman.com/apps).
* En [IoT-hubb som distribueras till din Azure-prenumeration](../iot-hub/iot-hub-create-through-portal.md). Du behöver IoT-hubbens anslutnings sträng för att slutföra stegen i den här hand boken. Du kan hämta anslutnings strängen från Azure Portal.
* En [Cosmos DB-databas som distribueras till din Azure-prenumeration](../cosmos-db/create-sql-api-dotnet.md#create-account) som använder SQL API och som har kon figurer ATS för [stark konsekvens](../cosmos-db/how-to-manage-database-account.md). Du behöver Cosmos DB databasens anslutnings sträng för att slutföra stegen i den här hand boken. Du kan hämta anslutnings strängen från Azure Portal.
* Ett [Azure Storage-konto som distribueras till din Azure-prenumeration](../storage/common/storage-account-create.md). Du behöver lagrings kontots anslutnings sträng för att slutföra stegen i den här hand boken. Du kan hämta anslutnings strängen från Azure Portal.

## <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Utför följande uppgifter för att förbereda utvecklings miljön:

* Hämta källan för mikrotjänsten Device simulering.
* Ladda ned källan för Storage adapter-mikrotjänsten.
* Kör Storage adapter-mikrotjänsten lokalt.

I instruktionerna i den här artikeln förutsätter vi att du använder Windows. Om du använder ett annat operativ system kan du behöva justera några av fil Sök vägarna och kommandona för att passa din miljö.

### <a name="download-the-microservices"></a>Ladda ned mikrotjänster

Ladda ned och zippa upp [mikrotjänsterna för fjärrövervakning](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn. Den här lagrings platsen innehåller den mikrotjänst för lagrings kort som du behöver för den här instruktionen.

Ladda ned och zippa upp [mikrotjänsten för enhets simulering](https://github.com/Azure/device-simulation-dotnet/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn.

### <a name="run-the-storage-adapter-microservice"></a>Köra mikrotjänsten Storage adapter

Öppna mappen **Remote-Monitoring-Services-dotNet-master\storage-adapter** i Visual Studio Code. Klicka på alla **återställnings** knappar för att åtgärda olösta beroenden.

Öppna filen **. VSCode/launch.jspå** filen och tilldela Cosmos DB anslutnings sträng till **datorerna \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** .

> [!NOTE]
> När du kör mikrotjänsten lokalt på din dator kräver den fortfarande en Cosmos DB instans i Azure för att fungera korrekt.

Om du vill köra en mikrotjänst i Storage-adaptern lokalt klickar du på **Felsök \> Starta fel sökning**.

**Terminalfönstret** i Visual Studio Code visar utdata från den aktiva mikrotjänsten inklusive en URL för webb tjänstens hälso kontroll: <http://127.0.0.1:9022/v1/status> . När du navigerar till den här adressen ska statusen vara "OK: Alive".

Lämna den mikrotjänsten Storage adapter som körs i den här instansen av Visual Studio Code medan du utför följande steg.

## <a name="define-your-device-model"></a>Definiera din enhets modell

Öppna mappen **Device-simulering-dotNet-Master** som du laddade ned från GitHub i en ny instans av Visual Studio Code. Klicka på alla **återställnings** knappar för att åtgärda eventuella olösta beroenden.

I den här instruktions guiden skapar du en ny enhets modell för en till gångs Spårare:

1. Skapa en ny enhets modell fil med namnet **assettracker-01.js** i mappen **Services\data\devicemodels**

1. Definiera enhets funktionen i enhets modell **assettracker-01.jsi** filen. Avsnittet telemetri i en protobuf-enhets modell måste:

   * Inkludera namnet på protobuf-klassen som du genererar för enheten. I följande avsnitt visas hur du skapar den här klassen.
   * Ange protobuf som meddelande format.

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>Skapa enhets beteende skript

Skriv beteende skriptet som definierar hur enheten beter sig. Mer information finns i [skapa en avancerad simulerad enhet](iot-accelerators-device-simulation-advanced-device.md).

## <a name="define-your-protobuf-format"></a>Definiera ditt protobuf-format

När du har en enhets modell och bestämt meddelande formatet, kan du skapa en **proto** -fil. I **proto** -filen lägger du till:

* En `csharp_namespace` som matchar egenskapen **className** i din enhets modell.
* Ett meddelande för varje data struktur som ska serialiseras.
* Ett namn och en typ för varje fält i meddelandet.

1. Skapa en ny fil med namnet **assettracker. proto** i mappen **Services\Models\Protobuf\proto** .

1. Definiera syntax, namnrymd och meddelande schema i filen **proto** enligt följande:

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

`=1` `=2` Markörerna för varje element anger en unik tagg som används i den binära kodningen i fältet. Nummer 1-15 kräver en mindre byte för att koda än högre tal.

## <a name="generate-the-protobuf-class"></a>Generera protobuf-klassen

När du har en **proto** -fil är nästa steg att generera de klasser som behövs för att läsa och skriva meddelanden. För att slutföra det här steget behöver du **Protoc** protobuf-kompilatorn.

1. [Ladda ned protobuf-kompilatorn från GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Kör kompilatorn, ange käll katalogen, mål katalogen och namnet på din **proto** -fil. Exempel:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Det här kommandot genererar en **Assettracker. cs** -fil i mappen **Services\Models\Protobuf** .

## <a name="test-protobuf-locally"></a>Testa protobuf lokalt

I det här avsnittet testar du den till gångs spårnings enhet som du skapade i föregående avsnitt lokalt.

### <a name="run-the-device-simulation-microservice"></a>Kör mikrotjänsten enhets simulering

Öppna filen **. VSCode/launch.jspå** filen och tilldela din:

* IoT Hub anslutnings sträng till **PC- \_ IOTHUB \_ CONNSTRING** -miljövariabeln.
* Anslutnings sträng för lagrings konto **till \_ Azure \_ Storage- \_ kontots** miljö variabel.
* Cosmos DB anslutnings sträng till **PC- \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** Environment variabel.

Öppna **webbtjänst/egenskaper/launchSettings.jspå** filen och tilldela din:

* IoT Hub anslutnings sträng till **PC- \_ IOTHUB \_ CONNSTRING** -miljövariabeln.
* Anslutnings sträng för lagrings konto **till \_ Azure \_ Storage- \_ kontots** miljö variabel.
* Cosmos DB anslutnings sträng till **PC- \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING** Environment variabel.

Öppna **WebService\appsettings.ini** -filen och ändra inställningarna enligt följande:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Konfigurera lösningen så att den innehåller de nya filerna för enhets modellen

Som standard kopieras inte dina nya enhets modell-JSON-och JS-filer till den färdiga lösningen. Du måste uttryckligen inkludera dem.

Lägg till en post i **services\services.CSPROJ** -filen för varje fil som du vill inkludera. Exempel:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Kör mikrotjänsten lokalt genom att klicka på **Felsök \> Starta fel sökning**.

**Terminalfönstret** i Visual Studio Code visar utdata från den aktiva mikrotjänsten.

Lämna mikrotjänsten för enhets simulering som körs i den här instansen av Visual Studio Code medan du slutför nästa steg.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurera en Övervakare för enhets händelser

I det här avsnittet använder du Azure CLI för att konfigurera en händelse Övervakare för att Visa telemetri som skickas från enheter som är anslutna till din IoT-hubb.

Följande skript förutsätter att namnet på din IoT Hub är **Device-simulering-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Lämna händelse övervakaren igång medan du testar de simulerade enheterna.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Skapa en simulering med enhets typen till gångs Spårare

I det här avsnittet ska du använda Postman-verktyget för att begära mikrotjänsten för enhets simulering för att köra en simulering med enhets typen till gångs spårare. Postman är ett verktyg som du kan använda för att skicka REST-begäranden till en webb tjänst.

Så här konfigurerar du Postman:

1. Öppna Postman på den lokala datorn.

1. Klicka på **fil \> import**. Klicka sedan på **Välj filer**.

1. Välj **Azure IoT Device simulering Solution Accelerator. Postman- \_ samling** och **Azure IoT Device simulering Solution Accelerator. Postman- \_ miljö** och klicka på **Öppna**.

1. Expandera **Solution Accelerator för Azure IoT Device simulering** för att visa de begär Anden som du kan skicka.

1. Klicka på **ingen miljö** och välj **Azure IoT Device simulering Solution Accelerator**.

Nu har du en samling och en miljö som har lästs in i Postman-arbetsytan som du kan använda för att interagera med mikrotjänsten Device simulering.

Konfigurera och köra simuleringen:

1. I Postman-samlingen väljer du **skapa simulering av till gångs spår** och klickar på **Skicka**. Den här begäran skapar fyra instanser av enhets typen simulerad till gångs spårare.

1. Händelse övervakarens utdata i fönstret Azure CLI visar Telemetrin från de simulerade enheterna.

Stoppa simuleringen genom att välja begäran **stoppa simulering** i Postman och klicka på **Skicka**.

### <a name="clean-up-resources"></a>Rensa resurser

Du kan stoppa de två lokalt använda mikrotjänster i sina Visual Studio Code-instanser (**Felsök \> stoppa fel sökning**).

Om du inte längre behöver IoT Hub och Cosmos DB instanser tar du bort dem från din Azure-prenumeration för att undvika onödiga kostnader.

## <a name="iot-hub-support"></a>IoT Hub support

Många IoT Hub funktioner har inte inbyggt stöd för protobuf eller andra binära format. Du kan till exempel inte dirigera utifrån meddelandets nytto Last eftersom IoT Hub inte kan bearbeta meddelandets nytto Last. Du kan dock dirigera baserat på meddelande rubriker.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du anpassar enhets simuleringen för att använda protobuf för att skicka telemetri, nästa steg finns på GitHub-lagringsplatsen för att lära dig mer [enhets simulering](https://github.com/Azure/device-simulation-dotnet).
