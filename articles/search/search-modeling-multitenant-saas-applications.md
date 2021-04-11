---
title: Flera innehavare och innehålls isolering
titleSuffix: Azure Cognitive Search
description: Lär dig om vanliga design mönster för SaaS-program med flera innehavare när du använder Azure Kognitiv sökning.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 7833dcf8fbe2b6460346310a4d094c7bb5d606c4
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581582"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Design mönster för SaaS-program med flera innehavare och Azure Kognitiv sökning

Ett program för flera innehavare är ett program som tillhandahåller samma tjänster och funktioner för alla klienter som inte kan se eller dela data för någon annan klient organisation. I det här dokumentet beskrivs strategier för klient isolering för program med flera klienter som skapats med Azure Kognitiv sökning.

## <a name="azure-cognitive-search-concepts"></a>Azure Kognitiv sökning-koncept

Som en Sök-som-tjänst-lösning gör [Azure kognitiv sökning](search-what-is-azure-search.md) att utvecklare kan lägga till omfattande Sök upplevelser i program utan att behöva hantera en infrastruktur eller bli expert på informations hämtning. Data överförs till tjänsten och lagras sedan i molnet. Med hjälp av enkla begär anden till Azure Kognitiv sökning API: et kan data sedan ändras och genomsökas. 

### <a name="search-services-indexes-fields-and-documents"></a>Sök tjänster, index, fält och dokument

Innan du diskuterar design mönster är det viktigt att du förstår några grundläggande begrepp.

När du använder Azure Kognitiv sökning en prenumeration på en *Sök tjänst*. När data överförs till Azure Kognitiv sökning lagras de i ett *index* i Sök tjänsten. Det kan finnas ett antal index i en enskild tjänst. Om du vill använda de välkända koncepten för databaser kan Sök tjänsten likened till en databas medan indexen i en tjänst kan vara likened till tabeller i en databas.

Varje index i en Sök tjänst har sitt eget schema, vilket definieras av ett antal anpassningsbara *fält*. Data läggs till i ett Azure Kognitiv sökning-index i form av enskilda *dokument*. Varje dokument måste överföras till ett visst index och måste passa det indexets schema. När du söker efter data med hjälp av Azure Kognitiv sökning utfärdas full text Sök frågorna mot ett visst index.  Om du vill jämföra dessa begrepp med databaserna i en databas kan du likened kolumner i en tabell och dokument kan likened till rader.

### <a name="scalability"></a>Skalbarhet

