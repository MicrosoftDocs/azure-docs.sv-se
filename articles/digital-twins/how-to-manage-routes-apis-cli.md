---
title: 'Hantera slut punkter och vägar (API: er och CLI)'
titleSuffix: Azure Digital Twins
description: Se hur du konfigurerar och hanterar slut punkter och händelse vägar för Azure Digitals dubbla data.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 11/18/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: b0e440f9fe0b7ce1591318362ac0419b9aa01baf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102433309"
---
# <a name="manage-endpoints-and-routes-in-azure-digital-twins-apis-and-cli"></a>Hantera slut punkter och vägar i Azure Digitals dubbla (API: er och CLI)

[!INCLUDE [digital-twins-route-selector.md](../../includes/digital-twins-route-selector.md)]

I Azure Digitals dubbla, kan du dirigera [händelse meddelanden](how-to-interpret-event-data.md) till underordnade tjänster eller anslutna beräknings resurser. Det gör du genom att först konfigurera **slutpunkter** som kan ta emot händelserna. Du kan sedan skapa  [**händelse vägar**](concepts-route-events.md) som anger vilka händelser som genereras av digitala Digital-meddelanden i Azure som levereras till vilka slut punkter.

Den här artikeln vägleder dig genom processen för att skapa slut punkter och vägar med [REST-API: er](/rest/api/azure-digitaltwins/), [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client)och [Azure Digitals flätat CLI](how-to-use-cli.md).

