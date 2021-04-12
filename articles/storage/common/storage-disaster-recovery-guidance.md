---
title: Haveriberedskap och lagringskontoredundans
titleSuffix: Azure Storage
description: Azure Storage stöder redundans för geo-redundanta lagrings konton. Med konto redundans kan du initiera redundansväxlingen för ditt lagrings konto om den primära slut punkten blir otillgänglig.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 11d9b38d71d428a3c6c829b508318389338f5a15
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800371"
---
# <a name="disaster-recovery-and-storage-account-failover"></a>Haveriberedskap och lagringskontoredundans

Microsoft strävar efter att se till att Azure-tjänster alltid är tillgängliga. Oplanerade drifts avbrott kan dock uppstå. Om ditt program kräver återhämtning rekommenderar Microsoft att använda Geo-redundant lagring, så att dina data kopieras till en andra region. Dessutom bör kunderna ha en katastrof återställnings plan för hantering av ett regionalt tjänst avbrott. En viktig del av en katastrof återställnings plan förbereder att redundansväxla till den sekundära slut punkten i händelse av att den primära slut punkten blir otillgänglig.

Azure Storage stöder redundans för geo-redundanta lagrings konton. Med konto redundans kan du initiera redundansväxlingen för ditt lagrings konto om den primära slut punkten blir otillgänglig. Redundansväxlingen uppdaterar den sekundära slut punkten för att bli den primära slut punkten för ditt lagrings konto. När redundansväxlingen är klar kan klienter börja skriva till den nya primära slut punkten.

Redundansväxling är tillgängligt för konton av typen generell användning v1, generell användning v2 och bloblagring med Azure Resource Manager-distributioner. Redundansväxling av kontot stöds för alla offentliga regioner men är inte tillgänglig i suveräna eller nationella moln för tillfället. Redundansväxling av konton stöds inte för lagrings konton med hierarkiskt namn område aktiverat.