Alla Azure Kognitiv sökning-tjänster på standard [pris nivån](https://azure.microsoft.com/pricing/details/search/) kan skalas i två dimensioner: lagring och tillgänglighet.

+ Du kan lägga till *partitioner* för att öka lagringen för en Sök tjänst.
+ *Repliker* kan läggas till i en tjänst för att öka data flödet för begär Anden som en Sök tjänst kan hantera.

Genom att lägga till och ta bort partitioner och repliker på kan du öka kapaciteten för Sök tjänsten med den mängd data och trafik som programmet kräver. För att en Sök tjänst ska kunna uppnå ett [service avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/)för läsning kräver det två repliker. För att en tjänst ska kunna uppnå ett service [avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/)med Läs-och skriv åtgärder krävs tre repliker.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Tjänst-och index gränser i Azure Kognitiv sökning

Det finns några olika [pris nivåer](https://azure.microsoft.com/pricing/details/search/) i Azure kognitiv sökning, var och en av nivåerna har olika [gränser och kvoter](search-limits-quotas-capacity.md). Några av dessa begränsningar finns på tjänst nivå, vissa finns på index-nivå och några finns på partition-nivå.

|  | Grundläggande | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| **Maximalt antal repliker per tjänst** |3 |12 |12 |12 |12 |
| **Maximalt antal partitioner per tjänst** |1 |12 |12 |12 |3 |
| **Maximalt antal Sök enheter (repliker * partitioner) per tjänst** |3 |36 |36 |36 |36 (max 3 partitioner) |
| **Maximalt lagrings utrymme per tjänst** |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| **Maximalt lagrings utrymme per partition** |2 GB |25 GB |100 GB |200 GB |200 GB |
| **Maximalt antal index per tjänst** |5 |50 |200 |200 |3000 (max 1000-index/partition) |

#### <a name="s3-high-density"></a>S3 med hög densitet

I Azure Kognitiv söknings S3-pris nivå finns ett alternativ för läget hög densitet (HD) som är specifikt för flera klient scenarier. I många fall är det nödvändigt att stödja ett stort antal mindre klienter under en enda tjänst för att uppnå fördelarna med enkelhet och kostnads effektivitet.

S3 HD gör att många små index kan packas upp under hanteringen av en enskild Sök tjänst genom att handel kan skala ut index med partitioner för att kunna hantera fler index i en enskild tjänst.

En S3-tjänst är utformad för att vara värd för ett fast antal index (högst 200) och tillåta att varje index skalas i storlek vågrätt när nya partitioner läggs till i tjänsten. Om du lägger till partitioner i S3 HD-tjänster ökar det maximala antalet index som tjänsten kan vara värd för. Den perfekta maximala storleken för ett enskilt S3HD-index är cirka 50-80 GB, även om det inte finns någon storleks gräns för varje index som systemet infört.

## <a name="considerations-for-multitenant-applications"></a>Att tänka på för program med flera klienter

Program för flera innehavare måste effektivt distribuera resurser mellan klienterna samtidigt som du behåller en viss sekretess nivå mellan olika klienter. Det finns några saker att tänka på när du utformar arkitekturen för ett sådant program:

+ *Klient isolering:* Programutvecklare måste vidta lämpliga åtgärder för att säkerställa att inga klienter har obehörig eller oönskad åtkomst till data för andra klienter. Utöver data sekretessens perspektiv kräver klient isolerings strategier effektiv hantering av delade resurser och skydd mot störningar i grannar.

+ *Resurs kostnad för moln:* Precis som med alla andra program måste program varu lösningar vara kostnads konkurrens kraftiga som en komponent i ett program för flera innehavare.

+ *Enkel drift:* När du utvecklar en arkitektur för flera innehavare är påverkan av programmets drift och komplexitet viktigt att tänka på. Azure Kognitiv sökning har ett [service avtal på 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ *Global storlek:* Program för flera klient organisationer kan behöva betjäna klienter som distribueras över hela världen.

+ *Skalbarhet:* Programutvecklare måste fundera över hur de stämmer överens med en tillräckligt låg nivå av program komplexitet och att utforma programmet för skalning med antalet klienter och storleken på klient organisationens data och arbets belastning.

Azure Kognitiv sökning erbjuder några gränser som kan användas för att isolera innehavares data och arbets belastning.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Modellering av flera organisationer med Azure Kognitiv sökning

I ett scenario med flera innehavare förbrukar programutvecklaren en eller flera Sök tjänster och delar upp sina klienter bland tjänster, index eller både och. Azure Kognitiv sökning har några vanliga mönster vid modellering av ett scenario med flera innehavare:

+ *Ett index per klient:* Varje klient har sitt eget index i en Sök tjänst som delas med andra klienter.

+ *En tjänst per klient:* Varje klient har sin egen dedikerade Azure Kognitiv sökning-tjänst som erbjuder den högsta nivån av data och separation av arbets belastning.

+ *Blandning av båda:* Större, mer-aktiva klienter tilldelas dedikerade tjänster, medan mindre klienter tilldelas enskilda index inom delade tjänster.

## <a name="model-1-one-index-per-tenant"></a>Modell 1: ett index per klient

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png" alt-text="En portrayal av modellen index-per-klient" border="false":::

I en modell för index per klient använder flera klienter en enda Azure Kognitiv sökning-tjänst där varje klient har sitt eget index.

Klienter uppnår data isolering eftersom alla Sök förfrågningar och dokument åtgärder utfärdas på en index nivå i Azure Kognitiv sökning. I program lagret behöver du veta mer om hur du dirigerar de olika klienternas trafik till rätt index samtidigt som du även hanterar resurser på tjänst nivå över alla klienter.

Ett nyckelattribut för modellen index-per-klient är möjligheten för programutvecklaren att överlåta kapaciteten hos en Sök tjänst bland programmets klienter. Om klienterna har en ojämn fördelning av arbets belastningen, kan den optimala kombinationen av klienter distribueras över en Sök tjänsts index för att hantera ett antal mycket aktiva, resurs krävande klienter samtidigt som en lång ände av mindre aktiva klienter betjänas samtidigt. Kompromissen är oförmåga för modellen att hantera situationer där varje klient är samtidigt hög aktiv.

Modellen index-per-klient utgör grunden för en variabel kostnads modell, där en hel Azure Kognitiv sökning-tjänst har köpts upp och sedan fyllts med klient organisationer. Detta gör det möjligt för oanvänd kapacitet att utses för försök och kostnads fria konton.

För program med global storlek kanske modellen index per klient inte är den mest effektiva. Om ett programs klienter distribueras över hela världen kan en separat tjänst vara nödvändig för varje region som kan duplicera kostnader för var och en av dem.

Med Azure Kognitiv sökning kan du skala både enskilda index och det totala antalet index som ska växa. Om en lämplig pris nivå väljs, kan partitioner och repliker läggas till i hela Sök tjänsten när ett enskilt index i tjänsten växer för stort när det gäller lagring eller trafik.

Om det totala antalet index blir för stort för en enskild tjänst, måste en annan tjänst tillhandahållas för att hantera de nya klient organisationerna. Om index måste flyttas mellan Sök tjänsterna när nya tjänster läggs till, måste data från indexet kopieras manuellt från ett index till det andra eftersom Azure Kognitiv sökning inte tillåter att ett index flyttas.

## <a name="model-2-once-service-per-tenant"></a>Modell 2: när tjänsten per klient

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png" alt-text="En portrayal av modellen för tjänst per klient" border="false":::

I en arkitektur för tjänst per klient organisation har varje klient organisation sin egen Sök tjänst.

I den här modellen uppnår programmet den maximala isolerings nivån för dess klienter. Varje tjänst har dedikerad lagring och data flöde för hantering av Sök begär Anden samt separata API-nycklar.

För program där varje klient har ett stort utrymme eller om arbets belastningen har liten variation från klient organisation till klient organisation, är tjänst per klient modell ett effektivt val som resurser inte delas mellan olika klienters arbets belastningar.

En tjänst per klient modell erbjuder också fördelen med en förutsägbar, fast kostnads modell. Det finns ingen direkt investering i en hel Sök tjänst tills det finns en klient som kan fylla den, men kostnaden per klient är högre än en modell med index per klient.

Modellen för tjänst per klient är ett effektivt alternativ för program med global storlek. Med geografiskt distribuerade klienter är det enkelt att ha varje klients tjänst i lämplig region.

Utmaningarna med att skala det här mönstret uppstår när enskilda klienter utökar sin tjänst. Azure Kognitiv sökning stöder för närvarande inte uppgradering av pris nivån för en Sök tjänst, så alla data måste kopieras manuellt till en ny tjänst.

## <a name="model-3-hybrid"></a>Modell 3: hybrid

Ett annat mönster för modellering av flera innehavare är att blanda både index-per-klient-och tjänst per klient-strategier.

Genom att blanda de två mönstren kan ett programs största innehavare ha dedikerade tjänster medan den långa änden av mindre aktiva och mindre klienter kan uppta index i en delad tjänst. Den här modellen säkerställer att de största klient organisationerna har konsekvent höga prestanda från tjänsten samtidigt som den hjälper till att skydda de mindre klienterna från alla störningar i olika störningar.

Att implementera den här strategin är dock beroende av utseende för att förutsäga vilka klienter som kräver en dedikerad tjänst jämfört med ett index i en delad tjänst. Program komplexiteten ökar med behovet av att hantera båda de här flera innehavande modellerna.

## <a name="achieving-even-finer-granularity"></a>Uppnå ännu bättre precision

Design mönstren ovan för att modellera scenarier för flera innehavare i Azure Kognitiv sökning förutsätter ett enhetligt omfång där varje klient är en hel instans av ett program. Program kan dock ibland hantera många mindre omfång.

Om modeller för tjänst per klient och index per klient inte är tillräckligt små omfattningar, är det möjligt att modellera ett index för att uppnå en ännu bättre grad av granularitet.

Om ett enskilt index beter sig annorlunda för olika klient slut punkter kan ett fält läggas till i ett index som anger ett visst värde för varje möjlig klient. Varje gången en klient anropar Azure Kognitiv sökning för att fråga eller ändra ett index, anger koden från klient programmet lämpligt värde för fältet med hjälp av Azure Kognitiv söknings [filter](./query-odata-filter-orderby-syntax.md) funktion vid tidpunkten för frågan.

Den här metoden kan användas för att uppnå funktioner i separata användar konton, olika behörighets nivåer och till och med helt separata program.

> [!NOTE]
> Genom att använda den metod som beskrivs ovan för att konfigurera ett enda index för att betjäna flera klienter, påverkar Sök resultatnas relevans. Sök Resultat beräknas på ett omfång på index nivå, inte på en omfattning på klient nivå, så alla innehavares data införlivas i resultat underliggande statistik, till exempel term frekvens.
>

## <a name="next-steps"></a>Nästa steg

Azure Kognitiv sökning är ett övertygande val för många program. När du utvärderar de olika design mönstren för program med flera klienter bör du överväga de [olika pris nivåerna](https://azure.microsoft.com/pricing/details/search/) och respektive [tjänst begränsningar](search-limits-quotas-capacity.md) för att skräddarsy Azure-kognitiv sökning så att den passar program arbets belastningar och arkitekturer i alla storlekar.