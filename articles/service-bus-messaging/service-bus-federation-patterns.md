---
title: Aktivitets mönster för meddelande replikering – Azure Service Bus | Microsoft Docs
description: Den här artikeln innehåller detaljerad information om hur du implementerar aktivitets mönster för en speciell meddelande replikering
ms.topic: article
ms.date: 12/12/2020
ms.openlocfilehash: d823ee7ccd4f53bfc3e10211a4f44908273a110d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657595"
---
# <a name="message-replication-tasks-patterns"></a>Mönster för meddelande replikering

[Federations översikten](service-bus-federation-overview.md) och [översikten över replikerare](service-bus-federation-replicator-functions.md) förklarar grunderna för och de grundläggande elementen i replikeringen, och vi rekommenderar att du bekantar dig med dem innan du fortsätter med den här artikeln.

I den här artikeln beskriver vi implementerings vägledningen för flera av mönstren som är markerade i översikts avsnittet. 

## <a name="replication"></a>Replikering 

I det här mönstret kopieras meddelanden från en kö eller ett ämne till nästa, eller från en kö eller ett ämne till ett annat mål, till exempel en Event Hub. Meddelandena vidarebefordras utan att göra några ändringar i meddelande nytto lasten. 

Implementeringen av det här mönstret omfattas av [meddelandet replikering till och från Azure Service Bus](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy) -exemplet.

### <a name="sequences-and-order-preservation"></a>Sekvenser och bevarad ordning

Replikeringssystemet syftar inte till att bevara den absoluta ordningen på meddelanden i en källhierarki eller ett ämne i en målkö eller ett ämne, men fokuserar vid behov på att bevara den relativa ordningen på meddelanden där programmet kräver det. Programmet aktiverar detta genom att aktivera stöd för session för källentiteten och gruppera relaterade meddelanden med samma [sessionsnyckel](message-sessions.md).

De sessioner som är medvetna om förbyggd replikering ser till att meddelandesekvensnummer med samma sessions-ID som hämtats från en källentiteten skickas till målkön eller ämnet som en batch i den ursprungliga sekvensen och med samma sessions-ID. 

### <a name="service-assigned-metadata"></a>Tjänstetilldelade metadata 

De tjänstetilldelade metadata för ett meddelande som hämtats från käll kön eller ämnet, ursprunglig Kölängd och sekvensnummer, ersätts med nya tjänstetilldelade värden i målkön eller ämnet, men med standardreplikerings aktiviteterna som finns i våra exempel, bevaras de ursprungliga värdena i användar egenskaper: `repl-enqueue-time` (iso8601 sträng) och `repl-sequence` .

Egenskaperna är av typen sträng och innehåller stringified-värdet för respektive ursprungliga egenskaper.  Om meddelandet vidarebefordras flera gånger läggs de tjänstetilldelade metadata för den omedelbara källan till i redan befintliga egenskaper, med värden avgränsade med semikolon. 

### <a name="failover"></a>Redundans

Om du använder replikering för haveri beredskap, för att skydda mot regionala tillgänglighets meddelanden i Service Buss tjänsten eller mot nätverks avbrott, kräver ett sådant fel scenario att en redundansväxling sker från en kö eller ett ämne till nästa, vilket innebär att producenter och/eller konsumenter använder den sekundära slut punkten.

För alla failover-scenarier antas att de nödvändiga elementen i namn områdena är strukturellt identiska, vilket innebär att köer och ämnen är identiska och att signaturer för delad åtkomst och/eller rollbaserad åtkomst kontroll har kon figurer ATS på samma sätt. Du kan skapa (och uppdatera) ett sekundärt namn område genom att följa [rikt linjerna för att flytta namnrum](move-across-regions.md) och utelämna steget rensa.

För att göra det möjligt för producenter och konsumenter att byta, måste du göra informationen om vilken namnrymd som ska användas för sökning på en plats som är lätt att komma åt och uppdatera. Om tillverkare eller konsumenter stöter på frekventa eller beständiga fel bör de kontakta den platsen och ändra sin konfiguration. Det finns flera sätt att dela den konfigurationen, men vi pekar på två i följande: DNS-och fil resurser.

#### <a name="dns-based-failover-configuration"></a>Konfiguration av DNS-baserad redundans

En kandidat metod är att hålla informationen i DNS SRV-poster i en DNS som du styr och peka på respektive slut punkter för kön eller ämnen. Observera att meddelande hubbar inte tillåter att dess slut punkter får ett direkt alias med CNAME-poster, vilket innebär att du kommer att använda DNS som en elastisk söknings funktion för slut punkts adresser och inte att direkt matcha IP-adressinformation. 

