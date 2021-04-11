---
title: Oracle Database i Azure Virtual Machines säkerhets kopierings strategier
description: Alternativ för att säkerhetskopiera Oracle-databaser i en Azure Linux VM-miljö.
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 8a1eb1c21663e0294cd384daa0ba644adf78007a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101673220"
---
# <a name="oracle-database-in-azure-linux-vm-backup-strategies"></a>Oracle Database i Azure Linux-strategier för VM-säkerhetskopiering

Databas säkerhets kopior skyddar databasen mot data förlust på grund av ett problem med lagrings komponenten och data Center haveriet. De kan också vara ett sätt att återställa från mänskligt fel och ett sätt att klona en databas i utvecklings-eller testnings syfte. 

Eftersom allt lagrings utrymme är mycket redundant och förlusten av en eller flera diskar inte leder till ett databas avbrott, används säkerhets kopieringarna oftast för att skydda mot mänskligt fel, för att klona åtgärder eller bevara data i enlighet med lagstiftningen. De är också ett sätt att skydda mot regionala avbrott där en katastrof återställnings teknik som DataGuard inte används. I det här fallet måste säkerhets kopiorna lagras i olika Azure-regioner med Geo-redundant replikering, så att de blir tillgängliga utanför det primära databas området.

## <a name="azure-storage"></a>Azure Storage 

[Azure Storage tjänsterna](../../../storage/common/storage-introduction.md) är Microsofts moln lagrings lösning för moderna data lagrings scenarier. Azure Storage erbjuder ett antal tjänster som kan användas för att montera extern lagring till den virtuella Azure Linux-datorn, vilket är lämpligt som säkerhets kopierings medier för Oracle-databaser. Ett säkerhets kopierings verktyg som Oracle RMAN krävs för att initiera en säkerhets kopierings-eller återställnings åtgärd och kopiera säkerhets kopian till/från tjänsten Azure Storage.
 
Azure Storage Services erbjuder följande fördelar:

-  Pålitlig och mycket tillgänglig. Redundans garanterar att dina data är säkra i händelse av tillfälliga maskinvarufel. All lagring är som standard tredubbla speglad. Du kan också välja att replikera data mellan data Center eller geografiska regioner för ytterligare skydd från lokala Catastrophe eller natur katastrofer. Data som replikeras på det här sättet förblir mycket tillgängliga vid ett oväntat avbrott.

-  Skydda. Alla data som skrivs till ett Azure Storage-konto krypteras av tjänsten. Med Azure Storage får du detaljerad kontroll över vem som har tillgång till dina data.

-  Scalable. Azure Storage är utformat för att vara mycket skalbart för att uppfylla krav på datalagring och prestanda för dagens program.

-  Leda. Azure hanterar maskin varu underhåll, uppdateringar och kritiska problem åt dig.

-  Åtkomlig. Data i Azure Storage är åtkomlig från hela världen via HTTP eller HTTPS varifrån som helst. Microsoft tillhandahåller klient bibliotek för Azure Storage på flera olika språk, t. ex. .NET, Java, Node.js, python, PHP, ruby, go med mera, samt en vuxen REST API. Azure Storage stöder skriptning i Azure PowerShell eller Azure CLI. Azure-portalen och Azure Storage Explorer erbjuder även enkla visuella lösningar för att arbeta med dina data.

Azure Storage Platform innehåller följande data tjänster som är lämpliga att använda som säkerhets kopierings medier för Oracle-databasen:

-  Azure Blobs: En mycket skalbar objektlagring för textdata och binära data. Innehåller även stöd för stor data analys via Data Lake Storage Gen2.

-  Azure Files: hanterade fil resurser för molnbaserade eller lokala distributioner.

-  Azure-diskar: lagrings volymer på Block nivå för virtuella Azure-datorer.

### <a name="cross-regional-storage-mounting"></a>Lagrings montering över regioner

