---
title: Hämta Azures industriella IoT-data till ADX
description: I den här självstudien får du lära dig hur du hämtar IIoT-data till ADX.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 4c344dc09ad6c8aa4b2aa431952fc271d946b60d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787773"
---
# <a name="tutorial-pull-azure-industrial-iot-data-into-adx"></a>Självstudie: Hämta Azures industriella IoT-data till ADX

IIoT-plattformen (Azure industriella IoT) kombinerar Edge-moduler och moln mikrotjänster med ett antal Azure PaaS-tjänster för att tillhandahålla funktioner för identifiering av industriella till gångar och för att samla in data från dessa till gångar med OPC UA. [Azure datautforskaren (ADX)](https://docs.microsoft.com/azure/data-explorer) är ett naturligt mål för IIoT-data med funktioner för data analys som gör det möjligt att köra flexibla frågor på inmatade data från OPC UA-servrar som är anslutna till IoT Hub via OPC-utgivaren. Även om ett ADX-kluster kan mata in data direkt från IoT Hub, gör IIoT-plattformen ytterligare bearbetning av data, så att det blir mer användbart innan du lägger till den i Event Hub, som tillhandahålls när en fullständig distribution av mikrotjänster används (se IIoT Platform Architecture).

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tabell i ADX
> * Ansluta Event Hub till ADX-klustret
> * Analysera data i ADX

## <a name="how-to-make-the-data-available-in-the-adx-cluster-to-query-it-effectively"></a>Så här gör du data tillgängliga i ADX-klustret för att fråga det effektivt 

Om vi tittar på meddelande formatet från Händelsehubben (som definieras av klassen Microsoft. Azure. IIoT. OpcUa. Subscriber. Models. MonitoredItemMessageModel) kan vi se ett tips för den struktur som vi behöver för ADX Table-schemat.

![Struktur](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-1.png)

Nedan visas de steg som vi behöver för att göra data tillgängliga i ADX-klustret och för att fråga data på ett effektivt sätt.  
1. Skapa ett ADX-kluster. Om du inte har något ADX-kluster med IIoT-plattformen redan, eller om du vill använda ett annat kluster, följer du stegen [här](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-cluster). 
2. Aktivera strömnings inmatning i ADX-klustret enligt beskrivningen [här](https://docs.microsoft.com/azure/data-explorer/ingest-data-streaming#enable-streaming-ingestion-on-your-cluster). 
3. Skapa en ADX-databas genom att följa stegen [här](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal#create-a-database).

I följande steg ska vi använda [ADX-webbgränssnittet](https://docs.microsoft.com/azure/data-explorer/web-query-data) för att köra de nödvändiga frågorna. Se till att lägga till klustret i webb gränssnittet enligt beskrivningen i länken.  
 
4. Skapa en tabell i ADX för att lagra inmatade data i.  Även om klassen MonitoredItemMessageModel kan användas för att definiera schemat för ADX-tabellen, rekommenderar vi att du matar in data först i en mellanlagringsdatabas med en kolumn av typen [dynamisk](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic). Detta ger oss större flexibilitet vid hantering av data och bearbetning i andra tabeller (eventuellt kombinerar den med andra data källor) som uppfyller behoven för flera användnings fall. Följande ADX-fråga skapar mellanlagringsplatsen iiot_stage med en kolumn nytto Last,

```
.create table ['iiot_stage']  (['payload']:dynamic)
```

Vi måste också lägga till en JSON-inmatnings mappning för att instruera klustret att lägga hela JSON-meddelandet från Händelsehubben till mellanlagringsdatabas,

```
.create table ['iiot_stage'] ingestion json mapping 'iiot_stage_mapping' '[{"column":"payload","path":"$","datatype":"dynamic"}]'
```

5. Vår tabell är nu redo att ta emot data från Händelsehubben. 
6. Använd anvisningarna [här](https://docs.microsoft.com/azure/data-explorer/ingest-data-event-hub#connect-to-the-event-hub) för att ansluta HÄNDELSEHUBBEN till ADX-klustret och börja mata in data i vår mellanlagrings tabell. Vi behöver bara skapa anslutningen eftersom vi redan har en Event Hub som tillhandahålls av IIoT-plattformen.  
7. När anslutningen har verifierats börjar data flöda till vår tabell och efter en kort fördröjning kan vi börja utforska data i vår tabell. Använd följande fråga i ADX-webbgränssnittet för att titta på ett data exempel på 10 rader. Vi kan se hur data i nytto lasten liknar klassen MonitoredItemMessageModel som nämnts tidigare.

![Fråga](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-2.png)

8. Nu ska vi köra lite analys på dessa data genom att parsa dynamiska data i kolumnen nytto Last direkt. I det här exemplet beräknar vi genomsnittet av Telemetrin som identifieras av "DisplayName": "PositiveTrendData", över tid i Windows på 10 minuter, på alla poster som matats in sedan en viss tidpunkt (definieras av variabeln min_t) Låt min_t = datetime (2020-10-23). iiot_stage | där ToDateTime (nytto Last. Tidsstämpel) > min_t | där toString (nytto Last. DisplayName) = = ' PositiveTrendData ' | sammanfatta event_avg = AVG (ToDouble (nytto Last. Värde)) per bin (ToDateTime (nytto Last. Timestamp), 10 m)
 
Eftersom vår nytto Last-kolumn innehåller en dynamisk datatyp måste vi utföra data konverteringen vid tidpunkten så att våra beräkningar utförs på rätt data typer.

![Tidsstämpel för nytto Last](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-3.png)

Som vi nämnt tidigare, vilket innebär att OPC UA-data matas in i en mellanlagringsplats med en dynamisk kolumn, ger oss flexibilitet. Men för att kunna köra konverteringar av data typer vid tidpunkten kan det leda till fördröjningar i att köra frågorna, särskilt om data volymen är stor och om det finns många samtidiga frågor. I det här skedet kan vi skapa en annan tabell med de data typer som redan har identifierats, så att vi undviker data typs konverteringarna för frågans tid.
 
9. Skapa en ny tabell för de parsade data som består av en begränsad markering från innehållet i den dynamiska nytto lasten i mellanlagrings tabellen. Observera att vi har skapat en värde kolumn för var och en av de förväntade data typerna som förväntas i vår telemetri.

```
.create table ['iiot_parsed']  
    (['Tag_Timestamp']: datetime ,  
    ['Tag_PublisherId']:string ,  
    ['Tag']:string ,
    ['Tag_Datatype']:string ,  
    ['Tag_NodeId']:string,  
    ['Tag_value_long']:long ,  
    ['Tag_value_double']:double,  
    ['Tag_value_boolean']:bool)
```

10. Skapa en funktion (på databas nivå) för att projicera nödvändiga data från mellanlagrings tabellen. Här väljer vi "timestamp", "PublisherId", "DisplayName", "datatype" och "NodeId"-objekt från kolumnen "nytto Last" och projektet som "Tag_Timestamp", "Tag_PublisherId", "tag", "Tag_Datatype", "Tag_NodeId". Objektet "värde" projiceras som tre olika delar baserat på "datatype".

```
.create-or-alter function fn_InflightParseIIoTEvent()
{
iiot_stage
| extend Tag_Timestamp =  todatetime(payload.Timestamp)
| extend Tag_PublisherId = tostring(payload.PublisherId)
| extend Tag = tostring(payload.DisplayName)
| extend Tag_Datatype = tostring(payload.DataType)
| extend Tag_NodeId = tostring(payload.NodeId)
| extend Tag_value_long = case(Tag_Datatype == "Int64", tolong(payload.Value), long(null))
| extend Tag_value_double = case(Tag_Datatype == "Double", todouble(payload.Value), double(null))
| extend Tag_value_boolean = case(Tag_Datatype == "Boolean", tobool(payload.Value), bool(null))
| project Tag_Timestamp, Tag_PublisherId, Tag, Tag_Datatype, Tag_NodeId, Tag_value_long, Tag_value_double, Tag_value_boolean
}
```

Mer information om att mappa data typer i ADX finns [här](https://docs.microsoft.com/azure/data-explorer/kusto/query/scalar-data-types/dynamic)och för funktioner i ADX kan du börja [här](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/stored-functions).
 
11. Använd funktionen från föregående steg till den parsade tabellen med hjälp av en uppdaterings princip. Uppdaterings [principen](https://docs.microsoft.com/azure/data-explorer/kusto/management/updatepolicy) instruerar ADX att automatiskt lägga till data i en mål tabell när nya data infogas i käll tabellen, baserat på en omvandlings fråga som körs på data som infogats i käll tabellen. Vi kan använda följande fråga för att tilldela den parsade tabellen som mål och scen tabellen som källa för den uppdaterings princip som definierats av funktionen som vi skapade i föregående steg.

```
.alter table iiot_parsed policy update
@'[{"IsEnabled": true, "Source": "iiot_stage", "Query": "fn_InflightParseIIoTEvent()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
```

Så snart som ovanstående fråga körs börjar data flöda och fyller i mål tabellen iiot_parsed. Vi kan titta på data i iiot_parsed på följande sätt.

![Parsad tabell](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-4.png)

12. Nu ska vi titta på hur vi kan upprepa analysen som vi gjorde i ett föregående steg. beräkna medelvärdet för telemetri som identifieras av "DisplayName": "PositiveTrendData", över tid i Windows med 10 minuter, på alla poster som matats in sedan en viss tidpunkt (definieras av variabeln min_t). Eftersom vi nu har värdena för taggen ' PositveTrendData ' lagrade i en kolumn med data typen Double förväntar vi oss en förbättring av frågans prestanda.

![Upprepa analys](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-5.png)

13. Låt oss slutligen jämföra frågeresultatet i båda fallen. Vi kan ta reda på hur lång tid det tar att köra frågan med hjälp av "statistik" i ADX-ANVÄNDARGRÄNSSNITTET (som kan finnas ovanför frågeresultaten).  

![Fråga prestanda 1](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-6.png)

![Fråga prestanda 2](media/tutorial-iiot-data-adx/industrial-iot-in-azure-data-explorer-pic-7.png)

Vi kan se att frågan som använder den parsade tabellen är ungefär två gånger så snabb som för mellanlagrings tabellen. I det här exemplet har vi en liten data uppsättning och det finns inga samtidiga frågor som körs så att det inte är bra att påverka frågans körnings tid, men för realistiska arbets belastningar skulle det påverka prestandan. Det är därför det är viktigt att överväga att separera de olika data typerna till olika kolumner.

> [!NOTE] 
> Uppdaterings principen fungerar bara på de data som matas in i mellanlagringsplatsen efter att principen har kon figurer ATS och gäller inte för befintliga data. Detta måste beaktas när vi till exempel måste ändra uppdaterings principen. Fullständig information finns i ADX-dokumentationen.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig hur du ändrar standardvärdena för konfigurationen kan du 

> [!div class="nextstepaction"]
> [Konfigurera industriella IoT-komponenter](tutorial-configure-industrial-iot-components.md)

> [!div class="nextstepaction"]
> [Visualisera och analysera data med hjälp av Time Series Insights](tutorial-visualize-data-time-series-insights.md)