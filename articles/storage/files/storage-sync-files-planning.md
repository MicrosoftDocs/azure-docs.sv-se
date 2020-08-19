---
title: Planera för distribution av Azure File Sync | Microsoft Docs
description: Planera för en distribution med Azure File Sync, en tjänst som gör att du kan cachelagra ett antal Azure-filresurser på en lokal Windows Server eller virtuell dator i molnet.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: c2d3237e629c7ed5d2931e15939b154e0239f259
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553115"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planera för distribution av Azure File Sync

:::row:::
    :::column:::
        [![Intervju och demo introduktion till Azure File Sync – Klicka för att spela upp!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync är en tjänst som gör att du kan cachelagra ett antal Azure-filresurser på en lokal Windows Server eller virtuell dator i molnet. 
        
        I den här artikeln beskrivs hur du Azure File Sync koncept och funktioner. När du är bekant med Azure File Sync bör du tänka på följande [Azure File Sync distributions guide](storage-sync-files-deployment-guide.md) för att prova den här tjänsten.        
    :::column-end:::
:::row-end:::

Filerna kommer att lagras i molnet i [Azure-filresurser](storage-files-introduction.md). Azure-filresurser kan användas på två sätt: genom att direkt montera dessa Server lös Azure-filresurser (SMB) eller genom att cachelagra Azure-filresurser lokalt med Azure File Sync. Vilket distributions alternativ du väljer ändrar de aspekter du behöver tänka på när du planerar för distributionen. 

- **Direkt montering av en Azure-fil resurs**: eftersom Azure Files ger SMB-åtkomst kan du montera Azure-filresurser lokalt eller i molnet med hjälp av standard-SMB-klienten som är tillgänglig i Windows, MacOS och Linux. Eftersom Azure-filresurser är utan server, behöver distributionen för produktions scenarier inte hantera en fil server eller NAS-enhet. Det innebär att du inte behöver tillämpa program varu korrigeringar eller byta ut fysiska diskar. 

- **Cachelagra Azure-filresurser lokalt med Azure File Sync**: Azure File Sync gör det möjligt att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten för en lokal fil server. Azure File Sync transformerar en lokal (eller moln) Windows Server till ett snabbt cacheminne för Azure-filresursen. 

## <a name="management-concepts"></a>Hanterings begrepp
En Azure File Sync distribution har tre grundläggande hanterings objekt:

- **Azure-fil resurs**: en Azure-filresurs är en moln fil resurs utan server, som tillhandahåller *moln slut punkten* för en Azure File Sync synkroniseringsrelation. Filer i en Azure-filresurs kan nås direkt med SMB eller det fileraste protokollet, men vi rekommenderar att du huvudsakligen kommer åt filerna via Windows Server-cachen när Azure-filresursen används med Azure File Sync. Detta beror på att Azure Files idag saknar en effektiv mekanism för ändrings identifiering som Windows Server har, så att ändringar i Azure-filresursen direkt tar tid att sprida tillbaka till Server slut punkterna.
- **Server slut punkt**: sökvägen på Windows-servern som synkroniseras med en Azure-filresurs. Detta kan vara en speciell mapp på en volym eller volymens rot. Flera Server slut punkter kan finnas på samma volym om deras namn rymder inte överlappar varandra.
- **Sync-grupp**: objektet som definierar den synkroniserade relationen mellan en **moln slut punkt**eller Azure-filresurs och en server slut punkt. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. Om du till exempel har två distinkta uppsättningar med filer som du vill hantera med Azure File Sync skapar du två synkroniserade grupper och lägger till olika slut punkter i varje synkroniseringsresurs.

### <a name="azure-file-share-management-concepts"></a>Hanterings koncept för Azure-filresurs
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Principer för Azure File Sync hantering
Sync-grupper distribueras till **Storage Sync-tjänster**, som är toppnivå objekt som registrerar servrar som ska användas med Azure File Sync och innehåller grupp relationerna. Tjänsten Storage Sync service är en peer med lagrings konto resursen och kan distribueras på samma sätt till Azure-resurs grupper. En tjänst för synkronisering av lagring kan skapa synkroniserade grupper som innehåller Azure-filresurser över flera lagrings konton och flera registrerade Windows-servrar.

Innan du kan skapa en Sync-grupp i en tjänst för synkronisering av lagring måste du först registrera en Windows-Server med tjänsten för synkronisering av lagring. Detta skapar ett **registrerat Server** objekt som representerar en förtroende relation mellan servern eller klustret och tjänsten för synkronisering av lagring. Om du vill registrera en tjänst för synkronisering av lagring måste du först installera Azure File Sync-agenten på-servern. En enskild server eller ett kluster kan bara registreras med en lagrings tjänst för synkronisering i taget.

En Sync-grupp innehåller en moln slut punkt, en Azure-filresurs och minst en server slut punkt. Serverns slut punkts objekt innehåller de inställningar som konfigurerar kapaciteten för **moln nivåer** , som tillhandahåller cachelagring-funktionen för Azure File Sync. För att synkronisera med en Azure-filresurs måste lagrings kontot som innehåller Azure-filresursen finnas i samma Azure-region som tjänsten för synkronisering av lagring.

### <a name="management-guidance"></a>Vägledning för hantering
När du distribuerar Azure File Sync rekommenderar vi att:

- Distribuera Azure File-resurser 1:1 med Windows-filresurser. Med serverns slut punkt objekt får du en stor flexibilitet för hur du ställer in topologin på Server sidan i den synkroniserade relationen. För att förenkla hanteringen ska du göra sökvägen till Server slut punkten matcha sökvägen till Windows-filresursen. 

- Använd så få tjänster som möjligt för Storage-synkronisering. Detta fören klar hanteringen när du har synkronisera grupper som innehåller flera Server slut punkter, eftersom en Windows Server bara kan registreras till en lagrings tjänst för synkronisering i taget. 

- Betala till ett lagrings kontos IOPS-begränsningar när du distribuerar Azure-filresurser. Vi rekommenderar att du mappar fil resurser 1:1 med lagrings konton, men det kanske inte alltid är möjligt på grund av olika begränsningar och begränsningar, både från din organisation och från Azure. Om det inte går att ha en enda fil resurs som har distribuerats i ett lagrings konto bör du överväga vilka resurser som ska vara hög aktiva och vilka resurser som är mindre aktiva för att säkerställa att de hetaste fil resurserna inte placeras i samma lagrings konto tillsammans.

## <a name="windows-file-server-considerations"></a>Windows fil Server-överväganden
Om du vill aktivera Sync-funktionen på Windows Server måste du installera den Azure File Sync nedladdnings bara agenten. Azure File Sync agenten innehåller två huvud komponenter: `FileSyncSvc.exe` , den bakgrunds fönster tjänst som ansvarar för att övervaka ändringar på Server slut punkter och initiera svarssessioner, och `StorageSync.sys` ett fil system filter som aktiverar moln nivåer och snabb haveri beredskap.  

### <a name="operating-system-requirements"></a>Operativsystemskrav
Azure File Sync stöds med följande versioner av Windows Server:

| Version | SKU: er som stöds | Distributions alternativ som stöds |
|---------|----------------|------------------------------|
| Windows Server 2019 | Data Center, standard och IoT | Full och Core |
| Windows Server 2016 | Data Center, standard och lagrings Server | Full och Core |
| Windows Server 2012 R2 | Data Center, standard och lagrings Server | Full och Core |

Framtida versioner av Windows Server kommer att läggas till när de släpps.

> [!Important]  
> Vi rekommenderar att du behåller alla servrar som du använder med Azure File Sync uppdaterad med de senaste uppdateringarna från Windows Update. 

### <a name="minimum-system-resources"></a>Minsta system resurser
Azure File Sync kräver en server, antingen fysisk eller virtuell, med minst en processor och minst 2 GiB minne.

> [!Important]  
> Om servern körs på en virtuell dator med dynamiskt minne aktiverat ska den virtuella datorn konfigureras med minst 2048 MiB-minne.

För de flesta produktions arbets belastningar rekommenderar vi inte att du konfigurerar en Azure File Sync Sync-server med bara minimi kraven. Se [rekommenderade system resurser](#recommended-system-resources) för mer information.

### <a name="recommended-system-resources"></a>Rekommenderade system resurser
Precis som alla Server funktioner eller program bestäms system resurs kraven för Azure File Sync av distributionens skala. större distributioner på en server kräver större system resurser. För Azure File Sync bestäms skalningen av antalet objekt över Server slut punkter och omsättningen på data uppsättningen. En enskild server kan ha Server slut punkter i flera Sync-grupper och antalet objekt som anges i följande tabell konton för det fullständiga namn område som en server är kopplad till. 

Till exempel Server slut punkt A med 10 000 000 objekt + Server slut punkt B med 10 000 000 objekt = 20 000 000-objekt. För den här exempel distributionen rekommenderar vi 8 processorer, 16 GiB minne för stabilt tillstånd och (om möjligt) 48 GiB minne för den första migreringen.
 
Namn områdes data lagras i minnet av prestanda skäl. På grund av detta kräver större namn rymder mer minne för att upprätthålla bästa prestanda och mer omsättning kräver mer processor kraft för att bearbeta. 
 
I följande tabell har vi tillhandahållit både storleken på namn området och en konvertering till kapacitet för vanliga fil resurser i generella syften, där den genomsnittliga fil storleken är 512 KiB. Om fil storlekarna är mindre bör du överväga att lägga till ytterligare minne för samma mängd kapacitet. Basera minnes konfigurationen på storleken på namn området.

| Storlek på namnrymd – filer & kataloger (miljoner)  | Typisk kapacitet (TiB)  | PROCESSOR kärnor  | Rekommenderat minne (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (ursprunglig synkronisering)/2 (typisk omsättning)      |
| 5        | 2.3     | 2        | 16 (ursprunglig synkronisering)/4 (typisk omsättning)    |
| 10       | 4,7     | 4        | 32 (ursprunglig synkronisering)/8 (typisk omsättning)   |
| 30       | 14,0    | 8        | 48 (ursprunglig synkronisering)/16 (typisk omsättning)   |
| 50       | 23,3    | 16       | 64 (ursprunglig synkronisering)/32 (typisk omsättning)  |
| 100 *     | 46,6    | 32       | 128 (ursprunglig synkronisering)/32 (typisk omsättning)  |

\*Synkronisering av fler än 100 000 000 filer & kataloger rekommenderas inte för tillfället. Detta är en mjuk gräns som baseras på våra testade tröskelvärden. Mer information finns i [Azure Files skalbarhets-och prestanda mål](storage-files-scale-targets.md#azure-file-sync-scale-targets).

> [!TIP]
> Inledande synkronisering av ett namn område är en intensiv åtgärd och vi rekommenderar att du allokerar mer minne tills den första synkroniseringen har slutförts. Detta är inte obligatoriskt, men det kan påskynda den inledande synkroniseringen. 
> 
> Typisk omsättning är 0,5% av namn området som ändras per dag. Överväg att lägga till mer processor för högre omsättnings nivåer. 

- En lokalt ansluten volym som är formaterad med NTFS-filsystemet.

### <a name="evaluation-cmdlet"></a>Utvärderings-cmdlet
Innan du distribuerar Azure File Sync bör du utvärdera om den är kompatibel med systemet med hjälp av Azure File Sync Evaluation-cmdleten. Denna cmdlet söker efter eventuella problem med fil systemet och data uppsättningen, till exempel tecken som inte stöds eller en operativ system version som inte stöds. Kontrollerna avser de flesta men inte alla funktioner som nämns nedan. Vi rekommenderar att du läser igenom resten av det här avsnittet noggrant för att se till att distributionen går smidigt. 

Du kan installera utvärderings-cmdleten genom att installera AZ PowerShell-modulen, som kan installeras genom att följa anvisningarna här: [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Användning  
Du kan starta utvärderings verktyget på ett par olika sätt: du kan utföra system kontroller, data uppsättnings kontroller eller båda. Så här utför du både system-och data uppsättnings kontroller: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Så här testar du endast din data uppsättning:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Så här testar du system kraven:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Så här visar du resultatet i CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path
    C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Filsystemkompatibilitet
Azure File Sync stöds endast i direktansluten NTFS-volymer. Direct Attached Storage eller DAS på Windows Server innebär att operativ systemet Windows Server äger fil systemet. DAS kan tillhandahållas genom att fysiskt bifoga diskar till fil servern, ansluta virtuella diskar till en virtuell fil server (till exempel en virtuell dator som finns i Hyper-V) eller till och med via ISCSI.

Endast NTFS-volymer stöds. ReFS, FAT, FAT32 och andra fil system stöds inte.

I följande tabell visas interop-tillstånd för NTFS-fil system funktioner: 

| Funktion | Supportstatus | Kommentarer |
|---------|----------------|-------|
| Åtkomstkontrollistor (ACL) | Fullt stöd | Windows-typ Discretionary Access Control Lists bevaras av Azure File Sync och verkställs av Windows Server på Server slut punkter. ACL: er kan också tillämpas när du monterar Azure-filresursen direkt, men detta kräver ytterligare konfiguration. Mer information finns i [avsnittet om identiteter](#identity) . |
| Hårda länkar | Överhoppad | |
| Symboliska länkar | Överhoppad | |
| Monterings punkter | Stöds delvis | Monterings punkter kan vara roten i en server slut punkt, men de hoppas över om de finns i en server slut punkts namnrymd. |
| Knut punkter | Överhoppad | Till exempel Distributed File System DfrsrPrivate-och DFSRoots-mappar. |
| Referenspunkter | Överhoppad | |
| NTFS-komprimering | Fullt stöd | |
| Sparse-filer | Fullt stöd | Synkronisering av sparse-filer (blockeras inte), men synkroniserar till molnet som en fullständig fil. Om fil innehållet ändras i molnet (eller på en annan server) är filen inte längre sparse när ändringen laddas ned. |
| Alternativa data strömmar (ADS) | Konserverat, men inte synkroniserat | Till exempel synkroniseras inte klassificerings etiketter som skapats av fil klassificerings infrastrukturen. Befintliga klassificerings etiketter på filer på alla Server slut punkter lämnas orörda. |

<a id="files-skipped"></a>Azure File Sync kommer också att hoppa över vissa temporära filer och systemmappar:

| Fil/mapp | Anteckning |
|-|-|
| pagefile.sys | Filinformation till system |
| Desktop.ini | Filinformation till system |
| tummes. db | Temporär fil för miniatyrer |
| ehthumbs. db | Temporär fil för medie miniatyrer |
| ~$\*.\* | Tillfällig Office-fil |
| \*. tmp | Temporär fil |
| \*.laccdb | Lås fil för åtkomst databasen|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern Sync-fil|
| \\System volym information | Mapp som är speciell för volym |
| $RECYCLE. PLATS| Mapp |
| \\SyncShareState | Mapp för synkronisering |

### <a name="failover-clustering"></a>Redundanskluster
Windows Server-redundanskluster stöds av Azure File Sync för distributions alternativet "fil server för allmän användning". Redundanskluster stöds inte på Skalbar filserver för program data (SOFS) eller på klusterdelade volymer (CSV: er).

> [!Note]  
> Azure File Sync agenten måste installeras på varje nod i ett redundanskluster för att synkroniseringen ska fungera korrekt.

### <a name="data-deduplication"></a>Datadeduplicering
**Windows Server 2016 och Windows Server 2019**   
Datadeduplicering stöds på volymer med moln nivåer som är aktiverade på Windows Server 2016 och Windows Server 2019. Genom att aktivera datadeduplicering på en volym med aktive rad moln nivå kan du cachelagra fler filer lokalt utan att tillhandahålla mer lagrings utrymme. 

När datadeduplicering har Aktiver ATS på en volym med aktive rad moln nivå, kommer deduplicering av optimerade filer på serverns slut punkt att på samma sätt som en normal fil baserat på princip inställningarna för moln skiktet. När de deduplicerade filerna har flyttats, körs skräp insamlings jobbet för datadeduplicering automatiskt för att frigöra disk utrymme genom att ta bort onödiga segment som inte längre refereras till av andra filer på volymen.

Observera att volym besparingarna gäller endast för servern. dina data i Azure-filresursen kommer inte att dedupliceras.

> [!Note]  
> För att stödja datadeduplicering på volymer med moln skiktning aktiverat på Windows Server 2019 måste Windows Update [KB4520062](https://support.microsoft.com/help/4520062) installeras och Azure File Sync agent version 9.0.0.0 eller senare krävs.

**Windows Server 2012 R2**  
Azure File Sync stöder inte datadeduplicering och moln nivåer på samma volym på Windows Server 2012 R2. Om datadeduplicering har Aktiver ATS på en volym måste moln nivån vara inaktive rad. 

**Kommentarer**
- Om datadeduplicering installeras innan du installerar Azure File Sync agent krävs en omstart för att stödja datadeduplicering och moln nivåer på samma volym.
- Om datadeduplicering har Aktiver ATS på en volym efter att moln nivån har Aktiver ATS optimerar optimerings jobbet för den inledande dedupliceringen filer på volymen som inte redan är i nivå och kommer att ha följande påverkan på moln nivåer:
    - Principen för ledigt utrymme kommer att fortsätta att göra filer på nivå av filer efter det lediga utrymmet på volymen med hjälp av termisk karta.
    - Datum policyn hoppar över nivåer av filer som annars kan ha kvalificerats för lagrings nivåer på grund av att optimerings jobbet för deduplicering har åtkomst till filerna.
- För pågående optimerings jobb för deduplicering kommer moln nivåer med datum policyn att förskjutas av [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) -inställningen för datadeduplicering, om filen inte redan är i nivå. 
    - Exempel: om MinimumFileAgeDays-inställningen är sju dagar och den datum policyn för molnnivå är 30 dagar, kommer datum principen att nivånamn efter 37 dagar.
    - Obs! när en fil har en nivå av Azure File Sync hoppar optimerings jobbet för deduplicering över filen.
- Om en server som kör Windows Server 2012 R2 med Azure File Sync-agenten installerad uppgraderas till Windows Server 2016 eller Windows Server 2019, måste följande steg utföras för att stödja datadeduplicering och moln nivåer på samma volym:  
    - Avinstallera Azure File Sync-agenten för Windows Server 2012 R2 och starta om servern.
    - Hämta Azure File Sync agent för den nya versionen av serveroperativ systemet (Windows Server 2016 eller Windows Server 2019).
    - Installera Azure File Sync agent och starta om servern.  
    
    Obs! Azure File Sync konfigurations inställningar på servern bevaras när agenten avinstalleras och installeras om.

### <a name="distributed-file-system-dfs"></a>Distributed File System (DFS)
Azure File Sync stöder interop med DFS-namnområden (DFS-N) och DFS Replication (DFS-R).

**DFS-namnrymder (DFS-n)**: Azure File Sync stöds fullt ut på DFS-n-servrar. Du kan installera Azure File Sync agenten på en eller flera DFS-N-medlemmar för att synkronisera data mellan server slut punkterna och moln slut punkten. Mer information finns i [Översikt över DFS-namnrymder](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**DFS Replication (DFS-r)**: eftersom DFS-r och Azure File Sync båda är lösningar för replikering rekommenderar vi i de flesta fall att du ersätter DFS-R med Azure File Sync. Det finns dock flera scenarier där du vill använda DFS-R och Azure File Sync tillsammans:

- Du migrerar från en DFS-R-distribution till en Azure File Sync-distribution. Mer information finns i [Migrera en DFS Replication (DFS-R) distribution till Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Alla lokala servrar som behöver en kopia av dina fildata kan vara anslutna direkt till Internet.
- Filial servrar konsoliderar data till en enda hubb-server som du vill använda Azure File Sync.

För att Azure File Sync och DFS-R ska fungera sida vid sida:

1. Azure File Sync moln nivåer måste inaktive ras på volymer med replikerade DFS-R-mappar.
2. Server slut punkter ska inte konfigureras i mappar för skrivskyddad DFS-R-replikering.

Mer information finns i [DFS Replication översikt](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Att använda Sysprep på en server där Azure File Sync-agenten är installerad stöds inte och kan leda till oväntade resultat. Agent installation och Server registrering bör ske när du har distribuerat Server avbildningen och slutfört Sysprep-miniinstallationsprogrammet.

### <a name="windows-search"></a>Windows-sök
Om moln skiktning är aktiverat på en server slut punkt hoppas filer som skiktas över och inte indexeras av Windows Search. Filer som inte är på en nivå indexeras korrekt.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andra HSM-lösningar (hierarkisk lagrings hantering)
Inga andra HSM-lösningar ska användas med Azure File Sync.

## <a name="identity"></a>Identitet
Azure File Sync fungerar med din standard-AD-baserade identitet utan särskilda inställningar utöver att konfigurera synkronisering. När du använder Azure File Sync är den allmänna förväntan att de flesta åtkomst går igenom Azure File Sync caching-servrar i stället för via Azure-filresursen. Eftersom Server slut punkterna finns på Windows Server, och Windows Server har stöd för AD-och Windows-ACL: er under en längre tid, behövs ingenting utöver att se till att de Windows-filservrar som är registrerade hos synkroniseringstjänsten för lagring är domänanslutna. Azure File Sync kommer att lagra ACL: er på filerna i Azure-filresursen och replikera dem till alla Server slut punkter.

Även om ändringar som görs direkt till Azure-filresursen tar längre tid att synkronisera till serverns slut punkter i Sync-gruppen, kanske du också vill se till att du kan genomdriva dina AD-behörigheter på fil resursen direkt i molnet. För att göra detta måste du domän ansluta till ditt lagrings konto till din lokala AD, precis som hur dina Windows-filservrar är domänanslutna. Mer information om domän anslutning till ditt lagrings konto till ett kundägda Active Directory finns i [Azure Files Active Directory översikt](storage-files-active-directory-overview.md).

> [!Important]  
> Det krävs ingen domän som ansluter till ditt lagrings konto Active Directory för att kunna distribuera Azure File Sync. Detta är ett strikt valfritt steg som gör att Azure-filresursen kan upprätthålla lokala ACL: er när användare monterar Azure-filresursen direkt.

## <a name="networking"></a>Nätverk
Azure File Sync-agenten kommunicerar med lagrings tjänsten för synkronisering och Azure-filresursen med hjälp av Azure File Sync REST-protokollet och det protokoll som används, och båda använder alltid HTTPS via port 443. SMB används aldrig för att ladda upp eller ladda ned data mellan Windows Server och Azure-filresursen. Eftersom de flesta organisationer tillåter HTTPS-trafik över port 443 krävs det vanligt vis inte någon särskild nätverks konfiguration för att kunna distribuera Azure File Sync.

Baserat på din organisations policy eller unika myndighets krav kan du behöva mer begränsad kommunikation med Azure, och därför Azure File Sync tillhandahålla flera mekanismer för att konfigurera nätverk. Utifrån dina krav kan du:

- Synkronisering av tunnlar och fil överföring/Ladda ned trafik över din ExpressRoute eller Azure VPN. 
- Använd Azure Files och funktioner i Azure-nätverk som tjänst slut punkter och privata slut punkter.
- Konfigurera Azure File Sync som stöder proxyservern i din miljö.
- Begränsa nätverks aktivitet från Azure File Sync.

Mer information om Azure File Sync och nätverk finns i [Azure File Sync nätverks överväganden](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Kryptering
När du använder Azure File Sync finns det tre olika krypterings lager som du bör tänka på: kryptering i den andra lagringen av Windows Server, kryptering under överföring mellan Azure File Sync agent och Azure och kryptering i resten av dina data i Azure-filresursen. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server-kryptering i vila 
Det finns två strategier för att kryptera data på Windows Server som fungerar normalt med Azure File Sync: kryptering under fil systemet, så att fil systemet och alla data som skrivs till den krypteras, och kryptering i själva fil formatet. Dessa metoder är inte ömsesidigt uteslutande. de kan användas tillsammans om det behövs eftersom krypterings syftet är annorlunda.

Windows Server tillhandahåller BitLocker-inkorg för att tillhandahålla kryptering under fil systemet. BitLocker är helt transparent för Azure File Sync. Den främsta anledningen till att använda en krypterings funktion som BitLocker är att förhindra fysisk exfiltrering av data från ditt lokala data Center genom att stjäla diskarna och förhindra inläsning av obehörigt operativ system för att utföra obehörig läsning/skrivning till dina data. Mer information om BitLocker finns i [Översikt över BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Produkter från tredje part som fungerar på liknande sätt som BitLocker, i de hamnar under NTFS-volymen, bör fungera på samma sätt som i sin helhet med Azure File Sync. 

Den andra huvudsakliga metoden för att kryptera data är att kryptera filens data ström när programmet sparar filen. Vissa program kan göra detta internt, men det är vanligt vis inte fallet. Ett exempel på en metod för kryptering av filens data ström är Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Den främsta anledningen till att använda en krypterings funktion som AIP/RMS är att förhindra data exfiltrering data från din fil resurs genom att kopiera den till alternativa platser, till exempel till en flash-enhet eller skicka e-post till en obehörig person. När en fils data ström är krypterad som en del av fil formatet kommer den här filen fortfarande att vara krypterad på Azure-filresursen. 

Azure File Sync fungerar inte med NTFS EFS (NTFS Encrypted File System) eller krypterings lösningar från tredje part som är ovanför fil systemet men under filens data ström. 

### <a name="encryption-in-transit"></a>Kryptering under överföring

> [!NOTE]
> Azure File Syncs tjänsten kommer att ta bort stöd för TLS 1.0 och 1,1 den 1 augusti 2020. Alla Azure File Sync agent versioner som stöds använder redan TLS 1.2 som standard. Användning av en tidigare version av TLS kan uppstå om TLS 1.2 inaktiverades på servern eller om en proxyserver används. Om du använder en proxyserver rekommenderar vi att du kontrollerar proxykonfigurationen. Azure File Sync service regioner som läggs till efter 5/1/2020 endast stöder TLS 1.2 och stöd för TLS 1.0 och 1,1 kommer att tas bort från befintliga regioner den 1 augusti 2020.  Mer information finns i [fel söknings guiden](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync).

Azure File Sync-agenten kommunicerar med lagrings tjänsten för synkronisering och Azure-filresursen med hjälp av Azure File Sync REST-protokollet och det protokoll som används, och båda använder alltid HTTPS via port 443. Azure File Sync skickar inte okrypterade begär Anden via HTTP. 

Azure Storage-konton innehåller en växel för att kräva kryptering vid överföring, som är aktiverat som standard. Även om växeln på lagrings konto nivån är inaktive rad, innebär det att okrypterade anslutningar till dina Azure-filresurser är möjliga, Azure File Sync fortfarande bara använder krypterade kanaler för åtkomst till fil resursen.

Den primära anledningen till att inaktivera kryptering vid överföring av lagrings kontot är att stödja ett äldre program som måste köras på ett äldre operativ system, till exempel Windows Server 2008 R2 eller äldre Linux-distribution, som kommunicerar med en Azure-filresurs direkt. Om det äldre programmet pratar med Windows Server-cachen för fil resursen, har den här inställningen ingen inverkan. 

Vi rekommenderar starkt att du ser till att kryptering av data överförs är aktiverat.

Mer information om kryptering i överföring finns i [krav på säker överföring i Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Azure File Share-kryptering i vila
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Lagringsnivåer
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Aktivera standard fil resurser för att täcka upp till 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regional tillgänglighet
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Tillgänglighet för Azure File Sync-regioner
Azure File Sync är tillgängligt i följande regioner:

| Azure-molnet | Geografisk region | Azure-region | Regions kod |
|-------------|-------------------|--------------|-------------|
| Offentlig | Asien | Asien, östra | `eastasia` |
| Offentlig | Asien | Sydostasien | `southeastasia` |
| Offentlig | Australien | Australien, östra | `australiaeast` |
| Offentlig | Australien | Australien, sydöstra | `australiasoutheast` |
| Offentlig | Brasilien | Brasilien, södra | `brazilsouth` |
| Offentlig | Kanada | Kanada, centrala | `canadacentral` |
| Offentlig | Kanada | Kanada, östra | `canadaeast` |
| Offentlig | Europa | Norra Europa | `northeurope` |
| Offentlig | Europa | Europa, västra | `westeurope` |
| Offentlig | Frankrike | Frankrike, centrala | `francecentral` |
| Offentlig | Frankrike | Frankrike, södra * | `francesouth` |
| Offentlig | Indien | Indien, centrala | `centralindia` |
| Offentlig | Indien | Indien, södra | `southindia` |
| Offentlig | Japan | Japan, östra | `japaneast` |
| Offentlig | Japan | Japan, västra | `japanwest` |
| Offentlig | Korea | Sydkorea, centrala | `koreacentral` |
| Offentlig | Korea | Sydkorea, södra | `koreasouth` |
| Offentlig | Sydafrika | Sydafrika, norra | `southafricanorth` |
| Offentlig | Sydafrika | Södra Afrika, västra * | `southafricawest` |
| Offentlig | UAE | Förenade Arabemiraten Central * | `uaecentral` |
| Offentlig | UAE | Förenade Arabemiraten, norra | `uaenorth` |
| Offentlig | Storbritannien | Storbritannien, södra | `uksouth` |
| Offentlig | Storbritannien | Storbritannien, västra | `ukwest` |
| Offentlig | USA | Central US | `centralus` |
| Offentlig | USA | East US | `eastus` |
| Offentlig | USA | USA, östra 2 | `eastus2` |
| Offentlig | USA | USA, norra centrala | `northcentralus` |
| Offentlig | USA | USA, södra centrala | `southcentralus` |
| Offentlig | USA | USA, västra centrala | `westcentralus` |
| Offentlig | USA | USA, västra | `westus` |
| Offentlig | USA | USA, västra 2 | `westus2` |
| US Gov | USA | US Gov, Arizona | `usgovarizona` |
| US Gov | USA | US Gov, Texas | `usgovtexas` |
| US Gov | USA | US Gov, Virginia | `usgovvirginia` |

Azure File Sync stöder endast synkronisering med en Azure-filresurs som är i samma region som tjänsten för synkronisering av lagring.

För regionerna som har marker ATS med asterisker måste du kontakta Azure-supporten för att begära åtkomst till Azure Storage i dessa regioner. Processen beskrivs i [det här dokumentet](https://azure.microsoft.com/global-infrastructure/geographies/).

## <a name="redundancy"></a>Redundans
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Redundant lagring för geo-redundanta och geo-zoner har möjlighet att manuellt redundansväxla lagring till den sekundära regionen. Vi rekommenderar att du inte gör detta utanför en katastrof när du använder Azure File Sync på grund av den ökade sannolikheten för data förlust. I händelse av en katastrof där du vill initiera en manuell redundansväxling av lagring måste du öppna ett support ärende med Microsoft för att få Azure File Sync att återuppta synkroniseringen med den sekundära slut punkten.

## <a name="migration"></a>Migrering
Om du har en befintlig Windows-fil Server kan Azure File Sync installeras direkt på plats, utan att behöva flytta data till en ny server. Om du planerar att migrera till en ny Windows-filserver som en del av att anta Azure File Sync finns det flera olika metoder för att flytta data över:

- Skapa server slut punkter för din gamla fil resurs och den nya fil resursen och låt Azure File Sync synkronisera data mellan server slut punkterna. Fördelen med den här metoden är att det är mycket enkelt att överprenumerera lagringen på den nya fil servern, eftersom Azure File Sync är beroende av moln nivåer. När du är klar kan du klippa över slutanvändare till fil resursen på den nya servern och ta bort den gamla fil resursens Server slut punkt.

- Skapa bara en server slut punkt på den nya fil servern och kopiera data till från den gamla fil resursen med hjälp av `robocopy` . Beroende på topologin för fil resurser på din nya server (hur många resurser du har på varje volym, hur det kostar varje volym osv.) kan du tillfälligt behöva etablera ytterligare lagrings utrymme eftersom det förväntas att `robocopy` från den gamla servern till den nya servern i det lokala data centret kommer att bli snabbare än Azure File Sync att flytta data till Azure.

Du kan också använda Data Box-enhet för att migrera data till en Azure File Sync-distribution. I de flesta fall, när kunder vill använda Data Box-enhet för att mata in data, gör de det eftersom de tror att de kommer att öka hastigheten på sin distribution, eller så kan de hjälpa till med begränsade bandbredds scenarier. Även om det är sant att när du använder en Data Box-enhet för att mata in data i din Azure File Sync-distribution minskar bandbredds användningen, kommer det förmodligen att gå snabbare för de flesta scenarier för att kunna använda en data uppladdning online via en av de metoder som beskrivs ovan. Mer information om hur du använder Data Box-enhet för att mata in data i din Azure File Sync-distribution finns i [migrera data till Azure File Sync med Azure Data Box](storage-sync-offline-data-transfer.md).

Vanliga misstag som kunder gör när de migrerar data till sin nya Azure File Sync-distribution är att kopiera data direkt till Azure-filresursen i stället för på sina Windows-filservrar. Även om Azure File Sync kommer att identifiera alla nya filer på Azure-filresursen och synkronisera tillbaka dem till dina Windows-filresurser, är detta normalt betydligt långsammare än att läsa in data via Windows-filservern. När du använder Azure Copy-verktyg, till exempel AzCopy, är det viktigt att använda den senaste versionen. Se [tabellen fil kopierings verktyg](storage-files-migration-overview.md#file-copy-tools) för att få en översikt över Azure Copy-verktyg så att du kan kopiera alla viktiga metadata för en fil, till exempel tidsstämplar och ACL: er.

## <a name="antivirus"></a>Antivirus
Eftersom antivirus programmet fungerar genom att söka igenom filer efter känd skadlig kod kan ett antivirus program orsaka återkallande av nivåbaserade filer, vilket resulterar i högt utgående kostnader. I version 4,0 och senare av Azure File Sync agenten har filer på nivån säker Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS angett. Vi rekommenderar att du rådfrågar din program varu leverantör för att lära dig hur du konfigurerar lösningen för att hoppa över att läsa filer med den här attributuppsättningen (många gör det automatiskt). 

Microsofts interna antivirus lösningar, Windows Defender och System Center Endpoint Protection (SCEP), hoppar båda automatiskt över att läsa filer som har det här attributet angivet. Vi har testat dem och identifierat ett mindre problem: när du lägger till en server i en befintlig Sync-grupp, anropas filer som är mindre än 800 byte (nedladdade) på den nya servern. De här filerna kommer att finnas kvar på den nya servern och kommer inte att skiktas eftersom de inte uppfyller storleks kravet för skikt (>64 KB).

> [!Note]  
> Antivirus leverantörer kan kontrol lera kompatibiliteten mellan sina produkter och Azure File Sync med hjälp av [Testsviten Azure File Sync Antivirus kompatibilitet](https://www.microsoft.com/download/details.aspx?id=58322)som är tillgänglig för hämtning på Microsoft Download Center.

## <a name="backup"></a>Backup 
Om moln skiktning är aktiverat ska lösningar som direkt säkerhetskopierar Server slut punkten eller en virtuell dator där Server slut punkten finns inte användas. Moln nivåer gör att endast en delmängd av dina data lagras på Server slut punkten, med den fullständiga data uppsättningen som finns i Azure-filresursen. Beroende på vilken säkerhets kopierings lösning som används kommer filer i nivå antingen att hoppas över och inte säkerhets kopie ras (eftersom de har attributet FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS), eller så kommer de att återkallas till disk, vilket resulterar i höga utgående kostnader. Vi rekommenderar att du använder en lösning för säkerhets kopiering i molnet för att säkerhetskopiera Azure-filresursen direkt. Mer information finns i [om säkerhets kopiering av Azure-filresurser](https://docs.microsoft.com/azure/backup/azure-file-share-backup-overview?toc=/azure/storage/files/toc.json) eller kontakta säkerhets kopierings leverantören för att se om de stöder säkerhets kopiering av Azure-filresurser.

Om du föredrar att använda en lokal lösning för säkerhets kopiering ska säkerhets kopieringar utföras på en server i den synkroniserade grupp där moln nivå inaktive ras. När du utför en återställning använder du alternativen på volym-eller fil nivå återställning. Filer som återställs med alternativet Återställning på filnivå synkroniseras till alla slut punkter i Sync-gruppen och befintliga filer ersätts med den version som återställs från säkerhets kopian.  Återställningar på volym nivå ersätter inte nyare fil versioner i Azure-filresursen eller andra server slut punkter.

> [!Note]  
> Återställning utan operativ system (BMR) kan orsaka oväntade resultat och stöds inte för närvarande.

> [!Note]  
> Med version 9 av Azure File Sync agent, stöds nu VSS-ögonblicksbilder (inklusive tidigare versioner) på volymer som har aktiverat moln skikt. Du måste dock aktivera tidigare versions kompatibilitet via PowerShell. [Lär dig hur](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nästa steg
* [Överväg inställningar för brand vägg och proxy](storage-sync-files-firewall-and-proxy.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Distribuera Azure Files](storage-files-deployment-guide.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
* [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
