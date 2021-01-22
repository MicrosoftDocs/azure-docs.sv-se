---
title: Azure Service Bus geo-haveri beredskap | Microsoft Docs
description: Hur du använder geografiska regioner för att redundansväxla och utföra haveri beredskap i Azure Service Bus
ms.topic: article
ms.date: 01/04/2021
ms.openlocfilehash: b25fd1befded253c79267b1b016cef979005d01e
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98676463"
---
# <a name="azure-service-bus-geo-disaster-recovery"></a>Azure Service Bus geo-haveri beredskap

Återhämtning mot katastrofal-avbrott i data bearbetnings resurser är ett krav för många företag och i vissa fall även om bransch bestämmelser kräver det. 

Azure Service Bus är redan utsatt för risken för oåterkalleliga fel på enskilda datorer eller till och med fullständiga rack i kluster som sträcker sig över flera fel domäner i ett Data Center och implementerar transparent fel identifiering och redundansväxling, så att tjänsten fortsätter att arbeta inom garanterade tjänste nivåer och normalt utan märkbart avbrott i händelse av sådana fel. Om ett Service Bus-namnområde har skapats med det aktiverade alternativet för [tillgänglighets zoner](../availability-zones/az-overview.md), är risken att risken för avbrott är en ytterligare spridning mellan tre fysiskt åtskilda anläggningar och att tjänsten har tillräckligt med kapacitets reserver för att snabbt kunna hantera hela den kompletta driften. 

Kluster modellen all-Active Azure Service Bus med stöd för tillgänglighets zon är överlägsna alla lokala meddelande Broker-produkter i form av återhämtning mot grava maskin varu fel och till och med oåterkalleligt avbrott i hela Data Center. Det kan fortfarande finnas grava situationer med omfattande fysisk förstörelse som även dessa åtgärder inte kan skydda sig mot. 

Service Bus geo-Disaster Recovery-funktionen är utformad för att göra det enklare att återställa från en katastrof av den här storleken och överge en misslyckad Azure-region och utan att behöva ändra program konfigurationerna. Att överge en Azure-region omfattar vanligt vis flera tjänster och den här funktionen syftar främst till att bevara integriteten för den sammansatta applikations konfigurationen. Funktionen är globalt tillgänglig för Service Bus Premium-SKU: n. 

Geo-Disaster återställnings funktionen säkerställer att hela konfigurationen av ett namn område (köer, ämnen, prenumerationer och filter) replikeras kontinuerligt från ett primärt namn område till ett sekundärt namn område vid länkning, och det gör att du kan initiera en gång som bara kan flyttas från den primära till den sekundära datorn när som helst. Flyttningen av redundansen pekar på nytt aliasnamn för namn området till det sekundära namn området och bryter sedan ihopparningen. Redundansväxlingen är nästan momentant när den har startats. 

> [!IMPORTANT]
> Funktionen möjliggör omedelbar kontinuitet i åtgärder med samma konfiguration, men **replikerar inte meddelanden som lagras i köer eller ämnes prenumerationer eller köer med obeställbara meddelanden**. För att bevara semantiken i kön kräver sådan replikering inte bara replikering av meddelande data, men för varje tillstånds ändring i Broker. För de flesta Service Bus-namnområden skulle den nödvändiga replikeringstrafiken vara mycket större än program trafiken och med köer med hög data flöde, men de flesta meddelanden replikeras fortfarande till den sekundära medan de redan tas bort från den primära, vilket ger orimlig onödig trafik. För replikerings vägar med hög latens, vilket gäller för många länkningar som du väljer för geo-haveri beredskap, kan det också vara omöjligt för replikeringstrafiken att hålla sig uppdaterad med program trafiken på grund av latens orsakade begränsnings effekter.
 
> [!TIP]
> För att replikera innehållet i köer och ämnes prenumerationer och hantera motsvarande namn områden i aktiva/aktiva konfigurationer för att hantera avbrott och haverier, kan du inte sätta fast på den här funktionen för geo-haveri återställning, men följer de [anvisningar](service-bus-federation-overview.md)som följer.  

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att notera skillnaden mellan "avbrott" och "katastrofer". 

Ett *avbrott* är tillfälligt otillgängligt för Azure Service Bus och kan påverka vissa komponenter i tjänsten, t. ex. ett meddelande arkiv eller till och med hela data centret. När problemet har åtgärd ATS blir Service Bus dock tillgängligt igen. Normalt orsakar ett avbrott inte att meddelanden eller andra data går förlorade. Ett exempel på ett sådant avbrott kan vara ett strömavbrott i data centret. Vissa avbrott är bara korta anslutnings förluster på grund av tillfälliga eller nätverks problem. 

