---
title: Konsekvensnivåer i Azure Cosmos DB
description: Azure Cosmos DB har fem konsekvens nivåer som gör det enklare att balansera eventuell konsekvens, tillgänglighet och svars tid.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: 0a203531e026d00b274ac98784076d33b22666d8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800150"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Konsekvensnivåer i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Distribuerade databaser som förlitar sig på replikering för hög tillgänglighet, låg latens eller både och måste göra en grundläggande kompromiss mellan Läs konsekvens, tillgänglighet, svars tid och data flöde som definieras av [PACLC-satsen](https://en.wikipedia.org/wiki/PACELC_theorem). Linearizability för en stark konsekvens modell är guld standarden för data programmering. Men det lägger till ett brant-pris från högre Skriv fördröjningar på grund av data som måste replikeras och genomföras över stora avstånd. Stark konsekvens kan också drabbas av minskad tillgänglighet (vid fel) eftersom data inte kan replikeras och allokeras i varje region. Eventuell konsekvens ger högre tillgänglighet och bättre prestanda, men det är svårare för program program eftersom data kanske inte är helt konsekventa i alla regioner.

De flesta kommersiellt tillgängliga distribuerade NoSQL-databaser som är tillgängliga på marknaden i dag ger endast stark och eventuell konsekvens. Azure Cosmos DB erbjuder fem väldefinierade nivåer. Från starkast till svagt, är nivåerna:

- *Stark*
- *Begränsad föråldrad*
- *Session*
- *Konsekvent prefix*
- *Slutlig*

Varje nivå ger kompromisser om tillgänglighet och prestanda. Följande bild visar olika konsekvens nivåer som ett spektrum.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="Konsekvent som ett spektrum" border="false" :::

Konsekvens nivåerna är regions oberoende och garanteras för alla åtgärder oavsett vilken region läsningen och skrivningen betjänas, antalet regioner som associeras med ditt Azure Cosmos-konto eller om ditt konto har kon figurer ATS med en eller flera Skriv regioner.

## <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Konsekvensnivåer och API:er för Azure Cosmos DB

Azure Cosmos DB har inbyggt stöd för API-kompatibla API: er för populära databaser. Dessa omfattar MongoDB, Apache Cassandra, Gremlin och Azure Table Storage. När du använder Gremlin API och Tabell-API används den standard konsekvens nivå som kon figurer ATS på Azure Cosmos-kontot. Mer information om konsekvens nivå mappning mellan API för Cassandra eller API för MongoDB och Azure Cosmos DBs konsekvens nivåer finns i [API för Cassandra konsekvens mappning](cassandra-consistency.md) och [API för MongoDB konsekvens mappning](mongodb-consistency.md).

## <a name="scope-of-the-read-consistency"></a>Omfattningen av Läs konsekvensen

Läs konsekvens gäller för en enskild Läs åtgärd som är begränsad i en logisk partition. Läs åtgärden kan utfärdas av en fjärran sluten klient eller en lagrad procedur.

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

