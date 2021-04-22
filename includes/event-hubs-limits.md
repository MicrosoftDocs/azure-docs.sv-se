---
title: include file description: include file services: event-hubs author: spell fältet ms.service: event-hubs ms.topic: include ms.date: 03/31/2021 ms.author: spell hubs ms.custom: "include file","fasttrack-edit","iot","event-hubs"

---

Följande tabeller innehåller kvoter och begränsningar som är specifika [för Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Mer information om Event Hubs finns i [Event Hubs priser.](https://azure.microsoft.com/pricing/details/event-hubs/)

### <a name="common-limits-for-all-tiers"></a>Vanliga gränser för alla nivåer
Följande begränsningar är gemensamma för alla nivåer. 

| Gräns |  Kommentarer | Värde |
| --- |  --- | --- |
| Antal Event Hubs namnrymder per prenumeration |- |100 |
| Antal händelsehubbb per namnområde | Efterföljande begäranden om att skapa en ny händelsehubb avvisas. |10 |
| Namnet på en händelsehubb |- | 256 tecken |
| Storleken på ett konsumentgruppnamn | Kafka-protokollet kräver inte att en konsumentgrupp skapas. | <p>Kafka: 256 tecken</p><p>AMQP: 50 tecken |
| Antal icke-epokmottagare per konsumentgrupp |- |5 |
| Antal auktoriseringsregler per namnområde | Efterföljande begäranden om att skapa auktoriseringsregel avvisas.|12 |
| Antal anrop till metoden GetRuntimeInformation |  - | 50 per sekund | 
| Antal virtuella nätverk (VNet) | - | 128 | 
| Antal IP-konfigurationsregler | - | 128 | 

### <a name="basic-vs-standard-tiers"></a>Nivåerna Basic och Standard
I följande tabell visas gränser som kan vara olika för nivåerna Basic och Standard. 

| Gräns | Kommentarer | Basic | Standard |
|---|---|--|---|
| Maximal storlek på Event Hubs publicering| &nbsp; | 256 kB | 1 MB |
| Antal konsumentgrupper per händelsehubb | &nbsp; |1 |20 |
| Antal AMQP-anslutningar per namnområde | Efterföljande begäranden om ytterligare anslutningar avvisas och ett undantag tas emot av den anropande koden. |100 |5 000|
| Maximal kvarhållningsperiod för händelsedata | &nbsp; |1 dag |1–7 dagar |
| Maximalt dataflöde |Om den här gränsen överskrids begränsas dina data och ett undantagsfel genereras [för servern.](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) Om du vill begära ett större antal dataflödesenheter för en standardnivå kan du skicka en [supportbegäran.](../articles/azure-portal/supportability/how-to-create-azure-support-request.md) [Ytterligare dataflödesenheter](../articles/event-hubs/event-hubs-auto-inflate.md) är tillgängliga i block om 20 för inköp. |20 | 20 | 
| Antal partitioner per händelsehubb | |32 | 32 | 

> [!NOTE]
>
> Du kan publicera händelser individuellt eller i batchar. 
> Publiceringsgränsen (enligt SKU) gäller oavsett om det är en enskild händelse eller en batch. Publicering av händelser som är större än det högsta tröskelvärdet avvisas.

### <a name="dedicated-tier-vs-standard-tier"></a>Dedikerad nivå jämfört med standardnivån
Erbjudandet Event Hubs Dedicated faktureras till ett fast månadspris med minst 4 timmars användning. På nivån Dedikerad finns alla funktioner i standardplanen, men med kapacitet och begränsningar i företagsskala för kunder med krävande arbetsbelastningar. 

Läs det här [dokumentet om](../articles/event-hubs/event-hubs-dedicated-cluster-create-portal.md) hur du skapar dedikerade Event Hubs kluster med Azure Portal.

| Funktion | Standard | Dedikerad |
| --- |:---|:---|
| Bandbredd | 20 TUs (upp till 40 TUs) | 20 CUs |
| Namnrymder |  100 per prenumeration | 50 per CU (100 per prenumeration) |
| Event Hubs |  10 per namnområde | 1 000 per namnområde |
| Ingresshändelser | Betala per miljon händelser | Ingår |
| Meddelandestorlek | 1 miljon byte | 1 miljon byte |
| Partitioner | 32 per händelsehubb | 1024 per händelsehubb<br/>2 000 per CU |
| Konsumentgrupper | 20 per händelsehubb | Ingen gräns per CU, 1 000 per händelsehubb |
| A brokered connections | 1 000 ingår, 5 000 max | 100 K ingår och max |
| Kvarhållning av meddelanden | 7 dagar, 84 GB ingår per TU | 90 dagar, 10 TB ingår per CU |
| Capture | Betala per timme | Ingår |


### <a name="schema-registry-limitations"></a>Schemaregisterbegränsningar

#### <a name="limits-that-are-the-same-for-standard-and-dedicated-tiers"></a>Samma begränsningar för standard- och dedikerade nivåer 
| Funktion | Gräns | 
|---|---|
| Maximal längd för ett schemagruppnamn | 50 |  
| Maximal längd för ett schemanamn | 100 |    
| Storlek i byte per schema | 1 MB |   
| Antal egenskaper per schemagrupp | 1024 |
| Storlek i byte per gruppegenskapsnyckel | 256 | 
| Storlek i byte per gruppegenskapsvärde | 1024 | 


#### <a name="limits-that-are-different-for-standard-and-dedicated-tiers"></a>Begränsningar som skiljer sig för standard- och dedikerade nivåer 

| Gräns | Standard | Dedikerad | 
|---|---|--|
| Schemaregistrets storlek (namnrymd) i megabyte | 25 |  1024 |
| Antal schemagrupper i ett schemaregister eller ett namnområde | 1 – exkludera standardgruppen | 1000 |
| Antal schemaversioner i alla schemagrupper | 25 | 10000 |