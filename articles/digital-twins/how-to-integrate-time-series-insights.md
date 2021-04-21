---
title: Integrera med Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Se hur du ställer in händelsevägar från Azure Digital Twins till Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: d89ee4c8e66ba4dda004fbd27e15b96ab13c642b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783781"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrera Azure Digital Twins med Azure Time Series Insights

I den här artikeln lär du dig att integrera Azure Digital Twins med [Azure Time Series Insights (TSI).](../time-series-insights/overview-what-is-tsi.md)

Med den lösning som beskrivs i den här artikeln kan du samla in och analysera historiska data om din IoT-lösning. Azure Digital Twins är ett bra alternativ för att mata in data i Time Series Insights, eftersom det gör att du kan korrelera flera dataströmmar och standardisera informationen innan du skickar den till Time Series Insights.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan konfigurera en Time Series Insights måste du konfigurera följande resurser:
* En **IoT-hubb**. Instruktioner finns i avsnittet [*Skapa en IoT Hub*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) i IoT Hub *snabbstarten* skicka telemetri.
* En **Azure Digital Twins instans**.
Anvisningar finns i [*Anvisningar: Konfigurera en instans Azure Digital Twins och autentisering.*](./how-to-set-up-instance-portal.md)
* En **modell och en tvilling i Azure Digital Twins instansen**.
Du behöver uppdatera tvillinginformationen några gånger för att se att data spåras i Time Series Insights. Instruktioner finns i avsnittet [*Lägg till en modell och tvilling*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) i artikeln How to: Ingest IoT hub (Så här gör *du: Mata in IoT Hub).*

> [!TIP]
> I den här artikeln uppdateras de föränderliga digitala tvillingvärdena som visas i Time Series Insights manuellt för enkelhetens skull. Men om du vill slutföra den här artikeln med live-simulerade data kan du konfigurera en Azure-funktion som uppdaterar digitala tvillingar baserat på IoT-telemetrihändelser från en simulerad enhet. Anvisningar finns i [*Anvisningar: Mata in IoT Hub data,*](how-to-ingest-iot-hub-data.md)inklusive de sista stegen för att köra enhetssimulatorn och verifiera att dataflödet fungerar.
>
> Leta senare efter ett annat TIPS som visar var du kan börja köra enhetssimulatorn och få dina Azure-funktioner att uppdatera tvillingarna automatiskt, i stället för att skicka manuella uppdateringskommandon för digitala tvillingar.


## <a name="solution-architecture"></a>Lösningsarkitektur

Du kommer att koppla Time Series Insights till Azure Digital Twins genom sökvägen nedan.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagram över Azure-tjänster i ett scenario från start till slut, med fokus Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Skapa namnområde för en händelsehubb

Innan du skapar händelsehubben skapar du först ett namnområde för händelsehubben som tar emot händelser från din Azure Digital Twins instans. Du kan antingen använda Azure CLI-anvisningarna nedan eller använda Azure Portal: Snabbstart: Skapa en [*händelsehubb med hjälp Azure Portal*](../event-hubs/event-hubs-create.md). Om du vill se vilka regioner som stöder händelsehubbb kan du gå [*till Tillgängliga Azure-produkter per region.*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs)

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Om du får ett felmeddelande som anger kontrollerar du att det namn som du har valt för namnområdet uppfyller namngivningskraven som beskrivs i det här `BadRequest: The specified service namespace is invalid.` referensdokumentet: [Skapa namnområde](/rest/api/servicebus/create-namespace).

Du kommer att använda händelsehubbens namnområde för att innehålla de två händelsehubbbar som behövs för den här artikeln:

  1. **Twins Hub –** Händelsehubb för att ta emot ändringshändelser för tvillingar
  2. **Tidsseriehubb** – Händelsehubb för att strömma händelser till Time Series Insights

I nästa avsnitt går vi igenom hur du skapar och konfigurerar dessa hubbar i händelsehubbens namnområde.

## <a name="create-twins-hub"></a>Skapa tvillinghubb

Den första händelsehubben som du skapar i den här artikeln är **tvillinghubben**. Den här händelsehubben tar emot tvillingändringshändelser från Azure Digital Twins.
Om du vill konfigurera tvillinghubben utför du följande steg i det här avsnittet:

