---
title: Vanliga frågor och svar (FAQ) för Azure Files | Microsoft Docs
description: Få svar på Azure Files vanliga frågor och svar. Du kan montera Azure-filresurser samtidigt i molnet eller lokala Windows-, Linux-eller macOS-distributioner.
author: roygara
ms.service: storage
ms.date: 02/23/2020
ms.author: rogarana
ms.subservice: files
ms.topic: conceptual
ms.openlocfilehash: 739e1dea23f87403a4aded50d5c9f254a55c64cc
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737621"
---
# <a name="frequently-asked-questions-faq-about-azure-files"></a>Vanliga frågor och svar om Azure Files
[Azure Files](storage-files-introduction.md) erbjuder fullständigt hanterade fil resurser i molnet som är tillgängliga via [SMB-protokollet (Server Message Block](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) ) som är bransch standard och [NFS-protokollet (Network File System](https://en.wikipedia.org/wiki/Network_File_System) ) (för hands version). Du kan montera Azure-filresurser samtidigt i molnet eller lokala distributioner av Windows, Linux och macOS. Du kan också cachelagra Azure-filresurser på Windows Server-datorer med hjälp av Azure File Sync för snabb åtkomst nära var data används.

I den här artikeln besvaras vanliga frågor om Azure Files funktioner och funktioner, inklusive användning av Azure File Sync med Azure Files. Om du inte ser svaret på din fråga kan du kontakta oss genom följande kanaler (i eskalerad ordning):

1. Avsnittet kommentarer i den här artikeln.
2. [Sidan Microsoft Q&en fråga för Azure Storage](/answers/topics/azure-file-storage.html).
3. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files). 
4. Microsoft Support. Om du vill skapa en ny supportbegäran går du till fliken **Hjälp** i Azure Portal, väljer **Hjälp + Support** -knappen och väljer sedan **ny supportbegäran**.

## <a name="general"></a>Allmänt
* <a id="why-files-useful"></a>
  **Hur är Azure Files användbart?**  
   Du kan använda Azure Files för att skapa fil resurser i molnet utan att du ansvarar för att hantera omkostnader för en fysisk server, enhet eller apparat. Vi gör det enfärgade arbetet åt dig, inklusive att tillämpa OS-uppdateringar och ersätta felaktiga diskar. Mer information om scenarier som Azure Files kan hjälpa dig med finns i [varför Azure Files är användbart](storage-files-introduction.md#why-azure-files-is-useful).

* <a id="file-access-options"></a>
  **Vad är olika sätt att komma åt filer i Azure Files?**  
    SMB-filresurser kan monteras på den lokala datorn med hjälp av SMB 3,0-protokollet, eller så kan du använda verktyg som [Storage Explorer](https://storageexplorer.com/) för att komma åt filer i fil resursen. NFS-filresurser kan monteras på din lokala dator genom att kopiera/klistra in skriptet som tillhandahålls av Azure Portal. Från ditt program kan du använda lagrings klient bibliotek, REST-API: er, PowerShell eller Azure CLI för att komma åt dina filer i Azure-filresursen.

* <a id="what-is-afs"></a>
  **Vad är Azure File Sync?**  
    Du kan använda Azure File Sync för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Azure File Sync omvandlar dina Windows Server-datorer till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt dina data lokalt, inklusive SMB, Network File System (NFS) och File Transfer Protocol-tjänsten (FTPS). Du kan ha så många cacheminnen som du behöver över hela världen.

* <a id="files-versus-blobs"></a>
  **Varför ska jag använda en Azure-filresurs jämfört med Azure Blob Storage för mina data?**  
    Azure Files och Azure Blob Storage erbjuder båda sätt att lagra stora mängder data i molnet, men de är användbara i något annat syfte. 
    
    Azure Blob Storage är användbart för storskaliga molnbaserade program som behöver lagra ostrukturerade data. För att maximera prestanda och skalning är Azure Blob Storage en enklare lagrings abstraktion än ett faktiskt fil system. Du kan bara komma åt Azure Blob Storage via REST-baserade klient bibliotek (eller direkt via det REST-baserade protokollet).

    Azure Files är särskilt ett fil system. Azure Files har alla fil sammandrag som du känner till och älskar från flera år i arbetet med lokala operativ system. Precis som Azure Blob Storage erbjuder Azure Files ett REST-gränssnitt och REST-baserade klient bibliotek. Till skillnad från Azure Blob Storage erbjuder Azure Files SMB-eller NFS-åtkomst till Azure-filresurser. Fil resurser kan monteras direkt på Windows, Linux eller macOS, antingen lokalt eller i virtuella datorer i molnet, utan att behöva skriva någon kod eller bifoga särskilda driv rutiner till fil systemet. Du kan också cachelagra Azure SMB-filresurser på lokala fil servrar med hjälp av Azure File Sync för snabb åtkomst, nära var data används. 
   
    En mer detaljerad beskrivning av skillnaderna mellan Azure Files och Azure Blob Storage finns i [Introduktion till core Azure Storage Services](../common/storage-introduction.md). Mer information om Azure Blob Storage finns i [Introduktion till Blob Storage](../blobs/storage-blobs-introduction.md).

* <a id="files-versus-disks"></a>**Varför ska jag använda en Azure-filresurs i stället för Azure-diskar?**  
    En disk på Azure-diskar är helt enkelt en disk. För att hämta värde från Azure-diskar måste du koppla en disk till en virtuell dator som körs i Azure. Azure-diskar kan användas för allt som du skulle använda för att använda en disk för på en lokal server. Du kan använda den som en OS-systemdisk, som växlings utrymme för ett operativ system eller som dedikerat lagrings utrymme för ett program. En intressant användning för Azure-diskar är att skapa en fil server i molnet som ska användas på samma platser där du kan använda en Azure-filresurs. Att distribuera en fil server i Azure Virtual Machines är ett högpresterande sätt att hämta fil lagring i Azure när du behöver distributions alternativ som för närvarande inte stöds av Azure Files. 

    Att köra en fil server med Azure-diskar som backend-lagring är vanligt vis mycket dyrare än att använda en Azure-filresurs, av några skäl. Förutom att betala för disk lagring måste du också betala för kostnaden för att köra en eller flera virtuella Azure-datorer. För det andra måste du också hantera de virtuella datorer som används för att köra fil servern. Du ansvarar till exempel för uppgraderingar av operativ systemet. Slutligen, om du slutligen kräver att data cachelagras lokalt, är det upp till dig att konfigurera och hantera teknik för replikering, till exempel Distributed File System replikering (DFSR), för att göra det.

    En metod för att få det bästa av både Azure Files och en fil server som är värd för Azure Virtual Machines (förutom att använda Azure-diskar som backend-lagring) är att installera Azure File Sync på en fil server som är värd för en virtuell dator i molnet. Om Azure-filresursen finns i samma region som fil servern kan du aktivera moln skiktning och ange volymen ledigt utrymme i procent till maximalt (99%). Detta säkerställer minimal dubblering av data. Du kan också använda alla program som du vill ha med dina fil servrar, t. ex. program som kräver stöd för NFS-protokollet.

    Information om ett alternativ för att konfigurera en fil server med hög prestanda och hög tillgänglighet i Azure finns i [distribuera IaaS-gäst kluster för virtuella datorer i Microsoft Azure](https://blogs.msdn.microsoft.com/clustering/2017/02/14/deploying-an-iaas-vm-guest-clusters-in-microsoft-azure/). En mer djupgående Beskrivning av skillnaderna mellan Azure Files och Azure-diskar finns i [Introduktion till core Azure Storage-tjänsterna](../common/storage-introduction.md). Mer information om Azure-diskar finns i [Översikt över azure Managed disks](../../virtual-machines/managed-disks-overview.md).

* <a id="get-started"></a>
  **Hur gör jag för att kom igång med Azure Files?**  
   Det är enkelt att komma igång med Azure Files. Först skapar du [en SMB-filresurs](storage-how-to-create-file-share.md) eller [hur du skapar en NFS-resurs](storage-files-how-to-create-nfs-shares.md)och monterar den sedan i önskat operativ system: 

  * [Montera en SMB-resurs i Windows](storage-how-to-use-files-windows.md)
  * [Montera en SMB-resurs i Linux](storage-how-to-use-files-linux.md)
  * [Montera en SMB-resurs i macOS](storage-how-to-use-files-mac.md)
  * [Montera en NFS-filresurs](storage-files-how-to-mount-nfs-shares.md)

    En mer djupgående guide om hur du distribuerar en Azure-filresurs för att ersätta produktions fil resurser i organisationen finns i [Planera för en Azure Files distribution](storage-files-planning.md).

* <a id="redundancy-options"></a>
  **Vilka alternativ för lagring av redundans stöds av Azure Files?**  
    För närvarande har Azure Files stöd för lokalt redundant lagring (LRS), zon redundant lagring (ZRS), Geo-redundant lagring (GRS) och geo-Zone-redundant lagring (GZRS). Azure Files Premium-nivån stöder för närvarande endast LRS och ZRS.

* <a id="tier-options"></a>
  **Vilka lagrings nivåer stöds i Azure Files?**  
    Azure Files stöder två lagrings nivåer: Premium och standard. Standard fil resurser skapas i lagrings konton för generell användning (GPv1 eller GPv2) och Premium-filresurser skapas i FileStorage Storage-konton. Lär dig mer om hur du skapar [standard fil resurser](storage-how-to-create-file-share.md) och [Premium-filresurser](./storage-how-to-create-file-share.md). 
    
    > [!NOTE]
    > Du kan inte skapa Azure-filresurser från Blob Storage-konton eller *Premium* -GPv1-eller GPv2-lagrings konton. Standard Azure-filresurser måste skapas i *standard* konton för generell användning och endast Premium Azure-filresurser måste skapas i FileStorage lagrings konton. GPv1-och GPv2-lagrings konton ( *Premium* General Purpose) är endast för-blobar för Premium sidor. 

* <a id="file-locking"></a>
  **Stöder Azure Files fil låsning?**  
    Ja, Azure Files fullständigt stöd för fil låsning i SMB/Windows-format, [se information](/rest/api/storageservices/managing-file-locks).

* <a id="give-us-feedback"></a>
  **Jag vill verkligen se en speciell funktion som har lagts till Azure Files. Kan du lägga till det?**  
    Azure Filess teamet är intresserade av att höra och all feedback om vår tjänst. Rösta på en funktions förfrågan på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files)! Vi ser fram emot att se om det finns många nya funktioner.

## <a name="azure-file-sync"></a>Azure File Sync

* <a id="afs-region-availability"></a>
  **Vilka regioner stöds för Azure File Sync?**  
    Du hittar listan över tillgängliga regioner i avsnittet [region tillgänglighet](storage-sync-files-planning.md#azure-file-sync-region-availability) i Azure File Sync planerings guide. Vi lägger kontinuerligt till stöd för ytterligare regioner, inklusive icke-offentliga regioner.

* <a id="cross-domain-sync"></a>
  **Kan jag ha domänanslutna och icke-domänanslutna servrar i samma Sync-grupp?**  
    Ja. En Sync-grupp kan innehålla Server slut punkter som har olika Active Directory medlemskap, även om de inte är domänanslutna. Även om den här konfigurationen tekniskt fungerar rekommenderar vi inte detta som en typisk konfiguration eftersom åtkomst kontrol listor (ACL: er) som har definierats för filer och mappar på en server kanske inte kan tillämpas av andra servrar i Sync-gruppen. För bästa resultat rekommenderar vi att du synkroniserar mellan servrar som finns i samma Active Directory skog, mellan servrar som finns i olika Active Directory skogar, men som har upprättat förtroende relationer, eller mellan servrar som inte finns i en domän. Vi rekommenderar att du inte använder en blandning av de här konfigurationerna.

* <a id="afs-change-detection"></a>
  **Jag skapade en fil direkt i min Azure-filresurs med hjälp av SMB eller i portalen. Hur lång tid tar det för filen att synkronisera till servrarna i Sync-gruppen?**  
    [!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]


* <a id="afs-sync-time"></a>
  **Hur lång tid tar det för Azure File Sync att ladda upp 1TiB av data?**
  
    Prestandan varierar beroende på dina miljö inställningar, konfiguration och om detta är en inledande synkronisering eller en pågående synkronisering. Mer information finns i [Azure File Sync prestanda mått](storage-files-scale-targets.md#azure-file-sync-performance-metrics)

* <a id="afs-conflict-resolution"></a>**Vad händer om samma fil ändras på två servrar på ungefär samma tid?**  
    Azure File Sync använder en enkel lösning för konflikt lösning: vi behåller båda ändringarna i filer som ändras i två slut punkter på samma gång. Den senast skrivna ändringen behåller det ursprungliga fil namnet. Den äldre filen (som bestäms av LastWriteTime) har slut punkts namnet och konflikt numret som läggs till i fil namnet. För Server slut punkter är slut punktens namn namnet på servern. För moln slut punkter är slut punktens namn **moln**. Namnet följer den här taxonomin: 
   
    \<FileNameWithoutExtension\>-\<endpointName\>\[-#\].\<ext\>  

    Den första CompanyReport.docx skulle till exempel bli CompanyReport-CentralServer.docx om CentralServer är där den äldre skrivningen ägde rum. Den andra konflikten får namnet CompanyReport-CentralServer-1.docx. Azure File Sync stöder 100-konfliktskapande filer per fil. När det maximala antalet konfliktskapande filer har nåtts kommer filen inte att synkroniseras förrän antalet konfliktskapande filer är mindre än 100.

* <a id="afs-storage-redundancy"></a>
  **Stöds Geo-redundant lagring för Azure File Sync?**  
    Ja, Azure Files stöder både lokalt redundant lagring (LRS) och Geo-redundant lagring (GRS). Om du initierar ett lagrings konto med redundans mellan kopplade regioner från ett konto som har kon figurer ATS för GRS, rekommenderar Microsoft att du behandlar den nya regionen som en säkerhets kopia av enbart data. Azure File Sync börjar inte synkronisera automatiskt med den nya primära regionen. 

* <a id="sizeondisk-versus-size"></a>
  **Varför stämmer inte *storleken på disk* egenskapen för en fil med egenskapen *size* efter att du använt Azure File Sync?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#sizeondisk-versus-size).

* <a id="is-my-file-tiered"></a>
  **Hur kan jag se om en fil har flyttats på nivå?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#is-my-file-tiered).

* <a id="afs-recall-file"></a>**En fil som jag vill använda har flera nivåer. Hur kan jag återkalla filen till disk för att använda den lokalt?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#afs-recall-file).

* <a id="afs-force-tiering"></a>
  **Hur gör jag för att tvinga en fil eller katalog att vara i nivå av?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#afs-force-tiering).

* <a id="afs-effective-vfs"></a>
  **Hur tolkas det *lediga volym utrymmet* när jag har flera Server slut punkter på en volym?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#afs-effective-vfs).
  
* <a id="afs-tiered-files-tiering-disabled"></a>
  **Jag har inaktiverat moln nivå, varför finns det filer på Server slut punkten?**  
  Se [förstå moln nivåer](storage-sync-cloud-tiering.md#afs-tiering-disabled).

* <a id="afs-files-excluded"></a>
  **Vilka filer eller mappar undantas automatiskt av Azure File Sync?**  
  Se [filer som hoppats över](storage-sync-files-planning.md#files-skipped).

* <a id="afs-os-support"></a>
  **Kan jag använda Azure File Sync med antingen Windows Server 2008 R2, Linux eller min nätverksanslutna lagrings enhet (NAS)?**  
    För närvarande stöder Azure File Sync endast Windows Server 2019, Windows Server 2016 och Windows Server 2012 R2. Vi har just nu inte några andra planer som vi kan dela, men vi är öppna för att stödja ytterligare plattformar baserat på kundens efter frågan. Berätta för oss på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files) vilka plattformar som vi skulle vilja stödja.

* <a id="afs-tiered-files-out-of-endpoint"></a>
  **Varför finns det skiktade filer utanför Server slut punktens namn område?**  
    Innan Azure File Sync agent version 3, Azure File Sync blockerade flyttningen av skiktade filer utanför Server slut punkten men på samma volym som server slut punkten. Kopierings åtgärder, flyttning av filer som inte är på nivå, och flyttningar av nivå till andra volymer påverkades inte. Orsaken till detta är att det implicita antagandet att Utforskaren och andra Windows-API: er har den här flytt åtgärden på samma volym är (nästan) momentant namnbytes åtgärder. Det innebär att flyttningar gör att Utforskaren eller andra flyttnings metoder (t. ex. kommando rad eller PowerShell) inte svarar medan Azure File Sync kommer att anropa data från molnet igen. Från och med [Azure File Sync agent version 3.0.12.0](storage-files-release-notes.md#supported-versions)kan Azure File Sync flytta en nivå fil utanför Server slut punkten. Vi undviker de negativa effekter som tidigare nämnts genom att tillåta att den skiktade filen finns som en nivå fil utanför Server slut punkten och sedan anropar filen i bakgrunden. Det innebär att flytten av samma volym är omedelbar och vi gör allt arbete för att återkalla filen till disken när flytten har slutförts. 

* <a id="afs-do-not-delete-server-endpoint"></a>
  **Jag har problem med Azure File Sync på min server (synkronisering, moln nivå osv.). Bör jag ta bort och återskapa min server slut punkt?**  
    [!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]
    
* <a id="afs-resource-move"></a>
  **Kan jag flytta synkroniseringstjänsten för lagring och/eller lagrings konto till en annan resurs grupp, prenumeration eller Azure AD-klient?**  
   Ja, tjänsten Storage Sync och/eller lagrings kontot kan flyttas till en annan resurs grupp, prenumeration eller Azure AD-klient. När lagrings tjänsten för synkronisering eller lagrings kontot har flyttats måste du ge Microsoft. StorageSync-programmet åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](./storage-sync-files-troubleshoot.md?tabs=portal1%252cportal#troubleshoot-rbac)).

    > [!Note]  
    > När du skapar moln slut punkten måste Storage Sync-tjänsten och lagrings kontot finnas i samma Azure AD-klient. När moln slut punkten har skapats kan tjänsten Storage Sync och lagrings kontot flyttas till olika Azure AD-klienter.
    
* <a id="afs-ntfs-acls"></a>
  **Ska Azure File Sync bevara NTFS-ACL: er för katalog-/fil nivå tillsammans med data som lagras i Azure Files?**

    Från och med februari 2020 24 sparas nya och befintliga ACL: er med Azure File Sync i NTFS-format och ACL-ändringar som görs direkt till Azure-filresursen synkroniseras till alla servrar i Sync-gruppen. Ändringar av ACL: er som görs i Azure Files kommer att synkroniseras via Azure File Sync. När du kopierar data till Azure Files, se till att du använder ett kopierings verktyg som stöder den nödvändiga "åter givningen" för att kopiera attribut, tidsstämplar och ACL: er till en Azure-filresurs – antingen via SMB eller REST. När du använder Azure Copy-verktyg, till exempel AzCopy, är det viktigt att använda den senaste versionen. Se [tabellen fil kopierings verktyg](storage-files-migration-overview.md#file-copy-tools) för att få en översikt över Azure Copy-verktyg så att du kan kopiera alla viktiga metadata för en fil.

    Om du har aktiverat Azure Backup i hanterade fil resurser för filsynkronisering kan fil åtkomst kontrol listor fortsätta att återställas som en del av arbets flödet för säkerhets kopierings återställning. Detta fungerar antingen för hela resursen eller enskilda filer/kataloger.

    Om du använder ögonblicks bilder som en del av den självhanterade säkerhets kopierings lösningen för fil resurser som hanteras av filsynkroniseringen, kanske dina ACL: er inte återställs korrekt till NTFS ACL: er om ögonblicks bilderna togs före februari 24 2020. Om detta inträffar kan du kontakta Azure-supporten.
    
## <a name="security-authentication-and-access-control"></a>Säkerhet, autentisering och åtkomst kontroll
* <a id="ad-support"></a>
**Stöds identitets baserad autentisering och åtkomst kontroll av Azure Files?**  
    
    Ja, Azure Files stöder identitets-baserad autentisering och åtkomst kontroll. Du kan välja ett av två sätt att använda identitetsbaserade åtkomst kontroller: lokala Active Directory Domain Services eller Azure Active Directory Domain Services (Azure AD DS). Lokala Active Directory Domain Services (AD DS) stöder autentisering med hjälp av domänanslutna AD DS-datorer, antingen lokalt eller i Azure, för att få åtkomst till Azure-filresurser över SMB. Azure AD DS-autentisering över SMB för Azure Files gör det möjligt för Azure AD DS-domänanslutna virtuella Windows-datorer att få åtkomst till resurser, kataloger och filer med hjälp av autentiseringsuppgifter för Azure AD. Mer information finns i [Översikt över Azure Files Identity-baserat stöd för autentisering för SMB-åtkomst](storage-files-active-directory-overview.md). 

    Azure Files erbjuder ytterligare två sätt att hantera åtkomst kontroll:

    - Du kan använda signaturer för delad åtkomst (SAS) för att skapa token som har specifika behörigheter och som är giltiga för ett visst tidsintervall. Du kan till exempel generera en token med skrivskyddad åtkomst till en bestämd fil som har en giltighet på 10 minuter. Alla som har token när token är giltiga har skrivskyddad åtkomst till filen under 10 minuter. Signaturer för delad åtkomst stöds endast via REST API eller i klient bibliotek. Du måste montera Azure-filresursen över SMB genom att använda lagrings konto nycklarna.

    - Azure File Sync bevarar och replikerar alla godtyckliga ACL: er, eller DACL: er (oavsett om de är Active Directory-baserade eller lokala) till alla Server slut punkter som den synkroniserar till. 
    
    Du kan referera till att [auktorisera åtkomst till Azure Storage](../common/storage-auth.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) för en omfattande åter givning av alla protokoll som stöds på Azure Storage Services. 
    
* <a id="encryption-at-rest"></a>
**Hur kan jag se till att min Azure-filresurs är krypterad i vila?**  

    Ja. Mer information finns i [Azure Storage tjänst kryptering](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="access-via-browser"></a>
**Hur kan jag ge åtkomst till en speciell fil med hjälp av en webbläsare?**  

    Du kan använda signaturer för delad åtkomst för att skapa token som har specifika behörigheter och som är giltiga för ett visst tidsintervall. Du kan till exempel generera en token som ger skrivskyddad åtkomst till en viss fil under en angiven tids period. Alla som har URL: en kan komma åt filen direkt från valfri webbläsare medan token är giltig. Du kan enkelt skapa en signatur för delad åtkomst från ett användar gränssnitt som Storage Explorer.

* <a id="file-level-permissions"></a>
**Är det möjligt att ange skrivskyddade eller skrivskyddade behörigheter för mappar i resursen?**  

    Om du monterar fil resursen med hjälp av SMB, saknar du kontroll över behörigheter på mappnivå. Men om du skapar en signatur för delad åtkomst med hjälp av REST API-eller klient bibliotek kan du ange skrivskyddade eller skrivskyddade behörigheter för mappar i resursen.

* <a id="ip-restrictions"></a>
**Kan jag implementera IP-begränsningar för en Azure-filresurs?**  

    Ja. Åtkomst till Azure-filresursen kan begränsas på lagrings konto nivå. Mer information finns i [konfigurera Azure Storage brand väggar och virtuella nätverk](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

* <a id="data-compliance-policies"></a>
**Vilka policyer för efterlevnadsprinciper stöder Azure Files?**  

   Azure Files körs ovanpå samma lagrings arkitektur som används i andra lagrings tjänster i Azure Storage. Azure Files använder samma policyer för efterlevnadsprinciper som används i andra Azure Storage-tjänster. För mer information om hur du Azure Storage data, kan du referera till [Azure Storage Compliance-erbjudanden](../common/storage-compliance-offerings.md)och gå till [Microsoft Trust Center](https://microsoft.com/trustcenter/default.aspx).

* <a id="file-auditing"></a>
**Hur kan jag granska fil åtkomst och ändringar i Azure Files?**

  Det finns två alternativ som ger gransknings funktioner för Azure Files:
  - Om användarna använder Azure-filresursen direkt, kan [Azure Storage loggar (för hands version)](../blobs/monitor-blob-storage.md?tabs=azure-powershell#analyzing-logs) användas för att spåra fil ändringar och användar åtkomst. De här loggarna kan användas i fel söknings syfte och förfrågningarna loggas på bästa möjliga sätt.
  - Om användarna har åtkomst till Azure-filresursen via en Windows-Server som har den Azure File Sync-agenten installerad, använder du en [gransknings princip](/windows/security/threat-protection/auditing/apply-a-basic-audit-policy-on-a-file-or-folder) eller en produkt från tredje part för att spåra fil ändringar och användar åtkomst på Windows Server. 
   
### <a name="ad-ds--azure-ad-ds-authentication"></a>AD DS & Azure AD DS-autentisering
* <a id="ad-support-devices"></a>
**Stöder Azure Files Azure Active Directory Domain Services-autentisering (Azure AD DS) SMB-åtkomst med Azure AD-autentiseringsuppgifter från enheter som är anslutna till eller registrerade med Azure AD?**

    Nej, det här scenariot stöds inte.

* <a id="ad-vm-subscription"></a>
**Kan jag komma åt Azure-filresurser med Azure AD-autentiseringsuppgifter från en virtuell dator under en annan prenumeration?**

    Om prenumerationen som fil resursen distribueras till är kopplad till samma Azure AD-klient som den Azure AD DS-distribution som den virtuella datorn är domänansluten till, kan du komma åt Azure-filresurser med samma autentiseringsuppgifter för Azure AD. Begränsningen införs inte i prenumerationen, men på den associerade Azure AD-klienten.
    
* <a id="ad-support-subscription"></a>
**Kan jag aktivera antingen Azure AD DS eller lokal AD DS-autentisering för Azure-filresurser med hjälp av en Azure AD-klient som skiljer sig från Azure-filresursens primära klient?**

    Nej, Azure Files stöder bara Azure AD DS eller lokal AD DS-integrering med en Azure AD-klient som finns i samma prenumeration som fil resursen. Det går bara att koppla en prenumeration till en Azure AD-klient. Den här begränsningen gäller både Azure AD DS och lokala AD DS-autentiseringsmetoder. När du använder en lokal AD DS för autentisering [måste AD DS-autentiseringsuppgiften synkroniseras med den Azure AD](../../active-directory/hybrid/how-to-connect-install-roadmap.md) som lagrings kontot är associerat med.

* <a id="ad-linux-vms"></a>
**Stöder Azure AD DS eller lokal AD DS-autentisering för Azure-filresurser virtuella Linux-datorer?**

    Nej, autentisering från virtuella Linux-datorer stöds inte.

* <a id="ad-aad-smb-afs"></a>
**Har fil resurser som hanteras av Azure File Sync stöd för antingen Azure AD DS eller lokal AD DS-autentisering?**

    Ja, du kan aktivera Azure AD DS eller lokal AD DS-autentisering på en fil resurs som hanteras av Azure File Sync. Ändringar i katalogen/filen NTFS ACL: er på lokala fil servrar kommer att nivåas till Azure Files och vice versa.

* <a id="ad-aad-smb-files"></a>
**Hur kan jag kontrol lera om jag har aktiverat AD DS-autentisering på mitt lagrings konto och hämta domän informationen?**

    Mer information finns [här](./storage-files-identity-ad-ds-enable.md#confirm-the-feature-is-enabled).

* <a id=""></a>
**Har Azure Files Azure AD-autentisering stöd för virtuella Linux-datorer?**

    Nej, autentisering från virtuella Linux-datorer stöds inte.

* <a id="ad-multiple-forest"></a>
**Stöder lokal AD DS-autentisering för Azure-filresurser integrering med en AD DS-miljö med flera skogar?**    

    Azure Files lokal AD DS-autentisering integreras bara med skogen för den domän tjänst som lagrings kontot är registrerat på. För att stödja autentisering från en annan skog måste din miljö ha ett skogs förtroende konfigurerat korrekt. Hur Azure Files registreras i AD DS nästan på samma sätt som en vanlig fil server, där den skapar en identitet (dator-eller tjänst inloggnings konto) i AD DS för autentisering. Den enda skillnaden är att lagrings kontots registrerade SPN slutar med "file.core.windows.net" som inte matchar domänsuffix. Kontakta domän administratören för att se om någon uppdatering av Routningsprincipen krävs för att aktivera flera skogar på grund av ett annat domänsuffix. Vi tillhandahåller ett exempel nedan för att konfigurera Dirigerings princip för suffix.
    
    Exempel: när användare i skogen en domän vill komma åt en fil resurs med det lagrings konto som registrerats mot en domän i skog B, fungerar detta inte automatiskt eftersom tjänstens huvud namn inte har något suffix som matchar suffixet för någon domän i skog A. Vi kan åtgärda det här problemet genom att manuellt konfigurera en regel för routning av suffix från skog A till skog B för det anpassade suffixet "file.core.windows.net".
    Först måste du lägga till ett nytt anpassat suffix i skog B. kontrol lera att du har rätt administratörs behörighet för att ändra konfigurationen och följ sedan dessa steg:   
    1. Logga in på en dator domän som är ansluten till skog B
    2.  Öppna konsolen Active Directory domäner och förtroenden
    3.  Högerklicka på "Active Directory domäner och förtroenden"
    4.  Klicka på "egenskaper"
    5.  Klicka på "Lägg till"
    6.  Lägg till "file.core.windows.net" som UPN-suffix
    7.  Klicka på Använd och sedan på OK för att stänga guiden
    
    Lägg sedan till regeln för routning av suffix i skog A, så att den omdirigerar till skog B.
    1.  Logga in på en dator domän som är ansluten till skog A
    2.  Öppna konsolen Active Directory domäner och förtroenden
    3.  Högerklicka på den domän som du vill komma åt fil resursen och klicka sedan på fliken "förtroenden" och välj skog B-domän från utgående förtroenden. Om du inte konfigurerar förtroende mellan de två skogarna måste du konfigurera förtroendet först
    4.  Klicka på "egenskaper..." sedan "routning av namnsuffix"
    5.  Kontrol lera att surffix "*. file.core.windows.net" visas. Annars klickar du på uppdatera
    6.  Välj "*. file.core.windows.net" och klicka på "Aktivera" och "tillämpa"

* <a id=""></a>
**Vilka regioner är tillgängliga för Azure Files AD DS-autentisering?**

    Mer information finns i [regional tillgänglighet för AD DS](storage-files-identity-auth-active-directory-enable.md#regional-availability) .
    
* <a id="ad-aad-smb-afs"></a>
**Kan jag utnyttja Azure Files Active Directory-autentisering (AD) på fil resurser som hanteras av Azure File Sync?**

    Ja, du kan aktivera AD-autentisering på en fil resurs som hanteras av Azure File Sync. Ändringar i katalogen/filen NTFS ACL: er på lokala fil servrar kommer att nivåas till Azure Files och vice versa.

* <a id="ad-aad-smb-files"></a>
**Finns det någon skillnad i att skapa ett dator konto eller tjänst inloggnings konto som representerar mitt lagrings konto i AD?**

    Att skapa antingen ett [dator konto](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory) (standard) eller ett [inloggnings konto för tjänsten](/windows/win32/ad/about-service-logon-accounts) har ingen skillnad på hur autentiseringen fungerar med Azure Files. Du kan välja hur du vill representera ett lagrings konto som en identitet i din AD-miljö. Standard DomainAccountType som angetts i Join-AzStorageAccountForAuth cmdlet är dator konto. Lösen ordets giltighets tid för lösen ord som kon figurer ATS i din AD-miljö kan dock vara olika för inloggnings kontot för datorn eller tjänsten och du måste ta hänsyn till [uppdateringen av lösen ordet för din lagrings konto identitet i AD](./storage-files-identity-ad-ds-update-password.md).
 
* <a id="ad-support-rest-apis"></a>
**Finns det REST-API: er som stöder get/set/Copy Directory/File Windows ACL: er?**

    Ja, vi har stöd för REST API: er som hämtar, anger eller kopierar NTFS ACL: er för kataloger eller filer när du använder REST API [2019-07-07](/rest/api/storageservices/versioning-for-the-azure-storage-services#version-2019-07-07) (eller senare). Vi har även stöd för att bevara Windows ACL: er i REST-baserade verktyg: [AzCopy v 10.4 +](https://github.com/Azure/azure-storage-azcopy/releases).

## <a name="network-file-system"></a>Network File System

* <a id="when-to-use-nfs"></a>
**När ska jag använda Azure Files NFS?**

    Se [NFS-resurser (för hands version)](storage-files-compare-protocols.md#nfs-shares-preview).

* <a id="backup-nfs-data"></a>
**Hur gör jag för att säkerhetskopierade data som lagras i NFS-resurser?**

    Säkerhets kopiering av dina data på NFS-resurser kan antingen dirigeras med hjälp av välbekanta verktyg som rsync eller produkter från någon av våra tredjeparts säkerhets kopierings partner. Flera säkerhets kopierings partner inklusive [CommVault](https://documentation.commvault.com/commvault/v11/article?p=92634.htm), [Veeam](https://www.veeam.com/blog/?p=123438)och [Veritas](https://players.brightcove.net/4396107486001/default_default/index.html?videoId=6189967101001) var en del av vår inledande för hands version och har utökat sina lösningar för att fungera med både SMB 3,0 och NFS 4,1 för Azure Files.

* <a id="migrate-nfs-data"></a>
**Kan jag migrera befintliga data till en NFS-resurs?**

    Inom en region kan du använda standard verktyg som SCP, rsync eller SSHFS för att flytta data. Eftersom Azure Files NFS kan nås från flera beräknings instanser samtidigt kan du förbättra kopierings hastigheten med parallella uppladdningar. Om du vill hämta data utanför en region använder du en VPN-eller ExpressRoute för att montera till ditt fil system från ditt lokala data Center.

## <a name="on-premises-access"></a>Lokal åtkomst

* <a id="port-445-blocked"></a>
**Internet leverantören eller den blockerar port 445 som inte Azure Files montering. Vad ska jag göra?**

    Du kan lära dig om [olika sätt att lösa den blockerade port 445 här](./storage-troubleshoot-windows-file-connection-problems.md#cause-1-port-445-is-blocked). Azure Files tillåter endast anslutningar med SMB 3,0 (med krypterings stöd) utanför regionen eller data centret. SMB 3,0-protokollet har infört många säkerhetsfunktioner, inklusive kanal kryptering som är mycket säkert att använda via Internet. Men det är möjligt att port 445 har blockerats på grund av historiska orsaker till de lägre SMB-versionerna. I det idealiska fallet bör porten blockeras endast för SMB 1,0-trafik och SMB 1,0 bör stängas av på alla klienter.

* <a id="expressroute-not-required"></a>
**Måste jag använda Azure ExpressRoute för att ansluta till Azure Files eller använda Azure File Sync lokalt?**  

    Nej. ExpressRoute krävs inte för att få åtkomst till en Azure-filresurs. Om du monterar en Azure-filresurs direkt lokalt, är allt det som krävs att port 445 (TCP utgående) är öppen för Internet åtkomst (detta är den port som SMB använder för att kommunicera). Om du använder Azure File Sync är allt det som krävs port 443 (TCP utgående) för HTTPS-åtkomst (ingen SMB krävs). Du *kan* dock använda ExpressRoute med något av dessa åtkomst alternativ.

* <a id="mount-locally"></a>
**Hur kan jag montera en Azure-filresurs på min lokala dator?**  

    Du kan montera fil resursen med hjälp av SMB-protokollet om Port 445 (TCP utgående) är öppen och klienten stöder SMB 3,0-protokollet (till exempel om du använder Windows 10 eller Windows Server 2016). Om Port 445 blockeras av din organisations princip eller av din Internet leverantör kan du använda Azure File Sync för att få åtkomst till Azure-filresursen.

## <a name="backup"></a>Backup
* <a id="backup-share"></a>
**Hur gör jag för att säkerhetskopiera min Azure-filresurs?**  
    Du kan använda periodiska [resurs ögonblicks bilder](storage-snapshots-files.md) för att skydda mot oavsiktlig borttagning. Du kan också använda AzCopy, Robocopy eller ett säkerhets kopierings verktyg från tredje part som kan säkerhetskopiera en monterad fil resurs. Azure Backup erbjuder säkerhets kopiering av Azure Files. Läs mer om hur du [säkerhetskopierar Azure-filresurser genom att Azure Backup](../../backup/backup-afs.md).

## <a name="share-snapshots"></a>Resursögonblicksbilder

### <a name="share-snapshots-general"></a>Dela ögonblicks bilder: allmänt
* <a id="what-are-snaphots"></a>
**Vad är ögonblicks bilder av fil resurser?**  
    Du kan använda Azures ögonblicks bilder av fil resurser för att skapa en skrivskyddad version av dina fil resurser. Du kan också använda Azure Files för att kopiera en tidigare version av innehållet tillbaka till samma resurs, till en annan plats i Azure eller lokalt för fler ändringar. Mer information om resurs ögonblicks bilder finns i [Översikt över resurs ögonblicks bilder](storage-snapshots-files.md).

* <a id="where-are-snapshots-stored"></a>
**Var lagras mina resurs ögonblicks bilder?**  
    Resurs ögonblicks bilder lagras i samma lagrings konto som fil resursen.

* <a id="snapshot-consistency"></a>
**Är resurs ögonblicks bilder program-konsekventa?**  
    Nej, resurs ögonblicks bilder är inte programkonsekventa. Användaren måste tömma Skriv åtgärder från programmet till resursen innan du tar bort ögonblicks bilden.

* <a id="snapshot-limits"></a>
**Finns det några begränsningar för hur många resurs-ögonblicksbilder jag kan använda?**  
    Ja. Azure Files kan behålla maximalt 200 resurs ögonblicks bilder. Resurs ögonblicks bilder räknas inte mot resurs kvoten, så det finns ingen gräns per resurs för det totala utrymmet som används av alla resurs ögonblicks bilder. Lagrings konto gränser gäller fortfarande. Efter 200 resurs ögonblicks bilder måste du ta bort äldre ögonblicks bilder för att skapa nya ögonblicks bilder av resursen.

* <a id="snapshot-cost"></a>
**Hur mycket kostar det att dela ögonblicks bilder?**  
    Standard transaktioner och standard lagrings kostnader gäller för ögonblicks bilder. Ögonblicks bilder är stegvisa. Den grundläggande ögonblicks bilden är själva resursen. Alla efterföljande ögonblicks bilder är stegvisa och kommer bara att lagra differensen från den tidigare ögonblicks bilden. Det innebär att delta ändringarna som visas på fakturan blir minimala om din arbets belastnings omsättning är minimal. Se [prissättnings sida](https://azure.microsoft.com/pricing/details/storage/files/) för standard Azure Files pris information. Idag är det dags att titta på den storlek som används av resurs ögonblicks bilden genom att jämföra den fakturerade kapaciteten med den använda kapaciteten. Vi arbetar på verktyg för att förbättra rapporteringen.

* <a id="ntfs-acls-snaphsots"></a>
**Är NTFS ACL: er för kataloger och filer som behålls i resurs ögonblicks bilder?**  
    NTFS ACL: er för kataloger och filer finns kvar i resurs ögonblicks bilder.

### <a name="create-share-snapshots"></a>Skapa resursögonblicksbilder
* <a id="file-snaphsots"></a>
**Kan jag skapa en resurs ögonblicks bild av enskilda filer?**  
    Resurs ögonblicks bilder skapas på fil resurs nivå. Du kan återställa enskilda filer från ögonblicks bilden av fil resursen, men du kan inte skapa resurs ögonblicks bilder på filnivå. Men om du har tagit en ögonblicks bild på resurs nivå och vill visa en lista över resurs ögonblicks bilder där en fil har ändrats, kan du göra detta under **tidigare versioner** på en Windows-monterad resurs. 
    
    Om du behöver en fil ögonblicks bilds funktion kan du meddela oss på [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).

* <a id="encrypted-snapshots"></a>
**Kan jag skapa resurs ögonblicks bilder av en krypterad fil resurs?**  
    Du kan ta en ögonblicks bild av Azure-filresurser som har kryptering i vila aktiverat. Du kan återställa filer från en resurs ögonblicks bild till en krypterad fil resurs. Om din resurs är krypterad krypteras även din resurs ögonblicks bild.

* <a id="geo-redundant-snaphsots"></a>
**Är mina resurs ögonblicks bilder geo-redundanta?**  
    Resurs ögonblicks bilder har samma redundans som den Azure-filresurs som de vidtogs för. Om du har valt Geo-redundant lagring för ditt konto lagras även din resurs ögonblicks bild i den kopplade regionen.

### <a name="manage-share-snapshots"></a>Hantera resurs ögonblicks bilder
* <a id="browse-snapshots-linux"></a>
**Kan jag bläddra i mina resurs ögonblicks bilder från Linux?**  
    Du kan använda Azure CLI för att skapa, Visa, bläddra i och återställa resurs ögonblicks bilder i Linux.

* <a id="copy-snapshots-to-other-storage-account"></a>
**Kan jag kopiera resurs ögonblicks bilderna till ett annat lagrings konto?**  
    Du kan kopiera filer från resurs ögonblicks bilder till en annan plats, men du kan inte kopiera själva resurs ögonblicks bilderna.

### <a name="restore-data-from-share-snapshots"></a>Återställa data från resurs ögonblicks bilder
* <a id="promote-share-snapshot"></a>
**Kan jag befordra en resurs ögonblicks bild till bas resursen?**  
    Du kan kopiera data från en resurs ögonblicks bild till andra mål. Du kan inte befordra en resurs ögonblicks bild till bas resursen.

* <a id="restore-snapshotted-file-to-other-share"></a>
**Kan jag återställa data från min resurs ögonblicks bild till ett annat lagrings konto?**  
    Ja. Filer från en resurs ögonblicks bild kan kopieras till den ursprungliga platsen eller till en annan plats som innehåller antingen samma lagrings konto eller ett annat lagrings konto, antingen i samma region eller i olika regioner. Du kan också kopiera filer till en lokal plats eller till ett annat moln.    
  
### <a name="clean-up-share-snapshots"></a>Rensa resurs ögonblicks bilder
* <a id="delete-share-keep-snapshots"></a>
**Kan jag ta bort min resurs men inte ta bort mina resurs ögonblicks bilder?**  
    Om du har ögonblicks bilder av aktiva resurser på resursen kan du inte ta bort resursen. Du kan använda ett API för att ta bort resurs ögonblicks bilder, tillsammans med resursen. Du kan också ta bort både resurs ögonblicks bilder och resursen i Azure Portal.

* <a id="delete-share-with-snapshots"></a>
**Vad händer med mina resurs ögonblicks bilder om jag tar bort mitt lagrings konto?**  
    Om du tar bort ditt lagrings konto tas även resurs ögonblicks bilderna bort.

## <a name="billing-and-pricing"></a>Fakturering och priser
* <a id="vm-file-share-network-traffic"></a>
**Räknas nätverks trafiken mellan en virtuell Azure-dator och en Azure-filresurs som extern bandbredd som debiteras för prenumerationen?**  
    Om fil resursen och den virtuella datorn finns i samma Azure-region finns det ingen extra kostnad för trafiken mellan fil resursen och den virtuella datorn. Om fil resursen och den virtuella datorn finns i olika regioner debiteras trafiken mellan dem som extern bandbredd.

* <a id="share-snapshot-price"></a>
**Hur mycket kostar det att dela ögonblicks bilder?**  
     Under för hands versionen kostar det inget att dela ögonblicks bilds kapacitet. Utgående och transaktionskostnader gäller för standard lagring. Efter den allmänna tillgängligheten debiteras prenumerationer för kapacitet och transaktioner för resurs ögonblicks bilder.
     
     Resurs ögonblicks bilder är stegvisa. Bas resursens ögonblicks bild är själva resursen. Alla efterföljande ögonblicks bilder av resurser är stegvisa och lagrar bara skillnaden från föregående ögonblicks bild. Du faktureras bara för det ändrade innehållet. Om du har en resurs med 100 GiB av data men bara 5 GiB har ändrats sedan den senaste ögonblicks bilden av en resurs, förbrukar resurs ögonblicks bilden bara fem ytterligare GiB och du debiteras för 105 GiB. Mer information om transaktions-och standard avgifter för utgående trafik finns på [sidan med priser](https://azure.microsoft.com/pricing/details/storage/files/).

## <a name="scale-and-performance"></a>Skalning och prestanda
* <a id="files-scale-limits"></a>
**Vilka är skalnings gränserna för Azure Files?**  
    Information om skalbarhets-och prestanda mål för Azure Files finns i [Azure Files skalbarhets-och prestanda mål](storage-files-scale-targets.md).

* <a id="need-larger-share"></a>
**Vilka storlekar är tillgängliga för Azure-filresurser?**  
    Azures fil resurs storlekar (Premium och standard) kan skala upp till 100 TiB. Se avsnittet [Publicera till större fil resurser (standard nivå)](storage-files-planning.md#enable-standard-file-shares-to-span-up-to-100-tib) i planerings guiden för att integrera instruktioner till de större fil resurserna för standard nivån.

* <a id="lfs-performance-impact"></a>
**Påverkar min fil resurs kvot mina arbets belastningar eller Azure File Sync?**
    
    Nej. Att utöka kvoten påverkar inte arbets belastningarna eller Azure File Sync.

* <a id="open-handles-quota"></a>
**Hur många klienter kan komma åt samma fil samtidigt?**   
    Det finns en kvot på 2 000 öppna referenser på en enskild fil. När du har 2 000 öppna handtag visas ett fel meddelande om att kvoten har nåtts.

* <a id="zip-slow-performance"></a>
**Min prestanda är långsam när jag packar upp filer i Azure Files. Vad ska jag göra?**  
    Om du vill överföra ett stort antal filer till Azure Files rekommenderar vi att du använder AzCopy (för Windows, i för hands versionen för Linux och UNIX) eller Azure PowerShell. Dessa verktyg har optimerats för nätverks överföring.

* <a id="slow-perf-windows-81-2012r2"></a>
**Varför är min prestanda långsam när jag har monterat min Azure-filresurs på Windows Server 2012 R2 eller Windows 8,1?**  
    Det finns ett känt problem när du monterar en Azure-filresurs på Windows Server 2012 R2 och Windows 8,1. Problemet har korrigerats i den kumulativa uppdateringen från april 2014 för Windows 8,1 och Windows Server 2012 R2. För bästa prestanda bör du se till att alla instanser av Windows Server 2012 R2 och Windows 8,1 har den här korrigerings filen tillämpad. (Du bör alltid ta emot Windows-korrigeringsfiler via Windows Update.) Mer information finns i den tillhör ande Microsoft Knowledge Base-artikeln [långsamma prestanda när du kommer åt Azure Files från Windows 8,1 eller Server 2012 R2](https://support.microsoft.com/kb/3114025).

## <a name="features-and-interoperability-with-other-services"></a>Funktioner och samverkan med andra tjänster
* <a id="cluster-witness"></a>
**Kan jag använda min Azure-filresurs som ett *fil resurs vittne* för mitt Windows Server-redundanskluster?**  
    Den här konfigurationen stöds för närvarande inte för en Azure-filresurs. Mer information om hur du konfigurerar detta för Azure Blob Storage finns i [distribuera ett moln vittne för ett redundanskluster](/windows-server/failover-clustering/deploy-cloud-witness).

* <a id="containers"></a>
**Kan jag montera en Azure-filresurs på en Azure Container instance?**  
    Ja, Azure-filresurser är ett bra alternativ när du vill spara information utanför livs längden för en behållar instans. Mer information finns i [montera en Azure-filresurs med Azure Container instances](../../container-instances/container-instances-volume-azure-files.md).

* <a id="rest-rename"></a>
**Finns det en namnbytes åtgärd i REST API?**  
    Inte just nu.

* <a id="nested-shares"></a>
**Kan jag konfigurera kapslade resurser? Med andra ord, en resurs under en resurs?**  
    Nej. Fil resursen *är* den virtuella driv rutin som du kan montera, så kapslade resurser stöds inte.

* <a id="ibm-mq"></a>
**Hur gör jag för att använda Azure Files med IBM MQ?**  
    IBM har publicerat ett dokument som hjälper IBM MQ-kunder att konfigurera Azure Files med IBM-tjänsten. Mer information finns i [så här konfigurerar du en IBM MQ multi-instance Queue Manager med Microsoft Azure Files service](https://github.com/ibm-messaging/mq-azure/wiki/How-to-setup-IBM-MQ-Multi-instance-queue-manager-with-Microsoft-Azure-File-Service).

## <a name="see-also"></a>Se även
* [Felsöka Azure Files i Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Felsöka Azure Files i Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)