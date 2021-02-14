---
title: Skalbarhet för Azure Files och prestandamål
description: Lär dig mer om skalbarhets-och prestanda mål för Azure Files, inklusive kapacitet, begär ande frekvens och inkommande och utgående bandbredds begränsningar.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6ef255d78d3dd3ff6fcc5eba7aad522018185299
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518903"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalbarhet för Azure Files och prestandamål
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via PROTOKOLLen SMB-och NFS-filsystem. I den här artikeln beskrivs skalbarhets-och prestanda mål för Azure Files och Azure File Sync.

De skalbarhets-och prestanda mål som anges här är avancerade mål, men kan påverkas av andra variabler i distributionen. Till exempel kan data flödet för en fil också begränsas av din tillgängliga nätverks bandbredd, inte bara de servrar som är värdar för dina Azure-filresurser. Vi rekommenderar starkt att du testar ditt användnings mönster för att avgöra om skalbarheten och prestandan för Azure Files uppfyller dina krav. Vi strävar också efter att öka de här gränserna med tiden. 

## <a name="azure-files-scale-targets"></a>Azure Files – skalningsmål
Azure-filresurser distribueras till lagrings konton som är toppnivå objekt som representerar en delad lagringspool. Den här lagringspoolen kan användas för att distribuera flera fil resurser. Det finns därför tre kategorier att tänka på: lagrings konton, Azure-filresurser och filer.

### <a name="storage-account-scale-targets"></a>Skalnings mål för lagrings konto
Azure har stöd för flera typer av lagrings konton för olika lagrings scenarier. kunder kan ha, men det finns två huvud typer av lagrings konton för Azure Files. Vilken typ av lagrings konto du behöver skapa beror på om du vill skapa en standard fil resurs eller en Premium fil resurs: 

- **GPv2-lagrings konton (General Purpose version 2)**: med GPv2 Storage-konton kan du Distribuera Azure-filresurser på standard-/hård diskbaserade (HDD-baserade) maskin vara. Förutom att lagra Azure-filresurser kan GPv2 lagrings konton lagra andra lagrings resurser, till exempel BLOB-behållare, köer och tabeller. Fil resurser kan distribueras i transaktionens optimerade (standard), frekventa eller låg frekventa nivåer.

- **FileStorage lagrings konton**: med FileStorage Storage-konton kan du Distribuera Azure-filresurser i Premium/Solid-State-baserad (SSD) maskin vara. FileStorage-konton kan bara användas för att lagra Azure-filresurser. inga andra lagrings resurser (BLOB-behållare, köer, tabeller osv.) kan distribueras i ett FileStorage-konto.

| Attribut | GPv2 lagrings konton (standard) | FileStorage lagrings konton (Premium) |
|-|-|-|
| Antal lagrings konton per region per prenumeration | 250 | 250 |
| Maximal kapacitet för lagrings konto | 5 PiB<sup>1</sup> | 100 TiB (etablerad) |
| Maximalt antal fil resurser | Obegränsat | Obegränsad, total allokerad storlek för alla resurser måste vara mindre än max värdet för lagrings kontots kapacitet |
| Maximal frekvens för samtidiga begär Anden | 20 000 IOPS<sup>1</sup> | 100 000 IOPS |
| Maximalt antal inkommande | <ul><li>USA/Europa: 10 GBP/SEK<sup>1</sup></li><li>Andra regioner (LRS/ZRS): 10 GBP/s<sup>1</sup></li><li>Andra regioner (GRS): 5 GBP/s<sup>1</sup></li></ul> | 4 136 MiB/SEK |
| Högsta utgående | 50 GBP/SEK<sup>1</sup> | 6 204 MiB/SEK |
| Maximalt antal virtuella nätverks regler | 200 | 200 |
| Maximalt antal IP-adress regler | 200 | 200 |
| Läs åtgärder för hantering | 800 per 5 minuter | 800 per 5 minuter |
| Skriv åtgärder för hantering | 10 per sekund/1200 per timme | 10 per sekund/1200 per timme |
| Hanterings List åtgärder | 100 per 5 minuter | 100 per 5 minuter |

