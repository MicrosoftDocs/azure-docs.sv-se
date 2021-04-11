---
title: Självstudie – utveckla modul för Windows-enheter med Azure IoT Edge
description: Den här självstudien vägleder dig genom att ställa in din utvecklings dator och moln resurser för att utveckla IoT Edge moduler med Windows-behållare för Windows-enheter
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/30/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
monikerRange: =iotedge-2018-06
ms.openlocfilehash: a2d8a215dfc34073783a5d8a6233ec8dbae4ec17
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106218880"
---
# <a name="tutorial-develop-iot-edge-modules-using-windows-containers"></a>Självstudie: utveckla IoT Edge moduler med Windows-behållare

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Använd Visual Studio för att utveckla och distribuera kod till Windows-enheter som kör IoT Edge.

>[!NOTE]
>IoT Edge 1,1 LTS är den sista versions kanal som stöder Windows-behållare. Från och med version 1,2 stöds inte Windows-behållare. Överväg att använda eller flytta till [IoT Edge för Linux i Windows](iot-edge-for-linux-on-windows.md) för att köra IoT Edge på Windows-enheter.

Den här självstudien vägleder dig genom vad det tar att utveckla och distribuera din egen kod till en IoT Edge enhet. Den här självstudien är en användbar förutsättning för de andra självstudierna som går in i detalj om särskilda programmeringsspråk eller Azure-tjänster.

I den här självstudien använder vi exemplet på att distribuera en **C#-modul till en Windows-enhet**. Det här exemplet valdes eftersom det är det vanligaste utvecklings scenariot. Om du är intresse rad av att utveckla på ett annat språk, eller om du planerar att distribuera Azure-tjänster som moduler, kommer den här kursen fortfarande att vara användbar för att lära dig mer om utvecklingsverktyg. När du förstår utvecklings koncepten kan du välja önskat språk eller Azure-tjänst för att få information.

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Konfigurera din utvecklings dator.
> * Använd IoT Edge verktyg för Visual Studio för att skapa ett nytt projekt.
> * Skapa ditt projekt som en behållare och lagra det i ett Azure Container Registry.
> * Distribuera din kod till en IoT Edge enhet.

## <a name="prerequisites"></a>Förutsättningar

En utvecklings dator:

* Windows 10 med 1809 Update eller senare.
* Du kan använda din egen dator eller en virtuell dator, beroende på dina utvecklings inställningar.
  * Kontrol lera att utvecklings datorn stöder kapslad virtualisering. Den här funktionen är nödvändig för att köra en behållar motor som du installerar i nästa avsnitt.
