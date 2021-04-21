---
title: Vanliga frågor och svar för Azure Files | Microsoft Docs
description: Få svar på Azure Files vanliga frågor och svar. Du kan montera Azure-filresurser samtidigt i molnet eller lokala Windows-, Linux- eller macOS-distributioner.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 4d7123aa22d95e3e4c3850be775ddad96f28d280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785315"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Vanliga frågor och svar om Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade filresurser i molnet som är tillgängliga via [SMB-protokollet (industry-standard Server Message Block)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) och [NFS-protokollet (network file system) (förhandsversion).](https://en.wikipedia.org/wiki/Network_File_System) Du kan montera Azure-filresurser samtidigt i molnet eller lokala distributioner av Windows, Linux och macOS. Du kan också cachelagra Azure-filresurser på Windows Server-datorer med hjälp Azure File Sync för snabb åtkomst nära den plats där data används.

Den här artikeln ger svar på vanliga Azure Files om funktioner, inklusive användning av Azure File Sync med Azure Files. Om du inte ser svaret på din fråga kan du kontakta oss via följande kanaler (i eskalerande ordning):

1. Kommentarsavsnittet i den här artikeln.
2. [Microsoft Q&A question page for Azure Storage](/answers/topics/azure-file-storage.html).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft Support. Om du vill skapa en ny supportbegäran går  Azure Portal klickar du på knappen **Hjälp + support** på fliken Hjälp och väljer sedan **Ny supportbegäran.**

