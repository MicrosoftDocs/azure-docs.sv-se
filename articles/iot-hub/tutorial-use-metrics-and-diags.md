---
title: Självstudie – konfigurera och Använd mått och loggar med en Azure IoT Hub
description: Självstudie – lär dig hur du konfigurerar och använder mått och loggar med en Azure IoT Hub. Detta ger data som ska analyseras för att hjälpa till att diagnostisera problem som navet kan ha.
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
ms.openlocfilehash: 1e2983fb50ce3ad1482db85025677cba5fee0c2e
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889605"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>Självstudie: Konfigurera och Använd mått och loggar med en IoT-hubb

Du kan använda Azure Monitor för att samla in mått och loggar för din IoT-hubb som kan hjälpa dig att övervaka driften av din lösning och felsöka problem när de inträffar. I den här artikeln får du se hur du skapar diagram baserat på mått, hur du skapar aviseringar som utlöser mått, hur du skickar IoT Hub åtgärder och fel till Azure Monitor loggar och hur du kontrollerar loggarna efter fel.

I den här självstudien används Azure-exemplet från [.net-skicka telemetri snabb start](quickstart-send-telemetry-dotnet.md) för att skicka meddelanden till IoT Hub. Du kan alltid använda en enhet eller ett annat exempel för att skicka meddelanden, men du kan behöva ändra några steg i enlighet med detta.

Det kan vara bra att känna till Azure Monitor koncept innan du påbörjar den här självstudien. Mer information finns i [övervaka IoT Hub](monitor-iot-hub.md). Mer information om de mått och resurs loggar som genereras av IoT Hub finns i [referens för övervaknings data](monitor-iot-hub-reference.md).

I den här självstudien utför du följande åtgärder:

