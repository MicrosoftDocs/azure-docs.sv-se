---
title: Integrera med Azure SignalR Service
titleSuffix: Azure Digital Twins
description: Se hur du direktuppspelar Azure Digitals dubbla telemetri till klienter med Azure SignalR
author: dejimarquis
ms.author: aymarqui
ms.date: 09/02/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d84acc5501b3d40f6db85d0ee6ee369aec5a6aa4
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051113"
---
# <a name="integrate-azure-digital-twins-with-azure-signalr-service"></a>Integrera Azure Digitals dubbla med Azure SignalR-tjänsten

I den här artikeln får du lära dig hur du integrerar Azure Digitals dubbla med [Azure SignalR-tjänsten](../azure-signalr/signalr-overview.md).

Lösningen som beskrivs i den här artikeln gör att du kan push-överföra digitala dubbla telemetridata till anslutna klienter, till exempel en enskild webb sida eller ett mobilt program. Det innebär att klienterna uppdateras med real tids mått och status från IoT-enheter, utan att behöva avsöka servern eller skicka nya HTTP-begäranden för uppdateringar.

## <a name="prerequisites"></a>Förutsättningar

Här är de krav du måste utföra innan du fortsätter:

* Innan du integrerar din lösning med Azure SignalR-tjänsten i den här artikeln bör du slutföra självstudien om Azure Digitals dubbla IT [_**-anvisningar: Anslut en lösning från slut punkt till slut punkt**_](tutorial-end-to-end.md), eftersom den här instruktionen bygger på den här instruktionen. Självstudien vägleder dig genom att konfigurera en digital Azure-instans som fungerar med en virtuell IoT-enhet för att utlösa digitala dubbla uppdateringar. Den här instruktionen kommer att ansluta dessa uppdateringar till en webbapp med Azure SignalR-tjänsten.
    - Du behöver namnet på det **Event Grid-ämne** som du skapade i självstudien.
