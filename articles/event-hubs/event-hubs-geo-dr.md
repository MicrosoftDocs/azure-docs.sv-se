---
title: Geo-haveriberedskap – Azure Event Hubs| Microsoft Docs
description: Så här använder du geografiska regioner för redundans och haveriberedskap i Azure Event Hubs
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 504a83772c2ac8e3afc86465899357d0eda4eb92
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478666"
---
# <a name="azure-event-hubs---geo-disaster-recovery"></a>Azure Event Hubs – Geo-haveriberedskap 

Motståndskraft mot allvarliga avbrott i databehandlingsresurser är ett krav för många företag och i vissa fall även krävs enligt branschbestämmelser. 

Azure Event Hubs sprider redan ut risken för oåterkalleliga fel på enskilda datorer eller till och med fullständiga rack över kluster som sträcker sig över flera feldomäner i ett datacenter och implementerar transparenta mekanismer för felidentifiering och redundans så att tjänsten fortsätter att fungera inom de garanterade servicenivåerna och vanligtvis utan märkbara avbrott i händelse av sådana fel. Om ett Event Hubs-namnområde har skapats med det aktiverade alternativet för tillgänglighetszoner [sprids](../availability-zones/az-overview.md)avbrottsrisken ytterligare över tre fysiskt avgränsade anläggningar, och tjänsten har tillräckligt med kapacitetsreserver för att omedelbart hantera den fullständiga, katastrofala förlusten av hela anläggningen. 

Modellen med alla aktiva Azure Event Hubs med stöd för tillgänglighetszoner ger återhämtning mot maskinvarufel och till och med oåterkallelig förlust av hela datacenter. Det kan dock finnas situationer med omfattande fysisk destruktion som inte ens dessa åtgärder kan vara tillräckligt försvar mot. 

Funktionen Event Hubs geo-haveriberedskap är utformad för att göra det enklare att återställa efter en katastrof av den här storleken och avbryta en misslyckad Azure-region för gott och utan att behöva ändra dina programkonfigurationer. Att lämna en Azure-region omfattar vanligtvis flera tjänster och syftet med den här funktionen är främst att hjälpa till att bevara integriteten i den sammansatta programkonfigurationen.  

Funktionen för Geo-Disaster-återställning säkerställer att hela konfigurationen av ett namnområde (Event Hubs, konsumentgrupper och inställningar) kontinuerligt replikeras från ett primärt namnområde till ett sekundärt namnområde när det är kopplat, och att du när som helst kan initiera en redundansförflyttning från den primära till den sekundära när som helst. Redundansflyttningen pekar det valda aliasnamnet på nytt för namnområdet till det sekundära namnområdet och bryter sedan parkopplingen. Redundansen är nästan omedelbar när den har initierats. 

> [!IMPORTANT]
> Funktionen möjliggör omedelbar kontinuitet för åtgärder med samma konfiguration, men **replikerar inte händelsedata**. Om inte katastrofen orsakade förlust av alla zoner kan händelsedata som bevaras i den primära händelsehubben efter redundansen återställas och historiska händelser kan hämtas därifrån när åtkomsten har återställts. Om du vill replikera händelsedata och använda motsvarande namnrymder i aktiva/aktiva konfigurationer för att hantera avbrott och katastrofer ska du inte förlita dig på den här funktionsuppsättningen för geo-haveriberedskap, utan följ replikeringsvägledningen [.](event-hubs-federation-overview.md)  

## <a name="outages-and-disasters"></a>Avbrott och katastrofer

Det är viktigt att notera skillnaden mellan "avbrott" och "katastrofer". Ett **avbrott är** den tillfälliga otillgängligheten för Azure Event Hubs och kan påverka vissa komponenter i tjänsten, till exempel ett meddelandearkiv eller hela datacentret. Men när problemet har åtgärdats blir Event Hubs tillgängligt igen. Normalt orsakar ett avbrott inte förlust av meddelanden eller andra data. Ett exempel på ett sådant avbrott kan vara ett strömavbrott i datacentret. Vissa avbrott är bara korta anslutningsförluster på grund av tillfälliga problem eller nätverksproblem. 