Den här artikeln beskriver koncepten och processen som är inblandade i ett konto för redundans och beskriver hur du förbereder ditt lagrings konto för återställning med minsta möjliga mängd kund påverkan. Information om hur du startar en redundansväxling av ett konto i Azure Portal eller PowerShell finns i [initiera ett konto redundansväxling](storage-initiate-account-failover.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Välj rätt redundans alternativ

Azure Storage underhåller flera kopior av ditt lagrings konto för att säkerställa hållbarhet och hög tillgänglighet. Vilket alternativ för redundans som du väljer för ditt konto beror på vilken återhämtnings grad du behöver. För skydd mot regionala avbrott konfigurerar du ditt konto för Geo-redundant lagring, med eller utan alternativet Läs behörighet från den sekundära regionen:  

**Geo-redundant lagring (GRS) eller geo-Zone-redundant lagring (GZRS)** kopierar dina data asynkront i två geografiska regioner som är minst hundratals mil. Om den primära regionen drabbas av ett avbrott fungerar den sekundära regionen som en redundant källa för dina data. Du kan initiera en redundansväxling för att transformera den sekundära slut punkten till den primära slut punkten.

**Read-Access Geo-redundant lagring (RA-GRS) eller geo-Zone-redundant lagring med Läs behörighet (ra-GZRS)** tillhandahåller Geo-redundant lagring med ytterligare fördel av Läs behörighet till den sekundära slut punkten. Om ett avbrott uppstår i den primära slut punkten kan program som kon figurer ATS för Läs åtkomst till den sekundära och utformad för hög tillgänglighet fortsätta att läsa från den sekundära slut punkten. Microsoft rekommenderar RA-GZRS för maximal tillgänglighet och hållbarhet för dina program.

Mer information om redundans i Azure Storage finns [Azure Storage redundans](storage-redundancy.md).

> [!WARNING]
> Geo-redundant lagring medför risk för data förlust. Data kopieras till den sekundära regionen asynkront, vilket innebär att det uppstår en fördröjning mellan när data som skrivs till den primära regionen skrivs till den sekundära regionen. I händelse av ett avbrott går Skriv åtgärder till den primära slut punkten som ännu inte har kopierats till den sekundära slut punkten förlorade.

## <a name="design-for-high-availability"></a>Utforma för hög tillgänglighet

Det är viktigt att utforma ditt program för hög tillgänglighet från start. Se dessa Azure-resurser för att få hjälp med att utforma ditt program och planera för haveri beredskap:

- [Utforma elastiska program för Azure](/azure/architecture/framework/resiliency/app-design): en översikt över viktiga begrepp för att utforma program med hög tillgänglighet i Azure.
- [Check lista för återhämtning](/azure/architecture/checklist/resiliency-per-service): en check lista för att kontrol lera att ditt program implementerar bästa design praxis för hög tillgänglighet.
- [Använd GEO-redundans för att skapa program med hög tillgänglighet](geo-redundant-design.md): design rikt linjer för att skapa program för att dra nytta av Geo-redundant lagring.
- [Självstudie: skapa ett program med hög tillgänglighet med Blob Storage](../blobs/storage-create-geo-redundant-storage.md): en själv studie kurs som visar hur du skapar ett program med hög tillgänglighet som automatiskt växlar mellan slut punkter som fel och återställningar simuleras. 

Tänk också på följande rekommendationer för att upprätthålla hög tillgänglighet för dina Azure Storage data:

- **Diskar:** Använd [Azure Backup](https://azure.microsoft.com/services/backup/) för att säkerhetskopiera de virtuella dator diskar som används av dina virtuella Azure-datorer. Överväg också att använda [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) för att skydda dina virtuella datorer i händelse av en regional katastrof.
- **Blockera blobbar:** Aktivera [mjuk borttagning](../blobs/soft-delete-blob-overview.md) för att skydda mot borttagningar på objekt nivå och skriv över eller kopiera block-blobar till ett annat lagrings konto i en annan region [med AZCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)eller [Azure Data flyttnings bibliotek](storage-use-data-movement-library.md).
- **Filer:** Använd [Azure Backup](../../backup/azure-file-share-backup-overview.md) för att säkerhetskopiera dina fil resurser. Aktivera även [mjuk borttagning](../files/storage-files-prevent-file-share-deletion.md) för att skydda mot oavsiktlig fil resurs borttagningar. För GEO-redundans när GRS inte är tillgängligt använder du [AzCopy](./storage-use-azcopy-v10.md) eller [Azure PowerShell](/powershell/module/az.storage/) för att kopiera filerna till ett annat lagrings konto i en annan region.
- **Tabeller:** Använd [AzCopy](./storage-use-azcopy-v10.md) för att exportera tabell data till ett annat lagrings konto i en annan region.

## <a name="track-outages"></a>Spåra avbrott

Kunder kan prenumerera på [Azure Service Health instrument panel](https://azure.microsoft.com/status/) för att spåra hälso tillståndet och statusen för Azure Storage och andra Azure-tjänster.

Microsoft rekommenderar också att du utformar ditt program för att förbereda dig för möjlighet till skrivfel. Programmet bör exponera skrivfel på ett sätt som varnar dig om risken för avbrott i den primära regionen.

## <a name="understand-the-account-failover-process"></a>Förstå processen för redundans av konto

Med redundans för Kundhanterade konton kan du inte återställa hela lagrings kontot till den sekundära regionen om den primära inte är tillgänglig av någon anledning. När du tvingar fram en redundansväxling till den sekundära regionen kan klienterna börja skriva data till den sekundära slut punkten när redundansväxlingen är klar. Redundansväxlingen tar vanligt vis ungefär en timme.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

### <a name="how-an-account-failover-works"></a>Så här fungerar kontoredundans

Under normala omständigheter skriver en klient data till ett Azure Storage-konto i den primära regionen och dessa data kopieras asynkront till den sekundära regionen. Följande bild visar scenariot när den primära regionen är tillgänglig:

![Klienter skriver data till lagrings kontot i den primära regionen](media/storage-disaster-recovery-guidance/primary-available.png)

Om den primära slut punkten blir otillgänglig av någon anledning kan klienten inte längre skriva till lagrings kontot. Följande bild visar scenariot där den primära har blivit otillgänglig, men ingen återställning har skett än:

![Den primära är inte tillgänglig, så klienter kan inte skriva data](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

Kunden initierar redundansväxlingen av kontot till den sekundära slut punkten. Vid redundansväxlingen uppdateras DNS-posten från Azure Storage så att den sekundära slut punkten blir den nya primära slut punkten för ditt lagrings konto, vilket visas i följande bild:

![Kunden initierar konto redundans till en sekundär slut punkt](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

Skriv åtkomst återställs för geo-redundanta konton när DNS-posten har uppdaterats och begär Anden dirigeras till den nya primära slut punkten. Befintliga slut punkter för lagrings tjänster för blobbar, tabeller, köer och filer är oförändrade efter redundansväxlingen.

> [!IMPORTANT]
> När redundansväxlingen är klar konfigureras lagrings kontot för att vara lokalt redundant i den nya primära slut punkten. Om du vill återuppta replikeringen till den nya sekundära konfigurerar du kontot för GEO-redundans igen.
>
> Kom ihåg att om du konverterar ett LRS-konto för att använda GEO-redundans uppstår en kostnad. Den här kostnaden gäller för uppdatering av lagrings kontot i den nya primära regionen efter en redundansväxling.  

### <a name="anticipate-data-loss"></a>Vänta på data förlust

> [!CAUTION]
> En redundansväxling av ett konto innebär vanligt vis data förlust. Det är viktigt att förstå konsekvenserna av att initiera en konto redundansväxling.  

Eftersom data skrivs asynkront från den primära regionen till den sekundära regionen, finns det alltid en fördröjning innan en skrivning till den primära regionen kopieras till den sekundära regionen. Om den primära regionen blir otillgänglig kanske de senaste skrivningar ännu inte har kopierats till den sekundära regionen.

När du tvingar fram en redundansväxling försvinner alla data i den primära regionen eftersom den sekundära regionen blir den nya primära regionen och lagrings kontot är konfigurerat för att vara lokalt redundant. Alla data som redan har kopierats till den sekundära underhålls när redundansväxlingen sker. Data som skrivs till den primära som inte också har kopierats till den sekundära förloras dock permanent.

Egenskapen **senaste synkroniseringstid** anger den senaste tiden som data från den primära regionen garanterat har skrivits till den sekundära regionen. Alla data som skrivits före den senaste synkroniseringstid-tiden är tillgängliga på den sekundära, medan data som skrivs efter den senaste synkroniseringen kanske inte har skrivits till den sekundära och kan gå förlorade. Använd den här egenskapen i händelse av ett avbrott för att uppskatta mängden data förlust du kan stöta på genom att initiera ett konto redundansväxling.

Vi rekommenderar att du utformar ditt program så att du kan använda den senaste synkroniseringstid för att utvärdera förväntad data förlust. Om du till exempel loggar alla Skriv åtgärder kan du jämföra tiden för dina senaste Skriv åtgärder med den senaste synkroniseringen för att avgöra vilka skrivningar som inte har synkroniserats med den sekundära.

Mer information om hur du kontrollerar den **senaste synkroniseringstid** -egenskapen finns i [kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto](last-sync-time-get.md).

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Använd försiktighet när du växlar tillbaka till den ursprungliga primära

När du växlar över från den primära till den sekundära regionen konfigureras ditt lagrings konto för att vara lokalt redundant i den nya primära regionen. Sedan kan du konfigurera kontot för GEO-redundans igen. När kontot har kon figurer ATS för GEO-redundans igen efter en redundansväxling börjar den nya primära regionen omedelbart att kopiera data till den nya sekundära regionen, som var den primära före den ursprungliga redundansväxlingen. Det kan dock ta en stund innan befintliga data i den primära har kopierats fullständigt till den nya sekundära.

När lagrings kontot har kon figurer ATS om för GEO-redundans är det möjligt att initiera en annan redundansväxling från den nya primära tillbaka till den nya sekundära. I det här fallet blir den ursprungliga primära regionen före redundansväxlingen den primära regionen igen och är konfigurerad att vara lokalt redundant. Alla data i den primära regionen efter redundans (ursprunglig sekundär) går förlorade. Om de flesta data i lagrings kontot inte har kopierats till den nya sekundära platsen innan du växlar tillbaka kan du få en större data förlust.

Du kan undvika en större data förlust genom att kontrol lera värdet för den **senaste synkroniseringstid** -egenskapen innan du växlar tillbaka. Jämför den senaste synkroniseringen med de senaste tidpunkterna då data skrevs till den nya primära för att utvärdera förväntad data förlust. 

## <a name="initiate-an-account-failover"></a>Initiera en kontoredundans

Du kan starta ett konto vid fel från Azure Portal, PowerShell, Azure CLI eller Azure Storage Resource Provider API. Mer information om hur du initierar en redundansväxling finns i [initiera ett konto redundansväxling](storage-initiate-account-failover.md).

## <a name="additional-considerations"></a>Annat som är bra att tänka på

Granska ytterligare överväganden som beskrivs i det här avsnittet för att förstå hur dina program och tjänster kan påverkas när du tvingar fram en redundansväxling.

### <a name="storage-account-containing-archived-blobs"></a>Lagrings konto som innehåller arkiverade blobbar

Lagrings konton som innehåller arkiverade blobbar stöder redundans av konton. När redundansväxlingen är klar måste alla arkiverade blobbar rehydratiseras till en online-nivå innan kontot kan konfigureras för GEO-redundans.

### <a name="storage-resource-provider"></a>Lagringsresursprovider

När redundansväxlingen är klar kan klienterna läsa in och skriva Azure Storage data i den nya primära regionen. Azure Storage Resource providern växlar dock inte över, så resurs hanterings åtgärder måste ändå äga rum i den primära regionen. Om den primära regionen inte är tillgänglig går det inte att utföra hanterings åtgärder på lagrings kontot.

Eftersom Azure Storage Resource Provider inte växlar över, returnerar egenskapen [location](/dotnet/api/microsoft.azure.management.storage.models.trackedresource.location) den ursprungliga primära platsen när redundansväxlingen är klar.

### <a name="azure-virtual-machines"></a>Virtuella Azure-datorer

Virtuella Azure-datorer (VM) växlar inte över som en del av en redundansväxling av kontot. Om den primära regionen blir otillgänglig och du växlar över till den sekundära regionen måste du återskapa alla virtuella datorer efter redundansväxlingen. Det finns också en potentiell data förlust som är associerad med kontots redundans. Microsoft rekommenderar följande rikt linjer för [hög tillgänglighet](../../virtual-machines/availability.md) och [katastrof återställning](../../virtual-machines/backup-recovery.md) som är särskilt tillgängliga för virtuella datorer i Azure.

### <a name="azure-unmanaged-disks"></a>Azure-ohanterade diskar

Som bästa praxis rekommenderar Microsoft att du konverterar ohanterade diskar till hanterade diskar. Om du behöver redundansväxla ett konto som innehåller ohanterade diskar som är anslutna till virtuella Azure-datorer måste du dock stänga av den virtuella datorn innan du initierar redundansväxlingen.

Ohanterade diskar lagras som Page blobbar i Azure Storage. När en virtuell dator körs i Azure, lånas alla ohanterade diskar som är anslutna till den virtuella datorn. Det går inte att fortsätta med redundansväxlingen när det finns ett lån på en blob. Följ dessa steg om du vill utföra redundansväxlingen:

1. Innan du börjar noterar du namnen på eventuella ohanterade diskar, deras logiska enhets nummer (LUN) och den virtuella dator som de är kopplade till. På så sätt blir det enklare att återansluta diskarna efter redundansväxlingen.
2. Stäng av den virtuella datorn.
3. Ta bort den virtuella datorn, men behåll VHD-filerna för de ohanterade diskarna. Observera när du tog bort den virtuella datorn.
4. Vänta tills den **senaste synkroniseringstid-tiden** har uppdaterats och är senare än den tid då du tog bort den virtuella datorn. Det här steget är viktigt, eftersom om den sekundära slut punkten inte har uppdaterats fullständigt med VHD-filerna när redundansväxlingen inträffar, kanske den virtuella datorn inte fungerar korrekt i den nya primära regionen.
5. Initiera redundansväxlingen av kontot.
6. Vänta tills kontots redundans är klart och att den sekundära regionen har blivit den nya primära regionen.
7. Skapa en virtuell dator i den nya primära regionen och återanslut de virtuella hård diskarna.
8. Starta den nya virtuella datorn.

Tänk på att data som lagras på en temporär disk förloras när den virtuella datorn stängs av.

## <a name="unsupported-features-and-services"></a>Funktioner och tjänster som inte stöds

Följande funktioner och tjänster stöds inte för redundans av konto:

- Azure File Sync stöder inte redundans för lagrings konto. Lagringskonton med Azure-filresurser som används som molnslutpunkter i Azure File Sync får inte redundansväxlas. Om du gör det slutar synkroniseringen att fungera och det kan leda till oväntade dataförluster för nyligen nivåbaserade filer.
- ADLS Gen2 lagrings konton (konton med hierarkiskt namn område aktiverat) stöds inte för tillfället.
- Det går inte att redundansväxla ett lagrings konto som innehåller Premium block-blobar. Lagrings konton som stöder Premium block-blobbar har för närvarande inte stöd för GEO-redundans.
- Det går inte att redundansväxla ett lagrings konto som innehåller oföränderlighets-aktiverade behållare för [Worm-principer](../blobs/storage-blob-immutable-storage.md) . Olåst/låst tidsbaserad kvarhållning eller principer för juridiskt bevarande förhindrar redundans för att bibehålla kompatibiliteten.

## <a name="copying-data-as-an-alternative-to-failover"></a>Kopiera data som ett alternativ till redundans

Om ditt lagrings konto har kon figurer ATS för Läs åtkomst till den sekundära kan du utforma ditt program för att läsa från den sekundära slut punkten. Om du föredrar att inte redundansväxla vid ett avbrott i den primära regionen kan du använda verktyg som [AzCopy](./storage-use-azcopy-v10.md), [Azure PowerShell](/powershell/module/az.storage/)eller [Azure Data flyttnings bibliotek](../common/storage-use-data-movement-library.md) för att kopiera data från ditt lagrings konto i den sekundära regionen till ett annat lagrings konto i en ej påverkad region. Du kan sedan peka dina program till det lagrings kontot för både Läs-och skriv tillgänglighet.

> [!CAUTION]
> En växling vid fel ska inte användas som en del av din strategi för datamigrering.

## <a name="microsoft-managed-failover"></a>Microsoft-hanterad redundans

I extrema fall där en region försvinner på grund av en betydande katastrof kan Microsoft initiera en regional redundansväxling. I det här fallet krävs ingen åtgärd på din del. Du har inte skriv åtkomst till ditt lagrings konto förrän den Microsoft-hanterade redundansväxlingen har slutförts. Dina program kan läsa från den sekundära regionen om ditt lagrings konto har kon figurer ATS för RA-GRS eller RA-GZRS.

## <a name="see-also"></a>Se även

- [Använd GEO-redundans för att skapa program med hög tillgänglighet](geo-redundant-design.md)
- [Initiera en kontoredundans](storage-initiate-account-failover.md)
- [Kontrol lera den senaste synkroniseringstid-egenskapen för ett lagrings konto](last-sync-time-get.md)
- [Självstudie: Bygg ett program med hög tillgänglighet med Blob Storage](../blobs/storage-create-geo-redundant-storage.md)