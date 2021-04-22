---
title: Utveckla och felsöka moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att utveckla, bygga och felsöka en modul för Azure IoT Edge med C#, Python, Node.js, Java eller C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: 7cc5061911ffc2cbc91dfa8c0d2fd1a6e59ff1a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875673"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Använd Visual Studio Code för att utveckla och felsöka moduler för Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Du kan omvandla din affärslogik till moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio Code som huvudverktyg för att utveckla och felsöka moduler.

Det finns två sätt att felsöka moduler som skrivits i C#, Node.js eller Java i Visual Studio Code: Du kan antingen koppla en process i en modulcontainer eller starta modulkoden i felsökningsläge. Om du vill felsöka moduler som skrivits i Python eller C kan du bara ansluta till en process i Linux amd64-containrar.

Om du inte är bekant med felsökningsfunktionerna i Visual Studio Code kan du läsa [om felsökning.](https://code.visualstudio.com/Docs/editor/debugging)

Den här artikeln innehåller instruktioner för att utveckla och felsöka moduler på flera språk för flera arkitekturer. För närvarande Visual Studio Code stöd för moduler skrivna i C#, C, Python, Node.js och Java. De enhetsarkitekturer som stöds är X64 och ARM32. Mer information om operativsystem, språk och arkitekturer som stöds finns i [Stöd för språk och arkitektur.](module-development.md#language-and-architecture-support)

>[!NOTE]
>Stöd för utveckling och felsökning för Linux ARM64-enheter finns i [offentlig förhandsversion.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Mer information finns i Utveckla [och felsöka ARM64 IoT Edge moduler i Visual Studio Code (förhandsversion)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Förutsättningar

Du kan använda en dator eller en virtuell dator som kör Windows, macOS eller Linux som utvecklingsdator. På Windows-datorer kan du utveckla antingen Windows- eller Linux-moduler. Om du vill utveckla Windows-moduler använder du en Windows-dator som kör version 1809/version 17763 eller senare. Om du vill utveckla Linux-moduler använder du en Windows-dator som [uppfyller kraven för Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Installera [Visual Studio Code](https://code.visualstudio.com/) först och lägg sedan till följande tillägg:

- [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio tillägg som är specifika för det språk som du utvecklar i:
  - C#, inklusive Azure Functions: [C#-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [Python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java-tilläggspaketet för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [C/C++-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Du måste också installera några ytterligare språkspecifika verktyg för att utveckla din modul:

- C#, inklusive Azure Functions: [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1)

- Python: [Python](https://www.python.org/downloads/) och [Pip för](https://pip.pypa.io/en/stable/installing/#installation) att installera Python-paket (ingår vanligtvis i python-installationen).

- Node.js: [Node.js](https://nodejs.org). Du vill också installera [Yeoman och](https://www.npmjs.com/package/yo) Azure IoT Edge Node.js [Module Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java SE Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) och [Maven](https://maven.apache.org/). Du måste ange [miljövariabeln `JAVA_HOME` så att den](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) pekar på JDK-installationen.

För att skapa och distribuera modulavbildningen behöver du Docker för att skapa modulavbildningen och ett containerregister som ska innehålla modulavbildningen:

- [Docker Community Edition](https://docs.docker.com/install/) på utvecklingsdatorn.

- [Azure Container Registry](../container-registry/index.yml) eller [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Du kan använda ett lokalt Docker-register i prototyp- och testsyfte i stället för ett molnregister.

Såvida du inte utvecklar din modul i C behöver du även det Python-baserade [Azure IoT EdgeHub Dev Tool](https://pypi.org/project/iotedgehubdev/) för att konfigurera din lokala utvecklingsmiljö att felsöka, köra och testa din IoT Edge lösning. Om du inte redan har gjort det installerar du [Python (2.7/3.6/3.7)](https://www.python.org/) och Pip och installerar **sedan iotedgehubdev** genom att köra det här kommandot i terminalen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> För närvarande använder iotedgehubdev ett docker-py-bibliotek som inte är kompatibelt med Python 3.8.
>
> Om du har flera Python, inklusive förinstallerad python 2.7 (till exempel i Ubuntu eller macOS), kontrollerar du att du använder rätt eller installerar `pip` `pip3` **iotedgehubdev**

Om du vill testa modulen på en enhet behöver du en aktiv IoT-hubb med minst en IoT Edge enhet. Om du vill använda datorn IoT Edge en enhet följer du stegen i snabbstarten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md). Om du kör IoT Edge daemon på utvecklingsdatorn kan du behöva stoppa EdgeHub och EdgeAgent innan du går vidare till nästa steg.

## <a name="create-a-new-solution-template"></a>Skapa en ny lösningsmall

Följande steg visar hur du skapar en IoT Edge-modul på önskat utvecklingsspråk (inklusive Azure Functions som skrivits i C#) med hjälp av Visual Studio Code och Azure IoT Tools. Du börjar med att skapa en lösning och genererar sedan den första modulen i lösningen. Varje lösning kan innehålla flera moduler.

1. Välj **Visa**  >  **kommandopalett.**

1. I kommandopaletten anger och kör du kommandot **Azure IoT Edge: New IoT Edge Solution**.

   ![Köra ny IoT Edge lösning](./media/how-to-develop-csharp-module/new-solution.png)

1. Bläddra till mappen där du vill skapa den nya lösningen och välj sedan **Välj mapp.**

1. Ange ett namn för lösningen.

1. Välj en modulmall för det utvecklingsspråk du föredrar för att bli den första modulen i lösningen.

1. Ange ett namn för modulen. Välj ett namn som är unikt i containerregistret.

1. Ange namnet på modulens avbildningsdatabas. Visual Studio Code fyller automatiskt i modulnamnet **med localhost:5000/<ditt modulnamn \>**. Ersätt den med din egen registerinformation. Om du använder ett lokalt Docker-register för testning går det bra **med localhost.** Om du Azure Container Registry använder du inloggningsservern från registrets inställningar. Inloggningsservern ser ut **_\<registry name\>_ som .azurecr.io**. Ersätt endast **localhost:5000-delen** av strängen så att slutresultatet ser ut **\<*registry name*\> som .azurecr.io/ _\<your module name\>_**.

   ![Ange lagringsplatsen för Docker-avbildningen](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code tar den information du angav, skapar en IoT Edge lösning och läser sedan in den i ett nytt fönster.

Det finns fyra objekt i lösningen:

- En **.vscode-mapp** innehåller felsökningskonfigurationer.

- En **modulmapp** har undermappar för varje modul.  I mappen för varje modul finns en fil, **module.jspå**, som styr hur moduler byggs och distribueras.  Den här filen måste ändras för att ändra containerregistret för moduldistribution från localhost till ett fjärrregister. Nu har du bara en modul.  Men du kan lägga till mer i kommandopaletten med kommandot **Azure IoT Edge: Lägg IoT Edge Modul**.

- En **.env-fil** visar en lista över dina miljövariabler. Om Azure Container Registry är ditt register har du ett Azure Container Registry användarnamn och lösenord i det.

  > [!NOTE]
  > Miljöfilen skapas bara om du anger en avbildningsdatabas för modulen. Om du har accepterat localhost-standardvärdena för att testa och felsöka lokalt behöver du inte deklarera miljövariabler.

- En **deployment.template.jspå** filen visar en lista över den nya modulen tillsammans med ett exempel på en **SimulatedTemperatureSensor-modul** som simulerar data som du kan använda för testning. Mer information om hur distributionsmanifest fungerar finns i [Lär dig hur du använder distributionsmanifest för att distribuera moduler och upprätta vägar.](module-composition.md)

Om du vill se hur modulen för simulerad temperatur fungerar kan du visa [källkoden SimulatedTemperatureSensor.csproj.](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor)

## <a name="add-additional-modules"></a>Lägga till ytterligare moduler

Om du vill lägga till ytterligare moduler i din lösning kör du **kommandot Azure IoT Edge: Lägg IoT Edge modul från** kommandopaletten. Du kan också högerklicka på mappen **modules** eller filen i Visual Studio Code Explorer-vyn och sedan välja `deployment.template.json` Lägg till IoT Edge **Modul**.

## <a name="develop-your-module"></a>Utveckla din modul

Standardmodulkoden som medföljer lösningen finns på följande plats:

- Azure Function (C#): **moduler > din modul ger modulen *&lt; &gt;*  >  *&lt; &gt; namnet*.cs**
- C#: **moduler > ditt *&lt; modulnamn &gt;* > Program.cs**
- Python: **moduler > *&lt; ditt &gt; modulnamn* > main.py**
- Node.js: moduler **> *&lt; ditt modulnamn &gt;* > app.js**
- Java: **moduler > *&lt; &gt; ditt* modulnamn > src > main > java > com > edgemodulemodules > App.java**
- C: **moduler > ditt *&lt; modulnamn &gt;* > main.c**

Modulen och deployment.template.jspå filen konfigureras så att du kan skapa lösningen, push-installera den i containerregistret och distribuera den till en enhet för att börja testa utan att behöva röra någon kod. Modulen är skapad för att helt enkelt ta indata från en källa (i det här fallet modulen SimulatedTemperatureSensor som simulerar data) och skicka dem till IoT Hub.

När du är redo att anpassa mallen med din egen kod kan du använda [Azure IoT Hub-SDK:er](../iot-hub/iot-hub-devguide-sdks.md) för att skapa moduler som adresserar de viktigaste behoven för IoT-lösningar som säkerhet, enhetshantering och tillförlitlighet.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Felsöka en modul utan en container (C#, Node.js, Java)

Om du utvecklar i C#, Node.js eller Java måste modulen använda ett **ModuleClient-objekt** i standardmodulkoden så att den kan starta, köra och dirigera meddelanden. Du använder också standardindatakanalen **input1 för** att vidta åtgärder när modulen tar emot meddelanden.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera en IoT Edge för IoT Edge lösning

På utvecklingsdatorn kan du starta en IoT Edge-simulator i stället för att installera IoT Edge säkerhetsdaemon så att du kan köra din IoT Edge lösning.

1. I Enhetsutforskaren till vänster högerklickar du på ditt IoT Edge enhets-ID och väljer sedan **Konfigurera IoT Edge Simulator** för att starta simulatorn med enhetens anslutningssträng.
1. Du kan se IoT Edge simulatorn har ställts in genom att läsa förloppet i den integrerade terminalen.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Konfigurera en IoT Edge för en modulapp

Om du vill konfigurera och starta simulatorn kör du **kommandot Azure IoT Edge: Start IoT Edge Hub Simulator for Single Module** (Starta IoT Edge Hub Simulator for Single Module) från kommandopaletten Visual Studio Code. När du uppmanas till det använder du **värdet input1** från standardmodulkoden (eller motsvarande värde från din kod) som indatanamn för ditt program. Kommandot utlöser **CLI:t iotedgehubdev** och startar sedan IoT Edge simulatorn och en testverktygsmodulcontainer. Du kan se utdata nedan i den integrerade terminalen om simulatorn har startats i enkelt modulläge. Du kan också se ett `curl` kommando som hjälper dig att skicka meddelanden. Du ska använda det senare.

   ![Konfigurera en IoT Edge för en modulapp](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Du kan använda Docker Explorer-vyn i Visual Studio Code för att se modulens körningsstatus.

   ![Status för simulatormodul](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev-containern** är kärnan i den lokala IoT Edge simulatorn. Den kan köras på utvecklingsdatorn utan IoT Edge säkerhetsdaemon och tillhandahåller miljöinställningar för din interna modulapp eller modulcontainrar. **Indatacontainern** exponerar REST-API:er för att hjälpa att överbrygga meddelanden till målindatakanalen i modulen.

### <a name="debug-module-in-launch-mode"></a>Felsökningsmodul i startläge

1. Förbered din miljö för felsökning enligt kraven i utvecklingsspråket, ange en brytpunkt i modulen och välj den felsökningskonfiguration som ska användas:
   - **C#**
     - I den Visual Studio Code-integrerade terminalen ***&lt; &gt;*** ändrar du katalogen till mappen med modulnamnet och kör sedan följande kommando för att skapa .NET Core-programmet.

       ```cmd
       dotnet build
       ```

     - Öppna filen och `Program.cs` lägg till en brytpunkt.

     - Gå till Visual Studio kodfelsökningsvyn genom att **välja Visa > Felsök.** Välj felsökningskonfigurationen **_&lt; som &gt; modulnamnet_ Lokal felsökning (.NET Core)** i listrutan.

        > [!NOTE]
        > Om .NET Core inte är konsekvent med programsökvägen i måste du uppdatera programsökvägen i manuellt så att den matchar i `TargetFramework` `launch.json` `launch.json` .csproj-filen så att Visual Studio Code kan starta det här `TargetFramework` programmet.

   - **Node.js**
     - I den Visual Studio Code-integrerade terminalen ***&lt; &gt;*** ändrar du katalogen till mappen med modulnamnet och kör sedan följande kommando för att installera Node-paket

       ```cmd
       npm install
       ```

     - Öppna filen och `app.js` lägg till en brytpunkt.

     - Gå till Visual Studio kodfelsökningsvyn genom att **välja Visa > Felsök**. Välj den felsökningskonfiguration **_&lt; som modulnamnet &gt;_ Lokal felsökning (Node.js)** i listrutan.
   - **Java**
     - Öppna filen och `App.java` lägg till en brytpunkt.

     - Gå till Visual Studio kodfelsökningsvyn genom att **välja Visa > Felsök**. Välj den felsökningskonfiguration **_&lt; som modulen heter &gt;_ Lokal felsökning (Java)** i listrutan.

1. Klicka **på Starta felsökning eller** tryck på **F5** för att starta felsökningssessionen.

1. I den Visual Studio Code-integrerade terminalen kör du följande kommando för att **skicka Hello World** meddelande till modulen. Det här är kommandot som visas i föregående steg när du konfigurerade IoT Edge simulatorn.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Om du använder Windows kontrollerar du att gränssnittet för din integrerade Visual Studio Code är **Git Bash** eller **WSL Bash.** Du kan inte köra `curl` kommandot från en PowerShell- eller kommandotolk.
   > [!TIP]
   > Du kan också använda [PostMan eller](https://www.getpostman.com/) andra API-verktyg för att skicka meddelanden via i stället för `curl` .

1. I Visual Studio kodfelsökningsvyn visas variablerna i den vänstra panelen.

1. Om du vill stoppa felsökningssessionen väljer du knappen Stoppa eller trycker på **Skift + F5** och kör **sedan Azure IoT Edge: Stoppa IoT Edge Simulator** i kommandopaletten för att stoppa simulatorn och rensa.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Felsök i anslutningsläge med IoT Edge Simulator (C#, Node.js, Java, Azure Functions)

Din standardlösning innehåller två moduler, en är en simulerad temperatursensormodul och den andra är pipe-modulen. Den simulerade temperatursensorn skickar meddelanden till pipe-modulen och sedan skickas meddelandena till IoT Hub. I modulmappen som du skapade finns det flera Docker-filer för olika containertyper. Använd någon av de filer som slutar med tillägget **.debug för** att skapa modulen för testning.

Felsökning i anslutningsläge stöds för närvarande endast på följande sätt:

- C#-moduler, inklusive för Azure Functions, stöder felsökning i Linux amd64-containrar
- Node.js-moduler stöder felsökning i Linux amd64- och arm32v7-containrar och Windows amd64-containrar
- Java-moduler stöder felsökning i Linux amd64- och arm32v7-containrar

> [!TIP]
> Du kan växla mellan alternativen för standardplattformen för din IoT Edge lösning genom att klicka på objektet i Visual Studio statusfältet kod.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera en IoT Edge simulator för IoT Edge lösning

På utvecklingsdatorn kan du starta en IoT Edge simulator i stället för att installera IoT Edge säkerhetsdaemon så att du kan köra din IoT Edge lösning.

1. I Enhetsutforskaren till vänster högerklickar du på ditt IoT Edge enhets-ID och väljer sedan **Konfigurera IoT Edge Simulator** för att starta simulatorn med enhetens anslutningssträng.

1. Du kan se IoT Edge simulatorn har ställts in genom att läsa förloppet i den integrerade terminalen.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Skapa och kör container för felsökning och felsökning i anslutningsläge

1. Öppna modulfilen ( `Program.cs` , , eller ) och lägg till en `app.js` `App.java` `<your module name>.cs` brytpunkt.

1. I Visual Studio Code Explorer-vyn högerklickar du på filen för din lösning och väljer sedan Build and Run IoT Edge solution in Simulator (Skapa och kör en `deployment.debug.template.json` **IoT Edge i Simulator).** Du kan se alla modulcontainerloggar i samma fönster. Du kan också navigera till Docker-vyn för att se containerstatus.

   ![Bevaka variabler](media/how-to-vs-code-develop-module/view-log.png)

1. Gå till Visual Studio Code Debug och välj konfigurationsfilen för felsökning för din modul. Namnet på felsökningsalternativet bör likna ***&lt; modulnamnet Fjärrfelsökning &gt;***

1. Välj **Starta felsökning eller** tryck på **F5.** Välj den process som du vill koppla till.

1. I Visual Studio kodfelsökningsvyn visas variablerna i den vänstra panelen.

1. Om du vill stoppa felsökningssessionen väljer du först knappen Stoppa eller trycker på **Skift + F5** och väljer **sedan Azure IoT Edge: Stoppa** IoT Edge simulatorn från kommandopaletten.

> [!NOTE]
> Föregående exempel visar hur du felsöker IoT Edge på containrar. Den lade till exponerade portar i modulens `createOptions` containerinställningar. När du har felsökt dina moduler rekommenderar vi att du tar bort dessa exponerade portar för produktionsklara IoT Edge moduler.
>
> För moduler som skrivits i C#, inklusive Azure Functions, baseras det här exemplet på felsökningsversionen av , som innehåller `Dockerfile.amd64.debug` .NET Core-kommandoradsfelsökaren (VSDBG) i containeravbildningen när du skapar den. När du har felsökt dina C#-moduler rekommenderar vi att du använder Dockerfile direkt utan VSDBG för produktionsklara IoT Edge moduler.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Felsöka en modul med IoT Edge körning

Det finns flera Docker-filer för olika containertyper i varje modulmapp. Använd någon av de filer som slutar med filnamnstillägget **.debug** för att skapa modulen för testning.

När du felsöker moduler med den här metoden körs modulerna ovanpå den IoT Edge körningen. Enheten IoT Edge och din Visual Studio Code kan finnas på samma dator, eller vanligtvis finns Visual Studio Code på utvecklingsdatorn och IoT Edge-körningen och modulerna körs på en annan fysisk dator. För att kunna felsöka från Visual Studio Code måste du:

- Konfigurera din IoT Edge-enhet, skapa dina IoT Edge-moduler med **.debug** Dockerfile och distribuera sedan till IoT Edge enhet.
- Exponera IP-adressen och porten för modulen så att felsökaren kan kopplas.
- Uppdatera så `launch.json` att Visual Studio Code kan ansluta till processen i containern på fjärrdatorn. Den här filen finns i mappen `.vscode` på arbetsytan och uppdateras varje gång du lägger till en ny modul som stöder felsökning.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Skapa och distribuera modulen till IoT Edge enhet

1. I Visual Studio Code öppnar du `deployment.debug.template.json` filen som innehåller felsökningsversionen av modulavbildningarna med rätt `createOptions` värden inställda.

1. Om du utvecklar din modul i Python följer du dessa steg innan du fortsätter:
   - Öppna filen och `main.py` lägg till den här koden efter importavsnittet:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Lägg till följande kodrad i motringningsprogrammet som du vill felsöka:

      ```python
      ptvsd.break_into_debugger()
      ```

     Om du till exempel vill felsöka funktionen `receive_message_listener` infogar du den kodraden enligt nedan:

      ```python
      def receive_message_listener(client):
          ptvsd.break_into_debugger()
          global RECEIVED_MESSAGES
          while True:
              message = client.receive_message_on_input("input1")   # blocking call
              RECEIVED_MESSAGES += 1
              print("Message received on input1")
              print( "    Data: <<{}>>".format(message.data) )
              print( "    Properties: {}".format(message.custom_properties))
              print( "    Total calls received: {}".format(RECEIVED_MESSAGES))
              print("Forwarding message to output1")
              client.send_message_to_output(message, "output1")
              print("Message successfully forwarded")
      ```

1. I kommandopaletten Visual Studio Code:
   1. Kör kommandot **Azure IoT Edge: Build and Push IoT Edge solution**.

   1. Välj `deployment.debug.template.json` filen för din lösning.

1. I avsnittet **Azure IoT Hub enheter** i Visual Studio Code Explorer:
   1. Högerklicka på ett IoT Edge enhets-ID och välj **sedan Skapa distribution för enskild enhet.**

      > [!TIP]
      > Bekräfta att enheten du har valt är **en IoT Edge-enhet** genom att välja den för att expandera listan över moduler och kontrollera att det finns $edgeHub **och $edgeAgent**. Varje IoT Edge enhet innehåller dessa två moduler.

   1. Navigera till din lösnings **konfigurationsmapp,** välj `deployment.debug.amd64.json` filen och välj sedan Välj **Distributionsmanifest för Edge.**

Du ser att distributionen har skapats med ett distributions-ID i den integrerade terminalen.

Du kan kontrollera containerstatusen genom att köra `docker ps` kommandot i terminalen. Om din Visual Studio Code och IoT Edge körs på samma dator kan du också kontrollera statusen i Visual Studio Code Docker-vyn.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Exponera IP-adressen och porten för modulen för felsökningsprogrammet

Du kan hoppa över det här avsnittet om modulerna körs på samma dator som Visual Studio Code, eftersom du använder localhost för att ansluta till containern och redan har rätt portinställningar i Dockerfile för **felsökning,** containerinställningar för modulen och `createOptions` `launch.json` filen. Om modulerna och Visual Studio Code körs på separata datorer följer du stegen för ditt utvecklingsspråk.

- **C#, inklusive Azure Functions**

  [Konfigurera SSH-kanalen på utvecklingsdatorn och IoT Edge och redigera](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) sedan filen som ska `launch.json` bifogas.

- **Node.js**

  - Kontrollera att modulen på datorn som ska felsökas körs och är redo för att felsökare ska anslutas och att port 9229 kan nås externt. Du kan kontrollera detta genom att `http://<target-machine-IP>:9229/json` öppna på felsökningsdatorn. Den här URL:en ska visa information Node.js modulen som ska felsökas.
  
  - På utvecklingsdatorn öppnar du Visual Studio Code och redigerar sedan så att adressvärdet för profilen Fjärrfelsökning `launch.json` ***&lt; &gt;* (Node.js) (eller** **_&lt; &gt;_ modulnamnet Fjärrfelsökning (Node.js i Windows-container)** om modulen körs som en Windows-container) är IP-adressen för den dator som felsöks.

- **Java**

  - Skapa en SSH-tunnel till datorn som ska felsökas genom att köra `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` .
  
  - På utvecklingsdatorn öppnar du Visual Studio Code ***&lt; &gt;*** och redigerar modulnamnet Remote Debug (Java) i så att du kan ansluta `launch.json` till måldatorn. Mer information om hur du redigerar och felsöker Java med Visual Studio Code finns i `launch.json` avsnittet om hur du [konfigurerar felsökningsprogrammet](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Kontrollera att port 5678 på datorn som ska felsökas är öppen och tillgänglig.

  - I koden som du tidigare infogade i ändrar du `ptvsd.enable_attach(('0.0.0.0', 5678))` `main.py` **0.0.0.0** till IP-adressen för den dator som ska felsökas. Skapa, push-distribuera IoT Edge modulen igen.

  - På utvecklingsdatorn öppnar du Visual Studio Code `launch.json` `host` ***&lt; &gt;*** och redigerar sedan så att värdet för modulnamnet Remote Debug (Python) profile använder MÅLDATORNs IP-adress i stället för `localhost` .

### <a name="debug-your-module"></a>Felsöka din modul

1. I Visual Studio i felsökningsvyn för kod väljer du konfigurationsfilen för felsökning för din modul. Namnet på felsökningsalternativet bör likna ***&lt; modulnamnet Fjärrfelsökning &gt;***

1. Öppna modulfilen för utvecklingsspråket och lägg till en brytpunkt:

   - **Azure-funktion (C#):** Lägg till brytpunkten i filen `<your module name>.cs` .
   - **C#**: Lägg till brytpunkten i filen `Program.cs` .
   - **Node.js**: Lägg till brytpunkten i filen `app.js` .
   - **Java:** Lägg till brytpunkten i filen `App.java` .
   - **Python:** Lägg till brytpunkten i filen `main.py` i återanropsmetoden där du lade till `ptvsd.break_into_debugger()` raden.
   - **C:** Lägg till brytpunkten i filen `main.c` .

1. Välj **Starta felsökning eller** välj **F5.** Välj den process som du vill koppla till.

1. I Visual Studio Code Debug visas variablerna i den vänstra panelen.

> [!NOTE]
> Föregående exempel visar hur du felsöker IoT Edge på containrar. Den har lagt till portar som exponerats för modulens `createOptions` containerinställningar. När du har felsökt klart modulerna rekommenderar vi att du tar bort dessa exponerade portar för produktionsklara IoT Edge moduler.

## <a name="build-and-debug-a-module-remotely"></a>Skapa och felsöka en modul via fjärrinstallation

Med de senaste ändringarna i både Docker- och Moby-motorerna för att stödja SSH-anslutningar och en ny inställning i Azure IoT Tools som möjliggör loggning av miljöinställningar i kommandopaletten för Visual Studio Code och Azure IoT Edge-terminaler kan du nu skapa och felsöka moduler på fjärranslutna enheter.

Mer information och stegvisa instruktioner finns i det här blogginlägget för [IoT-utvecklare.](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/)

## <a name="next-steps"></a>Nästa steg

När du har skapat modulen kan du lära dig hur du [distribuerar Azure IoT Edge moduler från Visual Studio Code](how-to-deploy-modules-vscode.md).

Om du vill utveckla moduler för [IoT Edge-enheter kan du förstå och använda Azure IoT Hub-SDK:er.](../iot-hub/iot-hub-devguide-sdks.md)