1. Skapa tvillinghubben
2. Skapa en auktoriseringsregel för att styra behörigheter till hubben
3. Skapa en slutpunkt i Azure Digital Twins använder auktoriseringsregeln för att komma åt hubben
4. Skapa en väg i Azure Digital Twins skickar händelsen twin updates till slutpunkten och den anslutna tvillinghubben
5. Hämta anslutningssträngen för Twins Hub

Skapa **tvillinghubben med** följande CLI-kommando. Ange ett namn för din tvillinghubb.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Skapa auktoriseringsregel för Twins Hub

Skapa en [auktoriseringsregel](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) med behörighet att skicka och ta emot. Ange ett namn för regeln.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Skapa twins hub-slutpunkt

Skapa en Azure Digital Twins [som](concepts-route-events.md#create-an-endpoint) länkar händelsehubben till din Azure Digital Twins instans. Ange ett namn för tvillinghubbens slutpunkt.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Skapa händelseväg för Twins Hub

Azure Digital Twins instanser kan generera [tvillinguppdateringshändelser](how-to-interpret-event-data.md) när en tvillings tillstånd uppdateras. I det här avsnittet skapar du en Azure Digital Twins **händelseväg** som dirigerar dessa uppdateringshändelser till tvillinghubben för vidare bearbetning.

Skapa en [väg](concepts-route-events.md#create-an-event-route) i Azure Digital Twins att skicka tvillinguppdateringshändelser till slutpunkten ovan. Filtret i den här vägen tillåter endast att tvillinguppdateringsmeddelanden skickas till slutpunkten. Ange ett namn för twins hub-händelsevägen.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Hämta anslutningssträngen för Twins Hub

Hämta [anslutningssträngen för twins-händelsehubben](../event-hubs/event-hubs-get-connection-string.md)med hjälp av de auktoriseringsregler som du skapade ovan för twins-hubben.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Anteckna värdet **primaryConnectionString från** resultatet för att konfigurera inställningen för Twins Hub-appen senare i den här artikeln.

## <a name="create-time-series-hub"></a>Skapa en tidsseriehubb

Den andra händelsehubben som du skapar i den här artikeln är **tidsseriehubben**. Det här är en händelsehubb som strömmar Azure Digital Twins händelser till Time Series Insights.
Om du vill konfigurera tidsseriehubben utför du följande steg:

1. Skapa tidsseriehubben
2. Skapa en auktoriseringsregel för att styra behörigheter till hubben
3. Hämta tidsseriehubbens anslutningssträng

Senare, när du skapar Time Series Insights-instansen, ansluter du den här tidsseriehubben som händelsekälla för Time Series Insights instansen.

Skapa **tidsseriehubben** med följande kommando. Ange ett namn för tidsseriehubben.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Skapa auktoriseringsregel för tidsseriehubb

Skapa en [auktoriseringsregel](/cli/azure/eventhubs/eventhub/authorization-rule#az_eventhubs_eventhub_authorization_rule_create) med behörighet att skicka och ta emot. Ange ett namn för auth-regeln för tidsseriehubben.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Hämta time series hub-anslutningssträng

Hämta [tidsseriehubbens anslutningssträng](../event-hubs/event-hubs-get-connection-string.md)med hjälp av de auktoriseringsregler som du skapade ovan för tidsseriehubben:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Anteckna värdet **primaryConnectionString från** resultatet för att konfigurera appinställningen för tidsseriehubben senare i den här artikeln.

Anteckna även följande värden för att använda dem senare för att skapa en Time Series Insights instans.
* Namnområde för händelsehubb
* Namn på tidsseriehubb
* Auth-regel för tidsseriehubb

## <a name="create-a-function"></a>Skapa en funktion

I det här avsnittet skapar du en Azure-funktion som konverterar uppdateringshändelser för tvillingar från deras ursprungliga format som JSON-korrigeringsdokument till JSON-objekt som endast innehåller uppdaterade och tillagda värden från dina tvillingar.

### <a name="step-1-create-function-app"></a>Steg 1: Skapa funktionsapp

Skapa först ett nytt funktionsappsprojekt i Visual Studio. Anvisningar om hur du gör detta finns i avsnittet Skapa en [**funktionsapp i Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) i artikeln *How-to: Set up a function for processing data (Konfigurera* en funktion för bearbetning av data).

### <a name="step-2-add-a-new-function"></a>Steg 2: Lägg till en ny funktion

Skapa en ny Azure-funktion *med namnet ProcessDTUpdatetoTSI.cs* för att uppdatera enhetens telemetrihändelser till Time Series Insights. Funktionstypen är **Händelsehubbutlösare.**

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Skärmbild av Visual Studio du skapar en ny Azure-funktion av typen händelsehubbutlösare.":::

### <a name="step-3-fill-in-function-code"></a>Steg 3: Fyll i funktionskod

Lägg till följande paket i projektet:
* [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft.Azure.WebJobs.Extensions.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft .NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Ersätt koden i filen *ProcessDTUpdatetoTSI.cs* med följande kod:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Spara funktionskoden.

### <a name="step-4-publish-the-function-app-to-azure"></a>Steg 4: Publicera funktionsappen till Azure

Publicera projektet med funktionen *ProcessDTUpdatetoTSI.cs* till en funktionsapp i Azure.

Anvisningar om hur du gör detta finns i avsnittet Publicera funktionsappen till [**Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) i artikeln Anvisningar: Konfigurera en *funktion för bearbetning av data.*

Spara funktionsappens namn om du vill använda senare för att konfigurera appinställningar för de två händelsehubben.

### <a name="step-5-security-access-for-the-function-app"></a>Steg 5: Säkerhetsåtkomst för funktionsappen

Tilldela sedan **en åtkomstroll för** funktionen och konfigurera **programinställningarna så att den** kan komma åt din Azure Digital Twins instans. Anvisningar om hur du gör detta [](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) finns i avsnittet Konfigurera säkerhetsåtkomst för funktionsappen i artikeln Anvisningar: Konfigurera en funktion *för bearbetning av data.*

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Steg 6: Konfigurera appinställningar för de två händelsehubben

Nu ska du lägga till miljövariabler i funktionsappens inställningar som gör att den kan komma åt tvillinghubben och tidsseriehubben.

Använd twins hub **primaryConnectionString-värdet** som du sparade tidigare för att skapa en appinställning i funktionsappen som innehåller anslutningssträngen för Twins Hub:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Använd värdet för time series hub **primaryConnectionString** som du sparade tidigare för att skapa en appinställning i funktionsappen som innehåller tidsseriehubbens anslutningssträng:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Skapa och ansluta en Time Series Insights-instans

I det här avsnittet ska du konfigurera en Time Series Insights för att ta emot data från din tidsseriehubb. Mer information om den här processen finns i [*Självstudie: Konfigurera en Azure Time Series Insights Gen2 PAYG-miljö*](../time-series-insights/tutorial-set-up-environment.md). Följ stegen nedan för att skapa en time series insights-miljö.

1. I [Azure Portal](https://portal.azure.com)du efter *Time Series Insights miljöer och* väljer knappen **Lägg** till. Välj följande alternativ för att skapa tidsseriemiljön.

    * **Prenumeration** – Välj din prenumeration.
        - **Resursgrupp** – Välj din resursgrupp.
    * **Miljönamn** – Ange ett namn för tidsseriemiljön.
    * **Plats** – Välj en plats.
    * **Nivå** – Välj **prisnivån Gen2(L1).**
    * **Egenskapsnamn** – Ange **$dtId** (Läs mer om att välja ett ID-värde i [*Metodtips för att välja ett tidsserie-ID*](../time-series-insights/how-to-select-tsid.md)).
    * **Lagringskontonamn** – Ange ett lagringskontonamn.
    * **Aktivera varm lagring** – Lämna det här fältet inställt på *Ja.*

    Du kan lämna standardvärdena för andra egenskaper på den här sidan. Välj knappen **Nästa: Händelsekälla >** händelsekälla.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Skärmbild av Azure Portal för att Time Series Insights miljö. Välj din prenumeration, resursgrupp och plats i respektive listrutan och välj ett namn för din miljö." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Skärmbild av Azure Portal för att Time Series Insights miljö. Prisnivån Gen2(L1) har valts och egenskapsnamnet för tidsserie-ID:t $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. På fliken *Händelsekälla* väljer du följande fält:

   * **Skapa en händelsekälla?** – Välj *Ja.*
   * **Källtyp** – Välj *Händelsehubb.*
   * **Namn** – Ange ett namn för händelsekällan.
   * **Prenumeration** – Välj din Azure-prenumeration.
   * **Namnområde för händelsehubb** – Välj det namnområde som du skapade tidigare i den här artikeln.
   * **Namn på händelsehubb** – Välj **det tidsseriehubbnamn** som du skapade tidigare i den här artikeln.
   * **Namn på händelsehubbåtkomstprincip** – Välj den *autentiseringsregel för tidsseriehubb* som du skapade tidigare i den här artikeln.
   * **Konsumentgrupp för händelsehubb** – *Välj Ny* och ange ett namn för händelsehubbens konsumentgrupp. Välj sedan Lägg *till*.
   * **Egenskapsnamn** – Lämna det här fältet tomt.
    
    Välj knappen **Granska + skapa** för att granska all information. Välj sedan knappen **Granska + skapa igen** för att skapa tidsseriemiljön.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Skärmbild av Azure Portal för att Time Series Insights miljö. Du skapar en händelsekälla med händelsehubbens information ovan. Du skapar också en ny konsumentgrupp." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Skicka IoT-data till Azure Digital Twins

Om du vill börja Time Series Insights data till en plats måste du börja uppdatera de digitala tvillingegenskaperna i den Azure Digital Twins med ändrade datavärden.

Använd följande CLI-kommando för att uppdatera *egenskapen Temperature* på *termostat67-tvillingen* som du lade till i din instans i [avsnittet](#prerequisites) Förutsättningar.

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Upprepa kommandot minst 4** gånger med olika temperaturvärden för att skapa flera datapunkter som kan observeras senare i Time Series Insights miljö.

> [!TIP]
> Om du vill slutföra den här artikeln med live-simulerade data i stället för att manuellt uppdatera [](#prerequisites) digitala tvillingvärden måste du först ha slutfört tipset i avsnittet Förutsättningar för att konfigurera en Azure-funktion som uppdaterar tvillingar från en simulerad enhet.
Efter det kan du köra enheten nu för att börja skicka simulerade data och uppdatera din digitala tvilling genom dataflödet.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualisera dina data i Time Series Insights

Nu bör data flöda till din Time Series Insights instans, redo att analyseras. Följ stegen nedan för att utforska de data som kommer in.

1. I Azure Portal [du](https://portal.azure.com)efter tidsseriemiljöns namn som du skapade tidigare. I menyalternativen till vänster väljer du Översikt *för* att se Time Series Insights-utforskaren *URL.* Välj URL:en för att visa temperaturändringarna som återspeglas Time Series Insights miljön.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Skärmbild av Azure Portal du väljer url Time Series Insights utforskaren på översiktsfliken i din Time Series Insights miljö." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. I utforskaren visas tvillingarna i Azure Digital Twins till vänster. Välj *termostat67-tvillingen,* välj egenskapen *Temperatur och* tryck på Lägg **till**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Skärmbild av Time Series Insights utforskaren för att välja termostat67, välj egenskapstemperatur och tryck på Lägg till." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Du bör nu se de inledande temperaturavläsningarna från termostaten enligt nedan. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Skärmbild av TSI-utforskaren för att visa inledande temperaturdata. Det är en rad med slumpmässiga värden mellan 68 och 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Om du tillåter att en simulering körs mycket längre ser visualiseringen ut ungefär så här:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Skärmbild av TSI-utforskaren där temperaturdata för varje tvilling visas i tre parallella linjer med olika färger." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Nästa steg

De digitala tvillingarna lagras som standard som en platt hierarki i Time Series Insights, men de kan berikas med modellinformation och en hierarki på flera nivåer för organisationen. Mer information om den här processen finns i: 

* [*Självstudie: Definiera och tillämpa en modell*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Du kan skriva anpassad logik för att automatiskt tillhandahålla den här informationen med hjälp av modell- och diagramdata som redan lagras i Azure Digital Twins. Mer information om hur du hanterar, uppgraderar och hämtar information från twins-diagrammet finns i följande referenser:

* [*Så här hanterar du en digital tvilling*](./how-to-manage-twin.md)
* [*Så här frågar du efter tvillingdiagrammet*](./how-to-query-graph.md)