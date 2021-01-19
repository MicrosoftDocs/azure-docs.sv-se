---
title: Integrera med Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Se så här ställer du in händelse vägar från Azure Digitals dubbla till Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 1/19/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 24b4f56e5798acc4d9bd0962be7059a359958645
ms.sourcegitcommit: 65cef6e5d7c2827cf1194451c8f26a3458bc310a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/19/2021
ms.locfileid: "98573249"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrera Azure Digitals dubbla med Azure Time Series Insights

I den här artikeln får du lära dig hur du integrerar Azure Digitals dubbla med [Azure Time Series Insights (TSD)](../time-series-insights/overview-what-is-tsi.md).

Lösningen som beskrivs i den här artikeln gör att du kan samla in och analysera historiska data om din IoT-lösning. Azure Digital Twins är ett bra alternativ för att mata in data i Time Series Insights, eftersom det gör att du kan korrelera flera dataströmmar och standardisera informationen innan du skickar den till Time Series Insights. 

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa en relation med Time Series Insights måste du ha en **digital Azure-instans**. Den här instansen bör konfigureras med möjlighet att uppdatera Digital dubbel information baserat på data, eftersom du behöver uppdatera dubbel information några gånger för att se att dessa data spåras i Time Series Insights. 

Om du inte redan har konfigurerat den här inställningen kan du skapa den genom att följa självstudien om Azure Digitals dubblare [*: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md). Självstudien vägleder dig genom att konfigurera en digital Azure-instans som fungerar med en virtuell IoT-enhet för att utlösa digitala dubbla uppdateringar.

## <a name="solution-architecture"></a>Lösningsarkitektur

Du kommer att bifoga Time Series Insights till Azure Digitals dubbla steg genom sökvägen nedan.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="En vy över Azure-tjänster i ett scenario från slut punkt till slut punkt, som markerar Time Series Insights" lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-a-route-and-filter-to-twin-update-notifications"></a>Skapa en väg och filtrera till tvillingmeddelanden om uppdateringar

Azure Digitals dubbla instanser kan generera [dubbla uppdaterings händelser](how-to-interpret-event-data.md) när en dubbel status uppdateras. I det här avsnittet ska du skapa en Azure Digitals- [**händelse väg**](concepts-route-events.md) som dirigerar dessa uppdaterings händelser till Azure [Event Hubs](../event-hubs/event-hubs-about.md) för ytterligare bearbetning.

Självstudien om Azure Digitals sammanhållen [*: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md) genom ett scenario där en termometer används för att uppdatera ett temperatur-attribut på en digital enhet som representerar ett rum. Det här mönstret förlitar sig på de dubbla uppdateringarna, i stället för att vidarebefordra telemetri från en IoT-enhet, vilket ger dig flexibilitet att ändra den underliggande data källan utan att behöva uppdatera Time Series Insights logiken.