Du kan när som helst konfigurera standard konsekvens nivån för ditt Azure Cosmos-konto. Standard konsekvens nivån som kon figurer ATS för ditt konto gäller för alla Azure Cosmos-databaser och behållare under det kontot. Alla läsningar och frågor som utfärdats mot en behållare eller databas använder den angivna konsekvens nivån som standard. Mer information finns i så här [konfigurerar du standard konsekvens nivån](how-to-manage-consistency.md#configure-the-default-consistency-level). Du kan även åsidosätta standard konsekvens nivån för en speciell begäran. mer information finns i så här [åsidosätter du artikeln standard konsekvens nivå](how-to-manage-consistency.md?#override-the-default-consistency-level) .

> [!IMPORTANT]
> Det krävs att du återskapar en SDK-instans när du har ändrat standard konsekvens nivån. Det kan du göra genom att starta om programmet. Detta säkerställer att SDK använder den nya standard konsekvens nivån.

## <a name="guarantees-associated-with-consistency-levels"></a>Garantier kopplade till konsekvens nivåer

Azure Cosmos DB garanterar att 100 procent av Läs begär Anden uppfyller konsekvens garantin för den valda konsekvens nivån. De exakta definitionerna av fem konsekvens nivåer i Azure Cosmos DB med språket TLA + Specification finns i [Azure-Cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub lagrings platsen.

Semantiken för de fem konsekvens nivåerna beskrivs i följande avsnitt.

### <a name="strong-consistency"></a>Stark konsekvens

Stark konsekvens erbjuder en linjärbarhetsgaranti. Linearizability refererar till att betjäna begär Anden samtidigt. Läsningarna garanterar att du returnerar den senaste allokerade versionen av ett objekt. En klient ser aldrig en obekräftad eller delvis skrivning. Användare är alltid garanterade att läsa den senaste dedikerade skrivningen.

  Följande bild illustrerar en stark konsekvens med noter. När data har skrivits till regionen "USA, västra 2" och du läser data från andra regioner får du det senaste värdet:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="Bild av stark konsekvens nivå":::

### <a name="bounded-staleness-consistency"></a>Konsekvens med begränsad föråldring

Vid begränsade inaktuella konsekvens är läsningarna garanterat att respektera den konsekventa prefix-garantin. Läsningarna kan vara sena efter skrivningar med de flesta *"K"* -versioner (det vill säga "uppdateringar") för ett objekt eller med *"T"* tidsintervall, beroende på vilken som uppnås först. Med andra ord kan "föråldrad" konfigureras på två sätt när du väljer begränsat inaktuellitet:

- Antalet versioner (*KB*) av objektet
- Tidsintervallet (*T*) läsningar kan vara fördröjningar bakom skrivningar

För ett enda region konto är det minsta värdet för *K* och *T* 10 Skriv åtgärder eller 5 sekunder. För konton med flera regioner är det lägsta värdet för *K* och *T* 100 000 Skriv åtgärder eller 300 sekunder.

Den begränsade inaktuella inaktuellheten ger total global ordning utanför inaktuella inaktuella fönster. När en klient utför Läs åtgärder inom en region som godkänner skrivningar, är de garantier som tillhandahålls av den begränsade inkonsekventa konsekvensen identiska med dessa garantier med stark konsekvens. När inaktuella fönster närmar sig för antingen tid eller uppdateringar, beroende på vilket som är närmare, kommer tjänsten att begränsa nya skrivningar så att replikeringen kan fångas upp och respektera konsekvens garantin.

I inaktuella fönster har den begränsade inaktuellaheten följande konsekvens garantier:

- Konsekvens för klienter i samma region för ett konto med en enda Skriv region = Strong
- Konsekvens för klienter i olika regioner för ett konto med en enda Skriv region = konsekvent prefix
- Konsekvens för klienter som skriver till en enda region för ett konto med flera Skriv regioner = konsekvent prefix
- Konsekvens för klienter som skriver till olika regioner för ett konto med flera Skriv regioner = eventuell

  Begränsad inaktuellhet väljs ofta av globalt distribuerade program som förväntar sig låga Skriv fördröjningar men kräver total global order garanti. Begränsat föråldrat är bra för program som har grupp samarbete och delning, aktie kurser, publicera-prenumeration/kö osv. Följande bild illustrerar den begränsade inaktuella konsekvensen med noter. Efter att data har skrivits till regionen "USA, västra 2", läser regionerna "USA, östra 2" och "Australien, östra" det skrivna värdet baserat på den konfigurerade maximala fördröjnings tiden eller maximalt antal åtgärder:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="Bild av den begränsade inaktuella konsekvens nivån":::

### <a name="session-consistency"></a>Sessionskonsekvens

I en konsekvens Åtgärd i en enskild klient session är läsningar garanterade att respektera konsekventa prefix, enkla och högpresterande läsningar, högpresterande skrivningar, Läs-och skriv åtgärder och läsnings garantier. Detta förutsätter en enskild "skribent"-session eller delar sessionstoken för flera skrivare.

Klienter utanför sessionen som utför skrivningar får följande garantier:

- Konsekvens för klienter i samma region för ett konto med en enda Skriv region = konsekvent prefix
- Konsekvens för klienter i olika regioner för ett konto med en enda Skriv region = konsekvent prefix
- Konsekvens för klienter som skriver till en enda region för ett konto med flera Skriv regioner = konsekvent prefix
- Konsekvens för klienter som skriver till flera regioner för ett konto med flera Skriv regioner = eventuell

  Konsekvens av sessionen är den mest använda konsekvens nivån för både en region och globalt distribuerade program. Det ger Skriv fördröjning, tillgänglighet och Läs data flöde som är jämförbar med eventuell konsekvens, men som även ger konsekvens garantier som passar de program som är skrivna för att köras i kontexten för en användare. Följande bild illustrerar konsekvensen i sessionen med noter. "Västra USA 2 Writer" och "västra USA 2-läsaren" använder samma session (session A) så att båda läser samma data samtidigt. Regionen "Australien, östra" använder "session B", så den tar emot data senare, men i samma ordning som skrivarna.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="Bild av konsekvens nivå för session":::

### <a name="consistent-prefix-consistency"></a>Konsekvens med konsekvent prefix

I ett enhetligt prefix-alternativ innehåller uppdateringar som returneras vissa prefix för alla uppdateringar, utan luckor. Konsekvent konsekvens nivå för prefix garanterar att läsning aldrig ser inloggade skrivningar.

Om skrivningar utfördes i beställningen `A, B, C` ser en klient antingen `A` , `A,B` , eller `A,B,C` , men inte i ordning-permutationer som `A,C` eller `B,A,C` . Konsekvent prefix ger Skriv fördröjningar, tillgänglighet och Läs data flöde som är jämförbara med den slutliga konsekvensen, men ger även order garantier som uppfyller behoven i scenarier där ordningen är viktig.

Nedan visas konsekvens garantier för konsekvent prefix:

- Konsekvens för klienter i samma region för ett konto med en enda Skriv region = konsekvent prefix
- Konsekvens för klienter i olika regioner för ett konto med en enda Skriv region = konsekvent prefix
- Konsekvens för klienter som skriver till en enda region för ett konto med flera Skriv regioner = konsekvent prefix
- Konsekvens för klienter som skriver till flera regioner för ett konto med flera Skriv regioner = eventuell

Följande bild illustrerar konsekvens för konsekvens med noter. I alla regioner ser läsningarna aldrig ut ur ordning skrivningar:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="Illustration av konsekvent prefix":::

### <a name="eventual-consistency"></a>Slutlig konsekvens

Vid eventuell konsekvens är det ingen beställnings garanti för läsningar. Om det inte finns fler skrivningar slås replikerna samman till slut.  
Eventuell konsekvens är den svagaste typen av konsekvens eftersom en klient kan läsa värdena som är äldre än de som har lästs tidigare. Eventuell konsekvens är idealisk där programmet inte kräver några ordnings garantier. I exemplen ingår antalet retweetar, gillar eller icke-trådade kommentarer. Följande bild illustrerar den slutliga konsekvensen med noter.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="viIllustration av eventuell konsekvens":::

## <a name="consistency-guarantees-in-practice"></a>Konsekvens garantier i praktiken

I praktiken kan du ofta få bättre konsekvens garantier. Konsekvens garantier för en Läs åtgärd motsvarar aktualiteten och ordningen för det databas tillstånd som du begär. Läs-konsekvens är knutet till beställning och spridning av Skriv-/uppdaterings åtgärder.  

Om det inte finns några Skriv åtgärder i databasen, är det troligt att en Läs **åtgärd med konsekvens nivåer för körning**, **session** eller **konsekvent prefix** ger samma resultat som en Läs åtgärd med stark konsekvens nivå.

Om ditt Azure Cosmos-konto har kon figurer ATS med en annan konsekvens nivå än den starka konsekvensen kan du ta reda på sannolikheten att dina klienter kan få starka och konsekventa läsningar för dina arbets belastningar genom att titta på Probabilistically-måttet ( *Bound* ). Det här måttet visas i Azure Portal. mer information finns i [övervaka Probabilistically-gränser för Inaktuellitet (PBS)](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic-begränsad föråldrad visar hur den slutliga konsekvensen är. Det här måttet ger en inblick i hur ofta du kan få en bättre konsekvens än den konsekvens nivå som du för närvarande har konfigurerat på ditt Azure Cosmos-konto. Med andra ord kan du se sannolikheten (mätt i millisekunder) för att få starkt konsekventa läsningar för en kombination av Skriv-och Läs regioner.

## <a name="consistency-levels-and-latency"></a>Konsekvens nivåer och svars tid

Läs fördröjningen för alla konsekvens nivåer garanterar alltid att vara mindre än 10 millisekunder vid 99 percentil. Den genomsnittliga Läs fördröjningen, vid den 50: e percentilen, är normalt 4 millisekunder eller mindre.

Skriv fördröjningen för alla konsekvens nivåer garanterar alltid att vara mindre än 10 millisekunder vid 99 percentil. Den genomsnittliga Skriv fördröjningen, vid den 50: e percentilen, är vanligt vis 5 millisekunder eller mindre. Azure Cosmos-konton som sträcker sig över flera regioner och som har kon figurer ATS med stark konsekvens är ett undantag till denna garanti.

### <a name="write-latency-and-strong-consistency"></a>Skriv fördröjning och stark konsekvens

För Azure Cosmos-konton som kon figurer ATS med stark konsekvens med mer än en region, är Skriv fördröjningen lika med två gånger tur och retur-tid (söksvar) mellan någon av de två precisaste regionerna, plus 10 millisekunder i 99 percentilen. Hög blandning av nätverk mellan regionerna översätts till högre latens för Cosmos DB begär Anden eftersom stark konsekvens bara slutför en åtgärd efter att ha kontrollerat att den har allokerats till alla regioner i ett konto.

Den exakta svars tiden för försvars tid är en funktion av hastigheten-lätt avstånd och Azure-nätverk sto pol Ogin. Azure-nätverk ger inte någon latens-service avtal för den förblandning som finns mellan två Azure-regioner, men det publicerar [Azure Network tur och retur latens-statistik](../networking/azure-network-latency.md). För ditt Azure Cosmos-konto visas replikeringens fördröjningar i Azure Portal. Du kan använda Azure Portal (gå till bladet mått, Välj fliken konsekvens) för att övervaka fördröjningen mellan olika regioner som är associerade med ditt Azure Cosmos-konto.

> [!IMPORTANT]
> Stark konsekvens för konton med regioner som sträcker sig över 5000 miles (8000 kilo meter) blockeras som standard på grund av hög Skriv fördröjning. Om du vill aktivera den här funktionen kontaktar du supporten.

## <a name="consistency-levels-and-throughput"></a>Konsekvens nivåer och data flöde

- För stark och begränsad föråldrad är läsningar gjorda mot två repliker i en fyra replik uppsättning (minoritets kvorum) för att tillhandahålla konsekvens garantier. Session, konsekvent prefix och gör det enkelt att läsa en enskild replik. Resultatet är att Läs data flöde för samma antal enheter för programbegäran är hälften av de andra konsekvens nivåerna.

- För en specifik typ av Skriv åtgärd, till exempel infoga, Ersätt, upsert och Delete, är Skriv data flödet för enheter för programbegäran identiskt för alla konsekvens nivåer.

|**Konsekvensnivå**|**Kvorum läsningar**|**Skrivningar till kvorum**|
|--|--|--|
|**Stark**|Lokal minoritet|Global majoritet|
|**Begränsad föråldring**|Lokal minoritet|Lokal majoritet|
|**Session**|Enskild replik (med sessionstoken)|Lokal majoritet|
|**Konsekvent prefix**|Enskild replik|Lokal majoritet|
|**Slutlig**|Enskild replik|Lokal majoritet|

> [!NOTE]
> RU/s-kostnaden för läsningar av lokala minoritets läsningar är två gånger för svagare konsekvens nivåer eftersom läsningar görs från två repliker för att ge konsekvens garantier för stark och begränsad föråldrad.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Konsekvens nivåer och data hållbarhet

I en globalt distribuerad databas miljö finns det ett direkt förhållande mellan konsekvens nivån och data hållbarhet i närvaro av ett områdes omfattande avbrott. När du utvecklar din verksamhets kontinuitets plan måste du förstå hur lång tid det tar innan programmet återställs fullständigt efter en störnings händelse. Tiden som krävs för att ett program ska återställas fullständigt kallas för **återställnings tids mål** (**RTO**). Du måste också förstå hur lång tid det tar för nya data uppdateringar som programmet kan tolerera vid återställning efter en störnings händelse. Tids perioden för uppdateringar som du kanske har råd att förlora kallas för **återställnings punkt mål (återställnings punkt mål** ).

I tabellen nedan definieras relationen mellan konsekvens modell och data hållbarhet i närvaro av ett brett avbrott i regionen. Det är viktigt att notera att i ett distribuerat system, även med stark konsekvens, är det omöjligt att ha en distribuerad databas med en återställnings-och RTO av noll på grund av [Cap satsen](https://en.wikipedia.org/wiki/CAP_theorem).

|**Region (er)**|**Replikeringsläget**|**Konsekvens nivå**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|En eller flera Skriv regioner|Vilken konsekvens nivå som helst|< 240 minuter|<1 vecka|
|>1|Enskild Skriv region|Session, konsekvent prefix, eventuell|< 15 minuter|< 15 minuter|
|>1|Enskild Skriv region|Begränsad föråldring|*K*  &  *T*|< 15 minuter|
|>1|Enskild Skriv region|Stark|0|< 15 minuter|
|>1|Flera Skriv regioner|Session, konsekvent prefix, eventuell|< 15 minuter|0|
|>1|Flera Skriv regioner|Begränsad föråldring|*K*  &  *T*|0|

*K* = antalet *"K"* versioner (d.v.s. uppdateringar) för ett objekt.

*T* = tidsintervallet *"T"* sedan den senaste uppdateringen.

För ett enda region konto är det minsta värdet för *K* och *T* 10 Skriv åtgärder eller 5 sekunder. För konton med flera regioner är det lägsta värdet för *K* och *T* 100 000 Skriv åtgärder eller 300 sekunder. Detta definierar lägsta återställnings punkt för data när gräns inaktuellheten används.

## <a name="strong-consistency-and-multiple-write-regions"></a>Stark konsekvens och flera Skriv regioner

Cosmos-konton som kon figurer ATS med flera Skriv regioner kan inte konfigureras för stark konsekvens eftersom det inte är möjligt för ett distribuerat system att leverera noll och en RTO på noll. Det finns dessutom inga fördelar med Skriv fördröjning för att använda stark konsekvens med flera Skriv regioner eftersom en skrivning till en region måste replikeras och allokeras till alla konfigurerade regioner i kontot. Detta resulterar i samma Skriv fördröjning som ett enda Skriv regions konto.

## <a name="additional-reading"></a>Mer att läsa

Läs mer om konsekvens koncept i följande artiklar:

- [Hög nivå TLA + specifikationer för de fem konsekvens nivåerna som erbjuds av Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla)
- [Replikerad data konsekvens förklaras genom baseboll (video) av Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Replikerad data konsekvens förklaras genom baseboll (whitepaper) av Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Sessioner garanterar svagt konsekvent replikerade data](https://dl.acm.org/citation.cfm?id=383631)
- [Konsekvens av konsekvens i moderna distribuerade databas system design: CAP är bara en del av berättelsen](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Probabilistic-begränsad föråldrad (PBS) för praktiska delar av kvorum](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Eventuellt konsekvent återbesökning](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>Nästa steg

Mer information om konsekvens nivåer i Azure Cosmos DB finns i följande artiklar:

- [Konfigurera standardkonsekvensnivån](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Åsidosätta standardkonsekvensnivån](how-to-manage-consistency.md#override-the-default-consistency-level)
- [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/)