En *katastrof* definieras som permanent eller långsiktig förlust av ett Event Hubs kluster, Azure-region eller datacenter. Regionen eller datacentret kan bli tillgängligt igen eller vara nere i flera timmar eller dagar. Exempel på sådana katastrofer är brand, översvämningar eller jordbävning. En katastrof som blir permanent kan orsaka förlust av vissa meddelanden, händelser eller andra data. I de flesta fall bör det dock inte ske någon dataförlust och meddelanden kan återställas när datacentret har återställts.

Funktionen geo-haveriberedskap i Azure Event Hubs är en haveriberedskapslösning. De begrepp och arbetsflöden som beskrivs i den här artikeln gäller katastrofscenarier och inte tillfälliga eller tillfälliga avbrott. En detaljerad beskrivning av haveriberedskap i Microsoft Azure finns i den [här artikeln.](/azure/architecture/resiliency/disaster-recovery-azure-applications)

## <a name="basic-concepts-and-terms"></a>Grundläggande begrepp och termer

Haveriberedskapsfunktionen implementerar metadata för haveriberedskap och förlitar sig på primära och sekundära namnområden för haveriberedskap. 

Funktionen geo-haveriberedskap är endast tillgänglig för [standard- och dedikerade SKU:er.](https://azure.microsoft.com/pricing/details/event-hubs/) Du behöver inte göra några ändringar i anslutningssträngen eftersom anslutningen görs via ett alias.

Följande termer används i den här artikeln:

-  *Alias:* Namnet på en konfiguration för haveriberedskap som du har ställt in. Aliaset tillhandahåller en enda stabil FQDN-anslutningssträng (fullständigt kvalificerat domännamn). Program använder den här aliasanslutningssträngen för att ansluta till ett namnområde. 

-  *Primärt/sekundärt namnområde:* De namnområden som motsvarar aliaset. Det primära namnområdet är "aktivt" och tar emot meddelanden (kan vara ett befintligt eller nytt namnområde). Det sekundära namnområdet är "passivt" och tar inte emot meddelanden. Metadata mellan båda är synkroniserade, så båda kan sömlöst acceptera meddelanden utan någon programkod eller ändringar i anslutningssträngen. Du måste använda aliaset för att säkerställa att endast det aktiva namnområdet tar emot meddelanden.
-  *Metadata:* Entiteter som händelsehubbbar och konsumentgrupper; och deras egenskaper för tjänsten som är associerade med namnområdet. Endast entiteter och deras inställningar replikeras automatiskt. Meddelanden och händelser replikeras inte. 
-  *Redundans:* Processen för att aktivera det sekundära namnområdet.

## <a name="supported-namespace-pairs"></a>Namnområdespar som stöds
Följande kombinationer av primära och sekundära namnrymder stöds:  

| Primärt namnområde | Sekundärt namnområde | Stöds | 
| ----------------- | -------------------- | ---------- |
| Standard | Standard | Ja | 
| Standard | Dedikerad | Ja | 
| Dedikerad | Dedikerad | Ja | 
| Dedikerad | Standard | Inga | 

> [!NOTE]
> Du kan inte koppla ihop namnrymder som finns i samma dedikerade kluster. Du kan koppla ihop namnområden som finns i separata kluster. 

## <a name="setup-and-failover-flow"></a>Konfigurations- och redundansflöde

Följande avsnitt är en översikt över redundansen och förklarar hur du ställer in den första redundansen. 

![1][]

### <a name="setup"></a>Konfiguration

Du skapar eller använder först ett befintligt primärt namnområde och ett nytt sekundärt namnområde och kopplar sedan ihop de två. Den här parkopplingen ger dig ett alias som du kan använda för att ansluta. Eftersom du använder ett alias behöver du inte ändra anslutningssträngar. Endast nya namnrymder kan läggas till i redundanskopplingen. 

1. Skapa det primära namnområdet.
1. Skapa det sekundära namnområdet i en annan region. Det här är valfritt. Du kan skapa den sekundära namnrymden när du skapar parkopplingen i nästa steg. 
1. I Azure Portal navigerar du till ditt primära namnområde.
1. Välj **Geo-återställning** på den vänstra menyn och välj **Initiera parkoppling** i verktygsfältet. 

    :::image type="content" source="./media/event-hubs-geo-dr/primary-namspace-initiate-pairing-button.png" alt-text="Initiera parkoppling från det primära namnområdet":::    
1. På sidan **Initiera parkoppling** följer du dessa steg:
    1. Välj ett befintligt sekundärt namnområde eller skapa ett i en annan region. I det här exemplet har ett befintligt namnområde valts.  
    1. För **Alias** anger du ett alias för geo-dr-parkopplingen. 
    1. Välj sedan **Skapa**. 

    :::image type="content" source="./media/event-hubs-geo-dr/initiate-pairing-page.png" alt-text="Välj det sekundära namnområdet":::        
1. Du bör se **sidan Geo-DR-alias.** Du kan också navigera till den här sidan från det primära namnområdet genom **att välja Geo-återställning** på den vänstra menyn.

    :::image type="content" source="./media/event-hubs-geo-dr/geo-dr-alias-page.png" alt-text="Aliassida för geo-DR":::    
1. På sidan **Geo-DR-alias** väljer du Principer för **delad åtkomst på** den vänstra menyn för att få åtkomst till aliasets primära anslutningssträng. Använd den här anslutningssträngen i stället för att använda anslutningssträngen direkt till det primära/sekundära namnområdet. 
1. På den **här** översiktssidan kan du utföra följande åtgärder: 
    1. Bryt parkopplingen mellan primära och sekundära namnrymder. Välj **Bryt parkoppling** i verktygsfältet. 
    1. Redundans manuellt till den sekundära namnrymden. Välj **Redundans** i verktygsfältet. 
    
        > [!WARNING]
        > Om du misslyckas aktiveras det sekundära namnområdet och det primära namnområdet tas bort från Geo-Disaster Recovery-parkopplingen. Skapa ett annat namnområde för att ha ett nytt återställningspar för geo-haveriberedskap. 

Slutligen bör du lägga till övervakning för att identifiera om en redundans krävs. I de flesta fall är tjänsten en del av ett stort ekosystem, vilket innebär att automatiska redundanser sällan är möjliga, eftersom redundanser ofta måste utföras synkroniserat med återstående undersystem eller infrastruktur.

### <a name="example"></a>Exempel

I ett exempel på det här scenariot kan du överväga en POS-lösning (Point of Sale) som ger antingen meddelanden eller händelser. Event Hubs skickar dessa händelser till en mappnings- eller omformateringslösning som sedan vidarebefordrar mappade data till ett annat system för vidare bearbetning. Då kan alla dessa system finnas i samma Azure-region. När och vilken del av redundansen som ska redundansen ska fattas beror på dataflödet i infrastrukturen. 

Du kan automatisera redundans med övervakningssystem eller med anpassade övervakningslösningar. Sådan automatisering kräver dock extra planering och arbete, vilket ligger utanför omfånget för den här artikeln.

### <a name="failover-flow"></a>Redundansflöde

Om du initierar redundansen krävs två steg:

1. Om ett annat avbrott inträffar vill du kunna redundans igen. Konfigurera därför ytterligare ett passivt namnområde och uppdatera parkopplingen. 

2. Hämta meddelanden från den tidigare primära namnrymden när den är tillgänglig igen. Använd sedan det namnområdet för vanliga meddelanden utanför konfigurationen för geo-återställning eller ta bort det gamla primära namnområdet.

> [!NOTE]
> Endast semantik för vidarebefordran vid fel stöds. I det här scenariot redundanskopplar du och parkopplar sedan om med ett nytt namnområde. Det går inte att gå tillbaka. till exempel i ett SQL-kluster. 

![2][]

## <a name="management"></a>Hantering

Om du har gjort ett misstag; Om du till exempel har kopplat ihop fel regioner under den första installationen kan du när som helst bryta parkopplingen av de två namnrymderna. Om du vill använda de parkopplade namnrymderna som vanliga namnområden tar du bort aliaset.

## <a name="samples"></a>Exempel

Exemplet [på GitHub visar](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/GeoDRClient) hur du ställer in och initierar en redundans. Det här exemplet visar följande begrepp:

- Inställningar som krävs Azure Active Directory att använda Azure Resource Manager med Event Hubs. 
- Steg som krävs för att köra exempelkoden. 
- Skicka och ta emot från den aktuella primära namnrymden. 

## <a name="considerations"></a>Överväganden

Tänk på följande:

1. Det är Event Hubs geo-haveriberedskap inte replikerar data, och därför kan du inte återanvända det gamla förskjutningsvärdet för din primära händelsehubb på den sekundära händelsehubben. Vi rekommenderar att du startar om händelsemottagaren med någon av följande metoder:

   - *EventPosition.FromStart() –* Om du vill läsa alla data på den sekundära händelsehubben.
   - *EventPosition.FromEnd()* – Om du vill läsa alla nya data från tidpunkten för anslutningen till din sekundära händelsehubb.
   - *EventPosition.FromEnqueuedTime(dateTime)* – Om du vill läsa alla data som tas emot i den sekundära händelsehubben från ett visst datum och en viss tid.

2. När du planerar för redundans bör du även ta hänsyn till tidsfaktorn. Om du till exempel förlorar anslutningen i mer än 15 till 20 minuter kan du välja att starta redundansen. 
 
3. Det faktum att inga data replikeras innebär att aktuella aktiva sessioner inte replikeras. Dessutom kanske dubblettidentifiering och schemalagda meddelanden inte fungerar. Nya sessioner, schemalagda meddelanden och nya dubbletter fungerar. 

4. Fel i en komplex distribuerad infrastruktur [bör repeteras minst](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan) en gång. 

5. Det kan ta lite tid att synkronisera entiteter, cirka 50–100 entiteter per minut.

## <a name="availability-zones"></a>Tillgänglighetszoner 

Den Event Hubs Standard-SKU:n [stöder Tillgänglighetszoner](../availability-zones/az-overview.md), vilket ger fel isolerade platser inom en Azure-region. 

> [!NOTE]
> Stödet Tillgänglighetszoner för Azure Event Hubs Standard är endast tillgängligt i [Azure-regioner](../availability-zones/az-region.md) där det finns tillgänglighetszoner.

Du kan aktivera Tillgänglighetszoner endast för nya namnrymder med hjälp av Azure Portal. Event Hubs stöder inte migrering av befintliga namnområden. Du kan inte inaktivera zonredundans när du har aktivera den i namnområdet.

När du använder tillgänglighetszoner replikeras både metadata och data (händelser) mellan datacenter i tillgänglighetszonen. 

![3][]

## <a name="private-endpoints"></a>Privata slutpunkter
Det här avsnittet innehåller fler överväganden när du använder geo-haveriberedskap med namnområden som använder privata slutpunkter. Mer information om hur du använder privata slutpunkter Event Hubs i allmänhet finns i [Konfigurera privata slutpunkter.](private-link-service.md)

### <a name="new-pairings"></a>Nya par
Om du försöker skapa en koppling mellan ett primärt namnområde med en privat slutpunkt och ett sekundärt namnområde utan en privat slutpunkt misslyckas parkopplingen. Parkopplingen lyckas bara om både primära och sekundära namnrymder har privata slutpunkter. Vi rekommenderar att du använder samma konfigurationer på de primära och sekundära namnrymderna och i virtuella nätverk där privata slutpunkter skapas.  

> [!NOTE]
> När du försöker koppla ihop det primära namnområdet med en privat slutpunkt och ett sekundärt namnområde kontrollerar valideringsprocessen bara om det finns en privat slutpunkt i det sekundära namnområdet. Den kontrollerar inte om slutpunkten fungerar eller kommer att fungera efter redundans. Det är ditt ansvar att se till att det sekundära namnområdet med privat slutpunkt fungerar som förväntat efter redundans.
>
> Om du vill testa att de privata slutpunktskonfigurationerna är samma i primära och sekundära namnrymder skickar du en läsbegäran (till exempel: [Hämta händelsehubb](/rest/api/eventhub/get-event-hub)) till det sekundära namnområdet utanför det virtuella nätverket och kontrollerar att du får ett felmeddelande från tjänsten.

### <a name="existing-pairings"></a>Befintliga parkopplingar
Om det redan finns en koppling mellan den primära och sekundära namnrymden misslyckas skapandet av den privata slutpunkten i det primära namnområdet. Lös problemet genom att först skapa en privat slutpunkt i det sekundära namnområdet och sedan skapa en för det primära namnområdet.

> [!NOTE]
> Även om vi tillåter skrivskyddat åtkomst till det sekundära namnområdet tillåts uppdateringar av privata slutpunktskonfigurationer. 

### <a name="recommended-configuration"></a>Rekommenderad konfiguration
När du skapar en konfiguration för haveriberedskap för ditt program och Event Hubs-namnområden måste du skapa privata slutpunkter för både primära och sekundära Event Hubs-namnrymder mot virtuella nätverk som är värdar för både primära och sekundära instanser av programmet. 

Anta att du har två virtuella nätverk: VNET-1, VNET-2 och dessa primära och sekundära namnområden: EventHubs-Namespace1-Primary, EventHubs-Namespace2-Secondary. Du måste göra följande: 

- På EventHubs-Namespace1-Primary skapar du två privata slutpunkter som använder undernät från VNET-1 och VNET-2
- På EventHubs-Namespace2-Secondary skapar du två privata slutpunkter som använder samma undernät från VNET-1 och VNET-2 

![Privata slutpunkter och virtuella nätverk](./media/event-hubs-geo-dr/private-endpoints-virtual-networks.png)

Fördelen med den här metoden är att redundans kan ske på programnivån oberoende av Event Hubs-namnområdet. Beakta följande scenarier: 

**Redundans endast för program:** Här finns inte programmet i VNET-1 men flyttas till VNET-2. Eftersom både privata slutpunkter konfigureras på både VNET-1 och VNET-2 för både primära och sekundära namnrymder, fungerar programmet bara. 

**Event Hubs** redundans endast för namnrymd: Eftersom båda privata slutpunkterna har konfigurerats på båda virtuella nätverken för både primära och sekundära namnrymder fungerar programmet bara. 

> [!NOTE]
> Vägledning om geo-haveriberedskap för ett virtuellt nätverk finns [i Virtual Network – Affärskontinuisering](../virtual-network/virtual-network-disaster-recovery-guidance.md).
 
## <a name="next-steps"></a>Nästa steg
Läs följande exempel eller referensdokumentation. 
- [.NET GeoDR-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/DotNet/GeoDRClient) 
- [Java GeoDR-exempel](https://github.com/Azure-Samples/eventhub-java-manage-event-hub-geo-disaster-recovery)
- [.NET – Azure.Messaging.EventHubs-exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [.NET – Microsoft.Azure.EventHubs-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet)
- [Java – azure-messaging-eventhubs-exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Java – azure-eventhubs-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
- [Python-exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)
- [JavaScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/javascript)
- [TypeScript-exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples/typescript)
- [Referens för REST-API](/rest/api/eventhub/)

[1]: ./media/event-hubs-geo-dr/geo1.png
[2]: ./media/event-hubs-geo-dr/geo2.png
[3]: ./media/event-hubs-geo-dr/eh-az.png