En *katastrof* definieras som en permanent eller mer långsiktig förlust av ett Service Bus-kluster, Azure-region eller data Center. Regionen eller data centret kan komma att bli otillgängliga igen, eller så kan det vara nere i timmar eller dagar. Exempel på sådana katastrofer är brand, översvämning eller jord bävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. I de flesta fall bör det dock inte finnas någon data förlust och meddelanden kan återställas när data centret har säkerhetskopierats.

Funktionen för att återställa geo-haverier i Azure Service Bus är en lösning för katastrof återställning. Begreppen och arbets flödet som beskrivs i den här artikeln gäller katastrof scenarier och inte tillfälligt, eller tillfälliga avbrott. En detaljerad beskrivning av haveri beredskap i Microsoft Azure finns i [den här artikeln](/azure/architecture/resiliency/disaster-recovery-azure-applications).   

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och villkor

Funktionen för haveri beredskap implementerar haveri beredskap för metadata och förlitar sig på de primära och sekundära återställnings namn områdena för haveri beredskap. Observera att funktionen för geo-katastrof återställning endast är tillgänglig för [Premium-SKU: n](service-bus-premium-messaging.md) . Du behöver inte göra några ändringar i anslutnings strängen eftersom anslutningen görs via ett alias.

Följande villkor används i den här artikeln:

-  *Alias*: namnet på en katastrof återställnings konfiguration som du ställer in. Aliaset innehåller en enda stabil fullständigt kvalificerad domän namns anslutnings sträng (FQDN). Program använder den här Ali Aset-anslutningssträngen för att ansluta till ett namn område. Genom att använda ett alias ser du till att anslutnings strängen är oförändrad När redundansväxlingen utlöses.

-  *Primär/sekundär namnrymd*: de namn områden som motsvarar aliaset. Det primära namn området är "aktivt" och tar emot meddelanden (det kan vara ett befintligt eller nytt namn område). Det sekundära namn området är "passiv" och tar inte emot meddelanden. Metadata mellan båda är synkroniserade, så båda kan sömlöst acceptera meddelanden utan program kod eller anslutnings sträng ändringar. För att säkerställa att endast det aktiva namn området tar emot meddelanden måste du använda aliaset. 

    > [!IMPORTANT]
    > Funktionen för geo-katastrof återställning kräver att prenumerationen och resurs gruppen är samma för primära och sekundära namn områden.
-  *Metadata*: entiteter som köer, ämnen och prenumerationer. och deras egenskaper för tjänsten som är associerad med namn området. Observera att endast entiteter och deras inställningar replikeras automatiskt. Meddelanden replikeras inte.

-  *Redundans*: processen att aktivera det sekundära namn området.

## <a name="setup"></a>Installation

Följande avsnitt är en översikt över hur du ställer in koppling mellan namn områden.

![1][]

Installations processen är följande:

1. Etablera ett ***primärt** _ Service Bus Premium-namnområde.

2. Etablera ett _*_sekundärt_*_ Service Bus Premium-namnområde i en region _different från där det primära namn området har etablerats *. Detta gör det möjligt att isolera fel i olika data Center regioner.

3. Skapa koppling mellan det primära namn området och det sekundära namn området för att hämta ***alias** _.

    >[!NOTE] 
    > Om du har [migrerat Azure Service Bus standard namn området till Azure Service Bus Premium](service-bus-migrate-standard-premium.md)måste du använda det befintliga aliaset (t. ex. din Service Bus standard namn rymds anslutnings sträng) för att skapa haveri beredskap med hjälp av _ *PS/CLI** eller **REST API**.
    >
    >
    > Detta beror på att när du migrerar Azure Service Bus standard namn områdets anslutnings sträng/DNS-namn till ett alias till ditt Azure Service Bus Premium-namnområde.
    >
    > Klient programmen måste använda det här aliaset (d.v.s. anslutnings strängen Azure Service Bus standard namn område) för att ansluta till Premium-namnområdet där haveri beredskap har kon figurer ATS.
    >
    > Om du använder portalen för att konfigurera haveri beredskaps konfigurationen kommer portalen att sammanställa detta villkor från dig.


4. Använd *-*_aliaset_* _ som hämtades i steg 3 för att ansluta dina klient program till det geo-Dr-aktiverade primära namn området. Från början pekar alias till det primära namn området.