<sup>1</sup> generell-syfte version 2 lagrings konton stöder högre kapacitets gränser och högre gränser för ingångs värden på begäran. Kontakta [Azure-supporten](https://azure.microsoft.com/support/faq/)om du vill begära en ökning av konto gränserna.

### <a name="azure-file-share-scale-targets"></a>Skalnings mål för Azure-filresurs
| Attribut | Standard fil resurser<sup>1</sup> | Premium fil resurser |
|-|-|-|
| Minsta storlek på en fil resurs | Ingen minsta | 100 GiB (etablerad) |
| Allokerad storlek, öka/minska enhet | Ej tillämpligt | 1 GiB |
| Maximal storlek på en fil resurs | <ul><li>100 TiB, med stor fil resurs funktion aktive rad<sup>2</sup></li><li>5 TiB, standard</li></ul> | 100 TiB |
| Maximalt antal filer i en fil resurs | Obegränsad | Obegränsad |
| Högsta begär ande frekvens (max IOPS) | <ul><li>10 000, med stor fil resurs funktion aktive rad<sup>2</sup></li><li>1 000-eller 100-begäranden per 100 MS, standard</li></ul> | <ul><li>Bas linje IOPS: 400 + 1 IOPS per GiB, upp till 100 000</li><li>IOPS-burst: Max (4000, 3x IOPS per GiB), upp till 100 000</li></ul> |
| Maximalt antal inkommande data för en enda fil resurs | <ul><li>Upp till 300 MiB/s, med stor fil resurs funktion aktive rad<sup>2</sup></li><li>Upp till 60 MiB/s, standard</li></ul> | 40 MiB/s + 0,04 * etablerad GiB |
| Maximalt utgående för en enskild fil resurs | <ul><li>Upp till 300 MiB/s, med stor fil resurs funktion aktive rad<sup>2</sup></li><li>Upp till 60 MiB/s, standard</li></ul> | 60 MiB/s + 0,06 * etablerad GiB |
| Maximalt antal resurs ögonblicks bilder | 200 ögonblicks bilder | 200 ögonblicks bilder |
| Namn längd för maximalt objekt (kataloger och filer) | 2 048 tecken | 2 048 tecken |
| Maximal Sök vägs komponent (i sökvägen \A\B\C\D är varje bokstav en komponent) | 255 tecken | 255 tecken |
| Gräns för hårda länkar (endast NFS) | Ej tillämpligt | 178 |
| Maximalt antal SMB Multichannel-kanaler | Saknas | 4 |
| Maximalt antal lagrade åtkomst principer per fil resurs | 5 | 5 |

<sup>1</sup> gränserna för standard fil resurser gäller alla tre av de nivåer som är tillgängliga för standard fil resurser: transaktion optimerad, frekvent och låg frekvent.

<sup>2</sup> standard fil resurser är 5 TIB, se [Aktivera och skapa stora fil resurser](./storage-files-how-to-create-large-file-share.md) för information om hur du ökar standard fil resurserna skala upp till 100 TIB.

### <a name="file-scale-targets"></a>Fil skalnings mål
| Attribut | Filer i standard fil resurser  | Filer i Premium-filresurser  |
|-|-|-|
| Maximal filstorlek | 4 TiB | 4 TiB |
| Maximal frekvens för samtidiga begär Anden | 1 000 IOPS | Upp till 8 000<sup>1</sup> |
| Maximalt antal inkommande data för en fil | 60 MiB/SEK | 200 MiB/SEK (upp till 1 GiB/s med SMB Multichannel Preview)<sup>2</sup>|
| Högsta utgående för en fil | 60 MiB/SEK | 300 MiB/SEK (upp till 1 GiB/s med SMB Multichannel Preview)<sup>2</sup> |
| Maximalt antal samtidiga referenser | 2 000-referenser | 2 000-referenser  |

<sup>1 gäller för läsning och skrivning av iOS (vanligt vis mindre i/o-storlekar som är mindre än eller lika med 64 KiB). Metadata-åtgärder, förutom läsning och skrivningar, kan vara lägre.</sup> 
 <sup>2 omfattas av gränser för dator nätverk, tillgänglig bandbredd, IO-storlek, ködjup och andra faktorer. Mer information finns i [SMB Multichannel-prestanda](./storage-files-smb-multichannel-performance.md).</sup>

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync – skalningsmål
Följande tabell visar gränserna för Microsofts testning och indikerar även vilka mål som är hårda gränser:

| Resurs | Mål | Hård gräns |
|----------|--------------|------------|
| Tjänster för synkronisering av lagring per region | 100 lagrings tjänster för synkronisering | Ja |
| Synkronisera grupper per tjänst för synkronisering av lagring | 200 Sync-grupper | Ja |
| Registrerade servrar per tjänst för synkronisering av lagring | 99-servrar | Ja |
| Moln slut punkter per Sync-grupp | 1 moln slut punkt | Ja |
| Server slut punkter per Sync-grupp | 100 Server slut punkter | Ja |
| Server slut punkter per server | 30 Server slut punkter | Ja |
| Fil system objekt (kataloger och filer) per Sync-grupp | 100 000 000 objekt | Inga |
| Maximalt antal fil system objekt (kataloger och filer) i en katalog | 5 000 000 objekt | Ja |
| Säkerhets beskrivnings storlek för maximalt objekt (kataloger och filer) | 64 KiB | Ja |
| Filstorlek | 100 GiB | Inga |
| Minsta fil storlek för en fil i nivå | V9 och nyare: baserat på fil systemets kluster storlek (kluster storlek för dubbla fil system). Om fil systemets kluster storlek till exempel är 4 KiB, kommer den minsta fil storleken att vara 8 KiB.<br> V8 och äldre: 64 KiB  | Ja |

> [!Note]  
> En Azure File Sync-slutpunkt kan skala upp till storleken på en Azure-filresurs. Om storleks gränsen för Azure-filresursen uppnås går det inte att använda synkroniseringen.

### <a name="azure-file-sync-performance-metrics"></a>Azure File Sync – prestandamått
Eftersom Azure File Sync-agenten körs på en Windows Server-dator som ansluter till Azure-filresurser, beror den effektiva synkroniseringen på ett antal faktorer i infrastrukturen: Windows Server och den underliggande disk konfigurationen, nätverks bandbredden mellan servern och Azure-lagring, fil storlek, total data uppsättnings storlek och aktivitet i data uppsättningen. Eftersom Azure File Sync fungerar på filnivå, mäts prestanda egenskaperna för en Azure File Sync-baserad lösning bättre i antalet objekt (filer och kataloger) som bearbetas per sekund.

För Azure File Sync är prestanda kritisk i två steg:

1. **Första engångs etablering**: för att optimera prestanda vid inledande etablering, se [onboarding med Azure File Sync](storage-sync-files-deployment-guide.md#onboarding-with-azure-file-sync) för den optimala distributions informationen.
2. **Pågående synkronisering**: när data ursprungligen har dirigerats i Azure-filresurserna, kan Azure File Sync hålla flera slut punkter synkroniserade.

För att hjälpa dig att planera distributionen för var och en av stegen nedan är resultaten som observeras under den interna testningen på ett system med en konfiguration

| Systemkonfiguration | Information |
|-|-|
| Processor | 64 virtuella kärnor med 64 MiB L3-cache |
| Minne | 128 GiB |
| Disk | SAS-diskar med RAID 10 med batteriuppbackat cache-lagring |
| Nätverk | 1 Gbit/s nätverk |
| Arbetsbelastning | Generell användning fil Server|

| Första engångs etablering  | Information |
|-|-|
| Antal objekt | 25 000 000 objekt |
| Storlek på data uppsättning| ~ 4,7 TiB |
| Genomsnittlig fil storlek | ~ 200 KiB (största fil: 100 GiB) |
| Första moln ändrings uppräkning | 20 objekt per sekund  |
| Ladda upp data flöde | 20 objekt per sekund per Sync-grupp |
| Hämta data område | 400 objekt per sekund |

### <a name="initial-one-time-provisioning"></a>Första engångs etablering
Första **moln ändrings uppräkning**: när en ny synkroniseringsresurs skapas är den första omräkningen av moln ändringar det första steget som ska utföras. I den här processen kommer systemet att räkna upp alla objekt i Azure-filresursen. Under den här processen kommer det inte att finnas någon Sync-aktivitet, d.v.s. inga objekt hämtas från moln slut punkten till Server slut punkten och inga objekt överförs från Server slut punkten till moln slut punkten. Synkroniseringen återupptas när den första uppräkningen av moln ändringar slutförs.
Prestanda frekvensen är 20 objekt per sekund. Kunder kan uppskatta hur lång tid det tar att slutföra den inledande moln ändrings uppräkningen genom att fastställa antalet objekt i moln resursen och använda följande formel för att hämta tiden i dagar. 

   **Tid (i dagar) för inledande moln uppräkning = (antal objekt i moln slut punkt)/(20 * 60 * 60 * 24)**

**Hämta data område** När en ny server slut punkt läggs till i en befintlig Sync-grupp, laddas inte Azure File Sync-agenten ned något av fil innehållet från moln slut punkten. Den synkroniserar först det fullständiga namn området och utlöser sedan bakgrunden återkallande för att ladda ned filerna, antingen i sin helhet eller, om moln nivåer är aktiverade, till den moln nivå princip som angetts på Server slut punkten.

| Pågående synkronisering  | Information  |
|-|--|
| Antal synkroniserade objekt| 125 000 objekt (~ 1% omsättning) |
| Storlek på data uppsättning| 50 GiB |
| Genomsnittlig fil storlek | ~ 500 KiB |
| Ladda upp data flöde | 20 objekt per sekund per Sync-grupp |
| Fullständig hämtning av data flöde * | 60 objekt per sekund |

* Om moln nivån är aktive rad kommer du förmodligen att se bättre prestanda eftersom endast några av de fildata som hämtas hämtas. Azure File Sync hämtar endast data från cachelagrade filer när de ändras på någon av slut punkterna. För alla nivåbaserade eller nyligen skapade filer laddar inte agenten ned fildata och synkroniserar istället bara namn området till alla Server slut punkter. Agenten stöder också ofullständiga hämtningar av filer på nivå som används av användaren. 

> [!Note]  
> Siffrorna ovan är inte en indikation på den prestanda som du kommer att uppleva. Den faktiska prestandan beror på flera faktorer som beskrivs i början av det här avsnittet.

Som en allmän guide för din distribution bör du ha några saker i åtanke:

- Objekt data flödet ungefär skalar i proportion till antalet Sync-grupper på servern. Delning av data i flera Sync-grupper på en server ger bättre data flöde, som också begränsas av servern och nätverket.
- Objekt data flödet är inverterat i förhållande till MiB: s per sekund-genomflöde. För mindre filer kommer du att uppleva högre data flöde vad gäller antalet bearbetade objekt per sekund, men med lägre MiB per sekund genom strömning. För större filer får du färre objekt som bearbetas per sekund, men högre MiB-data flöde per sekund. Data flödet för MiB per sekund begränsas av Azure Files skala mål.

## <a name="see-also"></a>Se även
- [Planera för en Azure Files-distribution](storage-files-planning.md)
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)