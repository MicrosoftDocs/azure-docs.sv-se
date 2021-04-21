---
title: Välj Azure File Sync principer för molnnivåindelad | Microsoft Docs
description: Information om vad du bör tänka på när du Azure File Sync principer för molnnivåindelad lagring.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f0bf41e1a847335a99b3e8f2e9ecbac504c3179e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797390"
---
# <a name="choose-cloud-tiering-policies"></a>Välj principer för molnnivåindelad lagring

Den här artikeln innehåller vägledning för användare som väljer och justerar sina principer för molnnivåindelad lagring. Innan du läser igenom den här artikeln bör du se till att du förstår hur molnnivåindelad lagring fungerar. Grundläggande om molnnivåindelad finns [i Förstå Azure File Sync lagringsnivåer för moln.](file-sync-cloud-tiering-overview.md) En detaljerad förklaring av principer för molnnivåindelad lagring med exempel finns i [Azure File Sync principer för molnnivåindelad lagring.](file-sync-cloud-tiering-policy.md)

## <a name="limitations"></a>Begränsningar
- Molnnivåindelad lagring stöds inte på Windows-systemvolymen.

- Du kan fortfarande aktivera molnnivåindelad lagring om du har en FSRM-kvot på volymnivå. När en FSRM-kvot har angetts rapporterar fråge-API:erna för ledigt utrymme som anropas automatiskt det lediga utrymmet på volymen enligt kvotinställningen. 

### <a name="minimum-file-size-for-a-file-to-tier"></a>Minsta filstorlek för en fil till nivå

För agentversion 9 och senare baseras den minsta filstorleken för en fil till nivå på filsystemets klusterstorlek. Den minsta filstorlek som är berättigad för molnnivåindelning beräknas med 2 gånger klusterstorleken och minst 8 kB. I följande tabell visas de minsta filstorlekar som kan nivåindelade, baserat på volymens klusterstorlek:

|Volymklusterstorlek (byte) |Filer av den här storleken eller större kan nivåindelade  |
|----------------------------|---------|
|4 kB eller mindre (4 096)      | 8 kB    |
|8 kB (8192)                 | 16 kB   |
|16 kB (16384)               | 32 kB   |
|32 kB (32768)               | 64 kB   |
|64 kB (65536)    | 128 kB  |
|128 kB (131072) | 256 kB |
|256 kB (262144) | 512 kB |
|512 kB (524288) | 1 MB |
|1 MB (1048576) | 2 MB |
|2 MB (2097152) | 4 MB |

Klusterstorlekar upp till 2 MB stöds med Azure File Sync agentversion 12, men för större storlekar fungerar inte molnnivåinförande.

Alla filsystem som används av Windows, ordna hårddisken baserat på klusterstorlek (kallas även storlek på allokeringsenhet). Klusterstorlek representerar den minsta mängden diskutrymme som kan användas för att innehålla en fil. När filstorlekarna inte blir ens flera av klusterstorleken måste ytterligare utrymme användas för att hålla filen – upp till nästa multipel av klusterstorleken.

Azure File Sync stöds på NTFS-volymer med Windows Server 2012 R2 och nyare. I följande tabell beskrivs standardklusterstorlekarna när du skapar en ny NTFS-volym med Windows Server 2019.

|Volymstorlek    |Windows Server 2019             |
|---------------|--------------------------------|
|7 MB–16 TB   | 4 kB                |
|16 TB – 32 TB   | 8 kB                |
|32 TB – 64 TB   | 16 kB               |
|64 TB – 128 TB  | 32 kB               |
|128 TB – 256 TB | 64 kB (tidigare max) |
|256 TB – 512 TB| 128 kB              |
|512 TB – 1 PB  | 256 kB              |
|1 PB– 2 PB    | 512 kB              |
|2 TB – 4 PB    | 1 024 kB             |
|4 TB –8 TB    | 2 048 kB (maxstorlek)  |
|> 8 TB         | stöds inte       |