* Installera [git](https://git-scm.com/).

En Azure IoT Edge enhet i Windows:

* [Installera och hantera Azure IoT Edge med Windows-behållare](how-to-install-iot-edge-windows-on-windows.md).
* Vi rekommenderar att du inte kör IoT Edge på din utvecklings dator, utan i stället använder en separat enhet om det är möjligt. Den här skillnaden mellan utvecklings datorn och IoT Edge enheten är mer korrekt speglar ett verkligt distributions scenario och hjälper till att hålla de olika koncepten direkt.

Molnresurser:

* En [IoT-hubb](../iot-hub/iot-hub-create-through-portal.md) (kostnads fri) eller standard nivå i Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="key-concepts"></a>Viktiga begrepp

Den här självstudien vägleder dig genom utvecklingen av en IoT Edge modul. En *IoT Edge modul*, eller ibland bara *modul* för kort, är en behållare som innehåller körbar kod. Du kan distribuera en eller flera moduler till en IoT Edge enhet. Moduler utför vissa uppgifter som att mata in data från sensorer, utföra data analyser eller åtgärder för data rensning eller skicka meddelanden till en IoT-hubb. Mer information finns i [förstå Azure IoT Edge moduler](iot-edge-modules.md).

När du utvecklar IoT Edge moduler är det viktigt att förstå skillnaden mellan utvecklings datorn och mål IoT Edges enheten där modulen slutligen kommer att distribueras. Den behållare som du skapar för att hålla din modul kod måste matcha *mål enhetens* operativ system (OS). För utveckling av Windows-behållare är det här konceptet enklare eftersom Windows-behållare bara körs på Windows-operativsystem. Men du kan till exempel använda Windows Development-datorn för att bygga moduler för Linux IoT Edge-enheter. I det scenariot måste du kontrol lera att din utvecklings dator körde Linux-behållare. När du går igenom den här självstudien bör du tänka på skillnaden mellan *utvecklings datorns operativ system* och *behållar operativ systemet*.

Den här kursen riktar sig till Windows-enheter som kör IoT Edge. Windows IoT Edge-enheter använder Windows-behållare. Vi rekommenderar att du använder Visual Studio för att utveckla för Windows-enheter, så att den här självstudien kommer att använda. Du kan även använda Visual Studio Code även om det finns skillnader i stödet mellan de två verktygen.

I följande tabell visas de utvecklings scenarier som stöds för **Windows-behållare** i Visual Studio Code och Visual Studio.

|   | Visuell Studio-kod | Visual Studio 2017/2019 |
| - | ------------------ | ------------------ |
| **Azure-tjänster** | Azure Functions <br> Azure Stream Analytics |   |
| **Språk** | C# (fel sökning stöds inte) | C <br> C# |
| **Mer information** | [Azure IoT Edge för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) | [Azure IoT Edge verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools)<br>[Azure IoT Edge verktyg för Visual Studio 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) |

## <a name="install-container-engine"></a>Installera container motor

IoT Edge moduler paketeras som behållare, så du behöver en behållar motor på utvecklings datorn för att bygga och hantera behållarna. Vi rekommenderar att du använder Docker Desktop för utveckling på grund av dess många funktioner och popularitet som en behållar motor. Med Docker Desktop på en Windows-dator kan du växla mellan Linux-behållare och Windows-behållare så att du enkelt kan utveckla moduler för olika typer av IoT Edge enheter.

Använd Docker-dokumentationen för att installera på utvecklings datorn:

* [Installera Docker Desktop for Windows](https://docs.docker.com/docker-for-windows/install/)

  * När du installerar Docker Desktop för Windows får du en fråga om du vill använda Linux-eller Windows-behållare. I den här självstudien använder du **Windows-behållare**. Mer information finns i [Växla mellan Windows-och Linux-behållare](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers).

## <a name="set-up-visual-studio-and-tools"></a>Konfigurera Visual Studio och verktyg

IoT-tilläggen för Visual Studio hjälper dig att utveckla IoT Edge moduler. Dessa tillägg tillhandahåller projektmallar, automatiserar skapandet av distributions manifestet och låter dig övervaka och hantera IoT Edge enheter. I det här avsnittet installerar du Visual Studio och IoT Edge-tillägget och konfigurerar sedan ditt Azure-konto för att hantera IoT Hub-resurser i Visual Studio.

I den här självstudien får du lära dig utvecklings stegen för Visual Studio 2019. Om du använder Visual Studio 2017 (version 15,7 eller senare) är stegen liknande. Om du hellre vill använda Visual Studio Code läser du instruktionerna i [använda Visual Studio Code för att utveckla och felsöka moduler för Azure IoT Edge](how-to-vs-code-develop-module.md).

1. Förbered Visual Studio 2019 på din utvecklings dator.

   * Om du inte redan har Visual Studio på utvecklings datorn installerar du [Visual studio 2019](/visualstudio/install/install-visual-studio) med följande arbets belastningar:

      * Azure Development
      * Skriv bords utveckling med C++
      * .NET Core plattformsoberoende utveckling

   * Om du redan har Visual Studio 2019 på utvecklings datorn följer du stegen i [ändra Visual Studio](/visualstudio/install/modify-visual-studio) för att lägga till de nödvändiga arbets belastningarna.

2. Hämta och installera tillägget [Azure IoT Edge-verktyg](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) för Visual Studio 2019.

   Om du använder Visual Studio 2017 (version 15,7 eller senare) kan du hämta och installera [Azure IoT Edge verktyg för Visual Studio 2017](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools).

3. När installationen är klar öppnar du Visual Studio 2019 och väljer **Fortsätt utan kod**.

4. Välj **Visa**  >  **Cloud Explorer**.

5. Välj profil ikonen i Cloud Explorer och logga in på ditt Azure-konto om du inte redan har loggat in.

6. När du har loggat in visas dina Azure-prenumerationer. Expandera prenumerationen som har IoT-hubben.

7. Under prenumerationen expanderar du **IoT** Hub och IoT Hub. Du bör se en lista över dina IoT-enheter och du kan använda den här Utforskaren för att hantera dem.

   ![Åtkomst IoT Hub resurser i Cloud Explorer](./media/tutorial-develop-for-windows/cloud-explorer-view-hub.png)

[!INCLUDE [iot-edge-create-container-registry](../../includes/iot-edge-create-container-registry.md)]

## <a name="create-a-new-module-project"></a>Skapa ett nytt modul-projekt

Tillägget Azure IoT Edge-verktyg innehåller projektmallar för alla språk som stöds IoT Edge modul i Visual Studio. Dessa mallar har alla filer och all kod som du behöver för att distribuera en fungerande modul för att testa IoT Edge, eller ge dig en start punkt för att anpassa mallen med din egen affärs logik.

1. Välj **Arkiv**  >  **nytt**  >  **projekt...**

2. I fönstret nytt projekt söker du efter **IoT Edge** och väljer **Azure IoT Edge-projektet (Windows amd64)** . Klicka på **Nästa**.

   ![Skapa ett nytt Azure IoT Edge-projekt](./media/tutorial-develop-for-windows/new-project.png)

3. I fönstret Konfigurera ditt nya projekt byter du namn på projektet och lösningen till något beskrivande som **CSharpTutorialApp**. Skapa projektet genom att klicka på **skapa** .

   ![Konfigurera ett nytt Azure IoT Edge projekt](./media/tutorial-develop-for-windows/configure-project.png)

4. I fönstret Lägg till modul konfigurerar du ditt projekt med följande värden:

   | Fält | Värde |
   | ----- | ----- |
   | Visual Studio-mall | Välj **C#-modul**. |
   | Modulnamn | Godkänn standard- **IotEdgeModule1**. |
   | URL för databas | En bildlagringsplats innehåller namnet på containerregistret och namnet på containeravbildningen. Behållar avbildningen fylls i automatiskt från värdet för modulens projekt namn. Ersätt **localhost: 5000** med **inloggnings serverns** värde från Azure Container Registry. Du kan hämta inloggnings serverns värde från sidan Översikt i behållar registret i Azure Portal. <br><br> Den slutliga avbildnings lagrings platsen ser ut som \<registry name\> . azurecr.io/iotedgemodule1. |

      ![Konfigurera ditt projekt för mål enheten, modultypen och behållar registret](./media/tutorial-develop-for-windows/add-module-to-solution.png)

5. Välj **Lägg till** för att skapa modulen.

När ditt nya projekt har lästs in i Visual Studio-fönstret kan du bekanta dig med de filer som har skapats:

* Ett IoT Edge-projekt med namnet **CSharpTutorialApp**.
  * Mappen **moduler** innehåller pekare till de moduler som ingår i projektet. I det här fallet bör det bara vara IotEdgeModule1.
  * Den dolda **. miljö** filen innehåller autentiseringsuppgifterna till behållar registret. Autentiseringsuppgifterna delas med din IoT Edge-enhet så att de har åtkomst till att hämta behållar avbildningarna.
  * **deployment.template.js** filen är en mall som hjälper dig att skapa ett distributions manifest. Ett *distributions manifest* är en fil som definierar exakt vilka moduler som du vill distribuera på en enhet, hur de ska konfigureras och hur de kan kommunicera med varandra och molnet.
    > [!TIP]
    > I avsnittet autentiseringsuppgifter för registret fylls adressen till från den information som du angav när du skapade lösningen. Variablerna användar namn och lösen ord är dock lagrade i. miljö-filen. Detta är av säkerhets nivå, eftersom. miljö filen är git, men distributions mal len inte är det.
* Ett IoT Edge module-projekt med namnet **IotEdgeModule1**.
  * Filen **program. cs** innehåller den C#-modul kod som medföljer projekt mal len. Standardmodulen tar emot inmatade objekt från en källa och skickar den vidare till IoT Hub.
  * **module.jspå** fil innehåller information om modulen, inklusive den fullständiga avbildnings lagrings platsen, avbildnings versionen och vilken Dockerfile som ska användas för varje plattform som stöds.

### <a name="provide-your-registry-credentials-to-the-iot-edge-agent"></a>Ange autentiseringsuppgifterna för registret för IoT Edge agenten

Den IoT Edge körningen behöver dina autentiseringsuppgifter för att hämta behållar avbildningarna till IoT Edges enheten. IoT Edge-tillägget försöker hämta information om behållar registret från Azure och fylla det i distributions mal len.

1. Öppna filen **deployment.template.js** i din modul-lösning.

1. Hitta egenskapen **registryCredentials** i önskade egenskaper för $edgeAgent. Den bör ha en ifylld registrerings adress från den information som du angav när du skapade projektet, och fälten username och Password måste innehålla variabel namn. Exempel:

   ```json
   "registryCredentials": {
     "<registry name>": {
       "username": "$CONTAINER_REGISTRY_USERNAME_<registry name>",
       "password": "$CONTAINER_REGISTRY_PASSWORD_<registry name>",
       "address": "<registry name>.azurecr.io"
     }
   }
   ```

1. Öppna **. kuvert** -filen i din modul-lösning. (Den är dold som standard i Solution Explorer, så du kan behöva välja knappen **Visa alla filer** för att visa den.)

1. Lägg till värdena för **användar namn** och **lösen ord** som du kopierade från Azure Container Registry.

1. Spara ändringarna i. kuvert-filen.

### <a name="review-the-sample-code"></a>Granska exempel koden

I lösnings mal len som du skapade ingår exempel kod för en IoT Edge-modul. Den här exempel modulen tar bara emot meddelanden och skickar dem vidare. Pipeline-funktionen visar ett viktigt begrepp i IoT Edge, vilket är hur moduler kommunicerar med varandra.

Varje modul kan ha flera *indata* -och *utmatnings* köer som deklareras i koden. IoT Edge hubben som körs på enheten dirigerar meddelanden från utdata från en modul till indata för en eller flera moduler. Den speciella koden för att deklarera indata och utdata varierar mellan olika språk, men begreppet är detsamma i alla moduler. Mer information om routning mellan moduler finns i [deklarera vägar](module-composition.md#declare-routes).

C#-koden som medföljer projekt mal len använder [klassen ModuleClient](/dotnet/api/microsoft.azure.devices.client.moduleclient) från IoT Hub SDK för .net.

1. Leta reda på **SetInputMessageHandlerAsync** -metoden i filen **program. cs** .

2. Metoden [SetInputMessageHandlerAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.setinputmessagehandlerasync) konfigurerar en inkommande kö för att ta emot inkommande meddelanden. Granska den här metoden och se hur den initierar en indatakö med namnet **INPUT1**.

   ![Hitta indatamängden i SetInputMessageHandlserAsync-konstruktorn](./media/tutorial-develop-for-windows/declare-input-queue.png)

3. Leta sedan reda på **SendEventAsync** -metoden.

4. [SendEventAsync](/dotnet/api/microsoft.azure.devices.client.moduleclient.sendeventasync) -metoden bearbetar mottagna meddelanden och konfigurerar en utgående kö för att skicka dem vidare. Granska den här metoden och se att den initierar en utgående kö med namnet **output1**.

   ![Hitta utdatafilens namn i SendEventAsync-konstruktorn](./media/tutorial-develop-for-windows/declare-output-queue.png)

5. Öppna filen **deployment.template.js** .

6. Hitta egenskapen **modules** för de $edgeAgent önskade egenskaperna.

   Det bör finnas två moduler som visas här. En är **SimulatedTemperatureSensor** -modulen som ingår i alla mallar som standard för att tillhandahålla simulerade temperatur data som du kan använda för att testa dina moduler. Den andra är **IotEdgeModule1** -modulen som du skapade som en del av det här projektet.

   Med den här modulen deklaras vilka moduler som ska ingå i distributionen till enheten eller enheterna.

7. Hitta egenskapen **vägar** för de $edgeHub önskade egenskaperna.

   En av funktionerna i modulen IoT Edge Hub är att dirigera meddelanden mellan alla moduler i en distribution. Granska värdena i egenskapen routes. En väg, **IotEdgeModule1ToIoTHub**, använder ett jokertecken (* *\** _) för att inkludera alla meddelanden som kommer från en utgående kö i IotEdgeModule1-modulen. Dessa meddelanden hamnar i _ $ Stream *, vilket är ett reserverat namn som visar IoT Hub. Den andra vägen, **sensorToIotEdgeModule1**, tar meddelanden från SimulatedTemperatureSensor-modulen och dirigerar dem till *INPUT1* -indatakö i IotEdgeModule1-modulen.

   ![Granska vägar i deployment.template.jspå](./media/tutorial-develop-for-windows/deployment-routes.png)

## <a name="build-and-push-your-solution"></a>Skapa och push-överföra lösningen

Du har granskat modulens kod och distributions mal len för att förstå några viktiga distributions koncept. Nu är du redo att bygga IotEdgeModule1 behållar avbildning och push-överför den till behållar registret. Med IoT tools-tillägget för Visual Studio genererar det här steget även distributions manifestet baserat på informationen i mallfilen och modulens information från lösningsfiler.

### <a name="sign-in-to-docker"></a>Logga in på Docker

Ange dina autentiseringsuppgifter för behållar registret till Docker på din utvecklings dator så att den kan push-överföra din behållar avbildning till att lagras i registret.

1. Öppna PowerShell eller en kommando tolk.

2. Logga in på Docker med de autentiseringsuppgifter för Azure Container Registry som du sparade när du skapade registret.

   ```cmd
   docker login -u <ACR username> -p <ACR password> <ACR login server>
   ```

   Du kan få en säkerhets varning som rekommenderar att du använder `--password-stdin` . Det bästa tillvägagångs sättet rekommenderas för produktions scenarier, men det ligger utanför omfånget för den här självstudien. Mer information finns i [inloggnings referens för Docker](https://docs.docker.com/engine/reference/commandline/login/#provide-a-password-using-stdin) .

### <a name="build-and-push"></a>Bygg och skicka

Nu har din utvecklings dator åtkomst till ditt behållar register, och dina IoT Edge enheter är också. Det är dags att omvandla projekt koden till en behållar avbildning.

1. Högerklicka på **CSharpTutorialApp** -projektmappen och välj Bygg- **och push-IoT Edge moduler**.

   ![Bygga och push-IoT Edge moduler](./media/tutorial-develop-for-windows/build-and-push-modules.png)

   Kommandot build och push startar tre åtgärder. Först skapar den en ny mapp i lösningen som heter **config** och som innehåller det fullständiga distributions manifestet, och bygger ut information i distributions mal len och andra filer i lösningen. Sedan körs den `docker build` för att bygga behållar avbildningen baserat på lämpliga Dockerfile för din mål arkitektur. Sedan körs den `docker push` för att skicka avbildnings lagrings platsen till behållar registret.

   Den här processen kan ta flera minuter första gången, men går snabbare nästa gång du kör kommandona.

2. Öppna filen **deployment.windows-amd64.jspå** filen i den nyligen skapade mappen config. (Config-mappen kanske inte visas i Solution Explorer i Visual Studio. Om så är fallet väljer du ikonen **Visa alla filer** i aktivitets fältet Solution Explorer.)

3. Hitta parametern **image** i avsnittet IotEdgeModule1. Observera att avbildningen innehåller den fullständiga avbildnings lagrings platsen med taggen namn, version och arkitektur från module.jsi filen.

4. Öppna filen **module.js** i mappen IotEdgeModule1

5. Ändra versions numret för modulens bild. (Versionen, inte $schema-versionen.) Du kan till exempel öka korrigerings versions numret till **0.0.2** som om vi hade gjort en liten korrigering i modulens kod.

   >[!TIP]
   >Med versioner av versioner aktiverar du versions kontroll och du kan testa ändringar på en liten uppsättning enheter innan du distribuerar uppdateringar till produktion. Om du inte ökar modulens version innan du skapar och skickar, skriver du över lagrings platsen i behållar registret.

6. Spara ändringarna i module.jspå filen.

7. Högerklicka på mappen **CSharpTutorialApp** igen och välj **Build och push IoT Edge modules** igen.

8. Öppna **deployment.windows-amd64.js** filen igen. Observera att en ny fil inte skapades när du körde kommandot build och push igen. I stället har samma fil uppdaterats för att återspegla ändringarna. IotEdgeModule1-avbildningen pekar nu mot 0.0.2-versionen av behållaren. Den här ändringen i distributions manifestet är hur du meddelar IoT Edge-enheten att det finns en ny version av en modul att hämta.

9. För att ytterligare kontrol lera vad build och push-kommandot gjorde går du till [Azure Portal](https://portal.azure.com) och navigerar till behållar registret.

10. I behållar registret väljer du **databaser** och sedan **iotedgemodule1**. Kontrol lera att båda versionerna av avbildningen push-överfördes till registret.

    ![Visa båda avbildnings versionerna i behållar registret](./media/tutorial-develop-for-windows/view-repository-versions.png)

### <a name="troubleshoot"></a>Felsöka

Om du stöter på fel när du skapar och skickar en modultyp måste det ofta göras med Docker-konfiguration på din utvecklings dator. Använd följande kontroller för att granska konfigurationen:

* Körde du `docker login` kommandot med de autentiseringsuppgifter som du kopierade från behållar registret? De här autentiseringsuppgifterna skiljer sig från de som du använder för att logga in på Azure.
* Stämmer containerlagringsplatsen? Har du rätt namn på behållar registret och rätt Modulnamn? Öppna filen **module.js** i mappen IotEdgeModule1 för att kontrol lera. Värdet för lagring bör se ut som **\<registry name\> . azurecr.io/iotedgemodule1**.
* Om du har använt ett annat namn än **IotEdgeModule1** för modulen, är det namnet konsekvent i lösningen?
* Kör datorn samma typ av behållare som du skapar? Den här självstudien gäller för Windows IoT Edge-enheter, så dina Visual Studio-filer bör ha **Windows-amd64-** tillägget och Docker-skrivbordet ska köra Windows-behållare.

## <a name="deploy-modules-to-device"></a>Distribuera moduler till enhet

Du verifierade att de inbyggda behållar avbildningarna lagras i behållar registret, så det är dags att distribuera dem till en enhet. Kontrol lera att din IoT Edges enhet är igång.

1. Öppna Cloud Explorer i Visual Studio och expandera informationen för din IoT Hub.

2. Välj namnet på den enhet som du vill distribuera till. I listan **åtgärder** väljer du **skapa distribution**.

   ![Skapa distribution för en enskild enhet](./media/tutorial-develop-for-windows/create-deployment.png)

3. I Utforskaren navigerar du till mappen config i projektet och väljer filen **deployment.windows-amd64.js** . Den här filen finns ofta på `C:\Users\<username>\source\repos\CSharpTutorialApp\CSharpTutorialApp\config\deployment.windows-amd64.json`

   Använd inte deployment.template.jspå filen, som inte har de fullständiga värdena för modulens avbildning.

4. Expandera informationen för din IoT Edge-enhet i Cloud Explorer för att se modulerna på enheten.

5. Använd knappen **Uppdatera** för att uppdatera enhetens status för att se att SimulatedTemperatureSensor-och IotEdgeModule1-modulerna har distribuerats till enheten.

   ![Visa moduler som körs på din IoT Edge-enhet](./media/tutorial-develop-for-windows/view-running-modules.png)

## <a name="view-messages-from-device"></a>Visa meddelanden från enheten

IotEdgeModule1-koden tar emot meddelanden via sin indatakö och skickar dem tillsammans med dess utgående kö. Distributions manifestet deklarerade vägar som skickade meddelanden från SimulatedTemperatureSensor till IotEdgeModule1 och vidarebefordrade sedan meddelanden från IotEdgeModule1 till IoT Hub. Med Azure IoT Edge verktyg för Visual Studio kan du se meddelanden när de tas emot på IoT Hub från dina enskilda enheter.

1. I Visual Studio Cloud Explorer väljer du namnet på den IoT Edge enhet som du distribuerade till.

2. I menyn **åtgärder** väljer du **starta övervakning inbyggd händelse slut punkt**.

3. Titta på avsnittet **utdata** i Visual Studio för att se meddelanden som kommer till din IoT Hub.

   Det kan ta några minuter för båda modulerna att starta. Den IoT Edge körnings miljön måste ta emot sitt nya distributions manifest, Hämta modulens avbildningar från container körningen och sedan starta varje ny modul.

   ![Visa inkommande enhet till moln meddelanden](./media/tutorial-develop-for-windows/view-d2c-messages.png)

## <a name="view-changes-on-device"></a>Visa ändringar på enheten

Om du vill se vad som händer på själva enheten kan du använda kommandona i det här avsnittet för att granska IoT Edge körning och moduler som körs på enheten.

Kommandona i det här avsnittet gäller för din IoT Edge-enhet, inte din utvecklings dator. Om du använder en virtuell dator för din IoT Edge-enhet ansluter du till den nu. I Azure går du till sidan Översikt för den virtuella datorn och väljer **Anslut** för att få åtkomst till fjärr skrivbords anslutningen. Öppna ett kommando-eller PowerShell-fönster för att köra `iotedge` kommandona på enheten.

* Visa alla moduler som har distribuerats till enheten och kontrol lera deras status:

   ```cmd
   iotedge list
   ```

   Du bör se fyra moduler: de två IoT Edge runtime-modulerna, SimulatedTemperatureSensor och IotEdgeModule1. Alla fyra ska visas som körs.

* Granska loggarna för en speciell modul:

   ```cmd
   iotedge logs <module name>
   ```

   IoT Edge moduler är Skift läges känsliga.

   SimulatedTemperatureSensor-och IotEdgeModule1-loggarna bör visa de meddelanden som bearbetas. EdgeAgent-modulen ansvarar för att starta de andra modulerna, så dess loggar har information om att implementera distributions manifestet. Om någon modul inte finns med i listan eller inte körs, kommer edgeAgent-loggarna förmodligen att ha felen. EdgeHub-modulen ansvarar för kommunikation mellan modulerna och IoT Hub. Om modulerna är igång, men meddelandena inte kommer till din IoT-hubb, kommer edgeHub-loggarna förmodligen att ha felen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta med nästa rekommenderade artikel kan du behålla de resurser och konfigurationer som du skapat och använda dem igen. Du kan även fortsätta att använda samma IoT Edge-enhet som en testenhet.

Annars kan du ta bort de lokala konfigurationerna och de Azure-resurser som du använde i den här artikeln för att undvika avgifter.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien konfigurerar du Visual Studio 2019 på utvecklings datorn och distribuerar din första IoT Edge modul från den. Nu när du känner till de grundläggande begreppen kan du försöka lägga till funktioner i en modul så att den kan analysera data som passerar genom den. Välj önskat språk:

> [!div class="nextstepaction"]
> [C](tutorial-c-module-windows.md) 
>  [C#](tutorial-csharp-module-windows.md)