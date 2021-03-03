---
title: Översikt över Azures dedikerade värdar för virtuella datorer
description: Lär dig mer om hur Azure-dedikerade värdar kan användas för att distribuera virtuella datorer.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 46de182c7acfaf75b2e65fa318717348dd1c4b73
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667355"
---
# <a name="azure-dedicated-hosts"></a>Dedikerade Azure-värdar

Den dedikerade Azure-värden är en tjänst som tillhandahåller fysiska servrar som kan vara värd för en eller flera virtuella datorer som är dedikerade till en Azure-prenumeration. Dedikerade värdar är samma fysiska servrar som används i våra data Center, som tillhandahålls som en resurs. Du kan etablera dedikerade värdar inom en region, tillgänglighets zon och fel domän. Sedan kan du placera virtuella datorer direkt i dina etablerade värdar, i vilken konfiguration som passar bäst för dina behov.


## <a name="benefits"></a>Fördelar 

Att reservera hela värden ger följande fördelar:

-   Maskin varu isolering på den fysiska server nivån. Inga andra virtuella datorer kommer att placeras på värdarna. Dedikerade värdar distribueras i samma data Center och delar samma nätverk och underliggande lagrings infrastruktur som andra icke-isolerade värdar.
-   Kontroll över underhålls händelser som initieras av Azure-plattformen. Majoriteten av underhålls händelser har liten inverkan på dina virtuella datorer, men det finns vissa känsliga arbets belastningar där varje sekund paus kan påverka. Med dedikerade värdar kan du välja en underhålls period för att minska påverkan på tjänsten.
-   Med Azure Hybrid-förmånen kan du ta med dina egna licenser för Windows och SQL till Azure. Med hybrid förmånerna får du ytterligare fördelar. Mer information finns i [Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/).


## <a name="groups-hosts-and-vms"></a>Grupper, värdar och virtuella datorer  

