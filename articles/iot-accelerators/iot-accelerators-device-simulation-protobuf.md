---
title: Använda protokollbuffertar med enhetssimulering – Azure| Microsoft Docs
description: I den här guiden lär du dig att använda protokollbuffertar för att serialisera telemetri som skickas från lösningsacceleratorn för enhetssimulering.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: a94f3cc6b2387857d19c7b98fa0be64dd6646ec9
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713866"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Serialisera telemetri med protokollbuffertar

Protocol Buffers (Protobuf) är ett binärt serialiseringsformat för strukturerade data. Protobuf är utformat för att betona enkelhet och prestanda med målet att vara mindre och snabbare än XML.

Enhetssimulering stöder **proto3-versionen** av protokollbuffertspråket.

Eftersom Protobuf kräver kompilerad kod för att serialisera data måste du skapa en anpassad version av enhetssimuleringen.

Stegen i den här instruktionerna visar hur du:

1. Förbereda en utvecklingsmiljö
1. Ange med Protobuf-formatet i en enhetsmodell
1. Definiera ditt Protobuf-format
1. Generera Protobuf-klasser
1. Testa lokalt

## <a name="prerequisites"></a>Förutsättningar

Om du vill följa stegen i den här instruktionerna behöver du:

* Visual Studio Code. Du kan ladda [Visual Studio Code för Mac, Linux och Windows.](https://code.visualstudio.com/download)
* .NET Core. Du kan ladda [ned .NET Core för Mac, Linux och Windows.](https://www.microsoft.com/net/download)
* Brevbäraren. Du kan ladda [ned Postman för Mac, Windows eller Linux](https://www.getpostman.com/apps).
* En [IoT-hubb som distribuerats till din Azure-prenumeration.](../iot-hub/iot-hub-create-through-portal.md) Du behöver anslutningssträngen för IoT-hubben för att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure Portal.
* En [Cosmos DB-databas distribuerad till din Azure-prenumeration](../cosmos-db/create-sql-api-dotnet.md#create-account) som använder SQL-API:et och som har konfigurerats för [stark konsekvens](../cosmos-db/how-to-manage-database-account.md). Du behöver Cosmos DB databasens anslutningssträng för att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure Portal.
* Ett [Azure Storage-konto som distribuerats till din Azure-prenumeration.](../storage/common/storage-account-create.md) Du behöver anslutningssträngen för lagringskontot för att slutföra stegen i den här guiden. Du kan hämta anslutningssträngen från Azure Portal.

## <a name="prepare-your-development-environment"></a>Förbereda utvecklingsmiljön

Förbered utvecklingsmiljön genom att utföra följande uppgifter:

* Ladda ned källan för enhetssimuleringens mikrotjänst.
* Ladda ned källan för lagringskortets mikrotjänst.
* Kör lagringskortets mikrotjänst lokalt.

Anvisningarna i den här artikeln förutsätter att du använder Windows. Om du använder ett annat operativsystem kan du behöva justera några av filsökvägarna och kommandona så att de passar din miljö.

### <a name="download-the-microservices"></a>Ladda ned mikrotjänster

Ladda ned och packa upp [mikrotjänster för fjärrövervakning från](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip) GitHub till en lämplig plats på den lokala datorn. Den här lagringsplatsen innehåller den mikrotjänst för lagringskort som du behöver för den här i vilken du gör detta.

Ladda ned och packa upp [mikrotjänsten för enhetssimulering](https://github.com/Azure/azure-iot-pcs-device-simulation/archive/master.zip) från GitHub till en lämplig plats på den lokala datorn.

### <a name="run-the-storage-adapter-microservice"></a>Köra mikrotjänsten för lagringskortet

I Visual Studio Code öppnar du mappen **remote-monitoring-services-dotnet-master\storage-adapter.** Klicka på **återställningsknapparna** för att åtgärda olösta beroenden.

Öppna **filen .vscode/launch.js** och tilldela Cosmos DB-anslutningssträngen till **miljövariabeln PCS \_ STORAGEADAPTER \_ DOCUMENTDB \_ CONNSTRING.**

> [!NOTE]
> När du kör mikrotjänsten lokalt på datorn krävs det fortfarande en Cosmos DB i Azure för att fungera korrekt.

Om du vill köra lagringskortets mikrotjänst lokalt **klickar du på \> Felsök Starta felsökning.**

I **terminalfönstret** i Visual Studio Code visas utdata från den mikrotjänst som körs, inklusive en URL för webbtjänstens hälsokontroll: <http://127.0.0.1:9022/v1/status> . När du navigerar till den här adressen ska statusen vara "OK: Levande och väl".

Låt lagringskortets mikrotjänst köras i den här instansen Visual Studio Code medan du utför följande steg.

## <a name="define-your-device-model"></a>Definiera din enhetsmodell

Öppna mappen **device-simulation-dotnet-master** som du laddade ned från GitHub i en ny instans av Visual Studio Code. Klicka på **återställningsknapparna** för att åtgärda eventuella olösta beroenden.

I den här guiden skapar du en ny enhetsmodell för en tillgångsspårare:

1. Skapa en ny enhetsmodellfil med **namnetassettracker-01.jspå** i mappen **Services\data\devicemodels.**

1. Definiera enhetsfunktionen i enhetsmodellen som **assettracker-01.jspå** filen. Telemetriavsnittet i en Protobuf-enhetsmodell måste:

   * Inkludera namnet på den Protobuf-klass som du genererar för din enhet. I följande avsnitt visas hur du genererar den här klassen.
   * Ange Protobuf som meddelandeformat.

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

### <a name="create-device-behaviors-script"></a>Skapa skript för enhetsbeteenden

Skriv beteendeskriptet som definierar hur enheten beter sig. Mer information finns i [Skapa en avancerad simulerad enhet.](iot-accelerators-device-simulation-advanced-device.md)

## <a name="define-your-protobuf-format"></a>Definiera ditt Protobuf-format

När du har en enhetsmodell och har fastställt meddelandeformatet kan du skapa en **proto-fil.** I **proto-filen** lägger du till:

* En `csharp_namespace` som matchar egenskapen **ClassName** i din enhetsmodell.
* Ett meddelande för varje datastruktur som ska serialiseras.
* Ett namn och en typ för varje fält i meddelandet.

1. Skapa en ny fil med **namnet assettracker.proto** i mappen **Services\Models\Protobuf\proto.**

1. Definiera syntax, namnområde och meddelandeschema i **proto-filen** enligt följande:

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

Markörerna `=1` för varje element anger en unik tagg som fältet använder i `=2` binär kodning. Tal 1–15 kräver en mindre byte för att koda än högre tal.

## <a name="generate-the-protobuf-class"></a>Generera Protobuf-klassen

När du har en **proto-fil** är nästa steg att generera de klasser som behövs för att läsa och skriva meddelanden. För att slutföra det här steget behöver du **Protoc** Protobuf-kompilatorn.

1. [Ladda ned Protobuf-kompilatorn från GitHub](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. Kör kompilatorn och ange källkatalogen, målkatalogen och namnet på **proto-filen.** Exempel:

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    Det här kommandot genererar **en Assettracker.cs-fil** i **mappen Services\Models\Protobuf.**

## <a name="test-protobuf-locally"></a>Testa Protobuf lokalt

I det här avsnittet testar du tillgångsspårarenheten som du skapade i föregående avsnitt lokalt.

### <a name="run-the-device-simulation-microservice"></a>Köra mikrotjänsten för enhetssimulering

Öppna **filen .vscode/launch.js** och tilldela följande:

* IoT Hub anslutningssträngen till **\_ miljövariabeln PCS IOTHUB \_ CONNSTRING.**
* Anslutningssträng för lagringskonto till **\_ miljövariabeln PCS AZURE \_ \_ STORAGE ACCOUNT.**
* Cosmos DB anslutningssträngen till **\_ miljövariabeln PCS STORAGEADAPTER \_ \_ DOCUMENTDB CONNSTRING.**

Öppna filen **WebService/Properties/launchSettings.jsoch** tilldela följande:

* IoT Hub anslutningssträngen till **\_ miljövariabeln PCS IOTHUB \_ CONNSTRING.**
* Anslutningssträng för lagringskonto till **\_ miljövariabeln PCS AZURE \_ \_ STORAGE ACCOUNT.**
* Cosmos DB anslutningssträngen till **\_ miljövariabeln PCS STORAGEADAPTER \_ \_ DOCUMENTDB CONNSTRING.**

Öppna **WebService\appsettings.ini** och ändra inställningarna på följande sätt:

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>Konfigurera lösningen så att den innehåller dina nya enhetsmodellfiler

Som standard kopieras inte dina nya JSON- och JS-filer för enhetsmodellen till den inbyggda lösningen. Du måste uttryckligen inkludera dem.

Lägg till en post i **filen services\services.csproj** för varje fil som du vill inkludera. Exempel:

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

Om du vill köra mikrotjänsten lokalt klickar **du på \> Felsök Starta felsökning.**

**Terminalfönstret** i Visual Studio Code visar utdata från den mikrotjänst som körs.

Låt mikrotjänsten för enhetssimuleringen köras i den här instansen Visual Studio Code medan du slutför nästa steg.

### <a name="set-up-a-monitor-for-device-events"></a>Konfigurera en övervakare för enhetshändelser

I det här avsnittet använder du Azure CLI för att konfigurera en händelseövervakare för att visa telemetri som skickas från de enheter som är anslutna till din IoT-hubb.

Följande skript förutsätter att namnet på din IoT-hubb är **device-simulation-test**.

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

Låt händelseövervakaren köras medan du testar de simulerade enheterna.

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>Skapa en simulering med enhetstyp för tillgångsspårare

I det här avsnittet använder du Postman-verktyget för att begära mikrotjänsten för enhetssimulering för att köra en simulering med hjälp av enhetstypen tillgångsspårare. Postman är ett verktyg som gör att du kan skicka REST-begäranden till en webbtjänst.

Så här ställer du in Postman:

1. Öppna Postman på den lokala datorn.

1. Klicka **på \> Filimport.** Klicka sedan **på Välj filer.**

1. Välj **Azure IoT Device Simulation solution accelerator.postman \_ collection (Azure IoT-enhetssimuleringslösningsaccelerator.postman-samling)** och **Azure IoT Device Simulation solution accelerator.postman \_ environment (Azure IoT-enhetssimuleringslösningsaccelerator.postman-miljö)** och klicka **på Öppna**.

1. Expandera **lösningsacceleratorn för Azure IoT-enhetssimulering** för att visa de begäranden som du kan skicka.

1. Klicka **på Ingen miljö** och välj **lösningsacceleratorn för Azure IoT-enhetssimulering.**

Nu har du en samling och miljö som har lästs in på Din Postman-arbetsyta som du kan använda för att interagera med mikrotjänsten för enhetssimulering.

Så här konfigurerar och kör du simuleringen:

1. I Postman-samlingen väljer du Skapa **tillgångsspårarsimulering** och klickar på **Skicka**. Den här begäran skapar fyra instanser av enhetstypen för den simulerade tillgångsspåraren.

1. Händelseövervakarutdata i Azure CLI-fönstret visar telemetrin från de simulerade enheterna.

Om du vill stoppa simuleringen väljer du begäran **Stoppa simulering** i Postman och klickar på **Skicka**.

### <a name="clean-up-resources"></a>Rensa resurser

Du kan stoppa de två mikrotjänster som körs lokalt i deras Visual Studio Code-instanser **(Felsökning \> stoppa felsökning).**

Om du inte längre behöver IoT Hub och Cosmos DB instanser tar du bort dem från din Azure-prenumeration för att undvika onödiga kostnader.

## <a name="iot-hub-support"></a>IoT Hub Support

Många IoT Hub funktioner har inte inbyggt stöd för Protobuf eller andra binära format. Du kan till exempel inte dirigera baserat på meddelandenyttolasten eftersom IoT Hub inte kan bearbeta meddelandenyttolasten. Du kan dock dirigera baserat på meddelandehuvuden.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du anpassar enhetssimuleringen för att använda Protobuf för att skicka telemetri är nästa steg att besöka GitHub-lagringsplatsen för att lära dig mer [om enhetssimulering.](https://github.com/Azure/azure-iot-pcs-device-simulation)