* Ha [**Node.js**](https://nodejs.org/) installerat på datorn.

Du kan också gå vidare och logga in på [Azure Portal](https://portal.azure.com/) med ditt Azure-konto.

## <a name="solution-architecture"></a>Lösningsarkitektur

Du kommer att ansluta Azure SignalR-tjänsten till Azure Digitals dubbla steg genom sökvägen nedan. Avsnitten A, B och C i diagrammet hämtas från arkitektur diagrammet för det krav som gäller från [slut punkt till slut punkt](tutorial-end-to-end.md). i den här instruktionen kommer du att bygga på detta genom att lägga till avsnitt D.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-integration-topology.png" alt-text="En vy över Azure-tjänster i ett scenario från slut punkt till slut punkt. Visar data som flödar från en enhet till IoT Hub, via en Azure-funktion (pil B) till en digital Azure-instans (del A), sedan ut genom Event Grid till en annan Azure-funktion för bearbetning (pil C). Avsnitt D visar data som flödar från samma Event Grid i pil C ut till en Azure-funktion med etiketten broadcast. &quot;sändning&quot; kommunicerar med en annan Azure-funktion med etiketten &quot;förhandla&quot;, och både sändnings-och Negotiate-kommunikation med dator enheter." lightbox="media/how-to-integrate-azure-signalr/signalr-integration-topology.png":::

## <a name="download-the-sample-applications"></a>Ladda ned exempel programmen

Ladda först ned de exempel appar som krävs. Du behöver båda följande:
* [**Azure Digitals dubblare-exempel**](/samples/azure-samples/digital-twins-samples/digital-twins-samples/): det här exemplet innehåller en *AdtSampleApp* med två Azure Functions för att flytta data runt en Azure Digital-instansen (du kan lära dig mer om det här scenariot i [*Självstudier: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md)). Det innehåller också ett *DeviceSimulator* exempel program som simulerar en IoT-enhet och genererar ett nytt temperatur värde varje sekund. 
    - Navigera till exempel länken och tryck på *hämtnings zip* -knappen för att ladda ned en kopia av exemplet till din dator, som _**Azure_Digital_Twins_end_to_end_samples.zip**_. Packa upp mappen.
* [**Exempel på integrations-**](/samples/azure-samples/digitaltwins-signalr-webapp-sample/digital-twins-samples/)webbapp: det här är ett exempel på att en webbapp används för att använda Azure Digitals informations data från en Azure SignalR-tjänst.
    -  Navigera till exempel länken och tryck på *hämtnings zip* -knappen för att ladda ned en kopia av exemplet till din dator, som _**Azure_Digital_Twins_SignalR_integration_web_app_sample.zip**_. Packa upp mappen.

[!INCLUDE [Create instance](../azure-signalr/includes/signalr-quickstart-create-instance.md)]

Lämna webbläsarfönstret öppet till Azure Portal, eftersom du kommer att använda det igen i nästa avsnitt.

## <a name="configure-and-run-the-azure-functions-app"></a>Konfigurera och köra Azure Functions-appen

I det här avsnittet ska du konfigurera två Azure Functions:
* **Negotiate** – en http-utlösnings funktion. Den använder *SignalRConnectionInfo* -indata-bindning för att generera och returnera giltig anslutnings information.
* **sändning** – en [Event Grid](../event-grid/overview.md) trigger-funktion. Den får Azure Digitals dubbla telemetridata genom Event Grid och använder utgående bindningen för *signal* instansen som du skapade i föregående steg för att sända meddelandet till alla anslutna klient program.

Gå först till webbläsaren där Azure Portal öppnas och utför följande steg för att hämta **anslutnings strängen** för den signal instansen som du har konfigurerat. Du behöver den för att konfigurera funktionerna.
1. Bekräfta att den signal tjänst instans som du distribuerade tidigare har skapats. Du kan göra detta genom att söka efter dess namn i rutan Sök högst upp i portalen. Välj instansen för att öppna den.

1. Välj **nycklar** på menyn instans för att Visa anslutnings strängar för signal tjänst instansen.

1. Välj ikonen för att kopiera den primära anslutnings strängen.

    :::image type="content" source="media/how-to-integrate-azure-signalr/signalr-keys.png" alt-text="Skärm bild av Azure Portal som visar sidan nycklar för signal instansen. Ikonen Kopiera till Urklipp bredvid den primära ANSLUTNINGS STRÄNGEN är markerad." lightbox="media/how-to-integrate-azure-signalr/signalr-keys.png":::

Starta sedan Visual Studio (eller en annan valfri kod redigerare) och öppna kod lösningen i mappen *Azure_Digital_Twins_end_to_end_samples > ADTSampleApp* . Utför sedan följande steg för att skapa funktionerna:

1. Skapa en ny C# Sharp-klass med namnet **SignalRFunctions.cs** i *SampleFunctionsApp* -projektet.

1. Ersätt innehållet i klass filen med följande kod:
    
    :::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/signalRFunction.cs":::

1. I Visual Studios fönstret *Package Manager-konsol* eller något kommando fönster på datorn i mappen *Azure_Digital_Twins_end_to_end_samples \adtsampleapp\samplefunctionsapp* kör du följande kommando för att installera NuGet- `SignalRService` paketet till projektet:
    ```cmd
    dotnet add package Microsoft.Azure.WebJobs.Extensions.SignalRService --version 1.2.0
    ```

    Detta bör lösa eventuella beroende problem i klassen.

Publicera sedan din funktion till Azure med hjälp av stegen som beskrivs i artikeln [ *Publicera appen app*](tutorial-end-to-end.md#publish-the-app) i guiden *Anslut en lösning från slut punkt till slut punkt* . Du kan publicera den till samma app service/Function-app som du använde i den kompletta självstudien krav, eller skapa en ny, men du kanske vill använda samma som för att minimera duplicering. Slutför också appen publicera med följande steg:
1. Samla in URL: en för *Negotiate* -funktionens **http-slutpunkt**. Det gör du genom att gå till sidan för Azure Portal [funktions appar](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) och välja din Function-app i listan. I menyn app väljer du *Functions* och sedan *Negotiate* -funktionen.

    :::image type="content" source="media/how-to-integrate-azure-signalr/functions-negotiate.png" alt-text="Azure Portal visning av Function-appen med &quot;Functions&quot; markerat i menyn. Listan över funktioner visas på sidan och funktionen Negotiate är också markerad.":::

    Tryck på *Hämta funktions webb adress* och kopiera värdet **uppåt till _/API_ (ta inte med den senaste _/Negotiate?_)**. Du kommer att använda detta senare.

    :::image type="content" source="media/how-to-integrate-azure-signalr/get-function-url.png" alt-text="Azure Portal visning av funktionen Negotiate. Knappen &quot;Hämta funktions webb adress&quot; är markerad och del av URL: en från början till &quot;/API&quot;":::

1. Slutligen lägger du till **anslutnings strängen** för Azure SignalR från tidigare till funktionens appinställningar med hjälp av följande Azure CLI-kommando. Kommandot kan köras i [Azure Cloud Shell](https://shell.azure.com)eller lokalt om du har Azure CLI [installerat på datorn](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true):
 
    ```azurecli-interactive
    az functionapp config appsettings set -g <your-resource-group> -n <your-App-Service-(function-app)-name> --settings "AzureSignalRConnectionString=<your-Azure-SignalR-ConnectionString>"
    ```

    Utdata från det här kommandot skriver ut alla app-inställningar som har kon figurer ATS för din Azure-funktion. Leta upp längst `AzureSignalRConnectionString` ned i listan för att kontrol lera att den har lagts till.

    :::image type="content" source="media/how-to-integrate-azure-signalr/output-app-setting.png" alt-text="Utdrag ur utdata i ett kommando fönster som visar ett List objekt med namnet ' AzureSignalRConnectionString '":::

#### <a name="connect-the-function-to-event-grid"></a>Anslut funktionen till Event Grid

Sedan prenumererar du på *sändnings* Azure-funktionen i **avsnittet Event Grid** som du skapade under [*självstudien: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md) krav. Detta tillåter att telemetridata kan flöda från *thermostat67en* dubbla genom Event Grid-ämnet till funktionen, som kan sända den till alla klienter.

Om du vill göra det skapar du en **Event Grid-prenumeration** från ditt event Grid-ämne till din *broadcast* Azure-funktion som en slut punkt.

I [Azure Portal](https://portal.azure.com/)navigerar du till ditt event Grid-ämne genom att söka efter dess namn i det övre Sök fältet. Välj *+ Händelseprenumeration*.

:::image type="content" source="media/how-to-integrate-azure-signalr/event-subscription-1b.png" alt-text="Azure Portal: Event Grid händelse prenumeration":::

På sidan *Skapa händelse prenumeration* fyller du i fälten enligt följande (fält som är fyllda som standard nämns inte):
* *information om*  >  händelse prenumeration **Namn**: ge din händelse prenumeration ett namn.
* *slut punkts information*  >  **Slut punkts typ**: Välj *Azure Function* från meny alternativen.
* *slut punkts information*  >  **Slut punkt**: Tryck på länken *Välj en slut punkt* . Då öppnas ett *Välj Azure Function* -fönster:
    - Fyll i din **prenumeration**, **resurs grupp**, **Function-app** och **Function** (*sändning*). Vissa av dessa kan fyllas i automatiskt när du har valt prenumerationen.
    - **Bekräfta val** av träff.

:::image type="content" source="media/how-to-integrate-azure-signalr/create-event-subscription.png" alt-text="Azure Portal vy för att skapa en händelse prenumeration. Fälten ovan fylls i och knapparna bekräfta markering och skapa är markerade.":::

Gå tillbaka till sidan *Skapa händelse prenumeration* och tryck på **skapa**.

## <a name="configure-and-run-the-web-app"></a>Konfigurera och köra webb programmet

I det här avsnittet visas resultatet av åtgärden. Först ska du starta den **simulerade enhetens exempel App** som skickar telemetridata via din Azure Digitals-instans. Sedan konfigurerar du **exempel klientens webbapp** så att den ansluter till Azure SignalR-flödet som du har konfigurerat. Därefter bör du kunna se data som uppdaterar exempel webb programmet i real tid.

### <a name="run-the-device-simulator"></a>Köra enhets simulatorn

Under den heltäckande själv studie kursen [konfigurerade du enhets simulatorn](tutorial-end-to-end.md#configure-and-run-the-simulation) för att skicka data via en IoT Hub och till din Azure Digital-instansen.

Nu behöver du bara starta Simulator projektet, som finns i *Azure_Digital_Twins_end_to_end_samples > DeviceSimulator > DeviceSimulator. SLN*. Om du använder Visual Studio kan du öppna projektet och sedan köra det med den här knappen i verktygsfältet:

:::image type="content" source="media/how-to-integrate-azure-signalr/start-button-simulator.png" alt-text="Start knappen i Visual Studio (DeviceSimulator-projekt)":::

Ett konsol fönster öppnas och visar simulerade simulerings meddelanden för temperatur. De skickas via din Azure Digital-instansen, där de sedan hämtas av Azure Functions och SignalR.

Du behöver inte göra något annat i den här konsolen, men lämna det samtidigt som du slutför nästa steg.

### <a name="configure-the-sample-client-web-app"></a>Konfigurera en exempel klients webbapp

Sedan konfigurerar du **webb programmet för signal integrering** med följande steg:
1. Öppna den zippade _**Azure_Digital_Twins_SignalR_integration_web_app_sample**_ mappen som du laddade ned i avsnittet [*Ladda ned exempel program*](#download-the-sample-applications) , genom att använda Visual Studio eller valfri valfri kod redigerare.

1. Öppna filen *src/App.js* och ersätt webb adressen i `HubConnectionBuilder` med http-slutpunktens URL för den **förhandlande** funktionen som du sparade tidigare:

    ```javascript
        const hubConnection = new HubConnectionBuilder()
            .withUrl('<URL>')
            .build();
    ```
1. I Visual Studios *kommando tolk för utvecklare* eller ett kommando fönster på datorn går du till mappen *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* . Kör följande kommando för att installera de beroende Node-paketen:

    ```cmd
    npm install
    ```

Ange sedan behörigheter i din Function-app i Azure Portal:
1. På sidan Azure Portal [funktions appar](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Web%2Fsites/kind/functionapp) väljer du din funktions program instans.
1. Rulla ned på menyn instans och välj *CORS*. På sidan CORS lägger du till `http://localhost:3000` som ett tillåtet ursprung genom att ange den i rutan tom. Markera kryss rutan *Aktivera åtkomst kontroll-Tillåt-autentiseringsuppgifter* och tryck på *Spara*.

    :::image type="content" source="media/how-to-integrate-azure-signalr/cors-setting-azure-function.png" alt-text="CORS-inställning i Azure Function":::

### <a name="see-the-results"></a>Visa resultaten

Om du vill se resultatet av åtgärden startar du avsnittet för **signal integrerings-appen**. Du kan göra detta från alla konsol fönster på *Azure_Digital_Twins_SignalR_integration_web_app_sample \src* -platsen genom att köra det här kommandot:

```cmd
npm start
```

Då öppnas ett webbläsarfönster som kör exempel appen, som visar en visuell temperatur mätare. När appen har körts bör du se värdena för temperatur telemetri från enhets simulatorn som sprids via Azure Digital-delningar som återspeglas av webbappen i real tid.

:::image type="content" source="media/how-to-integrate-azure-signalr/signalr-webapp-output.png" alt-text="Utdrag från exempel klientens webbapp som visar en visuell temperatur mätare. Temperaturen som visas är 67,52":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver resurserna som skapats i den här artikeln följer du stegen nedan för att ta bort dem. 

Med hjälp av Azure Cloud Shell eller lokalt Azure CLI kan du ta bort alla Azure-resurser i en resurs grupp med kommandot [AZ Group Delete](/cli/azure/group?view=azure-cli-latest&preserve-view=true#az-group-delete) . Borttagning av resurs gruppen tas också bort...
* Azure Digitals dubbla instansen (från slut punkt till slut punkt)
* IoT-hubben och nav enhets registreringen (från slut punkt till slut punkt)
* Event Grid-ämnet och associerade prenumerationer
* Azure Functions-appen, inklusive alla tre funktionerna och tillhör ande resurser, t. ex. lagring
* Azure SignalR-instansen

> [!IMPORTANT]
> Att ta bort en resursgrupp kan inte ångras. Resursgruppen och alla resurser som ingår i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. 

```azurecli-interactive
az group delete --name <your-resource-group>
```

Slutligen tar du bort de exempel mappar för Project som du laddade ned till din lokala dator (*Azure_Digital_Twins_end_to_end_samples.zip* och *Azure_Digital_Twins_SignalR_integration_web_app_sample.zip*).

## <a name="next-steps"></a>Nästa steg

I den här artikeln ställer du in Azure Functions med SignalR för att skicka Azure Digital-telemetri om telemetri-händelser till ett exempel klient program.

Läs sedan mer om Azure SignalR-tjänsten:
* [*Vad är Azure SignalR Service?*](../azure-signalr/signalr-overview.md)

Eller Läs mer om Azure SignalR service-autentisering med Azure Functions:
* [*Azure SignalR Service-autentisering*](../azure-signalr/signalr-tutorial-authenticate-azure-functions.md)