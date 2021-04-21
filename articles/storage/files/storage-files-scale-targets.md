---
title: Skalbarhet för Azure Files och prestandamål
description: Lär dig mer om skalbarhets- och prestandamålen för Azure Files, inklusive kapacitet, begärandefrekvens och gränser för inkommande och utgående bandbredd.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 276dd7aa1925fefaaa94dfdd5d7a5baba5164f56
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790265"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalbarhet för Azure Files och prestandamål
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via SMB- och NFS-filsystemprotokollen. Den här artikeln beskriver målen för skalbarhet och prestanda för Azure Files och Azure File Sync.

Skalbarhets- och prestandamålen som anges här är avancerade mål, men kan påverkas av andra variabler i distributionen. Till exempel kan dataflödet för en fil också begränsas av din tillgängliga nätverksbandbredd, inte bara servrarna som är värdar för dina Azure-filresurser. Vi rekommenderar starkt att du testar ditt användningsmönster för att avgöra om skalbarheten och prestandan Azure Files uppfyller dina krav. Vi strävar också efter att öka dessa gränser över tid. 

## <a name="azure-files-scale-targets"></a>Azure Files – skalningsmål
Azure-filresurser distribueras till lagringskonton, som är objekt på den översta nivån som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera filresurser. Det finns därför tre kategorier att tänka på: lagringskonton, Azure-filresurser och filer.

### <a name="storage-account-scale-targets"></a>Skalningsmål för lagringskonto
Azure stöder flera typer av lagringskonton för olika lagringsscenarier som kunder kan ha, men det finns två huvudsakliga typer av lagringskonton för Azure Files. Vilken typ av lagringskonto du behöver skapa beror på om du vill skapa en standardfilresurs eller en premiumfilresurs: 

- **GPv2-lagringskonton (GPv2)** för generell användning: Med GPv2-lagringskonton kan du distribuera Azure-filresurser på standard-/hårddiskbaserad (HDD-baserad) maskinvara. Förutom att lagra Azure-filresurser kan GPv2-lagringskonton lagra andra lagringsresurser, till exempel blobcontainrar, köer eller tabeller. Filresurser kan distribueras till transaktionsoptimerade (standard), heta eller coola nivåer.

- **FileStorage-lagringskonton:** Med FileStorage-lagringskonton kan du distribuera Azure-filresurser på premium-/solid state-diskbaserad (SSD-baserad) maskinvara. FileStorage-konton kan bara användas för att lagra Azure-filresurser. inga andra lagringsresurser (blobcontainrar, köer, tabeller osv.) kan distribueras i ett FileStorage-konto.

| Attribut | GPv2-lagringskonton (standard) | FileStorage-lagringskonton (premium) |
|-|-|-|
| Antal lagringskonton per region och prenumeration | 250 | 250 |
| Maximal kapacitet för lagringskonto | 5 PiB<sup>1</sup> | 100 TiB (etablerat) |
| Maximalt antal filresurser | Obegränsat | Obegränsad, total etableringsstorlek för alla resurser måste vara mindre än maxkapaciteten för lagringskontot |
| Maximal frekvens för samtidiga förfrågningar | 20 000 IOPS<sup>1</sup> | 100 000 IOPS |
| Högsta ingress | <ul><li>USA/Europa: 10 Gbit/s<sup>1</sup></li><li>Andra regioner (LRS/ZRS): 10 Gbit/s<sup>1</sup></li><li>Andra regioner (GRS): 5 Gbit/s<sup>1</sup></li></ul> | 4 136 MiB/sek |
| Maximal utgående | 50 Gbit/s<sup>1</sup> | 6 204 MiB/sek |
| Maximalt antal regler för virtuellt nätverk | 200 | 200 |
| Maximalt antal IP-adressregler | 200 | 200 |
| Läsåtgärder för hantering | 800 per 5 minuter | 800 per 5 minuter |
| Skrivåtgärder för hantering | 10 per sekund/1200 per timme | 10 per sekund/1200 per timme |
| Åtgärder för hanteringslista | 100 per 5 minuter | 100 per 5 minuter |

