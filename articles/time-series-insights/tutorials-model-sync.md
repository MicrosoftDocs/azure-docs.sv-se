---
title: Synkronisering av modeller mellan Azure Digitals dubbla och Time Series Insights | Microsoft Docs
description: Metod tips och verktyg som används för att översätta till gångs modellen i ADT till till gångs modell i Azure TSD
ms.service: time-series-insights
services: time-series-insights
author: msrsrinivasan
ms.author: radhsrin
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 01/19/2021
ms.custom: dpalled
ms.openlocfilehash: 5e8e5367daa532fa0cc36a18cd28b382e7cd8f78
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98682910"
---
# <a name="model-synchronization-between-azure-digital-twins-and-time-series-insights-gen2"></a>Modellsynkronisering mellan Azure Digital Twins och Time Series Insights Gen2

I den här artikeln beskrivs bästa praxis och verktyg som används för att översätta till gångs modellen i Azure Digitals ADT () till till gångs modell i Azure Time Series Insights (TSD).  Den här artikeln är den andra delen i en själv studie kurs serie i två delar som förklarar integreringen av Azure Digital-dubbla med Azure Time Series Insights. Integrering av Azure Digitals dubbla med Time Series Insights möjliggör arkivering och spårning av historiken för telemetrivärden och beräknade egenskaper för digitala dubbla. Den här serien med självstudier är avsedd för utvecklare som arbetar för att integrera Time Series Insights med digitala Azure-dubbla. Del 1 förklarar hur du  [upprättar datapipelinen som hämtar faktiska tids serie data från Azure Digitals till Time Series Insights](../digital-twins/how-to-integrate-time-series-insights.md) och den andra delen av själv studie serien förklarar synkroniseringen av till gångs modeller mellan Azure Digitals och Time Series Insights. I den här självstudien beskrivs de bästa metoderna för att välja och upprätta namngivnings konvention för Time Series ID (TS-ID) och manuellt upprätta hierarkier i Time Series-modellen (TSM).

## <a name="choosing-a-time-series-id"></a>Välja ett Time Series-ID

Time Series ID är en unik identifierare som används för att identifiera till gångar i Time Series Insights. Time Series-data (telemetrivärden från fältet, som är tids värde par) visas med variabler som anges under TSID. I Azure Digitals, används dubbla egenskaper och telemetrivärden för att representera status för en enhet med dubbla och mått som produceras av båda två. Från och med den aktuella utformningen av TSM måste TSIDs vara unikt. Om du använder dubbla ID: n för de dubbla i Azures digitala dubbla, eller tillsammans med egenskaps-eller telemetri-namn, blir alltid unikt TS-ID i TSM. I ett typiskt fall blir **_`<Twin ID>`_** TSID och egenskaps-och telemetridata är variablerna i TSM. Det finns dock fall där det är önskvärt att till gångs hierarkier i Time Series Insights förenklas med formatet för sammansatt nyckel, t **_`<Twin ID>+ <Delimiter of Choice> + <Name of the Property or Telemetry>`_** . ex.. Låt oss ta ett exempel på att förklara det senare fallet. Överväg ett rum i en byggnad som modelleras som en dubbel och har ett Room22-ID. Egenskapen temperatur inställning måste översättas som **_TSID Room22_TempSetting_** och temperatur mätning som ska översättas till **_Room22_TempMea_** i TSM.

