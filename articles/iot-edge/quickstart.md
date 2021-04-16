---
title: Snabbstart för att skapa en Azure IoT Edge på Windows-| Microsoft Docs
description: I den här snabbstarten får du lära dig att skapa en IoT Edge-enhet och sedan fjärrdistribuera färdig kod från Azure Portal.
author: rsameser
manager: kgremban
ms.author: riameser
ms.date: 01/20/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 9f0562d4471ac1129bf9bc7ecfee058cddac7c61
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533131"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Snabbstart: Distribuera din första IoT Edge till en Windows-enhet (förhandsversion)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Prova att Azure IoT Edge den här snabbstarten genom att distribuera containerkod till en Linux på Windows IoT Edge enhet. IoT Edge kan du fjärrhantera kod på dina enheter så att du kan skicka fler av dina arbetsbelastningar till gränsen. För den här snabbstarten rekommenderar vi att du använder din egen enhet för att se hur enkelt det är att Azure IoT Edge för Linux på Windows.

I den här snabbstarten lär du dig att:

* Skapa en IoT-hubb.
* Registrera en IoT Edge-enhet till din IoT Hub.
* Installera och starta IoT Edge Linux på Windows-körningen på enheten.
* Fjärr distribuera en modul till en IoT Edge enhet och skicka telemetri.

![Diagram som visar arkitekturen för den här snabbstarten för din enhet och molnet.](./media/quickstart/install-edge-full.png)

Den här snabbstarten går igenom hur du operativsystemet Azure IoT Edge Linux på Windows-enheter. Sedan distribuerar du en modul från Azure Portal till din enhet. Den modul som du använder är en simulerad sensor som genererar temperatur-, fuktighets- och tryckdata. Andra Azure IoT Edge självstudier bygger på det arbete du gör här genom att distribuera moduler som analyserar simulerade data för affärsinsikter.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