Du kan också hantera slut punkter och vägar med [Azure Portal](https://portal.azure.com). En version av den här artikeln som använder portalen i stället finns i [*så här gör du: hantera slut punkter och vägar (portal)*](how-to-manage-routes-portal.md).

## <a name="prerequisites"></a>Förutsättningar

- Du behöver ett **Azure-konto** (du kan ställa in ett kostnads fritt [här](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- Du behöver en **Azure Digitals-instans** i din Azure-prenumeration. Om du inte redan har en instans kan du skapa en med hjälp av anvisningarna i [*instruktion: Konfigurera en instans och autentisering*](how-to-set-up-instance-cli.md). Ha följande värden från installations programmet som är praktiskt att använda senare i den här artikeln:
    - Instansnamn
    - Resursgrupp

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]
    
## <a name="create-an-endpoint-for-azure-digital-twins"></a>Skapa en slut punkt för Azure Digitals dubbla

Detta är de typer av slut punkter som stöds som du kan skapa för din instans:
* [Event Grid](../event-grid/overview.md)
* [Event Hubs](../event-hubs/event-hubs-about.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Mer information om olika typer av slut punkter finns i [*Välj mellan Azure Messaging Services*](../event-grid/compare-messaging-services.md).

I det här avsnittet beskrivs hur du skapar dessa slut punkter med hjälp av Azure CLI. Du kan också hantera slut punkter med [DigitalTwinsEndpoint Control plan-API: er](/rest/api/digital-twins/controlplane/endpoints).

[!INCLUDE [digital-twins-endpoint-resources.md](../../includes/digital-twins-endpoint-resources.md)]

### <a name="create-the-endpoint"></a>Skapa slut punkten

När du har skapat slut punkts resurserna kan du använda dem för en Azure digital-slutpunkt. I följande exempel visas hur du skapar slut punkter med hjälp av kommandot [AZ DT Endpoint Create](/cli/azure/ext/azure-iot/dt/endpoint/create) för [Azure Digitals flätat CLI](how-to-use-cli.md). Ersätt plats hållarna i kommandona med information om dina egna resurser.

Så här skapar du en Event Grid slut punkt:

```azurecli-interactive
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

Så här skapar du en Event Hubs slut punkt (nyckelbaserad autentisering):
```azurecli-interactive
az dt endpoint create eventhub --endpoint-name <Event-Hub-endpoint-name> --eventhub-resource-group <Event-Hub-resource-group> --eventhub-namespace <Event-Hub-namespace> --eventhub <Event-Hub-name> --eventhub-policy <Event-Hub-policy> -n <your-Azure-Digital-Twins-instance-name>
```

Så här skapar du en Service Bus avsnitts slut punkt (nyckelbaserad autentisering):
```azurecli-interactive 
az dt endpoint create servicebus --endpoint-name <Service-Bus-endpoint-name> --servicebus-resource-group <Service-Bus-resource-group-name> --servicebus-namespace <Service-Bus-namespace> --servicebus-topic <Service-Bus-topic-name> --servicebus-policy <Service-Bus-topic-policy> -n <your-Azure-Digital-Twins-instance-name>
```

När du har kört de här kommandona är event Grid, Event Hub eller Service Bus ämnet tillgängligt som en slut punkt i Azure Digitals, under det namn som du angav med `--endpoint-name` argumentet. Du använder vanligt vis det namnet som mål för en **händelse väg**, som du skapar [senare i den här artikeln](#create-an-event-route).

#### <a name="create-an-endpoint-with-identity-based-authentication"></a>Skapa en slut punkt med Identity-baserad autentisering

Du kan också skapa en slut punkt med Identity-baserad autentisering om du vill använda slut punkten med en [hanterad identitet](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Det här alternativet är endast tillgängligt för Event Hub-och Service Bus-typ slut punkter (det stöds inte för Event Grid).

CLI-kommandot för att skapa den här typen av slut punkt är nedan. Du behöver följande värden för att ansluta till plats hållarna i kommandot:
* Azure-resurs-ID för Azure Digital-instansen
* ett slut punkts namn
* en slut punkts typ
* slut punkts resursens namnrymd
* namnet på händelsehubben eller Service Bus ämnet
* platsen för Azure Digitals dubbla instansen

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

### <a name="create-an-endpoint-with-dead-lettering"></a>Skapa en slut punkt med obeställbara meddelanden

När en slut punkt inte kan leverera en händelse inom en viss tids period eller när händelsen försöker leverera händelsen ett visst antal gånger, kan den skicka den ej levererade händelsen till ett lagrings konto. Den här processen kallas för **obeställbara meddelanden**.

Slut punkter med aktivering med obeställbara meddelanden kan konfigureras med Azure Digitals flätade [CLI](how-to-use-cli.md) eller [kontroll Plans-API: er](how-to-use-apis-sdks.md#overview-control-plane-apis).

Mer information om obeställbara meddelanden finns i [*begrepp: händelse vägar*](concepts-route-events.md#dead-letter-events). För instruktioner om hur du konfigurerar en slut punkt med obeställbara meddelanden fortsätter du genom resten av det här avsnittet.

#### <a name="set-up-storage-resources"></a>Konfigurera lagrings resurser

Innan du anger platsen för obeställbara meddelanden måste du ha ett [lagrings konto](../storage/common/storage-account-create.md?tabs=azure-portal) med en [behållare](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) konfigurerad i ditt Azure-konto. 

Du ska ange URI: n för den här behållaren när du skapar slut punkten senare. Platsen för obeställbara meddelanden kommer att anges till slut punkten som en container-URI med en [SAS-token](../storage/common/storage-sas-overview.md). Token behöver `write` behörighet för mål behållaren i lagrings kontot. Den fullständigt formaterade **SAS-URI: n för obeställbara meddelanden** är i formatet: `https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>` .

Följ stegen nedan för att konfigurera de här lagrings resurserna på ditt Azure-konto för att förbereda för att konfigurera slut punkts anslutningen i nästa avsnitt.

1. Följ stegen i [*skapa ett lagrings konto*](../storage/common/storage-account-create.md?tabs=azure-portal) för att skapa ett **lagrings konto** i din Azure-prenumeration. Anteckna namnet på det lagrings konto som du vill använda senare.
2. Följ stegen i [*skapa en behållare*](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) för att skapa en **behållare** i det nya lagrings kontot. Anteckna namnet på behållaren som ska användas senare.
3. Skapa sedan en **SAS-token** för ditt lagrings konto som slut punkten kan använda för att komma åt den. Börja med att navigera till ditt lagrings konto i [Azure Portal](https://ms.portal.azure.com/#home) (du kan hitta det med namn med Portal Sök fältet).
4. På sidan lagrings konto väljer du länken _signatur för delad åtkomst_ i det vänstra navigerings fältet för att börja konfigurera SAS-token.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png" alt-text="Sidan lagrings konto i Azure Portal" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-1.png":::

1. På *sidan signatur för delad åtkomst* under *tillåtna tjänster* och *tillåtna resurs typer* väljer du de inställningar som du vill använda. Du måste markera minst en ruta i varje kategori. Under *tillåtna behörigheter* väljer du **Skriv** (du kan också välja andra behörigheter om du vill).
1. Ange de värden som du vill använda för återstående inställningar.
1. När du är klar väljer du knappen _generera SAS och anslutnings sträng_ för att generera SAS-token. 

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png" alt-text="Sidan lagrings konto i Azure Portal visar alla inställnings val för att generera en SAS-token och markera knappen &quot;generera SAS-och anslutnings sträng&quot;" lightbox="./media/how-to-manage-routes-apis-cli/generate-sas-token-2.png"::: 

1. Detta genererar flera SAS-och anslutnings sträng värden längst ned på samma sida, under inställnings alternativen. Bläddra nedåt för att visa värdena och Använd ikonen *Kopiera till Urklipp* för att kopiera värdet **SAS-token** . Spara den för att använda senare.

    :::image type="content" source="./media/how-to-manage-routes-apis-cli/copy-sas-token.png" alt-text="Kopiera SAS-token som ska användas i hemligheten för obeställbara meddelanden." lightbox="./media/how-to-manage-routes-apis-cli/copy-sas-token.png":::
    
#### <a name="create-the-dead-letter-endpoint"></a>Skapa slut punkten för obeställbara meddelanden

Om du vill skapa en slut punkt där obeställbara meddelanden har Aktiver ATS lägger du till följande död bokstavs parameter i kommandot [AZ DT Endpoint Create](/cli/azure/ext/azure-iot/dt/endpoint/create) för [Azure Digitals CLI](how-to-use-cli.md).

Värdet för parametern är den SAS- **URI för obeställbara meddelanden** som skapats av det lagrings konto namn, behållare namn och SAS-token som du samlade in i [föregående avsnitt](#set-up-storage-resources). Den här parametern skapar slut punkten med nyckelbaserad autentisering.

```azurecli
--deadletter-sas-uri https://<storage-account-name>.blob.core.windows.net/<container-name>?<SAS-token>
```

Lägg till den här parametern i slutet av kommandona för att skapa slut punkter från avsnittet [*skapa slut punkten*](#create-the-endpoint) tidigare för att skapa en slut punkt av önskad typ som har aktiverat obeställbara meddelanden.

Du kan också skapa slut punkter för obeställbara meddelanden med hjälp av [Azure Digitals kontroll Plans-API: er](how-to-use-apis-sdks.md#overview-control-plane-apis) i stället för cli. Det gör du genom att läsa [DigitalTwinsEndpoint-dokumentationen](/rest/api/digital-twins/controlplane/endpoints/digitaltwinsendpoint_createorupdate) för att se hur du strukturerar begäran och lägger till parametrarna för obeställbara meddelanden.

#### <a name="create-a-dead-letter-endpoint-with-identity-based-authentication"></a>Skapa en slut punkt för obeställbara meddelanden med Identity-baserad autentisering

Du kan också skapa en slut punkt för obeställbara meddelanden som har identitets-baserad autentisering för att använda slut punkten med en [hanterad identitet](concepts-security.md#managed-identity-for-accessing-other-resources-preview). Det här alternativet är endast tillgängligt för Event Hub-och Service Bus-typ slut punkter (det stöds inte för Event Grid).

Om du vill skapa den här typen av slut punkt använder du samma CLI-kommando från tidigare för att [skapa en slut punkt med Identity-baserad autentisering](#create-an-endpoint-with-identity-based-authentication), med ett extra fält i JSON-nyttolasten för en `deadLetterUri` .

Här är de värden du behöver för att ansluta till plats hållarna i kommandot:
* Azure-resurs-ID för Azure Digital-instansen
* ett slut punkts namn
* en slut punkts typ
* slut punkts resursens namnrymd
* namnet på händelsehubben eller Service Bus ämnet
* **SAS URI** -information för obeställbara meddelanden: lagrings konto namn, behållar namn
* platsen för Azure Digitals dubbla instansen

```azurecli-interactive
az resource create --id <Azure-Digital-Twins-instance-Azure-resource-ID>/endpoints/<endpoint-name> --properties '{\"properties\": { \"endpointType\": \"<endpoint-type>\", \"authenticationType\": \"IdentityBased\", \"endpointUri\": \"sb://<endpoint-namespace>.servicebus.windows.net\", \"entityPath\": \"<name-of-event-hub-or-Service-Bus-topic>\", \"deadLetterUri\": \"https://<storage-account-name>.blob.core.windows.net/<container-name>\"}, \"location\":\"<instance-location>\" }' --is-full-object
```

#### <a name="message-storage-schema"></a>Lagrings schema för meddelanden

När slut punkten med obeställbara meddelanden har kon figurer ATS lagras meddelanden med obeställbara meddelanden i följande format i ditt lagrings konto:

`{container}/{endpoint-name}/{year}/{month}/{day}/{hour}/{event-ID}.json`

Meddelanden med obeställbara meddelanden matchar schemat för den ursprungliga händelsen som var avsedd att levereras till den ursprungliga slut punkten.

Här är ett exempel på ett meddelande om obeställbara meddelanden för ett [dubbelt skapande meddelande](how-to-interpret-event-data.md#digital-twin-life-cycle-notifications):

```json
{
  "specversion": "1.0",
  "id": "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "<your-instance>.api.<your-region>.da.azuredigitaltwins-test.net",
  "data": {
    "$dtId": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "$etag": "W/\"xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
    "TwinData": "some sample",
    "$metadata": {
      "$model": "dtmi:test:deadlettermodel;1",
      "room": {
        "lastUpdateTime": "2020-10-14T01:11:49.3576659Z"
      }
    }
  },
  "subject": "<yourInstance>xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "time": "2020-10-14T01:11:49.3667224Z",
  "datacontenttype": "application/json",
  "traceparent": "00-889a9094ba22b9419dd9d8b3bfe1a301-f6564945cb20e94a-01"
}
```

## <a name="create-an-event-route"></a>Skapa en händelse väg

**Förutsättning**: du måste skapa slut punkter enligt beskrivningen ovan i den här artikeln innan du kan gå vidare till skapa en väg. Du kan fortsätta att skapa en händelse väg när slut punkterna har koner ATS.

> [!NOTE]
> Om du nyligen har distribuerat dina slut punkter kontrollerar du att de är klara med distributionen **innan** du försöker använda dem för en ny händelse väg. Om väg distributionen Miss lyckas eftersom slut punkterna inte är klara, väntar du några minuter och försöker igen.
>
> Om du använder skript för det här flödet kanske du vill ta hänsyn till det genom att skapa i 2-3 minuter vänte tid för slut punkts tjänsten för att slutföra distributionen innan du går vidare till väg installationen.

Om du faktiskt vill skicka data från digitala Azure-sändningar till en slut punkt måste du definiera en **händelse väg**. Händelse vägar används för att ansluta händelse flödet i systemet och till underordnade tjänster.

En flödes definition kan innehålla följande element:
* Det väg namn som du vill använda
* Namnet på den slut punkt som du vill använda
* Ett filter som definierar vilka händelser som skickas till slutpunkten 

Om det inte finns något väg namn dirigeras inga meddelanden utanför Azures digitala dubbla. Om det finns ett väg namn och filtret är `true` , dirigeras alla meddelanden till slut punkten. Om det finns ett flödes namn och ett annat filter läggs till filtreras meddelandena utifrån filtret.

En väg bör tillåta att flera meddelanden och händelse typer väljs. 

Händelse vägar kan skapas med Azure Digitals dubbla [ **EventRoutes** data Plans-API: er](/rest/api/digital-twins/dataplane/eventroutes) eller [ **AZ DT Route** CLI-kommandon](/cli/azure/ext/azure-iot/dt/route). Resten av det här avsnittet beskriver hur du skapar processen.

### <a name="create-routes-with-the-apis-and-c-sdk"></a>Skapa vägar med API: er och C# SDK

Ett sätt att definiera händelse vägar är med [API: er för data planet](how-to-use-apis-sdks.md#overview-data-plane-apis). I exemplen i det här avsnittet används [.net (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client).

`CreateOrReplaceEventRouteAsync` är SDK-anropet som används för att lägga till en händelse väg. Här är ett exempel på användningen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::
    
> [!TIP]
> Alla SDK-funktioner ingår i synkrona och asynkrona versioner.

#### <a name="event-route-sample-sdk-code"></a>SDK-kod för exempel på händelse väg

Följande exempel metod visar hur du skapar, visar och tar bort en händelse väg med C# SDK:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="FullEventRouteSample":::

### <a name="create-routes-with-the-cli"></a>Skapa vägar med CLI

Vägar kan också hanteras med [AZ DT Route](/cli/azure/ext/azure-iot/dt/route) -kommandon för Azure Digitals flätat-cli. 

Mer information om hur du använder CLI och vilka kommandon som är tillgängliga finns i [*How-to: använda Azure Digitals flätade CLI*](how-to-use-cli.md).

## <a name="filter-events"></a>Filtrera händelser

Utan filtrering tar slut punkter emot flera olika händelser från Azures digitala dubbla:
* Telemetri som har utlösts av [digitala sammanflätade](concepts-twins-graph.md) tjänster med Azure Digitals dubbla tjänst-API
* Dubbla egenskaps ändrings meddelanden, utlöses vid egenskaps ändringar för alla dubbla i Azure Digitals-instansen
* Livs cykel händelser, utlöses när de är uppdelade eller relationer skapade eller raderas

Du kan begränsa vilka händelser som ska skickas genom att lägga till ett **filter** för en slut punkt till händelse vägen.

Om du vill lägga till ett filter, kan du använda en skicka-begäran till *https://{Your-Azure-Digital-posters-hostname}/eventRoutes/{Event Route-Name}? API-version = 2020-10-31* med följande text:

:::code language="json" source="~/digital-twins-docs-samples/api-requests/filter.json":::

Här följer de väg filter som stöds. Använd informationen i kolumnen *filtrera text schema* för att ersätta `<filter-text>` plats hållaren i begär ande texten ovan.

[!INCLUDE [digital-twins-route-filters](../../includes/digital-twins-route-filters.md)]

[!INCLUDE [digital-twins-route-metrics](../../includes/digital-twins-route-metrics.md)]

## <a name="next-steps"></a>Nästa steg

Läs om de olika typerna av händelse meddelanden som du kan ta emot:
* [*Anvisningar: tolka händelse data*](how-to-interpret-event-data.md)
