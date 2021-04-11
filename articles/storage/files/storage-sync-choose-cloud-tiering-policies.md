---
title: Välj Azure File Sync principer för moln nivåer | Microsoft Docs
description: Information om vad du bör tänka på när du väljer Azure File Sync principer för moln nivåer.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 1/24/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 01bf9ac6a3bfcb30fb6e6a6f9d56de3f9f516f03
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059283"
---
# <a name="choose-cloud-tiering-policies"></a>Välj principer för moln nivåer

Den här artikeln innehåller rikt linjer för användare som väljer och justerar sina principer för moln nivåer. Innan du läser igenom den här artikeln bör du se till att du förstår hur moln nivån fungerar. Grundläggande information om moln nivåer finns i [förstå Azure File Sync moln nivåer](storage-sync-cloud-tiering-overview.md). En djupgående förklaring av principer för moln nivåer med exempel finns i [Azure File Sync principer för moln nivåer](storage-sync-cloud-tiering-policy.md).

## <a name="limitations"></a>Begränsningar
- Det finns inte stöd för moln nivåer på Windows-systemvolymen.

- Du kan fortfarande aktivera moln nivåer om du har en kvot på volym nivå. När du har angett en FSRM-kvot kan du med fråga-API: er som anropas automatiskt rapportera det lediga utrymmet på volymen enligt kvot inställningen. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Minsta fil storlek för en fil till nivå

För agent versioner 9 och senare baseras den minsta fil storleken för en fil på nivån på fil systemets kluster storlek. Den minsta fil storlek som är berättigad till moln skiktning beräknas med dubbelt så stor kluster storlek som kluster storlek och minst 8 KB. I följande tabell visas de minsta fil storlekarna som kan vara i nivå, baserat på volym kluster storleken:

|Volym kluster storlek (byte) |Filer av den här storleken eller större kan skiktas  |
|----------------------------|---------|
|4 KB eller mindre (4096)      | 8 kB    |
|8 KB (8192)                 | 16 kB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 kB   |
|64 KB (65536)    | 128 kB  |
|128 KB (131072) | 256 kB |
|256 KB (262144) | 512 kB |
|512 KB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

Kluster storlekar på upp till 2 MB stöds med Azure File Sync agent version 12, men för större storlekar fungerar inte moln nivåer.

Alla fil system som används av Windows, organisera din hård disk baserat på kluster storlek (även kallat storlek på allokeringsenhet). Kluster storleken representerar den minsta mängd disk utrymme som kan användas för att lagra en fil. Om fil storlekar inte kommer ut till en till följd av kluster storleken, måste ytterligare utrymme användas för att lagra filen till nästa multipel av kluster storleken.

Azure File Sync stöds på NTFS-volymer med Windows Server 2012 R2 och senare. I följande tabell beskrivs standard storlekarna för klustret när du skapar en ny NTFS-volym med Windows Server 2019.

|Volym storlek    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB – 16 TB   | 4 kB                |
|16TB – 32 TB   | 8 kB                |
|32 TB – 64 TB   | 16 kB               |
|64 TB – 128 TB  | 32 KB               |
|128TB – 256 TB | 64 KB (tidigare max) |
|256 TB – 512 TB| 128 kB              |
|512 TB – 1 PB  | 256 kB              |
|1 PB – 2 PB    | 512 kB              |
|2 TB – 4 PB    | 1024 KB             |
|4 TB – 8 TB    | 2048 KB (Max storlek)  |
|> 8 TB         | stöds inte       |

