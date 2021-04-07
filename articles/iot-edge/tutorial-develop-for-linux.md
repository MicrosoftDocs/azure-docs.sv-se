---
title: Självstudie – utveckla modul för Linux-enheter med Azure IoT Edge
description: Den här själv studie kursen beskriver hur du konfigurerar en utvecklings dator och moln resurser för att utveckla IoT Edge moduler med hjälp av Linux-behållare för Linux-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: fea8f52ebf40ba8195de134098693f90315bb384
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103461427"
---
# <a name="tutorial-develop-iot-edge-modules-with-linux-containers"></a>Självstudie: utveckla IoT Edge moduler med Linux-behållare

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Använd Visual Studio Code för att utveckla och distribuera kod till enheter som kör IoT Edge.

I snabb starten skapade du en IoT Edge-enhet och distribuerade en modul från Azure Marketplace. Den här självstudien vägleder dig genom att utveckla och distribuera din egen kod till en IoT Edge enhet. Den här artikeln är en användbar förutsättning för de andra självstudierna, som går till mer detaljerad information om särskilda programmeringsspråk eller Azure-tjänster.

I den här självstudien använder vi exemplet på att distribuera en **C#-modul till en Linux-enhet**. Det här exemplet valdes eftersom det är det vanligaste utvecklings scenariot för IoT Edge lösningar. Även om du planerar att använda ett annat språk eller distribuera en Azure-tjänst är den här självstudien fortfarande användbar för att lära dig mer om utvecklingsverktyg och koncept. Slutför den här introduktionen till utvecklings processen och välj sedan önskat språk eller Azure-tjänst för att komma in i informationen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Konfigurera din utvecklings dator.
> * Använd IoT Edge verktyg för Visual Studio Code för att skapa ett nytt projekt.
> * Skapa ditt projekt som en behållare och lagra det i ett Azure Container Registry.
> * Distribuera din kod till en IoT Edge enhet.

## <a name="prerequisites"></a>Förutsättningar

En utvecklings dator:

* Du kan använda din egen dator eller en virtuell dator, beroende på dina utvecklings inställningar.
  * Kontrol lera att utvecklings datorn stöder kapslad virtualisering. Den här funktionen är nödvändig för att köra en behållar motor som du installerar i nästa avsnitt.
* De flesta operativ system som kan köra en behållar motor kan användas för att utveckla IoT Edge moduler för Linux-enheter. I den här självstudien används en Windows-dator, men vi pekar på kända skillnader i macOS eller Linux.
* Installera [git](https://git-scm.com/), för att hämta mallar för modulblad senare i den här självstudien.  
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://www.microsoft.com/net/download).

En Azure IoT Edge-enhet:

* Vi rekommenderar att du inte kör IoT Edge på din utvecklings dator, utan i stället använder en separat enhet. Den här skillnaden mellan utvecklings datorn och IoT Edge enheten är mer korrekt speglar ett verkligt distributions scenario och hjälper till att hålla de olika koncepten direkt.
* Om du inte har någon andra enhet tillgänglig använder du snabb starts artikeln för att skapa en IoT Edge enhet i Azure med en [virtuell Linux-dator](quickstart-linux.md).

Molnresurser:

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) (kostnads fri) eller standard nivå i Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Viktiga begrepp

Den här självstudien vägleder dig genom utvecklingen av en IoT Edge modul. En *IoT Edge modul*, eller ibland bara *modul* för kort, är en behållare med körbar kod. Du kan distribuera en eller flera moduler till en IoT Edge enhet. Moduler utför vissa uppgifter som att mata in data från sensorer, rensa och analysera data eller skicka meddelanden till en IoT-hubb. Mer information finns i [förstå Azure IoT Edge moduler](iot-edge-modules.md).

När du utvecklar IoT Edge moduler är det viktigt att förstå skillnaden mellan utvecklings datorn och mål IoT Edges enheten där modulen slutligen kommer att distribueras. Den behållare som du skapar för att hålla din modul kod måste matcha *mål enhetens* operativ system (OS). Det vanligaste scenariot är att någon utvecklar en modul på en Windows-dator som avser att rikta in sig mot en Linux-enhet som kör IoT Edge. I så fall skulle behållar operativ systemet vara Linux. När du går igenom den här självstudien bör du tänka på skillnaden mellan *utvecklings datorn* och *BEhållar operativ* systemet.