> [!div class="checklist"]
>
> * Använd Azure CLI för att skapa en IoT-hubb, registrera en simulerad enhet och skapa en Log Analytics arbets yta.  
> * Skicka IoT Hub anslutningar och enhets telemetri resurs loggar till Azure Monitor loggar i Log Analytics arbets ytan.
> * Använd Metric Explorer för att skapa ett diagram baserat på valda mått och fäst det på din instrument panel.
> * Skapa mått aviseringar så att du kan få ett meddelande via e-post när viktiga villkor uppstår.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till IoT-hubben.
> * Visa aviseringar när villkoren inträffar.
> * Visa mått diagrammet på instrument panelen.
> * Visa IoT Hub fel och åtgärder i Azure Monitor loggar.

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Du behöver .NET Core SDK 2,1 eller mer på din utvecklings dator. Du kan ladda ned .NET Core-SDK:n för flera plattformar från [.NET](https://www.microsoft.com/net/download/all).

  Du kan kontrollera den aktuella versionen av C# på utvecklingsdatorn med följande kommando:

  ```cmd/sh
  dotnet --version
  ```

- Ett e-postkonto som kan ta emot e-post.

- Kontrol lera att port 8883 är öppen i brand väggen. Enhets exemplet i den här självstudien använder MQTT-protokoll, som kommunicerar via port 8883. Den här porten kan blockeras i vissa företags-och miljö nätverks miljöer. Mer information och sätt att kringgå det här problemet finns i [ansluta till IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>Konfigurera resurser

I den här självstudien behöver du en IoT-hubb, en Log Analytics-arbetsyta och en simulerad IoT-enhet. De här resurserna kan skapas med Azure CLI eller Azure PowerShell. Använd samma resursgrupp och plats för alla resurser. När du har gått igenom självstudien kan du ta bort allt i ett steg genom att ta bort resurs gruppen.

Här är de steg som krävs.

1. Skapa en [resurs grupp](../azure-resource-manager/management/overview.md).

2. Skapa en IoT-hubb.

3. Skapa en Log Analytics-arbetsyta.

4. Registrera en enhets identitet för den simulerade enhet som skickar meddelanden till IoT-hubben. Spara anslutnings strängen för enheten som ska användas för att konfigurera den simulerade enheten.

### <a name="set-up-resources-using-azure-cli"></a>Konfigurera resurser med hjälp av Azure CLI

Kopiera och klistra in det här skriptet i Cloud Shell. Skriptet körs en rad i taget, förutsatt att du redan är inloggad. Det kan ta lite tid att köra några av kommandona. De nya resurserna skapas i resurs gruppen *ContosoResources*.

Namnet på vissa resurser måste vara unikt i Azure. Skriptet genererar ett slumpmässigt värde med `$RANDOM` funktionen och lagrar det i en variabel. För dessa resurser lägger skriptet till detta slumpmässiga värde till ett bas namn för resursen, vilket gör resurs namnet unikt.

Endast en kostnads fri IoT Hub tillåts per prenumeration. Om du redan har en kostnads fri IoT-hubb i din prenumeration kan du antingen ta bort den innan du kör skriptet eller ändra skriptet så att det använder din kostnads fria IoT-hubb eller en IoT Hub som använder standard-eller Basic-nivån.

Skriptet skriver ut namnet på IoT-hubben, namnet på Log Analytics arbets ytan och anslutnings strängen för enheten som registreras. Se till att anteckna detta när du behöver dem senare i den här artikeln.

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
>När du skapar enhets identiteten kan du få följande fel: *inga nycklar hittades för principen iothubowner för IoT Hub ContosoTestHub*. Åtgärda felet genom att uppdatera IoT-tillägget för Azure CLI och kör de senaste två kommandona i skriptet igen. 
>
>Här är kommandot för att uppdatera tillägget. Kör det här kommandot i Cloud Shell-instansen.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>Samla in loggar för anslutningar och telemetri för enheter

IoT Hub avger resurs loggar för flera åtgärds kategorier. för att du ska kunna visa dessa loggar måste du dock skapa en diagnostisk inställning för att skicka dem till ett mål. Ett sådant mål är Azure Monitor loggar som samlas in i en Log Analytics arbets yta. IoT Hub resurs loggar grupperas i olika kategorier. Du kan välja vilka kategorier som du vill skicka till Azure Monitor loggar i den diagnostiska inställningen. I den här artikeln samlar vi in loggar för åtgärder och fel som inträffar med anslutningar och telemetri för enheter. En fullständig lista över de kategorier som stöds för IoT Hub finns i [IoT Hub resurs loggar](monitor-iot-hub-reference.md#resource-logs).

Följ dessa steg om du vill skapa en diagnostisk inställning för att skicka IoT Hub resurs loggar till Azure Monitor loggar:

1. Om du inte redan är på hubben i portalen väljer du **resurs grupper** och väljer resurs gruppen ContosoResources. Välj IoT-hubben i listan över resurser som visas.

1. Leta upp **övervakningsavsnittet** på IoT Hub-bladet. Välj **diagnostikinställningar**. Välj sedan **Lägg till diagnostisk inställning**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="Skärm bild som visar diagnostikinställningar i avsnittet övervakning.":::

1. I fönstret **diagnostikinställningar anger** du ett beskrivande namn, till exempel "skicka anslutningar och telemetri till loggar".

1. Under **kategori information** väljer du **anslutningar** och **telemetri för enheter**.

1. Under **mål information** väljer du **Skicka till Log Analytics** och använder sedan väljaren Log Analytics arbets yta för att välja den arbets yta som du antecknade tidigare. När du är klar bör den diagnostiska inställningen se ut ungefär som på följande skärm bild:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="Skärmbild som visar de slutliga diagnostiklogginställningarna.":::

1. Spara inställningarna genom att klicka på **Spara**. Stäng fönstret **diagnostik-inställning** . Du kan se den nya inställningen i listan över diagnostikinställningar.

## <a name="set-up-metrics"></a>Konfigurera mått

Nu ska vi använda Metrics Explorer för att skapa ett diagram som visar mått som du vill spåra. Du fäster det här diagrammet på standard instrument panelen i Azure Portal.

1. I den vänstra rutan i IoT Hub väljer du **mått** i avsnittet **övervakning** .

1. Längst upp på skärmen väljer du de **senaste 24 timmarna (automatiskt)**. I list rutan som visas väljer du **senaste 4 timmar** för **tidsintervall**, ställer in **tids kornig het** på **1 minut** och väljer **lokalt** för **Visa tid som**. Välj **Använd** för att spara inställningarna. Inställningen bör nu säga **lokal tid: senaste 4 timmarna (1 minut)**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="Skärmbild som visar tidsinställningar för mått.":::

1. I diagrammet finns det en del mått inställning som visas som en del av din IoT Hub. Lämna standardvärdena **för områdes** **namn och mått namn rymder** . Välj **mått** inställningen och skriv "telemetri" och välj sedan **telemetri meddelanden som skickas** från List rutan. **Agg regeringen** anges automatiskt till **Sum**. Observera att namnet på diagrammet också ändras.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="Skärm bild som visar hur du lägger till telemetri meddelanden som har skickats till diagrammet.":::

1. Välj nu **Lägg till mått** för att lägga till ett mått i diagrammet. Under **du Mått** väljer du **Total number of messages used** (Totalt antal använda meddelanden). **Agg regeringen** anges automatiskt till **AVG**. Lägg märke till att diagrammets rubrik har ändrats för att ta med det här måttet.

   Nu visar skärmen minimerade mått för *Telemetry messages sent* (Skickade telemetrimeddelanden), plus det nya måttet för *Total number of messages used* (Totalt antal använda meddelanden).

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="Skärm bild som visar hur du lägger till sammanlagt antal meddelanden som används av måttet i diagrammet.":::

1. I det övre högra hörnet i diagrammet väljer **du fäst på instrument panelen**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="Skärm bild som visar knappen Fäst på instrument panelen.":::

1. I fönstret **Fäst på instrument panelen** väljer du den **befintliga** fliken. Välj **privat** och välj sedan **instrument panel** i list rutan instrument panel. Slutligen väljer du **Fäst** för att fästa diagrammet på standard instrument panelen i Azure Portal. Om du inte fäster ditt diagram på en instrument panel behålls inte inställningarna när du avslutar Metric Explorer.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="Skärm bild som visar inställningar för fäst vid instrument panelen.":::

## <a name="set-up-metric-alerts"></a>Konfigurera mått varningar

Nu ska vi ställa in aviseringar som ska utlösas på två mått *meddelanden om telemetri som skickas* och det *totala antalet meddelanden som används*.

*Meddelanden som skickas med telemetri* är ett utmärkt mått att övervaka för att spåra meddelande data flöde och undvika att begränsas. För en IoT Hub på den kostnads fria nivån är begränsnings gränsen 100 meddelanden/SEK. Med en enda enhet kommer vi inte att kunna uppnå denna typ av data flöde, så i stället ställer vi in aviseringen att utlösa om antalet meddelanden överstiger 1000 på en 5 minuters period. I produktion kan du ställa in signalen till ett mer signifikant värde baserat på nivån, versionen och antalet enheter i din IoT Hub.

*Totalt antal meddelanden som används* spårar det dagliga antalet meddelanden som används. Måttet återställs varje dag med 00:00 UTC. Om du överskrider den dagliga kvoten efter en viss tröskel kommer IoT Hub inte längre att acceptera meddelanden. För en IoT Hub på den kostnads fria nivån är den dagliga meddelande kvoten 8000. Vi konfigurerar aviseringen så att den utlöses om det totala antalet meddelanden överstiger 4000, 50% av kvoten. I praktiken skulle du förmodligen ange den här procent andelen till ett högre värde. Det dagliga kvot svärdet beror på nivån, versionen och antalet enheter i din IoT-hubb.

Mer information om kvot-och begränsnings gränser med IoT Hub finns i [kvoter och begränsning](iot-hub-devguide-quotas-throttling.md).

Så här ställer du in mått aviseringar:

1. Gå till din IoT Hub i Azure Portal.

1. Under **övervakning** väljer du **aviseringar**. Välj sedan **ny aviserings regel**.  Fönstret **skapa aviserings regel** öppnas.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="Skärm bild som visar fönstret Skapa aviserings regel.":::

    I fönstret **skapa aviserings regel** finns fyra avsnitt:

    * **Omfånget** har redan angetts till din IoT-hubb, så vi lämnar endast det här avsnittet.
    * **Villkor** anger den signal och de villkor som ska utlösa aviseringen.
    * **Åtgärder** konfigurerar vad som händer när aviseringen utlöses.
    * Med **aviserings regel information** kan du ange ett namn och en beskrivning för aviseringen.

1. Konfigurera först villkoret som aviseringen ska utlösa.

    1. Under **villkor** väljer du **Lägg till villkor**. Skriv "telemetri" i rutan Sök i rutan **Konfigurera signal logik** i sökrutan och välj **telemetri meddelanden som skickats**.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="Skärm bild som visar valet av mått.":::

    1. I fönstret **Konfigurera signal logik** anger eller bekräftar du följande fält under **aviserings logiken** (du kan ignorera diagrammet):

       **Tröskelvärde**:  *statisk*.

       **Operator**: *större än*.

       **Sammansättnings typ**: *totalt*.

       **Tröskelvärde**: 1000.

       **Agg regerings kornig het (period)**: *5 minuter*.

       **Utvärderings frekvens**: *var 1 minut*

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="Skärm bild som visar inställningar för aviserings villkor.":::

       De här inställningarna ställer in signalen för att summera antalet meddelanden under en period på 5 minuter. Den här summan kommer att utvärderas varje minut, och om summan för de föregående 5 minuterna överstiger 1000 meddelanden, utlöses aviseringen.

       Välj **färdig** för att spara signal logiken.

1. Nu ska du konfigurera åtgärden för aviseringen.

    1. Gå tillbaka till fönstret **skapa aviserings regel** under **åtgärder** och välj **Lägg till åtgärds grupper**. I fönstret **Välj en åtgärds grupp som ska kopplas till den här varnings regeln** väljer du **skapa åtgärds grupp**.

    1. Ge åtgärds gruppen ett namn och ett visnings namn under fliken **grundläggande** i fönstret **skapa åtgärds grupp** .

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="Skärm bild som visar fliken grundläggande i fönstret Skapa åtgärds grupp.":::

    1. Välj fliken **meddelanden** . För **meddelande typ** väljer du **e-post/SMS-meddelande/push/röst** i list rutan. Fönstret **e-post/SMS/push/Voice** öppnas.

    1. I fönstret **e-post/SMS/push/Voice** väljer du e-post och anger din e-postadress och väljer sedan **OK**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="Skärm bild som visar inställningar för e-postadress.":::

    1. Gå tillbaka till fönstret **meddelanden** och ange ett namn för meddelandet.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="Skärm bild som visar fönstret slutförda aviseringar.":::

    1. Valfritt Om du väljer fliken **åtgärder** och sedan markerar List rutan **Åtgärds typ** kan du se vilka typer av åtgärder som du kan utlösa med en avisering. I den här artikeln använder vi bara meddelanden, så du kan ignorera inställningarna på den här fliken.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="Skärm bild som visar tillgängliga åtgärds typer i åtgärds fönstret.":::

    1. Välj fliken **Granska och skapa** , verifiera inställningarna och välj **skapa**.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="Skärm bild som visar fönstret granska och skapa.":::

    1. Gå tillbaka till fönstret **skapa aviserings regel** och Lägg märke till att din nya åtgärds grupp har lagts till i aviseringens åtgärder.  

1. Konfigurera slutligen varnings regel informationen och spara varnings regeln.

    1. Ange ett namn och en beskrivning för aviseringen i fönstret **skapa aviserings regel** under information om aviserings regler. till exempel "varna om över 1000 meddelanden över 5 minuter". Kontrol lera att **Aktivera varnings regel när** du har skapat den har marker ATS. Den slutförda varnings regeln ser ut ungefär som den här skärm bilden.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="Skärm bild som visar fönstret Skapa aviserings regel.":::

    1. Välj **skapa aviserings regel** för att spara din nya regel.

1. Ställ nu in en ny avisering för de *sammanlagda antalet använda meddelanden*. Det här måttet är användbart om du vill skicka en avisering när antalet meddelanden som används närmar sig den dagliga kvoten för IoT Hub, då IoT Hub kommer att börja avvisa meddelanden. Följ de steg du gjorde tidigare, med följande skillnader.

    * För signalen i fönstret **Konfigurera signal logik** väljer du det **totala antalet meddelanden som används**.

    * I fönstret **Konfigurera signal logik** anger eller bekräftar du följande fält (du kan ignorera diagrammet):

       **Tröskelvärde**:  *statisk*.

       **Operator**: *större än*.

       **Sammansättnings typ**: *maximum*.

       **Tröskelvärde**: 4000.

       **Agg regerings kornig het (period)**: *1 minut*.

       **Utvärderings frekvens**: *var 1 minut*

       De här inställningarna ställer in signalen för att utlösa när antalet meddelanden når 4000. Måttet utvärderas varje minut.

    * När du anger åtgärden för varnings regeln väljer du bara åtgärds gruppen som du skapade tidigare.

    * För aviserings informationen väljer du ett annat namn och en annan beskrivning än du tidigare.

1. Välj **aviseringar** under **övervakning** i den vänstra rutan i IoT Hub. Välj nu **Hantera aviserings regler** på menyn överst i **aviserings** fönstret. Fönstret **regler** öppnas. Nu bör du se de två aviseringarna:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="Skärm bild som visar fönstret regler med de nya varnings reglerna.":::

1. Stäng fönstret **regler** .

Med de här inställningarna utlöses en avisering och du får ett e-postmeddelande när mer än 1000 meddelanden skickas inom en tids period på 5 minuter och även när det totala antalet meddelanden som används överskrider 4000 (50% av den dagliga kvoten för en IoT-hubb på den kostnads fria nivån).

## <a name="run-the-simulated-device-app"></a>Kör den simulerade Device-appen

I avsnittet [Konfigurera resurser](#set-up-resources) registrerade du en enhets identitet som ska användas för att simulera användning av en IoT-enhet. I det här avsnittet kan du ladda ned en .NET-konsol app som simulerar en enhet som skickar enhets-till-moln-meddelanden till en IoT Hub, konfigurera den för att skicka dessa meddelanden till IoT-hubben och sedan köra det.

> [!IMPORTANT]
>
> Aviseringar kan ta upp till 10 minuter innan de är helt konfigurerade och aktiverade genom att IoT Hub. Vänta minst 10 minuter från det att du konfigurerar din senaste avisering och kör den simulerade Device-appen.

Ladda ned lösningen för [IoT-enhetssimuleringen](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Den här länken laddar ned en lagrings platsen med flera program. den som du söker efter finns i IoT-Hub/snabb starter/simulerad enhet/.

1. I ett lokalt terminalfönster navigerar du till rotmappen för lösningen. Gå sedan till mappen **iot-hub\Quickstarts\simulated-device**.

1. Öppna filen **SimulatedDevice.cs** i en valfri textredigerare.

    1. Ersätt värdet för `s_connectionString` variabeln med den enhets anslutnings sträng som du antecknade när du körde skriptet för att konfigurera resurser.

    1. I `SendDeviceToCloudMessagesAsync` -metoden ändrar du `Task.Delay` från 1000 till 1, vilket minskar tiden mellan att skicka meddelanden från 1 sekund till 0,001 sekunder. När du förkortar den här fördröjningen ökar antalet meddelanden som skickas. (Du kommer troligen inte att få en meddelande frekvens på 100 meddelanden per sekund.)

        ```csharp
        await Task.Delay(1);
        ```

    1. Spara ändringarna i **SimulatedDevice. cs**.

1. I det lokala terminalfönstret kör du följande kommando för att installera de nödvändiga paketen för det simulerade enhets programmet:

    ```cmd/sh
    dotnet restore
    ```

1. Kör programmet för simulerad enhet genom att skapa och köra följande kommandon i det lokala terminalfönstret:

    ```cmd/sh
    dotnet run
    ```

    Följande skärmbild visar utdata när det simulerade enhetsprogrammet skickar telemetri till din IoT-hubb:

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="Skärm bild som visar simulerade enhets utdata.":::

Låt programmet köras i minst 10-15 minuter. Vi rekommenderar att den körs tills den slutar skicka meddelanden (cirka 20-30 minuter). Detta inträffar när du har överskridit den dagliga meddelande kvoten för din IoT-hubb och har slutat att acceptera fler meddelanden.

> [!NOTE]
> Om du låter enhets appen köras under en längre period när den slutar att skicka meddelanden kan du få ett undantag. Du kan ignorera detta undantag på ett säkert sätt och stänga app-fönstret.

## <a name="view-metrics-chart-on-your-dashboard"></a>Visa mått diagram på din instrument panel

1. I det övre vänstra hörnet av Azure Portal öppnar du Portal-menyn och väljer **instrument panel**.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="Skärm bild som visar hur du väljer din instrument panel.":::

1. Hitta diagrammet som du har fäst tidigare och klicka någonstans på panelen utanför diagrammets data för att expandera det. Det visar de meddelanden om telemetri som skickas och det totala antalet meddelanden som används i diagrammet. De senaste talen visas längst ned i diagrammet. Du kan flytta markören i diagrammet om du vill se mått värden för vissa tider. Du kan också ändra tid svärdet och precisionen längst upp i diagrammet för att begränsa eller expandera data till en tids period av intresse.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="Skärm bild som visar mått diagrammet.":::

   I det här scenariot är den simulerade enhetens meddelande data flöde inte tillräckligt stort för att orsaka att IoT Hub begränsar sina meddelanden. I ett scenario som faktiskt inbegriper begränsning kan du se att de meddelanden som skickas överskrider begränsnings gränsen för din IoT-hubb under en begränsad tid. Detta är för att hantera burst-trafik. Mer information finns i [trafik utformning](iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="view-the-alerts"></a>Visa aviseringarna

När antalet meddelanden som skickas överskrider de gränser som du har angett i aviserings reglerna börjar du få e-postaviseringar.

Om du vill se om det finns några aktiva aviseringar väljer du **aviseringar** under **övervakning** i den vänstra rutan i IoT Hub. Rutan **aviseringar** visar antalet aviseringar som har utlösts sorterade efter allvarlighets grad för det angivna tidsintervallet.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="Skärm bild som visar aviserings sammanfattningen.":::

Välj raden för allvarlighets grad allvarlighets grad 3. Fönstret **alla aviseringar** öppnas och visar en lista över de allvarlighets grad 3-aviseringar som har utlösts.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="Skärm bild som visar fönstret alla aviseringar.":::

Välj en av aviseringarna om du vill se aviserings informationen.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="Skärm bild som visar aviserings information.":::

Kontrol lera din inkorg för e-postmeddelanden från Microsoft Azure. Ämnes raden beskriver den avisering som har utlösts. Till exempel *: Azure: aktive rad allvarlighets grad: 3 avisering om över 1000 meddelanden över 5 minuter*. Texten ser ut ungefär som på följande bild:

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="Skärmbild av e-postmeddelandet som visar att aviseringarna har aktiverats.":::

## <a name="view-azure-monitor-logs"></a>Visa Azure Monitor loggar

I avsnittet [samla in loggar för anslutningar och telemetri](#collect-logs-for-connections-and-device-telemetry) har du skapat en diagnostisk inställning för att skicka resurs loggar som skickats av din IoT Hub för anslutning och telemetri till Azure Monitor loggar. I det här avsnittet ska du köra en Kusto-fråga mot Azure Monitor loggar för att se eventuella fel som har inträffat.

1. Under **övervakning** i den vänstra rutan i IoT hub i Azure Portal väljer du **loggar**. Stäng fönstret inledande **frågor** om det öppnas.

1. I fönstret ny fråga väljer du fliken **frågor** och expandera sedan **IoT Hub** för att se listan över standard frågor.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="Skärm bild av IoT Hub standard frågor.":::

1. Välj *fel sammanfattnings* fråga. Frågan visas i fönstret Frågeredigeraren. Välj **Kör** i redigerings fönstret och titta på frågeresultaten. Expandera en av raderna för att se information.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="Skärm bild av de loggar som returneras av frågan Sammanfattning av fel.":::

   > [!NOTE]
   > Om du inte ser några fel kan du försöka köra frågan *nyligen anslutna enheter* . Detta bör returnera en rad för den simulerade enheten.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser du skapade för den här självstudien tar du bort hela resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tar den bort IoT Hub, Log Analytics arbets ytan och själva resurs gruppen. Om du har fäst mått diagram på instrument panelen måste du ta bort dem manuellt genom att klicka på de tre punkterna i det övre högra hörnet i varje diagram och välja **ta bort**. Se till att spara ändringarna när du har tagit bort diagrammen.

Om du vill ta bort resursgruppen använder du kommandot [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder IoT Hub mått och loggar genom att utföra följande uppgifter:

> [!div class="checklist"]
>
> * Använd Azure CLI för att skapa en IoT-hubb, registrera en simulerad enhet och skapa en Log Analytics arbets yta.  
> * Skicka IoT Hub anslutningar och enhets telemetri resurs loggar till Azure Monitor loggar i Log Analytics arbets ytan.
> * Använd Metric Explorer för att skapa ett diagram baserat på valda mått och fäst det på din instrument panel.
> * Skapa mått aviseringar så att du kan få ett meddelande via e-post när viktiga villkor uppstår.
> * Ladda ned och kör en app som simulerar en IoT-enhet som skickar meddelanden till IoT-hubben.
> * Visa aviseringar när villkoren inträffar.
> * Visa mått diagrammet på instrument panelen.
> * Visa IoT Hub fel och åtgärder i Azure Monitor loggar.

Gå vidare till nästa självstudie där du får lära dig hur du hanterar tillstånd för en IoT-enhet. 

> [!div class="nextstepaction"]
> [Konfigurera dina enheter från en serverdelstjänst](tutorial-device-twins.md)