## <a name="general"></a>Allmänt
* <a id="why-files-useful"></a>
  **Hur är Azure Files användbart?**  
   Du kan använda Azure Files för att skapa filresurser i molnet, utan att behöva hantera omkostnaderna för en fysisk server, enhet eller enhet. Vi gör det monotona arbetet åt dig, inklusive att tillämpa OS-uppdateringar och ersätta dåliga diskar. Mer information om de scenarier som Azure Files kan hjälpa dig med finns i [Why Azure Files is useful](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Vad är olika sätt att komma åt filer i Azure Files?**  
    SMB-filresurser kan monteras på den lokala datorn med hjälp av SMB 3.0-protokollet, eller så kan du använda verktyg som [Storage Explorer för](https://storageexplorer.com/) att komma åt filer i filresursen. NFS-filresurser kan monteras på den lokala datorn genom att kopiera/klistra in skriptet som tillhandahålls av Azure Portal. Från ditt program kan du använda lagringsklientbibliotek, REST-API:er, PowerShell eller Azure CLI för att komma åt dina filer i Azure-filresursen.

* <a id="what-is-afs"></a>
  **Vad är Azure File Sync?**  
    Du kan Azure File Sync för att centralisera organisationens filresurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver. Azure File Sync omvandlar dina Windows Server-datorer till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, Network File System (NFS) och File Transfer Protocol Service (FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

* <a id="files-versus-blobs"></a>
  **Varför ska jag använda en Azure-filresurs jämfört med Azure Blob Storage för mina data?**  
    Azure Files och Azure Blob Storage erbjuder båda sätt att lagra stora mängder data i molnet, men de är användbara för något olika syften. 
    
    Azure Blob Storage är användbart för storskaliga, molnbaserade program som behöver lagra ostrukturerade data. För att maximera prestanda och skalning är Azure Blob Storage en enklare lagringsabstrahering än ett verkligt filsystem. Du kan bara komma åt Azure Blob Storage via REST-baserade klientbibliotek (eller direkt via det REST-baserade protokollet).

    Azure Files är specifikt ett filsystem. Azure Files har alla filabstraher som du känner till och gillar från flera års arbete med lokala operativsystem. Precis som Azure Blob Storage Azure Files ett REST-gränssnitt och REST-baserade klientbibliotek. Till skillnad från Azure Blob Storage Azure Files SMB- eller NFS-åtkomst till Azure-filresurser. Filresurser kan monteras direkt på Windows, Linux eller macOS, antingen lokalt eller på virtuella datorer i molnet, utan att skriva någon kod eller koppla några särskilda drivrutiner till filsystemet. Du kan också cachelagra Azure SMB-filresurser på lokala filservrar genom att använda Azure File Sync för snabb åtkomst, nära den plats där data används. 
   
    En mer detaljerad beskrivning av skillnaderna mellan Azure Files och Azure Blob Storage finns i [Introduction to the core Azure Storage services](../common/storage-introduction.md). Mer information om Azure Blob Storage finns i [Introduktion till Blob Storage.](../blobs/storage-blobs-introduction.md)

* <a id="files-versus-disks"></a>**Varför ska jag använda en Azure-filresurs i stället för Azure Disks?**  
    En disk i Azure Disks är helt enkelt en disk. Om du vill hämta värde från Azure-diskar måste du ansluta en disk till en virtuell dator som körs i Azure. Azure-diskar kan användas för allt som du använder en disk för på en lokal server. Du kan använda den som operativsystemdisk, som växlingsutrymme för ett operativsystem eller som dedikerad lagring för ett program. En intressant användning för Azure Disks är att skapa en filserver i molnet som ska användas på samma platser där du kan använda en Azure-filresurs. Att distribuera en filserver i Azure Virtual Machines är ett högpresterande sätt att få fillagring i Azure när du behöver distributionsalternativ som för närvarande inte stöds av Azure Files. 

    Men att köra en filserver med Azure Disks som backend-lagring är vanligtvis mycket dyrare än att använda en Azure-filresurs, av några skäl. Förutom att betala för disklagring måste du också betala för kostnaden för att köra en eller flera virtuella Azure-datorer. För det andra måste du också hantera de virtuella datorer som används för att köra filservern. Du ansvarar till exempel för OS-uppgraderingar. Om du slutligen kräver att data cachelagras lokalt är det upp till dig att konfigurera och hantera replikeringstekniker, till exempel Distributed File System Replication (DFSR), för att göra detta.

    En metod för att få ut det bästa av både Azure Files och en filserver som finns i Azure Virtual Machines (förutom att använda Azure-diskar som serverlagring) är att installera Azure File Sync på en filserver som finns på en virtuell dator i molnet. Om Azure-filresursen finns i samma region som filservern kan du aktivera molnnivåindelning och ange maximalt utrymmesutrymmesvolym (99 %). Detta säkerställer minimal duplicering av data. Du kan också använda alla program som du vill med dina filservrar, till exempel program som kräver stöd för NFS-protokoll.

    Information om ett alternativ för att konfigurera en filserver med hög prestanda och hög tillgänglighet i Azure finns i Distribuera [IaaS VM-gästkluster i Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). En mer detaljerad beskrivning av skillnaderna mellan Azure Files och Azure Disks finns i Introduktion till kärntjänsterna [Azure Storage .](../common/storage-introduction.md) Mer information om Azure-diskar finns i [Översikt över Azure Managed Disks.](../../virtual-machines/managed-disks-overview.md)

* <a id="get-started"></a>
  **Hur gör jag för att komma igång med Azure Files?**  
   Det är enkelt Azure Files att komma igång med Azure Files. Först skapar du [antingen en SMB-filresurs](storage-how-to-create-file-share.md) eller en How to create an NFS share (Så här skapar du en [NFS-resurs)](storage-files-how-to-create-nfs-shares.md)och monterar den sedan i önskat operativsystem: 

  * [Montera en SMB-resurs i Windows](storage-how-to-use-files-windows.md)
  * [Montera en SMB-resurs i Linux](storage-how-to-use-files-linux.md)
  * [Montera en SMB-resurs i macOS](storage-how-to-use-files-mac.md)
  * [Montera en NFS-filresurs](storage-files-how-to-mount-nfs-shares.md)

    En mer detaljerad guide om hur du distribuerar en Azure-filresurs för att ersätta produktionsfilresurser i din organisation finns i Planera för [en Azure Files distribution](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Vilka alternativ för lagringsredundans stöds av Azure Files?**  
    För närvarande Azure Files stöd för lokalt redundant lagring (LRS), zonredundant lagring (ZRS), geo-redundant lagring (GRS) och geo-zonredundant lagring (GZRS). Azure Files premiumnivån stöder för närvarande endast LRS och ZRS.

* <a id="tier-options"></a>
  **Vilka lagringsnivåer stöds i Azure Files?**  
    Azure Files två lagringsnivåer: premium och standard. Standardfilresurser skapas i GPv1- eller GPv2-konton (generell användning) och premiumfilresurser skapas i FileStorage-lagringskonton. Läs mer om hur du skapar [standardfilresurser och](storage-how-to-create-file-share.md) [premiumfilresurser.](./storage-how-to-create-file-share.md) 
    
    > [!NOTE]
    > Du kan inte skapa Azure-filresurser från Blob Storage-konton eller *Premium* GPv1- eller GPv2-lagringskonton. Azure-standardfilresurser måste endast skapas *i allmänna standardkonton,* och Azure-premiumfilresurser måste endast skapas i FileStorage-lagringskonton.  Premium-lagringskonton för generell användning (GPv1 och GPv2) är endast avsedda för premium-sidblobar. 

* <a id="file-locking"></a>
  **Stöder Azure Files fillåsning?**  
    Ja, Azure Files stöder SMB-/Windows-fillåsning helt. [Mer information finns i](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Jag vill verkligen se en specifik funktion som lagts till i Azure Files. Kan du lägga till det?**  
    Teamet Azure Files är intresserade av att höra all feedback du har om vår tjänst. Rösta på funktionsförfrågningar på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Vi ser fram emot att ge dig många nya funktioner.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Vilka regioner stöds för Azure File Sync?**  
    Listan över tillgängliga regioner finns i avsnittet [Regionstillgänglighet i](../file-sync/file-sync-planning.md#azure-file-sync-region-availability) guiden för Azure File Sync planering. Vi kommer kontinuerligt att lägga till stöd för ytterligare regioner, inklusive icke-offentliga regioner.

* <a id="cross-domain-sync"></a>
  **Kan jag ha domän-ansluten och icke-domän-ansluten servrar i samma synkroniseringsgrupp?**  
    Ja. En synkroniseringsgrupp kan innehålla serverslutpunkter som har olika Active Directory-medlemskap, även om de inte är domän-ansluten. Även om den här konfigurationen tekniskt sett fungerar rekommenderar vi inte detta som en typisk konfiguration eftersom åtkomstkontrollistor (ACL:er) som har definierats för filer och mappar på en server kanske inte kan tillämpas av andra servrar i synkroniseringsgruppen. För bästa resultat rekommenderar vi att du synkroniserar mellan servrar som finns i samma Active Directory-skog, mellan servrar som finns i olika Active Directory-skogar men som har etablerade förtroenderelationer eller mellan servrar som inte finns i en domän. Vi rekommenderar att du undviker att använda en blandning av dessa konfigurationer.

* <a id="afs-change-detection"></a>
  **Jag har skapat en fil direkt i min Azure-filresurs med hjälp av SMB eller i portalen. Hur lång tid tar det innan filen synkroniseras med servrarna i synkroniseringsgruppen?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Hur lång tid tar det för Azure File Sync att ladda upp 1TiB data?**
  
    Prestanda varierar beroende på miljöinställningar, konfiguration och om det här är en inledande synkronisering eller en pågående synkronisering. Mer information finns i [Azure File Sync prestandamått](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Vad händer om samma fil ändras på två servrar ungefär samtidigt?**  
    Azure File Sync använder en enkel konfliktlösningsstrategi: vi behåller båda ändringarna av filer som ändras i två slutpunkter samtidigt. Den senast skrivna ändringen behåller det ursprungliga filnamnet. Den äldre filen (bestäms av LastWriteTime) har slutpunktsnamnet och konfliktnumret tillagt i filnamnet. För serverslutpunkter är slutpunktsnamnet namnet på servern. För molnslutpunkter är slutpunktsnamnet **Cloud**. Namnet följer den här taxonomin: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Till exempel skulle den första konflikten med CompanyReport.docx bli CompanyReport-CentralServer.docx CentralServer är där den äldre skriven inträffade. Den andra konflikten skulle heta CompanyReport-CentralServer-1.docx. Azure File Sync har stöd för 100 konfliktfiler per fil. När det maximala antalet konfliktfiler har nåtts kan inte filen synkroniseras förrän antalet konfliktfiler är mindre än 100.

* <a id="afs-storage-redundancy"></a>
  **Stöds geo-redundant lagring för Azure File Sync?**  
    Ja, Azure Files stöder både lokalt redundant lagring (LRS) och geo-redundant lagring (GRS). Om du initierar en redundans för lagringskontot mellan parkopplade regioner från ett konto som konfigurerats för GRS, rekommenderar Microsoft att du behandlar den nya regionen som en säkerhetskopia av data endast. Azure File Sync börjar inte automatiskt synkronisera med den nya primära regionen. 

* <a id="sizeondisk-versus-size"></a>
  **Varför matchar inte egenskapen *Storlek på disk för* en fil egenskapen Storlek när *du* har använt Azure File Sync?**  
  Se [Förstå Azure File Sync för molnnivåer.](../file-sync/file-sync-cloud-tiering-overview.md#tiered-vs-locally-cached-file-behavior)

* <a id="is-my-file-tiered"></a>
  **Hur vet jag om en fil har nivåindelats?**  
  Se [Förstå molnnivåindelad .](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-check-if-your-files-are-being-tiered)

* <a id="afs-recall-file"></a>**En fil som jag vill använda har nivåindelats. Hur kan jag återkalla filen till disken för att använda den lokalt?**  
  Se [Förstå molnnivåindelad .](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)

* <a id="afs-force-tiering"></a>
  **Hur gör jag för att du tvinga en fil eller katalog att nivåindelad?**  
  Se [Förstå molnnivåindelad .](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-force-a-file-or-directory-to-be-tiered)

* <a id="afs-effective-vfs"></a>
  **Hur tolkas *ledigt volymutrymme* när jag har flera serverslutpunkter på en volym?**  
  Se [Förstå molnnivåindelad .](../file-sync/file-sync-cloud-tiering-policy.md#multiple-server-endpoints-on-a-local-volume)
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Jag har molnnivåindelad, varför finns det nivåindelade filer på serverns slutpunktsplats?**  
    Det finns två orsaker till varför nivåindelade filer kan finnas på serverns slutpunktsplats:

    - När du lägger till en ny serverslutpunkt i en befintlig synkroniseringsgrupp visas filerna som nivåindelade tills de hämtas lokalt om du väljer alternativet för att återkalla namnområdet först eller återkalla namnområdet i det första nedladdningsläget. Undvik detta genom att välja alternativet undvik nivåindelade filer för det första nedladdningsläget. Om du vill återkalla filer manuellt använder [du cmdleten Invoke-StorageSyncFileRecall.](../file-sync/file-sync-how-to-manage-tiered-files.md#how-to-recall-a-tiered-file-to-disk)

    - Om molnnivåindelade aktiverades på serverslutpunkten och sedan inaktiverades förblir filerna nivåindelade tills de nås.

* <a id="afs-tiered-files-not-showing-thumbnails"></a>
  **Varför visas inte mina nivåindelade filer miniatyrer eller förhandsgranskningar i Utforskaren?**  
    För nivåindelade filer visas inte miniatyrer och förhandsgranskningar på serverslutpunkten. Det här beteendet är förväntat eftersom cachefunktionen för miniatyrer i Windows avsiktligt hoppar över läsning av filer med offlineattributet. När molnnivåindelad lagring är aktiverat kan läsning via nivåindelade filer göra att de laddas ned (återkallas).

    Det här beteendet är inte specifikt för Azure File Sync. Utforskaren visar ett "grått X" för alla filer som har offlineattributet inställt. Du ser X-ikonen vid åtkomst till filer via SMB. En detaljerad förklaring av det här beteendet finns i [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

    Frågor om hur du hanterar nivåindelade filer finns i [Så här hanterar du nivåindelade filer.](../file-sync/file-sync-how-to-manage-tiered-files.md)

* <a id="afs-files-excluded"></a>
  **Vilka filer eller mappar undantas automatiskt av Azure File Sync?**  
  Se [Filer som hoppas över.](../file-sync/file-sync-planning.md#files-skipped)

* <a id="afs-os-support"></a>
  **Kan jag använda Azure File Sync med antingen Windows Server 2008 R2, Linux eller min NAS-enhet (network-attached storage)?**  
    För närvarande Azure File Sync endast Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2. Just nu har vi inga andra planer som vi kan dela, men vi är öppna för att stödja ytterligare plattformar baserat på kundernas efterfrågan. Berätta för oss på [Azure Files UserVoice vilka](https://feedback.azure.com/forums/217298-storage/category/180670-files) plattformar du vill att vi ska stödja.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Varför finns nivåindelade filer utanför serverslutpunktens namnområde?**  
    Innan Azure File Sync agentversion 3 Azure File Sync blockerat flytten av nivåindelade filer utanför serverslutpunkten men på samma volym som serverslutpunkten. Kopieringsåtgärder, flytt av filer som inte är nivåindelade och flytt av nivåindelade till andra volymer påverkades inte. Orsaken till det här beteendet var det implicita antagandet att Utforskaren och andra Windows-API:er har att flyttåtgärder på samma volym är (nästan) omedelbara namnbytesåtgärder. Det innebär att flyttar gör Utforskaren eller andra flyttmetoder (till exempel kommandoraden eller PowerShell) inte svarar medan Azure File Sync återkallar data från molnet. Från och [Azure File Sync agentversion 3.0.12.0](../file-sync/file-sync-release-notes.md#supported-versions)kan Azure File Sync flytta en nivåindelad fil utanför serverslutpunkten. Vi undviker de negativa effekter som tidigare nämnts genom att låta den nivåindelade filen finnas som en nivåindelad fil utanför serverslutpunkten och sedan återkalla filen i bakgrunden. Det innebär att förflyttningar på samma volym är omedelbara och vi gör allt arbete för att återkalla filen till disken när flytten har slutförts. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Jag har problem med att Azure File Sync på min server (synkronisering, molnnivåindelad osv.). Ska jag ta bort och återskapa min serverslutpunkt?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Kan jag flytta tjänsten för synkronisering av lagring och/eller lagringskontot till en annan resursgrupp, prenumeration eller Azure AD-klient?**  
   Ja, tjänsten för synkronisering av lagring och/eller lagringskontot kan flyttas till en annan resursgrupp, prenumeration eller Azure AD-klientorganisation. När tjänsten för synkronisering av lagring eller lagringskontot har flyttats måste du ge programmet Microsoft.StorageSync åtkomst till lagringskontot (se Kontrollera att Azure File Sync har åtkomst till [lagringskontot](../file-sync/file-sync-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > När du skapar molnslutpunkten måste tjänsten för synkronisering av lagring och lagringskontot finnas i samma Azure AD-klientorganisation. När molnslutpunkten har skapats kan tjänsten för synkronisering av lagring och lagringskontot flyttas till olika Azure AD-klienter.
    
* <a id="afs-ntfs-acls"></a>
  **Bevarar Azure File Sync NTFS-ACL:er på katalog-/filnivå tillsammans med data som lagras i Azure Files?**

    Från och med den 24 februari 2020 bevaras nya och befintliga ACL:er som nivåindelats av Azure File Sync i NTFS-format, och ACL-ändringar som görs direkt till Azure-filresursen synkroniseras till alla servrar i synkroniseringsgruppen. Alla ändringar av ACL:er som görs Azure Files synkroniseras via Azure-filsynkronisering. När du kopierar data till Azure Files bör du använda ett kopieringsverktyg som stöder nödvändig "återgivning" för att kopiera attribut, tidsstämplar och ACL:er till en Azure-filresurs – antingen via SMB eller REST. När du använder Azure-kopieringsverktyg, till exempel AzCopy, är det viktigt att du använder den senaste versionen. Titta i [tabellen med filkopieringsverktyg](storage-files-migration-overview.md#file-copy-tools) för att få en översikt över Azure-kopieringsverktygen för att se till att du kan kopiera alla viktiga metadata för en fil.

    Om du har aktiverat Azure Backup filsynkronisering av hanterade filresurser kan fil-ACL:er fortsätta att återställas som en del av arbetsflödet för säkerhetskopieringsåterställning. Detta fungerar antingen för hela resursen eller enskilda filer/kataloger.

    Om du använder ögonblicksbilder som en del av den själv hanterade säkerhetskopieringslösningen för filresurser som hanteras av filsynkronisering kanske dina ACL:er inte återställs korrekt till NTFS-ACL:er om ögonblicksbilderna togs före den 24 februari 2020. Om detta inträffar kan du kontakta Azure Support.

* <a id="afs-lastwritetime"></a>
  **Synkroniserar Azure File Sync LastWriteTime för kataloger?**  
    Nej, Azure File Sync synkroniserar inte LastWriteTime för kataloger. Det här är avsiktligt.
    
## <a name="security-authentication-and-access-control"></a>Säkerhet, autentisering och åtkomstkontroll
* <a id="ad-support"></a>
**Stöds identitetsbaserad autentisering och åtkomstkontroll av Azure Files?**  
    
    Ja, Azure Files stöder identitetsbaserad autentisering och åtkomstkontroll. Du kan välja ett av två sätt att använda identitetsbaserad åtkomstkontroll: lokal Active Directory Domain Services eller Azure Active Directory Domain Services (Azure AD DS). Lokala datorer Active Directory Domain Services (AD DS) stöder autentisering med ad ds domän-ansluten datorer, antingen lokalt eller i Azure, för att komma åt Azure-filresurser via SMB. Azure AD DS-autentisering via SMB för Azure Files kan Azure AD DS domän-ansluten virtuella Windows-datorer att komma åt resurser, kataloger och filer med hjälp av Azure AD-autentiseringsuppgifter. Mer information finns i [Översikt över Azure Files för identitetsbaserad autentisering för SMB-åtkomst.](storage-files-active-directory-overview.md) 

    Azure Files två ytterligare sätt att hantera åtkomstkontroll:

    - Du kan använda signaturer för delad åtkomst (SAS) för att generera token som har specifika behörigheter och som är giltiga under ett angivet tidsintervall. Du kan till exempel generera en token med skrivskyddade åtkomst till en specifik fil som har en förfallominut på 10 minuter. Alla som har token medan token är giltig har skrivskyddade åtkomst till filen under de 10 minuterna. Signaturnycklar för delad åtkomst stöds endast via REST API eller i klientbibliotek. Du måste montera Azure-filresursen över SMB med hjälp av lagringskontonycklarna.

    - Azure File Sync bevarar och replikerar alla godtyckliga ACL:er eller DACL:er (oavsett om de är Active Directory-baserade eller lokala) till alla serverslutpunkter som den synkroniserar till. 
    
    Du kan referera till [Auktorisera åtkomst till Azure Storage](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för en omfattande representation av alla protokoll som stöds på Azure Storage tjänster. 
    
* <a id="encryption-at-rest"></a>
**Hur kan jag se till att min Azure-filresurs är krypterad i vila?**  

    Ja. Mer information finns i [Azure Storage Service Encryption](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hur kan jag ge åtkomst till en viss fil med hjälp av en webbläsare?**  

    Du kan använda signaturer för delad åtkomst för att generera token som har specifika behörigheter och som är giltiga under ett angivet tidsintervall. Du kan till exempel generera en token som ger skrivskyddade åtkomst till en viss fil under en viss tidsperiod. Alla som har URL:en kan komma åt filen direkt från valfri webbläsare medan token är giltig. Du kan enkelt skapa en signaturnyckel för delad åtkomst från ett användargränssnitt som Storage Explorer.

* <a id="file-level-permissions"></a>
**Går det att ange skrivskyddade eller skrivskyddade behörigheter för mappar i resursen?**  

    Om du monterar filresursen med hjälp av SMB har du ingen kontroll på mappnivå över behörigheter. Men om du skapar en signatur för delad åtkomst med hjälp av REST API eller klientbibliotek kan du ange skrivskyddad eller skrivskyddad behörighet för mappar i resursen.

* <a id="ip-restrictions"></a>
**Kan jag implementera IP-begränsningar för en Azure-filresurs?**  

    Ja. Åtkomsten till din Azure-filresurs kan begränsas på lagringskontonivå. Mer information finns i [Konfigurera Azure Storage brandväggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Vilka principer för dataefterlevnad stöder Azure Files dataefterlevnad?**  

   Azure Files körs ovanpå samma lagringsarkitektur som används i andra lagringstjänster i Azure Storage. Azure Files samma principer för dataefterlevnad som används i andra Azure-lagringstjänster. Mer information om Azure Storage dataefterlevnad finns [i Azure Storage efterlevnadserbjudanden](../common/storage-compliance-offerings.md)och gå till [Microsoft Trust Center.](https://microsoft.com/trustcenter/default.aspx)

* <a id="file-auditing"></a>
**Hur granskar jag filåtkomst och ändringar i Azure Files?**

  Det finns två alternativ som tillhandahåller granskningsfunktioner för Azure Files:
  - Om användarna använder Azure-filresursen direkt [kan Azure Storage (förhandsversion)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) användas för att spåra filändringar och användaråtkomst. Dessa loggar kan användas i felsökningssyfte och begäranden loggas efter bästa prestanda.
  - Om användarna använder Azure-filresursen via en Windows Server som har Azure File Sync-agenten installerad använder du en granskningsprincip eller en produkt från tredje part för att spåra filändringar och användaråtkomst på Windows Server. [](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS& Azure AD DS autentisering
* <a id="ad-support-devices"></a>
**Stöder Azure Files Azure Active Directory Domain Services (Azure AD DS) SMB-åtkomst med Azure AD-autentiseringsuppgifter från enheter som är anslutna till eller registrerade med Azure AD?**

    Nej, det här scenariot stöds inte.

* <a id="ad-vm-subscription"></a>
**Kan jag komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter från en virtuell dator under en annan prenumeration?**

    Om prenumerationen där filresursen distribueras är associerad med samma Azure AD-klient som den Azure AD DS-distribution som den virtuella datorn är domän-ansluten till kan du komma åt Azure-filresurser med samma Autentiseringsuppgifter för Azure AD. Begränsningen gäller inte för prenumerationen utan för den associerade Azure AD-klientorganisationen.
    
* <a id="ad-support-subscription"></a>
**Kan jag aktivera antingen Azure AD DS eller lokal AD DS-autentisering för Azure-filresurser med hjälp av en Azure AD-klient som skiljer sig från Azure-filresursens primära klientorganisation?**

    Nej, Azure Files stöder Azure AD DS eller lokal AD DS-integrering med en Azure AD-klient som finns i samma prenumeration som filresursen. Endast en prenumeration kan associeras med en Azure AD-klientorganisation. Den här begränsningen gäller både Azure AD DS lokala AD DS-autentiseringsmetoder. När du använder lokal AD DS för autentisering måste AD DS-autentiseringskontot synkroniseras med [det Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) som lagringskontot är associerat med.

* <a id="ad-linux-vms"></a>
**Stöder Azure AD DS lokala AD DS-autentisering för Azure-filresurser virtuella Linux-datorer?**

    Nej, autentisering från virtuella Linux-datorer stöds inte.

* <a id="ad-aad-smb-afs"></a>
**Har filresurser som hanteras Azure File Sync stöd för antingen Azure AD DS eller lokal AD DS-autentisering?**

    Ja, du kan aktivera Azure AD DS eller lokal AD DS-autentisering på en filresurs som hanteras av Azure File Sync. Ändringar i katalogen/filens NTFS-ACL:er på lokala filservrar nivåindelade till Azure Files och vice versa.

* <a id="ad-aad-smb-files"></a>
**Hur kontrollerar jag om jag har aktiverat AD DS-autentisering på mitt lagringskonto och hämtar domäninformationen?**

    Anvisningar finns [här.](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled)

* <a id=""></a>
**Har Azure Files Azure AD-autentisering stöd för virtuella Linux-datorer?**

    Nej, autentisering från virtuella Linux-datorer stöds inte.

* <a id="ad-multiple-forest"></a>
**Stöder lokal AD DS-autentisering för Azure-filresurser integrering med en AD DS-miljö med flera skogar?**    

    Azure Files lokal AD DS-autentisering integreras endast med skogen för domäntjänsten som lagringskontot är registrerat i. För att stödja autentisering från en annan skog måste din miljö ha ett skogsförtroende som är korrekt konfigurerat. Sättet som Azure Files registreras i AD DS på nästan samma sätt som en vanlig filserver, där den skapar en identitet (dator- eller tjänstinloggningskonto) i AD DS för autentisering. Den enda skillnaden är att det registrerade SPN:et för lagringskontot slutar med "file.core.windows.net" som inte matchar domänsuffixet. Kontakta domänadministratören för att se om någon uppdatering av routningsprincipen för suffix krävs för att aktivera autentisering med flera skogar på grund av det olika domänsuffixet. Vi tillhandahåller ett exempel nedan för att konfigurera suffixroutningsprincipen.
    
    Exempel: När användare i skog A-domän vill nå en filresurs med lagringskontot registrerat mot en domän i skog B, fungerar detta inte automatiskt eftersom tjänstens huvudnamn för lagringskontot inte har ett suffix som matchar suffixet för någon domän i skog A. Vi kan åtgärda det här problemet genom att manuellt konfigurera en suffixroutningsregel från skog A till skog B för ett anpassat suffix som är "file.core.windows.net".
    Först måste du lägga till ett nytt anpassat suffix i skog B. Kontrollera att du har rätt administratörsbehörighet för att ändra konfigurationen och följ sedan dessa steg:   
    1. Logga in på en datordomän som är ansluten till skog B
    2.  Öppna konsolen "Active Directory-domäner och förtroenden"
    3.  Högerklicka på Active Directory-domäner och -förtroenden
    4.  Klicka på "Egenskaper"
    5.  Klicka på "Lägg till"
    6.  Lägg till "file.core.windows.net" som UPN-suffix
    7.  Klicka på "Använd" och sedan på "OK" för att stänga guiden
    
    Lägg sedan till suffixdirigeringsregeln i skog A så att den omdirigeras till skog B.
    1.  Logga in på en datordomän som är ansluten till skog A
    2.  Öppna konsolen "Active Directory-domäner och förtroenden"
    3.  Högerklicka på den domän som du vill komma åt filresursen och klicka sedan på fliken "Förtroenden" och välj skog B-domän från utgående förtroenden. Om du inte har konfigurerat förtroende mellan de två skogarna måste du först konfigurera förtroendet
    4.  Klicka på "Egenskaper..." sedan "Name Suffix Routing" (Namnsuffixroutning)
    5.  Kontrollera om suffixet "*.file.core.windows.net" visas. Annars klickar du på Uppdatera
    6.  Välj "*.file.core.windows.net" och klicka sedan på "Aktivera" och "Tillämpa"

* <a id=""></a>
**Vilka regioner är tillgängliga för Azure Files AD DS-autentisering?**

    Mer information [finns i regional tillgänglighet för AD DS.](storage-files-identity-auth-active-directory-enable.md#regional-availability)
    
* <a id="ad-aad-smb-afs"></a>
**Kan jag använda Azure Files Active Directory-autentisering (AD) på filresurser som hanteras av Azure File Sync?**

    Ja, du kan aktivera AD-autentisering på en filresurs som hanteras av Azure File Sync. Ändringar i katalogen/filens NTFS-ACL:er på lokala filservrar nivåindelade till Azure Files och vice versa.

* <a id="ad-aad-smb-files"></a>
**Finns det någon skillnad i att skapa ett datorkonto eller ett tjänstinloggningskonto som representerar mitt lagringskonto i AD?**

    Att skapa [antingen ett datorkonto](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standard) eller ett tjänstinloggningskonto har ingen skillnad på hur autentiseringen skulle fungera med Azure Files. [](/windows/win32/ad/about-service-logon-accounts) Du kan välja själv hur ett lagringskonto ska representeras som en identitet i AD-miljön. Standardvärdet för DomainAccountType som angetts Join-AzStorageAccountForAuth cmdleten är datorkontot. Förfalloåldern för lösenord som konfigurerats i din AD-miljö kan dock vara annorlunda för dator- eller tjänstinloggningskontot och du måste ta hänsyn till det för Uppdatera lösenordet för din lagringskontoidentitet i [AD](./storage-files-identity-ad-ds-update-password.md).
 
* <a id="ad-support-rest-apis"></a>
**Finns det REST-API:er som stöder Windows-ACL:er för Get/Set/Copy directory/file?**

    Ja, vi stöder REST API:er som hämtar, anger eller kopierar NTFS-ACL:er för kataloger eller filer när du använder [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (eller senare) REST API. Vi stöder även bestående Windows-ACL:er i REST-baserade verktyg: [AzCopy v10.4+](https://github.com/Azure/azure-storage-azcopy/releases).

* <a id="ad-support-rest-apis"></a>
**Hur tar jag bort cachelagrade autentiseringsuppgifter med lagringskontonyckel och tar bort befintliga SMB-anslutningar innan du initierar en ny anslutning med Azure AD- eller AD-autentiseringsuppgifter?**

    Du kan följa de två stegen nedan för att ta bort de sparade autentiseringsuppgifter som är associerade med lagringskontonyckeln och ta bort SMB-anslutningen: 
    1. Kör cmdleten nedan i Windows Cmd.exe för att ta bort autentiseringssuppgifter. Om du inte hittar någon innebär det att du inte har bevarat autentiseringssuppgifter och kan hoppa över det här steget.
    
       cmdkey /delete:Domain:target=storage-account-name.file.core.windows.net
    
    2. Ta bort den befintliga anslutningen till filresursen. Du kan ange monteringssökvägen som antingen den monterade enhetsbeteckningen eller storage-account-name.file.core.windows.net sökvägen.
    
       net use <drive-letter/share-path> /delete

## <a name="network-file-system"></a>Nätverksfilsystem

* <a id="when-to-use-nfs"></a>
**När ska jag använda Azure Files NFS?**

    Se [NFS-resurser (förhandsversion)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Hur gör jag för att säkerhetskopierade data som lagras i NFS-resurser?**

    Säkerhetskopiering av data på NFS-resurser kan antingen samordnas med välbekanta verktyg som rsync eller produkter från någon av våra tredjepartspartner för säkerhetskopiering. Flera säkerhetskopieringspartner, inklusive [Commvault,](https://documentation.commvault.com/commvault/v11/article?p=92634.htm) [Veeam](https://www.veeam.com/blog/?p=123438)och [Veritas,](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) var en del av vår första förhandsversion och har utökat sina lösningar så att de fungerar med både SMB 3.0 och NFS 4.1 för Azure Files.

* <a id="migrate-nfs-data"></a>
**Kan jag migrera befintliga data till en NFS-resurs?**

    Inom en region kan du använda standardverktyg som scp, rsync eller SSHFS för att flytta data. Eftersom Azure Files NFS kan nås från flera beräkningsinstanser samtidigt kan du förbättra kopieringshastigheten med parallella uppladdningar. Om du vill hämta data utanför en region använder du ett VPN eller en ExpressRoute för att montera till filsystemet från ditt lokala datacenter.

## <a name="on-premises-access"></a>Lokal åtkomst

* <a id="port-445-blocked"></a>
**Internetleverantören eller IT-leverantören blockerar port 445 som inte Azure Files montering. Vad ska jag göra?**

    Du kan lära dig [mer om olika sätt att komma runt blockerad port 445 här.](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked) Azure Files endast anslutningar som använder SMB 3.0 (med krypteringsstöd) utanför regionen eller datacentret. SMB 3.0-protokollet har infört många säkerhetsfunktioner, inklusive kanalkryptering som är mycket säker att använda via Internet. Det är dock möjligt att port 445 har blockerats på grund av historiska orsaker till sårbarheter i lägre SMB-versioner. I bästa fall bör porten endast blockeras för SMB 1.0-trafik och SMB 1.0 ska vara avstängd på alla klienter.

* <a id="expressroute-not-required"></a>
**Måste jag använda Azure ExpressRoute för att ansluta till Azure Files eller använda Azure File Sync lokalt?**  

    Nej. ExpressRoute krävs inte för åtkomst till en Azure-filresurs. Om du monterar en Azure-filresurs direkt lokalt är allt som krävs att port 445 (utgående TCP) är öppen för Internetåtkomst (det här är den port som SMB använder för att kommunicera). Om du använder en Azure File Sync allt som krävs är port 443 (utgående TCP) för HTTPS-åtkomst (ingen SMB krävs). Du kan dock *använda* ExpressRoute med något av dessa åtkomstalternativ.

* <a id="mount-locally"></a>
**Hur monterar jag en Azure-filresurs på min lokala dator?**  

    Du kan montera filresursen med hjälp av SMB-protokollet om port 445 (utgående TCP) är öppen och klienten stöder SMB 3.0-protokollet (till exempel om du använder Windows 10 eller Windows Server 2016). Om port 445 blockeras av din organisations princip eller av internetleverantören kan du använda Azure File Sync för att komma åt Din Azure-filresurs.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hur gör jag för att du min Azure-filresurs?**  
    Du kan använda [periodiska resursögonblicksbilder](storage-snapshots-files.md) för att skydda mot oavsiktliga borttagningar. Du kan också använda AzCopy, Robocopy eller ett säkerhetskopieringsverktyg från tredje part som kan säkerhetskopiera en monterad filresurs. Azure Backup erbjuder säkerhetskopiering av Azure Files. Lär dig mer om [hur du kan eftersom azure-filresurser Azure Backup](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Resursögonblicksbilder

### <a name="share-snapshots-general"></a>Resursögonblicksbilder: Allmänt
* <a id="what-are-snaphots"></a>
**Vad är ögonblicksbilder av filresurs?**  
    Du kan använda ögonblicksbilder av Azure-filresurser för att skapa en skrivskyddad version av dina filresurser. Du kan också använda Azure Files för att kopiera en tidigare version av ditt innehåll tillbaka till samma resurs, till en alternativ plats i Azure eller lokalt för fler ändringar. Mer information om resursögonblicksbilder finns i Översikt [över resursögonblicksbild.](storage-snapshots-files.md)

* <a id="where-are-snapshots-stored"></a>
**Var lagras mina resursögonblicksbilder?**  
    Resursögonblicksbilder lagras i samma lagringskonto som filresursen.

* <a id="snapshot-consistency"></a>
**Är resursögonblicksbilder program konsekventa?**  
    Nej, resursögonblicksbilder är inte program konsekventa. Användaren måste tömma skrivningar från programmet till resursen innan de tar ögonblicksbilden av resursen.

* <a id="snapshot-limits"></a>
**Finns det begränsningar för hur många ögonblicksbilder av resursen som jag kan använda?**  
    Ja. Azure Files kan behålla högst 200 resursögonblicksbilder. Resursögonblicksbilder räknas inte mot resurskvoten, så det finns ingen gräns per resurs för det totala utrymme som används av alla resursögonblicksbilder. Begränsningar för lagringskonto gäller fortfarande. Efter 200 resursögonblicksbilder måste du ta bort äldre ögonblicksbilder för att skapa nya resursögonblicksbilder.

* <a id="snapshot-cost"></a>
**Hur mycket kostar det att dela ögonblicksbilder?**  
    Standardtransaktions- och standardlagringskostnaden gäller för ögonblicksbilder. Ögonblicksbilder är inkrementella till sin natur. Basögonblicksbild är själva resursen. Alla efterföljande ögonblicksbilder är inkrementella och lagrar endast differensen från den tidigare ögonblicksbilden. Det innebär att deltaändringarna som visas på fakturan blir minimala om din arbetsbelastningsomsättning är minimal. Se [prissättningssidan](https://azure.microsoft.com/pricing/details/storage/files/) för Standard Azure Files prisinformation. I dag kan du titta på storleken som förbrukas av resursögonblicksbilder genom att jämföra den fakturerade kapaciteten med använd kapacitet. Vi arbetar med verktyg för att förbättra rapporteringen.

* <a id="ntfs-acls-snaphsots"></a>
**Finns NTFS-ACL:er på kataloger och filer kvar i resursögonblicksbilder?**  
    NTFS-ACL:er för kataloger och filer bevaras i resursögonblicksbilder.

### <a name="create-share-snapshots"></a>Skapa resursögonblicksbilder
* <a id="file-snaphsots"></a>
**Kan jag skapa resursögonblicksbild av enskilda filer?**  
    Resursögonblicksbilder skapas på filresursnivå. Du kan återställa enskilda filer från ögonblicksbilden av filresursen, men du kan inte skapa resursögonblicksbilder på filnivå. Men om du har tagit en resursögonblicksbild på resursnivå och vill visa en lista över resursögonblicksbilder där en specifik fil har ändrats, kan du göra detta under **Tidigare** versioner på en Windows-monterad resurs. 
    
    Om du behöver en funktion för filögonblicksbild kan du meddela oss [på Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Kan jag skapa resursögonblicksbilder av en krypterad filresurs?**  
    Du kan ta en resursögonblicksbild av Azure-filresurser som har kryptering i vila aktiverat. Du kan återställa filer från en resursögonblicksbild till en krypterad filresurs. Om resursen är krypterad krypteras även ögonblicksbilden av resursen.

* <a id="geo-redundant-snaphsots"></a>
**Är mina resursögonblicksbilder geo-redundanta?**  
    Resursögonblicksbilder har samma redundans som Azure-filresursen som de togs för. Om du har valt geo-redundant lagring för ditt konto lagras även resursögonblicksbild redundant i den parkopplade regionen.

### <a name="manage-share-snapshots"></a>Hantera resursögonblicksbilder
* <a id="browse-snapshots-linux"></a>
**Kan jag bläddra bland mina resursögonblicksbilder från Linux?**  
    Du kan använda Azure CLI för att skapa, lista, bläddra och återställa resursögonblicksbilder i Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kan jag kopiera resursögonblicksbilderna till ett annat lagringskonto?**  
    Du kan kopiera filer från resursögonblicksbilder till en annan plats, men du kan inte kopiera själva resursögonblicksbilderna.

### <a name="restore-data-from-share-snapshots"></a>Återställa data från resursögonblicksbilder
* <a id="promote-share-snapshot"></a>
**Kan jag höja upp en resursögonblicksbild till basresursen?**  
    Du kan kopiera data från en resursögonblicksbild till ett annat mål. Du kan inte höja upp en resursögonblicksbild till basresursen.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kan jag återställa data från min resursögonblicksbild till ett annat lagringskonto?**  
    Ja. Filer från en ögonblicksbild av en resurs kan kopieras till den ursprungliga platsen eller till en alternativ plats som innehåller antingen samma lagringskonto eller ett annat lagringskonto, antingen i samma region eller i olika regioner. Du kan också kopiera filer till en lokal plats eller till andra moln.    
  
### <a name="clean-up-share-snapshots"></a>Rensa resursögonblicksbilder
* <a id="delete-share-keep-snapshots"></a>
**Kan jag ta bort min resurs men inte ta bort mina resursögonblicksbilder?**  
    Om du har aktiva resursögonblicksbilder på resursen kan du inte ta bort resursen. Du kan använda ett API för att ta bort resursögonblicksbilder tillsammans med resursen. Du kan också ta bort både resursögonblicksbilderna och resursen i Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Vad händer med mina resursögonblicksbilder om jag tar bort mitt lagringskonto?**  
    Om du tar bort ditt lagringskonto tas även resursögonblicksbilderna bort.

## <a name="billing-and-pricing"></a>Fakturering och prissättning
* <a id="vm-file-share-network-traffic"></a>
**Räknas nätverkstrafiken mellan en virtuell Azure-dator och en Azure-filresurs som extern bandbredd som debiteras för prenumerationen?**  
    Om filresursen och den virtuella datorn finns i samma Azure-region tillkommer ingen extra kostnad för trafiken mellan filresursen och den virtuella datorn. Om filresursen och den virtuella datorn finns i olika regioner debiteras trafiken mellan dem som extern bandbredd.

* <a id="share-snapshot-price"></a>
**Hur mycket kostar det att dela ögonblicksbilder?**  
     Under förhandsversionen debiteras ingen kapacitet för resursögonblicksbilder. Standardlagringskostnader för utgående lagring och transaktioner gäller. Efter allmän tillgänglighet debiteras prenumerationer för kapacitet och transaktioner på resursögonblicksbilder.
     
     Resursögonblicksbilder är inkrementella till sin natur. Ögonblicksbilden av basresursen är själva resursen. Alla efterföljande resursögonblicksbilder är inkrementella och lagrar endast skillnaden från föregående resursögonblicksbild. Du debiteras bara för det ändrade innehållet. Om du har en resurs med 100 GiB data men endast 5 GiB har ändrats sedan din senaste ögonblicksbild av resursen förbrukar ögonblicksbilden bara 5 ytterligare GiB och du debiteras för 105 GiB. Mer information om transaktions- och standardavgifter för utgående data finns på [sidan Med priser.](https://azure.microsoft.com/pricing/details/storage/files/)

## <a name="scale-and-performance"></a>Skalning och prestanda
* <a id="files-scale-limits"></a>
**Vilka är skalningsgränserna för Azure Files?**  
    Information om skalbarhets- och prestandamål för Azure Files finns [i Azure Files skalbarhets- och prestandamål.](storage-files-scale-targets.md)

* <a id="need-larger-share"></a>
**Vilka storlekar är tillgängliga för Azure-filresurser?**  
    Azure-filresursstorlekar (premium och standard) kan skalas upp till 100 TiB. Se avsnittet [Publicera till större filresurser (standardnivå)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) i planeringsguiden för onboarding-instruktioner till de större filresurser för standardnivån.

* <a id="lfs-performance-impact"></a>
**Påverkar expandering av min filresurskvot mina arbetsbelastningar eller Azure File Sync?**
    
    Nej. Om du utökar kvoten påverkas inte dina arbetsbelastningar eller Azure File Sync.

* <a id="open-handles-quota"></a>
**Hur många klienter har åtkomst till samma fil samtidigt?**   
    Det finns en kvot på 2 000 öppna referenser på en enda fil. När du har 2 000 öppna referenser visas ett felmeddelande om att kvoten har nåtts.

* <a id="zip-slow-performance"></a>
**Mina prestanda är långsamma när jag packa upp filer i Azure Files. Vad ska jag göra?**  
    Om du vill överföra ett stort antal filer till Azure Files rekommenderar vi att du använder AzCopy (för Windows, i förhandsversion för Linux och UNIX) eller Azure PowerShell. Dessa verktyg har optimerats för nätverksöverföring.

* <a id="slow-perf-windows-81-2012r2"></a>
**Varför går prestanda långsammare när jag har monterat min Azure-filresurs på Windows Server 2012 R2 eller Windows 8.1?**  
    Det finns ett känt problem när du monterar en Azure-filresurs på Windows Server 2012 R2 och Windows 8.1. Problemet korrigerades i den kumulativa uppdateringen från april 2014 för Windows 8.1 och Windows Server 2012 R2. Se till att korrigeringen tillämpas på alla instanser av Windows Server 2012 R2 och Windows 8.1 för optimala prestanda. (Du bör alltid få Windows-korrigeringar via Windows Update.) Mer information finns i tillhörande artikel om Microsoft Knowledge Base prestanda när du kommer [åt Azure Files från Windows 8.1 eller Server 2012 R2.](https://support.microsoft.com/kb/3114025)

## <a name="features-and-interoperability-with-other-services"></a>Funktioner och samverkan med andra tjänster
* <a id="cluster-witness"></a>
**Kan jag använda min Azure-filresurs som *filresursvittne för* mitt Windows Server-redundanskluster?**  
    Den här konfigurationen stöds för närvarande inte för en Azure-filresurs. Mer information om hur du ställer in detta för Azure Blob Storage finns i [Distribuera ett molnvittne för ett redundanskluster.](/windows-server/failover-clustering/deploy-cloud-witness)

* <a id="containers"></a>
**Kan jag montera en Azure-filresurs på en Azure Container-instans?**  
    Ja, Azure-filresurser är ett bra alternativ när du vill spara information utöver livslängden för en containerinstans. Mer information finns i Montera [en Azure-filresurs med Azure Container Instances](../../container-instances/container-instances-volume-azure-files.md).

* <a id="rest-rename"></a>
**Finns det en namnbytesåtgärd i REST API?**  
    Inte just nu.

* <a id="nested-shares"></a>
**Kan jag konfigurera kapslade resurser? Med andra ord en resurs under en resurs?**  
    Nej. Filresursen *är den* virtuella drivrutin som du kan montera, så kapslade resurser stöds inte.

* <a id="ibm-mq"></a>
**Hur gör jag för att använda Azure Files med IBM MQ?**  
    IBM har publicerat ett dokument som hjälper IBM MQ-kunder att konfigurera Azure Files med IBM-tjänsten. Mer information finns i How [to set up an IBM MQ multi-instance queue manager with Microsoft Azure Files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Se även
* [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsöka Azure File Sync](../file-sync/file-sync-troubleshoot.md)
