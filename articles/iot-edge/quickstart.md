---
title: Snabbstart för att skapa en Azure IoT Edge-enhet i Windows | Microsoft Docs
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
ms.openlocfilehash: f3af2b7839465f886d1edba01eb9988419761dac
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631087"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device-preview"></a>Snabb start: distribuera din första IoT Edge-modul till en Windows-enhet (förhands granskning)

Prova Azure IoT Edge i den här snabb starten genom att distribuera container kod till en Linux på Windows IoT Edge-enhet. Med IoT Edge kan du fjärrhantera kod på dina enheter så att du kan skicka fler arbets belastningar till gränsen. I den här snabb starten rekommenderar vi att du använder din egen enhet för att se hur enkelt det är att använda Azure IoT Edge för Linux i Windows.

I den här snabbstarten lär du dig att:

* Skapa en IoT-hubb.
* Registrera en IoT Edge-enhet till din IoT Hub.
* Installera och starta IoT Edge för Linux på Windows Runtime på din enhet.
* Distribuera en modul via fjärr anslutning till en IoT Edge enhet och skicka telemetri.

![Diagram – Snabbstart av arkitektur för enhet och moln](./media/quickstart/install-edge-full.png)

Den här snabb starten vägleder dig genom hur du konfigurerar Azure IoT Edge för Linux på Windows-enheter. Sedan distribuerar du en modul från Azure Portal till din enhet. Modulen som används i den här snabb starten är en simulerad sensor som genererar temperatur-, fuktighets-och tryck data. De andra Azure IoT Edge självstudierna bygger på det arbete du gör här genom att distribuera ytterligare moduler som analyserar simulerade data för affärs insikter.

Om du inte har en aktiv Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

