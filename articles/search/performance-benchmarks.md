---
title: Mått
titleSuffix: Azure Cognitive Search
description: Lär dig mer om prestanda för Azure Kognitiv sökning via olika prestanda mått
author: dereklegenzoff
manager: luisca
ms.author: delegenz
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 9f4473d6c8a584bf60e5c8fe2d69d6a56a55e71d
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108444"
---
# <a name="azure-cognitive-search-performance-benchmarks"></a>Prestanda mått för Azure Kognitiv sökning

Azure Kognitiv söknings prestanda beror på [flera olika faktorer](search-performance-tips.md) , inklusive storleken på Sök tjänsten och de typer av frågor som du skickar. För att hjälpa till att beräkna storleken på Sök tjänsten som krävs för din arbets belastning har vi kört flera riktmärken för att dokumentera prestanda för olika Sök tjänster och konfigurationer. Dessa benchmarks på inget sätt garanterar en viss nivå av prestanda från tjänsten, men kan ge dig en uppfattning om de prestanda du kan förväntar dig.

För att kunna använda flera olika användnings fall körde vi benchmarks för två huvud scenarier:

* **E-handelssökning** – detta riktmärke emulerar ett verkligt e-handelsscenario och bygger på den nordiska e- [handelsCDON](https://cdon.com).
* **Dokuments ökning** – det här scenariot består av nyckelords sökning över fullständiga text dokument från [semantiska](http://s2-public-api-prod.us-west-2.elasticbeanstalk.com/corpus/download/). Detta emulerar en typisk dokument Sök lösning.

De här scenarierna reflekterar olika användnings fall, men alla scenarier är olika så vi rekommenderar alltid prestanda testning av din enskilda arbets belastning. Vi har publicerat en [lösning för prestanda testning med jmeter](https://github.com/Azure-Samples/azure-search-performance-testing) så att du kan köra liknande tester mot din egen tjänst.

## <a name="testing-methodology"></a>Testa metodik

För att kunna mäta prestanda för Azure-Kognitiv sökning kördes vi tester för två olika scenarier på olika nivåer och kombinationer av repliker/partitioner.

Följande metod användes för att skapa dessa benchmarks:

1. Testet börjar vid `X` frågor per sekund (frågor per sekund) i 180 sekunder. Detta var vanligt vis 5 eller 10 frågor per sekund.
2. FRÅGOR per sekund ökade sedan med `X` och kördes i ytterligare en 180 sekunder
3. Var 180 sekund ökar testet med `X` frågor per sekund tills den genomsnittliga svars tiden ökat över 1000 MS eller mindre än 99% av frågorna har slutförts.

Diagrammet nedan ger ett visuellt exempel på hur frågans inläsning ser ut:

![Exempel test](./media/performance-benchmarks/example-test.png)

Varje scenario använder minst 10 000 unika frågor för att undvika att testerna skevas med cachelagring.

> [!IMPORTANT]
> De här testerna omfattar bara fråge arbets belastningar. Om du förväntar dig att ha en hög volumne av indexerings åtgärder måste du vara noga med att bedöma i beräknings-och prestanda testningen. Exempel kod för simulering av indexering finns i den här [självstudien](tutorial-optimize-indexing-push-api.md).

### <a name="definitions"></a>Definitioner

- **Maximalt frågor per sekund** – det högsta antalet frågor per sekund som anges nedan baseras på den högsta frågor per sekund som uppnås i ett test där 99% av frågorna har slutförts utan begränsning och Genomsnittlig latens i 1000 MS.

- **Procent andel av Max frågor per sekund** – en procent andel av den högsta frågor per sekund som uppnås för ett visst test. Om till exempel ett angivet test uppnår högst 100 frågor per sekund skulle 20% av Max frågor per sekund vara 20 frågor per sekund.

- **Svars tid** – serverns svars tid för en fråga. numren omfattar inte tur och [RETUR-fördröjning](https://en.wikipedia.org/wiki/Round-trip_delay). Värdena nedan är i millisekunder (MS).

### <a name="disclaimer"></a>Disclaimer

Koden som vi använde för att köra dessa prestandatester finns [här](https://github.com/Azure-Samples/azure-search-performance-testing/tree/main/other_tools). Det är värt att notera att vi har observerat något lägre frågor per sekund-nivåer med [jmeter-lösningen för prestanda testning](https://github.com/Azure-Samples/azure-search-performance-testing) än i benchmarks nedan. Skillnaderna kan hänföras till skillnader i testens format. Detta talar om vikten av att göra dina prestandatester så lika med din produktions belastning som möjligt.

Dessa benchmarks på inget sätt garanterar en viss prestanda nivå från tjänsten, men kan ge dig en uppfattning om de prestanda du kan förväntar dig baserat på ditt scenario.

Om du har frågor eller problem kan du kontakta oss på azuresearch_contact@microsoft.com .

## <a name="benchmark-1-e-commerce-search"></a>Benchmark 1: E-handelssökning

:::row:::
   :::column span="1":::
      ![CDON-logotyp](./media/performance-benchmarks/cdon-logo-160px2.png)
   :::column-end:::
   :::column span="3":::
      Detta riktmärke skapades i samarbete med e-handelscompany, [CDON](https://cdon.com), den nordiska regionens största online-marknads plats med åtgärder i Sverige, Finland, Norge och Danmark. Via dess 1 500-handlare erbjuder CDON en mängd olika sortiment som innehåller fler än 8 000 000 produkter. I 2020 hade CDON över 120 000 000-besökare och 2 000 000 aktiva kunder. Du kan lära dig mer om CDON användning av Azure Kognitiv sökning i [den här artikeln](https://pulse.microsoft.com/transform/na/fa1-how-cdon-has-been-using-technology-to-become-the-leading-marketplace-in-the-nordics/).
   :::column-end:::
:::row-end:::

För att köra de här testerna använde vi en ögonblicks bild av CDONs produktions Sök index och tusentals unika frågor från [webbplatsen](https://cdon.com).

### <a name="scenario-details"></a>Scenario information

- **Antal dokument**: 6 000 000 
- **Index storlek**: 20 GB
- **Index schema**: ett brett index med 250 fält totalt, 25 sökbara fält och 200 fasettiska/filter bara fält
- **Frågetyper**: fullständiga texts öknings frågor, inklusive FACET, filter, ordning och bedömnings profiler

### <a name="s1-performance"></a>S1-prestanda

#### <a name="queries-per-second"></a>Frågor per sekund

I diagrammet nedan visas den högsta frågan som en tjänst kan hantera under en längre tids period med avseende på frågor per sekund (frågor per sekund).

![Högsta underhålls bara frågor per sekund eCommerce S1](./media/performance-benchmarks/s1-ecom-qps.png)

#### <a name="query-latency"></a>Fråge svars tid

Svars tiden varierar beroende på belastningen på tjänsten och tjänsterna under högre belastning kommer att ha en högre genomsnittlig svars tid för frågor. Tabellen nedan visar de 25, femtio, 75th, nittionde, 95 och 99 percentilerna för frågans svars tid för tre olika användnings nivåer.

| Procent andel av Max frågor per sekund  | Genomsnittlig svars tid | 25 % | 75 % | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- | 
| 20%  | 104 MS  | 35 MS  | 115 MS   | 177 MS | 257 MS | 738 MS |
| 50 %  | 140 MS  | 47 MS  | 144 MS   | 241 MS | 400 MS | 1175 MS |
| 80 %  | 239 MS  | 77 MS  | 248 MS   | 466 MS | 763 MS | 1752 MS | 


### <a name="s2-performance"></a>S2-prestanda

#### <a name="queries-per-second"></a>Frågor per sekund

I diagrammet nedan visas den högsta frågan som en tjänst kan hantera under en längre tids period med avseende på frågor per sekund (frågor per sekund).

![Högsta underhålls bara frågor per sekund-eCommerce S2](./media/performance-benchmarks/s2-ecom-qps.png)

#### <a name="query-latency"></a>Fråge svars tid

Svars tiden varierar beroende på belastningen på tjänsten och tjänsterna under högre belastning kommer att ha en högre genomsnittlig svars tid för frågor. Tabellen nedan visar de 25, femtio, 75th, nittionde, 95 och 99 percentilerna för frågans svars tid för tre olika användnings nivåer.

| Procent andel av Max frågor per sekund  | Genomsnittlig svars tid | 25 % | 75 % | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- | 
| 20%  | 56 MS | 21 MS | 68 MS  | 106 MS  | 132 MS | 210 MS | 
| 50 %  | 71 MS  | 26 MS  | 83 MS   | 132 MS | 177 MS | 329 MS |
| 80 %  | 140 MS  | 47 MS  | 153 MS   | 293 MS | 452 MS | 924 MS | 

### <a name="s3-performance"></a>S3-prestanda

#### <a name="queries-per-second"></a>Frågor per sekund

I diagrammet nedan visas den högsta frågan som en tjänst kan hantera under en längre tids period med avseende på frågor per sekund (frågor per sekund).

![Högsta underhålls bara frågor per sekund eCommerce S3](./media/performance-benchmarks/s3-ecom-qps.png)

I det här fallet ser vi att om du lägger till en andra partition ökar den maximala frågor per sekund avsevärt, men om du lägger till en tredje partition får du minskat marginal returer. Den mindre förbättringen är förmodligen att alla data redan hämtas till det aktiva S3's-minnet med bara två partitioner.

#### <a name="query-latency"></a>Fråge svars tid

Svars tiden varierar beroende på belastningen på tjänsten och tjänsterna under högre belastning kommer att ha en högre genomsnittlig svars tid för frågor. Tabellen nedan visar de 25, femtio, 75th, nittionde, 95 och 99 percentilerna för frågans svars tid för tre olika användnings nivåer.

| Procent andel av Max frågor per sekund  | Genomsnittlig svars tid | 25 % | 75 % | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20%  | 50 MS  | 20 MS  | 64 MS   | 83 MS | 98 MS | 160 MS |
| 50 %  | 62 MS  | 24 MS  | 80 MS   | 107 ms | 130 MS | 253 MS |
| 80 %  | 115 MS  | 38 MS  | 121 MS   | 218 MS | 352 MS | 828 MS |

## <a name="benchmark-2-document-search"></a>Benchmark 2: dokuments ökning

### <a name="scenario-details"></a>Scenario information

- **Antal dokument**: 7 500 000
- **Index storlek**: 22 GB
- **Index schema**: 23 fält; 8 sökbar, 10 kan filtreras/aspektbar
- **Frågetyper**: nyckelords sökning med ansikte och träff markering

### <a name="s1-performance"></a>S1-prestanda

#### <a name="queries-per-second"></a>Frågor per sekund

I diagrammet nedan visas den högsta frågan som en tjänst kan hantera under en längre tids period med avseende på frågor per sekund (frågor per sekund).

![Högsta underhålls bara frågor per sekund doc-sökning S1](./media/performance-benchmarks/s1-docsearch-qps.png)

#### <a name="query-latency"></a>Fråge svars tid

Svars tiden varierar beroende på belastningen på tjänsten och tjänsterna under högre belastning kommer att ha en högre genomsnittlig svars tid för frågor. Tabellen nedan visar de 25, femtio, 75th, nittionde, 95 och 99 percentilerna för frågans svars tid för tre olika användnings nivåer.

| Procent andel av Max frågor per sekund  | Genomsnittlig svars tid | 25 % | 75 % | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20%  | 67 MS  | 44 MS  | 77 MS   | 103 MS | 126 MS | 216 MS |
| 50 %  | 93 MS  | 59 MS  | 110 MS   | 150 MS | 184 MS | 304 MS |
| 80 %  | 150 MS  | 96 MS  | 184 MS   | 248 MS | 297 MS | 424 MS |

### <a name="s2-performance"></a>S2-prestanda

#### <a name="queries-per-second"></a>Frågor per sekund

I diagrammet nedan visas den högsta frågan som en tjänst kan hantera under en längre tids period med avseende på frågor per sekund (frågor per sekund).

![Högsta underhålls bara frågor per sekund doc-sökning S2](./media/performance-benchmarks/s2-docsearch-qps.png)

#### <a name="query-latency"></a>Fråge svars tid

Svars tiden varierar beroende på belastningen på tjänsten och tjänsterna under högre belastning kommer att ha en högre genomsnittlig svars tid för frågor. Tabellen nedan visar de 25, femtio, 75th, nittionde, 95 och 99 percentilerna för frågans svars tid för tre olika användnings nivåer.

| Procent andel av Max frågor per sekund  | Genomsnittlig svars tid | 25 % | 75 % | 90 % | 95 % | 99 %|
|---|---|---|---| --- | --- | --- |
| 20%  | 45 MS  | 31 MS  | 55 MS   | 73 MS | 84 MS | 109 MS |
| 50 %  | 63 MS  | 39 MS  | 81 MS   | 106 MS | 123 MS | 163 MS |
| 80 %  | 115 MS  | 73 MS  | 145 MS   | 191 MS | 224 MS | 291 MS |

## <a name="takeaways"></a>Lärdomar

Med hjälp av dessa riktmärken kan du få en uppfattning om Azures Kognitiv sökning erbjudanden. Du kan också se skillnaden mellan tjänster på olika nivåer.

Några viktiga metoder för att utföra dessa benchmarks:

* En S2 kan vanligt vis hantera minst fyra gånger till frågesträngen som S1
* En S2 har vanligt vis kortare latens än en S1-volym med jämförbara frågesträngar
* När du lägger till repliker kan frågor per sekund som en tjänst hanterar vanligt vis skalas linjärt (t. ex. om en replik kan hantera 10 frågor per sekund, kan vanligt vis hantera 50 frågor per sekund)
* Ju högre belastning på tjänsten, desto högre blir den genomsnittliga svars tiden

Du kan också se att prestandan kan variera drastiskt mellan scenarier. Om du inte får den prestanda du förväntar dig kan du ta en titt på [tipsen för bättre prestanda](search-performance-tips.md).

## <a name="next-steps"></a>Nästa steg

Nu när du har sett prestanda mätningarna kan du lära dig mer om hur du analyserar Kognitiv sökning prestanda och viktiga faktorer som påverkar prestanda.

> [!div class="nextstepaction"]
> [Analysera prestanda](search-performance-analysis.md) 
>  [Tips för bättre prestanda](search-performance-tips.md)