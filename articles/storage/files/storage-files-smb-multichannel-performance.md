---
title: SMB Multichannel-prestanda-Azure Files
description: Läs mer om SMB Multichannel-prestanda.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a9edd93aa265622732be4a7582cce9900959bf6d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374991"
---
# <a name="smb-multichannel-performance"></a>SMB Multichannel-prestanda

Azure Files SMB Multichannel (för hands version) gör det möjligt för en SMB 3. x-klient att upprätta flera nätverks anslutningar till Premium-filresurserna i ett FileStorage-konto. SMB 3,0-protokollet introducerade SMB Multichannel-funktionen i Windows Server 2012-och Windows 8-klienter. Därför kan alla Azure Files SMB 3. x-klienter som stöder SMB Multichannel dra nytta av funktionen för sina Azure Premium-filresurser. Det kostar inget extra att aktivera SMB Multichannel på ett lagrings konto.

## <a name="benefits"></a>Fördelar

Azure Files SMB Multichannel gör det möjligt för klienter att använda flera nätverks anslutningar som ger bättre prestanda och lägre ägande kostnader. Ökad prestanda uppnås genom bandbredds agg regering över flera nätverkskort och använder stöd för mottagnings sidans skalning (RSS) för nätverkskort för att distribuera IO-belastningen över flera processorer.

- **Ökat data flöde**: flera anslutningar gör att data kan överföras över flera sökvägar parallellt och därmed avsevärt fördelar arbets belastningar som använder större fil storlekar med större IO-storlekar och kräver högt data flöde från en enda virtuell dator eller en mindre uppsättning virtuella datorer. Några av de här arbets belastningarna är media och underhållning för skapande av innehåll eller kodning, genomik och analys av finansiella tjänster.
- **Högre IOPS**: RSS-kapacitet för nätverkskort möjliggör effektiv belastnings fördelning över flera processorer med flera anslutningar. Detta hjälper till att uppnå högre IOPS-skalning och effektiv användning av VM-processorer. Detta är användbart för arbets belastningar som har små i/o-storlekar, till exempel databas program.
- **Nätverks fel tolerans**: flera anslutningar minskar risken för avbrott eftersom klienterna inte längre förlitar sig på en enskild anslutning.
- **Automatisk konfiguration**: när SMB Multichannel är aktive rad på klienter och lagrings konton, möjliggör dynamisk identifiering av befintliga anslutningar och kan skapa ytterligare anslutnings Sök vägar om det behövs.
- **Kostnads optimering**: arbets belastningar kan uppnå högre skala från en enskild virtuell dator eller en liten uppsättning virtuella datorer, samtidigt som de ansluter till Premium-resurser. Detta kan minska den totala ägande kostnaden genom att minska antalet virtuella datorer som behövs för att köra och hantera en arbets belastning.

Mer information om SMB Multichannel finns i [Windows-dokumentationen](/azure-stack/hci/manage/manage-smb-multichannel).

