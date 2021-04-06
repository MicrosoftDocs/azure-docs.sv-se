---
title: Välj en pris nivå
titleSuffix: Azure Cognitive Search
description: 'Läs mer om pris nivåerna (eller SKU: er) för Azure Kognitiv sökning. En Sök tjänst kan tillhandahållas på dessa nivåer: kostnads fri, Basic och standard. Standard är tillgängligt i olika webbplatskonfigurationer och kapacitets nivåer.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: df218c2a4c066343ef571c6f24554ecaa806b639
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99987886"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Välj en pris nivå för Azure Kognitiv sökning

En del av att [skapa en Sök tjänst](search-create-service-portal.md) innebär att välja en pris nivå (eller SKU) som har åtgärd ATS för livs längden för tjänsten. Priser – eller den uppskattade månads kostnaden för att köra tjänsten – visas på sidan **Välj pris nivå** för portalen när du skapar tjänsten. Om du har etablering via PowerShell eller Azure CLI i stället anges nivån via- **`-Sku`** parametern och du bör kontrol lera [service prissättningen](https://azure.microsoft.com/pricing/details/search/) för att lära dig mer om de beräknade kostnaderna.

Nivån som du väljer avgör:

+ Maximalt antal index och andra objekt som tillåts för tjänsten
+ Storlek och hastighet för partitioner (fysisk lagring)
+ Fakturerbart pris som en fast månads kostnad, men även en stegvis kostnad om du lägger till kapacitet

I några få instanser bestämmer nivån som du väljer tillgänglighet för [Premium funktioner](#premium-features).

> [!NOTE]
> Letar du efter information om "Azure SKU"? Börja med [priser för Azure](https://azure.microsoft.com/pricing/) och rulla sedan nedåt för länkar till pris sidor per tjänst.

## <a name="tier-descriptions"></a>Nivå beskrivningar

Nivåerna är **kostnads fria**, **Basic**, **standard** och **Storage-optimerade**. Standard-och lagrings optimering är tillgänglig med flera konfigurationer och kapaciteter. Följande skärm bild från Azure Portal visar tillgängliga nivåer, minus priser (som du hittar i portalen och på [prissättnings sidan](https://azure.microsoft.com/pricing/details/search/). 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Pris nivå diagram" border="true":::

**Kostnads fri** skapar en begränsad Sök tjänst för mindre projekt, som att köra självstudier och kod exempel. Internt delas system resurser mellan flera prenumeranter. Du kan inte skala en kostnads fri tjänst eller köra betydande arbets belastningar.

**Basic** och **standard** är de vanligaste fakturerbara nivåerna, med **standard** som standard eftersom det ger dig större flexibilitet vid skalning för arbets belastningar. Med dedikerade resurser under kontrollen kan du distribuera större projekt, optimera prestanda och öka kapaciteten.

Vissa nivåer är utformade för vissa typer av arbete. Till exempel är **standard 3 hög densitet (S3 HD)** ett *värd läge* för S3, där den underliggande maskin varan är optimerad för ett stort antal mindre index och är avsedd för flera organisationers scenarier. S3 HD har samma kostnad per enhet som S3, men maskin varan är optimerad för snabba fil läsningar på ett stort antal mindre index.

**Lagrings optimerings** nivåer ger större lagrings kapacitet till ett lägre pris per TB än standard nivåerna. Den främsta kompromissen är högre svars tid för frågor, som du bör validera för dina specifika program krav. Mer information om prestanda överväganden för den här nivån finns i [överväganden för prestanda och optimering](search-performance-optimization.md).

Du hittar mer information om olika nivåer på [sidan prissättning](https://azure.microsoft.com/pricing/details/search/)i avsnittet [begränsningar för tjänsten i Azure kognitiv sökning](search-limits-quotas-capacity.md) -artikeln och på Portal sidan när du skapar en tjänst.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>Funktions tillgänglighet per nivå

De flesta funktioner är tillgängliga på alla nivåer, inklusive den kostnads fria nivån. I några fall påverkar den nivå du väljer möjligheten att implementera en funktion. I följande tabell beskrivs de funktions begränsningar som är relaterade till tjänst nivån.

| Funktion | Begränsningar |
|---------|-------------|
| [indexerare](search-indexer-overview.md) | Indexerare är inte tillgängliga på S3 HD.  |
| [AI-anrikning](search-security-manage-encryption-keys.md) | Körs på den kostnads fria nivån men rekommenderas inte. |
| [Hanterade eller betrodda identiteter för utgående (index) åtkomst](search-howto-managed-identities-data-sources.md) | Inte tillgängligt på den kostnads fria nivån.|
| [Kundhanterade krypterings nycklar](search-security-manage-encryption-keys.md) | Inte tillgängligt på den kostnads fria nivån. |
| [Åtkomst till IP-brandvägg](service-configure-firewall.md) | Inte tillgängligt på den kostnads fria nivån. |
| [Privat slut punkt (integrering med Azures privata länk)](service-create-private-endpoint.md) | För inkommande anslutningar till en search-tjänst är inte tillgänglig på den kostnads fria nivån. För utgående anslutningar från indexerare till andra Azure-resurser är de inte tillgängliga på kostnads fria eller S3 HD. För indexerare som använder färdighetsuppsättningar, inte tillgängligt på kostnads fri, grundläggande, S1 eller S3 HD.| 
| [Tillgänglighetszoner](search-performance-optimization.md) | Inte tillgängligt på den kostnads fria nivån och nivån Basic. |

Resurs krävande funktioner kanske inte fungerar bra om du inte ger den tillräckligt med kapacitet. [AI-anrikning](cognitive-search-concept-intro.md) har till exempel långvariga färdigheter som tar slut på en kostnads fri tjänst, om inte data uppsättningen är liten.

## <a name="upper-limits"></a>Övre gränser

Nivåerna fastställer det maximala lagrings utrymmet för själva tjänsten, samt det maximala antalet index, indexerare, data källor, färdighetsuppsättningar och synonymer som du kan skapa. För en fullständig paus av alla gränser, se [tjänst begränsningar i Azure kognitiv sökning](search-limits-quotas-capacity.md). 

## <a name="partition-size-and-speed"></a>Partitionens storlek och hastighet

Nivå priset innehåller information om lagrings utrymme per partition som sträcker sig från 2 GB för Basic, upp till 2 TB för lagrings optimerade (L2)-nivåer. Andra egenskaper för maskin vara, till exempel hastighet för åtgärder, latens och överförings priser, publiceras inte, men nivåer som är utformade för särskilda lösnings arkitekturer bygger på maskin vara som har funktioner som stöder dessa scenarier. Mer information om partitioner finns i [Beräkna och hantera kapacitet](search-capacity-planning.md) och [skala för prestanda](search-performance-optimization.md).

## <a name="billing-rates"></a>Fakturerings taxa

Nivåer har olika fakturerings takt, med högre taxa för nivåer som körs på dyrare maskin vara eller ger dyrare funktioner. Du hittar fakturerings takten per nivå på [Azures pris sidor](https://azure.microsoft.com/pricing/details/search/) för Azure kognitiv sökning.

När du har skapat en tjänst blir fakturerings takten både en *fast kostnad* för att köra tjänsten dygnet runt och en *stegvis kostnad* om du väljer att lägga till mer kapacitet.

Sök tjänster allokeras till dator resurser i form av *partitioner* (för lagring) och *repliker* (instanser av frågemotor). Inlednings vis skapas en tjänst med var och en av dem och fakturerings takten består av båda resurserna. Men om du skalar kapaciteten går kostnaderna upp eller ned i steg om fakturerings takten.

I följande exempel visas en illustration. Anta en hypotetisk fakturerings taxa på $100 per månad. Om du behåller Sök tjänsten med den första kapaciteten för en partition och en replik, är $100 vad du kan förväntat dig att betala i slutet av månaden. Men om du lägger till ytterligare två repliker för att uppnå hög tillgänglighet ökar månads fakturan till $300 ($100 för det första replik-partition paret, följt av $200 för de två replikerna).

Den här pris modellen baseras på konceptet med att tillämpa fakturerings taxan på antalet *Sök enheter* (SU) som används av en Sök tjänst. Alla tjänster etablerades från början till en SU, men du kan öka SUs-filen genom att lägga till partitioner eller repliker för att hantera större arbets belastningar. Mer information finns i [så här uppskattar du kostnader för en Sök tjänst](search-sku-manage-costs.md).

## <a name="next-steps"></a>Nästa steg

Det bästa sättet att välja en pris nivå är att starta med en lägsta-kostnad-nivå och sedan tillåta erfarenhet och testning att informera ditt beslut om att behålla tjänsten eller skapa en ny på en högre nivå. För nästa steg rekommenderar vi att du skapar en Sök tjänst på en nivå som kan hantera den test nivå som du föreslår att göra, och sedan granska följande vägledning för att få rekommendationer om hur du uppskattar kostnader och kapacitet.

+ [Skapa en Sök tjänst](search-create-service-portal.md)
+ [Uppskatta kostnader](search-sku-manage-costs.md)
+ [Beräknad kapacitet](search-sku-manage-costs.md)