[![Välja ett Time Series-ID](media/tutorials-model-sync/choosing-tsid.png)](media/tutorials-model-sync/choosing-tsid.png#lightbox)

## <a name="contextualizing-time-series"></a>Sammanhangsbaserad tids serie

Contextualization av data (främst spatialdata) i Time Series Insights uppnås genom till gångs hierarkier och samma används för enkel data navigering via en trädvy i Time Series Insights Explorer. Tids serie typer och hierarkier definieras med tids serie modellen (TSM) i Time Series Insights. Typer i TSM-hjälpen för att definiera variabler, medan hierarki nivåer och instans fält värden används för att skapa trädvyn i Time Series Insights Explorer. Mer information om TSM finns i Time Series Insights- [dokumentationen online](./concepts-model-overview.md).

I Azure Digitals dubbla, uttrycks anslutning mellan till gångar med hjälp av dubbla relationer. Dubbla relationer är helt enkelt ett diagram över anslutna till gångar. Men i insikter i Time Series är relationer mellan till gångar hierarkiskt av typ. Det innebär att till gångar delar en överordnad-underordnad typ OD-relation och representeras med en träd struktur. För att översätta Relations information från digitala Azure-band till Time Series Insights-hierarkier, måste vi välja relevanta hierarkiska relationer från Azure Digitals flätar. Azure Digitals-enheter använder ett öppet standard-, modellerings språk som kallas Digital DTDL (Digital Definition Language). I DTDL-modeller beskrivs hur du använder en variant av JSON som kallas JSON-LD. Se [DTDL-dokumentationen](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) för fullständig information om specifikationen.

[![Anslutning mellan till gångar](media/tutorials-model-sync/asset-connection.png)](media/tutorials-model-sync/asset-connection.png#lightbox)

## <a name="translating-graph-representation-in-azure-digital-twins-to-tree-structure-in-time-series-insights"></a>Översättning av diagram representation i Azure Digitals flätar till träd struktur i Time Series Insights

I följande avsnitt i själv studie kursen beskrivs några viktiga scenarier där du manuellt översätter diagram strukturen i Azure Digitals till träd strukturen i Time Series Insights.

Exempel system: den här självstudien använder följande exempel för att förklara de begrepp som beskrivs nedan. Det är ett enkelt, fiktivt Bygg hanterings system med en våning och två rum. Den har tre termostater, en i varje rum och en annan som är gemensam för golvet. Dessutom har det också en mätning av vatten flödet som mäter vatten flödet från Room21 till Room22 via en pipe-anslutning mellan rummen. När du tittar på den spatiala relationen mellan dubbla, finns båda typerna av relationer.

1. Vanligaste hierarkiska relationer. Till exempel Building40-> Floor01-> golv, > temperatur
1. Inte så vanligt, cirkulär relation. Om du till exempel börjar från Building40 kan FlowMtr spåras via två olika sökvägar.

   1. Building40-> Floor01-> Room21-> FlowMtr *-> Flow
   1. Building40-> Floor01-> Room22-> FlowMtr *-> Flow  
      Där "flow" är den faktiska telemetri som mäter vatten flödet mellan Room21 och Room22

> [!Note]
>
> `*` Golv bestånd står för FloorThermoStat och FlowMtr står för flödes mätare och väljs vanligt vis som TSID. Temperatur och flöde är den råa telemetri som kallas variabler i Time Series Insights.

Med tanke på den aktuella begränsningen i Time Series Insights att en till gång inte kan representeras i flera grenar, förklarar följande avsnitt modellering av hierarkiska och cirkulära relationer i Time Series Insights.

## <a name="case-1-hierarchical-parent-child-relationship"></a>Fall 1: hierarkisk (överordnad-underordnad) relation

Det är den vanligaste typen av relation mellan de dubbla. en övergripande modell över-underordnad-relation förklaras i följande bild. Instans fälten och TSID härleds från dubbla ID: n som visas nedan. Medan instans fält kan uppdateras manuellt med Time Series Insights Explorer, förklarar avsnittet nedan med namnet "uppdatera instans fält med hjälp av API: er" att du lyssnar på modell ändringar i Azure Digitals och uppdaterar instans fälten i Time Series Insights med Azure Functions.

[![Mappa dubbla ID: n](media/tutorials-model-sync/mapping-twin-ids.png)](media/tutorials-model-sync/mapping-twin-ids.png#lightbox)

[![Mappa dubbla ID: n 2](media/tutorials-model-sync/mapping-twin-ids2.png)](media/tutorials-model-sync/mapping-twin-ids2.png#lightbox)

## <a name="case-2-circular-relationship"></a>Fall 2: cirkulär relation

### <a name="circular-relationship-in-azure-digital-twins-to-single-hierarchy-relationship-in-time-series-insights"></a>Cirkulär relation i digitala Azure-relationer till en hierarki i Time Series Insights

Eftersom TSID måste vara unikt och bara kan representeras i en hierarki, representerar det här fallet _"FlowMtr"_ med en telemetri med namnet _"flow"_ strax under _"Room21"_. I fortsättningen när Time Series Insights har stöd för flera representationer av Time Series i TSM, visas telemetri _"Flow_ " under _"Room 21"_ och _"Room 22"_

Följande skärm bild visar manuell mappning av dubbla ID: n i Azure Digitals dubbla till instans fält i TSM och den resulterande hierarkin i Time Series Insights.

[![Mappa dubbla ID: n i digitala Azure-anslutningar](media/tutorials-model-sync/mapping-twin-ids-adt.png)](media/tutorials-model-sync/mapping-twin-ids-adt.png#lightbox)

### <a name="circular-relationship-in-azure-digital-twins-to-multiple-hierarchies-in-time-series-insights-using-duplicates"></a>Cirkulär relation i Azure Digital-multiplar till flera hierarkier i Time Series Insights, med hjälp av dubbletter

I del 1 av själv studie kursen förklaras hur ett klient program (en Azure-funktion) hjälper till att överföra telemetridata från IoT Hub eller andra händelse källor till Azures digitala dubbla. Den här metoden föreslår att använda samma klient program för att göra uppdateringar av relevanta egenskaper för de överordnade dubbla. I det här exemplet, samtidigt som du läser FlowMtr-telemetri från IoT Hub och uppdaterar egenskapen "flow" i FlowMtr, så kan programmet också uppdatera motsvarande egenskaper i alla möjliga överordnat objekt i källan. I vårt exempel skulle det vara "outflowmea" (identifieras med relationen "utflödes relation) för egenskapen Room21 och" inflowmea "för Room22.  Skärm bilden nedan visar den slutliga användar upplevelsen i Time Series Insights Explorer. Det måste noteras att vi har data dubbletter genom den här metoden.

[![Time Series Insights Explorer](media/tutorials-model-sync/tsi-explorer.png)](media/tutorials-model-sync/tsi-explorer.png#lightbox)

Kodfragmentet nedan visar hur klient programmet kunde navigera i den dubbla relationen med hjälp av Azure Digitals dubbla API: er.

> [!Note]
>
> Det här kodfragmentet förutsätter att läsarna är bekant med [del 01](../digital-twins/tutorial-end-to-end.md#set-up-the-sample-function-app) i själv studie kursen och att den här kod ändringen gjordes i funktionen "ProcessHubToDTEvents".

```csharp
if (propertyPath.Equals("/Flow"))
{
//Update the flow value property of the flow meter
await AdtUtilities.UpdateTwinProperty(client, twinId, "replace",
propertyPath, "double", propertyValue, log);

//also update the sending end flow
string parentIdOutflow = await AdtUtilities.FindParent(client, twinId,
"outflow", log);
if (parentIdOutflow != null)
await AdtUtilities.UpdateTwinProperty(client, parentIdOutflow, "replace", "outflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with outflow
relationship for " + twinId );
//and receiving end flow value
string parentIdinflow = await AdtUtilities.FindParent(client, twinId,
"inflow", log);
if (parentIdinflow != null)

await AdtUtilities.UpdateTwinProperty(client, parentIdinflow,
"replace", "inflow", "double", propertyValue, log);
else
log.LogInformation("Unable to find Parent with inflow
relationship for " + twinId);
}
```

## <a name="updating-instance-fields-using-apis"></a>Uppdatera instans fält med hjälp av API: er

Det här avsnittet av själv studie kursen förklarar hur du lyssnar på modell ändringar i Azure Digitals, till exempel skapande, borttagning av dubbla eller ändringar i relationer mellan dubbla och uppdaterade instans fält och hierarkier via programmering med Time Series Insights modell-API: er. Den här metoden för att uppdatera Time Series Insights modell uppnås vanligt vis via Azure Functions. I Azure Digitals, kan händelse meddelanden som till exempel dubbla tillägg eller borttagningar dirigeras till efterföljande tjänster, till exempel Event Hubs som i sin tur kan matas till Azure Functions. Mer information om händelse dirigering och filtrering beskrivs [här](../digital-twins/how-to-manage-routes-portal.md).  I resten av det här avsnittet beskrivs hur du använder Time Series Insights modell-API: er i Azure Functions för att uppdatera Time Series Insights modellen som svar på en extra (en typ av modell ändring) i Azure Digitals dubbla.

### <a name="receiving-and-identifying-twin-addition-event-notification"></a>Ta emot och identifiera dubbla tillägg för händelse meddelanden

```csharp
[FunctionName("RouteEventsToTsi")]
public async Task Run([EventGridTrigger]EventGridEvent eventGridEvent)
{
    try
    {
        if (eventGridEvent != null && eventGridEvent.Data != null)
        {
            logger.LogInformation($"EventType: {eventGridEvent.EventType}");
            logger.LogInformation($"EventGridEvent: {JsonConvert.SerializeObject(eventGridEvent)}");

            //Shape event and Send event data to event hub and tsi
            await SendEventToEventHubAsync(eventGridEvent).ConfigureAwait(false);

            //If a new twin was created, update the newly created instance in TSI with info retrieved from ADT
            if (eventGridEvent.EventType == Constants.TwinCreateEventType)
            {
                //retrieve building, floor and room of value twin
                var twinInfo = await RetrieveTwinInfoAsync(eventGridEvent).ConfigureAwait(false);
                //Update Tsi instance with type(sensor type), hierarchy(space hierarchy) and instance fields(twin info retrieved above)
                var instance = await CreateInstanceToSendAsync(twinInfo).ConfigureAwait(false);
                var instanceToUpdate = new List<TimeSeriesInstance>() { instance };
                var response = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(new InstancesBatchRequest(update: instanceToUpdate)).ConfigureAwait(false);
            }
        }
    }
    catch (Exception ex)
    {
        logger.LogError($"Exception: {ex.Message}");
    }
}
```

### <a name="creating-time-series-insights-client-and-adding-instance-details"></a>Skapa Time Series Insights-klient och lägga till instans information

```csharp
private async Task<TimeSeriesInstance> CreateInstanceToSendAsync(Dictionary<string, string> twinInfo)
{
    try
    {
        tsiClient = await GetTSIClientAsync().ConfigureAwait(false);

        var timeSeriesId = new object[] { twinInfo[Constants.DtId] };
        var instances = await tsiClient.TimeSeriesInstances.ExecuteBatchAsync(
            new InstancesBatchRequest(
                get: new InstancesRequestBatchGetOrDelete(
                    new IList<object>[] { timeSeriesId }))).ConfigureAwait(false);
        var instance = instances.Get.First().Instance;

        if (instance != null)
        {
            instance = await AddHierarchyToInstanceAsync(instance).ConfigureAwait(false);
            instance = await AddTypeToInstanceAsync(instance, twinInfo[Constants.TwinType]).ConfigureAwait(false);

            instance.InstanceFields = new Dictionary<string, object>
                            {
                                { "Building", twinInfo[Constants.BuildingName] },
                                { "Floor", twinInfo[Constants.FloorName] },
                                { "Room", twinInfo[Constants.ParentName] }
                            };

            //If value twin is a sensor value twin, add sensor type instance field to diff from spatial value twin
            if (twinInfo[Constants.ParentType] == Constants.Sensor)
            {
                instance.InstanceFields.Add(Constants.SensorType, twinInfo[Constants.TwinType]);
            }
            return instance;
        }
        else
        {
            logger.LogError($"instance with id {twinInfo[Constants.DtId]} not found");
            return new TimeSeriesInstance();
        }
    }
    catch (Exception e)
    {
        logger.LogError(e.Message);
        throw;
    }
}
```

### <a name="applying-hierarchy-info-to-instance"></a>Använder information om hierarkin till instans

```csharp
private async Task<TimeSeriesInstance> AddHierarchyToInstanceAsync(TimeSeriesInstance instance)
{
    if (instance.HierarchyIds == null)
    {
        TimeSeriesHierarchy spacesHierarchy = null;
        try
        {
            var hierarchy = await RunGetHierarchiesAsync(Constants.SpaceHierarchy).ConfigureAwait(false);
            spacesHierarchy = hierarchy.First(h => h.Name.Equals(Constants.SpaceHierarchy));
            instance.HierarchyIds = new List<Guid?>();
            instance.HierarchyIds.Add(spacesHierarchy.Id);
        }
        catch (Exception ex)
        {
            logger.LogWarning($"Hierarchy 'space hierarchy' not found, {ex}");
            throw;
        }
    }
    return instance;
}
```

## <a name="next-steps"></a>Nästa steg

Det tredje i serien med självstudier är att visa hur du frågar historiska data från digitala Azure-dubbla med Time Series Insights API: er. Det är ett pågående arbete och avsnittet uppdateras när du är klar. Under tiden uppmanas läsarna att referera till [Time Series Insights API-dokumentation för data frågor](./concepts-query-overview.md).
