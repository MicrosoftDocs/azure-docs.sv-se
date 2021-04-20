---
title: Självstudie – Konfigurera och använda mått och loggar med en Azure IoT-hubb
description: Självstudie – Lär dig att konfigurera och använda mått och loggar med en Azure IoT-hubb. Detta ger data att analysera för att diagnostisera problem som din hubb kan ha.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: 62958dc374598e6f530af398f722001e5ed51acd
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739700"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Självstudie: Konfigurera och använda mått och loggar med en IoT-hubb

Du kan använda Azure Monitor för att samla in mått och loggar för din IoT-hubb som kan hjälpa dig att övervaka driften av din lösning och felsöka problem när de inträffar. I den här artikeln får du se hur du skapar diagram baserat på mått, hur du skapar aviseringar som utlöses för mått, hur du skickar IoT Hub-åtgärder och -fel till Azure Monitor-loggar och hur du kontrollerar om det finns fel i loggarna.

I den här självstudien används Azure-exemplet från [snabbstarten .NET Send telemetry](quickstart-send-telemetry-dotnet.md) (Skicka telemetri) för att skicka meddelanden till IoT Hub. Du kan alltid använda en enhet eller ett annat exempel för att skicka meddelanden, men du kan behöva ändra några steg därefter.

Viss kunskap om Azure Monitor kan vara användbart innan du påbörjar den här självstudien. Mer information finns i [Övervaka IoT Hub](monitor-iot-hub.md). Mer information om mått och resursloggar som genereras av IoT Hub finns i [Referens för övervakningsdata.](monitor-iot-hub-reference.md)

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
>
> * Använd Azure CLI för att skapa en IoT-hubb, registrera en simulerad enhet och skapa en Log Analytics-arbetsyta.  
> * Skicka IoT Hub och resursloggar för enhettelemetri till Azure Monitor loggar på Log Analytics-arbetsytan.
> * Använd Metric Explorer för att skapa ett diagram baserat på valda mått och fäst det på instrumentpanelen.
> * Skapa måttaviseringar så att du kan meddelas via e-post när viktiga villkor inträffar.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till IoT-hubben.
> * Visa aviseringarna när dina villkor inträffar.
> * Visa måttdiagrammet på instrumentpanelen.
> * Visa IoT Hub fel och åtgärder i Azure Monitor loggar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Du behöver .NET Core SDK 2.1 eller högre på utvecklingsdatorn. Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://www.microsoft.com/net/download/all).

  Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

  ```cmd/sh
  dotnet --version
  ```

- Ett e-postkonto som kan ta emot e-post.

