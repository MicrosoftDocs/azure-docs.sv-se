---
title: Välj en pris nivå
titleSuffix: Azure Cognitive Search
description: 'Azure Kognitiv sökning kan tillhandahållas i dessa nivåer: kostnads fri, Basic och standard, och standard är tillgängligt i olika datorkonfigurationer och kapacitets nivåer.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/15/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 062bd41b0803cbb08f74fbcbcebb89bbddeb0d45
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97559811"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Välj en pris nivå för Azure Kognitiv sökning

När du [skapar en Sök tjänst](search-create-service-portal.md)väljer du en pris nivå som har åtgärd ATS för tjänstens livs längd. Nivån som du väljer avgör:

+ Antal index och andra objekt som du kan skapa (max gränser)
+ Storlek och hastighet för partitioner (fysisk lagring)
+ Fakturerbart pris, en fast månads kostnad, men även en stegvis kostnad om du lägger till partitioner eller repliker

Dessutom finns det några [Premium funktioner](#premium-features) med nivå krav.

## <a name="tier-descriptions"></a>Nivå beskrivningar

Nivåerna är **kostnads fria**, **Basic**, **standard** och **Storage-optimerade**. Standard-och lagrings optimering är tillgänglig med flera konfigurationer och kapaciteter.

Följande skärm bild från Azure Portal visar tillgängliga nivåer, minus priser (som du hittar i portalen och på [prissättnings sidan](https://azure.microsoft.com/pricing/details/search/). 

![Pris nivåer för Azure Kognitiv sökning](media/search-sku-tier/tiers.png "Pris nivåer för Azure Kognitiv sökning")

**Kostnads fri** skapar en begränsad Sök tjänst för mindre projekt, som att köra självstudier och kod exempel. Internt delas repliker och partitioner mellan flera prenumeranter. Du kan inte skala en kostnads fri tjänst eller köra betydande arbets belastningar.

**Basic** och **standard** är de vanligaste fakturerbara nivåerna, med **standard standarden** . Med dedikerade resurser under kontrollen kan du distribuera större projekt, optimera prestanda och öka kapaciteten.

Vissa nivåer är optimerade för vissa typer av arbete. Till exempel är **standard 3 hög densitet (S3 HD)** ett *värd läge* för S3, där den underliggande maskin varan är optimerad för ett stort antal mindre index och är avsedd för flera organisationers scenarier. S3 HD har samma kostnad per enhet som S3, men maskin varan är optimerad för snabba fil läsningar på ett stort antal mindre index.

**Lagrings optimerings** nivåer ger större lagrings kapacitet till ett lägre pris per TB än standard nivåerna. Den främsta kompromissen är högre svars tid för frågor, som du bör validera för dina specifika program krav. Mer information om prestanda överväganden för den här nivån finns i [överväganden för prestanda och optimering](search-performance-optimization.md).

Du hittar mer information om olika nivåer på [sidan prissättning](https://azure.microsoft.com/pricing/details/search/)i avsnittet [begränsningar för tjänsten i Azure kognitiv sökning](search-limits-quotas-capacity.md) -artikeln och på Portal sidan när du skapar en tjänst.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Funktions tillgänglighet per nivå

De flesta funktioner är tillgängliga på alla nivåer, inklusive den kostnads fria nivån. I några fall påverkar den nivå du väljer möjligheten att implementera en funktion. I följande tabell beskrivs de funktions begränsningar som är relaterade till tjänst nivån.

| Funktion | Begränsningar |
|---------|-------------|
| [indexerare](search-indexer-overview.md) | Indexerare är inte tillgängliga på S3 HD.  |
| [AI-berikning](search-security-manage-encryption-keys.md) | Körs på den kostnads fria nivån men rekommenderas inte. |
| [Kundhanterade krypterings nycklar](search-security-manage-encryption-keys.md) | Inte tillgängligt på den kostnads fria nivån. |
| [Åtkomst till IP-brandvägg](service-configure-firewall.md) | Inte tillgängligt på den kostnads fria nivån. |
| [Privat slut punkt (integrering med Azures privata länk)](service-create-private-endpoint.md) | För inkommande anslutningar till en search-tjänst är inte tillgänglig på den kostnads fria nivån. För utgående anslutningar från indexerare till andra Azure-resurser är de inte tillgängliga på kostnads fria eller S3 HD. För indexerare som använder färdighetsuppsättningar, inte tillgängligt på kostnads fri, grundläggande, S1 eller S3 HD.|

Resurs krävande funktioner kanske inte fungerar bra om du inte ger den tillräckligt med kapacitet. [AI-anrikning](cognitive-search-concept-intro.md) har till exempel långvariga färdigheter som tar slut på en kostnads fri tjänst, om inte data uppsättningen är liten.

## <a name="billable-events"></a>Fakturerbara händelser

En lösning som bygger på Azure Kognitiv sökning kan ådra sig kostnaderna på följande sätt:

+ [Kostnaden för själva tjänsten](#service-costs) , som körs dygnet runt, med lägsta konfiguration (en partition och replik), enligt bas priset. Du kan tänka på detta som den fasta kostnaden för att köra tjänsten.

+ Lägga till kapacitet (repliker eller partitioner), där kostnader ökar vid ökningar av fakturerings takten

+ Bandbredds avgifter (utgående data överföring)

+ Tilläggs tjänster som krävs för vissa funktioner eller funktioner:

  + AI-anrikning (kräver [Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/))
  + kunskaps lager (kräver [Azure Storage](https://azure.microsoft.com/pricing/details/storage/))
  + stegvis anrikning (kräver [Azure Storage](https://azure.microsoft.com/pricing/details/storage/), gäller AI-anrikning)
  + Kundhanterade nycklar och Double Encryption (kräver [Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/))
  + privata slut punkter för en modell utan Internet åtkomst (kräver [Azures privata länk](https://azure.microsoft.com/pricing/details/private-link/))

### <a name="service-costs"></a>Tjänste kostnader

Till skillnad från virtuella datorer eller andra resurser som kan vara "pausade" för att undvika avgifter är en Azure Kognitiv sökning-tjänst alltid tillgänglig på maskin vara som är dedikerad för exklusiv användning. Därför är att skapa en tjänst en fakturerbar händelse som startar när du skapar tjänsten och slutar när du tar bort tjänsten. 

Den lägsta avgiften är den första Sök enheten (en partition med en enda replik x) till fakturerings takten. Detta minimum är fast för tjänstens livs längd eftersom tjänsten inte kan köras på något som är mindre än den här konfigurationen. Utöver det lägsta antalet kan du lägga till repliker och partitioner oberoende av varandra. Stegvisa ökningar i kapaciteten via repliker och partitioner ökar din faktura utifrån följande formel: [(repliker x partitioner x hastighet)](#search-units), där den hastighet du debiteras beror på vilken pris nivå du väljer.

När du uppskattar kostnaden för en Sök lösning bör du tänka på att priserna och kapaciteten inte är linjära. (Dubbla kapaciteten är mer än dubbelt så mycket som kostnaden.) Ett exempel på hur formeln fungerar finns i [så här allokerar du repliker och partitioner](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

### <a name="bandwidth-charges"></a>Bandbredds avgifter

Användningen av [indexerare](search-indexer-overview.md) kan påverka faktureringen om Azure-datakällan är i en annan region än Azure kognitiv sökning. I det här scenariot är detta en kostnad för att flytta utgående data från Azure Data Source till Azure Kognitiv sökning. 

Du kan ta bort avgifter för utgående data helt och hållet om du skapar Azure Kognitiv sökning-tjänsten i samma region som dina data. Här är lite information från [prissättnings sidan för bandbredd](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft debiterar inte några inkommande data till någon tjänst i Azure.
+ Det finns ingen utgående data avgift från Azure Kognitiv sökning. Om din Sök tjänst till exempel är i västra USA och en Azure-webbapp finns i USA, kan Microsoft inte debiteras för de nytto svars nytto laster som kommer från västra USA.
+ I lösningar med flera tjänster är det ingen avgift för data som passerar kabeln när alla tjänster är i samma region.

Avgifterna gäller för utgående data om tjänsterna är i olika regioner. Dessa avgifter ingår inte i din Azure Kognitiv sökning-faktura. De anges här eftersom om du använder data eller AI-berikade indexerare för att hämta data från olika regioner, ser du kostnader som återspeglas i din övergripande faktura.

### <a name="ai-enrichment-with-cognitive-services"></a>AI-anrikning med Cognitive Services

För [AI-anrikning](cognitive-search-concept-intro.md)bör du planera att [koppla en fakturerbar Azure Cognitive Services-resurs](cognitive-search-attach-cognitive-services.md), i samma region som Azure kognitiv sökning, på pris nivån S0 för att betala per användning. Det finns ingen fast kostnad för att bifoga Cognitive Services. Du betalar bara för den bearbetning du behöver.

| Åtgärd | Fakturerings påverkan |
|-----------|----------------|
| Dokument sprickor, text extrahering | Kostnadsfri |
| Dokument sprickor, avbildnings extrahering | Faktureras enligt antalet bilder som har extraherats från dina dokument. I en [indexerare-konfiguration](/rest/api/searchservice/create-indexer#indexer-parameters)är **imageAction** den parameter som utlöser avbildnings extrahering. Om **imageAction** är inställt på "ingen" (standard) debiteras du inte för avbildnings extrahering. Hastigheten för avbildnings extrahering dokumenteras på sidan med [pris information](https://azure.microsoft.com/pricing/details/search/) för Azure kognitiv sökning.|
| [Inbyggda kognitiva kompetenser](cognitive-search-predefined-skills.md) | Faktureras enligt samma takt som om du har utfört uppgiften genom att använda Cognitive Services direkt. |
| Anpassade färdigheter | En anpassad färdighet är funktioner som du anger. Kostnaden för att använda en anpassad färdighet beror helt på om anpassad kod anropar andra avgiftsbelagda tjänster. |

Med funktionen för [förhands granskning (för hands version)](cognitive-search-incremental-indexing-conceptual.md) kan du tillhandahålla ett cacheminne som gör det möjligt för indexeraren att vara mer effektivt när du bara kör de kognitiva färdigheter som behövs om du ändrar din färdigheter i framtiden, vilket sparar tid och pengar.

<a name="search-units"></a>

## <a name="billing-formula-r-x-p--su"></a>Fakturerings formel (R x P = SU)

Det viktigaste fakturerings konceptet att förstå för Azure Kognitiv sökning-åtgärder är *Sök enheten* (SU). Eftersom Azure Kognitiv sökning är beroende av både repliker och partitioner för indexering och frågor, är det inte bra att fakturera med bara en eller ett annat. Faktureringen baseras i stället på en sammansatt av båda.

SU är produkten av de *repliker* och *partitioner* som används av en tjänst: **(R x P = SU)**.

Varje tjänst börjar med en SU (en replik multiplicerad med en partition) som minimum. Det maximala antalet för en tjänst är 36 SUs. Det här max värdet kan nås på flera sätt: 6 partitioner x 6 repliker eller 3 partitioner x 12 repliker, till exempel. Det är vanligt att använda mindre än den totala kapaciteten (t. ex. en 3-replikering, en 3-partitions tjänst som faktureras som 9 SUs). Se diagrammet [partition och replik kombinations](search-capacity-planning.md#chart) diagram för giltiga kombinationer.

Fakturerings takten är per timme per SU. Varje nivå har en progressivt högre hastighet. Högre nivåer levereras med större och speedier partitioner, och detta bidrar till en högre pris per timme för den nivån. Du kan visa priserna för varje nivå på [pris informations](https://azure.microsoft.com/pricing/details/search/) sidan.

De flesta kunder tar bara en del av den totala kapaciteten online, där resten av reserven behålls. För fakturering bestämmer antalet partitioner och repliker som du ansluter online, vilket avgör vad du betalar per timme.

## <a name="next-steps"></a>Nästa steg

Kostnads hantering är en integrerad del av kapacitets planeringen. I nästa steg ska du fortsätta med följande artikel för vägledning om hur du uppskattar kapacitet och hanterar kostnader.

> [!div class="nextstepaction"]
> [Så här hanterar du kostnader och beräknings kapacitet i Azure Kognitiv sökning](search-sku-manage-costs.md)