Den här funktionen ger bättre prestanda för program med flera trådar, men kan vanligt vis inte hjälpa program med en tråd. Mer information finns i avsnittet [prestanda jämförelse](#performance-comparison) .

## <a name="limitations"></a>Begränsningar

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Konfiguration

SMB Multichannel fungerar bara när funktionen är aktive rad på både klient sidan (klienten) och på tjänst sidan (ditt Azure Storage-konto).

SMB Multichannel är aktiverat som standard på Windows-klienter. Du kan verifiera konfigurationen genom att köra följande PowerShell-kommando: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
På ditt Azure Storage-konto måste du aktivera SMB Multichannel. Se [Aktivera SMB Multichannel (för hands version)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>Inaktivera SMB Multichannel
I de flesta fall, särskilt Multi-threaded-arbetsbelastningar, bör klienterna se bättre prestanda med SMB Multichannel. Vissa olika scenarier, till exempel enkel tråds arbets belastningar eller för testning, kanske du vill inaktivera SMB Multichannel. Mer information finns i [jämförelse av prestanda](#performance-comparison) .

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Verifiera att SMB Multichannel är korrekt konfigurerad

1. Skapa en Premium fil resurs eller Använd en befintlig.
1. Se till att klienten har stöd för SMB Multichannel (ett eller flera nätverkskort har skalning på mottagar sidan aktiverat). Mer information finns i [Windows-dokumentationen](/azure-stack/hci/manage/manage-smb-multichannel) .
1. Montera en fil resurs på klienten.
1. Generera belastning med ditt program.
    Ett kopierings verktyg, till exempel Robocopy/MT, eller ett prestanda verktyg som Diskspd för att läsa/skriva filer kan generera belastning.
1. Öppna PowerShell som administratör och Använd följande kommando: `Get-SmbMultichannelConnection |fl`
1. Sök efter egenskaper för **MaxChannels** och **CurrentChannels**

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Skärm bild av Get-SMBMultichannelConnection resultat." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Prestanda jämförelse

Det finns två kategorier för arbets belastnings-och skriv arbets belastnings mönster – en trådad och multi-threading. De flesta arbets belastningar använder flera filer, men det kan finnas vissa användnings fall där arbets belastningen fungerar med en enda fil i en resurs. I det här avsnittet beskrivs olika användnings fall och prestanda påverkan för var och en av dem. I allmänhet är de flesta arbets belastningar flertrådade och distribuerar arbets belastningar över flera filer så att de bör studera betydande prestanda förbättringar med SMB Multichannel.

- **Flertrådade/flera filer**: beroende på arbets belastnings mönstret bör du se betydande prestanda förbättringar i Läs-och skriv iOS över flera kanaler. Prestanda vinster skiljer sig från var som helst mellan 2x och 4x vad gäller IOPS, data flöde och svars tid. I den här kategorin ska SMB Multichannel vara aktive rad för bästa prestanda.
- **Multi-threadd/Single File**: för de flesta användnings fall i den här kategorin drar arbets belastningarna till att ha SMB Multichannel aktiverat, särskilt om arbets belastningen har en genomsnittlig IO-storlek > ~ 16k. Några exempel scenarier som drar nytta av SMB Multichannel är säkerhets kopiering eller återställning av en enda stor fil. Ett undantag där du kanske vill inaktivera SMB Multichannel är om din arbets belastning är en låg frekvent IOs. I så fall kan det vara en liten prestanda förlust på ~ 10%. Beroende på användnings fallet bör du överväga att sprida belastningen över flera filer eller inaktivera funktionen. Mer information finns i [konfigurations](#configuration) avsnittet.
- **Single-threaded/multiple files eller Single File**: för de flesta trådade arbets belastningar finns det minimala prestanda för delar på grund av bristande parallellitet. det finns vanligt vis en försämrad prestanda försämring på ~ 10% om SMB Multichannel är aktiverat. I det här fallet är det idealiskt att inaktivera SMB Multichannel, med ett undantag. Om den entrådiga arbets belastningen kan distribuera belastningen över flera filer och använder en genomsnittlig större IO-storlek (> ~ 16k), bör det finnas små prestanda för delar från SMB Multichannel.

### <a name="performance-test-configuration"></a>Prestandatest konfiguration

I diagrammen i den här artikeln användes följande konfiguration: en enkel standard D32s v3 VM med ett enda RSS-aktiverat nätverkskort med fyra kanaler. Inläsningen genererades med diskspd.exe, multipl-threadd med i/o-djup på 10 och slumpmässig IOs med olika IO-storlekar.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Skärm bild som visar prestanda test konfigurationen." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Multifaktorautentisering-trådade/flera filer med SMB Multichannel

Inläsningen genererades mot 10 filer med olika i/o-storlekar. Resultaten för skala upp test visade betydande förbättringar i både IOPS-och data flödes test resultat med SMB Multichannel aktiverat. Följande diagram illustrerar resultaten:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagram över prestanda" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagram över data flödes prestanda." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- På ett enda nätverkskort har prestanda ökningen av 2x-3x observerats och för skrivningar, med 3x – 4x, vad gäller både IOPS och data flöden.
- SMB Multichannel tillåts IOPS och data flöde för att uppnå VM-gränser även med ett enda nätverkskort och den fyra kanal gränsen.
- Eftersom utgång (eller läsningar till lagring) inte går att mäta, kunde Read-genomflödet överskrida den publicerade virtuella datorn på 16 000 Mbit/s (2 GiB/s). Testet nåddes >2,7 GiB/s. Ingress (eller skrivningar till lagring) omfattas fortfarande av begränsningar för virtuella datorer.
- Att sprida belastningen över flera filer får betydande förbättringar.

Ett exempel kommando som användes i det här testet är: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Arbets belastningar med flera trådar och enstaka filer med SMB Multichannel

Belastningen genererades mot en enskild 128 GiB-fil. När SMB Multichannel är aktiverat visar skala upp-testet med flera trådbaserade/enskilda filer förbättringar i de flesta fall. Följande diagram illustrerar resultaten:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagram över IOPS-prestanda." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagram över prestanda för enkel data flöde." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- På ett enda nätverkskort med en större genomsnittlig IO-storlek (> ~ 16k) fanns det betydande förbättringar i både läsningar och skrivningar.
- För mindre IO-storlekar var det en liten inverkan på ~ 10% av prestanda när SMB Multichannel var aktiverat. Detta kan minskas genom att belastningen sprids över flera filer eller att funktionen inaktive ras.
- Prestandan är fortfarande kopplad till [gränser för en enskild fil](storage-files-scale-targets.md#file-scale-targets).

## <a name="optimizing-performance"></a>Optimera prestanda

Följande tips kan hjälpa dig att optimera prestandan:

- Se till att ditt lagrings konto och din klient befinner sig i samma Azure-region för att minska nätverks fördröjningen.
- Använd flertrådade program och belastnings utjämning över flera filer.
- Prestanda för delar av SMB Multichannel-ökning med antalet filer som distribuerar belastningen.
- Premium-resursens prestanda är kopplad till en etablerad resurs storlek (IOPS/utgångs/inkommande) och fil gränser. Mer information finns i [förstå etablering för Premium-filresurser](understanding-billing.md#provisioned-model).
- Högsta prestanda för en enskild VM-klient är fortfarande kopplat till VM-gränser. [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) kan till exempel stödja en maximal bandbredd på 16 000 Mbit/s (eller 2Gbps), som utgångs punkt från den virtuella datorn (skrivning till lagring) mäts, ingress (läsningar från lagring) är inte. Prestanda för fil resurser är beroende av dator nätverks begränsningar, CPU: er, intern lagring tillgänglig nätverks bandbredd, i/o-storlekar, parallellitet, samt andra faktorer.
- Det första testet är vanligt vis ett varmt, ta bort resultaten och upprepa testet.
- Om prestanda begränsas av en enskild klient och arbets belastningen fortfarande är lägre än etablerade gränser kan du uppnå högre prestanda genom att sprida belastningen över flera klienter.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>Förhållandet mellan IOPS-, data flödes-och i/o-storlekar

**Data flöde = i/o-storlek * IOPS**

Högre IO-storlekar ger högre genomflöde och har högre latens, vilket resulterar i ett lägre antal net IOPS. Mindre i/o-storlekar kommer att köra högre IOPS men ger lägre netto genom strömning och fördröjning.

## <a name="next-steps"></a>Nästa steg

- [Aktivera SMB Multichannel på ett FileStorage-konto (förhands granskning)](storage-files-enable-smb-multichannel.md)
- Läs mer om SMB Multichannel i [Windows-dokumentationen](/azure-stack/hci/manage/manage-smb-multichannel) .
