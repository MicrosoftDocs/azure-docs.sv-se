---
title: Uppskatta kapaciteten för frågor och index-arbetsbelastningar
titleSuffix: Azure Cognitive Search
description: Justera partitions-och replik dator resurser i Azure Kognitiv sökning, där varje resurs priss ätts i de fakturerbara Sök enheterna.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: b1f742c1de259f6c1c06d9b31a8788699f0b8426
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580035"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Beräkna och hantera kapaciteten för en Azure Kognitiv sökning-tjänst

Innan du [konfigurerar en Sök tjänst](search-create-service-portal.md) och låser en viss pris nivå kan du ta några minuter på att förstå hur kapaciteten fungerar och hur du kan justera repliker och partitioner för att hantera arbets belastnings variationer.

Kapacitet är en funktion i [tjänst nivån](search-sku-tier.md), etablering av maximalt lagrings utrymme per tjänst, per partition och de maximala gränserna för antalet objekt som du kan skapa. Basic-nivån är utformad för appar som har enkla lagrings krav (endast en partition) men med möjlighet att köra i en konfiguration med hög tillgänglighet (tre repliker). Andra nivåer är utformade för vissa arbets belastningar eller mönster, till exempel flera innehavare. Internt har tjänster som skapats på dessa nivåer nytta av maskin vara som hjälper dessa scenarier.

Skalbarheten i Azure Kognitiv sökning baseras på flexibla kombinationer av repliker och partitioner så att du kan variera kapaciteten beroende på om du behöver fler frågor eller indexerings kraft. När en tjänst har skapats kan du öka eller minska antalet repliker eller partitioner oberoende av varandra. Kostnaderna går till varje ytterligare fysisk resurs, men när stora arbets belastningar är klara kan du minska skalan för att sänka fakturan. Beroende på nivån och storleken på justeringen kan tillägg eller minskning av kapaciteten ta var som helst från 15 minuter till flera timmar.

När du ändrar tilldelningen av repliker och partitioner rekommenderar vi att du använder Azure Portal. Portalen tillämpar gränser på tillåtna kombinationer som ligger under de maximala gränserna för en nivå. Men om du behöver en skript-eller kod baserad etablerings metod är [Azure PowerShell](search-manage-powershell.md) eller [hanterings REST API](/rest/api/searchmanagement/services) alternativa lösningar.

## <a name="concepts-search-units-replicas-partitions-shards"></a>Koncept: Sök enheter, repliker, partitioner, Shards

Kapaciteten uttrycks i *Sök enheter* som kan tilldelas i kombinationer av *partitioner* och *repliker*, med hjälp av en underliggande *horisontell partitionering* -mekanism som stöder flexibla konfigurationer:

| Koncept  | Definition|
|----------|-----------|
|*Sök enhet* | En enda ökning av den totala tillgängliga kapaciteten (36 enheter). Det är även fakturerings enheten för en Azure Kognitiv sökning-tjänst. Minst en enhet krävs för att köra tjänsten.|
|*Replik* | Instanserna av Sök tjänsten används främst för att belastningsutjämna frågor. Varje replik är värd för en kopia av ett index. Om du tilldelar tre repliker har du tre kopior av ett index som är tillgängligt för att betjäna fråge förfrågningar.|
|*Partition* | Fysisk lagring och I/O för Läs-och skriv åtgärder (till exempel när du återskapar eller uppdaterar ett index). Varje partition har ett segment av det totala indexet. Om du tilldelar tre partitioner är ditt index indelat i tredje. |
|*Shard* | En segment av ett index. Azure Kognitiv sökning delar upp varje index i Shards för att göra processen att lägga till partitioner snabbare (genom att flytta Shards till nya Sök enheter).|

I följande diagram visas relationen mellan repliker, partitioner, Shards och Sök enheter. Det visar ett exempel på hur ett enskilt index sträcker sig över fyra Sök enheter i en tjänst med två repliker och två partitioner. Var och en av de fyra Sök enheterna lagrar bara hälften av Shards i indexet. Sök enheterna i den vänstra kolumnen lagrar den första halvan av Shards, som består av den första partitionen, medan de i den högra kolumnen lagrar den andra halvan av Shards, som omfattar den andra partitionen. Eftersom det finns två repliker finns det två kopior av varje index Shard. Sök enheterna i den översta raden lagrar en kopia, som består av den första repliken, medan de i den nedersta raden lagrar en annan kopia, som omfattar den andra repliken.

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="Sök index är shardade över partitioner.":::