<sup>1</sup> Lagringskonton för generell användning version 2 stöder högre kapacitetsgränser och högre gränser för ingress efter begäran. Om du vill begära en ökning av kontogränserna kontaktar [du Azure Support](https://azure.microsoft.com/support/faq/).

### <a name="azure-file-share-scale-targets"></a>Skalningsmål för Azure-filresurs
| Attribut | Standardfilresurser<sup>1</sup> | Premium-filresurser |
|-|-|-|
| Minsta storlek på en filresurs | Inget minimum | 100 GiB (etablerat) |
| Enheter för ökad/minskad etableringsstorlek | Ej tillämpligt | 1 GiB |
| Maximal storlek på en filresurs | <ul><li>100 TiB, med funktionen stor filresurs aktiverad<sup>2</sup></li><li>5 TiB, standard</li></ul> | 100 TiB |
| Maximalt antal filer i en filresurs | Obegränsad | Obegränsad |
| Maximal förfrågningsfrekvens (max IOPS) | <ul><li>10 000, med funktionen för stora filresurser aktiverad<sup>2</sup></li><li>1 000 eller 100 begäranden per 100 ms, standard</li></ul> | <ul><li>Baslinje-IOPS: 400 + 1 IOPS per GiB, upp till 100 000</li><li>IOPS-bursting: Max (4 000,3 x IOPS per GiB), upp till 100 000</li></ul> |
| Högsta ingress för en enskild filresurs | <ul><li>Upp till 300 MiB/sek, med funktionen för stor filresurs aktiverad<sup>2</sup></li><li>Upp till 60 MiB per sekund, standard</li></ul> | 40 MiB/s + 0,04 * etablerat GiB |
| Maximal utgående för en enskild filresurs | <ul><li>Upp till 300 MiB/sek, med funktionen för stor filresurs aktiverad<sup>2</sup></li><li>Upp till 60 MiB per sekund, standard</li></ul> | 60 MiB/s + 0,06 * etablerat GiB |
| Maximalt antal resursögonblicksbilder | 200 ögonblicksbilder | 200 ögonblicksbilder |
| Maximal namnlängd för objekt (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Högsta sökvägsnamnskomponent (i sökvägen \A\B\C\D är varje bokstav en komponent) | 255 tecken | 255 tecken |
| Gräns för hårda länkar (endast NFS) | Ej tillämpligt | 178 |
| Maximalt antal SMB Multichannel-kanaler | Saknas | 4 |
| Maximalt antal lagrade åtkomstprinciper per filresurs | 5 | 5 |

<sup>1</sup> Gränserna för standardfilresurser gäller för alla tre nivåerna som är tillgängliga för standardfilresurser: transaktionsoptimerad, het och kall.

<sup>2</sup> Standardvärdet för standardfilresurser är [](./storage-files-how-to-create-large-file-share.md) 5 TiB. Mer information om hur du ökar standardfilresurser skalas upp till 100 TiB finns i Aktivera och skapa stora filresurser.

### <a name="file-scale-targets"></a>Filskalningsmål
| Attribut | Filer i standardfilresurser  | Filer i premiumfilresurser  |
|-|-|-|
| Maximal filstorlek | 4 TiB | 4 TiB |
| Maximal frekvens för samtidiga förfrågningar | 1 000 IOPS | Upp till 8 000<sup>1</sup> |
| Maximalt antal ingresser för en fil | 60 MiB/sek | 200 MiB/sek (upp till 1 GiB/s med förhandsversion av SMB Multichannel)<sup>2</sup>|
| Maximal utgående för en fil | 60 MiB/sek | 300 MiB/sek (upp till 1 GiB/s med förhandsversion av SMB Multichannel)<sup>2</sup> |
| Maximalt antal samtidiga referenser | 2 000 referenser | 2 000 referenser  |

<sup>1 Gäller för läs- och skriv-I/O(vanligtvis mindre I/O-storlekar som är mindre än eller lika med 64 KiB). Metadataåtgärder, förutom läsningar och skrivningar, kan vara lägre.</sup> 
 <sup>2 Med hänsyn till datornätverksgränser, tillgänglig bandbredd, I/O-storlekar, ködjup och andra faktorer. Mer information finns i [Prestanda för SMB Multichannel.](./storage-files-smb-multichannel-performance.md)</sup>

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync – skalningsmål
I följande tabell visas gränserna för Microsofts testning och även vilka mål som är hårda gränser:

| Resurs | Mål | Hård gräns |
|----------|--------------|------------|
| Tjänster för synkronisering av lagring per region | 100 Tjänster för synkronisering av lagring | Yes |
| Synkroniseringsgrupper per tjänst för synkronisering av lagring | 200 synkroniseringsgrupper | Yes |
| Registrerade servrar per tjänst för synkronisering av lagring | 99 servrar | Yes |
| Molnslutpunkter per synkroniseringsgrupp | 1 molnslutpunkt | Yes |
| Serverslutpunkter per synkroniseringsgrupp | 100 serverslutpunkter | Yes |
| Serverslutpunkter per server | 30 serverslutpunkter | Yes |
| Filsystemobjekt (kataloger och filer) per synkroniseringsgrupp | 100 miljoner objekt | No |
| Maximalt antal filsystemobjekt (kataloger och filer) i en katalog | 5 miljoner objekt | Yes |
| Maximal storlek för objektsäkerhet (kataloger och filer) | 64 KiB | Yes |
| Filstorlek | 100 GiB | No |
| Minsta filstorlek för en fil som ska nivåindelad | V9 och nyare: Baseras på filsystemets klusterstorlek (dubbel filsystemklusterstorlek). Om filsystemets klusterstorlek till exempel är 4 KiB blir den minsta filstorleken 8 KiB.<br> V8 och äldre: 64 KiB  | Yes |

> [!Note]  
> En Azure File Sync-slutpunkt kan skala upp till storleken på en Azure-filresurs. Om storleksgränsen för Azure-filresursen har nåtts kommer synkroniseringen inte att fungera.

### <a name="azure-file-sync-performance-metrics"></a>Azure File Sync – prestandamått
Eftersom Azure File Sync-agenten körs på en Windows Server-dator som ansluter till Azure-filresurser beror den effektiva synkroniseringsprestandan på ett antal faktorer i infrastrukturen: Windows Server och den underliggande diskkonfigurationen, nätverksbandbredden mellan servern och Azure Storage, filstorlek, total datauppsättningsstorlek och aktiviteten på datauppsättningen. Eftersom Azure File Sync fungerar på filnivå mäts prestandaegenskaperna för en Azure File Sync-baserad lösning bättre i antalet objekt (filer och kataloger) som bearbetas per sekund.

För Azure File Sync är prestanda kritiskt i två steg:

1. **Inledande etablering en gång:** För att optimera prestanda vid inledande etablering, se Registrering med [Azure File Sync](../file-sync/file-sync-deployment-guide.md#onboarding-with-azure-file-sync) för optimal distributionsinformation.
2. **Pågående synkronisering:** När data initialt har seedats i Azure-filresurser kan Azure File Sync flera slutpunkter synkroniserade.

Nedan visas de resultat som observerats under den interna testningen i ett system med en konfiguration som hjälper dig att planera distributionen för varje steg

| Systemkonfiguration | Information |
|-|-|
| Processor | 64 virtuella kärnor med 64 MiB L3-cache |
| Minne | 128 GiB |
| Disk | SAS-diskar med RAID 10 med batteribaserad cache |
| Nätverk | 1 Gbit/s-nätverk |
| Arbetsbelastning | Generell användning-filserver|

| Första etableringen en gång  | Information |
|-|-|
| Antal objekt | 25 miljoner objekt |
| Datauppsättningens storlek| ~4,7 TiB |
| Genomsnittlig filstorlek | ~200 KiB (största fil: 100 GiB) |
| Inledande uppräkning av molnändring | 20 objekt per sekund  |
| Ladda upp dataflöde | 20 objekt per sekund per synkroniseringsgrupp |
| Dataflöde för nedladdning av namnområde | 400 objekt per sekund |

### <a name="initial-one-time-provisioning"></a>Inledande entidsetablering

**Inledande molnändringsuppräkning:** När en ny synkroniseringsgrupp skapas är den första molnändringsuppräkningen det första steget som ska köras. I den här processen räknar systemet upp alla objekt i Azure-filresursen. Under den här processen kommer ingen synkroniseringsaktivitet att ske. Inga objekt laddas ned från molnslutpunkten till serverslutpunkten och inga objekt laddas upp från serverslutpunkten till molnslutpunkten. Synkroniseringsaktiviteten återupptas när den första molnändringsuppräkningen har slutförts.
Prestandahastigheten är 20 objekt per sekund. Kunder kan uppskatta hur lång tid det tar att slutföra den första molnändringsuppräkningen genom att fastställa antalet objekt i molnresursen och använda följande formler för att hämta tiden i dagar. 

   **Tid (i dagar) för inledande molnuppräkning = (antal objekt i molnslutpunkten)/(20 * 60 * 60 * 24)**

**Inledande synkronisering av data från Windows Server** till Azure-filresurs: Många Azure File Sync-distributioner börjar med en tom Azure-filresurs eftersom alla data finns på Windows Server. I dessa fall går den första molnändringsuppräkningen snabbt och merparten av tiden kommer att läggas på att synkronisera ändringar från Windows Server till Azure-filresursen. 

Synkronisering laddar upp data till Azure-filresursen, men det finns ingen [](../file-sync/file-sync-server-registration.md#set-azure-file-sync-network-limits) avbrottstid på den lokala filservern, och administratörer kan konfigurera nätverksgränser för att begränsa mängden bandbredd som används för uppladdning av bakgrundsdata.

Inledande synkronisering begränsas vanligtvis av den inledande uppladdningshastigheten på 20 filer per sekund per synkroniseringsgrupp. Kunder kan uppskatta hur lång tid det tar att ladda upp alla sina data till Azure med hjälp av följande formler för att få tid i dagar:  

   **Tid (i dagar) för att ladda upp filer till en synkroniseringsgrupp = (antal objekt i serverslutpunkten)/(20 * 60 * 60 * 24)**

Att dela upp dina data i flera serverslutpunkter och synkroniseringsgrupper kan påskynda den här inledande datauppladdningen, eftersom uppladdningen kan göras parallellt för flera synkroniseringsgrupper med en hastighet på 20 objekt per sekund var. Därför skulle två synkroniseringsgrupper köras med en kombinerad hastighet på 40 objekt per sekund. Den totala tiden som ska slutföras är tidsuppskattningen för synkroniseringsgruppen med flest filer att synkronisera.

**Dataflöde för nedladdning av namnområde** När en ny serverslutpunkt läggs till i en befintlig synkroniseringsgrupp Azure File Sync agenten inte något filinnehåll från molnslutpunkten. Den synkroniserar först det fullständiga namnområdet och utlöser sedan bakgrundssynkronisering för att ladda ned filerna, antingen i sin helhet eller, om molnnivåindelad är aktiverad, till den princip för molnnivåindelad lagring som angetts på serverslutpunkten.

| Pågående synkronisering  | Information  |
|-|--|
| Antal objekt som synkroniserats| 125 000 objekt (~1 % omsättning) |
| Datauppsättningens storlek| 50 GiB |
| Genomsnittlig filstorlek | ~500 KiB |
| Ladda upp dataflöde | 20 objekt per sekund per synkroniseringsgrupp |
| Fullständigt dataflöde för nedladdning* | 60 objekt per sekund |

*Om molnnivåindelning är aktiverat ser du troligen bättre prestanda eftersom endast vissa fildata laddas ned. Azure File Sync laddar bara ned data för cachelagrade filer när de ändras på någon av slutpunkterna. För nivåindelade eller nyligen skapade filer laddar agenten inte ned fildata, utan synkroniserar i stället bara namnområdet till alla serverslutpunkter. Agenten har också stöd för partiell nedladdning av nivåindelade filer när de används av användaren. 

> [!Note]  
> Siffrorna ovan är inte en indikation på den prestanda som du kommer att uppleva. Den faktiska prestandan beror på flera faktorer som beskrivs i början av det här avsnittet.

Som en allmän vägledning för distributionen bör du tänka på några saker:

- Objektets dataflöde skalas ungefär i proportion till antalet synkroniseringsgrupper på servern. Att dela upp data i flera synkroniseringsgrupper på en server ger bättre dataflöde, vilket också begränsas av servern och nätverket.
- Objektets dataflöde är omvänt proportionellt mot Dataflödet MiB per sekund. För mindre filer får du högre dataflöde när det gäller antalet objekt som bearbetas per sekund, men lägre MiB per sekund. För större filer får du däremot färre objekt som bearbetas per sekund, men högre MiB per sekund-dataflöde. MiB per sekund-dataflödet begränsas av de Azure Files skalningsmål.

## <a name="see-also"></a>Se även
- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Planera för distribution av Azure File Sync](../file-sync/file-sync-planning.md)