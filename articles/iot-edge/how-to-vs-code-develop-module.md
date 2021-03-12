---
title: Utveckla och felsöka moduler för Azure IoT Edge | Microsoft Docs
description: Använd Visual Studio Code för att utveckla, bygga och felsöka en modul för Azure IoT Edge med C#, python, Node.js, Java eller C
services: iot-edge
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 08/07/2019
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-js
ms.openlocfilehash: eae6f1ec8cb6917d0d51deca8c9e88725c9b01a7
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200634"
---
# <a name="use-visual-studio-code-to-develop-and-debug-modules-for-azure-iot-edge"></a>Använd Visual Studio Code för att utveckla och felsöka moduler för Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Du kan omvandla din affärs logik till moduler för Azure IoT Edge. Den här artikeln visar hur du använder Visual Studio Code som huvud verktyg för att utveckla och felsöka moduler.

Det finns två sätt att felsöka moduler skrivna i C#, Node.js eller java i Visual Studio Code: du kan antingen koppla en process i en modul-behållare eller starta modulens kod i fel söknings läge. Om du vill felsöka moduler skrivna i python eller C kan du bara ansluta till en process i Linux amd64-behållare.

Om du inte är bekant med fel söknings funktionerna i Visual Studio Code läser du om [fel sökning](https://code.visualstudio.com/Docs/editor/debugging).

Den här artikeln innehåller instruktioner för att utveckla och felsöka moduler på flera språk för flera arkitekturer. För närvarande ger Visual Studio Code stöd för moduler som skrivits i C#, C, python, Node.js och Java. De enhets arkitekturer som stöds är x64 och ARM32. Mer information om operativ system, språk och arkitekturer som stöds finns i [stöd för språk och arkitektur](module-development.md#language-and-architecture-support).

>[!NOTE]
>Att utveckla och felsöka stöd för Linux ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [utveckla och FELSÖKA ARM64 IoT Edge moduler i Visual Studio Code (för hands version)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

## <a name="prerequisites"></a>Förutsättningar

Du kan använda en dator eller en virtuell dator som kör Windows, macOS eller Linux som utvecklings dator. På Windows-datorer kan du utveckla antingen Windows-eller Linux-moduler. Om du vill utveckla Windows-moduler använder du en Windows-dator som kör version 1809/build 17763 eller senare. Om du vill utveckla Linux-moduler använder du en Windows-dator som uppfyller [kraven för Docker Desktop](https://docs.docker.com/docker-for-windows/install/#what-to-know-before-you-install).

Installera [Visual Studio Code](https://code.visualstudio.com/) först och Lägg sedan till följande tillägg:

- [Azure IoT-verktyg](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)
- [Docker-tillägg](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)
- Visual Studio-tillägg som är specifika för det språk som du utvecklar i:
  - C#, inklusive Azure Functions: [c#-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)
  - Python: [python-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-python.python)
  - Java: [Java Extension Pack för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack)
  - C: [c/C++-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.cpptools)

Du måste också installera ytterligare språkbaserade verktyg för att utveckla modulen:

- C#, inklusive Azure Functions: [.net Core 2,1 SDK](https://www.microsoft.com/net/download)

- Python: [python](https://www.python.org/downloads/) och [pip](https://pip.pypa.io/en/stable/installing/#installation) för att installera python-paket (som vanligt vis ingår i python-installationen).

- Node.js: [Node.js](https://nodejs.org). Du vill också installera [Yeoman](https://www.npmjs.com/package/yo) och [Azure IoT Edge Node.js modul Generator](https://www.npmjs.com/package/generator-azure-iot-edge-module).

- Java: [Java se Development Kit 10](/azure/developer/java/fundamentals/java-jdk-long-term-support) och [maven](https://maven.apache.org/). Du måste [ställa in `JAVA_HOME` miljövariabeln](https://docs.oracle.com/cd/E19182-01/820-7851/inst_cli_jdk_javahome_t/) så att den pekar på din JDK-installation.

Om du vill bygga och distribuera din modul-avbildning behöver Docker du för att bygga modul avbildningen och ett behållar register för att lagra modulens avbildning:

- [Docker Community Edition](https://docs.docker.com/install/) på din utvecklings dator.

- [Azure Container Registry](../container-registry/index.yml) -eller [Docker-hubb](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

    > [!TIP]
    > Du kan använda ett lokalt Docker-register för prototyp-och testnings ändamål i stället för ett moln register.

Om du inte utvecklar modulen i C behöver du även det python-baserade [Azure IoT EdgeHub dev-verktyget](https://pypi.org/project/iotedgehubdev/) för att konfigurera din lokala utvecklings miljö för att felsöka, köra och testa din IoT Edge-lösning. Om du inte redan har gjort det installerar du [python (2.7/3.6/3.7) och pip](https://www.python.org/) och installerar sedan **iotedgehubdev** genom att köra det här kommandot i terminalen.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```
   
> [!NOTE]
> För närvarande använder iotedgehubdev ett Docker-py-bibliotek som inte är kompatibelt med python 3,8.
>
> Om du har flera python-versioner, inklusive förinstallerad python 2,7 (till exempel på Ubuntu eller macOS), kontrollerar du att du använder rätt `pip` eller `pip3` installerar **iotedgehubdev**

Om du vill testa modulen på en enhet behöver du en aktiv IoT-hubb med minst en IoT Edge enhet. Om du vill använda datorn som en IoT Edge enhet följer du stegen i snabb starten för [Linux](quickstart-linux.md) eller [Windows](quickstart.md). Om du kör IoT Edge daemon på din utvecklings dator kan du behöva stoppa EdgeHub och EdgeAgent innan du går vidare till nästa steg.

## <a name="create-a-new-solution-template"></a>Skapa en ny lösnings mall

Följande steg visar hur du skapar en IoT Edge-modul på önskat utvecklings språk (inklusive Azure Functions, skrivet i C#) med Visual Studio Code och Azure IoT-verktyg. Du börjar med att skapa en lösning och genererar sedan den första modulen i lösningen. Varje lösning kan innehålla flera moduler.

1. Välj **Visa**  >  **kommando palett**.

1. I paletten kommando anger och kör du kommandot **Azure IoT Edge: ny IoT Edge lösning**.

   ![Kör ny IoT Edge-lösning](./media/how-to-develop-csharp-module/new-solution.png)

1. Bläddra till den mapp där du vill skapa den nya lösningen och välj sedan **Välj mapp**.

1. Ange ett namn för din lösning.

1. Välj en mall för det programmeringsspråk som du föredrar som första modul i lösningen.

1. Ange ett namn för modulen. Välj ett namn som är unikt i behållar registret.

1. Ange namnet på modulens avbildnings lagrings plats. Visual Studio Code fyller automatiskt i modulnamnet med **localhost: 5000/<namnet \> på din modul**. Ersätt den med din egen register information. Om du använder ett lokalt Docker-register för testning är **localhost** bra. Om du använder Azure Container Registry använder du inloggnings servern från registrets inställningar. Inloggnings servern ser ut så här **_\<registry name\>_ . azurecr.io**. Ersätt endast **localhost: 5000** del av strängen så att det slutliga resultatet ser ut som **\<*registry name*\> . azurecr.io/ _\<your module name\>_**.

   ![Ange lagringsplatsen för Docker-avbildningen](./media/how-to-develop-csharp-module/repository.png)

Visual Studio Code tar den information du har angett, skapar en IoT Edge-lösning och läser sedan in den i ett nytt fönster.

Det finns fyra objekt i lösningen:

- En **. VSCode** -mapp innehåller felsöknings konfigurationer.

- En mapp för **moduler** innehåller undermappar för varje modul.  I mappen för varje modul finns det en fil **module.jspå**, som styr hur moduler byggs och distribueras.  Den här filen måste ändras för att ändra modulens distributions behållar register från localhost till ett fjär register. I det här läget har du bara en modul.  Men du kan lägga till fler i kommando paletten med kommandot **Azure IoT Edge: Lägg till IoT Edge-modul**.

- En **. en. miljö** -fil visar dina miljövariabler. Om Azure Container Registry är ditt register, har du ett Azure Container Registry användar namn och lösen ord.

  > [!NOTE]
  > Miljö filen skapas bara om du anger en avbildnings lagrings plats för modulen. Om du har accepterat standardvärdet för localhost för att testa och felsöka lokalt, behöver du inte deklarera miljövariabler.

- En **deployment.template.jspå** fil listar den nya modulen tillsammans med en exempel- **SimulatedTemperatureSensor** -modul som simulerar data som du kan använda för testning. Mer information om hur distributions manifest fungerar finns i [Lär dig hur du använder distributions manifest för att distribuera moduler och upprätta vägar](module-composition.md).

Om du vill se hur den simulerade temperaturen fungerar kan du Visa [käll koden för SimulatedTemperatureSensor. CSPROJ](https://github.com/Azure/iotedge/tree/master/edge-modules/SimulatedTemperatureSensor).

## <a name="add-additional-modules"></a>Lägg till ytterligare moduler

Om du vill lägga till ytterligare moduler till din lösning kör du kommandot **Azure IoT Edge: Lägg till IoT Edge modul** från paletten kommando. Du kan också högerklicka på mappen **moduler** eller `deployment.template.json` filen i Visual Studio Code Explorer-vyn och sedan välja **Lägg till IoT Edge modul**.

## <a name="develop-your-module"></a>Utveckla din modul

Standard koden för modulen som medföljer lösningen finns på följande plats:

- Azure function (C#): **moduler > *&lt; modulens namn &gt;*  >  *&lt; ditt Modulnamn &gt;*. cs**
- C#: **moduler > *&lt; ditt &gt; Modulnamn* > program.cs**
- Python: **moduler > *&lt; ditt &gt; Modulnamn* > main.py**
- Node.js: **moduler > *&lt; ditt &gt; Modulnamn* > app.js**
- Java: **moduler > *&lt; modulens namn &gt;* > src > main > java > com > edgemodulemodules > app. java**
- C: **moduler > *&lt; ditt &gt; Modulnamn* > main. c**

Modulen och deployment.template.jspå filen har kon figurer ATS så att du kan skapa lösningen, push-överför den till behållar registret och distribuera den till en enhet för att starta testning utan att behöva röra någon kod. Modulen är konstruerad för att helt enkelt ta med indata från en källa (i det här fallet SimulatedTemperatureSensor-modulen som simulerar data) och rör den till IoT Hub.

När du är redo att anpassa mallen med din egen kod kan du använda [Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md) : er för att bygga moduler som uppfyller nyckel behoven för IoT-lösningar som säkerhet, enhets hantering och pålitlighet.

## <a name="debug-a-module-without-a-container-c-nodejs-java"></a>Felsöka en modul utan en behållare (C#, Node.js, Java)

Om du utvecklar i C#, Node.js eller Java kräver modulen att du använder ett **ModuleClient** -objekt i kodens kodmodul så att den kan starta, köra och dirigera meddelanden. Du ska också använda standardvärdet för **INPUT1** för inkanaler för att vidta åtgärder när modulen tar emot meddelanden.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera IoT Edge Simulator för IoT Edge-lösning

På din utvecklings dator kan du starta en IoT Edge Simulator i stället för att installera IoT Edge Security daemon så att du kan köra din IoT Edge lösning.

1. I Device Explorer till vänster högerklickar du på IoT Edge enhets-ID och väljer sedan **installations IoT Edge Simulator** för att starta simulatorn med enhets anslutnings strängen.
1. Du kan se att IoT Edge Simulator har kon figurer ATS genom att läsa informationen om förloppet i den integrerade terminalen.

### <a name="set-up-iot-edge-simulator-for-single-module-app"></a>Konfigurera IoT Edge Simulator för en app med en modul

Konfigurera och starta simulatorn genom att köra kommandot **Azure IoT Edge: starta IoT Edge Hub Simulator för en modul** från Visual Studio Code-kommando paletten. När du uppmanas använder du värdet **INPUT1** från standardmodulens kod (eller motsvarande värde från koden) som Indataporten för ditt program. Kommandot utlöser **iotedgehubdev** CLI och startar sedan behållaren för IoT Edge Simulator och en testverktyg-modul. Du kan se utdata nedan i den integrerade terminalen om simulatorn har startats i läget för en modul. Du kan också se ett `curl` kommando för att skicka ett meddelande via. Du ska använda det senare.

   ![Konfigurera IoT Edge Simulator för en app med en modul](media/how-to-develop-csharp-module/start-simulator-for-single-module.png)

   Du kan använda vyn Docker-Utforskare i Visual Studio Code för att se modulens körnings status.

   ![Status för Simulator modul](media/how-to-develop-csharp-module/simulator-status.png)

   **EdgeHubDev** -behållaren är kärnan i den lokala IoT Edge simulatorn. Den kan köras på utvecklings datorn utan IoT Edge Security daemon och ger miljö inställningar för din app eller modul behållare i ursprunglig modul. **Indatamängden** EXPONERAr REST-API: er för att hjälpa att överbrygga meddelanden till mål Indataporten i modulen.

### <a name="debug-module-in-launch-mode"></a>Felsöka modul i Start läge

1. Förbered din miljö för fel sökning enligt kraven för ditt utvecklings språk, ange en Bryt punkt i modulen och välj den fel söknings konfiguration som ska användas:
   - **C#**
     - I Visual Studio Code Integrated Terminal ändrar du katalogen till mappen ***&lt; Modulnamn &gt;*** och kör sedan följande kommando för att skapa .net Core-program.

       ```cmd
       dotnet build
       ```

     - Öppna filen `Program.cs` och Lägg till en Bryt punkt.

     - Navigera till vyn Visual Studio Code debug genom att välja **visa > fel sökning**. Välj fel söknings konfiguration **_&lt; ditt &gt; Modulnamn_ lokal fel sökning (.net Core)** i list rutan.

        > [!NOTE]
        > Om din .NET Core `TargetFramework` inte stämmer överens med din program Sök väg i `launch.json` måste du manuellt uppdatera program Sök vägen i `launch.json` så att den matchar `TargetFramework` i din. CSPROJ-fil så att Visual Studio Code kan starta programmet.

   - **Node.js**
     - I Visual Studio Code Integrated Terminal ändrar du katalogen till mappen ***&lt; Modulnamn &gt;*** och kör sedan följande kommando för att installera Node-paket

       ```cmd
       npm install
       ```

     - Öppna filen `app.js` och Lägg till en Bryt punkt.

     - Navigera till vyn Visual Studio Code debug genom att välja **visa > fel sökning**. Välj fel söknings konfiguration **_&lt; ditt &gt; Modulnamn_ lokal fel sökning (Node.js)** i list rutan.
   - **Java**
     - Öppna filen `App.java` och Lägg till en Bryt punkt.

     - Navigera till vyn Visual Studio Code debug genom att välja **visa > fel sökning**. Välj fel söknings konfiguration **_&lt; din modul &gt; namn_ lokal fel sökning (Java)** i list rutan.

1. Starta felsökningssessionen genom att klicka på **Starta fel sökning** eller trycka på **F5** .

1. I Visual Studio Code Integrated Terminal kör du följande kommando för att skicka ett **Hello World** meddelande till modulen. Detta är kommandot som visas i föregående steg när du konfigurerar IoT Edge Simulator.

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   > [!NOTE]
   > Om du använder Windows kontrollerar du att gränssnittet för den integrerade Terminal Studio Code-terminalen är **git bash** eller **Wsl bash**. Du kan inte köra `curl` kommandot från PowerShell eller kommando tolken.
   > [!TIP]
   > Du kan också använda [Postman](https://www.getpostman.com/) eller andra API-verktyg för att skicka meddelanden via i stället för `curl` .

1. I Visual Studio Codes debug-vy ser du variablerna i den vänstra panelen.

1. Om du vill stoppa din felsökningssession väljer du knappen Stoppa eller trycker på **Shift + F5** och kör **Azure IoT Edge: stoppa IoT Edge simulatorn** på kommando paletten för att stoppa simulatorn och rensa.

## <a name="debug-in-attach-mode-with-iot-edge-simulator-c-nodejs-java-azure-functions"></a>Felsöka i Attach-läge med IoT Edge Simulator (C#, Node.js, Java, Azure Functions)

Standardlösningen innehåller två moduler, en är en simulerad temperatur sensor modul och den andra är pipe-modulen. Den simulerade temperatur sensorn skickar meddelanden till pipe-modulen och skickar sedan skickas till IoT Hub. I mappen modul som du har skapat finns det flera Docker-filer för olika behållar typer. Använd någon av filerna som slutar med tillägget **. debug** för att bygga modulen för testning.

För närvarande stöds fel sökning i kopplings läge endast på följande sätt:

- C#-moduler, inklusive de för Azure Functions, stöder fel sökning i Linux amd64-behållare
- Node.js moduler stöder fel sökning i Linux amd64-och arm32v7-behållare och Windows amd64-behållare
- Java-moduler stöder fel sökning i Linux amd64-och arm32v7-behållare

> [!TIP]
> Du kan växla mellan alternativ för standard plattformen för din IoT Edge lösning genom att klicka på objektet i statusfältet i Visual Studio-kod.

### <a name="set-up-iot-edge-simulator-for-iot-edge-solution"></a>Konfigurera IoT Edge Simulator för IoT Edge-lösning

I utvecklings datorn kan du starta en IoT Edge Simulator i stället för att installera IoT Edge Security daemon så att du kan köra din IoT Edge lösning.

1. I Device Explorer till vänster högerklickar du på IoT Edge enhets-ID och väljer sedan **installations IoT Edge Simulator** för att starta simulatorn med enhets anslutnings strängen.

1. Du kan se att IoT Edge Simulator har kon figurer ATS genom att läsa informationen om förloppet i den integrerade terminalen.

### <a name="build-and-run-container-for-debugging-and-debug-in-attach-mode"></a>Skapa och kör behållare för fel sökning och fel sökning i anslutnings läge

1. Öppna modul filen ( `Program.cs` ,, `app.js` `App.java` eller `<your module name>.cs` ) och Lägg till en Bryt punkt.

1. I vyn Visual Studio Code Explorer högerklickar du på `deployment.debug.template.json` filen för din lösning och väljer sedan **skapa och kör IoT Edge lösning i simulatorn**. Du kan se alla behållar loggar i samma fönster. Du kan också navigera till Docker-vyn för att se container status.

   ![Bevaka variabler](media/how-to-vs-code-develop-module/view-log.png)

1. Navigera till vyn Visual Studio Code debug och välj fel söknings konfigurations filen för modulen. Namnet på fel söknings alternativet bör likna ***&lt; ditt Modulnamn- &gt; namn* för fjärrfelsökning**

1. Välj **Starta fel sökning** eller tryck på **F5**. Välj den process som du vill koppla till.

1. I Visual Studio Codes debug-vy ser du variablerna i den vänstra panelen.

1. Om du vill stoppa felsökningssessionen väljer du först knappen Stoppa eller trycker på **Shift + F5** och väljer sedan **Azure IoT Edge: stoppa IoT Edge Simulator** från kommando paletten.

> [!NOTE]
> Föregående exempel visar hur du felsöker IoT Edge moduler på behållare. Den har lagt till exponerade portar till din moduls behållar `createOptions` Inställningar. När du har slutfört fel sökningen av dina moduler rekommenderar vi att du tar bort de exponerade portarna för produktions färdiga IoT Edge moduler.
>
> För moduler som skrivits i C#, inklusive Azure Functions, baseras det här exemplet på fel söknings versionen av `Dockerfile.amd64.debug` , som innehåller .net Core-kommandoraden (VSDBG) i behållar avbildningen när den skapas. När du har felsöker dina C#-moduler rekommenderar vi att du direkt använder Dockerfile utan VSDBG för produktion färdiga IoT Edge moduler.

## <a name="debug-a-module-with-the-iot-edge-runtime"></a>Felsöka en modul med IoT Edge runtime

I varje modul finns det flera Docker-filer för olika behållar typer. Använd någon av filerna som slutar med tillägget **. debug** för att bygga modulen för testning.

När du felsöker moduler med den här metoden körs dina moduler ovanpå IoT Edge Runtime. IoT Edge-enheten och Visual Studio-koden kan finnas på samma dator, eller mer vanligt vis Visual Studio Code på utvecklings datorn och IoT Edge körning och moduler körs på en annan fysisk dator. För att kunna felsöka från Visual Studio Code måste du:

- Konfigurera din IoT Edge-enhet, skapa IoT Edge-modul (er) med **. debug** -Dockerfile och distribuera sedan till den IoT Edge enheten.
- Exponera IP och port för modulen så att fel söknings programmet kan kopplas.
- Uppdatera `launch.json` så att Visual Studio Code kan kopplas till processen i behållaren på fjärrdatorn. Den här filen finns i `.vscode` mappen på din arbets yta och uppdateringar varje gången du lägger till en ny modul som stöder fel sökning.

### <a name="build-and-deploy-your-module-to-the-iot-edge-device"></a>Bygg och distribuera modulen till den IoT Edge enheten

1. Öppna filen i Visual Studio Code `deployment.debug.template.json` , som innehåller fel söknings versionen av modulens bilder med rätt `createOptions` värden.

1. Om du utvecklar din modul i python följer du de här stegen innan du fortsätter:
   - Öppna filen `main.py` och Lägg till den här koden efter import avsnittet:

      ```python
      import ptvsd
      ptvsd.enable_attach(('0.0.0.0',  5678))
      ```

   - Lägg till följande rad kod i motringningen som du vill felsöka:

      ```python
      ptvsd.break_into_debugger()
      ```

     Om du till exempel vill felsöka `receive_message_listener` funktionen infogar du den kodrad som visas nedan:

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

1. I kommando rads verktyget Visual Studio Code:
   1. Kör kommandot **Azure IoT Edge: skapa och Push IoT Edge-lösning**.

   1. Välj `deployment.debug.template.json` filen för din lösning.

1. I avsnittet **Azure IoT Hub-enheter** i vyn Visual Studio Code Explorer:
   1. Högerklicka på ett IoT Edge enhets-ID och välj sedan **skapa distribution för en enskild enhet**.

      > [!TIP]
      > För att bekräfta att enheten du har valt är en IoT Edge enhet, väljer du den för att expandera listan över moduler och kontrollerar förekomsten av **$edgeHub** och **$edgeAgent**. Varje IoT Edge enhet innehåller dessa två moduler.

   1. Navigera till din lösnings **config** -mapp, markera `deployment.debug.amd64.json` filen och välj sedan **Välj gräns distributions manifest**.

Du ser att distributionen har skapats med ett distributions-ID i den integrerade terminalen.

Du kan kontrol lera behållar status genom att köra `docker ps` kommandot i terminalen. Om din Visual Studio-kod och IoT Edge runtime körs på samma dator kan du också kontrol lera statusen i vyn Visual Studio Code Docker.

### <a name="expose-the-ip-and-port-of-the-module-for-the-debugger"></a>Exponera IP och port för fel sökningens modul

Du kan hoppa över det här avsnittet om dina moduler körs på samma dator som Visual Studio Code, eftersom du använder localhost för att ansluta till behållaren och redan har rätt port inställningar i **. debug** -Dockerfile, modulens behållar `createOptions` Inställningar och `launch.json` fil. Om dina moduler och Visual Studio-koden körs på separata datorer följer du stegen för ditt utvecklings språk.

- **C#, inklusive Azure Functions**

  [Konfigurera SSH-kanalen på utvecklings datorn och IoT Edge enhet](https://github.com/OmniSharp/omnisharp-vscode/wiki/Attaching-to-remote-processes) och redigera sedan `launch.json` filen som ska bifogas.

- **Node.js**

  - Kontrol lera att modulen på datorn som ska felsökas körs och är redo för fel sökning och att den porten 9229 är tillgänglig externt. Du kan kontrol lera detta genom att öppna `http://<target-machine-IP>:9229/json` fel söknings datorn. URL: en ska visa information om Node.js-modulen som ska felsökas.
  
  - På din utvecklings dator öppnar du Visual Studio Code och redigerar sedan `launch.json` så att adress värdet för ***&lt; &gt; din Modulnamn* -profil för fjärrfelsökning (Node.js)** (eller **_&lt; ditt Modulnamn &gt;_ , fjärrfelsökning (Node.js i Windows-behållare)** visas om modulen körs som en Windows-behållare) är IP-adressen för den dator som ska felsökas.

- **Java**

  - Bygg en SSH-tunnel till datorn som ska felsökas genom att köra `ssh -f <username>@<target-machine> -L 5005:127.0.0.1:5005 -N` .
  
  - Öppna Visual Studio Code på utvecklings datorn och redigera ***&lt; din modul namn &gt;* Remote debug (Java)-** profil i `launch.json` så att du kan ansluta till mål datorn. Mer information om hur du redigerar `launch.json` och felsöker Java med Visual Studio Code finns i avsnittet om hur [du konfigurerar fel sökning](https://code.visualstudio.com/docs/java/java-debugging#_configuration).

- **Python**

  - Kontrol lera att port 5678 på datorn som ska felsökas är öppen och tillgänglig.

  - I den kod `ptvsd.enable_attach(('0.0.0.0', 5678))` som du tidigare infogade i `main.py` , ändra **0.0.0.0** till IP-adressen för den dator som ska felsökas. Bygg, push och distribuera IoT Edge-modulen igen.

  - På din utvecklings dator öppnar du Visual Studio Code och redigerar sedan `launch.json` så att `host` värdet för ***&lt; din &gt;* -profil för Fjärrhjälp (python)** använder IP-adressen för mål datorn i stället för `localhost` .

### <a name="debug-your-module"></a>Felsöka modulen

1. I Visual Studio Code debug-vyn väljer du fel söknings konfigurations filen för modulen. Namnet på fel söknings alternativet bör likna ***&lt; ditt Modulnamn- &gt; namn* för fjärrfelsökning**

1. Öppna modul filen för ditt utvecklings språk och Lägg till en Bryt punkt:

   - **Azure function (C#)**: Lägg till din Bryt punkt i filen `<your module name>.cs` .
   - **C#**: Lägg till din Bryt punkt i filen `Program.cs` .
   - **Node.js**: Lägg till din Bryt punkt i filen `app.js` .
   - **Java**: Lägg till din Bryt punkt i filen `App.java` .
   - **Python**: Lägg till din Bryt punkt `main.py` i filen i callback-metoden där du lade till `ptvsd.break_into_debugger()` raden.
   - **C**: Lägg till din Bryt punkt i filen `main.c` .

1. Välj **Starta fel sökning** eller Välj **F5**. Välj den process som du vill koppla till.

1. I Visual Studio Codes debug-vy ser du variablerna i den vänstra panelen.

> [!NOTE]
> Föregående exempel visar hur du felsöker IoT Edge moduler på behållare. Den har lagt till exponerade portar till din moduls behållar `createOptions` Inställningar. När du har slutfört fel sökningen av dina moduler rekommenderar vi att du tar bort de exponerade portarna för produktions färdiga IoT Edge moduler.

## <a name="build-and-debug-a-module-remotely"></a>Bygg och Felsök en modul via fjärr anslutning

Med de senaste ändringarna i både Docker-och Moby-motorerna för att stödja SSH-anslutningar och en ny inställning i Azure IoT-verktyg som aktiverar inmatning av miljö inställningar i Visual Studio Code-kommando paletten och Azure IoT Edge terminaler, kan du nu skapa och felsöka moduler på fjärranslutna enheter.

Mer information och stegvisa anvisningar finns i blogg inlägget för [IoT-utvecklare](https://devblogs.microsoft.com/iotdev/easily-build-and-debug-iot-edge-modules-on-your-remote-device-with-azure-iot-edge-for-vs-code-1-9-0/) .

## <a name="next-steps"></a>Nästa steg

När du har skapat modulen lär du dig hur du [distribuerar Azure IoT Edge moduler från Visual Studio Code](how-to-deploy-modules-vscode.md).

För att utveckla moduler för dina IoT Edge enheter, [förstå och använda Azure IoT Hub SDK](../iot-hub/iot-hub-devguide-sdks.md): er.