Diagrammet ovan är bara ett exempel. Många kombinationer av partitioner och repliker är möjliga, upp till högst 36 totalt antal Sök enheter.

I Kognitiv sökning är hantering av Shard en implementerings information och kan inte konfigureras, men att veta att ett index är shardade hjälper till att förstå de tillfälliga avvikelserna i ranknings-och beteenden för autocomplete:

+ Rangordna avvikelser: Sök Resultat beräknas på Shard-nivå först och summeras sedan i en enda resultat uppsättning. Beroende på egenskaperna för Shard-innehållet kan matchningar från en Shard rangordnas högre än matchningarna i en annan. Om du upptäcker att du har intuitiva rangordning i Sök resultaten, beror det förmodligen på att effekterna av horisontell partitionering, särskilt om index är små. Du kan undvika dessa ranknings avvikelser genom att välja att [Beräkna resultat globalt över hela indexet](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions), men detta medför en prestanda försämring.

+ Avvikelser vid komplettering: Autoavsluta-frågor, där matchningar görs på de första flera tecknen i en delvis angiven term, Godkänn en Fuzzy-parameter som forgives små avvikelser i stavning. För automatisk komplettering är en partiell matchning begränsad till termer inom den aktuella Shard. Om till exempel en Shard innehåller "Microsoft" och en partiell term på "micor" anges, kommer sökmotorn att matcha "Microsoft" i den Shard, men inte i andra Shards som innehåller de återstående delarna av indexet.

## <a name="approaching-estimation"></a>Bedömning av bedömning

Kapacitet och kostnaderna för att köra tjänsten finns i handen. Nivåerna begränsar gränserna på två nivåer: innehåll (ett antal index för en tjänst, till exempel) och lagring. Det är viktigt att tänka på båda eftersom den gräns du uppnår först är den effektiva gränsen.

Antalet index och andra objekt bestäms vanligt vis av affärs-och teknik krav. Du kan till exempel ha flera versioner av samma index för aktiv utveckling, testning och produktion.

Lagrings behoven bestäms av storleken på de index som du förväntar dig att bygga. Det finns inga solida Heuristiker eller allmänna prognoser som bidrar till uppskattningar. Det enda sättet att fastställa storleken på ett index är att [bygga ett](search-what-is-an-index.md). Storleken kommer att baseras på importerade data, text analyser och index konfiguration, till exempel om du aktiverar förslag, filtrering och sortering.

