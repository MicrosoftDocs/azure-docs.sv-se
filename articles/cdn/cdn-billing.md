---
title: Förstå Azure CDN fakturering | Microsoft Docs
description: Läs om fakturerings strukturen för innehåll som finns i Azure Content Delivery Network, inklusive fakturerings regioner, leverans avgifter och för att hantera kostnader.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: allensu
ms.openlocfilehash: aa2f00a732a3978524fc017481285859c9535387
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96018689"
---
# <a name="understanding-azure-cdn-billing"></a>Så här fungerar fakturering för Azure CDN

Detta vanliga frågor beskriver fakturerings strukturen för innehåll som finns i Azure Content Delivery Network (CDN).

## <a name="what-is-a-billing-region"></a>Vad är en fakturerings region?
En fakturerings region är ett geografiskt område som används för att avgöra vilken taxa som debiteras för leverans av objekt från Azure CDN. De aktuella fakturerings zonerna och deras regioner är följande:

- Zon 1: Nordamerika, Europa, Mellanöstern och Afrika

- Zon 2: Asien och stillahavsområdet (inklusive Japan)

- Zon 3: södra Amerika

- Zon 4: Australien och nya Zeeland

- Zon 5: Indien

Information om hur du använder POP-regioner finns [Azure CDN pop-platser efter region](./cdn-pop-locations.md). En POP som finns i Mexiko är till exempel i Nordamerika region och ingår därför i zon 1. 

Information om Azure CDN priser finns i [Content Delivery Network prissättning](https://azure.microsoft.com/pricing/details/cdn/).

## <a name="how-are-delivery-charges-calculated-by-region"></a>Hur beräknas leverans kostnader per region?
Azure CDN fakturerings region baseras på platsen för käll servern som levererar innehållet till slutanvändaren. Klientens mål (fysiska plats) betraktas inte som fakturerings region.

Exempel: om en användare som finns i Mexiko utfärdar en begäran och denna begäran betjänas av en server som finns i ett USA POP på grund av peering-eller trafik förhållanden, är fakturerings regionen USA.

## <a name="what-is-a-billable-azure-cdn-transaction"></a>Vad är en fakturerbar Azure CDN transaktion?
Alla HTTP (S)-förfrågningar som avslutas vid CDN är en fakturerbar händelse som innehåller alla svars typer: lyckades, misslyckades eller annat. Olika svar kan dock generera olika trafik mängder. Till exempel, *304 har inte ändrats* och andra skrivskyddade svar genererar lite trafik eftersom de är ett litet huvud svar. på samma sätt är fel svar (till exempel *404 som inte hittas*) fakturerbara men kostar en låg kostnad på grund av den stora nytto lasten.

## <a name="what-other-azure-costs-are-associated-with-azure-cdn-use"></a>Vilka andra Azure-kostnader är associerade med Azure CDN använda?
Om du använder Azure CDN uppstår även några användnings kostnader för de tjänster som används som ursprung för dina objekt. Dessa kostnader är vanligt vis en liten del av den totala förbruknings kostnaden för CDN.

Om du använder Azure Blob Storage som ursprung för ditt innehåll, kommer du även att debiteras följande lagrings avgifter för cache-fyllningar:

- Faktiskt använt GB: den faktiska lagringen av dina käll objekt.

- Transaktioner: vid behov för att fylla cacheminnet.

- Överföringar i GB: mängden data som överförs för att fylla CDN-cacheminnen.

> [!NOTE]
> Från och med den 2019 oktober, om du använder Azure CDN från Microsoft, är kostnaden för data överföring från ursprung som finns i Azure till CDN pop kostnads fri. Azure CDN från Verizon och Azure CDN från Akamai omfattas de priser som beskrivs nedan.

Mer information om Azure Storage fakturering finns i [förstå Azure Storage fakturering – bandbredd, transaktioner och kapacitet](https://blogs.msdn.microsoft.com/windowsazurestorage/2010/07/08/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity/).

Om du använder *värdbaserad tjänst leverans* debiteras du följande:

- Azure Compute Time: de beräknings instanser som fungerar som ursprung.

- Azure Compute transfer: data överföringar från beräknings instanserna för att fylla Azure CDN cacheminnen.

Om klienten använder byte intervall begär Anden (oavsett ursprungs tjänst) gäller följande aspekter:

- En *byte Range-begäran* är en fakturerbar transaktion vid CDN. När en klient utfärdar en byte Range-begäran gäller den här begäran för en delmängd av objektet. CDN svarar bara med en del av det begärda innehållet. Detta partiella svar är en fakturerbar transaktion och överförings beloppet begränsas till storleken på intervallets svar (plus rubriker).

- När en begäran bara anländer till en del av ett objekt (genom att ange ett byte intervall-huvud) kan CDN hämta hela objektet till dess cacheminne. Det innebär att även om den fakturerbara transaktionen från CDN är för partiellt svar kan den fakturerbara transaktionen från ursprunget omfatta objektets fulla storlek.

## <a name="how-much-transfer-activity-occurs-to-support-the-cache"></a>Hur mycket överförings aktivitet sker för att stödja cachen?
Varje gång en CDN-POP måste fylla sin cache, gör den en begäran till ursprunget för objektet som cachelagras. Det innebär att ursprunget ådrar sig en fakturerbar transaktion på varje cache missar. Antalet Cachemissar beror på ett antal faktorer:

- Hur cacheable innehållet är: om innehållet har höga TTL-värden (Time-to-Live)/expiration och används ofta så att det förblir populärt i cachen, kommer den stora delen av belastningen att hanteras av CDN. En typisk bra kvot för cache-träff är väl över 90%, vilket innebär att mindre än 10% av klient begär Anden måste gå tillbaka till ursprunget, antingen för ett cacheminne som missar eller objekt uppdatering.

- Hur många noder som behöver läsa in objektet: varje gång en nod läser in ett objekt från ursprunget påförs en fakturerbar transaktion. Det innebär att mer globalt innehåll (som nås från fler noder) resulterar i mer fakturerbara transaktioner.

- TTL-påverkan: ett högre TTL-värde för ett objekt innebär att det måste hämtas från ursprunget mindre ofta. Det innebär också att klienter, till exempel webbläsare, kan cachelagra objektet längre, vilket kan minska transaktionerna till CDN.

## <a name="which-origin-services-are-eligible-for-free-data-transfer-with-azure-cdn-from-microsoft"></a>Vilka ursprungs tjänster är berättigade till kostnads fri data överföring med Azure CDN från Microsoft? 
Om du använder någon av följande Azure-tjänster som ditt CDN-ursprung kommer du inte att debiteras från data överföring från ursprunget till CDN pop. 

- Azure Storage
- Azure Media Services
- Azure Virtual Machines
- Virtual Network
- Load Balancer
- Application Gateway
- Azure DNS
- ExpressRoute
- VPN Gateway
- Traffic Manager
- Network Watcher
- Azure Firewall
- Azure Front Door Service
- Azure Bastion
- Azure App tjänst
- Azure Functions
- Azure Data Factory
- Azure API Management
- Azure Batch 
- Öppna Azure-datautforskaren
- HDInsight
- Azure Cosmos DB
- Azure Data Lake Store
- Azure Machine Learning 
- Azure SQL Database
- Hanterad Azure SQL-instans
- Azure Cache for Redis

## <a name="how-do-i-manage-my-costs-most-effectively"></a>Hur gör jag för att hantera mina kostnader effektivt?
Ange det längsta TTL-värdet som är möjligt för ditt innehåll.