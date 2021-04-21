---
title: Integrera med Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Se hur du strömmar Azure Digital Twins telemetri till klienter med Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 02/12/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: e8bdb843ab6304f2f38228f37d8709e4084ee52e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775339"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrera Azure Digital Twins med Azure SignalR Service

I den här artikeln lär du dig att integrera Azure Digital Twins med [Azure SignalR Service](../azure-signalr/signalr-overview.md).

Med lösningen som beskrivs i den här artikeln kan du skicka telemetridata för digitala tvillingar till anslutna klienter, till exempel en enda webbsida eller ett mobilprogram. Därför uppdateras klienterna med realtidsmått och status från IoT-enheter, utan att behöva avssöka servern eller skicka nya HTTP-begäranden för uppdateringar.

## <a name="prerequisites"></a>Förutsättningar

Här är de förutsättningar som du bör slutföra innan du fortsätter:

* Innan du integrerar din lösning med Azure SignalR Service i den här artikeln bör du slutföra Azure Digital Twins [_**Tutorial: Connect an end-to-end solution**_](tutorial-end-to-end.md), eftersom den här instruktionsartikeln bygger vidare på den. I den här självstudien får du hjälp att konfigurera en Azure Digital Twins-instans som fungerar med en virtuell IoT-enhet för att utlösa uppdateringar av digitala tvillingar. Den här artikeln ansluter dessa uppdateringar till en exempelwebbapp med hjälp av Azure SignalR Service.

* Du behöver följande värden från självstudien:
  - Event Grid-ämne
  - Resursgrupp
  - Apptjänst-/funktionsappens namn
    
