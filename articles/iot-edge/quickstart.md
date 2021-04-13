---
title: Snabb start för att skapa en Azure IoT Edge enhet i Windows | Microsoft Docs
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
ms.openlocfilehash: de24f6c8436b4537519f8cc65931325dd7d5f8d9
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107313393"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Snabb start: distribuera din första IoT Edge-modul till en Windows-enhet (förhands granskning)

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Prova Azure IoT Edge i den här snabb starten genom att distribuera container kod till en Linux på Windows IoT Edge-enhet. Med IoT Edge kan du fjärrhantera kod på dina enheter så att du kan skicka fler arbets belastningar till gränsen. I den här snabb starten rekommenderar vi att du använder din egen enhet för att se hur enkelt det är att använda Azure IoT Edge för Linux i Windows.

I den här snabbstarten lär du dig att:

* Skapa en IoT-hubb.
* Registrera en IoT Edge-enhet till din IoT Hub.
* Installera och starta IoT Edge för Linux på Windows Runtime på din enhet.
* Distribuera en modul via fjärr anslutning till en IoT Edge enhet och skicka telemetri.

![Diagram som visar arkitekturen för den här snabb starten för enheten och molnet.](./media/quickstart/install-edge-full.png)

Den här snabb starten vägleder dig genom hur du konfigurerar Azure IoT Edge för Linux på Windows-enheter. Sedan distribuerar du en modul från Azure Portal till din enhet. Modulen som du ska använda är en simulerad sensor som genererar temperatur-, fuktighets-och tryck data. Andra Azure IoT Edge själv studie kurser som bygger på det arbete du gör här genom att distribuera moduler som analyserar simulerade data för affärs insikter.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

