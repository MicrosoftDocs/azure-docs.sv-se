---
title: Planera för en Azure File Sync distribution | Microsoft Docs
description: Planera för en distribution med Azure File Sync, en tjänst som gör att du kan cachelagra flera Azure-filresurser på en lokal Windows Server eller en virtuell dator i molnet.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: cbc6e119348e5a0e805ac502de9eddfa9d9c4b6d
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717917"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planera för distribution av Azure File Sync

:::row:::
    :::column:::
        [![Intervju och demonstration med Azure File Sync – klicka för att spela upp!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync är en tjänst som gör att du kan cachelagra flera Azure-filresurser på en lokal Windows Server eller en virtuell dator i molnet. 
        
        Den här artikeln beskriver Azure File Sync begrepp och funktioner. När du är bekant med Azure File Sync bör du överväga att följa [Azure File Sync distributionsguide för](storage-sync-files-deployment-guide.md) att prova den här tjänsten.        
    :::column-end:::
:::row-end:::

Filerna lagras i molnet i [Azure-filresurser](storage-files-introduction.md). Azure-filresurser kan användas på två sätt: genom att direkt montera dessa serverlösa Azure-filresurser (SMB) eller genom att cachelagra Azure-filresurser lokalt med hjälp av Azure File Sync. Vilket distributionsalternativ du väljer ändrar de aspekter som du behöver tänka på när du planerar för distributionen. 

- **Direkt montering av en** Azure-filresurs: Eftersom Azure Files ger SMB-åtkomst kan du montera Azure-filresurser lokalt eller i molnet med hjälp av standard-SMB-klienten som är tillgänglig i Windows, macOS och Linux. Eftersom Azure-filresurser är serverlösa kräver distribution för produktionsscenarier inte hantering av en filserver eller NAS-enhet. Det innebär att du inte behöver tillämpa programkorrigeringar eller byta ut fysiska diskar. 

- **Cachelagra En Azure-filresurs** lokalt med Azure File Sync: med Azure File Sync kan du centralisera organisationens filresurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver. Azure File Sync omvandlar en lokal (eller molnbaserad) Windows Server till ett snabbt cacheminne för din Azure-filresurs. 

## <a name="management-concepts"></a>Hanteringsbegrepp
En Azure File Sync-distribution har tre grundläggande hanteringsobjekt:

- **Azure-filresurs:** En Azure-filresurs är en serverlös molnfilresurs som tillhandahåller *molnslutpunkten* för en Azure File Sync-synkroniseringsrelation. Filer i en Azure-filresurs kan nås direkt med SMB eller FileREST-protokollet, men vi rekommenderar att du främst kommer åt filerna via Windows Server-cachen när Azure-filresursen används med Azure File Sync. Det beror på Azure Files idag saknar en effektiv ändringsidentifieringsmekanism som Windows Server har, så det tar tid att sprida ändringarna direkt till serverslutpunkterna.
- **Serverslutpunkt:** Sökvägen på Den Windows Server som synkroniseras till en Azure-filresurs. Detta kan vara en specifik mapp på en volym eller volymens rot. Flera serverslutpunkter kan finnas på samma volym om deras namnområden inte överlappar varandra.
- **Synkroniseringsgrupp:** Det objekt som definierar synkroniseringsrelationen mellan en **molnslutpunkt,** eller Azure-filresurs, och en serverslutpunkt. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. Om du till exempel har två olika uppsättningar filer som du vill hantera med Azure File Sync, skapar du två synkroniseringsgrupper och lägger till olika slutpunkter i varje synkroniseringsgrupp.

### <a name="azure-file-share-management-concepts"></a>Hanteringsbegrepp för Azure-filresurs
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync hanteringsbegrepp
Synkroniseringsgrupper distribueras till **Storage Sync Services**, som är toppnivåobjekt som registrerar servrar för användning med Azure File Sync och innehåller synkroniseringsgrupprelationerna. Storage Sync Service-resursen är en peer för lagringskontoresursen och kan på liknande sätt distribueras till Azure-resursgrupper. En tjänst för synkronisering av lagring kan skapa synkroniseringsgrupper som innehåller Azure-filresurser över flera lagringskonton och flera registrerade Windows-servrar.

Innan du kan skapa en synkroniseringsgrupp i en tjänst för synkronisering av lagring måste du först registrera en Windows Server med tjänsten för synkronisering av lagring. Detta skapar ett **registrerat serverobjekt** som representerar en förtroenderelation mellan din server eller ditt kluster och tjänsten för synkronisering av lagring. Om du vill registrera en tjänst för synkronisering av lagring måste du först Azure File Sync agenten på servern. En enskild server eller ett kluster kan bara registreras med en tjänst för synkronisering av lagring i taget.

En synkroniseringsgrupp innehåller en molnslutpunkt eller Azure-filresurs och minst en serverslutpunkt. Serverslutpunktsobjektet innehåller inställningarna  som konfigurerar molnnivåindelad kapacitet, vilket ger cachelagringsfunktionerna i Azure File Sync. För att kunna synkronisera med en Azure-filresurs måste lagringskontot som innehåller Azure-filresursen finnas i samma Azure-region som tjänsten för synkronisering av lagring.

> [!Important]  
> Du kan göra ändringar i namnområdet för valfri molnslutpunkt eller serverslutpunkt i synkroniseringsgruppen och synkronisera filerna med de andra slutpunkterna i synkroniseringsgruppen. Om du gör en direkt ändring i molnslutpunkten (Azure-filresursen) måste ändringarna först identifieras av en Azure File Sync-jobb för ändringsidentifiering. Ett ändringsidentifieringsjobb initieras endast för en molnslutpunkt en gång var 24:e timme. Mer information finns i [Azure Files vanliga frågor och svar.](storage-files-faq.md#afs-change-detection)

### <a name="consider-the-count-of-storage-sync-services-needed"></a>Ta hänsyn till antalet lagringssynkroniseringstjänster som behövs
I ett tidigare avsnitt beskrivs kärnresursen som ska konfigureras för Azure File Sync: en *tjänst för synkronisering av lagring.* En Windows Server kan bara registreras till en tjänst för synkronisering av lagring. Därför är det ofta bäst att bara distribuera en enda tjänst för synkronisering av lagring och registrera alla servrar som tjänsten använder. 

Skapa endast flera tjänster för synkronisering av lagring om du har:
* distinkta uppsättningar servrar som aldrig får utbyta data med varandra. I det här fallet vill du utforma systemet så att vissa uppsättningar servrar undantas för synkronisering med en Azure-filresurs som redan används som en molnslutpunkt i en synkroniseringsgrupp i en annan tjänst för synkronisering av lagring. Ett annat sätt att titta på detta är att Windows-servrar som är registrerade i en annan tjänst för synkronisering av lagring inte kan synkroniseras med samma Azure-filresurs.
* ett behov av att ha fler registrerade servrar eller synkroniseringsgrupper än en enda tjänst för synkronisering av lagring har stöd för. Granska Azure File Sync [för mer](storage-files-scale-targets.md#azure-file-sync-scale-targets) information.

## <a name="plan-for-balanced-sync-topologies"></a>Planera för balanserade synkroniserings topologier
Innan du distribuerar resurser är det viktigt att planera vad du ska synkronisera på en lokal server, med vilken Azure-filresurs. Genom att skapa en plan kan du avgöra hur många lagringskonton, Azure-filresurser och synkroniseringsresurser du behöver. Dessa överväganden är fortfarande relevanta, även om dina data för närvarande inte finns på en Windows Server eller den server som du vill använda på lång sikt. [Migreringsavsnittet](#migration) kan hjälpa dig att fastställa lämpliga migreringsvägar för din situation.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>Överväganden för Windows-filserver
Om du vill aktivera synkroniseringsfunktionerna på Windows Server måste du installera Azure File Sync hämtningsbar agent. Agenten Azure File Sync två huvudkomponenter: , Windows-bakgrundstjänsten som ansvarar för att övervaka ändringar på serverslutpunkterna och initiera synkroniseringssessioner, och , ett filsystemsfilter som möjliggör molnnivåindelning och snabb `FileSyncSvc.exe` haveriberedskap. `StorageSync.sys`  

### <a name="operating-system-requirements"></a>Operativsystemskrav
Azure File Sync stöds med följande versioner av Windows Server:

| Version | SKU:er som stöds | Distributionsalternativ som stöds |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard och IoT | Full och Core |
| Windows Server 2016 | Datacenter, Standard och Lagringsserver | Full och Core |
| Windows Server 2012 R2 | Datacenter, Standard och Lagringsserver | Full och Core |

Framtida versioner av Windows Server kommer att läggas till när de släpps.

> [!Important]  
> Vi rekommenderar att du håller alla servrar som Azure File Sync uppdaterade med de senaste uppdateringarna från Windows Update. 

### <a name="minimum-system-resources"></a>Minsta systemresurser
Azure File Sync kräver en server, antingen fysisk eller virtuell, med minst en processor och minst 2 GiB minne.

> [!Important]  
> Om servern körs på en virtuell dator med dynamiskt minne aktiverat ska den virtuella datorn konfigureras med minst 2 048 MiB minne.

För de flesta produktionsarbetsbelastningar rekommenderar vi inte att du konfigurerar Azure File Sync en synkroniseringsserver med endast minimikraven. Mer information [finns i Rekommenderade systemresurser.](#recommended-system-resources)

### <a name="recommended-system-resources"></a>Rekommenderade systemresurser
Precis som alla server-funktioner eller program bestäms systemresurskraven för Azure File Sync av distributionens skala. större distributioner på en server kräver större systemresurser. För Azure File Sync bestäms skalningen av antalet objekt i serverslutpunkterna och datamängdens omsättning. En enskild server kan ha serverslutpunkter i flera synkroniseringsgrupper och antalet objekt som anges i följande tabellkonton för det fullständiga namnområdet som en server är ansluten till. 

Till exempel serverslutpunkt A med 10 miljoner objekt + serverslutpunkt B med 10 miljoner objekt = 20 miljoner objekt. För den exempeldistributionen rekommenderar vi 8 processorer, 16 GiB minne för stabilt tillstånd och (om möjligt) 48 GiB minne för den första migreringen.
 
Namnområdesdata lagras i minnet av prestandaskäl. Därför kräver större namnrymder mer minne för att upprätthålla bra prestanda, och mer dataomsättning kräver mer processorkraft för att bearbetas. 
 
I följande tabell har vi angett både storleken på namnområdet och en konvertering till kapacitet för typiska allmänna filresurser, där den genomsnittliga filstorleken är 512 KiB. Om filstorlekarna är mindre kan du överväga att lägga till mer minne för samma mängd kapacitet. Basera minneskonfigurationen på namnområdets storlek.

| Namnområdesstorlek – filer & kataloger (miljoner)  | Typisk kapacitet (TiB)  | CPU-kärnor  | Rekommenderat minne (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (inledande synkronisering)/ 2 (typisk omsättning)      |
| 5        | 2.3     | 2        | 16 (inledande synkronisering)/ 4 (typisk omsättning)    |
| 10       | 4.7     | 4        | 32 (inledande synkronisering)/ 8 (typisk omsättning)   |
| 30       | 14,0    | 8        | 48 (inledande synkronisering)/ 16 (typisk omsättning)   |
| 50       | 23,3    | 16       | 64 (inledande synkronisering)/ 32 (typisk omsättning)  |
| 100*     | 46.6    | 32       | 128 (inledande synkronisering)/ 32 (typisk omsättning)  |

\*Synkronisering av fler än 100 miljoner &-kataloger rekommenderas inte just nu. Det här är en mjuk gräns baserat på våra testade tröskelvärden. Mer information finns i Azure Files [mål för skalbarhet och prestanda.](storage-files-scale-targets.md#azure-file-sync-scale-targets)

> [!TIP]
> Inledande synkronisering av ett namnområde är en intensiv åtgärd och vi rekommenderar att du allokerar mer minne tills den inledande synkroniseringen är klar. Detta krävs inte, men kan påskynda den inledande synkroniseringen. 
> 
> En typisk omsättning är 0,5 % av namnområdet som ändras per dag. Överväg att lägga till mer CPU för högre omsättningsnivåer. 

- En lokalt ansluten volym formaterad med NTFS-filsystemet.

### <a name="evaluation-cmdlet"></a>Cmdlet för utvärdering
Innan du Azure File Sync bör du utvärdera om det är kompatibelt med systemet med hjälp Azure File Sync cmdleten för utvärdering. Denna cmdlet söker efter potentiella problem med filsystemet och datauppsättningen, till exempel tecken som inte stöds eller en operativsystemversion som inte stöds. Dess kontroller omfattar de flesta men inte alla funktioner som anges nedan. Vi rekommenderar att du läser igenom resten av det här avsnittet noggrant för att säkerställa att distributionen går smidigt. 

Utvärderings-cmdleten kan installeras genom att installera Az PowerShell-modulen, som kan installeras genom att följa anvisningarna här: Installera och [konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Användning  
Du kan anropa utvärderingsverktyget på några olika sätt: du kan utföra systemkontroller, datauppsättningskontroller eller både och. Så här utför du både system- och datauppsättningskontrollerna: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Så här testar du endast din datauppsättning:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Så här testar du endast systemkrav:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
Så här visar du resultatet i CSV:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Filsystemkompatibilitet
Azure File Sync stöds endast på direkt anslutna NTFS-volymer. Direktkopplad lagring, eller DAS, på Windows Server innebär att Windows Server-operativsystemet äger filsystemet. DAS kan tillhandahållas genom att fysiskt koppla diskar till filservern, koppla virtuella diskar till en virtuell filserverdator (till exempel en virtuell dator som finns i Hyper-V) eller till och med via ISCSI.

Endast NTFS-volymer stöds. ReFS, FAT, FAT32 och andra filsystem stöds inte.

I följande tabell visas interopstillståndet för NTFS-filsystemfunktioner: 

| Funktion | Supportstatus | Kommentarer |
|---------|----------------|-------|
| Åtkomstkontrollistor (ACL) | Fullständigt stöd | Listor över godtyckliga åtkomstkontroller i Windows-format bevaras av Azure File Sync och tillämpas av Windows Server på serverslutpunkter. ACL:er kan också tillämpas när du monterar Azure-filresursen direkt, men detta kräver ytterligare konfiguration. Mer information [finns i](#identity) avsnittet Identitet. |
| Hårda länkar | Överhoppad | |
| Symboliska länkar | Överhoppad | |
| Monteringspunkter | Stöds delvis | Monteringspunkter kan vara roten på en serverslutpunkt, men de hoppas över om de finns i en serverslutpunkts namnområde. |
| Korsningar | Överhoppad | Du kan till Distributed File System mapparna DfrsrPrivate och DFSRoots. |
| Referenspunkter | Överhoppad | |
| NTFS-komprimering | Fullständigt stöd | |
| Glesa filer | Fullständigt stöd | Sparse-filsynkronisering (blockeras inte), men de synkroniserar till molnet som en fullständig fil. Om filinnehållet ändras i molnet (eller på en annan server) blir filen inte längre gles när ändringen hämtas. |
| Alternativa dataströmmar (ADS) | Bevarad, men inte synkroniserad | Till exempel synkroniseras inte klassificeringstaggar som skapats av filklassificeringsinfrastrukturen. Befintliga klassificeringstaggar för filer på var och en av serverslutpunkterna lämnas oförändrade. |

<a id="files-skipped"></a>Azure File Sync hoppar även över vissa tillfälliga filer och systemmappar:

| Fil/mapp | Anteckning |
|-|-|
| pagefile.sys | Fil som är specifik för systemet |
| Desktop.ini | Fil som är specifik för systemet |
| Thumbs.db | Temporär fil för miniatyrbilder |
| yethumbs.db | Temporär fil för medieminiatyrer |
| ~$\*.\* | Tillfällig Office-fil |
| \*Tmp | Temporär fil |
| \*.laccdb | Åtkomst till DB-låsningsfil|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern synkroniseringsfil|
| \\Systemvolyminformation | Mapp som är specifik för volymen |
| $RECYCLE. Bin| Mapp |
| \\SyncShareState | Mapp för synkronisering |

### <a name="failover-clustering"></a>Redundanskluster
Windows Server-redundansklustring stöds av Azure File Sync för distributionsalternativet "Filserver för allmänt bruk". Redundansklustring stöds inte på "Skalbar filserver för programdata" (SOFS) eller på klustrade delade volymer (CSV:er).

> [!Note]  
> Agenten Azure File Sync installeras på varje nod i ett redundanskluster för att synkroniseringen ska fungera korrekt.

### <a name="data-deduplication"></a>Datadeduplicering
**Windows Server 2016 och Windows Server 2019**   
Datadeduplicering stöds oavsett om molnnivåindelning är aktiverat eller inaktiverat på en eller flera serverslutpunkter på volymen för Windows Server 2016 och Windows Server 2019. Genom att aktivera Datadeduplicering på en volym med molnnivåindelad lagring aktiverat kan du cachelagra fler filer lokalt utan att etablera mer lagringsutrymme. 

När Datadeduplicering är aktiverat på en volym med molnnivåindelning aktiverat kommer dedupliceringsoptimerade filer på serverslutpunktens plats att nivåindelad likna en normal fil baserat på principinställningarna för molnnivåindelning. När de deduplicerade filerna har nivåindelats körs skräpinsamlingsjobbet datadeduplicering automatiskt för att frigöra diskutrymme genom att ta bort onödiga segment som inte längre refereras till av andra filer på volymen.

Observera att volymbesparingarna endast gäller för servern. dina data i Azure-filresursen kommer inte att deduplicas.

> [!Note]  
> För att stödja datadeduplicering på volymer med molnnivåindelad lagring aktiverat på Windows Server 2019 måste Windows Update [KB4520062 – oktober 2019](https://support.microsoft.com/help/4520062) eller en senare månatlig samlad uppdatering installeras och Azure File Sync-agentversion 12.0.0.0 eller senare krävs.

**Windows Server 2012 R2**  
Azure File Sync stöder inte datadeduplicering och molnnivåindelad lagring på samma volym på Windows Server 2012 R2. Om Datadeduplicering är aktiverat på en volym måste molnnivåindelad vara inaktiverad. 

**Kommentarer**
- Om Datadeduplicering har installerats innan du installerar Azure File Sync agenten krävs en omstart för att stödja datadeduplicering och molnnivåindeling på samma volym.
- Om Datadeduplicering är aktiverat på en volym när molnnivåindelad lagring har aktiverats optimerar det inledande optimeringsjobbet filer på volymen som inte redan är nivåindelade och påverkar molnnivåindelad lagring:
    - Principen för ledigt utrymme fortsätter att nivåindelad nivå för filer enligt det lediga utrymmet på volymen med hjälp av den heatmap.
    - Datumprincip hoppar över nivåindelad lagring av filer som annars kan vara berättigade till nivåindelad lagring på grund av dedupliceringsoptimeringsjobbet som kommer åt filerna.
- För pågående dedupliceringsoptimeringsjobb fördröjs molnnivåindelningen med datumprincipen av inställningen [Datadeduplicering MinimumFileAgeDays,](/powershell/module/deduplication/set-dedupvolume) om filen inte redan är nivåindelad. 
    - Exempel: Om inställningen MinimumFileAgeDays är sju dagar och principen för molnnivåindelad lagring är 30 dagar kommer datumprincipen att nivåindela filer efter 37 dagar.
    - Obs! När en fil har nivåindelats Azure File Sync dedupliceringsoptimeringsjobbet att hoppa över filen.
- Om en server som kör Windows Server 2012 R2 med Azure File Sync-agenten installerad uppgraderas till Windows Server 2016 eller Windows Server 2019, måste följande steg utföras för att stödja datadeduplicering och molnnivåindelade på samma volym:  
    - Avinstallera Azure File Sync för Windows Server 2012 R2 och starta om servern.
    - Ladda ned Azure File Sync för den nya versionen av serveroperativsystemet (Windows Server 2016 eller Windows Server 2019).
    - Installera Azure File Sync agenten och starta om servern.  
    
    Obs! Azure File Sync-konfigurationsinställningarna på servern bevaras när agenten avinstalleras och installeras om.

### <a name="distributed-file-system-dfs"></a>Distributed File System (DFS)
Azure File Sync stöder interop med DFS-namnrymder (DFS-N) och DFS Replication (DFS-R).

**DFS-namnrymder (DFS-N):** Azure File Sync stöds fullt ut på DFS-N-servrar. Du kan installera Azure File Sync på en eller flera DFS-N-medlemmar för att synkronisera data mellan serverslutpunkterna och molnslutpunkten. Mer information finns i [Översikt över DFS-namnrymder.](/windows-server/storage/dfs-namespaces/dfs-overview)
 
**DFS Replication (DFS-R):** Eftersom DFS-R och Azure File Sync båda är replikeringslösningar rekommenderar vi i de flesta fall att ersätta DFS-R med Azure File Sync. Det finns dock flera scenarier där du vill använda DFS-R och Azure File Sync tillsammans:

- Du migrerar från en DFS-R-distribution till en Azure File Sync distribution. Mer information finns i [Migrera en DFS Replication -distribution (DFS-R) till Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Det är inte alla lokala servrar som behöver en kopia av dina fildata som kan anslutas direkt till Internet.
- Grenservrar konsoliderar data till en enda hubbserver som du vill använda Azure File Sync.

För Azure File Sync och DFS-R ska fungera sida vid sida:

1. Azure File Sync på molnnivåindelad nivå måste inaktiveras på volymer med DFS-R-replikerade mappar.
2. Serverslutpunkter bör inte konfigureras på skrivskyddade DFS-R-replikeringsmappar.

Mer information finns i [DFS Replication översikt.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11))

### <a name="sysprep"></a>Sysprep
Att använda sysprep på en server som har Azure File Sync agent installerad stöds inte och kan leda till oväntade resultat. Agentinstallation och serverregistrering bör ske när du har distribuerat serveravbildningen och slutfört sysprep-miniinstallationen.

### <a name="windows-search"></a>Windows-sök
Om molnnivåindelad lagring är aktiverat på en serverslutpunkt hoppas filer som är nivåindelade över och indexeras inte av Windows Search. Filer som inte är nivåindelade indexeras korrekt.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andra HSM-lösningar (hierarkisk lagringshantering)
Inga andra HSM-lösningar bör användas med Azure File Sync.

## <a name="performance-and-scalability"></a>Prestanda och skalbarhet

Eftersom Azure File Sync-agenten körs på en Windows Server-dator som ansluter till Azure-filresurser beror den effektiva synkroniseringsprestandan på ett antal faktorer i infrastrukturen: Windows Server och den underliggande diskkonfigurationen, nätverksbandbredden mellan servern och Azure Storage, filstorlek, total datauppsättningsstorlek och aktiviteten på datauppsättningen. Eftersom Azure File Sync fungerar på filnivå mäts prestandaegenskaperna för en Azure File Sync-baserad lösning bättre i antalet objekt (filer och kataloger) som bearbetas per sekund.

Ändringar som görs i Azure-filresursen med Azure Portal eller SMB identifieras inte omedelbart och replikeras som ändringar i serverslutpunkten. Azure Files ännu inte har ändringsmeddelanden eller journaler, så det finns inget sätt att automatiskt initiera en synkroniseringssession när filer ändras. På Windows Server använder Azure File Sync [Windows USN-journaler för](/windows/win32/fileio/change-journals) att automatiskt initiera en synkroniseringssession när filer ändras

Om du vill identifiera ändringar i Azure-filresursen Azure File Sync ett schemalagt jobb som kallas för ett ändringsidentifieringsjobb. Ett ändringsidentifieringsjobb räknar upp varje fil i filresursen och jämför den sedan med synkroniseringsversionen för filen. När ändringsidentifieringsjobbet avgör att filer har ändrats Azure File Sync en synkroniseringssession. Ändringsidentifieringsjobbet initieras var 24:e timme. Eftersom jobbet för ändringsidentifiering fungerar genom att räkna upp alla filer i Azure-filresursen tar ändringsidentifiering längre tid i större namnrymder än i mindre namnrymder. För stora namnrymder kan det ta längre tid än en gång var 24:e timme att avgöra vilka filer som har ändrats.

Mer information finns i [Azure File Sync prestandamått och Azure File Sync](storage-files-scale-targets.md#azure-file-sync-performance-metrics) [skalningsmål](storage-files-scale-targets.md#azure-file-sync-scale-targets)

## <a name="identity"></a>Identitet
Azure File Sync fungerar med din STANDARD AD-baserade identitet utan några särskilda inställningar utöver att konfigurera synkronisering. När du använder Azure File Sync är den allmänna förväntningen att de flesta åtkomsterna går via Azure File Sync cachelagringsservrar i stället för via Azure-filresursen. Eftersom serverslutpunkterna finns på Windows Server och Windows Server har stöd för AD- och Windows-ACL:er under en längre tid, behövs inget utöver att se till att Windows-filservrarna som registrerats med tjänsten för synkronisering av lagring är domänanslutning. Azure File Sync lagrar ACL:er på filerna i Azure-filresursen och replikerar dem till alla serverslutpunkter.

Även om ändringar som görs direkt i Azure-filresursen tar längre tid att synkronisera till serverslutpunkterna i synkroniseringsgruppen, kan du också se till att du kan tillämpa dina AD-behörigheter på din filresurs direkt i molnet också. Om du vill göra detta måste du domän-ansluta ditt lagringskonto till din lokala AD, precis som hur dina Windows-filservrar är domänkopplingar. Mer information om hur du ansluter ditt lagringskonto till en kundägd Active Directory finns i [Azure Files Active Directory-översikt.](storage-files-active-directory-overview.md)

> [!Important]  
> Domän som ansluter ditt lagringskonto till Active Directory krävs inte för att distribuera Azure File Sync. Det här är ett strikt valfritt steg som gör att Azure-filresursen kan framtvinga lokala ACL:er när användare monterar Azure-filresursen direkt.

## <a name="networking"></a>Nätverk
Agenten Azure File Sync storage sync-tjänsten och Azure-filresursen med hjälp av Azure File Sync REST-protokollet och FileREST-protokollet, som båda alltid använder HTTPS via port 443. SMB används aldrig för att ladda upp eller ned data mellan Din Windows Server och Azure-filresursen. Eftersom de flesta organisationer tillåter HTTPS-trafik via port 443, som ett krav för att besöka de flesta webbplatser, krävs vanligtvis ingen särskild nätverkskonfiguration för att distribuera Azure File Sync.

Baserat på organisationens policy eller unika regelkrav kan du behöva mer restriktiv kommunikation med Azure, och därför Azure File Sync flera mekanismer för att konfigurera nätverk. Baserat på dina krav kan du:

- Tunnelsynkronisering och filuppladdning/nedladdning av trafik via ExpressRoute eller Azure VPN. 
- Använd funktioner Azure Files och Azure-nätverkstjänster, till exempel tjänstslutpunkter och privata slutpunkter.
- Konfigurera Azure File Sync att stödja din proxy i din miljö.
- Begränsa nätverksaktiviteten från Azure File Sync.

Mer information om Azure File Sync och nätverk finns i Azure File Sync [nätverksöverväganden](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Kryptering
När du använder Azure File Sync finns det tre olika lager av kryptering att tänka på: kryptering i vila för Windows Server, kryptering under överföring mellan Azure File Sync-agenten och Azure och kryptering av resten av dina data i Azure-filresursen. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server-kryptering i vila 
Det finns två strategier för att kryptera data på Windows Server som vanligtvis fungerar med Azure File Sync: kryptering under filsystemet så att filsystemet och alla data som skrivs till det krypteras och kryptering i själva filformatet. Dessa metoder är inte ömsesidigt uteslutande. De kan användas tillsammans om du vill eftersom syftet med kryptering är annorlunda.

Windows Server tillhandahåller BitLocker-inkorg för att tillhandahålla kryptering under filsystemet. BitLocker är helt transparent för Azure File Sync. Det främsta skälet till att använda en krypteringsmekanism som BitLocker är att förhindra fysisk exfiltrering av data från ditt lokala datacenter genom att någon stjäl diskarna och förhindra separat inläsning av ett obehörigt operativsystem för att utföra obehöriga läsningar/skrivningar till dina data. Mer information om BitLocker finns i [BitLocker-översikt.](/windows/security/information-protection/bitlocker/bitlocker-overview)

Produkter från tredje part som fungerar på liknande sätt som BitLocker, i och med att de finns under NTFS-volymen, bör på samma sätt fungera helt transparent med Azure File Sync. 

Den andra huvudmetoden för att kryptera data är att kryptera filens dataström när programmet sparar filen. Vissa program kan göra detta inbyggt, men detta är vanligtvis inte fallet. Ett exempel på en metod för att kryptera filens dataström är Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. Det främsta skälet till att använda en krypteringsmekanism som AIP/RMS är att förhindra data exfiltrering av data från filresursen genom att personer kopierar dem till alternativa platser, till exempel till en flash-enhet eller skickar dem via e-post till en obehörig person. När en fils dataström krypteras som en del av filformatet fortsätter den här filen att vara krypterad på Azure-filresursen. 

Azure File Sync inte med NTFS Encrypted File System (NTFS EFS) eller krypteringslösningar från tredje part som finns ovanför filsystemet men under filens dataström. 

### <a name="encryption-in-transit"></a>Kryptering under överföring

> [!NOTE]
> Azure File Sync tjänsten tar bort stödet för TLS1.0 och 1.1 den 1 augusti 2020. Alla versioner Azure File Sync som stöds använder redan TLS1.2 som standard. En tidigare version av TLS kan inträffa om TLS1.2 har inaktiverats på servern eller om en proxy används. Om du använder en proxyserver rekommenderar vi att du kontrollerar proxykonfigurationen. Azure File Sync tjänstregioner som läggs till efter 2020-05-01 stöder endast TLS1.2 och stöd för TLS1.0 och 1.1 tas bort från befintliga regioner den 1 augusti 2020.  Mer information finns i [felsökningsguiden.](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync)

Azure File Sync-agenten kommunicerar med tjänsten för synkronisering av lagring och Azure-filresursen med hjälp av Azure File Sync REST-protokollet och FileREST-protokollet, som båda alltid använder HTTPS via port 443. Azure File Sync skickar inte okrypterade begäranden via HTTP. 

Azure Storage-konton innehåller en växel för att kräva kryptering under överföring, vilket är aktiverat som standard. Även om växeln på lagringskontonivå är inaktiverad, vilket innebär att okrypterade anslutningar till dina Azure-filresurser är möjliga, använder Azure File Sync fortfarande endast krypterade kanaler för att komma åt filresursen.

Det främsta skälet till att inaktivera kryptering under överföring för lagringskontot är att stödja ett äldre program som måste köras på ett äldre operativsystem, till exempel Windows Server 2008 R2 eller en äldre Linux-distribution, och kommunicera med en Azure-filresurs direkt. Om det äldre programmet pratar med Windows Server-cachen för filresursen har växlingen av den här inställningen ingen effekt. 

Vi rekommenderar starkt att kryptering av data under överföring är aktiverat.

Mer information om kryptering under överföring finns i [kräva säker överföring i Azure Storage.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-file-share-encryption-at-rest"></a>Kryptering av Azure-filresurs i vila
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Lagringsnivåer
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Aktivera standardfilresurser för att sträcka sig över upp till 100 TiB

Som standard kan standardfilresurser endast sträcka sig över upp till 5 TiB, men du kan öka resursgränsen till 100 TiB. Information om hur du ökar resursgränsen finns i [Aktivera och skapa stora filresurser.](storage-files-how-to-create-large-file-share.md)


#### <a name="regional-availability"></a>Regional tillgänglighet
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Tillgänglighet för Azure File Sync-regioner

För regional tillgänglighet, se [Produkttillgänglighet per region.](https://azure.microsoft.com/global-infrastructure/services/?products=storage)

Följande regioner kräver att du begär åtkomst till Azure Storage innan du kan använda Azure File Sync med dem:

- Frankrike, södra
- Sydafrika, västra
- Förenade Arabemiraten, centrala

Följ processen i det här dokumentet för att begära åtkomst [för dessa regioner.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="redundancy"></a>Redundans
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Geo-redundant och geo-zonredundant lagring har möjlighet att manuellt redundansredundanslagring till den sekundära regionen. Vi rekommenderar att du inte gör detta utanför en katastrof när du använder Azure File Sync på grund av den ökade sannolikheten för dataförlust. I händelse av en katastrof där du vill initiera en manuell redundans av lagringen måste du öppna ett supportfall hos Microsoft för att få Azure File Sync att återuppta synkroniseringen med den sekundära slutpunkten.

## <a name="migration"></a>Migrering
Om du har en befintlig Windows-filserver 2012R2 eller nyare kan Azure File Sync installeras direkt på plats, utan att data behöver flyttas över till en ny server. Om du planerar att migrera till en ny Windows-filserver som en del av att börja använda Azure File Sync, eller om dina data för närvarande finns på NAS (Network Attached Storage) finns det flera möjliga migreringsmetoder för att använda Azure File Sync med dessa data. Vilken migreringsmetod du bör välja beror på var dina data finns för närvarande. 

Läs artikeln [migreringsöversikt Azure File Sync azure-filresurs](storage-files-migration-overview.md) där du hittar detaljerad vägledning för ditt scenario.

## <a name="antivirus"></a>Antivirus
Eftersom antivirusprogrammet genomsöker filer efter känd skadlig kod kan en antivirusprodukt orsaka återkallande av nivåindelade filer, vilket resulterar i höga utgående avgifter. I version 4.0 och senare av Azure File Sync-agenten har nivåindelade filer det säkra Windows-FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS angetts. Vi rekommenderar att du tar hjälp av programvaruleverantören för att lära dig hur man konfigurerar sin lösning för att hoppa över läsning av filer med den här attributuppsättningen (många gör det automatiskt). 

Microsofts in-house antivirus solutions, Windows Defender and System Center Endpoint Protection (SCEP), hoppar båda automatiskt över läsning av filer som har den här attributuppsättningen. Vi har testat dem och identifierat ett mindre problem: när du lägger till en server i en befintlig synkroniseringsgrupp kommer filer som är mindre än 800 byte att återkallas (hämtas) på den nya servern. De här filerna finns kvar på den nya servern och kommer inte att nivåindelade eftersom de inte uppfyller storlekskraven för nivåindelade (>64 kB).

> [!Note]  
> Antivirusleverantörer kan kontrollera kompatibiliteten mellan sina produkter och Azure File Sync med [hjälp av Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), som kan laddas ned på Microsoft Download Center.

## <a name="backup"></a>Backup 
Om molnnivåindelad lagring är aktiverad ska lösningar som direkt backar upp serverslutpunkten eller en virtuell dator där serverslutpunkten finns inte användas. Molnnivåindelning gör att endast en delmängd av dina data lagras på serverslutpunkten, där den fullständiga datauppsättningen finns i din Azure-filresurs. Beroende på vilken säkerhetskopieringslösning som används kommer nivåindelade filer antingen att hoppas över och inte säkerhetskopieras (eftersom de har FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS-attributet angett), eller så kommer de att återkallas till disk, vilket resulterar i höga utgående avgifter. Vi rekommenderar att du använder en molnsäkerhetskopieringslösning för att säkerhetskopiera Azure-filresursen direkt. Mer information finns i Om [säkerhetskopiering av Azure-filresurser](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) eller kontakta din säkerhetskopieringsleverantör för att se om de stöder säkerhetskopiering av Azure-filresurser.

Om du föredrar att använda en lokal säkerhetskopieringslösning bör säkerhetskopieringar utföras på en server i synkroniseringsgruppen som har molnnivåindelad. När du utför en återställning använder du återställningsalternativen på volym- eller filnivå. Filer som återställs med hjälp av återställningsalternativet på filnivå synkroniseras till alla slutpunkter i synkroniseringsgruppen och befintliga filer ersätts med den version som återställs från säkerhetskopian.  Återställningar på volymnivå ersätter inte nyare filversioner i Azure-filresursen eller andra serverslutpunkter.

> [!WARNING]
> Om du behöver använda Robocopy /B med en Azure File Sync-agent som körs på antingen käll- eller målservern uppgraderar du till Azure File Sync agentversion v12.0 eller senare. Om du använder Robocopy /B med agentversioner som är mindre än v12.0 skadas nivåindelade filer under kopieringen.

> [!Note]  
> Återställning utan metal (BMR) kan orsaka oväntade resultat och stöds inte för närvarande.

> [!Note]  
> Med version 9 av Azure File Sync-agenten stöds NU VSS-ögonblicksbilder (inklusive fliken Tidigare versioner) på volymer som har molnnivåindelad nivåindelad. Du måste dock aktivera kompatibilitet med tidigare versioner via PowerShell. [Lär dig hur](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-classification"></a>Dataklassificering
Om du har installerat programvara för dataklassificering kan aktivering av molnnivåindelning resultera i ökade kostnader av två skäl:

1. När molnnivåindelning är aktiverat cachelagras dina hetaste filer lokalt och de coolaste filerna nivåindelade till Azure-filresursen i molnet. Om din dataklassificering regelbundet genomsöker alla filer i filresursen måste de filer som är nivåindelade i molnet återkallas när de genomsöks. 

2. Om dataklassificeringsprogrammet använder metadata i dataströmmen för en fil måste filen återkallas fullständigt för att programvaran ska kunna se klassificeringen. 

Dessa ökningar av både antalet återkallanden och mängden data som återkallas kan öka kostnaderna.

## <a name="azure-file-sync-agent-update-policy"></a>Uppdateringsprincip för Azure File Sync-agenten
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Nästa steg
* [Överväg brandväggs- och proxyinställningar](storage-sync-files-firewall-and-proxy.md)
* [Planera för en Azure Files-distribution](storage-files-planning.md)
* [Distribuera Azure Files](./storage-how-to-create-file-share.md)
* [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
* [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