>[!TIP]
>Om du använder [IoT Edge för Linux i Windows](iot-edge-for-linux-on-windows.md), är *mål enheten* i ditt scenario den virtuella Linux-datorn, inte Windows-värden.

Den här kursen riktar sig till enheter som kör IoT Edge med Linux-behållare. Du kan använda det önskade operativ systemet så länge din utvecklings dator kör Linux-behållare. Vi rekommenderar att du använder Visual Studio Code för att utveckla med Linux-behållare, så det är vad den här självstudien kommer att använda. Du kan även använda Visual Studio även om det finns skillnader i stödet mellan de två verktygen.

I följande tabell visas de utvecklings scenarier som stöds för **Linux-behållare** i Visual Studio Code och Visual Studio.

|   | Visuell Studio-kod | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Arkitektur för Linux-enhet** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-tjänster** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Språk** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mer information** | [Azure IoT Edge för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge verktyg för Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Stöd för Linux ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Mer information finns i [utveckla och FELSÖKA ARM64 IoT Edge moduler i Visual Studio Code (för hands version)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

I den här självstudien får du lära dig utvecklings stegen för Visual Studio Code. Om du hellre vill använda Visual Studio läser du instruktionerna i [använda Visual studio 2019 för att utveckla och felsöka moduler för Azure IoT Edge](how-to-visual-studio-develop-module.md).
## <a name="install-container-engine"></a>Installera container motor

IoT Edge moduler paketeras som behållare, så du behöver en behållar motor på utvecklings datorn för att bygga och hantera dem. Vi rekommenderar Docker Desktop för utveckling på grund av dess funktions support och popularitet. Med Docker Desktop i Windows kan du växla mellan Linux-behållare och Windows-behållare så att du enkelt kan utveckla moduler för olika typer av IoT Edge enheter.

Använd Docker-dokumentationen för att installera på utvecklings datorn:

* [Installera Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * När du installerar Docker Desktop för Windows får du en fråga om du vill använda Linux-eller Windows-behållare. Du kan ändra det här beslutet när som helst med hjälp av en enkel växel. I den här självstudien använder vi Linux-behållare eftersom våra moduler är riktade mot Linux-enheter. Mer information finns i [Växla mellan Windows-och Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

* [Installera Docker Desktop för Mac](https://docs.docker.com/docker-for-mac/install/)

* Läs [om Docker CE](https://docs.docker.com/install/) för installations information på flera Linux-plattformar.
  * För Windows-undersystemet för Linux (WSL) installerar du Docker Desktop för Windows.

## <a name="set-up-vs-code-and-tools"></a>Konfigurera VS-kod och verktyg

Använd IoT-tilläggen för Visual Studio Code för att utveckla IoT Edge moduler. Dessa tillägg tillhandahåller projektmallar, automatiserar skapandet av distributions manifestet och låter dig övervaka och hantera IoT Edge enheter. I det här avsnittet installerar du Visual Studio Code och IoT-tillägget och konfigurerar sedan ditt Azure-konto för att hantera IoT Hub-resurser i Visual Studio Code.

1. Installera [Visual Studio Code](https://code.visualstudio.com/) på utvecklings datorn.

2. När installationen är färdig väljer du **Visa**  >  **tillägg**.

3. Sök efter **Azure IoT-verktyg**, som i själva verket är en samling tillägg som hjälper dig att interagera med IoT Hub-och IoT-enheter samt utveckla IoT Edge moduler.

4. Välj **installera**. Varje inkluderat tillägg installeras individuellt.

5. När du har installerat tilläggen öppnar du paletten kommando genom att välja **Visa**  >  **kommando palett**.

6. I paletten kommando söker du efter och väljer **Azure: Logga** in. Följ anvisningarna för att logga in på ditt Azure-konto.

7. I kommando paletten igen söker du efter och väljer **Azure-IoT Hub: välj IoT Hub**. Följ anvisningarna för att välja din Azure-prenumeration och IoT Hub.

8. Öppna avsnittet Explorer i Visual Studio Code genom att antingen välja ikonen i aktivitets fältet till vänster eller genom att välja **Visa**  >  **Utforskaren**.

9. Längst ned i Explorer-avsnittet expanderar du menyn komprimerade **Azure-IoT Hub/-enheter** . Du bör se de enheter och IoT Edge enheter som är kopplade till den IoT-hubb som du valde via kommando paletten.

   ![Visa enheter i din IoT-hubb](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Skapa ett nytt modul-projekt

Tillägget Azure IoT Tools innehåller projektmallar för alla språk som stöds IoT Edge-moduler i Visual Studio Code. Dessa mallar har alla filer och all kod som du behöver för att distribuera en fungerande modul för att testa IoT Edge, eller ge dig en start punkt för att anpassa mallen med din egen affärs logik.

I den här självstudien använder vi C#-modulen, eftersom det är den mall som används oftast.

### <a name="create-a-project-template"></a>Skapa en projektmall

I kommando paletten Visual Studio Code söker du efter och väljer **Azure IoT Edge: ny IoT Edge lösning**. Följ anvisningarna och Använd följande värden för att skapa din lösning:

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C#-modul**. |
   | Ange ett modulnamn | Godkänn standard- **SampleModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry. Du kan hämta inloggnings serverns värde från sidan Översikt i behållar registret i Azure Portal. <br><br> Den slutliga avbildnings lagrings platsen ser ut som \<registry name\> . azurecr.io/samplemodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-develop-for-linux/image-repository.png)

När din nya lösning har lästs in i Visual Studio Code-fönstret kan du bekanta dig med de filer som den skapade:

* Mappen **. VSCode** innehåller en fil med namnet **launch.jspå**, som används för fel söknings moduler.
* Mappen **moduler** innehåller en mapp för varje modul i din lösning. Just nu är det bara **SampleModule** eller det namn som du har fått till modulen. Mappen SampleModule innehåller huvud program koden, modulens metadata och flera Docker-filer.
* **. Miljö** filen innehåller autentiseringsuppgifterna till behållar registret. Autentiseringsuppgifterna delas med din IoT Edge-enhet så att de har åtkomst till att hämta behållar avbildningarna.
* **deployment.debug.template.jsi** filen och **deployment.template.jspå** filen är mallar som hjälper dig att skapa ett distributions manifest. Ett *distributions manifest* är en fil som definierar exakt vilka moduler som du vill distribuera på en enhet, hur de ska konfigureras och hur de kan kommunicera med varandra och molnet. Mallfilerna använder punkter för vissa värden. När du transformerar mallen till ett sant distributions manifest ersätts pekarna med värden som tas från andra lösningsfiler. Leta upp de två vanliga plats hållarna i distributions mal len:

  * I avsnittet autentiseringsuppgifter för registret fylls adressen till från den information som du angav när du skapade lösningen. Användar namnet och lösen ordet refererar dock till de variabler som lagras i. miljö-filen. Den här konfigurationen är av säkerhets nivå, eftersom. filen. ".
  * I avsnittet SampleModule fylls behållar avbildningen inte i, även om du angav avbildnings lagrings platsen när du skapade lösningen. Plats hållaren pekar på **module.jspå** filen i mappen SampleModule. Om du går till filen ser du att bild fältet innehåller lagrings platsen, men också ett tagg värde som består av versionen och behållarens plattform. Du kan iterera versionen manuellt som en del av din utvecklings cykel och du väljer behållar plattformen med en växlaren som vi introducerar senare i det här avsnittet.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Ange autentiseringsuppgifterna för registret för IoT Edge agenten

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta behållar avbildningarna till den IoT Edge enheten.

>[!NOTE]
>Om du inte har bytt **localhost: 5000** värde med inloggnings serverns värde från ditt Azure Container Registry, i steget [**skapa en projekt mal len**](#create-a-project-template) , saknas **..** ..... 

IoT Edge-tillägget försöker hämta dina autentiseringsuppgifter för behållar registret från Azure och fylla dem i miljö filen. Kontrol lera om dina autentiseringsuppgifter redan ingår. Om inte, lägger du till dem nu:

1. Öppna **. kuvert** -filen i din modul-lösning.
2. Lägg till värdena för **användar namn** och **lösen ord** som du kopierade från Azure Container Registry.
3. Spara ändringarna i. kuvert-filen.

### <a name="select-your-target-architecture"></a>Välj din mål arkitektur

För närvarande kan Visual Studio Code utveckla C#-moduler för Linux AMD64-och ARM32v7-enheter. Du måste välja vilken arkitektur du är riktad mot varje lösning, eftersom det påverkar hur behållaren byggs och körs. Standardvärdet är Linux AMD64.

1. Öppna paletten kommando och Sök efter **Azure IoT Edge: Ange standard plattform för Edge-lösning** eller Välj gen vägs ikonen i sido fältet längst ned i fönstret.

   ![Välj arkitektur ikon i sido fältet](./media/tutorial-develop-for-linux/select-architecture.png)

2. I paletten kommando väljer du mål arkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så behåller standard- **amd64**.

### <a name="review-the-sample-code"></a>Granska exempel koden

I lösnings mal len som du skapade ingår exempel kod för en IoT Edge-modul. Den här exempel modulen tar bara emot meddelanden och skickar dem vidare. Pipeline-funktionen visar ett viktigt begrepp i IoT Edge, vilket är hur moduler kommunicerar med varandra.

Varje modul kan ha flera *indata* -och *utmatnings* köer som deklareras i koden. IoT Edge hubben som körs på enheten dirigerar meddelanden från utdata från en modul till indata för en eller flera moduler. Den speciella koden för att deklarera indata och utdata varierar mellan olika språk, men begreppet är detsamma i alla moduler. Mer information om routning mellan moduler finns i [deklarera vägar](module-composition.md#declare-routes).

C#-koden som medföljer projekt mal len använder [klassen ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) från IoT Hub SDK för .net.

1. Öppna filen **program. cs** , som finns i mappen **moduler/SampleModule/** Folder.

2. Hitta **SetInputMessageHandlerAsync** -metoden i program. cs.

3. Metoden [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) konfigurerar en inkommande kö för att ta emot inkommande meddelanden. Granska den här metoden och se hur den initierar en indatakö med namnet **INPUT1**.

   ![Hitta indatamängden i SetInputMessageCallback-konstruktorn](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Leta sedan reda på **SendEventAsync** -metoden.

5. [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) -metoden bearbetar mottagna meddelanden och konfigurerar en utgående kö för att skicka dem vidare. Granska den här metoden och se att den initierar en utgående kö med namnet **output1**.

   ![Hitta utdatafilens namn i SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Öppna filen **deployment.template.js** .

7. Hitta egenskapen **modules** för de $edgeAgent önskade egenskaperna.

   Det bör finnas två moduler som visas här. En är **SimulatedTemperatureSensor** -modulen som ingår i alla mallar som standard för att tillhandahålla simulerade temperatur data som du kan använda för att testa dina moduler. Den andra är **SampleModule** -modulen som du skapade som en del av den här lösningen.

8. Leta upp önskade egenskaper för **$edgeHub** -modulen längst ned i filen.

   En av funktionerna i modulen IoT Edge Hub är att dirigera meddelanden mellan alla moduler i en distribution. Granska värdena i egenskapen **routes** . En väg, **SampleModuleToIoTHub**, använder ett jokertecken (* *\** _) för att visa alla meddelanden som kommer från alla utgående köer i SampleModule-modulen. Dessa meddelanden hamnar i _ $ Stream *, vilket är ett reserverat namn som visar IoT Hub. Den andra vägen, **sensorToSampleModule**, tar meddelanden från SimulatedTemperatureSensor-modulen och dirigerar dem till den *INPUT1* -indatakö som du såg i SampleModule-koden.

   ![Granska vägar i deployment.template.jspå](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

Du har granskat modulens kod och distributions mal len för att förstå några viktiga distributions koncept. Nu är du redo att bygga SampleModule behållar avbildning och push-överför den till behållar registret. Med IoT tools-tillägget för Visual Studio Code genererar det här steget även distributions manifestet baserat på informationen i mallfilen och modulens information från lösningsfiler.

### <a name="sign-in-to-docker"></a>Logga in på Docker

Ange dina autentiseringsuppgifter för behållar registret till Docker så att den kan skicka behållar avbildningen så att den lagras i registret.

1. Öppna Visual Studio Code Integrated Terminal genom att välja **Visa**  >  **Terminal**.

2. Logga in på Docker med de autentiseringsuppgifter för Azure Container Registry som du sparade när du skapade registret.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar att du använder `--password-stdin` . Det bästa tillvägagångs sättet rekommenderas för produktions scenarier, men det ligger utanför omfånget för den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .
   
3. Logga in på Azure Container Registry

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Bygg och skicka

Visual Studio Code har nu åtkomst till ditt behållar register, så det är dags att omvandla lösnings koden till en behållar avbildning.

1. I Visual Studio Code Explorer högerklickar du på filen **deployment.template.js** och väljer **Build och push IoT Edge lösning**.

   ![Bygga och push-IoT Edge moduler](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** och som innehåller det fullständiga distributions manifestet, och bygger ut information i distributions mal len och andra filer i lösningen. Sedan körs den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs den `docker push` för att skicka avbildnings lagrings platsen till behållar registret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

2. Öppna filen **deployment.amd64.jspå** filen i den nyligen skapade mappen config. Fil namnet visar mål arkitekturen, så det är en annan om du väljer en annan arkitektur.

3. Observera att de två parametrarna som hade plats hållare nu fylls med sina rätta värden. I avsnittet **registryCredentials** finns ditt register användar namn och lösen ord hämtade från. miljö-filen. **SampleModule** har den fullständiga avbildnings lagrings platsen med namnet, versionen och arkitektur tag gen från module.jsi filen.

4. Öppna filen **module.js** i mappen SampleModule

5. Ändra versions numret för modulens bild. (Versionen, inte $schema-versionen.) Du kan till exempel öka korrigerings versions numret till **0.0.2** som om vi hade gjort en liten korrigering i modulens kod.

   >[!TIP]
   >Med versioner av versioner aktiverar du versions kontroll och du kan testa ändringar på en liten uppsättning enheter innan du distribuerar uppdateringar till produktion. Om du inte ökar modulens version innan du skapar och skickar, skriver du över lagrings platsen i behållar registret.

6. Spara ändringarna i module.jspå filen.

7. Högerklicka på **deployment.template.js** filen igen och välj sedan **build-och push-IoT Edge lösning** igen.

8. Öppna **deployment.amd64.js** filen igen. Observera att en ny fil inte skapades när du körde kommandot build och push igen. I stället har samma fil uppdaterats för att återspegla ändringarna. SampleModule-avbildningen pekar nu mot 0.0.2-versionen av behållaren.

9. För att ytterligare kontrol lera vad build och push-kommandot gjorde går du till [Azure Portal](https://portal.azure.com) och navigerar till behållar registret.

10. I behållar registret väljer du **databaser** och sedan **samplemodule**. Kontrol lera att båda versionerna av avbildningen push-överfördes till registret.

    ![Visa båda avbildnings versionerna i behållar registret](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Felsöka

Om du stöter på fel när du skapar och skickar en modultyp måste det ofta göras med Docker-konfiguration på din utvecklings dator. Använd följande kontroller för att granska konfigurationen:

* Körde du `docker login` kommandot med de autentiseringsuppgifter som du kopierade från behållar registret? De här autentiseringsuppgifterna skiljer sig från de som du använder för att logga in på Azure.
* Stämmer containerlagringsplatsen? Har du rätt namn på behållar registret och rätt Modulnamn? Öppna filen **module.js** i mappen SampleModule för att kontrol lera. Värdet för lagring bör se ut som **\<registry name\> . azurecr.io/samplemodule**.
* Om du har använt ett annat namn än **SampleModule** för modulen, är det namnet konsekvent i lösningen?
* Kör datorn samma typ av behållare som du skapar? Den här självstudien gäller för Linux IoT Edge-enheter, så Visual Studio Code bör säga **amd64** eller **arm32v7** i sido fältet, och Docker Skriv bordet ska köra Linux-behållare.  

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Du verifierade att de inbyggda behållar avbildningarna lagras i behållar registret, så det är dags att distribuera dem till en enhet. Kontrol lera att din IoT Edges enhet är igång.

1. I Visual Studio Code Explorer, under avsnittet **Azure IoT Hub** , expanderar du **enheter** för att se listan med IoT-enheter.

2. Högerklicka på den IoT Edge enhet som du vill distribuera till och välj sedan **skapa distribution för en enskild enhet**.

   ![Skapa distribution för en enskild enhet](./media/tutorial-develop-for-linux/create-deployment.png)

3. I Utforskaren navigerar du till mappen **config** och väljer sedan filen **deployment.amd64.js** .

   Använd inte deployment.template.jspå filen, som inte har autentiseringsuppgifter för behållar registret eller modulens bildvärden. Om du är mål för en Linux ARM32-enhet får distributions manifestet namnet deployment.arm32v7.jspå.

4. Under din enhet expanderar du **moduler** för att se en lista över distribuerade och aktiva moduler. Klicka på uppdateringsknappen. Du bör se de nya SimulatedTemperatureSensor-och SampleModule-modulerna som körs på enheten.

   Det kan ta några minuter innan modulerna startar. Den IoT Edge körnings miljön måste ta emot sitt nya distributions manifest, Hämta modulens avbildningar från container körningen och sedan starta varje ny modul.

   ![Visa moduler som körs på din IoT Edge-enhet](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visa meddelanden från enheten

SampleModule-koden tar emot meddelanden via sin indatakö och skickar dem tillsammans med dess utgående kö. Distributions manifestet deklarerade vägar som skickade meddelanden till SampleModule från SimulatedTemperatureSensor och vidarebefordrade meddelanden från SampleModule till IoT Hub. Med Azure IoT-verktygen för Visual Studio Code kan du se meddelanden när de kommer till IoT Hub från dina enskilda enheter.

1. I Visual Studio Code Explorer högerklickar du på den IoT Edge enhet som du vill övervaka och väljer sedan **starta övervakning av inbyggd händelse slut punkt**.

2. Titta på fönstret utdata i Visual Studio Code för att se meddelanden som anländer till din IoT Hub.

   ![Visa inkommande enhet till moln meddelanden](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visa ändringar på enheten

Om du vill se vad som händer på själva enheten kan du använda kommandona i det här avsnittet för att granska IoT Edge körning och moduler som körs på enheten.

Kommandona i det här avsnittet gäller för din IoT Edge-enhet, inte din utvecklings dator. Om du använder en virtuell dator för din IoT Edge-enhet ansluter du till den nu. I Azure går du till sidan Översikt för den virtuella datorn och väljer **Anslut** för att få åtkomst till säker Shell-anslutning.

* Visa alla moduler som har distribuerats till enheten och kontrol lera deras status:

   ```bash
   iotedge list
   ```

   Du bör se fyra moduler: de två IoT Edge runtime-modulerna, SimulatedTemperatureSensor och SampleModule. Alla fyra ska visas som körs.

* Granska loggarna för en speciell modul:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge moduler är Skift läges känsliga.

   SimulatedTemperatureSensor-och SampleModule-loggarna bör visa de meddelanden som bearbetas. EdgeAgent-modulen ansvarar för att starta de andra modulerna, så dess loggar har information om att implementera distributions manifestet. Om någon modul inte finns med i listan eller inte körs, kommer edgeAgent-loggarna förmodligen att ha felen. EdgeHub-modulen ansvarar för kommunikation mellan modulerna och IoT Hub. Om modulerna är igång, men meddelandena inte kommer till din IoT-hubb, kommer edgeHub-loggarna förmodligen att ha felen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien ställer du in Visual Studio Code på utvecklings datorn och distribuerar din första IoT Edge modul från den. Nu när du känner till de grundläggande begreppen kan du försöka lägga till funktioner i en modul så att den kan analysera data som passerar genom den. Välj önskat språk:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md) 
>  [C#](tutorial-csharp-module.md) 
>  [Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  [Python](tutorial-python-module.md)
