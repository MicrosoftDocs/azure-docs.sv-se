---
title: Självstudie – Utveckla modul för Linux-enheter med Azure IoT Edge
description: Den här självstudien visar hur du ställer in utvecklingsdatorn och molnresurser för att utveckla IoT Edge-moduler med Linux-containrar för Linux-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 10742ec277f5742067c432a2823cbb7592e6a752
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874708"
---
# <a name="tutorial-develop-iot-edge-modules-with-linux-containers"></a>Självstudie: Utveckla IoT Edge moduler med Linux-containrar

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Använd Visual Studio Code för att utveckla och distribuera kod till enheter som kör IoT Edge.

I snabbstarten skapade du en IoT Edge och distribuerade en modul från Azure Marketplace. Den här självstudien visar hur du utvecklar och distribuerar din egen kod till en IoT Edge enhet. Den här artikeln är ett användbart krav för de andra självstudierna, som går in mer i detalj på specifika programmeringsspråk eller Azure-tjänster.

I den här självstudien används ett exempel på hur du **distribuerar en C#-modul till en Linux-enhet.** Det här exemplet valdes eftersom det är det vanligaste scenariot för utvecklare IoT Edge lösningar. Även om du planerar att använda ett annat språk eller distribuera en Azure-tjänst är den här självstudien fortfarande användbar för att lära dig mer om utvecklingsverktyg och begrepp. Slutför den här introduktionen till utvecklingsprocessen och välj sedan önskat språk eller Azure-tjänst för att fördjupa dig i detaljerna.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Konfigurera utvecklingsdatorn.
> * Använd verktygen IoT Edge för att Visual Studio Code för att skapa ett nytt projekt.
> * Skapa projektet som en container och lagra det i ett Azure-containerregister.
> * Distribuera din kod till en IoT Edge enhet.

## <a name="prerequisites"></a>Förutsättningar

En utvecklingsdator:

* Du kan använda din egen dator eller en virtuell dator, beroende på dina utvecklingsinställningar.
  * Kontrollera att utvecklingsdatorn stöder kapslad virtualisering. Den här funktionen är nödvändig för att köra en containermotor, som du installerar i nästa avsnitt.