>[!NOTE]
>IoT Edge för Linux på Windows finns i en [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Förbered din miljö för Azure CLI.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Molnresurser:

* En resursgrupp som du använder för att hantera alla resurser i den här snabbstarten.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

IoT Edge-enhet:

* Enheten måste vara en Windows-dator eller-server, version 1809 eller senare
* Minst 4 GB minne, rekommenderas 8 GB minne
* 10 GB ledigt diskutrymme

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Starta snabbstarten genom att skapa en IoT-hubb med Azure CLI.

![Diagram – Skapa en IoT-hubb i molnet](./media/quickstart/create-iot-hub.png)

Den kostnadsfria nivån för IoT Hub fungerar för den här snabbstarten. Om du har använt IoT Hub tidigare och redan har skapat en hubb, kan du använda den IoT Hub.

Följande kod skapar en kostnads fri **F1** -hubb i resurs gruppen `IoTEdgeResources` . Ersätt `{hub_name}` med ett unikt namn för din IoT Hub. Det kan ta några minuter att skapa en IoT Hub.

   ```azurecli-interactive
   az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
   ```

   Om du får ett felmeddelande eftersom det redan finns en kostnadsfri hubb i din prenumeration ändrar du SKU till **S1**. Om du får ett felmeddelande om att IoT Hub-namnet inte är tillgängligt innebär det att någon annan redan har en hubb med det namnet. Prova med ett nytt namn.

## <a name="register-an-iot-edge-device"></a>Registrera en IoT Edge-enhet

Registrera en IoT Edge-enhet med IoT-hubben som du nyss skapade.

![Diagram – Registrera en enhet med en IoT Hub-identitet](./media/quickstart/register-device.png)

Skapa en enhetsidentitet för den simulerade enheten så att den kan kommunicera med din IoT Hub. Enhetsidentiteten finns i molnet, och du använder en unik enhetsanslutningssträng för att associera en fysisk enhet med en enhetsidentitet.

Eftersom IoT Edge-enheter fungerar och kan hanteras på annat sätt än typiska IoT-enheter deklarerar du den här identiteten till att höra till en IoT Edge-enhet med flaggan `--edge-enabled`.

1. I Azure Cloud Shell anger du följande kommando för att skapa en enhet med namnet **myEdgeDevice** i hubben.

   ```azurecli-interactive
   az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
   ```

   Om du får ett fel meddelande om princip nycklar för iothubowner kontrollerar du att din Cloud Shell kör den senaste versionen av Azure-IoT-tillägget.

2. Visa anslutnings strängen för enheten, som länkar din fysiska enhet med sin identitet i IoT Hub. Det innehåller namnet på din IoT-hubb, namnet på enheten och sedan en delad nyckel som autentiserar anslutningar mellan de två.

   ```azurecli-interactive
   az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
   ```

3. Kopiera värdet för `connectionString`-nyckeln från JSON-utdata och spara det. Det här värdet är enhetens anslutningssträng. Du ska använda den här anslutningssträngen för att konfigurera IoT Edge-körningen i nästa avsnitt.

   ![Hämta anslutningssträngen från CLI-utdata](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Installera och starta IoT Edge-körningen

Installera IoT Edge för Linux i Windows på din enhet och konfigurera den med en enhets anslutnings sträng.

![Diagram – starta IoT Edge runtime på enheten](./media/quickstart/start-runtime.png)

1. [Hämta Windows administrations Center](https://aka.ms/WACDownloadEFLOW).
2. Följ installations guiden för att konfigurera Windows administrations Center på enheten.
3. När du är i Windows administrations Center väljer du **kugg hjuls ikonen Inställningar** i det övre högra hörnet på skärmen  
4. Från menyn Inställningar, under Gateway, väljer du **tillägg**
5. I listan över **tillgängliga tillägg** väljer du **Azure IoT Edge**
6. **Installera** tillägget

7. När tillägget har installerats går du till sidan för huvud instrument panelen genom att välja **Windows administrations Center** överst till vänster i hörnet på skärmen.

8. Den lokala värd anslutningen visas som representerar den dator där du kör Windows administrations Center.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page.png" alt-text="Skärm bild – start sida för Windows-administratör":::

9. Välj **Lägg till**.

   :::image type="content" source="media/quickstart/windows-admin-center-start-page-add.png" alt-text="Skärm bild – Windows Admin start sida Lägg till knapp":::

10. Leta upp panelen Azure IoT Edge och välj **Skapa ny**. Då startas installations guiden.

    :::image type="content" source="media/quickstart/select-tile-screen.png" alt-text="Skärm bild – Azure IoT Edge för Linux på Windows-panelen":::

11. Fortsätt med installations guiden för att godkänna licens avtalet och välj **Nästa**

    :::image type="content" source="media/quickstart/wizard-welcome-screen.png" alt-text="Skärm bild – Välkommen till guiden":::

12. Välj de **valfria diagnostikdata** för att tillhandahålla utökade diagnostikdata som hjälper Microsoft att övervaka och underhålla tjänst kvalitet och klicka på **Nästa: Distribuera**

    :::image type="content" source="media/quickstart/diagnostic-data-screen.png" alt-text="Skärm bild – diagnostikdata":::

13. På skärmen **Välj mål enhet** väljer du önskad målenhet för att kontrol lera att den uppfyller minimi kraven. I den här snabb starten ska vi installera IoT Edge på den lokala enheten, så Välj localhost-anslutningen. När du har bekräftat väljer du **Nästa** för att fortsätta

    :::image type="content" source="media/quickstart/wizard-select-target-device-screen.png" alt-text="Skärm bild – Välj mål enhet":::

14. Godkänn standardinställningarna genom att välja **Nästa**.

15. På sidan distribution visas processen för att ladda ned paketet, installera paketet, konfigurera värden och slutföra konfigurationen av den virtuella Linux-datorn.  En lyckad distribution kommer att se ut så här:

    :::image type="content" source="media/quickstart/wizard-deploy-success-screen.png" alt-text="Skärm bild – guide distributionen lyckades":::

16. Klicka på **Nästa: Anslut** för att fortsätta till det sista steget för att etablera din Azure IoT Edge-enhet med sitt enhets-ID från din IoT Hub-instans.

17. Kopiera anslutnings strängen från enheten i Azure IoT Hub och klistra in den i fältet enhets anslutnings sträng. Välj sedan **etablering med den valda metoden**.

    > [!NOTE]
    > Se steg 3 i föregående avsnitt, [Registrera en IoT Edge-enhet](#register-an-iot-edge-device)för att hämta anslutnings strängen.

    :::image type="content" source="media/quickstart/wizard-provision.png" alt-text="Skärm bild – guide etablering":::

18. När etableringen är klar väljer du **Slutför** för att slutföra och återgå till Start skärmen i Windows administrations Center. Du bör nu kunna se din enhet i listan som en IoT Edge enhet.

    :::image type="content" source="media/quickstart/windows-admin-center-device-screen.png" alt-text="Skärm bild – Windows administrations Center Azure IoT Edge enhet":::

19. Välj din Azure IoT Edge enhet om du vill visa dess instrument panel. Du bör se att arbets belastningarna från enheten är dubbla i Azure IoT Hub har distribuerats. **Listan IoT Edge modul** ska visa en modul som kör, **edgeAgent** och **IoT Edges status** ska visa **aktiv (körs)**.

IoT Edge-enheten har nu konfigurerats. Den är redo att köra molndistribuerade moduler.

## <a name="deploy-a-module"></a>Distribuera en modul

Hantera din Azure IoT Edge-enhet från molnet för att distribuera en modul som skickar telemetridata till IoT Hub.

![Diagram – Distribuera en modul från ett moln till en enhet](./media/quickstart/deploy-module.png)

[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

## <a name="view-generated-data"></a>Visa genererade data

I den här snabbstarten skapade du en ny IoT Edge-enhet och installerade IoT Edge-körningsmiljön på den. Sedan använde du Azure-portalen för att distribuera en IoT Edge-modul som ska köras på enheten utan att några ändringar behövs göras i själva enheten.

I det här fallet genererar modulen som du har överfört exempel miljö data som du kan använda för testning senare. Den simulerade sensorn övervakar både en dator och miljön runt datorn. Den här sensorn kan till exempel finnas i ett serverrum, på fabriksgolvet eller på en vindturbin. Meddelandet innehåller rumstemperatur och fuktighet, maskintemperatur och tryck samt en tidsstämpel. I IoT Edge-självstudierna används de data som skapas av den här modulen som testdata för analys.

Bekräfta att modulen som distribuerats från molnet körs på din IoT Edge-enhet genom att gå till kommando tolken i Windows administrations Center.

1. Anslut till din nyligen skapade IoT Edge-enhet

   :::image type="content" source="media/quickstart/connect-edge-screen.png" alt-text="Skärm bild – Anslut enhet":::

2. På sidan **Översikt** visas **listan IoT Edge-modul** och **IoT Edge status** där du kan se de olika moduler som har distribuerats samt enhets status.  

3. Under **verktyg** väljer du **kommando gränssnitt**. Kommando gränssnittet är en PowerShell-Terminal som automatiskt använder SSH (Secure Shell) för att ansluta till din Azure IoT Edge enhets virtuella Linux-dator på din Windows-dator.

   :::image type="content" source="media/quickstart/command-shell-screen.png" alt-text="Skärm bild – kommando gränssnitt":::

4. Om du vill kontrol lera de tre modulerna på enheten kör du följande **bash-kommando**:

   ```bash
   sudo iotedge list
   ```

   :::image type="content" source="media/quickstart/iotedge-list-screen.png" alt-text="Skärm bild – Shell-lista för kommandon":::

5. Visa meddelanden som skickas från modulen temperatursensor till molnet.

   ```bash
   iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!TIP]
   >IoT Edge-kommandon är skiftlägeskänsliga när det gäller modulnamnen.

   :::image type="content" source="media/quickstart/temperature-sensor-screen.png" alt-text="Skärm bild – temperatur sensor":::

Du kan också se att meddelandena kommer till din IoT-hubb genom att använda [Azure IoT Hub-tillägget för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta med IoT Edge-självstudierna kan du använda enheten du registrerade och konfigurerade i den här snabbstarten. Annars kan du ta bort de Azure-resurser som du har skapat för att undvika avgifter.

Om du skapade den virtuella datorn och IoT-hubben i en ny resursgrupp kan du ta bort den gruppen och alla associerade resurser. Kontrollera att det inte finns något du vill behålla i innehållet i resursgruppen. Om du inte vill ta bort hela gruppen kan du ta bort enskilda resurser i stället.

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras.

Ta bort gruppen **IoTEdgeResources**. Det kan ta några minuter att ta bort en resurs grupp.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Du kan bekräfta att resurs gruppen tas bort genom att visa listan över resurs grupper.

```azurecli-interactive
az group list
```

### <a name="clean-removal-of-azure-iot-edge-for-linux-on-windows"></a>Borttagning av Azure IoT Edge för Linux i Windows rensas

Du kan avinstallera Azure IoT Edge för Linux på Windows från din IoT Edge-enhet via instrument panels tillägget i Windows administrations Center.

1. Anslut till Azure IoT Edge för Linux på Windows enhets anslutning i Windows administrations Center. Tillägget för Azure Dashboard-verktyget kommer att läsas in.
2. Välj **Avinstallera**. När Azure IoT Edge för Linux på Windows har tagits bort kommer Windows administrations Center att navigera till start sidan och ta bort Azure IoT Edge-enhetens anslutnings post från listan.

Ett annat sätt att ta bort Azure IoT Edge från Windows-systemet är att gå till **Starta**  >  **Inställningar**  >  **appar**  >  **Azure IoT Edge**  >  **Avinstallera** på din IoT Edge enhet. Detta tar bort Azure IoT Edge från IoT Edges enheten, men lämnar anslutningen bakom i Windows administrations Center. Windows administrations Center kan också avinstalleras från menyn Inställningar.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en IoT Edge-enhet och använde molngränssnittet i Azure IoT Edge för att distribuera kod till enheten. Nu har du en testenhet som genererar rådata för sin miljö.

Nästa steg är att konfigurera din lokala utvecklings miljö så att du kan börja skapa IoT Edge moduler som kör din affärs logik.

> [!div class="nextstepaction"]
> [Börja utveckla IoT Edge moduler](tutorial-develop-for-linux.md)