1. Börja med att skapa ett namn område för Event Hub som tar emot händelser från din Azure Digitals-instans. Du kan antingen använda Azure CLI-instruktionerna nedan eller använda Azure Portal: [*snabb start: skapa en händelsehubben med Azure Portal*](../event-hubs/event-hubs-create.md). Om du vill se vilka regioner som stöder Event Hubs kan du besöka [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

    ```azurecli-interactive
    az eventhubs namespace create --name <name for your Event Hubs namespace> --resource-group <resource group name> -l <region>
    ```

2. Skapa en händelsehubben i namn området för att få dubbla ändrings händelser. Ange ett namn för händelsehubben.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your Twins event hub> --resource-group <resource group name> --namespace-name <Event Hubs namespace from above>
    ```

3. Skapa en [auktoriseringsregel](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) med behörigheter för att skicka och ta emot. Ange ett namn för regeln.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from above> --eventhub-name <Twins event hub name from above> --name <name for your Twins auth rule>
    ```

4. Skapa en Azure Digital- [slutpunkt](concepts-route-events.md#create-an-endpoint) som länkar din händelsehubben till din Azure Digital-instansen.

    ```azurecli-interactive
    az dt endpoint create eventhub --endpoint-name <name for your Event Hubs endpoint> --eventhub-resource-group <resource group name> --eventhub-namespace <Event Hubs namespace from above> --eventhub <Twins event hub name from above> --eventhub-policy <Twins auth rule from above> -n <your Azure Digital Twins instance name>
    ```

5. Skapa en [väg](concepts-route-events.md#create-an-event-route) i Azure Digital Twins för att skicka dubbla uppdateringshändelser till din slutpunkt. Filtret i den här vägen tillåter endast att dubbla uppdaterings meddelanden skickas till din slut punkt.

    >[!NOTE]
    >Det finns ett **känt problem** i Cloud Shell som påverkar dessa kommandogrupper: `az dt route`, `az dt model`, `az dt twin`.
    >
    >Du kan lösa problemet genom att köra `az login` i Cloud Shell innan du kör kommandot eller använda det [lokala kommandoradsgränssnittet](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) i stället för Cloud Shell. Mer information finns i [*fel sökning: kända problem i Azure Digitals*](troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).

    ```azurecli-interactive
    az dt route create -n <your Azure Digital Twins instance name> --endpoint-name <Event Hub endpoint from above> --route-name <name for your route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
    ```

Innan du fortsätter bör du anteckna *Event Hubs namnrymd* och *resurs grupp*, eftersom du kommer att använda dem igen för att skapa ytterligare en händelsehubben senare i den här artikeln.

## <a name="create-a-function-in-azure"></a>skapar en funktion i Azure

Härnäst ska du använda Azure Functions för att skapa en **Event Hubs utlöst funktion** i en Function-app. Du kan använda Function-appen som skapats i kursen från slut punkt till slut punkt ([*Självstudier: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md)) eller din egen. 

Med den här funktionen konverteras de dubbla uppdaterings händelserna från sina ursprungliga formulär som JSON-korrigeringsfiler till JSON-objekt, som endast innehåller uppdaterade och tillagda värden från dina dubbla.

Mer information om hur du använder Event Hubs med Azure Functions finns i [*Azure Event Hubs-utlösare för Azure Functions*](../azure-functions/functions-bindings-event-hubs-trigger.md).

I din publicerade Function-app ersätter du funktions koden med följande kod.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Härifrån skickar funktionen de JSON-objekt som skapas till en andra händelsehubben, som du kommer att ansluta till Time Series Insights.

Senare kan du ange vissa miljövariabler som den här funktionen ska använda för att ansluta till dina egna Event Hub.

## <a name="send-telemetry-to-an-event-hub"></a>Skicka telemetri till en händelsehubb

Nu ska du skapa en andra händelsehubben och konfigurera funktionen för att strömma utdata till den händelsehubben. Den här händelsehubben kommer sedan att anslutas till Time Series Insights.

### <a name="create-an-event-hub"></a>Skapa en händelsehubb

Om du vill skapa den andra händelsehubben kan du antingen använda Azure CLI-instruktionerna nedan eller använda Azure Portal: [*snabb start: skapa en händelsehubben med Azure Portal*](../event-hubs/event-hubs-create.md).

1. Förbered din *Event Hubs namnrymd* och *resurs grupp* namn från tidigare i den här artikeln

2. Skapa en ny händelsehubben. Ange ett namn för händelsehubben.

    ```azurecli-interactive
    az eventhubs eventhub create --name <name for your TSI event hub> --resource-group <resource group name from earlier> --namespace-name <Event Hubs namespace from earlier>
    ```
3. Skapa en [auktoriseringsregel](/cli/azure/eventhubs/eventhub/authorization-rule?view=azure-cli-latest&preserve-view=true#az-eventhubs-eventhub-authorization-rule-create) med behörigheter för att skicka och ta emot. Ange ett namn för regeln.

    ```azurecli-interactive
        az eventhubs eventhub authorization-rule create --rights Listen Send --resource-group <resource group name> --namespace-name <Event Hubs namespace from earlier> --eventhub-name <TSI event hub name from above> --name <name for your TSI auth rule>
    ```

## <a name="configure-your-function"></a>Konfigurera funktionen

Därefter måste du ställa in miljövariabler i din Function-app från tidigare, som innehåller anslutnings strängarna för de händelse nav som du har skapat.

### <a name="set-the-twins-event-hub-connection-string"></a>Ange anslutningssträngen för den dubbla händelsehubben

1. Hämta den sammanflätade [Event Hub-anslutningssträngen](../event-hubs/event-hubs-get-connection-string.md)med hjälp av de auktoriseringsregler som du skapade ovan för den dubbla hubben.

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <Twins event hub name from earlier> --name <Twins auth rule from earlier>
    ```

2. Använd anslutningssträngen som du får som resultat för att skapa en app-inställning i din funktionsapp som innehåller anslutningssträngen:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<Twins event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

### <a name="set-the-time-series-insights-event-hub-connection-string"></a>Ange anslutningssträngen för Time Series Insights-händelsehubben

1. Hämta [anslutnings strängen för TSD Event Hub](../event-hubs/event-hubs-get-connection-string.md)med hjälp av de auktoriseringsregler som du skapade ovan för Time Series Insights Hub:

    ```azurecli-interactive
    az eventhubs eventhub authorization-rule keys list --resource-group <resource group name> --namespace-name <Event Hubs namespace> --eventhub-name <TSI event hub name> --name <TSI auth rule>
    ```

2. I funktionsappen skapar du en appinställning som innehåller anslutningssträngen:

    ```azurecli-interactive
    az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<TSI event hub connection string>" -g <resource group> -n <your App Service (function app) name>
    ```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Skapa och ansluta en Time Series Insights-instans

Därefter ställer du in en Time Series Insights-instans för att ta emot data från den andra händelsehubben. Följ stegen nedan och mer information om den här processen finns i [*Självstudier: Konfigurera en Azure Time Series Insights Gen2 PAYG-miljö*](../time-series-insights/tutorials-set-up-tsi-environment.md).

1. I Azure Portal börjar du skapa en Time Series Insights resurs. 
    1. Välj pris nivå för **Gen2 (L1)** .
    2. Du måste välja ett **Time Series-ID** för den här miljön. Tids serie-ID: t kan innehålla upp till tre värden som du kan använda för att söka efter data i Time Series Insights. I den här självstudien kan du använda **$dtId**. Läs mer om att välja ett ID-värde i [*metod tips för att välja ett Time Series-ID*](../time-series-insights/how-to-select-tsid.md).
    
        :::image type="content" source="media/how-to-integrate-time-series-insights/create-twin-id.png" alt-text="Skapa Portal-UX för en Time Series Insights miljö. Pris nivån för Gen2 (L1) väljs och egenskaps namnet för Time Series-ID: t är $dtId" lightbox="media/how-to-integrate-time-series-insights/create-twin-id.png":::

2. Välj **Nästa: händelse källa** och välj din Event Hubs information från ovan. Du måste också skapa en ny Event Hubs konsument grupp.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/event-source-twins.png" alt-text="Skapa Portal-UX för en Time Series Insights miljö händelse källa. Du skapar en händelse källa med information om händelsehubben från ovan. Du skapar också en ny konsument grupp." lightbox="media/how-to-integrate-time-series-insights/event-source-twins.png":::

## <a name="begin-sending-iot-data-to-azure-digital-twins"></a>Börja skicka IoT-data till Azure Digitals, dubbla

För att kunna börja skicka data till Time Series Insights måste du börja uppdatera de digitala dubbla egenskaperna i Azure Digitals med ändra data värden. Använd kommandot [AZ DT dubbla Update](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext-azure-iot-az-dt-twin-update) .

Om du använder självstudierna från slut punkt till slut punkt ([*Självstudier: ansluta en heltäckande lösning*](tutorial-end-to-end.md)) för att hjälpa dig med miljö installationen kan du börja skicka simulerade IoT-data genom att köra *DeviceSimulator* -projektet från exemplet. Anvisningarna finns i avsnittet [*Konfigurera och köra simulering*](tutorial-end-to-end.md#configure-and-run-the-simulation) i självstudien.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualisera dina data i Time Series Insights

Nu bör data flöda till Time Series Insights-instansen som är redo att analyseras. Följ stegen nedan för att utforska de data som kommer i.

1. Öppna Time Series Insights-instansen i [Azure Portal](https://portal.azure.com) (du kan söka efter namnet på din instans i portalens Sök fält). Besök den *Time Series Insights Explorer-URL* som visas i instans översikten.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Välj URL: en för Time Series Insights Explorer på fliken Översikt i din Time Series Insights-miljö":::

2. I Utforskaren ser du de tre dubblarna från Azures digitala dubbla, som visas till vänster. Välj _**thermostat67**_, Välj **temperatur** och tryck på **Lägg till**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Välj * * thermostat67 * *, Välj * * temperatur * * och tryck på * * Lägg till * *":::

3. Du bör nu se de första temperatur avläsningarna från din termostat, som du ser nedan. Samma temperatur läsning uppdateras för *Room21* och *floor1*, och du kan visualisera dessa data strömmar i tandem.
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Inledande temperatur data visas i TSD-Utforskaren. Det är en rad med slumpmässiga värden mellan 68 och 85":::

4. Om du tillåter att simuleringen körs mycket längre, ser visualiseringen ut ungefär så här:
    
    :::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Temperatur data för varje snöre visas i tre parallella rader med olika färger.":::

## <a name="next-steps"></a>Nästa steg

De digitala multiplarna lagras som standard som en platt hierarki i Time Series Insights, men de kan berikas med modell information och en hierarki med flera nivåer för organisationen. Läs mer om den här processen genom att läsa: 

* [*Självstudie: definiera och tillämpa en modell*](../time-series-insights/tutorials-set-up-tsi-environment.md#define-and-apply-a-model) 

Du kan skriva anpassad logik för att automatiskt tillhandahålla den här informationen med modell-och diagram data som redan finns lagrade i Azure Digitals. Läs mer om hur du hanterar, uppgraderar och hämtar information från diagrammet med dubbla diagram i följande referenser:

* [*Anvisningar: hantera en digital, dubbel*](./how-to-manage-twin.md)
* [*Anvisningar: fråga det dubbla diagrammet*](./how-to-query-graph.md)