För full texts ökning är den primära data strukturen en [inverterad index](https://en.wikipedia.org/wiki/Inverted_index) struktur som har andra egenskaper än källdata. För ett inverterat index bestäms storlek och komplexitet av innehåll, inte nödvändigt vis av mängden data som du matar in i det. En stor data källa med hög redundans kan resultera i ett mindre index än en mindre data mängd som innehåller mycket varierande innehåll. Det går sällan att härleda index storleken baserat på storleken på den ursprungliga data uppsättningen.

Attributen i indexet, till exempel aktivering av filter och sortering, påverkar lagrings kraven. Användning av förslag har också lagrings effekter. Mer information finns i [attribut och index storlek](search-what-is-an-index.md#index-size).

> [!NOTE]
> Även om det går att uppskatta framtida behov för index och lagring kan det vara värt att göra. Om en nivås kapacitet blir för låg måste du etablera en ny tjänst på en högre nivå och sedan [läsa in indexen](search-howto-reindex.md)på nytt. Det finns ingen uppgradering på plats av en tjänst från en nivå till en annan.
>

### <a name="estimate-with-the-free-tier"></a>Uppskatta med den kostnads fria nivån

En metod för att uppskatta kapaciteten är att börja med den kostnads fria nivån. Kom ihåg att den kostnads fria tjänsten erbjuder upp till tre index, 50 MB lagrings utrymme och 2 minuters indexerings tid. Det kan vara svårt att uppskatta en beräknad index storlek med de här begränsningarna, men de är stegen:

+ [Skapa en kostnads fri tjänst](search-create-service-portal.md).
+ Förbered en liten, representativ data uppsättning.
+ Skapa ett index och Läs in dina data. Om data uppsättningen kan finnas i en Azure-datakälla som stöds av indexerare kan du använda [guiden Importera data i portalen](search-get-started-portal.md) för att både skapa och läsa in indexet. Annars bör du använda REST och [Postman](search-get-started-rest.md) eller [Visual Studio Code](search-get-started-vs-code.md) för att skapa indexet och skicka data. Push-modellen kräver att data är i form av JSON-dokument, där fälten i dokumentet motsvarar fält i indexet.
+ Samla in information om indexet, till exempel storlek. Funktioner och attribut påverkar lagringen. Om du till exempel lägger till förslags ställare (sökning efter fråga) ökar lagrings kraven. 

  Med samma data uppsättning kan du prova att skapa flera versioner av ett index, med olika attribut i varje fält, för att se hur lagrings kraven varierar. Mer information finns [i "lagrings konsekvenser" i skapa ett grundläggande index](search-what-is-an-index.md#index-size).

Med en grov uppskattning i handen kan du dubblera beloppet till budget för två index (utveckling och produktion) och sedan välja din nivå.

### <a name="estimate-with-a-billable-tier"></a>Beräkna med en fakturerbar nivå

Dedikerade resurser kan hantera större samplings-och bearbetnings tider för mer realistiska uppskattningar av index antal, storlek och fråga-volymer under utvecklingen. Vissa kunder går direkt till med en fakturerbar nivå och sedan utvärderas igen som utvecklingsprojekt för projektet.

1. [Granska tjänst begränsningar på varje nivå](./search-limits-quotas-capacity.md#index-limits) för att avgöra om lägre nivåer kan stödja det antal index du behöver. På nivåerna Basic, S1 och S2 är index gränserna 15, 50 respektive 200. Lagrings optimerings nivån har en gräns på 10 index eftersom den har utformats för att stödja ett lågt antal mycket stora index.

1. [Skapa en tjänst på en fakturerbar nivå](search-create-service-portal.md):

    + Starta låg, med Basic eller S1, om du inte är säker på den projekterade belastningen.
    + Starta hög, på S2 eller till och med S3 om testningen innehåller storskalig indexering och frågor.
    + Börja med den optimerade lagringen, vid L1 eller L2, om du har indexerat en stor mängd data och frågan är relativt låg, precis som med ett internt affärs program.

1. [Bygg ett första index](search-what-is-an-index.md) för att avgöra hur källdata översätts till ett index. Detta är det enda sättet att uppskatta index storleken. 

1. [Övervaka lagring, tjänst gränser, fråga volym och svars tid](search-monitor-usage.md) i portalen. Portalen visar frågor per sekund, begränsade frågor och Sök svars tid. Alla dessa värden kan hjälpa dig att avgöra om du har valt rätt nivå.

1. Lägg till repliker om du behöver hög tillgänglighet eller om du upplever långsam frågans prestanda.

   Det finns inga rikt linjer för hur många repliker som behövs för att läsa in frågor. Fråga om prestanda beror på komplexiteten i frågan och konkurrerande arbets belastningar. Även om du lägger till repliker tydligt resulterar i bättre prestanda är resultatet inte strikt linjärt: om du lägger till tre repliker garanterar vi inte tredubbel data flöde. Vägledning för att uppskatta frågor per sekund för din lösning finns i [skala för prestanda](search-performance-optimization.md)-och [övervaknings frågor](search-monitor-queries.md).

> [!NOTE]
> Lagrings kraven kan vara deflata om du inkluderar data som aldrig kommer att genomsökas. Vi rekommenderar att dokument endast innehåller de data som du behöver för Sök funktionen. Binära data är inte sökbara och bör lagras separat (kanske i en Azure-tabell eller Blob-lagring). Ett fält ska sedan läggas till i indexet för att innehålla en URL-referens till externa data. Den maximala storleken för ett enskilt Sök dokument är 16 MB (eller mindre om du överför flera dokument i en begäran). Mer information finns i [tjänst begränsningar i Azure kognitiv sökning](search-limits-quotas-capacity.md).
>

**Fråga om volym överväganden**

Frågor per sekund (frågor per sekund) är ett viktigt mått under prestanda justeringen, men det är vanligt vis bara en nivå som beaktas om du förväntar dig hög fråga-volym vid början.

Standard-nivåerna kan ge en balans mellan repliker och partitioner. Du kan öka leveransen genom att lägga till repliker för belastnings utjämning eller lägga till partitioner för parallell bearbetning. Du kan sedan justera efter prestanda när tjänsten har tillhandahållits.

Om du förväntar dig hög varaktiga frågegrupper från början bör du överväga högre standard nivåer, med mer kraftfulla maskin vara. Du kan sedan ta partitioner och repliker offline, eller till och med växla till en tjänst på lägre nivå, om dessa fråga-volymer inte inträffar. Mer information om hur du beräknar frågans data flöde finns i [Azure kognitiv sökning prestanda och optimering](search-performance-optimization.md).

De optimerade lagrings nivåerna är användbara för stora data arbets belastningar och stöder mer övergripande tillgängligt index lagring för när svars tids fördröjnings kraven är mindre viktiga. Du bör fortfarande använda ytterligare repliker för belastnings utjämning och ytterligare partitioner för parallell bearbetning. Du kan sedan justera efter prestanda när tjänsten har tillhandahållits.

**Service nivå avtal**

De kostnads fria nivåerna och för hands versions funktionerna omfattas inte av [service nivå avtal (service avtal)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). För alla fakturerbara nivåer börjar service avtal gälla när du etablerar tillräckligt med redundans för din tjänst. Du måste ha två eller flera repliker för fråga (Läs) service avtal. Du måste ha tre eller fler repliker för fråge-och indexerings-service avtal (Läs-och Skriv behörighet). Antalet partitioner påverkar inte service avtal.

## <a name="tips-for-capacity-planning"></a>Tips för kapacitets planering

+ Tillåt att mått skapas runt frågor och samla in data i användnings mönster (frågor under kontors tid, indexering under låg belastnings tider). Använd dessa data för att informera om tjänste etablerings beslut. Även om det inte är praktiskt vid en tim-eller daglig takt, kan du dynamiskt justera partitioner och resurser för att hantera planerade ändringar i frågornas volymer. Du kan också hantera oplanerad men varaktiga ändringar om nivåerna är tillräckligt långa för att motivera att vidta åtgärder.

+ Kom ihåg att den enda nack delen med etablering är att du kan behöva riva av en tjänst om de faktiska kraven är större än dina förutsägelser. För att undvika avbrott i tjänsten skapar du en ny tjänst på en högre nivå och kör den sida vid sida tills alla appar och begär Anden riktar sig mot den nya slut punkten.

## <a name="when-to-add-capacity"></a>När du ska lägga till kapacitet

En tjänst har inlednings vis tilldelats en minimal resurs nivå som består av en partition och en replik. [Nivån du väljer](search-sku-tier.md) bestämmer partitionens storlek och hastighet, och varje nivå optimeras runt en uppsättning egenskaper som passar olika scenarier. Om du väljer en högre nivå kan du behöva färre partitioner än om du går med S1. En av de frågor som du behöver svara via självdirigerad testning är om en större och dyrare partition ger bättre prestanda än två billigare partitioner på en tjänst som tillhandahålls på en lägre nivå.

En enskild tjänst måste ha tillräckligt med resurser för att hantera alla arbets belastningar (indexering och frågor). Ingen arbets belastning körs i bakgrunden. Du kan schemalägga indexeringen för tider när fråge förfrågningar är mycket mindre frekventa, men tjänsten kan inte på annat sätt prioritera en aktivitet över en annan. En viss mängd redundans utjämnar dessutom frågans prestanda när tjänster eller noder uppdateras internt.

Några rikt linjer för att avgöra om du ska lägga till kapacitet är:

+ Uppfyller kriterierna för hög tillgänglighet för service nivå avtalet
+ Frekvensen för HTTP 503-fel ökar
+ Stora fråga-volymer förväntas

Som en allmän regel kräver Sök program att du behöver fler repliker än partitioner, särskilt när tjänst åtgärderna prioriteras mot frågor till arbets belastningar. Varje replik är en kopia av ditt index, så att tjänsten kan belastningsutjämna begär Anden mot flera kopior. All belastnings utjämning och replikering av ett index hanteras av Azure Kognitiv sökning och du kan ändra antalet repliker som har allokerats för din tjänst när du vill. Du kan allokera upp till 12 repliker i en standard Sök tjänst och tre repliker i en grundläggande Sök tjänst. Replik tilldelning kan göras antingen från [Azure Portal](search-create-service-portal.md) eller något av programmerings alternativen.

Sök efter program som kräver data uppdatering i nära real tid måste ha proportionellt sett fler partitioner än repliker. Att lägga till partitioner sprider Läs-och skriv åtgärder över ett större antal beräknings resurser. Du får också mer disk utrymme för att lagra ytterligare index och dokument.

Slutligen tar större index längre tid att fråga. Därför kanske du upptäcker att varje stegvis ökning i partitioner kräver en mindre men proportionell ökning av repliker. Komplexiteten för dina frågor och frågesträngen kommer att kopplas till hur snabbt frågekörningen ska aktive ras runt.

> [!NOTE]
> Om du lägger till fler repliker eller partitioner ökar kostnaden för att köra tjänsten och det kan leda till små variationer i hur resultaten sorteras. Se till att kontrol lera [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att förstå fakturerings konsekvenserna för att lägga till fler noder. [Diagrammet nedan](#chart) kan hjälpa dig att korsa referenser till antalet Sök enheter som krävs för en bestämd konfiguration. Mer information om hur ytterligare repliker påverkar bearbetning av frågor finns i [sortera resultat](search-pagination-page-layout.md#ordering-results).

<a name="adjust-capacity"></a>

## <a name="add-or-reduce-replicas-and-partitions"></a>Lägg till eller minska repliker och partitioner

1. Logga in på [Azure Portal](https://portal.azure.com/) och välj Sök tjänsten.

1. Under **Inställningar**, öppna sidan **skala** för att ändra repliker och partitioner. 

   Följande skärm bild visar en grundläggande standard som är etablerad med en replik och partition. Formeln längst ned anger hur många Sök enheter som används (1). Om enhets priset var $100 (inte ett verkligt pris) blir månads kostnaden för att köra tjänsten $100 i genomsnitt.

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="Skala sidan visar aktuella värden" border="true":::

1. Använd skjutreglaget för att öka eller minska antalet partitioner. Formeln längst ned anger hur många Sök enheter som används. Välj **Spara**.

   Det här exemplet lägger till en andra replik och partition. Observera antalet Sök enheter; nu är det fyra eftersom fakturerings formeln är repliker multiplicerade med partitioner (2 x 2). Dubbla kapaciteter är mer än dubbelt så mycket kostnaden för att köra tjänsten. Om priset för Sök enheten var $100 skulle den nya månads fakturan nu vara $400.

   För aktuella kostnader per enhet för varje nivå går du till [sidan med priser](https://azure.microsoft.com/pricing/details/search/).

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="Lägg till repliker och partitioner" border="true":::

1. När du har sparat kan du kontrol lera aviseringarna för att bekräfta att åtgärden har slutförts.

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="Spara ändringar" border="true":::

   Ändringar i kapaciteten kan ta var som helst från 15 minuter upp till flera timmar att slutföra. Det går inte att avbryta när processen har startats och det inte finns någon real tids övervakning för replik-och partitions justeringar. Följande meddelande är dock fortfarande synligt när ändringarna pågår.

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="Status meddelande i portalen" border="true":::

> [!NOTE]
> När en tjänst har allokerats kan den inte uppgraderas till en högre nivå. Du måste skapa en Sök tjänst på den nya nivån och läsa in index på nytt. Se [skapa en Azure kognitiv sökning-tjänst i portalen](search-create-service-portal.md) för hjälp med tjänst etablering.
>
> Dessutom hanteras partitioner och repliker exklusivt och internt av tjänsten. Det finns inget begrepp för processor tillhörighet eller tilldelning av en arbets belastning till en speciell nod.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinationer av partition och repliker

En grundläggande tjänst kan ha exakt en partition och upp till tre repliker, för en gräns på tre SUs. Den enda justerbara resursen är repliker. Du behöver minst två repliker för att få hög tillgänglighet för frågor.

Alla standard-och lagrings optimerade Sök tjänster kan utgå från följande kombinationer av repliker och partitioner, beroende på 36-SU-gränsen som tillåts för dessa nivåer. 

|   | **1 partition** | **2 partitioner** | **3 partitioner** | **4 partitioner** | **6 partitioner** | **12 partitioner** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replik** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliker** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliker** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliker** |4 SU |8 SU |12 SU |16 SU |24 SU |Ej tillämpligt |
| **5 repliker** |5 SU |10 SU |15 SU |20 SU |30 SU |Ej tillämpligt |
| **6 repliker** |6 SU |12 SU |18 SU |24 SU |36 SU |Ej tillämpligt |
| **12 repliker** |12 SU |24 SU |36 SU |Saknas |Saknas |Saknas |

SUs, priser och kapacitet beskrivs i detalj på Azure-webbplatsen. Mer information finns i [pris information](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Antalet repliker och partitioner delas upp jämnt i 12 (särskilt, 1, 2, 3, 4, 6, 12). Detta beror på att Azure Kognitiv sökning fördelar varje index i 12 Shards så att det kan spridas i lika stora delar av alla partitioner. Om din tjänst till exempel har tre partitioner och du skapar ett index, kommer varje partition att innehålla fyra Shards av indexet. Hur Azure Kognitiv sökning Shards ett index är en implementerings information som kan komma att ändras i framtida versioner. Även om talet är 12 i dag, bör du inte vänta att antalet alltid är 12 i framtiden.
>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hantera kostnader](search-sku-manage-costs.md)