>[!NOTE]
>IoT Edge för Linux i Windows finns i [offentlig förhandsversion](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Skapa en molnresursgrupp för att hantera alla resurser som du ska använda i den här snabbstarten.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Kontrollera att IoT Edge enheten uppfyller följande krav:

* Utgåvor
  * Windows 10 version 1809 eller senare; version 17763 eller senare
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 version 17763 eller senare

* Maskinvarukrav
  * Minsta lediga minne: 1 GB
  * Minsta lediga diskutrymme: 10 GB

>[!NOTE]
>Den här snabbstarten använder Windows Admin Center för att skapa en distribution IoT Edge för Linux i Windows. Du kan också använda PowerShell. Om du vill använda PowerShell för att skapa distributionen följer du stegen i instruktionerna för att installera och etablera Azure IoT Edge för Linux på [en Windows-enhet.](how-to-install-iot-edge-on-windows.md)

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Börja med att skapa en IoT-hubb med Azure CLI.

![Diagram som visar steget för att skapa en I o T-hubb.](./media/quickstart/create-iot-hub.png)

Den kostnadsfria nivån av Azure IoT Hub fungerar för den här snabbstarten. Om du har använt IoT Hub tidigare och redan har skapat en hubb kan du använda den IoT-hubben.

Följande kod skapar en kostnadsfri **F1-hubb** i resursgruppen `IoTEdgeResources` . Ersätt `{hub_name}` med ett unikt namn för din IoT-hubb. Det kan ta några minuter att skapa en IoT-hubb.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Om du får ett felmeddelande eftersom du redan har en kostnadsfri hubb i din prenumeration ändrar du SKU:n till `S1` . Om du får ett felmeddelande om att IoT-hubbnamnet inte är tillgängligt har någon annan redan en hubb med det namnet. Prova med ett nytt namn.

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med IoT-hubben som du nyss skapade.

![Diagram som visar steget för att registrera en enhet med en IoT Hub-identitet.](./media/quickstart/register-device.png)

Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT Hub. Enhetsidentiteten finns i molnet, och du använder en unik enhetsanslutningssträng för att associera en fysisk enhet med en enhetsidentitet.

IoT Edge beter sig och kan hanteras på ett annat sätt än vanliga IoT-enheter. Använd flaggan `--edge-enabled` för att deklarera att den här identiteten är för en IoT Edge enhet.

1. I Azure Cloud Shell du följande kommando för att skapa en enhet med namnet **myEdgeDevice** i hubben.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Om du får ett felmeddelande om principnycklar kontrollerar du `iothubowner` att Cloud Shell kör den senaste versionen av Azure IoT-tillägget.

1. Visa anslutningssträngen för enheten, som länkar den fysiska enheten med dess identitet i IoT Hub. Den innehåller namnet på din IoT-hubb, namnet på din enhet och en delad nyckel som autentiserar anslutningar mellan dem.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Kopiera värdet för `connectionString`-nyckeln från JSON-utdata och spara det. Det här värdet är enhetens anslutningssträng. Du använder den för att konfigurera IoT Edge körning i nästa avsnitt.

     ![Skärmbild som visar connectionString-utdata i Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera IoT Edge För Linux på Windows på enheten och konfigurera den med enhetens anslutningssträng.

![Diagram som visar steget för att IoT Edge körningen.](./media/quickstart/start-runtime.png)

1. [Ladda ned Windows Admin Center](https://aka.ms/wacdownload).

1. Följ anvisningarna i installationsguiden för att konfigurera Windows Admin Center på enheten.

1. Öppna Windows Admin Center.

1. Välj **kugghjulsikonen** inställningar i det övre högra hörnet och välj sedan **Tillägg.**

1. På fliken **Feeds** väljer du Lägg **till**.

1. Ange `https://aka.ms/wac-insiders-feed` i textrutan och välj sedan Lägg **till**.

1. När feeden har lagts till går du till **fliken Tillgängliga tillägg** och väntar tills listan över tillägg har uppdaterats.

1. I listan över **Tillgängliga tillägg väljer** du **Azure IoT Edge**.

1. Installera tillägget.

1. När tillägget har installerats väljer du **Windows Admin Center** i det övre vänstra hörnet för att gå till huvudsidan för instrumentpanelen.

     **LocalHost-anslutningen** representerar den dator där du kör Windows Admin Center.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Skärmbild av startsidan för Windows-administratör.":::

1. Välj **Lägg till**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Skärmbild som visar hur du väljer knappen Lägg till i Windows Admin Center.":::

1. På Azure IoT Edge väljer du Skapa **ny för** att starta installationsguiden.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Skärmbild som visar hur du skapar en ny distribution Azure IoT Edge til.":::

1. Fortsätt med installationsguiden för att acceptera Licensvillkor för Programvara från Microsoft och välj sedan **Nästa.**

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Skärmbild som visar hur du väljer Nästa för att fortsätta med installationsguiden.":::

1. Välj **Valfria diagnostikdata** och välj sedan **Nästa: Distribuera**. Det här valet ger utökade diagnostikdata som hjälper Microsoft att övervaka och upprätthålla tjänstkvaliteten.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Skärmbild som visar alternativen för diagnostikdata.":::

1. På skärmen **Välj målenhet** väljer du önskad målenhet för att verifiera att den uppfyller minimikraven. För den här snabbstarten installerar vi IoT Edge på den lokala enheten, så välj **localhost-anslutningen.** Om målenheten uppfyller kraven väljer du **Nästa för** att fortsätta.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Skärmbild som visar listan Målenhet.":::

1. Välj **Nästa** för att acceptera standardinställningarna. Distributionsskärmen visar processen för att ladda ned paketet, installera paketet, konfigurera värden och slutföra konfigurationen av den virtuella Linux-datorn (VM). En lyckad distribution ser ut så här:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Skärmbild av en lyckad distribution.":::

1. Välj **Nästa: Anslut för** att fortsätta till det sista steget för att etablera Azure IoT Edge enhet med dess enhets-ID från din IoT Hub-instans.

1. Klistra in anslutningssträngen som du [kopierade tidigare i den här snabbstarten](#register-an-iot-edge-device) i **fältet Enhetsanslutningssträng.** Välj sedan **Etablering med den valda metoden**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Skärmbild som visar anslutningssträngen i fältet Enhetsanslutningssträng.":::

1. När etableringen är klar väljer du **Slutför för** att slutföra och återgår till Windows Admin Center startskärmen. Du bör se din enhet som en IoT Edge enhet.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Skärmbild som visar alla anslutningar i Windows Admin Center.":::

1. Välj din Azure IoT Edge för att visa dess instrumentpanel. Du bör se att arbetsbelastningarna från enhetstvillingen i Azure IoT Hub har distribuerats. Listan **IoT Edge modulen ska** visa en modul som kör **edgeAgent**, och **IoT Edge status** ska vara aktiv **(körs).**

IoT Edge-enheten har nu konfigurerats. Den är redo att köra molndistribuerade moduler.

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som skickar telemetridata till IoT Hub.

![Diagram som visar steget för att distribuera en modul.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

En av de viktigaste funktionerna i Azure IoT Edge är att distribuera kod till IoT Edge enheter från molnet. *IoT Edge moduler är* körbara paket som implementeras som containrar. I det här avsnittet distribuerar du en förbyggd modul från [avsnittet IoT Edge Modules i Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) direkt från Azure IoT Hub.

Den modul som du distribuerar i det här avsnittet simulerar en sensor och skickar genererade data. Modulen är användbar kod när du kör igång med IoT Edge eftersom du kan använda simulerade data för utveckling och testning. Om du vill se exakt vad den här modulen gör kan du visa [källkoden för den simulerade temperatursensorn](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Följ de här stegen för att distribuera din första modul från Azure Marketplace.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din IoT-hubb.

1. I menyn till vänster, under **Automatisk Enhetshantering** väljer du **IoT Edge**.

1. Välj målenhetens enhets-ID i listan över enheter.

1. Välj Ange moduler i det **övre fältet.**

   ![Skärmbild som visar val av Ange moduler.](./media/quickstart/select-set-modules.png)

1. Under **IoT Edge Modules**(Moduler) öppnar du **listrutan** Lägg till och väljer sedan **Marketplace-modul**.

   ![Skärmbild som visar den nedrullningsna menyn Lägg till.](./media/quickstart/add-marketplace-module.png)

1. I **IoT Edge-modulen Marketplace** söker du efter och väljer `Simulated Temperature Sensor` modulen.

   Modulen läggs till i avsnittet IoT Edge Modules med önskad **körningsstatus.**

1. Välj **Nästa: Vägar** för att fortsätta till nästa steg i guiden.

   ![Skärmbild som visar hur du fortsätter till nästa steg efter att modulen har lagts till.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. På fliken **Vägar** tar du bort standardvägen, dirigerar och väljer sedan **Nästa: Granska +** skapa för att fortsätta till nästa steg i guiden.

   >[!Note]
   >Vägar konstrueras med hjälp av namn- och värdepar. Du bör se två vägar på den här sidan. Standardvägen, **route**, skickar alla meddelanden till IoT Hub (som kallas `$upstream` ). En andra väg, **SimulatedTemperatureSensorToIoTHub,** skapades automatiskt när du lade till modulen från Azure Marketplace. Den här vägen skickar alla meddelanden från modulen för simulerad temperatur till IoT Hub. Du kan ta bort standardvägen eftersom den är redundant i det här fallet.

   ![Skärmbild som visar hur du tar bort standardvägen och sedan går vidare till nästa steg.](./media/quickstart/delete-route-next-review-create.png)

1. Granska JSON-filen och välj sedan **Skapa.** JSON-filen definierar alla moduler som du distribuerar till din IoT Edge enhet. Du ser modulen **SimulatedTemperatureSensor och** de två körningsmodulerna **edgeAgent** och **edgeHub.**

   >[!Note]
   >När du skickar en ny distribution till en IoT Edge-enhet distribueras ingenting till enheten. I stället frågar enheten regelbundet IoT Hub efter nya instruktioner. Om enheten hittar ett uppdaterat distributionsmanifest använder den informationen om den nya distributionen för att hämta modulavbildningarna från molnet och börjar sedan köra modulerna lokalt. Den här processen kan ta några minuter.

1. När du har skapat information om moduldistributionen återgår guiden till sidan med enhetsinformation. Visa distributionsstatusen på **fliken** Moduler.

   Du bör se tre moduler: **$edgeAgent**, **$edgeHub** och **SimulatedTemperatureSensor**. Om en eller flera av modulerna har **YES (JA)** under **SPECIFIED IN DEPLOYMENT** (ANGES I DISTRIBUTION) men inte under REPORTED BY **DEVICE**(RAPPORTERAS AV ENHET) IoT Edge enheten fortfarande startar dem. Vänta några minuter och uppdatera sedan sidan.

   ![Skärmbild som visar Simulerad temperatursensor i listan över distribuerade moduler.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>Visa genererade data

I den här snabbstarten skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure Portal för att distribuera en IoT Edge-modul som ska köras på enheten utan att behöva göra ändringar på själva enheten.

Modulen som du push-pushade genererar exempelmiljödata som du kan använda för testning senare. Den simulerade sensorn övervakar både en dator och miljön runt datorn. Den här sensorn kan till exempel finnas i ett serverrum, på fabriksgolvet eller på en vindturbin. Meddelandena som skickas är omgivande temperatur och luftfuktighet, maskintemperatur och tryck samt en tidsstämpel. IoT Edge självstudier använder de data som skapats av den här modulen som testdata för analys.

Från kommandogränssnittet i Windows Admin Center du att modulen som du distribuerade från molnet körs på din IoT Edge enhet.

1. Anslut till din nyligen IoT Edge enhet.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Skärmbild som visar hur du väljer Anslut i Windows Admin Center.":::

     På sidan **Översikt** visas listan över IoT Edge **och IoT Edge** **Status**. Du kan se de moduler som har distribuerats och enhetens status.  

1. Under **Verktyg** väljer du **Kommandogränssnitt.** Kommandogränssnittet är en PowerShell-terminal som automatiskt använder Secure Shell (SSH) för att ansluta till din Azure IoT Edge virtuella Linux-dator på din Windows-dator.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Skärmbild som visar hur du öppnar kommandogränssnittet.":::

1. Kontrollera de tre modulerna på enheten genom att köra följande Bash-kommando:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Skärmbild som visar kommandogränssnittet I o T Edge-listutdata.":::

1. Visa meddelanden som skickas från modulen temperatursensor till molnet.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge kommandon är fallkänsliga när de refererar till modulnamn.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Skärmbild som visar listan över meddelanden som skickas från modulen till molnet.":::

Du kan också använda tillägget [Azure IoT Hub för att Visual Studio Code för](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) att se när meddelanden kommer till din IoT-hubb.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med självstudierna IoT Edge du hoppa över det här steget. Du kan använda den enhet som du registrerade och konfigurerade i den här snabbstarten. Annars kan du ta bort de Azure-resurser som du skapade för att undvika kostnader.

Om du skapade den virtuella datorn och IoT-hubben i en ny resursgrupp kan du ta bort den gruppen och alla associerade resurser. Om du inte vill ta bort hela gruppen kan du ta bort enskilda resurser i stället.

> [!IMPORTANT]
> Kontrollera innehållet i resursgruppen för att se till att det inte finns något som du vill behålla. Att ta bort en resursgrupp kan inte ångras.

Använd följande kommando för att ta bort **gruppen IoTEdgeResources.** Borttagningen kan ta några minuter.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Du kan bekräfta att resursgruppen har tagits bort med hjälp av det här kommandot för att visa listan över resursgrupper.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Ta Azure IoT Edge för Linux i Windows

Använd instrumentpanelstillägget i Windows Admin Center för att avinstallera Azure IoT Edge för Linux i Windows.

1. Anslut till IoT Edge i Windows Admin Center. Verktygstillägget för Azure-instrumentpanelen läses in.

1. Välj **Avinstallera**. När Azure IoT Edge har tagits Windows Admin Center bort Azure IoT Edge enhetsanslutningsposten från **startsidan.**

>[!Note]
>Ett annat sätt att Azure IoT Edge från Windows-systemet är att välja **Starta** inställningar  >    >  **Appar**  >  **Azure IoT Edge**  >  **Avinstallera** på din IoT Edge enhet. Den här metoden tar Azure IoT Edge från IoT Edge enhet, men lämnar anslutningen kvar i Windows Admin Center. Slutför borttagningen genom att Windows Admin Center från **menyn** Inställningar.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en IoT Edge-enhet och använde molngränssnittet i Azure IoT Edge för att distribuera kod till enheten. Nu har du en testenhet som genererar rådata om miljön.

Konfigurera sedan din lokala utvecklingsmiljö så att du kan börja skapa IoT Edge moduler som kör din affärslogik.

> [!div class="nextstepaction"]
> [Börja utveckla IoT Edge moduler](tutorial-develop-for-linux.md)
