---
title: Integrera med Azure Time Series Insights
titleSuffix: Azure Digital Twins
description: Se så här ställer du in händelse vägar från Azure Digitals dubbla till Azure Time Series Insights.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 4/7/2021
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 256bb80687ffedba9718303710335cce1a582c1d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304013"
---
# <a name="integrate-azure-digital-twins-with-azure-time-series-insights"></a>Integrera Azure Digitals dubbla med Azure Time Series Insights

I den här artikeln får du lära dig hur du integrerar Azure Digitals dubbla med [Azure Time Series Insights (TSD)](../time-series-insights/overview-what-is-tsi.md).

Lösningen som beskrivs i den här artikeln gör att du kan samla in och analysera historiska data om din IoT-lösning. Azure Digital Twins är ett bra alternativ för att mata in data i Time Series Insights, eftersom det gör att du kan korrelera flera dataströmmar och standardisera informationen innan du skickar den till Time Series Insights.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan skapa en relation med Time Series Insights måste du konfigurera följande resurser:
* En **IoT-hubb**. Instruktioner finns i avsnittet [*skapa en IoT Hub*](../iot-hub/quickstart-send-telemetry-cli.md#create-an-iot-hub) i snabb starten för att *skicka telemetri i IoT Hub* .
* En **digital Azure-instans**.
Anvisningar finns i anvisningar [*: Konfigurera en digital Azure-instans och autentisering*](./how-to-set-up-instance-portal.md).
* En **modell och en dubbla i Azure Digitals-instansen**.
Du behöver uppdatera den dubbla informationen några gånger för att se att data spåras i Time Series Insights. Mer information finns i artikeln [*lägga till en modell och ett*](how-to-ingest-iot-hub-data.md#add-a-model-and-twin) avsnitt i artikeln om *att: mata in IoT Hub* .

> [!TIP]
> I den här artikeln uppdateras de ändrade digitala dubbla värdena som visas i Time Series Insights manuellt för enkelhetens skull. Men om du vill slutföra den här artikeln med försimulerade data kan du konfigurera en Azure-funktion som uppdaterar digitala enheter baserat på IoT telemetri-händelser från en simulerad enhet. Anvisningar finns i så [*här gör du: mata in IoT Hub data*](how-to-ingest-iot-hub-data.md), inklusive de sista stegen för att köra enhets simulatorn och kontrol lera att data flödet fungerar.
>
> Om du senare vill visa var du kan börja köra enhets simulatorn kan du leta efter ett annat tips, i stället för att skicka manuella digitala dubbla uppdaterings kommandon.


## <a name="solution-architecture"></a>Lösningsarkitektur

Du kommer att bifoga Time Series Insights till Azure Digitals dubbla steg genom sökvägen nedan.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-integrate-time-series-insights/diagram-simple.png" alt-text="Diagram över Azure-tjänster i ett scenario från slut punkt till slut punkt, som markerar Time Series Insights." lightbox="media/how-to-integrate-time-series-insights/diagram-simple.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

## <a name="create-event-hub-namespace"></a>Skapa namnområde för en händelsehubb

Innan du skapar händelse hubbarna måste du först skapa ett namn område för Event Hub som tar emot händelser från din Azure Digitals-instans. Du kan antingen använda Azure CLI-instruktionerna nedan eller använda Azure Portal: [*snabb start: skapa en händelsehubben med Azure Portal*](../event-hubs/event-hubs-create.md). Om du vill se vilka regioner som stöder Event Hub kan du gå till [*Azure-produkter som är tillgängliga efter region*](https://azure.microsoft.com/global-infrastructure/services/?products=event-hubs).

```azurecli-interactive
az eventhubs namespace create --name <name-for-your-event-hubs-namespace> --resource-group <your-resource-group> -l <region>
```

> [!TIP]
> Om du får ett fel meddelande `BadRequest: The specified service namespace is invalid.` kontrollerar du att namnet som du har valt för ditt namn område uppfyller namngivnings kraven som beskrivs i det här referens dokumentet: [skapa namnrymd](/rest/api/servicebus/create-namespace).

Du kommer att använda den här namn rymden för Event Hub för att hålla de två händelse nav som behövs för den här artikeln:

  1. **Flätar hubb** – händelsehubben för att ta emot dubbla ändrings händelser
  2. **Time Series Hub** – Event Hub för att strömma händelser till Time Series Insights

I nästa avsnitt får du stegvisa anvisningar om hur du skapar och konfigurerar dessa hubbar i namn området för händelsehubben.

## <a name="create-twins-hub"></a>Skapa dubbla hubbar

Den första händelsehubben som du skapar i den här artikeln är den **dubbla hubben**. Den här händelsehubben tar emot dubbla ändrings händelser från Azure Digitals dubbla.
Om du vill konfigurera den sammanflätade hubben utför du följande steg i det här avsnittet:

1. Skapa en flätad hubb
2. Skapa en auktoriseringsregel för att kontrol lera behörigheterna för hubben
3. Skapa en slut punkt i Azure Digital-dubbla, som använder auktoriseringsregeln för att komma åt hubben
4. Skapa en väg i Azure Digitals dubbla, som skickar dubbla uppdaterings händelser till slut punkten och anslutna dubbla nav
5. Hämta den sammanflätade hubbens anslutnings sträng

Skapa den **dubbla hubben** med följande CLI-kommando. Ange ett namn för din flätade hubb.

```azurecli-interactive
az eventhubs eventhub create --name <name-for-your-twins-hub> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-above>
```

### <a name="create-twins-hub-authorization-rule"></a>Skapa en regel för att skapa en mellanliggande hubb

Skapa en [auktoriseringsregel](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) med behörigheter för att skicka och ta emot. Ange ett namn för regeln.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-twins-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above>
```

### <a name="create-twins-hub-endpoint"></a>Skapa flätad Hub-slutpunkt

Skapa en Azure Digital- [slutpunkt](concepts-route-events.md#create-an-endpoint) som länkar din händelsehubben till din Azure Digital-instansen. Ange ett namn för din-Hubbs slut punkt.

```azurecli-interactive
az dt endpoint create eventhub -n <your-Azure-Digital-Twins-instance-name> --eventhub-resource-group <your-resource-group> --eventhub-namespace <your-event-hubs-namespace-from-earlier> --eventhub <your-twins-hub-name-from-above> --eventhub-policy <your-twins-hub-auth-rule-from-earlier> --endpoint-name <name-for-your-twins-hub-endpoint>
```

### <a name="create-twins-hub-event-route"></a>Skapa sammanflätade Hubbs händelse väg

Azure Digitals dubbla instanser kan generera [dubbla uppdaterings händelser](how-to-interpret-event-data.md) när en dubbel status uppdateras. I det här avsnittet skapar du en Azure Digitals- **händelse väg** som dirigerar dessa uppdaterings händelser till den dubbla hubben för vidare bearbetning.

Skapa en [väg](concepts-route-events.md#create-an-event-route) i Azure Digitals flätas för att skicka dubbla uppdaterings händelser till din slut punkt ovan. Filtret i den här vägen tillåter endast att dubbla uppdaterings meddelanden skickas till din slut punkt. Ange ett namn för händelse vägen för dubblar hubb.

```azurecli-interactive
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <your-twins-hub-endpoint-from-above> --route-name <name-for-your-twins-hub-event-route> --filter "type = 'Microsoft.DigitalTwins.Twin.Update'"
```

### <a name="get-twins-hub-connection-string"></a>Hämta dubbla nav anslutnings sträng

Hämta den [sammanflätade Event Hub-anslutningssträngen](../event-hubs/event-hubs-get-connection-string.md)med hjälp av de auktoriseringsregler som du skapade ovan för den dubbla hubben.

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hubs-namespace-from-earlier> --eventhub-name <your-twins-hub-from-above> --name <your-twins-hub-auth-rule-from-earlier>
```
Anteckna **primaryConnectionString** -värdet från resultatet för att konfigurera inställningen för den dubbla Hub-appen senare i den här artikeln.

## <a name="create-time-series-hub"></a>Skapa Time Series Hub

Den andra händelsehubben som du skapar i den här artikeln är **Time Series Hub**. Det här är en händelsehubben som strömmar de digitala Azure-händelserna till Time Series Insights.
Om du vill konfigurera Time Series Hub slutför du följande steg:

1. Skapa Time Series Hub
2. Skapa en auktoriseringsregel för att kontrol lera behörigheterna för hubben
3. Hämta Time Series Hub-anslutningssträngen

När du senare skapar Time Series Insights-instansen ansluter du tids serie navet som händelse källa för Time Series Insights-instansen.

Skapa **Time Series Hub** med följande kommando. Ange ett namn för Time Series Hub.

```azurecli-interactive
 az eventhubs eventhub create --name <name-for-your-time-series-hub> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier>
```

### <a name="create-time-series-hub-authorization-rule"></a>Skapa en auktoriseringsregel för Time Series Hub

Skapa en [auktoriseringsregel](/cli/azure/eventhubs/eventhub/authorization-rule#az-eventhubs-eventhub-authorization-rule-create) med behörigheter för att skicka och ta emot. Ange ett namn för Time Series Hub auth-regeln.

```azurecli-interactive
az eventhubs eventhub authorization-rule create --rights Listen Send --name <name-for-your-time-series-hub-auth-rule> --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-above>
```

### <a name="get-time-series-hub-connection-string"></a>Hämta Time Series Hub-anslutningssträng

Hämta [Time Series Hub-anslutningssträngen](../event-hubs/event-hubs-get-connection-string.md)med hjälp av de auktoriseringsregler som du skapade ovan för Time Series Hub:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group <your-resource-group> --namespace-name <your-event-hub-namespace-from-earlier> --eventhub-name <your-time-series-hub-name-from-earlier> --name <your-time-series-hub-auth-rule-from-earlier>
```
Anteckna **primaryConnectionString** -värdet från resultatet för att konfigurera Time Series Hub-appens inställning senare i den här artikeln.

Tänk också på följande värden för att använda dem senare för att skapa en Time Series Insights-instans.
* Namnområde för händelsehubb
* NAV namn för Time Series
* Authentication-regel för Time Series Hub

## <a name="create-a-function"></a>Skapa en funktion

I det här avsnittet ska du skapa en Azure-funktion som konverterar dubbla uppdaterings händelser från sina ursprungliga formulär som JSON-korrigeringsfiler till JSON-objekt, som endast innehåller uppdaterade och tillagda värden från dina dubbla.

### <a name="step-1-create-function-app"></a>Steg 1: skapa Function-app

Skapa först ett nytt Function app-projekt i Visual Studio. Instruktioner för hur du gör detta finns i avsnittet [**skapa en Function-app i Visual Studio**](how-to-create-azure-function.md#create-a-function-app-in-visual-studio) i artikeln *instruktion: Konfigurera en funktion för bearbetning av data* .

### <a name="step-2-add-a-new-function"></a>Steg 2: Lägg till en ny funktion

Skapa en ny Azure-funktion med namnet *ProcessDTUpdatetoTSI. cs* för att uppdatera händelserna för telemetri av enhet till Time Series Insights. Funktions typen är **Event Hub-utlösare**.

:::image type="content" source="media/how-to-integrate-time-series-insights/create-event-hub-trigger-function.png" alt-text="Skärm bild av Visual Studio för att skapa en ny Azure-funktion av typen Event Hub-utlösare.":::

### <a name="step-3-fill-in-function-code"></a>Steg 3: Fyll i funktions koden

Lägg till följande paket i projektet:
* [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. Azure. WebJobs. Extensions. EventHubs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/)
* [Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/)

Ersätt koden i filen *ProcessDTUpdatetoTSI. cs* med följande kod:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/updateTSI.cs":::

Spara funktions koden.

### <a name="step-4-publish-the-function-app-to-azure"></a>Steg 4: publicera Function-appen till Azure

Publicera projektet med funktionen *ProcessDTUpdatetoTSI. cs* i en Function-app i Azure.

Instruktioner för hur du gör detta finns i avsnittet [**publicera Function-appen till Azure**](how-to-create-azure-function.md#publish-the-function-app-to-azure) med *instruktionen så här konfigurerar du en funktion för bearbetning av data* .

Spara appens funktions namn för att använda senare för att konfigurera appinställningar för de två händelse hubbarna.

### <a name="step-5-security-access-for-the-function-app"></a>Steg 5: säkerhets åtkomst för Function-appen

Tilldela sedan **en åtkomst roll** för funktionen och **Konfigurera program inställningarna** så att de kan komma åt din Azure Digital-instansen. Instruktioner för hur du gör detta finns i avsnittet [**Konfigurera säkerhets åtkomst för funktionen app**](how-to-create-azure-function.md#set-up-security-access-for-the-function-app) för *instruktionen instruktion: Konfigurera en funktion för bearbetning av data* .

### <a name="step-6-configure-app-settings-for-the-two-event-hubs"></a>Steg 6: Konfigurera appinställningar för de två händelse hubbarna

Härnäst ska du lägga till miljövariabler i funktions appens inställningar som ger åtkomst till den dubbla hubben och tids serie navet.

Använd **primaryConnectionString** -värdet med dubbla nav som du sparade tidigare för att skapa en app-inställning i din Function-app som innehåller anslutnings strängen för den dubbla-hubben:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-Twins=<your-twins-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

Använd Time Series Hub **primaryConnectionString** -värdet som du sparade tidigare för att skapa en app-inställning i din Function-app som innehåller tids seriens hubb anslutnings sträng:

```azurecli-interactive
az functionapp config appsettings set --settings "EventHubAppSetting-TSI=<your-time-series-hub-primaryConnectionString>" -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

## <a name="create-and-connect-a-time-series-insights-instance"></a>Skapa och ansluta en Time Series Insights-instans

I det här avsnittet ska du konfigurera Time Series Insights instans för att ta emot data från din Time Series Hub. Mer information om den här processen finns i [*Självstudier: Konfigurera en Azure Time Series Insights Gen2 PAYG-miljö*](../time-series-insights/tutorial-set-up-environment.md). Följ stegen nedan för att skapa en Time Series Insights-miljö.

1. Sök efter *Time Series Insights miljöer* i [Azure Portal](https://portal.azure.com)och välj knappen **Lägg till** . Välj följande alternativ för att skapa en Time Series-miljö.

    * **Prenumeration** – välj din prenumeration.
        - **Resurs grupp** – välj din resurs grupp.
    * **Miljö namn** – ange ett namn för din tids serie miljö.
    * **Plats** – Välj en plats.
    * **Nivå** – Välj pris nivå för **Gen2 (L1)** .
    * **Egenskaps namn** – ange **$dtId** (Läs mer om att välja ett ID-värde i [*metod tips för att välja ett Time Series-ID*](../time-series-insights/how-to-select-tsid.md)).
    * **Lagrings konto namn** – ange ett lagrings konto namn.
    * **Aktivera varmt Arkiv** – lämna fältet inställt på *Ja*.

    Du kan lämna standardvärden för andra egenskaper på den här sidan. Välj **Nästa: knappen för händelse källan >** .

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png" alt-text="Skärm bild av Azure Portal att skapa Time Series Insightss miljö. Välj din prenumeration, resurs grupp och plats i respektive listruta och välj ett namn för din miljö." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-1.png":::
        
    :::image type="content" source="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png" alt-text="Skärm bild av Azure Portal att skapa Time Series Insightss miljö. Pris nivån Gen2 (L1) har valts och egenskaps namnet för Time Series ID är $dtId." lightbox="media/how-to-integrate-time-series-insights/create-time-series-insights-environment-2.png":::

2. På fliken *händelse källa* väljer du följande fält:

   * **Vill du skapa en händelse källa?** -Välj *Ja*.
   * **Källtyp** – Välj *Event Hub*.
   * **Namn** – ange ett namn för din händelse källa.
   * **Prenumeration** – välj din Azure-prenumeration.
   * **Event Hub-namnområde** – Välj det namn område som du skapade tidigare i den här artikeln.
   * Händelsehubben- **namn** – Välj det **Hubbs namn för Time Series** som du skapade tidigare i den här artikeln.
   * **Namn på åtkomst princip för händelsehubben** – Välj den *tids serie Hub-aukt-regel* som du skapade tidigare i den här artikeln.
   * **Konsument grupp för Event Hub** – Välj *ny* och ange ett namn för konsument gruppen för Event Hub. Välj sedan *Lägg till*.
   * **Egenskaps namn** – lämna fältet tomt.
    
    Klicka på knappen **Granska + skapa** för att granska all information. Välj sedan knappen **Granska + skapa** igen för att skapa en Time Series-miljö.

    :::image type="content" source="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png" alt-text="Skärm bild av Azure Portal att skapa Time Series Insightss miljö. Du skapar en händelse källa med information om händelsehubben från ovan. Du skapar också en ny konsument grupp." lightbox="media/how-to-integrate-time-series-insights/create-tsi-environment-event-source.png":::

## <a name="send-iot-data-to-azure-digital-twins"></a>Skicka IoT-data till Azure Digitals, dubbla

För att kunna börja skicka data till Time Series Insights måste du börja uppdatera de digitala dubbla egenskaperna i Azure Digitals med ändra data värden.

Använd följande CLI-kommando för att uppdatera *temperatur* egenskapen på den *thermostat67* som du har lagt till i din instans i avsnittet [krav](#prerequisites) .

```azurecli-interactive
az dt twin update -n <your-azure-digital-twins-instance-name> --twin-id thermostat67 --json-patch '{"op":"replace", "path":"/Temperature", "value": 20.5}'
```

**Upprepa kommandot minst fyra gånger med olika temperatur värden** för att skapa flera data punkter som kan observeras senare i Time Series Insightss miljön.

> [!TIP]
> Om du vill slutföra den här artikeln med försimulerade data i stället för att manuellt uppdatera de digitala dubbla värdena, kontrollerar du först att du har slutfört TIPSet från avsnittet [*krav*](#prerequisites) för att konfigurera en Azure-funktion som uppdaterar från en simulerad enhet.
Sedan kan du köra enheten nu för att börja skicka simulerade data och uppdatera din digitala data flöde.

## <a name="visualize-your-data-in-time-series-insights"></a>Visualisera dina data i Time Series Insights

Nu bör data flöda till Time Series Insights-instansen som är redo att analyseras. Följ stegen nedan för att utforska de data som kommer i.

1. I [Azure Portal](https://portal.azure.com)söker du efter namnet på din tids serie miljö som du skapade tidigare. I meny alternativen till vänster väljer du *Översikt* för att se *URL: en för Time Series Insights Explorer*. Välj URL: en för att visa temperatur ändringarna reflekterade i Time Series Insightss miljön.

    :::image type="content" source="media/how-to-integrate-time-series-insights/view-environment.png" alt-text="Skärm bild av Azure Portal för att välja URL: en för Time Series Insights Explorer på fliken Översikt i din Time Series Insights miljö." lightbox="media/how-to-integrate-time-series-insights/view-environment.png":::

2. I Utforskaren visas de dubbla i Azure Digitals-instansen som visas till vänster. Välj *thermostat67* , Välj egenskapen *temperatur* och tryck på **Lägg till**.

    :::image type="content" source="media/how-to-integrate-time-series-insights/add-data.png" alt-text="Skärm bild av Time Series Insights Explorer för att välja thermostat67, Välj egenskaps temperatur och tryck på Lägg till." lightbox="media/how-to-integrate-time-series-insights/add-data.png":::

3. Du bör nu se de första temperatur avläsningarna från din termostat, som du ser nedan. 

    :::image type="content" source="media/how-to-integrate-time-series-insights/initial-data.png" alt-text="Skärm bild av TSD-Utforskaren för att visa den ursprungliga temperatur informationen. Det är en rad med slumpmässiga värden mellan 68 och 85" lightbox="media/how-to-integrate-time-series-insights/initial-data.png":::

Om du tillåter en simulering att köras mycket längre, ser visualiseringen ut ungefär så här:

:::image type="content" source="media/how-to-integrate-time-series-insights/day-data.png" alt-text="Skärm bild av TSD-Utforskaren där temperatur data för varje snöre är grafade i tre parallella rader med olika färger." lightbox="media/how-to-integrate-time-series-insights/day-data.png":::

## <a name="next-steps"></a>Nästa steg

De digitala multiplarna lagras som standard som en platt hierarki i Time Series Insights, men de kan berikas med modell information och en hierarki med flera nivåer för organisationen. Läs mer om den här processen genom att läsa: 

* [*Självstudie: definiera och tillämpa en modell*](../time-series-insights/tutorial-set-up-environment.md#define-and-apply-a-model) 

Du kan skriva anpassad logik för att automatiskt tillhandahålla den här informationen med modell-och diagram data som redan finns lagrade i Azure Digitals. Läs mer om hur du hanterar, uppgraderar och hämtar information från diagrammet med dubbla diagram i följande referenser:

* [*Anvisningar: hantera en digital, dubbel*](./how-to-manage-twin.md)
* [*Anvisningar: fråga det dubbla diagrammet*](./how-to-query-graph.md)