5. Valfritt Lägg till viss övervakning för att identifiera om en redundansväxling krävs.

## <a name="failover-flow"></a>Flöde för växling vid fel

En redundansväxling utlöses manuellt av kunden (antingen manuellt via ett kommando eller via den klient som ägs av företaget och som utlöser kommandot) och aldrig av Azure. Detta ger kunden fullständig ägande rätt och synlighet för avbrotts lösningar på Azures stamnät.

![4][]

När redundansväxlingen har utlösts –

1. _*_Ali Asets_*_ anslutnings sträng uppdateras för att peka på det sekundära Premium-namnområdet.

2. Klienter (avsändare och mottagare) ansluter automatiskt till det sekundära namn området.

3. Det befintliga paret mellan primär och sekundär Premium-namnrymd är brutet.

När redundansväxlingen har initierats –

1. Om ett annat avbrott inträffar vill du kunna redundansväxla igen. Konfigurera därför ett annat passivt namn område och uppdatera ihopparningen. 

2. Hämta meddelanden från det tidigare primära namn området när det är tillgängligt igen. Efter det använder du det namn området för vanliga meddelanden utanför din geo-återställnings installation eller tar bort det gamla primära namn området.

> [!NOTE]
> Det finns bara stöd för att vidarebefordra semantik. I det här scenariot kan du växla över och sedan para ihop med ett nytt namn område. Det finns inte stöd för att återställa igen. till exempel i ett SQL-kluster. 

Du kan automatisera redundans antingen med övervaknings system eller med anpassade övervaknings lösningar. Sådan automatisering tar dock extra planering och arbete som ligger utanför den här artikelns räckvidd.

![2][]

## <a name="management"></a>Hantering

Om du har gjort ett misstag, Du kan till exempel para ihop fel regioner under den första installationen. du kan när som helst avbryta länkningen av de två namn områdena. Om du vill använda de kopplade namn rymderna som vanliga namn områden, tar du bort aliaset.

## <a name="use-existing-namespace-as-alias"></a>Använd befintligt namn område som alias

Om du har ett scenario där du inte kan ändra anslutningarna till producenter och konsumenter kan du återanvända namn områdets namn som aliasnamn. Se [exempel koden på GitHub här](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR_existing_namespace_name).

## <a name="samples"></a>Exempel

I [exemplen på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/) visas hur du konfigurerar och initierar en redundansväxling. Dessa exempel demonstrerar följande begrepp:

- Ett .NET-exempel och inställningar som krävs i Azure Active Directory för att använda Azure Resource Manager med Service Bus för att konfigurera och aktivera geo-katastrof återställning.
- Steg som krävs för att köra exempel koden.
- Använda ett befintligt namn område som ett alias.
- Steg för att aktivera geo-katastrof återställning via PowerShell eller CLI.
- [Skicka och ta emot](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1) från det aktuella primära eller sekundära namn området med hjälp av aliaset.

## <a name="considerations"></a>Överväganden

Tänk på följande när du är i åtanke med den här versionen:

1. I planeringen av redundansväxling bör du även överväga tids faktorn. Om du till exempel förlorar anslutningen i mer än 15 till 20 minuter kan du välja att initiera redundansväxlingen.

2. Det faktum att inga data replikeras innebär att aktuella aktiva sessioner inte replikeras. Dessutom kanske inte dubblettidentifiering och schemalagda meddelanden fungerar. Nya sessioner kommer nya schemalagda meddelanden och nya dubbletter att fungera. 

3. Att redundansväxla en komplex distribuerad infrastruktur bör återställas [minst en](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) gång.

4. Synkronisering av entiteter kan ta lite tid, ungefär 50-100 entiteter per minut. Prenumerationer och regler räknas också som entiteter.

## <a name="availability-zones"></a>Tillgänglighetszoner

Service Bus Premium-SKU: n stöder också [Tillgänglighetszoner](../availability-zones/az-overview.md)som ger felisolerade platser inom en Azure-region.

> [!NOTE]
> Tillgänglighetszoner stöd för Azure Service Bus Premium är bara tillgängligt i [Azure-regioner](../availability-zones/az-region.md) där tillgänglighets zoner finns.

Du kan bara aktivera Tillgänglighetszoner på nya namn områden med hjälp av Azure Portal. Service Bus stöder inte migrering av befintliga namn rymder. Du kan inte inaktivera zon redundans när du har aktiverat den i namn området.

![3][]

