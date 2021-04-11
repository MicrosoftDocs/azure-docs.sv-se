---
title: Prestandatips
titleSuffix: Azure Cognitive Search
description: Lär dig mer om tips och bästa praxis för att maximera prestanda på en Sök tjänst.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 28325a1bbda1b2d4a4bb060ae3e79057275ee42a
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106582347"
---
# <a name="tips-for-better-performance-in-azure-cognitive-search"></a>Tips för bättre prestanda i Azure Kognitiv sökning

Den här artikeln är en samling tips och bästa praxis som ofta rekommenderas för att öka prestandan. Att veta vilka faktorer som är mest sannolika för att påverka Sök prestanda kan hjälpa dig att undvika ineffektivitet och få ut mesta möjliga av din Sök tjänst. Några viktiga faktorer är:

+ Index sammansättning (schema och storlek)
+ Frågetyper
+ Tjänst kapacitet (nivå, antal repliker och partitioner)

## <a name="index-size-and-schema"></a>Index storlek och schema

Frågor körs snabbare på mindre index. Detta är delvis en funktion som har färre fält att genomsöka, men det beror också på hur systemet cachelagrar innehåll för framtida frågor. Efter den första frågan finns allt innehåll i minnet där det genomsöks mer effektivt. Eftersom index storleken tenderar att växa över tid, är det en bra idé att regelbundet gå igenom index sammansättning, både schema och dokument, för att söka efter innehålls minsknings möjligheter. Men om indexet är rätt storlek, är det bara andra kalibrering som du kan göra för att öka kapaciteten: du kan antingen [lägga till repliker](search-capacity-planning.md#adjust-capacity) eller uppgradera tjänst nivån. Avsnittet ["Tips: uppgradera till en standard S2-nivå"]] (#tip-Upgrade-to-a-standard-S2-Tier) visar hur du utvärderar degraderingen jämfört med utskalning.

Schema komplexitet kan också påverka indexering och fråga prestanda negativt. För hög fälts tilldelning skapas i begränsningar och bearbetnings krav. Det tar längre tid att indexera och fråga med [komplexa typer](search-howto-complex-data-types.md) . I följande avsnitt utforskar du varje villkor.

### <a name="tip-be-selective-in-field-attribution"></a>Tips: var selektiv i fält behörighet

Ett vanligt fel som administratörer och utvecklare gör när de skapar ett sökindex väljer alla tillgängliga egenskaper för fälten, i stället för att bara välja de egenskaper som behövs. Om ett fält till exempel inte behöver vara full text sökbar, hoppar du över fältet när du anger sökbart attribut.

:::image type="content" source="media/search-performance/perf-selective-field-attributes.png" alt-text="Selektiv behörighet" border="true":::

Stöd för filter, ansikte och sortering kan vara fyra lagrings krav. Om du lägger till förslag kan lagrings kraven gå upp ännu mer. En illustration av hur attributen påverkas av lagrings utrymme finns i [attribut och index storlek](search-what-is-an-index.md#index-size).

Sammanfattnings vis inkluderar aspekterna på över-behörighet:

+ Försämring av indexerings prestanda på grund av det extra arbete som krävs för att bearbeta innehållet i fältet och lagra det sedan i det inverterade indexet (ange det sökbara attributet i fält som innehåller sökbart innehåll).

+ Skapar en större yta som varje fråga måste avse. Alla fält som är markerade som sökbara genomsöks i en fullständig texts ökning.

+ Ökar drifts kostnaderna på grund av extra lagring. Filtrering och sortering kräver ytterligare utrymme för att lagra ursprungliga (icke-analyserade) strängar. Undvik att ställa in Filterable eller sorterbart på fält som inte behöver det.

+ I många fall begränsar sig över tilldelningen funktionerna i fältet. Om ett fält till exempel är fasettt, kan filtreras och sökbar, kan du bara lagra 16 KB text i ett fält, medan ett sökbart fält kan innehålla upp till 16 MB text.

> [!NOTE]
> Endast onödig behörighet bör undvikas. Filter och ansikte är ofta viktiga för Sök upplevelsen, och i de fall där filter används måste du ofta sortera så att du kan ordna resultaten (filter efter varandra returneras i en oordnad uppsättning).

### <a name="tip-consider-alternatives-to-complex-types"></a>Tips: Överväg alternativ till komplexa typer

Komplexa data typer är användbara när data har en komplex kapslad struktur, t. ex. överordnad-underordnad-element som finns i JSON-dokument. Nack delen med komplexa typer är de extra lagrings kraven och ytterligare resurser som krävs för att indexera innehållet, i jämförelse med icke-komplexa data typer. 

I vissa fall kan du undvika dessa kompromisser genom att mappa en komplex data struktur till en enklare fälttyp, till exempel en samling. Du kan också välja att förenkla en fältkod till enskilda fält på rotnivå.

:::image type="content" source="media/search-performance/perf-flattened-field-hierarchy.png" alt-text="sammanslagen fält struktur" border="true":::

## <a name="types-of-queries"></a>Typer av frågor

De typer av frågor som du skickar är en av de viktigaste faktorerna för prestanda, och fråge optimering kan drastiskt förbättra prestandan. Tänk på följande när du utformar frågor:

+ **Antal sökbara fält.** För alla ytterligare sökbara fält krävs ytterligare arbete från Sök tjänsten. Du kan begränsa vilka fält som genomsöks vid en fråga med hjälp av parametern "searchFields". Det är bäst att bara ange de fält som du bryr dig om att förbättra prestandan.

+ **Mängden data som returneras.** Att hämta mycket innehåll kan göra frågor långsammare. När du strukturerar en fråga returnerar du bara de fält som du behöver för att återge resultat sidan och hämtar sedan återstående fält med hjälp av [Sök-API: et](/rest/api/searchservice/lookup-document) när en användare väljer en matchning.

+ **Användning av partiella terms ökningar.** [Partiella term sökningar](search-query-partial-matching.md), till exempel prefixs ökning, fuzzy-sökning och sökning efter reguljära uttryck, är mer kostnads effektivt än vanliga nyckelords sökningar, eftersom de kräver fullständiga index genomsökningar för att skapa resultat.

+ **Antal facets.** Det krävs agg regeringar för varje fråga för att lägga till facets i frågor. I allmänhet ska du bara lägga till de ansikte som du planerar att återge i din app.

+ **Begränsa fält för hög kardinalitet.**  Ett *högt kardinalitet-fält* refererar till ett aspektable-eller filter bara fält som har ett stort antal unika värden, och som resultat använder betydande resurser när resultat beräknas. Om du till exempel ställer in ett produkt-ID eller beskrivnings fält som fasettable och Filterable räknas det som en hög kardinalitet eftersom de flesta värden från dokument till dokument är unika.

### <a name="tip-use-search-functions-instead-overloading-filter-criteria"></a>Tips: Använd Sök funktioner i stället överbelasta filter villkor

När en fråga använder ökande [komplexa filter villkor](search-query-odata-filter.md#filter-size-limitations)försämras prestanda för Sök frågan. Tänk på följande exempel som visar användningen av filter för att trimma resultat baserat på en användar identitet:

```json
$filter= userid eq 123 or userid eq 234 or userid eq 345 or userid eq 456 or userid eq 567
```

I det här fallet används filter uttryck för att kontrol lera om ett enskilt fält i varje dokument motsvarar ett av många möjliga värden för en användar identitet. Du hittar förmodligen det här mönstret i program som implementerar [säkerhetstrimning](search-security-trimming-for-azure-search.md) (kontrollerar ett fält som innehåller ett eller flera huvud-ID: n mot en lista över huvud namns-ID: n som representerar den användare som utfärdar frågan).

Ett mer effektivt sätt att köra filter som innehåller ett stort antal värden är att använda [ `search.in` funktionen](search-query-odata-search-in-function.md), som du ser i det här exemplet:

```json
search.in(userid, '123,234,345,456,567', ',')
```

### <a name="tip-add-partitions-for-slow-individual-queries"></a>Tips: Lägg till partitioner för långsamma enskilda frågor

När frågans prestanda går långsammare i allmänhet löser det ofta problemet genom att lägga till fler repliker. Men vad händer om problemet är en enskild fråga som tar för lång tid att slutföra? I det här scenariot hjälper det inte att lägga till repliker, men ytterligare partitioner kan vara kvar. En partition delar upp data mellan extra data behandlings resurser. Två partitioner dela data på hälften, en tredje partition delar den i tredje, och så vidare. 

En positiv sid effekt för att lägga till partitioner är att långsammare frågor ibland presterar snabbare på grund av parallell dator användning. Vi har noterat parallellisering för frågor med låg selektivitet, till exempel frågor som stämmer överens med många dokument, eller till att det finns ett stort antal dokument. Eftersom det krävs en betydande beräkning för att räkna med relevanta i dokumenten, eller för att räkna antalet dokument, så kan du lägga till extra partitioner snabbare.  

Använd [Azure Portal](search-create-service-portal.md), [POWERSHELL](search-manage-powershell.md), [Azure CLI](search-manage-azure-cli.md)eller ett hanterings-SDK för att lägga till partitioner.

## <a name="service-capacity"></a>Tjänst kapacitet

En tjänst överbelastas när frågor tar för lång tid eller när tjänsten börjar släppa förfrågningar. Om detta inträffar kan du åtgärda problemet genom att uppgradera tjänsten eller genom att lägga till kapacitet.

Nivån på Sök tjänsten och antalet repliker/partitioner har också en stor inverkan på prestandan. Varje högre nivå ger snabbare processorer och mer minne, som båda har positiv påverkan på prestanda.

### <a name="tip-upgrade-to-a-standard-s2-tier"></a>Tips: uppgradera till en standard S2-nivå

Standard S1 search-nivån är ofta där kunder startar. Ett vanligt mönster för S1-tjänster är att indexen växer över tid, vilket kräver fler partitioner. Fler partitioner leder till långsammare svars tider, så att fler repliker läggs till för att hantera frågans belastning. Som du kan föreställa dig kan kostnaden för att köra en S1-tjänst nu gå vidare till nivåer utöver den ursprungliga konfigurationen.

På det här juncture är en viktig fråga att fråga om det skulle vara bra att flytta till en högre nivå, i stället för att progressivt öka antalet partitioner eller repliker av den aktuella tjänsten. 

Tänk på följande topologi som ett exempel på en tjänst som har tagit på ökande kapacitets nivåer:

+ Standard S1-nivå
+ Index storlek: 190 GB
+ Antal partitioner: 8 (på S1 är partitionens storlek 25 GB per partition)
+ Antal repliker: 2
+ Totalt antal Sök enheter: 16 (8 partitioner x 2 repliker)
+ Hypotetiskt detalj handels pris: ~ $4 000 USD/månad (Antag $250 USD x 16 Sök enheter)

Anta att tjänst administratören fortfarande ser högre latens taxa och funderar på att lägga till en annan replik. Detta skulle ändra antalet repliker från 2 till 3 och till och med ändra antalet Sök enheter till 24 och ett resulterande pris på $6 000 USD/månad.

Men om administratören valde att flytta till en standard S2-nivå skulle topologin se ut så här:

+ Standard S2-nivå
+ Index storlek: 190 GB
+ Antal partitioner: 2 (på S2 är partitionens storlek 100 GB per partition)
+ Antal repliker: 2
+ Totalt antal Sök enheter: 4 (2 partitioner x 2 repliker)
+ Hypotetiskt detalj handels pris: ~ $4 000 USD/månad ($1000 USD x 4 Sök enheter)

Eftersom det här hypotetiska scenariot illustreras, kan du ha konfigurationer på lägre nivåer som resulterar i liknande kostnader som om du har valt en högre nivå på den första platsen. Högre nivåer levereras dock med Premium Storage, vilket gör indexeringen snabbare. Högre nivåer har också mycket mer beräknings kraft, samt extra minne. För samma kostnader kan du ha mer kraftfull infrastruktur som säkerhetskopierar samma index.

En viktig fördel med extra minne är att mer av indexet kan cachelagras, vilket resulterar i kortare Sök fördröjning och ett större antal frågor per sekund. Med den här extra kraften kanske administratören inte behöver öka antalet repliker och kan eventuellt betala mindre än genom att Vista S1-tjänsten.

## <a name="next-steps"></a>Nästa steg

Läs de här ytterligare artiklarna om tjänstens prestanda.

+ [Analysera prestanda](search-performance-analysis.md)
+ [Välj en tjänst nivå](search-sku-tier.md)
+ [Lägg till kapacitet (repliker och partitioner)](search-capacity-planning.md#adjust-capacity)