- Kontrollera att port 8883 är öppen i brandväggen. Enhetsexempel i den här självstudien använder MQTT-protokollet, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags- och utbildningsnätverksmiljöer. Mer information och sätt att komma runt det här problemet finns i [Ansluta till IoT Hub (MQTT).](iot-hub-mqtt-support.md#connecting-to-iot-hub)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Konfigurera resurser

I den här självstudien behöver du en IoT-hubb, en Log Analytics-arbetsyta och en simulerad IoT-enhet. De här resurserna kan skapas med Azure CLI eller Azure PowerShell. Använd samma resursgrupp och plats för alla resurser. När du är klar med självstudien kan du ta bort allt i ett steg genom att ta bort resursgruppen.

Här är de nödvändiga stegen.

1. Skapa en [resursgrupp](../azure-resource-manager/management/overview.md).

2. Skapa en IoT-hubb.

3. Skapa en Log Analytics-arbetsyta.

4. Registrera en enhetsidentitet för den simulerade enheten som skickar meddelanden till din IoT-hubb. Spara enhetsanslutningssträngen som ska användas för att konfigurera den simulerade enheten.

### <a name="set-up-resources-using-azure-cli"></a>Konfigurera resurser med hjälp av Azure CLI

Kopiera och klistra in det här skriptet i Cloud Shell. Skriptet körs en rad i taget, förutsatt att du redan är inloggad. Vissa kommandon kan ta lite tid att köra. De nya resurserna skapas i resursgruppen *ContosoResources*.

Namnet på vissa resurser måste vara unikt i Azure. Skriptet genererar ett slumpmässigt värde med `$RANDOM` funktionen och lagrar det i en variabel. För dessa resurser lägger skriptet till det här slumpmässiga värdet i ett basnamn för resursen, vilket gör resursnamnet unikt.

Endast en kostnadsfri IoT-hubb tillåts per prenumeration. Om du redan har en kostnadsfri IoT-hubb i din prenumeration kan du ta bort den innan du kör skriptet eller ändra skriptet så att det använder din kostnadsfria IoT-hubb eller en IoT Hub som använder standard- eller basic-nivån.

Skriptet skriver ut namnet på IoT-hubben, namnet på Log Analytics-arbetsytan och anslutningssträngen för den enhet som den registrerar. Kom ihåg att anteckna dessa eftersom du behöver dem senare i den här artikeln.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>När du skapar enhetsidentiteten kan du få följande fel: Inga nycklar hittades för principen *iothubowner för IoT Hub ContosoTestHub*. Åtgärda felet genom att uppdatera IoT-tillägget för Azure CLI och kör de senaste två kommandona i skriptet igen. 
>
>Här är kommandot för att uppdatera tillägget. Kör det här kommandot i din Cloud Shell instans.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Samla in loggar för anslutningar och enhettelemetri

IoT Hub sändar resursloggar för flera åtgärdskategorier. Men om du vill visa loggarna måste du skapa en diagnostikinställning för att skicka dem till ett mål. Ett sådant mål är Azure Monitor loggar som samlas in på en Log Analytics-arbetsyta. IoT Hub resursloggar grupperas i olika kategorier. Du kan välja vilka kategorier du vill skicka till Azure Monitor loggar i diagnostikinställningen. I den här artikeln samlar vi in loggar för åtgärder och fel som inträffar som har att göra med anslutningar och enhettelemetri. En fullständig lista över de kategorier som stöds för IoT Hub finns i [IoT Hub resursloggar](monitor-iot-hub-reference.md#resource-logs).

Följ dessa steg om du vill skapa en diagnostikinställning IoT Hub skicka loggar Azure Monitor till Azure Monitor loggar:

1. Om du inte redan är på hubben i  portalen väljer du Först Resursgrupper och sedan resursgruppen ContosoResources. Välj din IoT-hubb i listan över resurser som visas.

1. Leta upp **övervakningsavsnittet** på IoT Hub-bladet. Välj **Diagnostikinställningar.** Välj sedan **Lägg till diagnostikinställning.**

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Skärmbild som visar diagnostikinställningar i avsnittet Övervakning.":::

1. I fönstret **Diagnostikinställning** ger du inställningen ett beskrivande namn, till exempel "Skicka anslutningar och telemetri till loggar".

1. Under **Kategoriinformation** väljer du **Anslutningar** och **enhettelemetri.**

1. Under **Målinformation** väljer du **Skicka till Log Analytics** och använder sedan Log Analytics-arbetsyteväljaren för att välja den arbetsyta som du antecknade tidigare. När du är klar bör diagnostikinställningen se ut ungefär som på följande skärmbild:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Skärmbild som visar de slutliga diagnostiklogginställningarna.":::

1. Spara inställningarna genom att klicka på **Spara**. Stäng fönstret **Diagnostikinställning.** Du kan se den nya inställningen i listan över diagnostikinställningar.

## <a name="set-up-metrics"></a>Konfigurera mått

Nu ska vi använda Metrics Explorer för att skapa ett diagram som visar mått som du vill spåra. Du fäster det här diagrammet på standardinstrumentpanelen i Azure Portal.

1. I den vänstra rutan i din IoT Hub väljer **du Mått** i **avsnittet** Övervakning.

1. Längst upp på skärmen väljer du **Senaste 24 timmarna (automatiskt).** I listrutan som visas väljer du Senaste **4 timmarna** för Tidsintervall, anger Tidskornighet till **1** minut och väljer **Lokal** för Visa tid **som**.  Spara **inställningarna genom** att välja Tillämpa. Inställningen bör nu vara **Lokal tid: Senaste 4 timmarna (1 minut)**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Skärmbild som visar tidsinställningar för mått.":::

1. I diagrammet visas en partiell måttinställning som är begränsad till din IoT-hubb. Lämna värdena **för** **Omfång och Namnområde** för mått på standardvärdena. Välj inställningen **Mått** och skriv "Telemetri" och välj sedan **Telemetrimeddelanden som skickas** i listrutan. **Aggregering** ställs automatiskt in på **Summa**. Observera att diagrammets rubrik också ändras.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Skärmbild som visar tillägg av måttet Skickade telemetrimeddelanden i diagrammet.":::

1. Välj nu **Lägg till mått** för att lägga till ytterligare ett mått i diagrammet. Under **du Mått** väljer du **Total number of messages used** (Totalt antal använda meddelanden). **Aggregering** ställs automatiskt in **på Genomsnitt.** Observera återigen att diagrammets rubrik har ändrats för att inkludera det här måttet.

   Nu visar skärmen minimerade mått för *Telemetry messages sent* (Skickade telemetrimeddelanden), plus det nya måttet för *Total number of messages used* (Totalt antal använda meddelanden).

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Skärmbild som visar hur du lägger till måttet Totalt antal meddelanden som används i diagrammet.":::

1. Välj Fäst på instrumentpanelen uppe till **höger i diagrammet.**

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Skärmbild som visar knappen Fäst på instrumentpanelen.":::

1. I fönstret **Fäst på instrumentpanelen** väljer du **fliken** Befintlig. Välj **Privat** och sedan **Instrumentpanel** i listrutan Instrumentpanel. Välj slutligen Fäst **för** att fästa diagrammet på standardinstrumentpanelen i Azure Portal. Om du inte fäster diagrammet på en instrumentpanel behålls inte inställningarna när du avslutar Metric Explorer.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Skärmbild som visar inställningar för Fäst på instrumentpanelen.":::

## <a name="set-up-metric-alerts"></a>Konfigurera måttaviseringar

Nu ska vi konfigurera aviseringar som ska utlösas för två mått *Telemetrimeddelanden som skickats* och *Totalt antal meddelanden som används.*

*Skickade telemetrimeddelanden är* ett bra mått för att övervaka att spåra meddelandedataflöde och undvika att begränsas. För en IoT Hub på den kostnadsfria nivån är begränsningsgränsen 100 meddelanden per sekund. Med en enda enhet kommer vi inte att kunna uppnå den typen av dataflöde, så i stället ställer vi in aviseringen så att den utlöses om antalet meddelanden överskrider 1 000 under en 5-minutersperiod. I produktion kan du ställa in signalen på ett mer betydande värde baserat på nivå, utgåva och antal enheter i din IoT-hubb.

*Totalt antal meddelanden som används* spårar det dagliga antalet meddelanden som används. Det här måttet återställs varje dag kl. 00:00 UTC. Om du överskrider din dagliga kvot efter ett visst tröskelvärde kommer IoT Hub inte längre att acceptera meddelanden. För ett IoT Hub på den kostnadsfria nivån är den dagliga meddelandekvoten 8 000. Vi ställer in aviseringen så att den utlöses om det totala antalet meddelanden överskrider 4 000, 50 % av kvoten. I praktiken skulle du förmodligen ange den här procentandelen till ett högre värde. Det dagliga kvotvärdet beror på nivå, utgåva och antal enheter för din IoT-hubb.

Mer information om kvot- och begränsningsgränser med IoT Hub finns [i Kvoter och begränsning.](iot-hub-devguide-quotas-throttling.md)

Så här ställer du in måttaviseringar:

1. Gå till din IoT-hubb i Azure Portal.

1. Under **Övervakning** väljer du **Aviseringar.** Välj sedan **Ny aviseringsregel**.  Fönstret **Skapa aviseringsregel** öppnas.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Skärmbild som visar fönstret Skapa aviseringsregel.":::

    Det finns **fyra avsnitt i** fönstret Skapa aviseringsregel:

    * **Omfånget** är redan inställt på din IoT-hubb, så vi låter det här avsnittet vara kvar.
    * **Villkor** anger den signal och de villkor som utlöser aviseringen.
    * **Åtgärder** konfigurerar vad som händer när aviseringen utlöses.
    * **Med information om** aviseringsregeln kan du ange ett namn och en beskrivning för aviseringen.

1. Konfigurera först villkoret som aviseringen ska utlösas på.

    1. Under **Villkor** väljer du Lägg **till villkor**. I fönstret **Konfigurera signallogik** skriver du "telemetri" i sökrutan och väljer **Telemetrimeddelanden som skickats.**

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Skärmbild som visar val av mått.":::

    1. I fönstret **Konfigurera signallogik** anger eller bekräftar du följande fält under **Aviseringslogik** (du kan ignorera diagrammet):

       **Tröskelvärde:** *Statisk .*

       **Operator:** *Större än*.

       **Sammansättningstyp:** *Totalt*.

       **Tröskelvärde:** 1000.

       **Sammansättningskornighet (period):** *5 minuter*.

       **Utvärderingsfrekvens:** *var 1 minut*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Skärmbild som visar inställningar för aviseringsvillkor.":::

       De här inställningarna anger att signalen ska summera antalet meddelanden under en period på 5 minuter. Den här summan utvärderas varje minut och om totalsumman för de föregående 5 minuterna överskrider 1 000 meddelanden utlöses aviseringen.

       Välj **Klar** för att spara signallogiken.

1. Konfigurera nu åtgärden för aviseringen.

    1. I fönstret **Skapa aviseringsregel** går du till **Åtgärder och** väljer Lägg till **åtgärdsgrupper.** I fönstret **Välj en åtgärdsgrupp att ansluta till den här aviseringsregeln** väljer **du Skapa åtgärdsgrupp.**

    1. Under fliken **Grundläggande inställningar** i fönstret **Skapa åtgärdsgrupp** ger du åtgärdsgruppen ett namn och ett visningsnamn.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Skärmbild som visar fliken Grundläggande inställningar i fönstret Skapa åtgärdsgrupp.":::

    1. Välj **fliken** Meddelanden. Som **Meddelandetyp** väljer du **E-post/SMS-meddelande/Push/röst** i listrutan. Fönstret **E-post/SMS-meddelande/Push/röst** öppnas.

    1. I fönstret **E-post/SMS/Push/röst** väljer du e-post och anger din e-postadress. Välj sedan **OK.**

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Skärmbild som visar inställningen för e-postadress.":::

    1. I fönstret **Meddelanden** anger du ett namn för meddelandet.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Skärmbild som visar det slutförda meddelandefönstret.":::

    1. (Valfritt) Om du väljer **fliken Åtgärder** och  sedan väljer listrutan Åtgärdstyp kan du se vilka typer av åtgärder som du kan utlösa med en avisering. I den här artikeln använder vi bara meddelanden, så du kan ignorera inställningarna på den här fliken.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Skärmbild som visar de åtgärdstyper som är tillgängliga i fönstret Åtgärder.":::

    1. Välj fliken **Granska och skapa,** verifiera inställningarna och välj **Skapa.**

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Skärmbild som visar fönstret Granska och Skapa.":::

    1. I fönstret **Skapa aviseringsregel** ser du att den nya åtgärdsgruppen har lagts till i åtgärderna för aviseringen.  

1. Slutligen konfigurerar du aviseringsregelns information och sparar aviseringsregeln.

    1. Ange ett **namn och en** beskrivning för aviseringen under Information om aviseringsregel i fönstret Skapa aviseringsregel. till exempel "Avisering om mer än 1 000 meddelanden över 5 minuter". Kontrollera att Aktivera **aviseringsregel när den skapas** är markerat. Din slutförda aviseringsregel ser ut ungefär som på den här skärmbilden.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Skärmbild som visar det färdiga fönstret Skapa aviseringsregel.":::

    1. Välj **Skapa aviseringsregel** för att spara den nya regeln.

1. Ställ nu in en ny avisering för de *sammanlagda antalet använda meddelanden*. Det här måttet är användbart om du vill skicka en avisering när antalet meddelanden som används närmar sig den dagliga kvoten för IoT-hubben. Då börjar IoT-hubben avvisa meddelanden. Följ de steg som du gjorde tidigare, med följande skillnader.

    * För signalen i fönstret **Konfigurera signallogik** väljer du **Totalt antal meddelanden som används.**

    * I fönstret **Konfigurera signallogik** anger eller bekräftar du följande fält (du kan ignorera diagrammet):

       **Tröskelvärde:** *Statisk .*

       **Operator:** *Större än*.

       **Sammansättningstyp:** *Maximalt*.

       **Tröskelvärde:** 4000.

       **Sammansättningskornighet (period):** *1 minut*.

       **Utvärderingsfrekvens:** *var 1 minut*

       De här inställningarna anger att signalen ska skjutas när antalet meddelanden når 4 000. Måttet utvärderas varje minut.

    * När du anger åtgärden för aviseringsregeln väljer du bara den åtgärdsgrupp som du skapade tidigare.

    * För aviseringsinformationen väljer du ett annat namn och en annan beskrivning än du gjorde tidigare.

1. Välj **Aviseringar** under **Övervakning i** den vänstra rutan i din IoT-hubb. Välj nu **Hantera aviseringsregler** på menyn längst upp i **fönstret Aviseringar.** Fönstret **Regler** öppnas. Nu bör du se dina två aviseringar:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Skärmbild som visar fönstret Regler med de nya aviseringsreglerna.":::

1. Stäng **fönstret** Regler.

Med de här inställningarna utlöses en avisering och du får ett e-postmeddelande när fler än 1 000 meddelanden skickas inom ett tidsintervall på 5 minuter och även när det totala antalet meddelanden som används överskrider 4 000 (50 % av den dagliga kvoten för en IoT-hubb på den kostnadsfria nivån).

## <a name="run-the-simulated-device-app"></a>Köra den simulerade enhetsappen

I avsnittet [Konfigurera resurser registrerade du](#set-up-resources) en enhetsidentitet som ska användas för att simulera med hjälp av en IoT-enhet. I det här avsnittet laddar du ned en .NET-konsolapp som simulerar en enhet som skickar "enhet till molnet"-meddelanden till en IoT Hub, konfigurerar den för att skicka dessa meddelanden till din IoT-hubb och kör den sedan.

> [!IMPORTANT]
>
> Det kan ta upp till 10 minuter innan aviseringarna har konfigurerats fullständigt och aktiverats av IoT Hub. Vänta minst 10 minuter mellan den tidpunkt då du konfigurerade din senaste avisering och kör den simulerade enhetsappen.

Ladda ned lösningen för [IoT-enhetssimuleringen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Den här länken laddar ned en lagringsplatsen med flera program i den. det du letar efter finns i iot-hub/Quickstarts/simulated-device/.

1. Navigera till lösningens rotmapp i ett lokalt terminalfönster. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.cs** i en valfri textredigerare.

    1. Ersätt värdet för variabeln `s_connectionString` med enhetsanslutningssträngen som du antecknade när du körde skriptet för att konfigurera resurser.

    1. I metoden ändrar du från 1 000 till 1, vilket minskar tiden mellan att skicka meddelanden från 1 sekund till `SendDeviceToCloudMessagesAsync` `Task.Delay` 0,001 sekunder. När du förkortar den här fördröjningen ökar antalet meddelanden som skickas. (Du får förmodligen inte en meddelandefrekvens på 100 meddelanden per sekund.)

        ```csharp
        await Task.Delay(1);
        ```

    1. Spara ändringarna i **SimulatedDevice.cs.**

1. I det lokala terminalfönstret kör du följande kommando för att installera de paket som krävs för det simulerade enhetsprogrammet:

    ```cmd/sh
    dotnet restore
    ```

1. Kör programmet för simulerad enhet genom att skapa och köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet run
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Skärmbild som visar simulerade enhetsutdata.":::

Låt programmet köras i minst 10–15 minuter. Vi rekommenderar att den körs tills den slutar skicka meddelanden (cirka 20–30 minuter). Detta inträffar när du har överskridit den dagliga meddelandekvoten för din IoT-hubb och den har slutat acceptera fler meddelanden.

> [!NOTE]
> Om du lämnar enhetsappen igång under en längre tid efter att den har sluta skicka meddelanden kan du få ett undantag. Du kan ignorera det här undantaget och stänga appfönstret.

## <a name="view-metrics-chart-on-your-dashboard"></a>Visa måttdiagram på instrumentpanelen

1. I det övre vänstra hörnet i Azure Portal öppnar du portalmenyn och väljer sedan **Instrumentpanel.**

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Skärmbild som visar hur du väljer din instrumentpanel.":::

1. Leta upp diagrammet som du fäste tidigare och klicka var som helst på panelen utanför diagramdata för att expandera det. Den visar skickade telemetrimeddelanden och det totala antalet meddelanden som används i diagrammet. De senaste talen visas längst ned i diagrammet. Du kan flytta markören i diagrammet för att se måttvärdena för specifika tider. Du kan också ändra tidsvärdet och kornigheten överst i diagrammet för att begränsa eller expandera data till en tidsperiod av intresse.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Skärmbild som visar måttdiagrammet.":::

   I det här scenariot är den simulerade enhetens meddelandegenomflöde inte tillräckligt stort för att IoT Hub begränsa dess meddelanden. I ett scenario som faktiskt omfattar begränsning kan du se att telemetrimeddelanden som skickas överskrider begränsningsgränsen för din IoT-hubb under en begränsad tid. Detta är för att hantera burst-trafik. Mer information finns i [trafikformning](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Visa aviseringarna

När antalet meddelanden som skickas överskrider de gränser som du har angett i dina aviseringsregler börjar du få e-postaviseringar.

Om du vill se om det finns några aktiva aviseringar väljer **du** **Aviseringar** under Övervakning i den vänstra rutan i IoT-hubben. I **fönstret** Aviseringar visas antalet aviseringar som har uttskjutna, sorterade efter allvarlighetsgrad för det angivna tidsperioden.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Skärmbild som visar sammanfattningen av aviseringar.":::

Välj raden för allvarlighetsgrad Sev 3. Fönstret **Alla aviseringar** öppnas och visar en lista över de 3 aviseringar som har utlöses.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Skärmbild som visar fönstret Alla aviseringar.":::

Välj en av aviseringarna för att se aviseringsinformationen.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Skärmbild som visar aviseringsinformation.":::

Kontrollera din inkorg efter e-postmeddelanden från Microsoft Azure. Ämnesraden beskriver aviseringen som utlöstes. Till exempel *Azure: Aktiverad allvarlighetsgrad: 3 Avisering om mer än 1 000 meddelanden över 5 minuter*. Brödtexten ser ut ungefär som på följande bild:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Skärmbild av e-postmeddelandet som visar att aviseringarna har aktiverats.":::

## <a name="view-azure-monitor-logs"></a>Visa Azure Monitor loggar

I avsnittet Samla in loggar för anslutningar och [enhetste](#collect-logs-for-connections-and-device-telemetry) telemetri har du skapat en diagnostikinställning för att skicka resursloggar som genereras av din IoT-hubb för anslutnings- och enhetste telemetriåtgärder till Azure Monitor Logs. I det här avsnittet kör du en Kusto-fråga mot Azure Monitor loggar för att se eventuella fel som har inträffat.

1. Under **Övervakning** i den vänstra rutan i IoT-hubben i Azure Portal väljer du **Loggar**. Stäng det inledande **fönstret Frågor** om det öppnas.

1. I fönstret Ny fråga väljer du **fliken Frågor** och expanderar **sedan IoT Hub** för att se listan över standardfrågor.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Skärmbild av IoT Hub med standardfrågor.":::

1. Välj frågan *Felsammanfattning.* Frågan visas i fönstret Frågeredigeraren. Välj **Kör** i redigeringsfönstret och observera frågeresultatet. Expandera en av raderna för att se information.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Skärmbild av loggarna som returneras av felsammanfattningsfrågan.":::

   > [!NOTE]
   > Om du inte ser några fel kan du prova att köra frågan *Nyligen anslutna* enheter. Detta bör returnera en rad för den simulerade enheten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser du skapade för den här självstudien tar du bort hela resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar den bort IoT-hubben, Log Analytics-arbetsytan och själva resursgruppen. Om du har fäst måttdiagram på instrumentpanelen måste du ta bort dem manuellt genom att klicka på de tre punkterna i det övre högra hörnet av varje diagram och välja **Ta bort**. Se till att spara ändringarna när du har tagit bort diagrammen.

Om du vill ta bort resursgruppen använder du kommandot [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig IoT Hub använda mått och loggar genom att utföra följande uppgifter:

> [!div class="checklist"]
>
> * Använd Azure CLI för att skapa en IoT-hubb, registrera en simulerad enhet och skapa en Log Analytics-arbetsyta.  
> * Skicka IoT Hub-anslutningar och resursloggar för enhettelemetri till Azure Monitor loggar på Log Analytics-arbetsytan.
> * Använd Metric Explorer för att skapa ett diagram baserat på valda mått och fäst det på instrumentpanelen.
> * Skapa måttaviseringar så att du kan meddelas via e-post när viktiga villkor inträffar.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till IoT-hubben.
> * Visa aviseringarna när dina villkor inträffar.
> * Visa måttdiagrammet på instrumentpanelen.
> * Visa IoT Hub fel och åtgärder i Azure Monitor loggar.

Gå vidare till nästa självstudie där du får lära dig hur du hanterar tillstånd för en IoT-enhet. 

> [!div class="nextstepaction"]
> [Konfigurera dina enheter från en serverdelstjänst](tutorial-device-twins.md)