Möjligheten att komma åt lagring av säkerhets kopior över flera regioner är en viktig aspekt av BCDR och användbart för att klona databaser från säkerhets kopior till olika geografiska regioner. Azure Cloud Storage tillhandahåller fem nivåer av [redundans](../../../storage/common/storage-redundancy.md)
- Lokalt redundant lagring [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) där dina data replikeras tre gånger inom en enda fysisk plats i den primära regionen.  
- Zon redundant lagring [(ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage) där dina data skyddas av LRS i den primära regionen och replikeras synkront över tre tillgänglighets zoner (AZ) i den primära regionen. Varje AZ är även LRS skyddad. 
- Geo-Redundant Storage [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) där dina data skyddas av LRS i den primära regionen och replikeras asynkront till en sekundär region där den också skyddas av LRS.
- Geo-Zone-redundant lagring [(GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) kopierar dina data synkront över tre tillgänglighets zoner i Azure i den primära regionen med hjälp av ZRS. Därefter kopieras dina data asynkront till en enda fysisk plats i den sekundära regionen. Alla plats data skyddas av LRS. 
- Läs åtkomst Geo-Redundant lagring [(RA-GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) och Läs åtkomst geo-Zone-redundant lagring [(ra-GZRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) tillåter skrivskyddad åtkomst till data som replikeras till den sekundära regionen. 

#### <a name="blob-storage-and-file-storage"></a>Blob Storage och File Storage

Observera att Azure Files inte har stöd för Geo-redundant lagring (RA-GRS) och Read-Access Geo-redundant lagring (RA-GZRS) när du använder Azure Files med antingen SMB-eller 4,1 NFS-protokoll (för hands version) för att montera som säkerhets kopierings lagring. 

Om lagrings kravet för säkerhets kopiering är större än 5 TiB-Azure Files kräver [stora fildelnings funktioner](../../../storage/files/storage-files-planning.md) som inte stöder GRS eller GZRS REDUNDANS-LRS stöd. 

Azure Blob monterad med NFS 3,0-protokollet (för hands version) stöder för närvarande endast LRS-och ZRS-redundans.  

Azure-blobben som kon figurer ATS med ett alternativ för redundans kan monteras med Blobfuse.

#### <a name="recovery-services-vault"></a>Recovery Services-valv 

Ett Recovery Service-valv är en hanteringsenhet som lagrar återställningspunkter skapade över tid och tillhandahåller ett gränssnitt för säkerhetskopieringsrelaterade åtgärder. Dessa inkluderar säkerhetskopieringar på begäran, återställningar och att skapa säkerhetskopieringsprinciper.
Azure Backup hanterar automatiskt lagring för valvet. Du måste ange hur lagringen ska replikeras när den skapas. Den kan inte ändras efter att objekten har skyddats i valvet. För regional redundans väljer du inställningen Geo-redundant.

Om du vill återställa till en sekundär Azure- [kopplad region](../../../best-practices-availability-paired-regions.md) aktiverar du [återställnings funktionen över flera regioner](../../../backup/backup-create-rs-vault.md) . När återställning mellan regioner är aktive rad flyttas lagrings utrymmet från GRS till Geo-redundant lagring med Läs behörighet (RA-GRS). 

### <a name="azure-blob-storage"></a>Azure Blob Storage

Azure Blob Storage är en mycket kostnads effektiv, varaktig och säker molnbaserad lagrings tjänst som används för att lagra stora mängder ostrukturerade data och är ett perfekt medium som används för Oracle Database säkerhets kopieringar. Azure Blob Storage kan monteras till virtuella Azure Linux-datorer med hjälp av NFS v 3.0-protokollet eller Blobfuse (Linux-säkring).

#### <a name="azure-blob-blobfuse"></a>Azure Blob-Blobfuse

[Blobfuse](../../../storage/blobs/storage-how-to-mount-container-linux.md) är ett projekt med öppen källkod som har utvecklats för att tillhandahålla ett virtuellt fil system som backas upp av Azure Blob Storage. Det använder det lib-säkrade biblioteket med öppen källkod för att kommunicera med den Linux-säkra kernel-modulen och implementerar fil Systems åtgärder med hjälp av Azure Storage Blob REST-API: er. Blobfuse är för närvarande tillgängligt för Ubuntu-och CentOS/RedHat-distributioner. Den är också tillgänglig för Kubernetes med hjälp av [CSI-drivrutinen](https://github.com/kubernetes-sigs/blob-csi-driver). 

Medan allmänt förekommande i alla Azure-regioner och fungerar med alla typer av lagrings konton, inklusive generell användning v1/v2 och Azure Data Lake Store Gen2, har prestanda som erbjuds av Blobfuse visat sig vara mindre än alternativa protokoll som SMB eller NFS. För lämplighet som säkerhets kopiering av databasen rekommenderar vi att du använder SMB-eller [NFS](../../../storage/blobs/storage-how-to-mount-container-linux.md) -protokoll för att montera Azure Blob Storage. 

#### <a name="azure-blob-nfs-v30-preview"></a>Azure Blob NFS v 3.0 (för hands version)

Azure-stöd för Network File System (NFS) v 3.0-protokollet är nu i för hands version. [NFS](../../../storage/blobs/network-file-system-protocol-support.md) -stödet gör att Windows-och Linux-klienter kan montera en Blob Storage-behållare på en virtuell Azure-dator. 

NFS 3,0 offentlig för hands version stöder GPV2 lagrings konton med standard-nivå prestanda i följande regioner: 
- Australien, östra
- Sydkorea, centrala
- Södra centrala USA. 

För att säkerställa nätverks säkerheten måste det lagrings konto som används för NFS-montering finnas i ett VNet. Azure Active Directory (AD) säkerhet och åtkomst kontrol listor (ACL) stöds inte ännu i konton som har stöd för NFS 3,0-protokollet aktiverat.

### <a name="azure-files"></a>Azure Files

[Azure Files](../../../storage/files/storage-files-introduction.md) är ett molnbaserad, fullständigt hanterat distribuerat fil system som kan monteras på lokala eller molnbaserade Windows-, Linux-eller MacOS-klienter.

Azure Files erbjuder fullständigt hanterade fil resurser över plattformar i molnet som är tillgängliga via SMB-protokollet (Server Message Block) och NFS-protokollet (Network File System) (för hands version). Azure Files stöder för närvarande inte åtkomst till flera protokoll, så en resurs kan bara vara en NFS-resurs eller en SMB-resurs. Vi rekommenderar att du bestämmer vilket protokoll som passar bäst för dina behov innan du skapar Azure-filresurser.

Azure-filresurser kan också skyddas via Azure Backup till Recovery Services-valvet, vilket ger ett ytterligare skydds lager för säkerhets kopiering av Oracle-RMAN.

#### <a name="azure-files-nfs-v41-preview"></a>Azure Files NFS v 4.1 (för hands version)

Azure-filresurser kan monteras i Linux-distributioner med hjälp av Network File System (NFS) v 4.1-protokollet. I för hands versionen finns det ett antal begränsningar för vilka funktioner som stöds, vilka dokumenteras [här](../../../storage/files/storage-files-how-to-mount-nfs-shares.md). 

I för hands versionen Azure Files NFS v 4.1 också begränsas till följande [regioner](../../../storage/files/storage-files-how-to-mount-nfs-shares.md):
- USA, östra (LRS och ZRS)
- USA, östra 2
- USA, västra 2
- Europa, västra
- Sydostasien
- Storbritannien, södra
- Australien, östra

#### <a name="azure-files-smb-30"></a>Azure Files SMB 3,0

Azure-filresurser kan monteras i Linux-distributioner med SMB-kernel-klienten. CIFS-protokollet (common Internet File System), som är tillgängligt på Linux-distributioner, är en dialekt av SMB. När du monterar Azure Files SMB på virtuella Linux-datorer monteras det som CIFS-filsystem och CIFS-paketet måste vara installerat. 

Azure Files SMB är allmänt tillgängligt i alla Azure-regioner och visar samma prestanda egenskaper som NFS v 3.0-och v 4.1-protokoll, och det är för närvarande den rekommenderade metoden för att tillhandahålla lagrings medier för säkerhets kopiering till virtuella Azure Linux-datorer.  

Det finns två versioner av SMB som stöds, SMB 2,1 och SMB 3,0, med de sistnämnda rekommenderade som stöd för kryptering under överföring. Olika versioner av Linux-kärnan har dock olika stöd för SMB 2,1 och 3,0 och du bör kontrol lera tabellen [här](../../../storage/files/storage-how-to-use-files-linux.md) för att se till att programmet stöder SMB 3,0. 

Eftersom Azure Files har utformats för att vara en fil resurs tjänst för flera användare, finns det vissa egenskaper som du bör finjustera för att göra det mer lämpligt som ett lagrings medium för säkerhets kopior. Att stänga av cachelagring och ange användar-och grupp-ID: n för de filer som skapas rekommenderas.

## <a name="azure-netapp-files"></a>Azure NetApp Files

Tjänsten [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-solution-architectures.md) är en komplett lagrings lösning för Oracle-databaser på virtuella Azure-datorer. Med hög prestanda som bygger på en företags klass, högpresterande, data lagring med hög prestanda, stöder alla typer av arbets belastningar och är hög tillgängliga som standard. Tillsammans med dNFS-drivrutinen (Oracle Direct NFS) är Azure NetApp Files ett mycket optimerat lagrings lager för Oracle Database.

Azure NetApp Files tillhandahåller effektiva lagrings ögonblicks bilder som kopieras till det underliggande lagrings systemet som använder en mekanism för omdirigering på Skriv (rad). Även om kopior av ögonblicks bilder är mycket snabba att ta och återställa, fungerar de bara som en första rad försvars åtgärd, som kan hantera de flesta av de nödvändiga återställnings åtgärderna för en specifik organisation, vilket ofta brukar återställas från mänskligt fel. Ögonblicks bilds kopior är dock inte en fullständig säkerhets kopia. För att kunna ta med alla krav för säkerhets kopiering och återställning måste externa ögonblicks bilds repliker och/eller andra säkerhets kopior skapas i en fjärran slutet geografi för att skydda från regionalt avbrott. Läs den här [rapporten](https://www.netapp.com/pdf.html?item=/media/17105-tr4780pdf.pdf)om du vill veta mer om hur du använder NetApp-filer för Oracle-databaser på Azure.

## <a name="azure-backup-service"></a>Azure Backup tjänst

[Azure Backup](../../../backup/backup-overview.md) är en fullständigt hanterad PaaS som tillhandahåller enkla, säkra och kostnads effektiva lösningar för att säkerhetskopiera dina data och återställa dem från Microsoft Azure molnet. Azure Backup kan säkerhetskopiera och återställa lokala klienter, virtuella Azure-datorer, Azure Files resurser, samt SQL Server, Oracle, MySQL, PostreSQL och SAP HANA databaser på virtuella Azure-datorer. 

Med Azure Backup får du oberoende och isolerade säkerhetskopior, vilket skyddar originaldata från att förstöras oavsiktligt. Säkerhets kopior lagras i ett [Recovery Services valv](../../../backup/backup-azure-recovery-services-vault-overview.md) med inbyggd hantering av återställnings punkter. Konfiguration och skalbarhet är enkla, säkerhets kopieringar optimeras och du kan enkelt återställa efter behov. Den använder den underliggande kraftfulla och obegränsade skalningen av Azure-molnet för att leverera hög tillgänglighet utan underhåll eller övervakning. Azure Backup begränsar inte mängden inkommande eller utgående data som du överför, eller debiteras för de data som överförs, och data skyddas vid överföring och i vila. 

För att säkerställa hållbarhet Azure Backup erbjuder flera typer av replikering för att hålla dina säkerhets kopierings data hög tillgängliga.

- Lokalt redundant lagring [(LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage) replikerar dina data tre gånger (det skapas tre kopior av dina data) i en lagrings skalnings enhet i ett Data Center.
- Geo-redundant lagring [(GRS)](../../../storage/common/storage-redundancy.md#geo-redundant-storage) är standard alternativet och det rekommenderade alternativet för replikering. GRS replikerar dina data till en sekundär region (hundratals mil bort från den primära platsen för datakällan).

Ett valv som skapats med GRS-redundans omfattar alternativet att konfigurera funktionen för [återställning av kors region](../../../backup/backup-create-rs-vault.md#set-storage-redundancy) , vilket gör att du kan återställa data i en sekundär, Azure-kopplad region.

Azure Backup-tjänsten tillhandahåller ett [ramverk](../../../backup/backup-azure-linux-app-consistent.md) för att uppnå program konsekvens under säkerhets kopiering av virtuella Windows-och Linux-datorer för olika program som Oracle, MySQL, Mongo DB, SAP HANA och PostGreSQL, som kallas programkonsekventa ögonblicks bilder. Detta innebär att du anropar ett för skript (för att ta bort programmen) innan du tar en ögonblicks bild av diskarna och anropar efter skript (kommandon för att låsa upp programmen) när ögonblicks bilden har slutförts, för att returnera programmen till normalt läge. Exempel på för-skript och post-skript finns på GitHub, och det är ditt ansvar att skapa och underhålla dessa skript. Om det gäller Oracle måste databasen vara i archivelog-läge för att tillåta onlinesäkerhetskopiering, och lämpliga kommandon för databas start och slut säkerhets kopiering körs i de för-och efter-skript som du måste skapa och underhålla. 

Azure Backup tillhandahåller nu ett [förbättrat pre-script-och efter skript-ramverk](https://github.com/Azure/azure-linux-extensions/tree/master/VMBackup/main/workloadPatch/DefaultScripts), som för närvarande finns i för hands version, där Azure Backup-tjänsten tillhandahåller paketerade för-skript och post-skript för valda program. Azure Backup användare bara behöver ge programmet ett namn och sedan anropar Azure VM Backup automatiskt relevanta för skript och efter skript. Paketerade för-skript och post-skript kommer att behållas av Azure Backups teamet så att användarna kan garantera support, ägarskap och giltighet för dessa skript. För närvarande är de program som stöds för det förbättrade ramverket Oracle och MySQL, med fler program typer som förväntas i framtiden. Ögonblicks bilden är en fullständig kopia av lagringen och inte en stegvis eller kopia vid skrivning av ögonblicks bilder, så det är ett effektivt medium att återställa databasen från.

## <a name="next-steps"></a>Nästa steg

- [Skapa Oracle Database snabb start](oracle-database-quick-create.md)
- [Säkerhetskopiera Oracle Database till Azure Files](oracle-database-backup-azure-storage.md)
- [Säkerhetskopiera Oracle Database med hjälp av Azure Backup-tjänsten](oracle-database-backup-azure-backup.md)