* De flesta operativsystem som kan köra en containermotor kan användas för att utveckla IoT Edge moduler för Linux-enheter. Den här självstudien använder en Windows-dator, men pekar på kända skillnader i macOS eller Linux.
* Installera [Git](https://git-scm.com/)för att hämta modulmallpaket senare i den här självstudien.  
* [C# för Visual Studio Code-tillägg (drivs av OmniSharp)](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).
* [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet/2.1).

En Azure IoT Edge-enhet:

* Vi rekommenderar att du inte kör IoT Edge på utvecklingsdatorn utan i stället använder en separat enhet. Den här skillnaden mellan utvecklingsdatorn och IoT Edge på ett mer exakt sätt speglar ett verkligt distributionsscenario och hjälper till att hålla de olika begreppen räta.
* Om du inte har en andra tillgänglig enhet använder du snabbstartsartikeln för att skapa en IoT Edge i Azure med en [virtuell Linux-dator](quickstart-linux.md).

Molnresurser:

* En kostnadsfri [IoT-hubb eller IoT-hubb på](../iot-hub/iot-hub-create-through-portal.md) standardnivå i Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Viktiga begrepp

Den här självstudien går igenom utvecklingen av en IoT Edge-modul. En *IoT Edge modul*, eller ibland bara *modul,* är en container med körbar kod. Du kan distribuera en eller flera moduler till en IoT Edge enhet. Moduler utför specifika uppgifter som att mata in data från sensorer, rensa och analysera data eller skicka meddelanden till en IoT-hubb. Mer information finns i [Förstå Azure IoT Edge moduler](iot-edge-modules.md).

När du IoT Edge moduler är det viktigt att du förstår skillnaden mellan utvecklingsdatorn och måldatorn IoT Edge där modulen så småningom kommer att distribueras. Den container som du skapar för att innehålla modulkoden måste matcha målenhetens operativsystem *(OS).* Det vanligaste scenariot är till exempel att någon utvecklar en modul på en Windows-dator som avser att rikta in sig på en Linux-enhet som kör IoT Edge. I så fall skulle containeroperativsystemet vara Linux. När du går igenom den här självstudien bör du tänka på skillnaden mellan *operativsystemet för utvecklingsdatorn* och *operativsystemet för containrar.*

>[!TIP]
>Om du använder en [IoT Edge För Linux](iot-edge-for-linux-on-windows.md)i  Windows är målenheten i ditt scenario den virtuella Linux-datorn, inte Windows-värden.

Den här självstudien riktar sig till enheter IoT Edge med Linux-containrar. Du kan använda det operativsystem du föredrar så länge utvecklingsdatorn kör Linux-containrar. Vi rekommenderar att Visual Studio Code för att utveckla med Linux-containrar, så det är det som den här självstudien använder. Du kan använda Visual Studio också, även om det finns skillnader i stödet mellan de två verktygen.

I följande tabell visas de utvecklingsscenarier som stöds **för Linux-containrar** i Visual Studio Code och Visual Studio.

|   | Visuell Studio-kod | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Linux-enhetsarkitektur** | Linux AMD64 <br> Linux ARM32 | Linux AMD64 <br> Linux ARM32 |
| **Azure-tjänster** | Azure Functions <br> Azure Stream Analytics <br> Azure Machine Learning |   |
| **Språk** | C <br> C# <br> Java <br> Node.js <br> Python | C <br> C# |
| **Mer information** | [Azure IoT Edge för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge Tools för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) <br> [Azure IoT Edge Tools för Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

>[!NOTE]
>Stöd för Linux ARM64-enheter är tillgängligt i [offentlig förhandsversion.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Mer information finns i Utveckla [och felsöka ARM64 IoT Edge moduler i Visual Studio Code (förhandsversion)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview).

I den här självstudien lär du dig utvecklingsstegen för Visual Studio Code. Om du hellre vill använda Visual Studio kan du läsa anvisningarna i [Använda Visual Studio 2019](how-to-visual-studio-develop-module.md)för att utveckla och felsöka moduler för Azure IoT Edge .
## <a name="install-container-engine"></a>Installera containermotor

IoT Edge-moduler paketeras som containrar, så du behöver en containermotor på utvecklingsdatorn för att skapa och hantera dem. Vi rekommenderar Docker Desktop för utveckling på grund av dess funktionsstöd och popularitet. Med Docker Desktop i Windows kan du växla mellan Linux-containrar och Windows-containrar så att du enkelt kan utveckla moduler för olika typer IoT Edge enheter.

Använd Docker-dokumentationen för att installera på utvecklingsdatorn:

* [Installera Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * När du installerar Docker Desktop för Windows tillfrågas du om du vill använda Linux- eller Windows-containrar. Det här beslutet kan ändras när som helst med hjälp av en enkel växel. I den här självstudien använder vi Linux-containrar eftersom våra moduler riktar sig mot Linux-enheter. Mer information finns i Växla [mellan Windows- och Linux-containrar.](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)

* [Installera Docker Desktop för Mac](https://docs.docker.com/docker-for-mac/install/)

* Läs [Om Docker CE för](https://docs.docker.com/install/) installationsinformation på flera Linux-plattformar.
  * För Windows-undersystem för Linux (WSL) installerar du Docker Desktop för Windows.

## <a name="set-up-vs-code-and-tools"></a>Konfigurera VS Code och verktyg

Använd IoT-tilläggen för Visual Studio Code för att utveckla IoT Edge moduler. Dessa tillägg tillhandahåller projektmallar, automatiserar skapandet av distributionsmanifestet och gör att du kan övervaka och hantera IoT Edge enheter. I det här avsnittet ska du installera Visual Studio Code och IoT-tillägget och sedan konfigurera ditt Azure-konto för att hantera IoT Hub resurser inifrån Visual Studio Code.

1. Installera [Visual Studio Code](https://code.visualstudio.com/) på utvecklingsdatorn.

2. När installationen är klar väljer du **Visa**  >  **tillägg.**

3. Sök efter **Azure IoT Tools**, som i själva verket är en samling tillägg som hjälper dig att interagera med IoT Hub- och IoT-enheter, samt utveckla IoT Edge moduler.

4. Välj **installera**. Varje tillägg som ingår installeras individuellt.

5. När tilläggen har installerats öppnar du kommandopaletten genom att välja **Visa**  >  **kommandopalett.**

6. I kommandopaletten söker du efter och **väljer Azure: Logga in**. Följ anvisningarna för att logga in på ditt Azure-konto.

7. I kommandopaletten igen söker du efter och **väljer Azure IoT Hub: Välj IoT Hub**. Följ anvisningarna för att välja din Azure-prenumeration och IoT Hub.

8. Öppna utforskaravsnittet i Visual Studio Code genom att antingen välja ikonen i aktivitetsfältet till vänster eller genom att välja **Visa**  >  **utforskaren.**

9. Längst ned i utforskaravsnittet expanderar du den komprimerade **menyn Azure IoT Hub/Enheter.** Du bör se de enheter IoT Edge enheter som är associerade med den IoT-hubb som du valde via kommandopaletten.

   ![Visa enheter i din IoT-hubb](./media/tutorial-develop-for-linux/view-iot-hub-devices.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Skapa ett nytt modulprojekt

Tillägget Azure IoT Tools innehåller projektmallar för alla IoT Edge i Visual Studio Code. Dessa mallar har alla filer och all kod som du behöver för att distribuera en fungerande modul för att testa IoT Edge, eller ge dig en startpunkt för att anpassa mallen med din egen affärslogik.

I den här självstudien använder vi C#-modulmallen eftersom det är den mall som används oftast.

### <a name="create-a-project-template"></a>Skapa en projektmall

I kommandopaletten Visual Studio Code söker du efter och **väljer Azure IoT Edge: New IoT Edge Solution**. Följ anvisningarna och använd följande värden för att skapa din lösning:

   | Fält | Värde |
   | ----- | ----- |
   | Välj mapp | Välj den plats på utvecklingsdatorn där Visual Studio Code ska skapa lösningsfilerna. |
   | Ange ett namn på lösningen | Ange ett beskrivande namn för lösningen eller acceptera standardnamnet **EdgeSolution**. |
   | Välj modulmall | Välj **C#-modul**. |
   | Ange ett modulnamn | Acceptera standardvärdet **SampleModule**. |
   | Ange Docker-bildlagringsplats för modulen | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Containeravbildningen fylls i baserat på det namn du angav i föregående steg. Ersätt **localhost:5000 med** värdet **för inloggningsservern** från ditt Azure-containerregister. Du kan hämta värdet för inloggningsservern från översiktssidan för ditt containerregister i Azure Portal. <br><br> Den slutliga avbildningsdatabasen ser \<registry name\> ut som .azurecr.io/samplemodule. |

   ![Ange lagringsplatsen för Docker-avbildningen](./media/tutorial-develop-for-linux/image-repository.png)

När den nya lösningen har Visual Studio code-fönstret kan du bekanta dig med de filer som den skapade:

* Mappen **.vscode** innehåller en fil med **namnetlaunch.jspå**, som används för felsökning av moduler.
* Mappen **modules** innehåller en mapp för varje modul i din lösning. Just nu bör det bara vara **SampleModule**, eller det namn som du gav till modulen. Mappen SampleModule innehåller huvudprogramkoden, modulens metadata och flera Docker-filer.
* **.env-filen** innehåller autentiseringsuppgifterna till ditt containerregister. Dessa autentiseringsuppgifter delas med din IoT Edge så att den har åtkomst till att hämta containeravbildningarna.
* Den **deployment.debug.template.jsfilen** ochdeployment.template.jspå **filen** är mallar som hjälper dig att skapa ett distributionsmanifest. Ett *distributionsmanifest* är en fil som definierar exakt vilka moduler du vill distribuera på en enhet, hur de ska konfigureras och hur de kan kommunicera med varandra och molnet. Mallfilerna använder pekare för vissa värden. När du omvandlar mallen till ett verkligt distributionsmanifest ersätts pekarna med värden som tas från andra lösningsfiler. Leta upp de två vanliga platshållarna i distributionsmallen:

  * I avsnittet autentiseringsuppgifter för registret fylls adressen i automatiskt från den information som du angav när du skapade lösningen. Användarnamnet och lösenordet refererar dock till variablerna som lagras i .env-filen. Den här konfigurationen är av säkerhetsskäl eftersom .env-filen ignoreras, men inte distributionsmallen.
  * I avsnittet SampleModule fylls inte containeravbildningen i trots att du angav avbildningsdatabasen när du skapade lösningen. Platshållaren pekar påmodule.js **i** mappen SampleModule. Om du går till den filen ser du att avbildningsfältet innehåller lagringsplatsen, men även ett taggvärde som består av versionen och plattformen för containern. Du kan iterera versionen manuellt som en del av utvecklingscykeln och välja containerplattformen med hjälp av en switcher som vi introducerar senare i det här avsnittet.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Ange dina autentiseringsuppgifter för registret till IoT Edge agenten

Miljöfilen lagrar autentiseringsuppgifterna för containerregistret och delar dem med körningsmiljön för IoT Edge. Körningen behöver dessa autentiseringsuppgifter för att hämta dina containeravbildningar till IoT Edge enhet.

>[!NOTE]
>Om du inte ersätter **värdet localhost:5000** med inloggningsservervärdet från [](#create-a-project-template) Azure-containerregistret, kommer avsnittet **.env-fil** och registryCredentials i distributionsmanifestet att saknas i steget Skapa en projektmall. 

Tillägget IoT Edge försöker hämta dina autentiseringsuppgifter för containerregistret från Azure och fylla i dem i miljöfilen. Kontrollera om dina autentiseringsuppgifter redan ingår. Annars lägger du till dem nu:

1. Öppna **.env-filen** i modullösningen.
2. Lägg till **de värden** **för användarnamn** och lösenord som du kopierade från Azure-containerregistret.
3. Spara ändringarna i .env-filen.

### <a name="select-your-target-architecture"></a>Välj din målarkitektur

För närvarande Visual Studio Code utveckla C#-moduler för Linux AMD64- och ARM32v7-enheter. Du måste välja vilken arkitektur du riktar in dig på med varje lösning, eftersom det påverkar hur containern byggs och körs. Standardvärdet är Linux AMD64.

1. Öppna kommandopaletten och **sök efter Azure IoT Edge:** Ange standardmålplattform för Edge-lösning, eller välj genvägsikonen i sidofältet längst ned i fönstret.

   ![Välj arkitekturikonen i sidofältet](./media/tutorial-develop-for-linux/select-architecture.png)

2. I kommandopaletten väljer du målarkitekturen i listan med alternativ. I den här självstudien använder vi en virtuell Ubuntu-dator som IoT Edge enhet, så vi behåller standardinställningen **amd64**.

### <a name="review-the-sample-code"></a>Granska exempelkoden

Lösningsmallen som du skapade innehåller exempelkod för en IoT Edge modul. Den här exempelmodulen tar bara emot meddelanden och skickar dem sedan vidare. Pipelinefunktionen demonstrerar ett viktigt begrepp i IoT Edge, vilket är hur moduler kommunicerar med varandra.

Varje modul kan ha flera *indata-* *och utdataköer* deklarerade i sin kod. Den IoT Edge som körs på enheten dirigerar meddelanden från utdata från en modul till indata för en eller flera moduler. Den specifika koden för att deklarera indata och utdata varierar mellan olika språk, men konceptet är detsamma för alla moduler. Mer information om routning mellan moduler finns i [Deklarera vägar.](module-composition.md#declare-routes)

C#-exempelkoden som medföljer projektmallen använder [Klassen ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) från IoT Hub SDK för .NET.

1. Öppna filen **Program.cs,** som finns i **mappen modules/SampleModule/.**

2. Leta reda på metoden **SetInputMessageHandlerAsync** i program.cs.

3. Metoden [SetInputMessageHandlerAsync ställer](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) in en indatakö för att ta emot inkommande meddelanden. Granska den här metoden och se hur den initierar en indatakö med namnet **input1**.

   ![Hitta indatanamnet i SetInputMessageCallback-konstruktorn](./media/tutorial-develop-for-linux/declare-input-queue.png)

4. Leta sedan reda på **metoden SendEventAsync.**

5. Metoden [SendEventAsync bearbetar](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) mottagna meddelanden och uppsättningar en utdatakö för att skicka dem vidare. Granska den här metoden och se att den initierar en utdatakö med namnet **output1**.

   ![Hitta utdatanamnet i SendEventToOutputAsync](./media/tutorial-develop-for-linux/declare-output-queue.png)

6. Öppna filen **deployment.template.jsfilen.**

7. Leta reda på modulegenskapen för $edgeAgent önskade egenskaper. 

   Det bör finnas två moduler listade här. En är **modulen SimulatedTemperatureSensor,** som ingår i alla mallar som standard för att tillhandahålla simulerade temperaturdata som du kan använda för att testa dina moduler. Den andra är **SampleModule-modulen** som du skapade som en del av den här lösningen.

8. Längst ned i filen hittar du önskade egenskaper för den **$edgeHub** modulen.

   En av funktionerna i IoT Edge hub-modulen är att dirigera meddelanden mellan alla moduler i en distribution. Granska värdena i egenskapen **routes.** En väg, **SampleModuleToIoTHub,** använder jokertecken (* _) för att ange alla meddelanden som kommer från utdataköer i *\** sampleModule-modulen. Dessa meddelanden går till _$upstream*, vilket är ett reserverat namn som anger IoT Hub. Den andra vägen, **sensorToSampleModule,** tar meddelanden som kommer från modulen SimulatedTemperatureSensor och dirigerar dem till den *input1-indatakö* som du såg initierades i SampleModule-koden.

   ![Granska vägar i deployment.template.jspå](./media/tutorial-develop-for-linux/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

Du har granskat modulkoden och distributionsmallen för att förstå några viktiga distributionsbegrepp. Nu är du redo att skapa containeravbildningen SampleModule och skicka den till containerregistret. Med IoT-verktygstillägget för Visual Studio Code genererar det här steget även distributionsmanifestet baserat på informationen i mallfilen och modulinformationen från lösningsfilerna.

### <a name="sign-in-to-docker"></a>Logga in på Docker

Ange dina autentiseringsuppgifter för containerregistret till Docker så att den kan skicka containeravbildningen som ska lagras i registret.

1. Öppna den Visual Studio Code-integrerade terminalen genom att **välja**  >  **Visa terminal.**

2. Logga in på Docker med autentiseringsuppgifterna för Azure Container Registry som du sparade när du skapade registret.

   ```cmd/sh
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhetsvarning som rekommenderar att du använder `--password-stdin` . Även om bästa praxis rekommenderas för produktionsscenarier ligger det utanför omfånget för den här självstudien. Mer information finns i [docker-inloggningsreferensen.](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin)
   
3. Logga in på Azure Container Registry

   ```cmd/sh
   az acr login -n <ACR registry name>
   ```

### <a name="build-and-push"></a>Skapa och push-pusha

Visual Studio Code har nu åtkomst till ditt containerregister, så det är dags att omvandla lösningskoden till en containeravbildning.

1. I Visual Studio Code Explorer högerklickar du på **deployment.template.jspå** filen och väljer Build and Push IoT Edge Solution (Skapa och **push-IoT Edge-lösning).**

   ![Skapa och push-IoT Edge moduler](./media/tutorial-develop-for-linux/build-and-push-modules.png)

   Build- och push-kommandot startar tre åtgärder. Först skapas en ny mapp i lösningen med namnet **config** som innehåller det fullständiga distributionsmanifestet, som är byggt på information i distributionsmallen och andra lösningsfiler. För det andra körs den `docker build` för att skapa containeravbildningen baserat på lämplig dockerfile för målarkitekturen. Sedan körs den för `docker push` att push-skicka avbildningsdatabasen till containerregistret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

2. Öppna filen **deployment.amd64.jsi** den nyligen skapade konfigurationsmappen. Filnamnet återspeglar målarkitekturen, så det blir annorlunda om du väljer en annan arkitektur.

3. Observera att de två parametrarna som hade platshållare nu är ifyllda med rätt värden. I **avsnittet registryCredentials** hämtas ditt användarnamn och lösenord för registret från .env-filen. **SampleModule har** den fullständiga avbildningsdatabasen med namn, version och arkitekturtagg från module.jspå filen.

4. Öppna filen **module.jsfilen** i mappen SampleModule.

5. Ändra versionsnumret för modulavbildningen. (Versionen, inte $schema-version.) Öka till exempel korrigeringsversionsnumret **till 0.0.2** som om vi hade gjort en liten korrigering i modulkoden.

   >[!TIP]
   >Modulversioner aktiverar versionskontroll och gör att du kan testa ändringar på en liten uppsättning enheter innan du distribuerar uppdateringar till produktion. Om du inte ökar modulversionen innan du skapar och push-pushar skriver du över lagringsplatsen i containerregistret.

6. Spara ändringarna i module.jspå filen.

7. Högerklicka på filen **deployment.template.jsoch** välj sedan Build and Push IoT Edge Solution (Skapa och **push-IoT Edge lösning).**

8. Öppna filen **deployment.amd64.jsfil** igen. Observera att en ny fil inte skapades när du körde build- och push-kommandot igen. I stället uppdaterades samma fil för att återspegla ändringarna. SampleModule-avbildningen pekar nu på 0.0.2-versionen av containern.

9. Om du vill kontrollera vad kom build och push-kommandot gjorde går du [till Azure Portal](https://portal.azure.com) och navigerar till containerregistret.

10. I containerregistret väljer du **Lagringsplatsen och** **sedan samplemodule**. Kontrollera att båda versionerna av avbildningen push-skickades till registret.

    ![Visa båda avbildningsversionerna i containerregistret](./media/tutorial-develop-for-linux/view-repository-versions.png)

<!--Alternative steps: Use VS Code Docker tools to view ACR images with tags-->

### <a name="troubleshoot"></a>Felsöka

Om det uppstår fel när du skapar och push-pushar modulavbildningen, måste det ofta göra med Docker-konfigurationen på utvecklingsdatorn. Använd följande kontroller för att granska konfigurationen:

* Har du kört kommandot `docker login` med de autentiseringsuppgifter som du kopierade från containerregistret? De här autentiseringsuppgifterna skiljer sig från de som du använder för att logga in på Azure.
* Stämmer containerlagringsplatsen? Har det rätt containerregisternamn och rätt modulnamn? Öppna filen **module.js** SampleModule för att kontrollera den. Lagringsplatsvärdet bör se ut **\<registry name\> som .azurecr.io/samplemodule**.
* Om du använde ett annat namn än **SampleModule** för din modul, är det namnet konsekvent i hela lösningen?
* Kör datorn samma typ av containrar som du skapar? Den här självstudien är för Linux IoT Edge-enheter, så Visual Studio Code bör säga **amd64** eller **arm32v7** i sidofältet och Docker Desktop ska köra Linux-containrar.  

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Du har kontrollerat att de skapade containeravbildningarna lagras i containerregistret, så det är dags att distribuera dem till en enhet. Kontrollera att din IoT Edge är igång.

1. I Visual Studio Code-utforskaren under **avsnittet Azure IoT Hub** **expanderar** du Enheter för att se din lista över IoT-enheter.

2. Högerklicka på den IoT Edge som du vill distribuera till och välj sedan **Skapa distribution för enskild enhet.**

   ![Skapa distribution för en enskild enhet](./media/tutorial-develop-for-linux/create-deployment.png)

3. I Utforskaren navigerar du till **mappen config** och väljer sedan **deployment.amd64.jspå** filen.

   Använd inte den deployment.template.jsfilen, som inte har containerregistrets autentiseringsuppgifter eller modulavbildningsvärden i den. Om du riktar in dig på en Linux ARM32-enhet får distributionsmanifestet namnet deployment.arm32v7.jspå.

4. Under enheten expanderar du **Moduler för** att se en lista över distribuerade och körande moduler. Klicka på uppdateringsknappen. Du bör se de nya modulerna SimulatedTemperatureSensor och SampleModule som körs på enheten.

   Det kan ta några minuter för modulerna att starta. Den IoT Edge måste ta emot sitt nya distributionsmanifest, hämta modulavbildningarna från containerkörningen och sedan starta varje ny modul.

   ![Visa moduler som körs på IoT Edge enhet](./media/tutorial-develop-for-linux/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visa meddelanden från enheten

SampleModule-koden tar emot meddelanden via sin indatakö och skickar dem genom utdatakön. Distributionsmanifestet deklarerade vägar som skickade meddelanden till SampleModule från SimulatedTemperatureSensor och sedan vidarebefordrade meddelanden från SampleModule till IoT Hub. Med Azure IoT-verktygen för Visual Studio Code kan du se meddelanden när de tas emot IoT Hub från dina enskilda enheter.

1. I Visual Studio Code-utforskaren högerklickar du på den IoT Edge som du vill övervaka och väljer sedan Starta övervakning **av inbyggd händelseslutpunkt.**

2. Titta på utdatafönstret i Visual Studio Code för att se meddelanden som kommer till din IoT-hubb.

   ![Visa inkommande enhet till molnmeddelanden](./media/tutorial-develop-for-linux/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visa ändringar på enheten

Om du vill se vad som händer på själva enheten använder du kommandona i det här avsnittet för att granska IoT Edge och moduler som körs på enheten.

Kommandona i det här avsnittet gäller för IoT Edge enhet, inte utvecklingsdatorn. Om du använder en virtuell dator för din IoT Edge ansluter du till den nu. I Azure går du till den virtuella datorns översiktssida och väljer Anslut **för att** få åtkomst till anslutningen till det säkra gränssnittet.

* Visa alla moduler som distribuerats till enheten och kontrollera deras status:

   ```bash
   iotedge list
   ```

   Du bör se fyra moduler: de två IoT Edge runtime-modulerna SimulatedTemperatureSensor och SampleModule. Alla fyra bör visas som körs.

* Granska loggarna för en specifik modul:

   ```bash
   iotedge logs <module name>
   ```

   IoT Edge moduler är fallkänsliga.

   Loggarna SimulatedTemperatureSensor och SampleModule bör visa de meddelanden som de bearbetar. EdgeAgent-modulen ansvarar för att starta de andra modulerna, så dess loggar innehåller information om hur du implementerar distributionsmanifestet. Om någon modul inte visas eller inte körs kommer edgeAgent-loggarna förmodligen att ha felen. EdgeHub-modulen ansvarar för kommunikationen mellan modulerna och IoT Hub. Om modulerna är igång, men meddelandena inte kommer till din IoT-hubb, kommer edgeHub-loggarna förmodligen att ha felen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och De Azure-resurser som du använde i den här artikeln för att undvika kostnader.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerade Visual Studio Code på utvecklingsdatorn och distribuerade din första IoT Edge från den. Nu när du känner till de grundläggande begreppen kan du prova att lägga till funktioner i en modul så att den kan analysera de data som passerar genom den. Välj önskat språk:

> [!div class="nextstepaction"]
> [C](tutorial-c-module.md) 
>  [C#](tutorial-csharp-module.md) 
>  [Java](tutorial-java-module.md) 
>  [Node.js](tutorial-node-module.md) 
>  [Python](tutorial-python-module.md)
