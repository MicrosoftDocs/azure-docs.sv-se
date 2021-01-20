---
title: Skalbarhet för Azure Files och prestandamål
description: Lär dig mer om skalbarhets-och prestanda mål för Azure Files, inklusive kapacitet, begär ande frekvens och inkommande och utgående bandbredds begränsningar.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e10f45af89e19f6fe62ff729f96d870e008c96ec
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611108"
---
# <a name="azure-files-scalability-and-performance-targets"></a>Skalbarhet för Azure Files och prestandamål

[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via SMB-protokollet enligt bransch standard. I den här artikeln beskrivs skalbarhets-och prestanda mål för Azure Files och Azure File Sync.

De skalbarhets-och prestanda mål som anges här är avancerade mål, men kan påverkas av andra variabler i distributionen. Till exempel kan data flödet för en fil också begränsas av den tillgängliga nätverks bandbredden, inte bara de servrar som är värdar för tjänsten för Azure Files. Vi rekommenderar starkt att du testar ditt användnings mönster för att avgöra om skalbarheten och prestandan för Azure Files uppfyller dina krav. Vi strävar också efter att öka de här gränserna med tiden. Ovilliga inte att ge oss feedback, antingen i kommentarerna nedan eller på den [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files), om vilka gränser du vill se en ökning av USA.

## <a name="azure-storage-account-scale-targets"></a>Skalnings mål för Azure Storage-konto

Den överordnade resursen för en Azure-filresurs är ett Azure Storage-konto. Ett lagrings konto representerar en pool av lagrings utrymme i Azure som kan användas av flera lagrings tjänster, inklusive Azure Files, för att lagra data. Andra tjänster som lagrar data i lagrings konton är Azure Blob Storage, Azure Queue Storage och Azure Table Storage. Följande mål använder alla lagrings tjänster som lagrar data i ett lagrings konto:

[!INCLUDE [azure-storage-account-limits-standard](../../../includes/azure-storage-account-limits-standard.md)]

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

> [!Important]  
> Generell användning av lagrings konto användningen från andra lagrings tjänster påverkar dina Azure-filresurser i ditt lagrings konto. Om du till exempel når högsta kapacitet för lagrings konton med Azure Blob Storage kan du inte skapa nya filer på Azure-filresursen, även om din Azure-filresurs är lägre än den maximala resurs storleken.

## <a name="azure-files-scale-targets"></a>Azure Files – skalningsmål

Det finns tre typer av begränsningar att överväga för Azure Files: lagrings konton, resurser och filer.

Till exempel: med Premium-filresurser kan en enda resurs uppnå 100 000 IOPS och en enda fil kan skala upp till 5 000 IOPS. Så om du har tre filer i en resurs är det högsta antalet IOPS som du kan hämta från resursen 15 000.

### <a name="standard-storage-account-limits"></a>Standard gränser för lagrings konto

I avsnittet om [skalnings mål för Azure Storage-konto](#azure-storage-account-scale-targets) finns dessa gränser.

### <a name="premium-filestorage-account-limits"></a>Konto gränser för Premium-FileStorage

[!INCLUDE [azure-storage-limits-filestorage](../../../includes/azure-storage-limits-filestorage.md)]

> [!IMPORTANT]
> Lagrings konto gränser gäller för alla resurser. Det går bara att skala upp till max för FileStorage-konton om det bara finns en resurs per FileStorage-konto.

### <a name="file-share-and-file-scale-targets"></a>Fil resurs-och fil skalnings mål

> [!NOTE]
> Standard fil resurser som är större än 5 TiB har vissa begränsningar. En lista över begränsningar och instruktioner för att aktivera större fil resurs storlekar finns i avsnittet [Aktivera större fil resurser på standard fil resurser](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) i planerings guiden.

[!INCLUDE [storage-files-scale-targets](../../../includes/storage-files-scale-targets.md)]

[!INCLUDE [storage-files-premium-scale-targets](../../../includes/storage-files-premium-scale-targets.md)]

## <a name="azure-file-sync-scale-targets"></a>Azure File Sync – skalningsmål

Azure File Sync har utformats med målet obegränsad användning, men obegränsad användning är inte alltid möjlig. Följande tabell visar gränserna för Microsofts testning och indikerar även vilka mål som är hårda gränser:

[!INCLUDE [storage-sync-files-scale-targets](../../../includes/storage-sync-files-scale-targets.md)]

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