## <a name="private-endpoints"></a>Privata slutpunkter
Det här avsnittet innehåller ytterligare information om hur du använder geo-haveri beredskap med namn områden som använder privata slut punkter. Information om hur du använder privata slut punkter med Service Bus i allmänhet finns i [integrera Azure Service Bus med Azure Private Link](private-link-service.md).

### <a name="new-pairings"></a>Nya par
Om du försöker skapa en koppling mellan ett primärt namn område med en privat slut punkt och ett sekundärt namn område utan en privat slut punkt, kommer ihopparningen att Miss pare ras. Länkningen fungerar bara om både primärt och sekundärt namn område har privata slut punkter. Vi rekommenderar att du använder samma konfigurationer på de primära och sekundära namn områdena och i virtuella nätverk där privata slut punkter skapas. 

> [!NOTE]
> När du försöker koppla det primära namn området till en privat slut punkt och det sekundära namn området, kontrollerar validerings processen endast om det finns en privat slut punkt i det sekundära namn området. Den kontrollerar inte om slut punkten fungerar eller kommer att fungera efter en redundansväxling. Det är ditt ansvar att se till att det sekundära namn området med privat slut punkt fungerar som förväntat efter redundansväxlingen.
>
> Om du vill testa att de privata slut punkts konfigurationerna är identiska skickar du en [Get](/rest/api/servicebus/stable/queues/get) Queues-begäran till det sekundära namn området utanför det virtuella nätverket och kontrollerar att du får ett fel meddelande från tjänsten.

### <a name="existing-pairings"></a>Befintliga länkningar
Om ihopparningen mellan det primära och sekundära namn området redan finns, kommer privat slut punkt att skapas på det primära namn området att Miss pare ras. Lös problemet genom att skapa en privat slut punkt på det sekundära namn området och skapa sedan en för det primära namn området.

> [!NOTE]
> Vi tillåter skrivskyddad åtkomst till det sekundära namn området, men uppdateringar av konfigurationer för privat slut punkt tillåts. 

### <a name="recommended-configuration"></a>Rekommenderad konfiguration
När du skapar en haveri beredskaps konfiguration för ditt program och Service Bus måste du skapa privata slut punkter för både primära och sekundära Service Bus namn områden mot virtuella nätverk som är värdar för både primära och sekundära instanser av ditt program.

Anta att du har två virtuella nätverk: VNET-1, VNET-2 och dessa primära och andra namn områden: Service Bus-Namespace1-Primary, Service Bus-Namespace2-Secondary. Du måste utföra följande steg: 

- På Service Bus-Namespace1-Primary skapar du två privata slut punkter som använder undernät från VNET-1 och VNET-2
- På Service Bus-Namespace2-sekundär skapar du två privata slut punkter som använder samma undernät från VNET-1 och VNET-2 

![Privata slut punkter och virtuella nätverk](./media/service-bus-geo-dr/private-endpoints-virtual-networks.png)


Fördelen med den här metoden är att redundansväxlingen kan ske i program lagret, oberoende av Service Bus namn område. Beakta följande scenarier: 

_ *Endast programredundans:** här kommer programmet inte att finnas i VNet-1, men kommer att flyttas till VNet-2. Eftersom både privata slut punkter har kon figurer ATS på både VNET-1 och VNET-2 för både primär och sekundär namnrymd, fungerar programmet bara. 

**Service Bus namnrymd – endast redundans**: här igen eftersom både privata slut punkter har kon figurer ATS på båda virtuella nätverken för både primära och sekundära namn områden, fungerar programmet bara. 

> [!NOTE]
> Vägledning om geo-haveri beredskap för ett virtuellt nätverk finns i [Virtual Network verksamhets kontinuitet](../virtual-network/virtual-network-disaster-recovery-guidance.md).

## <a name="next-steps"></a>Nästa steg

- Se [REST API referens](/rest/api/servicebus/stable/disasterrecoveryconfigs)för geo-haveri återställning här.
- Kör återställnings exemplet för geo-haveri [på GitHub](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/SBGeoDR2/SBGeoDR2).
- Se det geo-katastrof återställnings [exempel som skickar meddelanden till ett alias](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoDR/TestGeoDR/ConsoleApp1).

Mer information om Service Bus meddelanden finns i följande artiklar:

* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [Använd Service Bus ämnen och prenumerationer](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* [REST-API](/rest/api/servicebus/) 

[1]: ./media/service-bus-geo-dr/geodr_setup_pairing.png
[2]: ./media/service-bus-geo-dr/geo2.png
[3]: ./media/service-bus-geo-dr/az.png
[4]: ./media/service-bus-geo-dr/geodr_failover_alias_update.png