* Du måste installera [**Node.js**](https://nodejs.org/) datorn.

Du bör också gå vidare och logga in på Azure Portal [med](https://portal.azure.com/) ditt Azure-konto.

## <a name="solution-architecture"></a>Lösningsarkitektur

Du kommer att koppla Azure SignalR Service till Azure Digital Twins via sökvägen nedan. Avsnitt A, B och C i diagrammet är tagna från arkitekturdiagrammet för självstudiekursen från [slutet till slut.](tutorial-end-to-end.md) I den här artikeln skapar du avsnitt D om den befintliga arkitekturen.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="En vy över Azure-tjänster i ett scenario från start till slut. Visar data som flödar från en enhet till IoT Hub, via en Azure-funktion (pil B) till en Azure Digital Twins-instans (avsnitt A) och sedan ut genom Event Grid till en annan Azure-funktion för bearbetning (pil C). Avsnitt D visar data som flödar från samma Event Grid i pil C ut till en Azure-funktion som är märkt &quot;broadcast&quot;. &quot;broadcast&quot; kommunicerar med en annan Azure-funktion med etiketten &quot;negotiate&quot; och både &quot;broadcast&quot; och &quot;negotiate&quot; kommunicerar med datorenheter." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Ladda ned exempelprogrammen

Ladda först ned de exempelappar som krävs. Du behöver båda följande:
* [**Azure Digital Twins**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/)exempel från start till slut: Det här exemplet innehåller *en AdtSampleApp* som innehåller två Azure-funktioner för att flytta data runt en [*Azure Digital Twins-instans*](tutorial-end-to-end.md)(du kan lära dig mer om det här scenariot i Självstudie: Ansluta en lösning från start till slut). Den innehåller också ett *DeviceSimulator-exempelprogram* som simulerar en IoT-enhet och genererar ett nytt temperaturvärde varje sekund.
    - Om du inte redan har laddat ned exemplet som en del av [](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) självstudien  i Krav [*går*](#prerequisites)du till exempellänken och väljer knappen Bläddra efter kod under rubriken. Då kommer du till GitHub-lagringsplatsen för exemplen, som du kan ladda ned som *en . ZIP* genom att välja *knappen Kod* och Ladda *ned ZIP.*

        :::image type="content" source="media/includes/download-repo-zip.png" alt-text="Vy över lagringsplatsen digital-twins-samples på GitHub. Knappen Kod är markerad och skapar en liten dialogruta där knappen Ladda ned ZIP är markerad." lightbox="media/includes/download-repo-zip.png":::

    Då laddas en kopia av exempeldatabasen ned till datorn, som **digital-twins-samples-master.zip**. Packa upp mappen.
* [**Exempel på webbapp för SignalR-integrering:**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)Det här är ett exempel på en React-webbapp som använder Azure Digital Twins telemetridata från en Azure SignalR Service.
    -  Gå till exempellänken och använd samma nedladdningsprocess för att ladda ned en kopia av exemplet till din dator, som _**digitaltwins-signalr-webapp-sample-main.zip**_. Packa upp mappen.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Lämna webbläsarfönstret öppet till Azure Portal, eftersom du ska använda det igen i nästa avsnitt.

## <a name="publish-and-configure-the-azure-functions-app"></a>Publicera och konfigurera Azure Functions appen

I det här avsnittet ska du konfigurera två Azure-funktioner:
* **negotiate –** En HTTP-utlösarfunktion. Den använder *indatabindningen SignalRConnectionInfo* för att generera och returnera giltig anslutningsinformation.
* **broadcast** – en [Event Grid](../event-grid/overview.md) utlösarfunktion. Den tar Azure Digital Twins telemetridata via händelserutnätet och använder utdatabindningen för *den SignalR-instans* som du skapade i föregående steg för att sända meddelandet till alla anslutna klientprogram.

Starta Visual Studio (eller någon annan valfri kodredigerare) och öppna kodlösningen i mappen *digital-twins-samples-master > ADTSampleApp.* Skapa sedan funktionerna genom att följa stegen nedan:

1. I *projektet SampleFunctionsApp* skapar du en ny C#-klass med namnet **SignalRFunctions.cs.**

1. Ersätt innehållet i klassfilen med följande kod:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. I Visual Studio package *manager-konsolfönstret* eller något kommandofönster på datorn navigerar du till mappen *digital-twins-samples-master\AdtSampleApp\SampleFunctionsApp* och kör följande kommando för att installera `SignalRService` NuGet-paketet i projektet:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Detta bör lösa eventuella beroendeproblem i klassen .

1. Publicera din funktion till Azure med hjälp av stegen som beskrivs i avsnittet Publicera [ *appen*](tutorial-end-to-end.md#publish-the-app) i självstudien Ansluta en lösning *från en lösning från ena* sidan till slutet. Du kan publicera den till samma apptjänst-/funktionsapp som du använde i självstudiekursen från slutet till [slut,](#prerequisites)eller skapa en ny, men du kanske vill använda samma för att minimera dupliceringen. 

Konfigurera sedan funktionerna för att kommunicera med din Azure SignalR-instans. Du börjar med att samla in SignalR-instansens **anslutningssträng** och sedan lägga till den i functions-appens inställningar.

1. Gå till [Azure Portal](https://portal.azure.com/) och sök efter namnet på din SignalR-instans i sökfältet längst upp i portalen. Välj instansen för att öppna den.
1. Välj **Nycklar** på instansmenyn för att visa anslutningssträngarna för SignalR Service-instansen.
1. Välj ikonen *Kopiera* för att kopiera den primära anslutningssträngen.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Skärmbild av Azure Portal som visar sidan Nycklar för SignalR-instansen. Ikonen &quot;Kopiera till Urklipp&quot; bredvid primär ANSLUTNINGSSTRÄNG är markerad." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

1. Slutligen lägger du till Azure **SignalR-anslutningssträngen** i funktionens appinställningar med hjälp av följande Azure CLI-kommando. Ersätt även platshållarna med resursgruppen och apptjänstens/funktionsappens namn från den nödvändiga [självstudien.](how-to-integrate-azure-signalr.md#prerequisites) Kommandot kan köras i [Azure Cloud Shell](https://shell.azure.com)lokalt om du har Azure CLI [installerat på datorn](/cli/azure/install-azure-cli):
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Utdata från det här kommandot skriver ut alla appinställningar som har ställts in för din Azure-funktion. Leta efter `AzureSignalRConnectionString` längst ned i listan för att kontrollera att det har lagts till.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Utdrag av utdata i ett kommandofönster som visar ett listobjekt med namnet &quot;AzureSignalRConnectionString&quot;":::

#### <a name="connect-the-function-to-event-grid"></a>Anslut funktionen till Event Grid

Prenumerera sedan på *Azure-sändningsfunktionen* på event **grid-ämnet som du** skapade under [självstudien.](how-to-integrate-azure-signalr.md#prerequisites) Detta gör att telemetridata kan flöda från termostat67-tvillingen genom Event Grid-ämnet och till funktionen. Härifrån kan funktionen sända data till alla klienter.

Om du vill göra det skapar du en **händelseprenumeration** från event grid-ämnet till din *Broadcast* Azure-funktion som en slutpunkt.

I Azure Portal [du](https://portal.azure.com/)till event grid-ämnet genom att söka efter dess namn i det övre sökfältet. Välj *+ Händelseprenumeration*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: Event Grid händelseprenumeration":::

På sidan *Skapa händelseprenumeration* fyller du i fälten enligt följande (fält som fyllts i som standard nämns inte):
* *INFORMATION OM HÄNDELSEPRENUMERATION*  >  **Namn:** Ge händelseprenumerationen ett namn.
* *SLUTPUNKTSINFORMATION*  >  **Typ av** slutpunkt: *Välj Azure-funktion* på menyalternativen.
* *SLUTPUNKTSINFORMATION*  >  **Slutpunkt:** Tryck på *länken Välj en slutpunkt.* Då öppnas fönstret *Välj Azure-funktion:*
    - Fyll i din **prenumeration,** **resursgrupp,** **funktionsapp** och **funktion** (*broadcast*). Vissa av dessa kan fyllas i automatiskt när du har valt prenumerationen.
    - Tryck **på Bekräfta val.**

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal för att skapa en händelseprenumeration. Fälten ovan fylls i och knapparna &quot;Bekräfta markering&quot; och &quot;Skapa&quot; är markerade.":::

Gå tillbaka till *sidan Skapa händelseprenumeration* och tryck **på Skapa.**

Nu bör du se två händelseprenumerationer på sidan *Event Grid ämne.*

:::image type="content" source="media/how-to-integrate-azure-signalr/view-event-subscriptions.png" alt-text="Azure Portal vy över två händelseprenumerationer på ämnessidan för Event Grid." lightbox="media/how-to-integrate-azure-signalr/view-event-subscriptions.png":::

## <a name="configure-and-run-the-web-app"></a>Konfigurera och köra webbappen

I det här avsnittet visas resultatet i praktiken. Konfigurera först **exempelklientwebbappen** så att den ansluter till det Azure SignalR-flöde som du har konfigurerat. Nu ska du starta den simulerade **exempelappen för** enheter som skickar telemetridata via din Azure Digital Twins instans. Därefter visar du exempelwebbappen för att se simulerade enhetsdata som uppdaterar exempelwebbappen i realtid.

### <a name="configure-the-sample-client-web-app"></a>Konfigurera exempelklientwebbappen

Nu ska du konfigurera exempelklientwebbappen. Börja med att samla **in HTTP-slutpunkts-URL:en** för *negotiate-funktionen* och använd den sedan för att konfigurera appkoden på datorn.

1. Gå till Azure Portal på sidan [Funktionsappar](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) och välj din funktionsapp i listan. I appmenyn väljer du *Funktioner* och sedan *negotiate-funktionen.*

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure Portal vy över funktionsappen med &quot;Functions&quot; markerat i menyn. Listan över funktioner visas på sidan och funktionen &quot;negotiate&quot; är också markerad.":::

1. Tryck på Get function URL (Hämta *funktions-URL)* och kopiera värdet uppåt **via _/api_ (inkludera inte den senaste _/negotiate?_)**. Du använder detta i nästa steg.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Azure Portal vy över funktionen &quot;negotiate&quot;. Knappen Hämta funktions-URL är markerad och delen av URL:en från början till och med &quot;/api&quot;":::

1. Med Visual Studio eller valfri kodredigerare öppnar du den uppackade _**mappen digitaltwins-signalr-webapp-sample-main**_ som du laddade ned i avsnittet Ladda ned [*exempelprogram.*](#download-the-sample-applications)

1. Öppna filen *src/App.js* och ersätt funktions-URL:en i med HTTP-slutpunktens URL för negotiate-funktionen som `HubConnectionBuilder` du sparade i föregående steg: 

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<Function URL>')
            .build();
    ```
1. I Visual Studio för *utvecklare* eller något kommandofönster på datorn navigerar du till mappen *digitaltwins-signalr-webapp-sample-main\src.* Kör följande kommando för att installera de beroende nodpaketen:

    ```cmd
    npm install
    ```

Ange sedan behörigheter i funktionsappen i Azure Portal:
1. På Azure Portal [funktionsappar väljer du](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) din funktionsappinstans.

1. Rulla nedåt i instansmenyn och välj *CORS*. På SIDAN CORS lägger du till `http://localhost:3000` som ett tillåtet ursprung genom att ange det i den tomma rutan. Markera kryssrutan för Enable *Access-Control-Allow-Credentials (Aktivera access-Control-Allow-Credentials)* och tryck *på Spara*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="CORS-inställning i Azure Function":::

### <a name="run-the-device-simulator"></a>Köra enhetssimulatorn

I självstudiekursen från slutet till [](tutorial-end-to-end.md#configure-and-run-the-simulation) slut konfigurerade du enhetssimulatorn så att den skickar data via en IoT Hub och till din Azure Digital Twins instans.

Nu behöver du bara starta simulatorprojektet som finns i *digital-twins-samples-master > DeviceSimulator > DeviceSimulator.sln*. Om du använder Visual Studio kan du öppna projektet och sedan köra det med den här knappen i verktygsfältet:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Start Visual Studio knappen (DeviceSimulator-projekt)":::

Ett konsolfönster öppnas och visar telemetrimeddelanden för simulerad temperatur. Dessa skickas via din Azure Digital Twins instans, där de sedan hämtas av Azure Functions och SignalR.

Du behöver inte göra något annat i den här konsolen, men lämna den igång medan du slutför nästa steg.

### <a name="see-the-results"></a>Visa resultaten

Om du vill se resultatet i praktiken startar du **webbappsexempel för SignalR-integrering.** Du kan göra detta från alla konsolfönster på platsen *digitaltwins-signalr-webapp-sample-main\src* genom att köra det här kommandot:

```cmd
npm start
```

Då öppnas ett webbläsarfönster som kör exempelappen, som visar en visuell temperaturmätare. När appen körs bör du börja se temperaturtelemetrivärdena från enhetssimulatorn som sprids genom Azure Digital Twins visas av webbappen i realtid.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Utdrag från exempelklientwebbappen som visar en visuell temperaturmätare. Temperaturen som återspeglas är 67,52":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver resurserna som skapades i den här artikeln följer du dessa steg för att ta bort dem. 

Med hjälp Azure Cloud Shell lokala Azure CLI kan du ta bort alla Azure-resurser i en resursgrupp med [kommandot az group delete.](/cli/azure/group#az_group_delete) Om du tar bort resursgruppen tas även...
* den Azure Digital Twins instansen (från självstudiekursen från slutet till slut)
* IoT-hubben och registreringen av hubbenheten (från självstudiekursen från slutet till slut)
* Event Grid-ämnet och associerade prenumerationer
* appen Azure Functions, inklusive alla tre funktionerna och associerade resurser som lagring
* Azure SignalR-instansen

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Slutligen tar du bort exempelmapparna för projektet som du laddade ned till den lokala datorn (*digital-twins-samples-master.zip*, *digitaltwins-signalr-webapp-sample-main.zip*, och deras uppackade motsvarigheter).

## <a name="next-steps"></a>Nästa steg

I den här artikeln ställer du in Azure Functions med SignalR för Azure Digital Twins skicka telemetrihändelser till ett exempelklientprogram.

Härnäst kan du läsa mer om Azure SignalR Service:
* [*Vad är Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Eller läs mer om Azure SignalR Service autentisering med Azure Functions:
* [*Azure SignalR Service-autentisering*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)