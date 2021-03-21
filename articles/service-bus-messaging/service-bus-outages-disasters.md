---
title: Isolera Azure Service Bus program mot avbrott och haverier
description: De här artiklarna innehåller tekniker för att skydda program mot ett möjligt Azure Service Bus avbrott.
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: b9090a54cd58788dbd13f528af4dda4aa96005b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374600"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Metodtips för isolering av program mot Service Bus-avbrott och katastrofer

Verksamhets kritiska program måste köras kontinuerligt, även i närvaro av oplanerade avbrott eller katastrofer. I den här artikeln beskrivs tekniker som du kan använda för att skydda Service Bus program mot ett potentiellt avbrott i tjänsten eller katastrof.

Ett avbrott definieras som tillfälligt otillgängligt för Azure Service Bus. Avbrottet kan påverka vissa komponenter i Service Bus, till exempel ett meddelande arkiv eller till och med hela data centret. När problemet har åtgärd ATS blir Service Bus tillgängligt igen. Normalt orsakar ett avbrott inte förlust av meddelanden eller andra data. Ett exempel på ett komponent haveri är att det inte går att ha till gång till ett visst meddelande arkiv. Ett exempel på ett Data Center – brett avbrott är ett strömavbrott i data centret eller en felaktig nätverks växel för data Center. Ett avbrott kan efter några minuter efter några dagar.

En katastrof definieras som den permanenta förlusten av en Service Bus skalnings enhet eller data Center. Data centret kan vara otillgängligt igen. Normalt orsakar en katastrof förlust av vissa eller alla meddelanden eller andra data. Exempel på katastrofer är brand, översvämning eller jord bävning.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Skydd mot avbrott och haverier – Service Bus Premium
Koncepten för hög tillgänglighet och haveri beredskap är inbyggda i Azure Service Bus Premium-nivån, både inom samma region (via Tillgänglighetszoner) och mellan olika regioner (via Geo-Disaster återställning).

### <a name="geo-disaster-recovery"></a>Geo-Disaster återställning

Service Bus Premium stöder geo-katastrof återställning på namn områdes nivå. Mer information finns i [Azure Service Bus geo-Disaster Recovery](service-bus-geo-dr.md). Funktionen för haveri beredskap, som endast är tillgänglig för [Premium SKU: n](service-bus-premium-messaging.md) , implementerar haveri beredskap för metadata och förlitar sig på de primära och sekundära katastrof återställnings namn områdena. Med Geo-Disaster återställning replikeras endast metadata för entiteter mellan primär och sekundär namnrymd.  

### <a name="availability-zones"></a>Tillgänglighetszoner