>[!NOTE]
>IoT Edge för Linux på Windows finns i en [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Skapa en moln resurs grupp för att hantera alla resurser du kommer att använda i den här snabb starten.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Kontrol lera att din IoT Edge enhet uppfyller följande krav:

* Utgåvor
  * Windows 10 version 1809 eller senare; Build 17763 eller senare
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019 build 17763 eller senare

* Maskinvarukrav
  * Minsta lediga minne: 2 GB
  * Minsta lediga disk utrymme: 10 GB

>[!NOTE]
>I den här snabb starten används Windows administrations Center för att skapa en distribution av IoT Edge för Linux i Windows. Du kan också använda PowerShell. Om du vill använda PowerShell för att skapa en distribution följer du stegen i instruktions guiden för att [Installera och etablering Azure IoT Edge för Linux på en Windows-enhet](how-to-install-iot-edge-on-windows.md).

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Börja med att skapa en IoT-hubb med Azure CLI.

![Diagram som visar steget för att skapa ett T o T-nav.](./media/quickstart/create-iot-hub.png)

Den kostnads fria nivån av Azure-IoT Hub fungerar för den här snabb starten. Om du har använt IoT Hub tidigare och redan har skapat en hubb, kan du använda den IoT Hub.

Följande kod skapar en kostnads fri **F1** -hubb i resurs gruppen `IoTEdgeResources` . Ersätt `{hub_name}` med ett unikt namn för din IoT Hub. Det kan ta några minuter att skapa en IoT-hubb.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Om du får ett fel meddelande eftersom du redan har en ledig hubb i prenumerationen ändrar du SKU till `S1` . Om du får ett fel meddelande om att namnet på IoT Hub inte är tillgängligt har någon annan redan en hubb med det namnet. Prova med ett nytt namn.

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med IoT-hubben som du nyss skapade.

![Diagram som visar steget för att registrera en enhet med en IoT Hub-identitet.](./media/quickstart/register-device.png)

Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT Hub. Enhetsidentiteten finns i molnet, och du använder en unik enhetsanslutningssträng för att associera en fysisk enhet med en enhetsidentitet.

IoT Edge enheter beter sig och kan hanteras på ett annat sätt än vanliga IoT-enheter. Använd `--edge-enabled` flaggan för att deklarera att denna identitet är för en IoT Edge enhet.

1. I Azure Cloud Shell anger du följande kommando för att skapa en enhet med namnet **myEdgeDevice** i hubben.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Om du får ett fel meddelande om `iothubowner` princip nycklar kontrollerar du att Cloud Shell kör den senaste versionen av Azure IoT-tillägget.

1. Visa anslutnings strängen för enheten, som länkar din fysiska enhet med sin identitet i IoT Hub. Det innehåller namnet på din IoT-hubb, namnet på enheten och en delad nyckel som autentiserar anslutningar mellan de två.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Kopiera värdet för `connectionString`-nyckeln från JSON-utdata och spara det. Det här värdet är enhetens anslutningssträng. Du använder den för att konfigurera IoT Edge runtime i nästa avsnitt.

     ![Skärm bild som visar connectionString-utdata i Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera IoT Edge för Linux i Windows på din enhet och konfigurera den med enhets anslutnings strängen.

![Diagram som visar steget för att starta IoT Edge Runtime.](./media/quickstart/start-runtime.png)

1. [Hämta Windows administrations Center](https://aka.ms/wacdownload).

1. Följ anvisningarna i installations guiden för att konfigurera Windows administrations Center på enheten.

1. Öppna administrations Center för Windows.

1. Välj **kugg hjuls** ikonen för inställningar i det övre högra hörnet och välj sedan **tillägg**.

1. På fliken **feeds** väljer du **Lägg till**.

1. Ange `https://aka.ms/wac-insiders-feed` i text rutan och välj sedan **Lägg till**.

1. När du har lagt till feeden går du till fliken **tillgängliga tillägg** och väntar på att tilläggs listan ska uppdateras.

1. I listan över **tillgängliga tillägg** väljer du **Azure IoT Edge**.

1. Installera tillägget.

1. När tillägget är installerat väljer du **Windows administrations Center** i det övre vänstra hörnet för att gå till huvud instrument panels sidan.

     **Localhost** -anslutningen representerar den dator där du kör Windows administrations Center.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Skärm bild av start sidan för Windows-administratören.":::

1. Välj **Lägg till**.

     :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Skärm bild som visar att du väljer knappen Lägg till i Windows administrations Center.":::

1. På panelen Azure IoT Edge väljer du **Skapa nytt** för att starta installations guiden.

     :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Skärm bild som visar hur du skapar en ny distribution i Azure IoT Edge fönstret.":::

1. Fortsätt genom installations guiden för att godkänna licens villkoren för program vara från Microsoft och välj sedan **Nästa**.

     :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Skärm bild som visar Välj nästa för att fortsätta med installations guiden.":::

1. Välj **valfria diagnostikdata** och välj sedan **Nästa: Distribuera**. Det här valet tillhandahåller utökade diagnostikdata som hjälper Microsoft att övervaka och underhålla tjänst kvalitet.

     :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Skärm bild som visar alternativen för diagnostikdata.":::

1. På skärmen **Välj mål enhet** väljer du önskad målenhet för att kontrol lera att den uppfyller minimi kraven. I den här snabb starten ska vi installera IoT Edge på den lokala enheten, så välj **localhost** -anslutningen. Om mål enheten uppfyller kraven väljer du **Nästa** för att fortsätta.

     :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Skärm bild som visar listan över mål enheter.":::

1. Välj **Nästa** för att godkänna standardinställningarna. På sidan distribution visas processen för att ladda ned paketet, installera paketet, konfigurera värden och slutföra konfigurationen av den virtuella Linux-datorn (VM). En lyckad distribution ser ut så här:

     :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Skärm bild av en lyckad distribution.":::

1. Välj **Nästa: Anslut** för att fortsätta till det sista steget för att etablera Azure IoT Edge-enheten med sitt enhets-ID från din IoT Hub-instans.

1. Klistra in anslutnings strängen som du kopierade [tidigare i den här snabb](#register-an-iot-edge-device) starten till fältet **enhets anslutnings sträng** . Välj sedan **etablering med den valda metoden**.

     :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Skärm bild som visar anslutnings strängen i fältet enhets anslutnings sträng.":::

1. När etableringen är klar väljer du **Slutför** för att slutföra och återgår till Start skärmen i Windows administrations Center. Enheten visas som en IoT Edge enhet.

     :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Skärm bild som visar alla anslutningar i Windows administrations Center.":::

1. Välj din Azure IoT Edge enhet om du vill visa dess instrument panel. Du bör se att arbets belastningarna från enheten är dubbla i Azure IoT Hub har distribuerats. **Listan IoT Edge modul** ska visa en modul som kör **edgeAgent** och **IoT Edges status** ska vara **aktiv (körs)**.

IoT Edge-enheten har nu konfigurerats. Den är redo att köra molndistribuerade moduler.

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som skickar telemetridata till IoT Hub.

![Diagram som visar steget för att distribuera en modul.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

En av de viktigaste funktionerna i Azure IoT Edge är att distribuera kod till dina IoT Edge-enheter från molnet. *IoT Edge moduler* är körbara paket som implementeras som behållare. I det här avsnittet ska du distribuera en fördefinierad modul från [avsnittet IoT Edge moduler i Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) direkt från Azure IoT Hub.

Den modul som du distribuerar i det här avsnittet simulerar en sensor och skickar genererade data. Modulen är användbar kod när du kör igång med IoT Edge eftersom du kan använda simulerade data för utveckling och testning. Om du vill se exakt vad den här modulen gör kan du visa [källkoden för den simulerade temperatursensorn](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Följ de här stegen för att distribuera din första modul från Azure Marketplace.

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till din IoT-hubb.

1. Välj **IoT Edge** under **Automatisk enhets hantering** på menyn till vänster.

1. Välj enhets-ID för mål enheten i listan med enheter.

1. I den övre stapeln väljer du **Ange moduler**.

   ![Skärm bild som visar att du väljer Ange moduler.](./media/quickstart/select-set-modules.png)

1. Öppna den nedrullningsbara menyn **Lägg till** under **IoT Edge moduler** och välj sedan **Marketplace-modul**.

   ![Skärm bild som visar den nedrullningsbara menyn Lägg till.](./media/quickstart/add-marketplace-module.png)

1. I **IoT Edge module Marketplace** söker du efter och väljer `Simulated Temperature Sensor` modulen.

   Modulen läggs till i avsnittet IoT Edge moduler med önskad **körnings** status.

1. Välj **Nästa: vägar** för att fortsätta till nästa steg i guiden.

   ![Skärm bild som visar fortsätter till nästa steg när modulen har lagts till.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. På fliken **vägar** tar du bort standard vägen, **dirigerar** och väljer sedan **Nästa: granska + skapa** för att fortsätta till nästa steg i guiden.

   >[!Note]
   >Vägar konstrueras med hjälp av namn-och värdepar. Du bör se två vägar på den här sidan. Standard vägen, **Route**, skickar alla meddelanden till IoT Hub (som kallas `$upstream` ). En andra väg, **SimulatedTemperatureSensorToIoTHub**, skapades automatiskt när du lade till modulen från Azure Marketplace. Den här vägen skickar alla meddelanden från modulen simulerad temperatur till IoT Hub. Du kan ta bort standard vägen eftersom den är redundant i det här fallet.

   ![Skärm bild som visar borttagning av standard väg och flyttas sedan till nästa steg.](./media/quickstart/delete-route-next-review-create.png)

1. Granska JSON-filen och välj sedan **skapa**. JSON-filen definierar alla moduler som du distribuerar till din IoT Edge-enhet. Du ser **SimulatedTemperatureSensor** -modulen och de två modulerna för körning, **edgeAgent** och **edgeHub**.

   >[!Note]
   >När du skickar en ny distribution till en IoT Edge-enhet distribueras ingenting till enheten. I stället frågar enheten regelbundet IoT Hub efter nya instruktioner. Om enheten hittar ett uppdaterat distributionsmanifest använder den informationen om den nya distributionen för att hämta modulavbildningarna från molnet och börjar sedan köra modulerna lokalt. Den här processen kan ta några minuter.

1. När du har skapat modulens distributions information återgår guiden till enhets informations sidan. Visa distributions status på fliken **moduler** .

   Du bör se tre moduler: **$edgeAgent**, **$edgeHub** och **SimulatedTemperatureSensor**. Om en eller flera av modulerna har värdet **Ja** under **anges i distributionen** , men inte under **rapporteras av enheten**, startar IoT Edge-enheten fortfarande. Vänta några minuter och uppdatera sedan sidan.

   ![Skärm bild som visar simulerad temperatur sensor i listan över distribuerade moduler.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>Visa genererade data

I den här snabbstarten skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure Portal för att distribuera en IoT Edge modul som ska köras på enheten utan att behöva göra några ändringar i själva enheten.

Den modul som du har överfört genererar exempel miljö data som du kan använda för testning senare. Den simulerade sensorn övervakar både en dator och miljön runt datorn. Den här sensorn kan till exempel finnas i ett serverrum, på fabriksgolvet eller på en vindturbin. Meddelandena som det skickar inkluderar omgivande temperatur och fuktighet, maskin temperatur och tryck och en tidsstämpel. IoT Edge självstudier använder data som skapats i den här modulen som test data för analys.

Från kommando gränssnittet i Windows administrations Center bekräftar du att modulen som du har distribuerat från molnet körs på din IoT Edge-enhet.

1. Anslut till din nyligen skapade IoT Edge-enhet.

     :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Skärm bild som visar Välj Anslut i Windows administrations Center.":::

     På sidan **Översikt** ser du **listan IoT Edge-modul** och **IoT Edge status**. Du kan se de moduler som har distribuerats och enhetens status.  

1. Under **verktyg** väljer du **kommando gränssnitt**. Kommando gränssnittet är en PowerShell-Terminal som automatiskt använder Secure Shell (SSH) för att ansluta till din Azure IoT Edge enhets virtuella Linux-dator på din Windows-dator.

     :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Skärm bild som visar öppning av kommando gränssnittet.":::

1. Om du vill kontrol lera de tre modulerna på enheten kör du följande bash-kommando:

     ```bash
     sudo iotedge list
     ```

    :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Skärm bild som visar utdata från kommando gränssnittet I o T Edge-listan.":::

1. Visa meddelanden som skickas från modulen temperatursensor till molnet.

     ```bash
     iotedge logs SimulatedTemperatureSensor -f
     ```

    >[!Important]
    >IoT Edge-kommandon är Skift läges känsliga när de refererar till namn på moduler.

    :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Skärm bild som visar en lista med meddelanden som skickas från modulen till molnet.":::

Du kan också använda [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) för att se meddelanden kommer till din IoT-hubb.

## <a name="clean-up-resources"></a>Rensa resurser

Hoppa över det här steget om du vill fortsätta med självstudierna för IoT Edge. Du kan använda den enhet som du har registrerat och konfigurerat i den här snabb starten. Annars kan du ta bort de Azure-resurser som du har skapat för att undvika avgifter.

Om du skapade den virtuella datorn och IoT-hubben i en ny resursgrupp kan du ta bort den gruppen och alla associerade resurser. Om du inte vill ta bort hela gruppen kan du ta bort enskilda resurser i stället.

> [!IMPORTANT]
> Kontrol lera innehållet i resurs gruppen för att se till att det inte finns något som du vill behålla. Att ta bort en resursgrupp kan inte ångras.

Använd följande kommando för att ta bort **IoTEdgeResources** -gruppen. Borttagningen kan ta några minuter.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Du kan bekräfta att resurs gruppen tas bort med hjälp av det här kommandot för att visa listan över resurs grupper.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Ta bort Azure IoT Edge för Linux i Windows

Använd tillägget instrument panel i Windows administrations Center för att avinstallera Azure IoT Edge för Linux i Windows.

1. Anslut till IoT Edge-enheten i administrations Center för Windows. Tillägget för Azure Dashboard Tool läses in.

1. Välj **Avinstallera**. När Azure IoT Edge har tagits bort tar Windows administrations Center bort Azure IoT Edge enhetens anslutnings post från **Start** sidan.

>[!Note]
>Ett annat sätt att ta bort Azure IoT Edge från Windows-systemet är att välja **Starta**  >  **Inställningar**  >  **appar**  >  **Azure IoT Edge**  >  **Avinstallera** på din IoT Edge enhet. Den här metoden tar bort Azure IoT Edge från IoT Edges enheten, men lämnar anslutningen bakom i Windows administrations Center. Om du vill slutföra borttagningen avinstallerar du Windows administrations Center från menyn **Inställningar** .

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en IoT Edge-enhet och använde molngränssnittet i Azure IoT Edge för att distribuera kod till enheten. Nu har du en testen het som genererar rå data om dess miljö.

Konfigurera sedan din lokala utvecklings miljö så att du kan börja skapa IoT Edge moduler som kör din affärs logik.

> [!div class="nextstepaction"]
> [Börja utveckla IoT Edge moduler](tutorial-develop-for-linux.md)