När volymen har skapats har du kanske formaterat volymen manuellt med en annan kluster storlek. Om volymen härrör från en äldre version av Windows kan standard kluster storlekarna också vara olika. [Den här artikeln innehåller mer information om standard kluster storlekar.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Även om du väljer en kluster storlek som är mindre än 4 KB, så gäller en 8 KB-gräns som den minsta fil storleken som kan skiktas, gäller fortfarande. (Även om den tekniskt 2x kluster storleken skulle likställas med mindre än 8 KB.)

Orsaken till det absoluta minimivärdet finns på hur NTFS lagrar mycket små filer-1 KB till 4 KB stora filer. Beroende på andra parametrar för volymen är det möjligt att små filer inte lagras i ett kluster på disken alls. Det är förmodligen mer effektivt att lagra sådana filer direkt i volymens huvud fil tabell eller "MFT-post". Referens punkten för moln skiktning lagras alltid på disk och tar upp exakt ett kluster. Att dela upp sådana små filer kan leda till att det inte går att spara. Extrema fall kan till och med få plats med mer utrymme med aktiverat moln skiktning. För att skydda mot detta är den minsta storleken på en fil som moln skikts nivån är 8 KB på en 4 KB eller mindre kluster storlek. 

## <a name="selecting-your-initial-policies"></a>Välja de ursprungliga principerna

När du aktiverar moln nivåer på en server slut punkt bör du vanligt vis skapa en lokal virtuell enhet för varje enskild server slut punkt. Genom att isolera Server slut punkten blir det enklare att förstå hur moln nivåer fungerar och justera dina principer på lämpligt sätt. Men Azure File Sync fungerar även om du har flera Server slut punkter på samma enhet. mer information finns i avsnittet [om flera Server slut punkter i avsnittet lokal volym](storage-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) . Vi rekommenderar också att när du först aktiverar moln nivåer, behåller du datum principen inaktive rad och volymens lediga utrymmes princip vid cirka 10% till 20%. För de flesta fil Server volymer är 20% volymens lediga utrymme vanligt vis det bästa alternativet.

För enkelhetens skull och för att få en tydlig förståelse för hur objekt kommer att skiktas, rekommenderar vi att du huvudsakligen justerar din mängd ledigt utrymme-princip och behåller din datum princip inaktive rad om det inte behövs. Vi rekommenderar detta eftersom de flesta kunder tycker att det är värdefullt att fylla i det lokala cacheminnet med så många frekventa filer som möjligt och att öka nivån på resten av molnet. Datum policyn kan dock vara fördelaktig om du vill kunna frigöra det lokala disk utrymmet proaktivt och du vet att filer i den server slut punkten nås efter det att antalet dagar som anges i din datum policy inte behöver hållas lokalt. Om du ställer in datum principen frigörs värdefull lokal disk kapacitet för andra slut punkter på samma volym för att cachelagra fler filer.

När du har angett dina principer, kan du övervaka utgående trafik och justera båda principerna efter behov. Vi rekommenderar att du specifikt tittar på **moln nivåns åter** användnings storlek och **moln nivåns återställnings storlek med hjälp av program** mått i Azure Monitor. Information om hur du övervakar utgående data finns i [övervaka moln nivåer](storage-sync-monitor-cloud-tiering.md).

## <a name="adjusting-your-policies"></a>Justera dina principer

Om mängden filer som ständigt återkallas från Azure är större än du vill ha, kan du ha fler frekventa filer än du har utrymme att spara på den lokala server volymen. Öka din lokala volym storlek om det är möjligt och/eller minska mängden ledigt utrymme i procent i små steg. Minskning av mängden ledigt utrymme i procent för mycket kan också ha negativa konsekvenser. Högre omsättning i din data uppsättning kräver mer ledigt utrymme – för nya filer och återkallande av "kall" filer. Nivå inleder i med en fördröjning på upp till en timme och behöver sedan bearbetnings tiden, vilket är anledningen till att du alltid bör ha gott om ledigt utrymme på volymen.

Att behålla mer data lokalt innebär lägre utgångs kostnader eftersom färre filer kommer att återkallas från Azure, men kräver också en större mängd lokal lagring som kommer att få en egen kostnad. 

När du justerar din princip för ledigt utrymme på din volym bestäms den mängd data som du ska behålla lokalt genom följande faktorer: bandbredd, data uppsättningens åtkomst mönster och budget. Med en anslutning med låg bandbredd kan du behöva mer lokala data för att säkerställa en minimal fördröjning för användarna. Annars kan du basera den på omsättnings skatten under en viss period. Om du till exempel vet att 10% av data uppsättningarna för 1 – TB eller är aktivt tillgängliga varje månad, kan du behålla 100 GB lokalt så att du inte ofta anropar filer. Om volymen är 2 TB vill du behålla 5% (eller 100 GB) lokalt, vilket innebär att återstående 95% är volymens lediga utrymme i procent. Du bör dock lägga till en buffert för perioder med högre omsättning – med andra ord som börjar med en större mängd ledigt utrymme i procent och sedan justerar den om det behövs senare.

## <a name="standard-operating-procedures"></a>Standard operativa procedurer

- När du först migrerar till Azure Files via Azure File Sync är moln skiktet beroende av den inledande uppladdningen
- Moln nivåer kontrollerar efterlevnad med volymens lediga utrymme och datum principer var 60: e minut
- Om du använder/LFSM-växeln på Robocopy när du migrerar filer kan filer synkroniseras och moln skiktas för att göra utrymmet under den inledande överföringen 
- Om skiktning inträffar innan en termisk karta skapas, kommer filer att belastas efter senast ändrad tidsstämpel

## <a name="next-steps"></a>Nästa steg
* [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