![Vy över de nya resurserna för dedikerade värdar.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

En **värd grupp** är en resurs som representerar en samling dedikerade värdar. Du skapar en värd grupp i en region och en tillgänglighets zon och lägger till värdar i den.

En **värd** är en resurs som är mappad till en fysisk server i ett Azure-datacenter. Den fysiska servern allokeras när värden skapas. En värd skapas i en värd grupp. En värd har en SKU som beskriver vilka VM-storlekar som kan skapas. Varje värd kan vara värd för flera virtuella datorer, med olika storlekar, så länge de är från samma storleks serie.


## <a name="high-availability-considerations"></a>Överväganden för hög tillgänglighet 

För hög tillgänglighet bör du distribuera flera virtuella datorer, sprida över flera värdar (minst 2). Med Azures dedikerade värdar har du flera alternativ för att etablera infrastrukturen för att forma gränser för fel isolering.

### <a name="use-availability-zones-for-fault-isolation"></a>Använd Tillgänglighetszoner för fel isolering

Tillgänglighets zoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. En värd grupp skapas i en enda tillgänglighets zon. När den har skapats placeras alla värdar i den zonen. Om du vill uppnå hög tillgänglighet i flera zoner måste du skapa flera värd grupper (en per zon) och sprida värdarna efter behov.

Om du tilldelar en värd grupp till en tillgänglighets zon måste alla virtuella datorer som skapas på värden skapas i samma zon.

### <a name="use-fault-domains-for-fault-isolation"></a>Använd fel domäner för fel isolering

En värd kan skapas i en angiven fel domän. Precis som VM i en skalnings uppsättning eller tillgänglighets uppsättning placeras värdar i olika fel domäner i olika fysiska rack i data centret. När du skapar en värd grupp måste du ange antalet fel domäner. När du skapar värdar i värd gruppen tilldelar du fel domänen för varje värd. De virtuella datorerna kräver ingen tilldelning av fel domäner.

Fel domäner är inte samma som samplacering. Att ha samma fel domän för två värdar innebär inte att de är i närheten av varandra.

Fel domäner är begränsade till värd gruppen. Du bör inte göra några antaganden om anti-tillhörighet mellan två värd grupper (om de inte finns i olika tillgänglighets zoner).

Virtuella datorer som distribueras till värdar med olika fel domäner har sina underliggande Managed disks-tjänster på flera stämplar, för att öka skyddet av fel isolering.

### <a name="using-availability-zones-and-fault-domains"></a>Använda Tillgänglighetszoner-och fel domäner

Du kan använda båda funktionerna tillsammans för att uppnå ännu mer fel isolering. I det här fallet ska du ange tillgänglighets zonen och antalet fel domäner i för varje värd grupp, tilldela en feldomän till var och en av dina värdar i gruppen och tilldela en tillgänglighets zon till var och en av dina virtuella datorer

Exempel mal len Resource Manager som hittas [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) använder zoner och fel domäner för att sprida värdar för maximal återhämtning i en region.


## <a name="manual-vs-automatic-placement"></a>Manuell eller automatisk placering 

När du skapar en virtuell dator i Azure kan du välja vilken dedikerad värd som ska användas. Du kan också använda alternativet för att automatiskt placera dina virtuella datorer på befintliga värdar i en värd grupp. 

När du skapar en ny värd grupp måste du kontrol lera att inställningen för automatisk VM-placering är markerad. När du skapar den virtuella datorn väljer du värd gruppen och låter Azure välja den bästa värden för den virtuella datorn. 

Värd grupper som är aktiverade för automatisk placering kräver inte att alla virtuella datorer placeras automatiskt. Du kommer fortfarande att kunna välja en värd explicit, även när automatisk placering har valts för värd gruppen. 

### <a name="limitations"></a>Begränsningar

Kända problem och begränsningar när du använder automatisk placering av virtuella datorer:

- Du kommer inte att kunna använda Azure Hybrid-förmåner på dina dedikerade värdar.
- Du kommer inte att kunna distribuera om den virtuella datorn. 
- Du kommer inte att kunna använda Lsv2, NVasv4, NVsv3, Msv2 eller M-seriens virtuella datorer med dedikerade värdar 


## <a name="virtual-machine-scale-set-support"></a>Stöd för skalnings uppsättning för virtuell dator

Med skalnings uppsättningar för virtuella datorer kan du behandla en grupp med virtuella datorer som en enda resurs och tillämpa principer för tillgänglighet, hantering, skalning och dirigering som en grupp. Befintliga dedikerade värdar kan också användas för skalnings uppsättningar för virtuella datorer. 

När du skapar en skalnings uppsättning för virtuella datorer kan du ange att en befintlig värd grupp ska ha alla VM-instanser som skapats på dedikerade värdar.

Följande krav gäller när du skapar en skalnings uppsättning för en virtuell dator i en särskild värd grupp:

- Automatisk placering av virtuella datorer måste aktive ras.
- Tillgänglighets inställningen för värd gruppen måste matcha din skalnings uppsättning. 
    - En regional värd grupp (skapad utan att ange en tillgänglighets zon) ska användas för regionala skalnings uppsättningar.
    - Värd gruppen och skalnings uppsättningen måste använda samma tillgänglighets zon. 
    - Antalet fel domäner för värd grupp nivån måste matcha antalet fel domäner för din skalnings uppsättning. Med Azure Portal kan du ange *maximal spridning* för din skalnings uppsättning, vilket anger antalet fel domäner för 1.
- Dedikerade värdar bör först skapas, med tillräcklig kapacitet och samma inställningar för skalnings uppsättnings zoner och fel domäner.
- De VM-storlekar som stöds för dina dedikerade värdar ska matcha det som används för din skalnings uppsättning.

Inte alla inställningar för dirigering och optimering av skalnings uppsättning stöds av dedikerade värdar. Använd följande inställningar för din skalnings uppsättning: 
- Överetablering rekommenderas inte och är inaktive rad som standard. Du kan aktivera överetablering, men allokeringen av skalnings uppsättningen kommer att Miss inaktive ras om värd gruppen inte har kapacitet för alla virtuella datorer, inklusive de överetablerade instanserna. 
- Använda ScaleSetVM Orchestration-läge 
- Använd inte närhets placerings grupper för samplacering



## <a name="maintenance-control"></a>Kontroll av underhåll

Den infrastruktur som stöder dina virtuella datorer kan ibland uppdateras för att förbättra tillförlitligheten, prestandan, säkerheten och för att starta nya funktioner. Azure-plattformen försöker minimera effekten av plattforms underhåll närhelst det är möjligt, men kunder med *underhålls känsliga* arbets belastningar kan inte tolerera några sekunder på att den virtuella datorn måste frysas eller kopplas från för underhåll.

Med **underhålls kontrollen** får kunderna möjlighet att hoppa över regelbundna plattforms uppdateringar som är schemalagda på sina dedikerade värdar, och sedan använda dem vid den tidpunkt som de väljer i ett rullande 35-dagars fönster. I underhålls fönstret kan du tillämpa underhåll direkt på värdnivå, i vilken ordning som helst. När underhålls perioden är över kommer Microsoft att gå framåt och tillämpa det väntande underhållet på värdarna i en ordning som inte kan följa de användardefinierade fel domänerna.

Mer information finns i [Hantera plattforms uppdateringar med underhålls kontroll](./maintenance-control.md).

## <a name="capacity-considerations"></a>Överväganden för kapaciteter

När en dedikerad värd har allokerats tilldelar Azure den till den fysiska servern. Detta garanterar att kapaciteten är tillgänglig när du behöver etablera den virtuella datorn. Azure använder hela kapaciteten i regionen (eller zonen) för att välja en fysisk server för värden. Det innebär också att kunderna kan vänta på att kunna utöka sina dedikerade värddatorer utan att det är svårt att köra slut på utrymme i klustret.

## <a name="quotas"></a>Kvoter

Det finns två typer av kvoter som används när du distribuerar en dedikerad värd.

1. Dedikerad värd vCPU kvot. Standard kvot gränsen är 3000 virtuella processorer per region.
1. Kvot för VM-storleks familj. En prenumeration där **du betalar per** användning kan till exempel bara ha en kvot på 10 virtuella processorer som är tillgängliga för Dsv3-storleks serien i regionen USA, östra. Om du vill distribuera en Dsv3-dedikerad värd måste du begära en kvot ökning till minst 64 virtuella processorer innan du kan distribuera den dedikerade värden. 

Om du vill begära en kvot ökning skapar du en support förfrågan i [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

Genom att etablering av en dedikerad värd förbrukas både dedikerad värd vCPU och vCPU-kvoten för VM-familjen, men den kommer inte att använda den regionala vCPU.


![Skärm bild av sidan användning och kvoter i portalen](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Mer information finns i [vCPU kvoter för virtuella datorer](./windows/quotas.md).

Den kostnads fria utvärderings versionen och MSDN-prenumerationen har inte kvot för Azure-dedikerade värdar.

## <a name="pricing"></a>Prissättning

Användare debiteras per dedikerad värd, oavsett hur många virtuella datorer som distribueras. I din månads instruktion visas en ny fakturerbar resurs typ för värdar. De virtuella datorerna på en dedikerad värd visas fortfarande i din instruktion, men kommer att ha priset 0.

Värd priset anges baserat på VM-serien, typ (maskin varu storlek) och region. Ett värd pris är i förhållande till den största VM-storlek som stöds på värden.

Program varu licensiering, lagring och nätverks användning faktureras separat från värden och de virtuella datorerna. Det finns ingen ändring av de fakturerbara artiklarna.

Mer information finns i pris information för [Azure-dedikerad värd](https://aka.ms/ADHPricing).

Du kan också spara pengar med en [reserverad instans av Azure-dedikerade värdar](prepay-dedicated-hosts-reserved-instances.md).
 
## <a name="sizes-and-hardware-generations"></a>Storlekar och maskin varu generationer

En SKU definieras för en värd och den representerar den virtuella datorns storleks serie och typ. Du kan blanda flera virtuella datorer av olika storlekar inom en enda värd så länge de är i samma storleks serie. 

*Typen* är maskin varu generationen. Olika maskin varu typer för samma VM-serie kommer från olika CPU-leverantörer och har olika processor generationer och antalet kärnor. 

Storlekarna och maskin varu typerna varierar beroende på region. Mer information hittar du på [prissättnings sidan](https://aka.ms/ADHPricing) för värden.

> [!NOTE]
> När en dedikerad värd är etablerat kan du inte ändra storlek eller typ. Om du behöver en annan typ av typ måste du skapa en ny värd.  

## <a name="host-life-cycle"></a>Värd livs cykel


Azure övervakar och hanterar dina värdars hälso status. Följande tillstånd kommer att returneras när du frågar din värd:

| Hälsotillstånd   | Beskrivning       |
|----------|----------------|
| Värd tillgänglig     | Det finns inga kända problem med värden.   |
| Värd under undersökning  | Vi har problem med värden som vi tittar på. Detta är ett över gångs tillstånd som krävs för att Azure ska kunna testa och identifiera omfattningen och rotor saken för det problem som identifierats. Virtuella datorer som körs på värden kan påverkas. |
| Väntande värd tilldelning   | Azure kan inte återställa värden till felfritt tillstånd och be dig att distribuera om de virtuella datorerna från den här värden. Om `autoReplaceOnFailure` är aktive rad, är de virtuella datorerna *tjänsten reservad* för maskin vara som är felfri. Annars kanske den virtuella datorn körs på en värd som inte fungerar.|
| Värd friallokerad  | Alla virtuella datorer har tagits bort från värden. Du debiteras inte längre för den här värden eftersom maskin varan togs bort från rotationen.   |


## <a name="next-steps"></a>Nästa steg

- Du kan distribuera en dedikerad värd med hjälp av [Azure PowerShell](./windows/dedicated-hosts-powershell.md), [portalen](./dedicated-hosts-portal.md)och [Azure CLI](./linux/dedicated-hosts-cli.md).

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.

- Du kan också spara pengar med en [reserverad instans av Azure-dedikerade värdar](prepay-dedicated-hosts-reserved-instances.md).