När volymen skapas kan du formatera volymen manuellt med en annan klusterstorlek. Om volymen kommer från en äldre version av Windows kan standardklusterstorlekarna också vara olika. [Den här artikeln innehåller mer information om standardklusterstorlekar.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Även om du väljer en klusterstorlek som är mindre än 4 kB gäller fortfarande en gräns på 8 kB som den minsta filstorleken som kan nivåindelad. (Även om klusterstorleken tekniskt sett skulle vara mindre än 8 kB.)

Orsaken till det absoluta minimivärdet finns på det sätt som NTFS lagrar mycket små filer – filer med en storlek på 1 kB till 4 kB. Beroende på andra parametrar för volymen är det möjligt att små filer inte lagras i ett kluster på disken alls. Det kan vara mer effektivt att lagra sådana filer direkt i volymens huvudfiltabell eller "MFT-post". Lagringsplatsen för molnnivåindelad lagring lagras alltid på disk och tar upp exakt ett kluster. Nivåindelad lagring av sådana små filer kan leda till att det inte finns några utrymmesbesparingar. Extrema fall kan till och med få mer utrymme med molnnivåindelad nivåindelad. För att skydda mot det är den minsta storleken på en fil som molnnivåindelningen kommer att nivåindelad vara 8 kB på en klusterstorlek på 4 kB eller mindre. 

## <a name="selecting-your-initial-policies"></a>Välja dina första principer

När du aktiverar molnnivåindelad på en serverslutpunkt bör du vanligtvis skapa en lokal virtuell enhet för varje enskild serverslutpunkt. Genom att isolera serverslutpunkten blir det lättare att förstå hur molnnivåindelad nivåindelad fungerar och justera dina principer därefter. Men Azure File Sync fungerar även om du har flera serverslutpunkter på samma enhet finns mer information i avsnittet [Flera serverslutpunkter på lokal](file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume) volym. När du först aktiverar molnnivåindelad lagring rekommenderar vi också att du behåller datumprincipen inaktiverad och principen för ledigt volymutrymme på cirka 10 % till 20 %. För de flesta filservervolymer är 20 % ledigt utrymme vanligtvis det bästa alternativet.

För enkelhetens skull och för att få en tydlig förståelse för hur objekt kommer att nivåindelad, rekommenderar vi att du främst justerar principen för ledigt utrymme på volymen och håller datumprincipen inaktiverad om det inte behövs. Vi rekommenderar detta eftersom de flesta kunder tycker att det är värdefullt att fylla det lokala cacheminnet med så många heta filer som möjligt och nivåindelat resten till molnet. Datumprincipen kan dock vara fördelaktig om du proaktivt vill frigöra lokalt diskutrymme och du vet att filer i den serverslutpunkten som nås efter det antal dagar som anges i din datumprincip inte behöver behållas lokalt. Genom att konfigurera datumprincipen frigörs värdefull lokal diskkapacitet för andra slutpunkter på samma volym för att cachelagra fler av sina filer.

När du har ställer in dina principer övervakar du utgående och justerar båda principerna därefter. Vi rekommenderar att du tittar specifikt på **storlek på återkallning** på molnnivå och storlek på molnnivåinsamling efter **programmått** i Azure Monitor. Information om hur du övervakar utgående data finns [i Övervaka molnnivåindelad .](file-sync-monitor-cloud-tiering.md)

## <a name="adjusting-your-policies"></a>Justera dina principer

Om mängden filer som hela tiden återkallas från Azure är större än du vill kan du ha fler heta filer än du har utrymme för att spara dem på den lokala servervolymen. Öka den lokala volymens storlek om det är möjligt och/eller minska procentandelen för principen för ledigt utrymme i små steg. Att minska procentandelen ledigt utrymme för mycket kan också få negativa konsekvenser. Högre omsättning i datauppsättningen kräver mer ledigt utrymme – för nya filer och återkallande av "kalla" filer. Nivåindelad in med en fördröjning på upp till en timme och behöver sedan bearbetningstid, vilket är anledningen till att du alltid bör ha gott om ledigt utrymme på volymen.

Att hålla mer data lokalt innebär lägre utgående kostnader eftersom färre filer kommer att återkallas från Azure, men kräver också en större mängd lokal lagring, vilket medför en egen kostnad. 

När du justerar principen för ledigt utrymme på volymen bestäms mängden data som du bör behålla lokalt av följande faktorer: din bandbredd, datauppsättningens åtkomstmönster och budget. Med en anslutning med låg bandbredd kanske du vill ha mer lokala data för att säkerställa minimal fördröjning för användarna. Annars kan du basera den på omsättningsfrekvensen under en viss period. Om du till exempel vet att 10 % av datauppsättningen på 1 TB ändras eller används aktivt varje månad kanske du vill behålla 100 GB lokalt så att du inte ofta återkallar filer. Om volymen är 2 TB bör du behålla 5 % (eller 100 GB) lokalt, vilket innebär att återstående 95 % är din procentandel ledigt volymutrymme. Du bör dock lägga till en buffert för perioder med högre omsättning, med andra ord att börja med en större volym ledigt utrymme i procent och sedan justera den om det behövs senare.

## <a name="standard-operating-procedures"></a>Standardprocedurer för drift

- När du först migrerar till Azure Files via Azure File Sync är molnnivåindelad beroende av den första uppladdningen
- Molnnivåindelad kontroll av efterlevnaden av principerna för ledigt utrymme och datum för volymen varje minut
- Om du använder växeln /LFSM på Robocopy vid migrering av filer kan filer synkroniseras och molnnivåindelad lagring kan skapa utrymme under den första uppladdningen 
- Om nivåindelad inträffar innan en heatmap skapas kommer filerna att nivåindelade efter tidsstämpel för senaste ändring

## <a name="next-steps"></a>Nästa steg

* [Planera för distribution av Azure File Sync](file-sync-planning.md)