Service Bus Premium-SKU: n stöder [Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger felisolerade platser inom samma Azure-region. Service Bus hanterar tre kopior av meddelande arkivet (1 primär och 2 sekundär). Service Bus behåller alla tre kopior som synkroniseras för data-och hanterings åtgärder. Om den primära kopian Miss lyckas höjs en av de sekundära kopiorna till primär utan uppfattande stillestånds tid. Om programmen ser tillfälliga Service Bus från kopplingar kommer logiken för återförsök automatiskt att återansluta till Service Bus. 

När du använder tillgänglighets zoner replikeras både metadata och data (meddelanden) över data Center i tillgänglighets zonen. 

> [!NOTE]
> Tillgänglighetszoner stöd för Azure Service Bus Premium är bara tillgängligt i [Azure-regioner](../availability-zones/az-region.md) där tillgänglighets zoner finns.

Du kan bara aktivera Tillgänglighetszoner på nya namn områden med hjälp av Azure Portal. Service Bus stöder inte migrering av befintliga namn rymder. Du kan inte inaktivera zon redundans när du har aktiverat den i namn området.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Skydd mot avbrott och haverier – Service Bus standard
För att få återhämtning mot data Center avbrott när du använder standard pris nivån för meddelande tjänster Service Bus stöder två metoder: *aktiv* och *passiv* replikering. Om en specifik kö eller ett ämne måste vara tillgängligt i närvaro av ett Data Center avbrott, kan du skapa det i båda namn områdena för varje metod. Båda entiteterna kan ha samma namn. En primär kö kan till exempel nås under **contosoPrimary.ServiceBus.Windows.net/myQueue**, medan dess sekundära motsvarighet kan nås under **contosoSecondary.ServiceBus.Windows.net/myQueue**.

>[!NOTE]
> Installations programmet för **aktiv replikering** och **passiv replikering** är generella lösningar och inte vissa funktioner i Service Bus. Replikeringsfrekvensen (som skickar till 2 olika namn rymder) finns på avsändar programmen och mottagaren måste ha anpassad logik för dubblettidentifiering.

Om programmet inte kräver permanent avsändare från avsändare till mottagare kan programmet implementera en beständig kö på klient sidan för att förhindra meddelande förlust och för att skydda avsändaren från eventuella tillfälliga Service Bus fel.

### <a name="active-replication"></a>Aktiv replikering
Active Replication använder entiteter i båda namn områdena för varje åtgärd. Alla klienter som skickar ett meddelande skickar två kopior av samma meddelande. Den första kopian skickas till den primära entiteten (till exempel **contosoPrimary.ServiceBus.Windows.net/Sales**) och den andra kopian av meddelandet skickas till den sekundära entiteten (till exempel **contosoSecondary.ServiceBus.Windows.net/Sales**).

En klient tar emot meddelanden från båda köerna. Mottagaren bearbetar den första kopian av ett meddelande och den andra kopian ignoreras. För att utelämna duplicerade meddelanden måste avsändaren tagga varje meddelande med en unik identifierare. Båda kopiorna av meddelandet måste vara taggade med samma identifierare. Du kan använda egenskaperna [BrokeredMessage. messageid][BrokeredMessage.MessageId] eller [BrokeredMessage. label][BrokeredMessage.Label] eller en anpassad egenskap för att tagga meddelandet. Mottagaren måste ha en lista över meddelanden som redan har tagits emot.

Exempel på [geo-replikering med Service Bus standard-nivå][Geo-replication with Service Bus Standard Tier] visar aktiv replikering av meddelande enheter.

> [!NOTE]
> Metoden aktiv replikering dubblerar antalet åtgärder, vilket innebär att den här metoden kan leda till högre kostnad.
> 
> 

### <a name="passive-replication"></a>Passiv replikering
I fel fria fall använder passiv replikering endast en av de två meddelande enheterna. En klient skickar meddelandet till den aktiva entiteten. Om åtgärden på den aktiva entiteten Miss lyckas med en felkod som indikerar att data centret som är värd för den aktiva entiteten kanske inte är tillgängliga, skickar klienten en kopia av meddelandet till entiteten säkerhets kopiering. I det här läget byter de aktiva och säkerhetskopierade entiteterna rollerna: den sändande klienten anser att den gamla aktiva entiteten är den nya entiteten för säkerhets kopiering och att den gamla entiteten säkerhets kopiering är den nya aktiva entiteten. Om båda sändnings åtgärderna inte fungerar, förblir rollerna för de två entiteterna oförändrade och ett fel returneras.

En klient tar emot meddelanden från båda köerna. Eftersom det finns en risk att mottagaren får två kopior av samma meddelande, måste mottagaren utelämna dubbletter av meddelanden. Du kan utelämna dubbletter på samma sätt som beskrivs för aktiv replikering.

I allmänhet är passiv replikering mer ekonomiskt än aktiv replikering eftersom endast en åtgärd utförs i de flesta fall. Svars tid, data flöde och monetär kostnad är identiska med det icke-replikerade scenariot.

När du använder passiv replikering kan meddelanden i följande scenarier gå förlorade eller tas emot två gånger:

* **Meddelande fördröjning eller förlust**: Antag att avsändaren har skickat ett meddelande M1 till den primära kön och sedan blir kön otillgänglig innan mottagaren får M1. Avsändaren skickar ett efterföljande meddelande m2 till den sekundära kön. Om den primära kön är tillfälligt otillgänglig, tar mottagaren M1 efter att kön blir tillgänglig igen. I händelse av en katastrof kan mottagaren aldrig få M1.
* **Dubblettidentifiering**: förutsätter att avsändaren skickar ett meddelande m till den primära kön. Service Bus lyckades bearbeta m men det går inte att skicka ett svar. När sändnings åtgärden har nått tids gränsen skickar sändaren en identisk kopia av m till den sekundära kön. Om mottagaren kan ta emot den första kopian av m innan den primära kön blir otillgänglig, får mottagaren båda kopiorna av m på ungefär samma gång. Om mottagaren inte kan ta emot den första kopian av m innan den primära kön blir otillgänglig, tar mottagaren först emot den andra kopian av m, men får sedan en andra kopia av m när den primära kön blir tillgänglig.

[Geo-replikering med Service Bus standard nivå][Geo-replication with Service Bus Standard Tier] exemplet visar passiv replikering av meddelande enheter.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Skydda relä slut punkter mot data Center avbrott eller katastrofer
Med geo-replikering av [Azure Relay](../azure-relay/relay-what-is-it.md) -slutpunkter kan en tjänst som exponerar en Relay-slutpunkt bli nåbar i närvaro av Service Bus avbrott. För att nå geo-replikering måste tjänsten skapa två relä slut punkter i olika namn områden. Namn områdena måste finnas i olika data Center och de två slut punkterna måste ha olika namn. En primär slut punkt kan till exempel nås under **contosoPrimary.ServiceBus.Windows.net/myPrimaryService**, medan dess sekundära motsvarighet kan nås under **contosoSecondary.ServiceBus.Windows.net/mySecondaryService**.

Tjänsten lyssnar sedan på båda slut punkterna och en klient kan anropa tjänsten via endera slut punkten. Ett klient program väljer slumpmässigt en av reläerna som den primära slut punkten och skickar sin begäran till den aktiva slut punkten. Om åtgärden Miss lyckas med en felkod anger det här felet att relä slut punkten inte är tillgänglig. Programmet öppnar en kanal till säkerhets kopierings slut punkten och skickar begäran igen. I det här fallet växlar rollerna aktiva och säkerhets kopierings slut punkter: klient programmet anser att den gamla aktiva slut punkten är den nya säkerhets kopierings slut punkten och att den gamla slut punkten för säkerhets kopieringen är den nya aktiva slut punkten. Om båda sändnings åtgärderna inte fungerar, förblir rollerna för de två entiteterna oförändrade och ett fel returneras.

## <a name="next-steps"></a>Nästa steg
Mer information om haveri beredskap finns i följande artiklar:

* [Azure Service Bus geo-haveri beredskap](service-bus-geo-dr.md)
* [Azure SQL Database affärs kontinuitet][Azure SQL Database Business Continuity]
* [Utforma motståndskraftiga program för Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]:../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png