Anta att du äger domänen `example.com` och, för ditt program, en zon `test.example.com` . För två alternativa Service Bus kommer du nu att skapa två ytterligare kapslade zoner och en SRV-post i var och en. 

SRV-posterna är, efter gemensam konvention, föregås av `_azure_servicebus._amqp` och innehåller två slut punkts poster: en för AMQP på port 5671 och en för AMQP för WebSockets på port 443, som båda pekar på den Service Bus slut punkten för namn området som motsvarar zonen.

| Zon                 | SRV-post
|----------------------|-------------------------------------------------------------
| `sb1.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb1-test-example-com.servicebus.windows.net`<br>`2 2 443 sb1-test-example-com.servicebus.windows.net`
| `sb2.test.example.com` | **`_azure_servicebus._amqp.sb1.test.example.com`**<br>`1 1 5671 sb2-test-example-com.servicebus.windows.net`<br>`2 2 443 sb2-test-example-com.servicebus.windows.net`

I programmets zon skapar du en CNAME-post som pekar på den underordnade zonen som motsvarar din primära kö eller ditt ämne:

| CNAME-post                 | Alias
|------------------------------|-------------------------------------------------------------
| `servicebus.test.example.com`  | `test1.test.example.com`

Om du använder en DNS-klient som tillåter att fråga CNAME-och SRV-poster explicit (de inbyggda klienterna i Java och .NET tillåter endast enkel matchning av namn på IP-adresser) kan du sedan lösa den önskade slut punkten. Till exempel är lookup-funktionen [DnsClient.net](https://dnsclient.michaco.net/):

``` C#
static string GetServiceBusName(string aliasName)
{
    const string SrvRecordPrefix = "_azure_servicebus._amqp.";
    LookupClient lookup = new LookupClient();

    return (from CNameRecord alias in (lookup.Query(aliasName, QueryType.CNAME).Answers)
            from SrvRecord srv in lookup.Query(SrvRecordPrefix + alias.CanonicalName, QueryType.SRV).Answers
            where srv.Port == 5671
            select srv.Target).FirstOrDefault()?.Value.TrimEnd('.');
}
```

Funktionen returnerar det mål värd namn som registrerats för port 5671 för zonen som för närvarande är aliased med CNAME som visas ovan. 

Om du utför en redundansväxling måste du redigera CNAME-posten och peka den mot den alternativa zonen. 

Fördelen med att använda DNS och specifikt [Azure DNS](../dns/dns-overview.md)är att Azure DNS information replikeras globalt och därmed är elastisk mot drifts avbrott i en region.

Den här proceduren liknar hur [Service Bus geo-Dr](service-bus-geo-dr.md) fungerar, men fullständigt under din egen kontroll och fungerar också med aktiva/aktiva scenarier.

#### <a name="file-share-based-failover-configuration"></a>Konfiguration av fil resurs baserad redundans

Det enklaste alternativet att använda DNS för att dela slut punkts information är att ange namnet på den primära slut punkten i en oformaterad textfil och betjäna filen från en infrastruktur som är stabil mot avbrott och fortfarande tillåter uppdateringar. 

Om du redan kör en infrastruktur med hög tillgänglighet med global tillgänglighet och innehålls replikering, lägger du till en sådan fil där och publicerar om filen om det behövs en växel.

## <a name="merge"></a>Sammanfoga

Sammanslagnings mönstret innehåller en eller flera replikeringar som pekar på ett mål, och samtidigt kan de vanliga tillverkarna också skicka meddelanden till samma mål. 

Variationer av det här mönstret är:
- Två eller flera replikeringsalternativ hämtar meddelanden från separata källor samtidigt och skickar dem till samma mål.
- En mer replikerings funktion som hämtar meddelanden från en källa medan målet också används direkt av producenterna. 
- Föregående mönster, men meddelanden som speglas mellan två eller flera ämnen, vilket leder till att dessa ämnen innehåller samma meddelanden, oavsett var meddelanden skapas.

De två första mönster variationerna är enkla och skiljer sig inte från aktiviteter med enkel replikering.

Det sista scenariot kräver att även redan replikerade meddelanden replikeras igen. Tekniken visas och förklaras i det aktiva/aktiva exemplet.

## <a name="editor"></a>Redigerare

Redigerings mönstret bygger på [replikeringsscopet](#replication) , men meddelanden ändras innan de vidarebefordras. Exempel på sådana ändringar är:

- **_Omkodning_* _ – om meddelande innehållet (kallas även "brödtext" eller "nytto Last") kommer från källan som kodas med _Apache Avro *-format eller ett visst format för egen serialisering, men förväntar dig att systemet som äger målet är att innehållet ska vara *JSON* -kodat deserialiserar en omkodning av replikeringen från *Apache Avro* till ett InMemory-objekt och sedan serialiserar du grafen till *JSON* -formatet för det meddelande som vidarebefordras. Omkodning omfattar även **innehålls komprimering** och avkomprimerings aktiviteter.
- **_Omvandlingen_* _ – meddelanden som innehåller strukturerade data kan kräva omstrukturering av dessa data för enklare konsumtion av efterföljande konsumenter. Detta kan innebära arbete som att förenkla inkapslade strukturer, rensa främmande data element eller omforma nytto lasten så att de passar exakt för ett angivet schema.
- _*_Batching_*_ – meddelanden kan tas emot i batchar (flera meddelanden i en enda överföring) från en källa, men måste vidarebefordras till ett mål eller vice versa. En aktivitet kan därför vidarebefordra flera meddelanden baserat på en överföring av ett enda indatameddelande eller samla in en uppsättning meddelanden som sedan överförs tillsammans. 
- _*_Validering_*_ – meddelande data från externa källor behöver ofta kontrol leras om de är kompatibla med en uppsättning regler innan de kan vidarebefordras. Reglerna kan uttryckas med hjälp av scheman eller kod. meddelanden som inte finns i överensstämmelse kan tas bort, med problemet som anges i loggar eller vidarebefordras till ett särskilt mål mål för att hantera dem ytterligare.   
- _*_Anrikning_*_ – meddelande data som kommer från vissa källor kan kräva att du använder ytterligare kontext för att det ska kunna användas i mål systemen. Detta kan innebära att du söker efter referens data och bäddar in dessa data med meddelandet, eller hur du lägger till information om källan som är känd för aktiviteten replikering, men som inte finns med i meddelandena. 
- _*_Filtrering_*_ – vissa meddelanden som kommer från en källa kan behöva tas från målet baserat på en regel. Ett filter testar meddelandet mot en regel och släpper meddelandet om meddelandet inte matchar regeln. Filtrering av duplicerade meddelanden genom att observera vissa kriterier och släppa efterföljande meddelanden med samma värden är en typ av filtrering.
- _*_Routning och partitionering_*_ – vissa replikeringar kan tillåtas för två eller fler alternativa mål, och definiera regler för vilka replikeringsscope som ska väljas för ett visst meddelande baserat på metadata eller innehåll i meddelandet. En särskild form av routning partitionering, där uppgiften uttryckligen tilldelar partitioner i ett målvärde baserat på regler.
- _*_Kryptografi_*_ – en replikering kan behöva dekryptera innehåll som kommer från källan och/eller kryptera innehållet till ett mål och/eller måste kontrol lera integriteten för innehåll och metadata i förhållande till en signatur som befordras i meddelandet eller bifoga en sådan signatur. 
- _*_Attestering_*_ -en replikeringslänk kan bifoga metadata, som potentiellt skyddas av en digital signatur, till ett meddelande som bekräftar att meddelandet har tagits emot via en speciell kanal eller vid en angiven tidpunkt.     
- _ *_Länkning_**-en replikeringslänk kan använda signaturer för sekvenser av meddelanden, så att den här sekvensens integritet är skyddat och att meddelanden som saknas kan identifieras.

Alla mönster kan implementeras med hjälp av Azure Functions med hjälp av [meddelande Hubbs utlösaren](../azure-functions/functions-bindings-service-bus-trigger.md) för att hämta meddelanden och [bindnings bindningen för kön eller ämnet](../azure-functions/functions-bindings-service-bus-output.md) för att leverera dem. 

## <a name="routing"></a>Routning

Mönstret för routning bygger på [replik](#replication) mönstret, men i stället för att ha en källa och ett mål har replikeringen flera mål, som illustreras här i C#:

``` csharp
[FunctionName("SBRouter")]
public static async Task Run(
    [ServiceBusTrigger("source", Connection = "serviceBusConnectionAppSetting")] Message[] messages,
    [ServiceBus("dest1", Connection = "serviceBusConnectionAppSetting")] QueueClient output1,
    [ServiceBus("dest2", Connection = "serviceBusConnectionAppSetting")] QueueClient output2,
    ILogger log)
{
    foreach (Message messageData in messages)
    {
        // send to output1 or output2 based on criteria 
    }
}
```

Funktionen routning kommer att betrakta meddelandets metadata och/eller meddelandets nytto last och sedan välja en av de tillgängliga destinationer som ska skickas till.

## <a name="next-steps"></a>Nästa steg

- [Message Replicators-program i Azure Functions][1]
- [Replikera meddelanden mellan Service Bus][2]
- [Replikera meddelanden till Azure Event Hubs][3]

[1]: service-bus-federation-replicator-functions.md
[2]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopy
[3]: https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config/ServiceBusCopyToEventHub