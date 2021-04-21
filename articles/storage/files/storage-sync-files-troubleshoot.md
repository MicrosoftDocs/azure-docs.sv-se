---
title: Felsöka Azure File Sync | Microsoft Docs
description: Felsök vanliga problem i en distribution Azure File Sync, som du kan använda för att omvandla Windows Server till ett snabbt cacheminne för din Azure-filresurs.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 4/12/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 872faab688844278caac4c78ff8cd9ae72e46a4b
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752794"
---
# <a name="troubleshoot-azure-file-sync"></a>Felsök Azure File Sync
Använd Azure File Sync för att centralisera organisationens filresurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln är utformad för att hjälpa dig att felsöka och lösa problem som kan uppstå med Azure File Sync distributionen. Vi beskriver också hur du samlar in viktiga loggar från systemet om en djupare undersökning av problemet krävs. Om du inte ser svaret på din fråga kan du kontakta oss via följande kanaler (i eskalerande ordning):

1. [Microsoft Q&A question page for Azure Storage](/answers/products/azure?product=storage).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft Support. Om du vill skapa en ny supportbegäran går  Azure Portal klickar du på knappen **Hjälp + support** på fliken Hjälp och väljer sedan **Ny supportbegäran.**

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Jag har problem med att Azure File Sync på min server (synkronisering, molnnivåindelad osv.). Bör jag ta bort och återskapa serverslutpunkten?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Agentinstallation och serverregistrering
<a id="agent-installation-failures"></a>**Felsöka agentinstallationsfel**  
Om den Azure File Sync agentinstallationen misslyckas i en upphöjd kommandotolk kör du följande kommando för att aktivera loggning under agentinstallationen:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Granska installer.log för att ta reda på orsaken till installationsfelet.

<a id="agent-installation-gpo"></a>**Agentinstallationen misslyckas med felet: Storage Sync Agent Installationsguiden avslutades i förtid på grund av ett fel**

I agentinstallationsloggen loggas följande fel:

```
CAQuietExec64:  + CategoryInfo          : SecurityError: (:) , PSSecurityException
CAQuietExec64:  + FullyQualifiedErrorId : UnauthorizedAccess
CAQuietExec64:  Error 0x80070001: Command line returned an error.
```

Det här problemet uppstår om [PowerShell-körningsprincipen](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) har konfigurerats med grupprincip och principinställningen är "Tillåt endast signerade skript". Alla skript som ingår i Azure File Sync agenten signeras. Installationen Azure File Sync agenten misslyckas eftersom installationsprogrammet utför skriptkörningen med hjälp av inställningen Kringgå körningsprincipen.

Lös problemet genom att tillfälligt inaktivera [grupprincipinställningen Aktivera skriptkörning](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies#use-group-policy-to-manage-execution-policy) på servern. När agentinstallationen är klar kan grupprincipinställningen återaktiveras.

<a id="agent-installation-on-DC"></a>**Agentinstallationen misslyckas på Active Directory-domän controller**  
Om du försöker installera synkroniseringsagenten på en Active Directory-domänkontrollant där PDC-rollägaren finns på en Windows Server 2008 R2-version eller under OS-versionen kan du få problem där synkroniseringsagenten inte kan installeras.

Lös problemet genom att överföra PDC-rollen till en annan domänkontrollant som kör Windows Server 2012 R2 eller senare och sedan installera synkronisering.

<a id="parameter-is-incorrect"></a>**Det går inte att komma åt en volym på Windows Server 2012 R2 med fel: Parametern är felaktig**  
När du har skapat en serverslutpunkt på Windows Server 2012 R2 uppstår följande fel vid åtkomst till volymen:

driveletter:\ är inte tillgänglig.  
Parametern är felaktig.

Lös problemet genom att installera [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) och starta om servern. Om den här uppdateringen inte installeras eftersom en senare uppdatering redan har installerats går du till Windows Update, installerar de senaste uppdateringarna för Windows Server 2012 R2 och startar om servern.

<a id="server-registration-missing-subscriptions"></a>**Serverregistrering visar inte alla Azure-prenumerationer**  
När du registrerar en server ServerRegistration.exe prenumeration saknas prenumerationer när du klickar på listrutan Azure-prenumeration.

Det här problemet beror på ServerRegistration.exe endast hämtar prenumerationer från de första 5 Azure AD-klientorganisationen. 

Om du vill öka klientgränsen för serverregistrering på servern skapar du ett DWORD-värde med namnet ServerRegistrationTenantLimit under HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync med ett värde som är större än 5.

Du kan också lösa det här problemet genom att använda följande PowerShell-kommandon för att registrera servern:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Serverregistrering visar följande meddelande: "Förutsättningar saknas"**  
Det här meddelandet visas om Az eller AzureRM PowerShell-modulen inte är installerad på PowerShell 5.1. 

> [!Note]  
> ServerRegistration.exe stöder inte PowerShell 6.x. Du kan använda Register-AzStorageSyncServer cmdlet på PowerShell 6.x för att registrera servern.

Utför följande steg för att installera Az- eller AzureRM-modulen på PowerShell 5.1:

1. Skriv **powershell från** en upphöjd kommandotolk och tryck på Retur.
2. Installera den senaste Az- eller AzureRM-modulen genom att följa dokumentationen:
    - [Az-modul (kräver .NET 4.7.2)](/powershell/azure/install-az-ps)
    - [AzureRM-modul](https://go.microsoft.com/fwlink/?linkid=856959)
3. Kör ServerRegistration.exe och slutför guiden för att registrera servern med en tjänst för synkronisering av lagring.

<a id="server-already-registered"></a>**Serverregistrering visar följande meddelande: "Den här servern är redan registrerad"** 

![En skärmbild av dialogrutan Serverregistrering med felmeddelandet "servern är redan registrerad"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Det här meddelandet visas om servern tidigare har registrerats med en tjänst för synkronisering av lagring. Om du vill avregistrera servern från den aktuella tjänsten för synkronisering av lagring och sedan registrera dig för en ny tjänst för synkronisering av lagring slutför du stegen som beskrivs i [Avregistrera](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service)en server med Azure File Sync .

Om servern inte visas under **Registrerade** servrar i tjänsten för synkronisering av lagring kör du följande PowerShell-kommandon på den server som du vill avregistrera:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Om servern är en del av ett kluster kan du använda den valfria parametern *Reset-StorageSyncServer -CleanClusterRegistration* för att även ta bort klusterregistreringen.

<a id="web-site-not-trusted"></a>**När jag registrerar en server ser jag många svar på "webbplatsen är inte betrodd". Varför?**  
Det här problemet uppstår när **principen Internet Explorer utökad säkerhet** aktiveras under serverregistreringen. Mer information om hur du inaktiverar principen förbättrad **Internet Explorer-säkerhet** finns i [Förbereda Windows Server](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) för användning med Azure File Sync och Så här [distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Servern visas inte under registrerade servrar i Azure Portal**  
Om en server inte visas under Registrerade **servrar för** en tjänst för synkronisering av lagring:
1. Logga in på den server som du vill registrera.
2. Öppna Utforskaren och gå sedan till installationskatalogen för Storage Sync Agent (standardplatsen är C:\Program Files\Azure\StorageSyncAgent). 
3. Kör ServerRegistration.exe och slutför guiden för att registrera servern med en tjänst för synkronisering av lagring.

## <a name="sync-group-management"></a>Hantering av synkroniseringsgrupp

### <a name="cloud-endpoint-creation-errors"></a>Fel vid skapande av molnslutpunkt

<a id="cloud-endpoint-using-share"></a>**Det går inte att skapa molnslutpunkten med det här felet: "Angiven Azure FileShare används redan av en annan CloudEndpoint"**  
Det här felet uppstår om Azure-filresursen redan används av en annan molnslutpunkt. 

Om du ser det här meddelandet och Azure-filresursen för närvarande inte används av en molnslutpunkt utför du följande steg för att rensa Azure File Sync-metadata på Azure-filresursen:

> [!Warning]  
> Om du tar bort metadata på en Azure-filresurs som för närvarande används av en molnslutpunkt misslyckas Azure File Sync åtgärder. Om du sedan använder den här filresursen för synkronisering i en annan synkroniseringsgrupp är dataförlust för filer i den gamla synkroniseringsgruppen nästan säker.

1. I Azure Portal du till Azure-filresursen.  
2. Högerklicka på Azure-filresursen och välj sedan **Redigera metadata.**
3. Högerklicka på **SyncService** och välj sedan Ta **bort.**

<a id="cloud-endpoint-authfailed"></a>**Det går inte att skapa molnslutpunkten med det här felet: "AuthorizationFailed"**  
Det här felet uppstår om ditt användarkonto inte har tillräcklig behörighet för att skapa en molnslutpunkt. 

Om du vill skapa en molnslutpunkt måste ditt användarkonto ha följande Microsoft-auktoriseringsbehörigheter:  
* Läsa: Hämta rolldefinition
* Skriva: Skapa eller uppdatera anpassad rolldefinition
* Läsa: Hämta rolltilldelning
* Skriva: Skapa rolltilldelning

Följande inbyggda roller har nödvändiga Microsoft-auktoriseringsbehörigheter:  
* Ägare
* Administratör för användaråtkomst

Så här avgör du om din användarkontoroll har de behörigheter som krävs:  
1. I Azure Portal väljer du **Resursgrupper.**
2. Välj den resursgrupp där lagringskontot finns och välj sedan **Åtkomstkontroll (IAM).**
3. Välj **fliken Rolltilldelningar.**
4. Välj **Roll** (till exempel Ägare eller Deltagare) för ditt användarkonto.
5. I listan **Resursprovider** väljer du **Microsoft Authorization**. 
    * **Rolltilldelningen** ska ha **läs-** och **skrivbehörighet.**
    * **Rolldefinitionen** ska ha **läs-** och **skrivbehörighet.**

### <a name="server-endpoint-creation-and-deletion-errors"></a>Fel vid skapande och borttagning av serverslutpunkt

<a id="-2134375898"></a>**Det går inte att skapa serverslutpunkten med följande fel: "MgmtServerJobFailed" (Felkod: -2134375898 eller 0x80c80226)**  
Felet uppstår om sökvägen till serverslutpunkten finns på systemvolymen och molnnivåindelning är aktiverat. Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.

<a id="-2147024894"></a>**Det går inte att skapa serverslutpunkten med följande fel: "MgmtServerJobFailed" (Felkod: -2147024894 eller 0x80070002)**  
Det här felet uppstår om den angivna sökvägen till serverslutpunkten är ogiltig. Kontrollera att sökvägen till serverslutpunkten är en lokalt ansluten NTFS-volym. Observera att Azure File Sync inte stöder mappade enheter som sökvägar till serverslutpunkter.

<a id="-2134375640"></a>**Det går inte att skapa serverslutpunkten med följande fel: "MgmtServerJobFailed" (Felkod: -2134375640 eller 0x80c80328)**  
Det här felet uppstår om den angivna sökvägen till serverslutpunkten inte är en NTFS-volym. Kontrollera att sökvägen till serverslutpunkten är en lokalt ansluten NTFS-volym. Observera att Azure File Sync inte stöder mappade enheter som sökvägar till serverslutpunkter.

<a id="-2134347507"></a>**Det går inte att skapa serverslutpunkten med det här felet: "MgmtServerJobFailed" (Felkod: -2134347507 eller 0x80c8710d)**  
Det här felet beror på att Azure File Sync inte stöder serverslutpunkter på volymer som har en komprimerad System Volume Information-mapp. Lös problemet genom att expandera mappen System Volume Information. Om mappen System Volume information är den enda mappen som komprimeras på volymen utför du följande steg:

1. Ladda [ned PsExec-verktyget.](/sysinternals/downloads/psexec)
2. Kör följande kommando från en upphöjd kommandotolk för att starta en kommandotolk som körs under systemkontot: **PsExec.exe -i -s -d cmd**
3. Från kommandotolken som körs under systemkontot skriver du följande kommandon och trycker på RETUR:   
    **cd /d "drive letter:\System Volume Information"**  
    **kompakt /u /s**

<a id="-2134376345"></a>**Det går inte att skapa serverslutpunkten med följande fel: "MgmtServerJobFailed" (Felkod: -2134376345 eller 0x80C80067)**  
Det här felet uppstår om gränsen för serverslutpunkter per server uppnås. Azure File Sync stöder för närvarande upp till 30 serverslutpunkter per server. Mer information finns i Azure File Sync [skalningsmål.](./storage-files-scale-targets.md#azure-file-sync-scale-targets)

<a id="-2134376427"></a>**Det går inte att skapa serverslutpunkten med följande fel: "MgmtServerJobFailed" (Felkod: -2134376427 eller 0x80c80015)**  
Det här felet uppstår om en annan serverslutpunkt redan synkroniserar den angivna serversökvägen. Azure File Sync har inte stöd för att flera serverslutpunkter synkroniserar samma katalog eller volym.

<a id="-2160590967"></a>**Det går inte att skapa serverslutpunkten med det här felet: "MgmtServerJobFailed" (Felkod: -2160590967 eller 0x80c80077)**  
Det här felet uppstår om sökvägen till serverslutpunkten innehåller överblivna nivåindelade filer. Om en serverslutpunkt nyligen har tagits bort väntar du tills rensningen av de överblivna nivåindelade filerna har slutförts. Händelse-ID 6662 loggas i telemetrihändelseloggen när rensningen av överblivna nivåindelade filer har startat. Händelse-ID 6661 loggas när rensningen av överblivna nivåindelade filer har slutförts och en serverslutpunkt kan återskapas med hjälp av sökvägen. Om det inte går att skapa serverslutpunkten efter att rensningen av nivåindelade filer har slutförts eller om händelse-ID 6661 inte kan hittas i händelseloggen för telemetri på grund av händelseloggens sammanslagning tar du bort de överblivna nivåindelade filerna genom att utföra stegen som beskrivs i nivåindelade filer är inte tillgängliga på servern efter att en [serverslutpunkt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) har tagits bort.

<a id="-2134347757"></a>**Borttagningen av serverslutpunkten misslyckas med följande fel: "MgmtServerJobExpired" (Felkod: -2134347757 eller 0x80c87013)**  
Det här felet uppstår om servern är offline eller inte har någon nätverksanslutning. Om servern inte längre är tillgänglig avregistrerar du servern i portalen, vilket tar bort serverslutpunkterna. Om du vill ta bort serverslutpunkterna följer du stegen som beskrivs i [Avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

### <a name="server-endpoint-health"></a>Serverslutpunktshälsa

<a id="server-endpoint-provisioningfailed"></a>**Det går inte att öppna egenskapssidan för serverslutpunkten eller uppdatera molnnivåprincipen**  
Det här problemet kan inträffa om en hanteringsåtgärd på serverslutpunkten misslyckas. Om egenskapssidan för serverslutpunkten inte öppnas i Azure Portal kan det här problemet åtgärdas om serverslutpunkten uppdateras med Hjälp av PowerShell-kommandon från servern. 

```powershell
# Get the server endpoint id based on the server endpoint DisplayName property
Get-AzStorageSyncServerEndpoint `
    -ResourceGroupName myrgname `
    -StorageSyncServiceName storagesvcname `
    -SyncGroupName mysyncgroup | `
Tee-Object -Variable serverEndpoint

# Update the free space percent policy for the server endpoint
Set-AzStorageSyncServerEndpoint `
    -InputObject $serverEndpoint
    -CloudTiering `
    -VolumeFreeSpacePercent 60
```
<a id="server-endpoint-noactivity"></a>**Serverslutpunkten har hälsostatusen "Ingen aktivitet" eller "Väntar" och servertillståndet på bladet med registrerade servrar är "Visas offline"**  

Det här problemet kan inträffa om Storage Sync Monitor-processen (AzureStorageSyncMonitor.exe) inte körs eller om servern inte kan komma åt Azure File Sync tjänsten.

På servern som visas som "Visas offline" i portalen tittar du på Händelse-ID 9301 i telemetrihändelseloggen (finns under Program och tjänster\Microsoft\FileSync\Agent i Loggboken) för att ta reda på varför servern inte kan komma åt Azure File Sync-tjänsten. 

- Om **GetNextJob har slutförts med status: 0** loggas kan servern kommunicera med Azure File Sync tjänsten. 
    - Öppna Aktivitetshanteraren på servern och kontrollera att Storage Sync Monitor-processen (AzureStorageSyncMonitor.exe) körs. Om processen inte körs provar du först att starta om servern. Om det inte går att lösa problemet genom att starta om servern uppgraderar du till den senaste [agentversionen](./storage-files-release-notes.md) för Azure File Sync. 

- Om GetNextJob slutfördes med **status: -2134347756** loggas, kan servern inte kommunicera med Azure File Sync-tjänsten på grund av en konfiguration av brandväggen, proxyn eller TLS-chiffersviten. 
    - Om servern finns bakom en brandvägg kontrollerar du att port 443 för utgående trafik tillåts. Om brandväggen begränsar trafik till specifika domäner bekräftar du att domänerna som anges i [brandväggsdokumentationen](./storage-sync-files-firewall-and-proxy.md#firewall) är tillgängliga.
    - Om servern finns bakom en proxyserver konfigurerar du de datoromfattande eller appspecifika proxyinställningarna genom att följa stegen i [proxydokumentationen](./storage-sync-files-firewall-and-proxy.md#proxy).
    - Använd Test-StorageSyncNetworkConnectivity för att kontrollera nätverksanslutningen till tjänstslutpunkterna. Mer information finns i Testa [nätverksanslutning till tjänstslutpunkter.](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints)
    - Om TLS-chiffersvitordningen har konfigurerats på servern kan du använda grupprinciper eller TLS-cmdlets för att lägga till chiffersviter:
        - Information om hur du använder grupprinciper [finns i Konfigurera TLS-chiffersvitordning med hjälp av grupprincip](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy).
        - Information om hur du använder TLS-cmdlets finns i [Configuring TLS Cipher Suite Order by using TLS PowerShell Cmdlets (Konfigurera TLS-chiffersvitordning med hjälp av TLS PowerShell-cmdlets).](/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets)
    
        Azure File Sync stöder för närvarande följande chiffersviter för TLS 1.2-protokollet:  
        - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256  

- Om GetNextJob slutfördes med **status: -2134347764** loggas, kan servern inte kommunicera med Azure File Sync-tjänsten på grund av ett utgånget eller borttagna certifikat.  
    - Kör följande PowerShell-kommando på servern för att återställa certifikatet som används för autentisering:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Serverslutpunkten har hälsostatusen "Ingen aktivitet" och servertillståndet på bladet med registrerade servrar är "Online"**  

Hälsostatusen ”Ingen aktivitet” för serverslutpunkten betyder att serverslutpunkten inte har loggat synkroniseringsaktivitet under de senaste två timmarna.

Om du vill kontrollera den aktuella synkroniseringsaktiviteten [på en server Hur gör jag för att övervaka förloppet för en aktuell synkroniseringssession?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

En serverslutpunkt kanske inte loggar synkroniseringsaktivitet på flera timmar på grund av en bugg eller otillräcklig systemresurser. Kontrollera att den Azure File Sync [agentversionen](./storage-files-release-notes.md) är installerad. Om problemet kvarstår öppnar du en supportbegäran.

> [!Note]  
> Om servertillståndet på bladet med registrerade servrar är "Visas offline" utför du stegen som beskrivs i serverslutpunkten har hälsostatusen "Ingen aktivitet" eller "Väntar" och servertillståndet på bladet med registrerade servrar är [avsnittet "Visas offline".](#server-endpoint-noactivity)

## <a name="sync"></a>Synkronisera
<a id="afs-change-detection"></a>**Hur lång tid tar det innan filen synkroniseras till servrar i synkroniseringsgruppen om jag har skapat en fil direkt i min Azure-filresurs via SMB eller via portalen?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Serverslutpunktens hälsa är i ett väntande tillstånd i flera timmar**  
Det här problemet är förväntat om du skapar en molnslutpunkt och använder en Azure-filresurs som innehåller data. Ändringsuppräkningsjobbet som söker efter ändringar i Azure-filresursen måste slutföras innan filer kan synkroniseras mellan molnet och serverslutpunkter. Hur lång tid jobbet tar beror på storleken på namnområdet i Azure-filresursen. Serverslutpunktens hälsa bör uppdateras när uppräkningsjobbet har slutförts.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Hur gör jag för att övervaka synkroniseringshälsa?
# <a name="portal"></a>[Portal](#tab/portal1)
I varje synkroniseringsgrupp kan du öka detaljgranskningen i de enskilda serverslutpunkterna för att se status för de senaste slutförda synkroniseringssessionerna. En grön hälsokolumn och värdet 0 För filer som inte synkroniseras indikerar att synkroniseringen fungerar som förväntat. Om så inte är fallet, se nedan för en lista över vanliga synkroniseringsfel och hur du hanterar filer som inte synkroniseras. 

![En skärmbild av Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Gå till serverns telemetriloggar, som finns i Loggboken på `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . Händelse 9102 motsvarar en slutförd synkroniseringssession. Om du vill ha den senaste synkroniseringsstatusen letar du upp den senaste händelsen med ID 9102. SyncDirection anger om den här sessionen var en uppladdning eller nedladdning. Om HResult är 0 lyckades synkroniseringssessionen. Ett HResult som inte är noll innebär att det uppstod ett fel under synkroniseringen. nedan finns en lista över vanliga fel. Om PerItemErrorCount är större än 0 innebär det att vissa filer eller mappar inte synkroniserades korrekt. Det är möjligt att ha ett HResult på 0 men ett PerItemErrorCount som är större än 0.

Nedan visas ett exempel på en lyckad uppladdning. Av utrymmesskäl visas bara några av värdena i varje 9102-händelse nedan. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Omvänt kan en misslyckad uppladdning se ut så här:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Ibland misslyckas synkroniseringssessioner totalt eller har en PerItemErrorCount som inte är noll, men fortsätter fortfarande, och vissa filer synkroniseras korrekt. Detta kan ses i fälten Tillämpad* (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes), som visar hur mycket av sessionen som lyckas. Om du ser flera synkroniseringssessioner på en rad som misslyckas men har ett ökande antal tillämpade* bör du ge synkroniseringen tid att försöka igen innan du öppnar en supportbiljett.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hur övervakar jag förloppet för en pågående synkronisering?
# <a name="portal"></a>[Portal](#tab/portal1)
I synkroniseringsgruppen går du till den aktuella serverslutpunkten och tittar på avsnittet Synkroniseringsaktivitet för att se antalet filer som laddats upp eller ned i den aktuella synkroniseringssessionen. Observera att denna status fördröjs med cirka 5 minuter och om synkroniseringssessionen är tillräckligt liten att slutföras inom den tiden kanske den inte rapporteras i portalen. 

# <a name="server"></a>[Server](#tab/server)
Titta på den senaste 9302-händelsen i telemetriloggen på servern (i Loggboken går du till Program- och tjänstloggar\Microsoft\FileSync\Agent\Telemetry). Den här händelsen anger statusen för den aktuella synkroniseringssessionen. TotalItemCount anger hur många filer som ska synkroniseras, AppliedItemCount antalet filer som har synkroniserats hittills och PerItemErrorCount antalet filer som inte kan synkroniseras (se nedan för hur du hanterar detta).

```
Replica Sync Progress. 
ServerEndpointName: <CI>sename</CI>, SyncGroupName: <CI>sgname</CI>, ReplicaName: <CI>rname</CI>, 
SyncDirection: Upload, CorrelationId: {AB4BA07D-5B5C-461D-AAE6-4ED724762B65}. 
AppliedItemCount: 172473, TotalItemCount: 624196. AppliedBytes: 51473711577, 
TotalBytes: 293363829906. 
AreTotalCountsFinal: true. 
PerItemErrorCount: 1006.
```
---

### <a name="how-do-i-know-if-my-servers-are-in-sync-with-each-other"></a>Hur vet jag att servrarna är synkroniserade med varandra?
# <a name="portal"></a>[Portal](#tab/portal1)
Kontrollera följande för varje server i en viss synkroniseringsgrupp:
- Tidsstämplar för senaste synkroniseringsförsök för både uppladdning och nedladdning är senaste.
- Statusen är grön för både uppladdning och nedladdning.
- Fältet Synkroniseringsaktivitet visar mycket få eller inga filer som återstår att synkronisera.
- Fältet Filer synkroniseras inte är 0 för både uppladdning och nedladdning.

# <a name="server"></a>[Server](#tab/server)
Titta på de slutförda synkroniseringssessionerna, som är markerade med 9102-händelser i telemetrihändelseloggen för varje server (i Loggboken går du till `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ). 

1. På en viss server vill du se till att de senaste uppladdnings- och nedladdningssessionerna har slutförts. Det gör du genom att kontrollera att HResult och PerItemErrorCount är 0 för både uppladdning och nedladdning (fältet SyncDirection anger om en viss session är en uppladdnings- eller nedladdningssession). Observera att om du inte ser en nyligen slutförd synkroniseringssession är det troligt att en synkroniseringssession pågår, vilket är förväntat om du precis har lagt till eller ändrat en stor mängd data.
2. När en server är helt uppdaterad med molnet och inte har några ändringar att synkronisera i någon av riktningarna visas tomma synkroniseringssessioner. Dessa indikeras av uppladdnings- och nedladdningshändelser där alla sync*-fält (SyncFileCount, SyncDirCount, SyncTombstoneCount och SyncSizeBytes) är noll, vilket innebär att det inte fanns något att synkronisera. Observera att dessa tomma synkroniseringssessioner kanske inte inträffar på servrar med hög omsättning eftersom det alltid finns något nytt att synkronisera. Om det inte finns någon synkroniseringsaktivitet bör de ske var 30:e minut. 
3. Om alla servrar är uppdaterade med molnet, vilket innebär att deras senaste uppladdnings- och nedladdningssessioner är tomma synkroniseringssessioner, kan du med rimlig säkerhet säga att systemet som helhet är synkroniserat. 
    
Observera att om du har gjort ändringar direkt i Azure-filresursen Azure File Sync inte den här ändringen förrän ändringsuppräkningen körs, vilket sker en gång var 24:e timme. Det är möjligt att en server säger att den är uppdaterad med molnet när den i själva verket saknar de senaste ändringarna som gjorts direkt i Azure-filresursen. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hur gör jag för att se om det finns vissa filer eller mappar som inte synkroniseras?
Om ditt PerItemErrorCount på servern eller antalet filer som inte synkroniseras i portalen är större än 0 för en viss synkroniseringssession innebär det att vissa objekt inte kan synkroniseras. Filer och mappar kan ha egenskaper som hindrar dem från att synkroniseras. Dessa egenskaper kan vara beständiga och kräva explicita åtgärder för att återuppta synkroniseringen, till exempel att ta bort tecken som inte stöds från fil- eller mappnamnet. De kan också vara tillfälliga, vilket innebär att filen eller mappen automatiskt återupptar synkroniseringen. Filer med öppna referenser återupptar till exempel automatiskt synkroniseringen när filen stängs. När Azure File Sync identifierar ett sådant problem skapas en fellogg som kan parsas för att lista de objekt som för närvarande inte synkroniseras korrekt.

Om du vill se dessa fel kör du **FileSyncErrorsReport.ps1** PowerShell-skriptet (finns i katalogen för agentinstallation för Azure File Sync-agenten) för att identifiera filer som inte kunde synkroniseras på grund av öppna referenser, tecken som inte stöds eller andra problem. Fältet ItemPath visar platsen för filen i förhållande till rotsynkroniseringskatalogen. Se listan över vanliga synkroniseringsfel nedan för reparationssteg.

> [!Note]  
> Om FileSyncErrorsReport.ps1 returnerar "Det fanns inga filfel hittades" eller inte listar fel per objekt för synkroniseringsgruppen är orsaken antingen:
>
>- Orsak 1: Den senaste slutförda synkroniseringssessionen hade inga fel per objekt. Portalen bör uppdateras snart för att visa 0 Filer synkroniseras inte. 
>    - Kontrollera [händelse-ID 9102](?tabs=server%252cazure-portal#broken-sync) i telemetrihändelseloggen för att bekräfta att PerItemErrorCount är 0. 
>
>- Orsak 2: Händelseloggen ItemResults på servern omsluten på grund av för många fel per objekt och händelseloggen innehåller inte längre fel för den här synkroniseringsgruppen.
>    - Du kan förhindra det här problemet genom att öka storleken på ItemResults-händelseloggen. Händelseloggen ItemResults finns under "Program- och tjänstloggar\Microsoft\FileSync\Agent" i Loggboken. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Felsöka synkroniseringsfel per fil/katalog
**ItemResults-logg – synkroniseringsfel per objekt**  

| Hresult | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | -2147942467 | ERROR_BAD_NET_NAME | Den nivåindelade filen på servern är inte tillgänglig. Det här problemet uppstår om den nivåindelade filen inte återkallades innan du tog bort en serverslutpunkt. | Information om hur du löser problemet finns [i Nivåindelade filer är inte tillgängliga på servern när du har tagit bort en serverslutpunkt.](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) |
| 0x80c80207 | -2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Fil- eller katalogändringen kan inte synkroniseras ännu eftersom en beroende mapp ännu inte har synkroniserats. Det här objektet synkroniseras när beroende ändringar har synkroniserats. | Ingen åtgärd krävs. Om felet kvarstår i flera dagar använder du powershellFileSyncErrorsReport.ps1 skriptet för att avgöra varför den beroende mappen ännu inte har synkroniserats. |
| 0x80C8028A | -2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | Fil- eller katalogändringen kan inte synkroniseras ännu eftersom en beroende mapp ännu inte har synkroniserats. Det här objektet synkroniseras när beroende ändringar har synkroniserats. | Ingen åtgärd krävs. Om felet kvarstår i flera dagar använder du powershellFileSyncErrorsReport.ps1 skriptet för att avgöra varför den beroende mappen ännu inte har synkroniserats. |
| 0x80c80284 | -2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Fil- eller katalogändringen kan inte synkroniseras ännu eftersom en beroende mapp ännu inte har synkroniserats och synkroniseringssessionen misslyckades. Det här objektet synkroniseras när beroende ändringar har synkroniserats. | Ingen åtgärd krävs. Om felet kvarstår undersöker du synkroniseringssessionsfelet. |
| 0x8007007b | -2147024773 | ERROR_INVALID_NAME | Fil- eller katalognamnet är ogiltigt. | Byt namn på filen eller katalogen i fråga. Mer information [finns i Hantera tecken som](?tabs=portal1%252cazure-portal#handling-unsupported-characters) inte stöds. |
| 0x80c80255 | -2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Fil- eller katalognamnet är ogiltigt. | Byt namn på filen eller katalogen i fråga. Mer information [finns i Hantera tecken som](?tabs=portal1%252cazure-portal#handling-unsupported-characters) inte stöds. |
| 0x80c80018 | -2134376424 | ECS_E_SYNC_FILE_IN_USE | Filen kan inte synkroniseras eftersom den används. Filen synkroniseras när den inte längre används. | Ingen åtgärd krävs. Azure File Sync skapar en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser. |
| 0x80c8031d | -2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Filen har ändrats, men ändringen har ännu inte identifierats av synkroniseringen. Sync återställs när den här ändringen har identifierats. | Ingen åtgärd krävs. |
| 0x80070002 | -2147024894 | ERROR_FILE_NOT_FOUND | Filen har tagits bort och sync känner inte till ändringen. | Ingen åtgärd krävs. Sync slutar logga det här felet när ändringsidentifiering upptäcker att filen har tagits bort. |
| 0x80070003 | -2147942403 | ERROR_PATH_NOT_FOUND | Det går inte att synkronisera borttagningen av en fil eller katalog eftersom objektet redan har tagits bort i målet och synkroniseringen inte känner till ändringen. | Ingen åtgärd krävs. Sync slutar logga det här felet när ändringsidentifiering körs på målet och synkroniseringen identifierar att objektet har tagits bort. |
| 0x80c80205 | -2134375931 | ECS_E_SYNC_ITEM_SKIP | Filen eller katalogen hoppas över men synkroniseras under nästa synkroniseringssession. Om det här felet rapporteras när du laddar ned objektet är fil- eller katalognamnet mer än sannolikt ogiltigt. | Ingen åtgärd krävs om det här felet rapporteras när filen laddas upp. Om felet rapporteras när du laddar ned filen byter du namn på filen eller katalogen i fråga. Mer information [finns i Hantera tecken som](?tabs=portal1%252cazure-portal#handling-unsupported-characters) inte stöds. |
| 0x800700B7 | -2147024713 | ERROR_ALREADY_EXISTS | Det går inte att synkronisera skapandet av en fil eller katalog eftersom objektet redan finns i målet och synkroniseringen inte känner till ändringen. | Ingen åtgärd krävs. Synkroniseringen kommer att sluta logga det här felet när ändringsidentifieringen körs på målet och Sync upptäcker det nya objektet. |
| 0x80c8603e | -2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Det går inte att synkronisera filen eftersom gränsen för Azure-filresursen har nåtts. | Information om hur du löser problemet [finns i avsnittet Om lagringsgränsen](?tabs=portal1%252cazure-portal#-2134351810) för Azure-filresurser i felsökningsguiden. |
| 0x80c8027C | -2134375812 | ECS_E_ACCESS_DENIED_EFS | Filen krypteras av en lösning som inte stöds (t.ex. NTFS EFS). | Dekryptera filen och använd en krypteringslösning som stöds. En lista över lösningar som stöds finns i avsnittet [Krypteringslösningar](./storage-sync-files-planning.md#encryption) i planeringsguiden. |
| 0x80c80283 | -2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Filen finns i en skrivskyddade DFS-R-replikeringsmapp. | Filen finns i en skrivskyddade DFS-R-replikeringsmapp. Azure Files Sync stöder inte serverslutpunkter i skrivskyddade DFS-R-replikeringsmappar. Mer information finns i [planeringsguiden](./storage-sync-files-planning.md#distributed-file-system-dfs). |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Filen har ett väntande borttagningstillstånd. | Ingen åtgärd krävs. Filen tas bort när alla öppna filreferenser har stängts. |
| 0x80c86044 | -2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Filen kan inte synkroniseras eftersom inställningarna för brandvägg och virtuellt nätverk på lagringskontot är aktiverade och servern inte har åtkomst till lagringskontot. | Lägg till serverns IP-adress eller virtuella nätverk genom att följa stegen i avsnittet Konfigurera inställningar [för](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) brandvägg och virtuellt nätverk i distributionsguiden. |
| 0x80c80243 | -2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Filen kan inte synkroniseras eftersom säkerhetsbeskrivningens storlek överskrider gränsen på 64 KiB. | Lös problemet genom att ta bort filens åtkomstkontrollposter så att säkerhetsbeskrivningen minskar i storlek. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Filen kan inte synkroniseras på grund av ett oväntat fel. | Om felet kvarstår i flera dagar öppnar du ett supportfall. |
| 0x80070020 | -2147024864 | ERROR_SHARING_VIOLATION | Det går inte att synkronisera filen eftersom den används. Filen synkroniseras när den inte längre används. | Ingen åtgärd krävs. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Filen ändrades under synkroniseringen, så den måste synkroniseras igen. | Ingen åtgärd krävs. |
| 0x80070017 | -2147024873 | ERROR_CRC | Det går inte att synkronisera filen på grund av ett CRC-fel. Det här felet kan inträffa om en nivåindelad fil inte återkallades innan du tog bort en serverslutpunkt eller om filen är skadad. | Information om hur du löser problemet finns i Nivåindelade filer som inte är tillgängliga på servern när du har tagit bort en [serverslutpunkt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) för att ta bort nivåindelade filer som är överblivna. Om felet kvarstår när du har tagit bort överblivna nivåindelade filer kör [du chkdsk](/windows-server/administration/windows-commands/chkdsk) på volymen. |
| 0x80c80200 | -2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Filen kan inte synkroniseras eftersom det maximala antalet konfliktfiler har nåtts. Azure File Sync har stöd för 100 konfliktfiler per fil. Mer information om filkonflikter finns i Azure File Sync [vanliga frågor och svar.](./storage-files-faq.md#afs-conflict-resolution) | Du kan lösa problemet genom att minska antalet motstridiga filer. Filen kommer att synkroniseras när antalet motstridiga filer är mindre än 100. |

#### <a name="handling-unsupported-characters"></a>Hantera tecken som inte stöds
Om **FileSyncErrorsReport.ps1** PowerShell-skriptet visar synkroniseringsfel per objekt på grund av tecken som inte stöds (felkod 0x8007007b eller 0x80c80255) bör du ta bort eller byta namn på tecken vid fel från respektive filnamn. PowerShell skriver sannolikt ut dessa tecken som frågetecken eller tomma rektanglar eftersom de flesta av dessa tecken inte har någon standardkodning av visuella objekt. 
> [!Note]  
> [Utvärderingsverktyget](storage-sync-files-planning.md#evaluation-cmdlet) kan användas för att identifiera tecken som inte stöds. Om datauppsättningen har flera filer med ogiltiga tecken använder du [skriptet ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) för att byta namn på filer som innehåller tecken som inte stöds.

Tabellen nedan innehåller alla Unicode-tecken som Azure File Sync stöder ännu inte.

| Teckenuppsättning | Antal tecken |
|---------------|-----------------|
| 0x00000000 – 0x0000001F (kontrolltecken) | 32 |
| 0x0000FDD0 – 0x0000FDDD (arabiska presentation formulär-a) | 14 |
| <ul><li>0x00000022 (citattecken)</li><li>0x0000002A (asterisk)</li><li>0x0000002F (snedstreck)</li><li>0x0000003A (kolon)</li><li>0x0000003C (mindre än)</li><li>0x0000003E (större än)</li><li>0x0000003F (frågetecken)</li><li>0x0000005C (omsnedstreck)</li><li>0x0000007C (rör eller stapel)</li></ul> | 9 |
| <ul><li>0x0004FFFE – 0x0004FFFF = 2 (icke-character)</li><li>0x0008FFFE – 0x0008FFFF = 2 (icke-character)</li><li>0x000CFFFE – 0x000CFFFF = 2 (icke-character)</li><li>0x0010FFFE – 0x0010FFFF = 2 (icke-character)</li></ul> | 8 |
| <ul><li>0x0000009D (osc-operativsystemkommando)</li><li>0x00000090 (dcs-enhetens kontrollsträng)</li><li>0x0000008F (ss3 single shift three)</li><li>0x00000081 (förinställd hög oktett)</li><li>0x0000007F (del delete)</li><li>0x0000008D (ri reverse line feed)</li></ul> | 6 |
| 0x0000FFF0, 0x0000FFFD, 0x0000FFFE, 0x0000FFFF (specialerbjudanden) | 4 |
| Filer eller kataloger som slutar med en punkt | 1 |

### <a name="common-sync-errors"></a>Vanliga synkroniseringsfel
<a id="-2147023673"></a>**Synkroniseringssessionen avbröts.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x800704c7 |
| **HRESULT (decimal)** | -2147023673 | 
| **Felsträng** | ERROR_CANCELLED |
| **Reparation krävs** | No |

Synkroniseringssessioner kan misslyckas av olika orsaker, till exempel att servern startas om eller uppdateras, VSS-ögonblicksbilder osv. Även om det här felet verkar kräva uppföljning är det säkert att ignorera det här felet om det inte kvarstår under en period på flera timmar.

<a id="-2147012889"></a>**Det gick inte att upprätta en anslutning till tjänsten.**    

| Fel | Kod |
|-|-|
| **Hresult** | 0x80072ee7 |
| **HRESULT (decimal)** | -2147012889 | 
| **Felsträng** | WININET_E_NAME_NOT_RESOLVED |
| **Reparation krävs** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Användarbegäran begränsades av tjänsten.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8004c |
| **HRESULT (decimal)** | -2134376372 |
| **Felsträng** | ECS_E_USER_REQUEST_THROTTLED |
| **Reparation krävs** | No |

Ingen åtgärd krävs. servern försöker igen. Om felet kvarstår i flera timmar kan du skapa en supportförfrågan.

<a id="-2134364043"></a>**Synkroniseringen blockeras tills ändringsidentifiering har slutförts efter återställningen**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c83075 |
| **HRESULT (decimal)** | -2134364043 |
| **Felsträng** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Reparation krävs** | No |

Ingen åtgärd krävs. När en fil eller filresurs (molnslutpunkt) återställs med Azure Backup blockeras synkroniseringen tills ändringsidentifiering har slutförts på Azure-filresursen. Den här identifieringen körs direkt när återställningen är färdig och hur lång tid det tar beror på antalet filer i filresursen.

<a id="-2147216747"></a>**Sync misslyckades eftersom synkroniseringsdatabasen togs bort.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80041295 |
| **HRESULT (decimal)** | -2147216747 |
| **Felsträng** | SYNC_E_METADATA_INVALID_OPERATION |
| **Reparation krävs** | No |

Det här felet uppstår vanligtvis när ett säkerhetskopieringsprogram skapar en VSS-ögonblicksbild och Sync-databasen tas bort från minnet. Om felet kvarstår i flera timmar kan du skapa en supportförfrågan.

<a id="-2134364065"></a>**Sync kan inte komma åt Azure-filresursen som anges i molnslutpunkten.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8305f |
| **HRESULT (decimal)** | -2134364065 |
| **Felsträng** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Reparation krävs** | Yes |

Det här felet beror på att Azure File Sync-agenten inte kommer åt Azure-filresursen, vilket kan bero på att Azure-filresursen eller lagringskontot som är värd inte längre finns. Du kan felsöka det här felet genom att utföra följande steg:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att Azure-filresursen finns.](#troubleshoot-azure-file-share)
3. [Kontrollera Azure File Sync har åtkomst till lagringskontot.](#troubleshoot-rbac)
4. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Sync misslyckades eftersom begäran inte har behörighet att utföra den här åtgärden.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c86044 |
| **HRESULT (decimal)** | -2134351804 |
| **Felsträng** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Reparation krävs** | Yes |

Det här felet beror på Azure File Sync agenten inte har behörighet att komma åt Azure-filresursen. Du kan felsöka det här felet genom att utföra följande steg:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att Azure-filresursen finns.](#troubleshoot-azure-file-share)
3. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Kontrollera Azure File Sync har åtkomst till lagringskontot.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Det gick inte att matcha lagringskontots namn.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80C83060 |
| **HRESULT (decimal)** | -2134364064 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Reparation krävs** | Yes |

1. Kontrollera att du kan matcha lagrings-DNS-namnet från servern.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
3. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ett okänt fel inträffade vid åtkomst till lagringskontot.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8308a |
| **HRESULT (decimal)** | -2134364022 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Reparation krävs** | Yes |

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Sync misslyckades på grund av låst lagringskonto.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c83092 |
| **HRESULT (decimal)** | -2134364014 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Reparation krävs** | Yes |

Det här felet beror på att lagringskontot har ett skrivskyddade [resurslås.](../../azure-resource-manager/management/lock-resources.md) Lös problemet genom att ta bort det skrivskyddade resurslåset på lagringskontot. 

<a id="-1906441138"></a>**Sync misslyckades på grund av ett problem med synkroniseringsdatabasen.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x8e5e044e |
| **HRESULT (decimal)** | -1906441138 |
| **Felsträng** | JET_errWriteConflict |
| **Reparation krävs** | Yes |

Det här felet uppstår när det är problem med den interna databasen som används av Azure File Sync. När det här problemet uppstår skapar du en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2134364053"></a>**Den Azure File Sync agentversionen som är installerad på servern stöds inte.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80C8306B |
| **HRESULT (decimal)** | -2134364053 |
| **Felsträng** | ECS_E_AGENT_VERSION_BLOCKED |
| **Reparation krävs** | Yes |

Det här felet inträffar om den Azure File Sync-agentversion som är installerad på servern inte stöds. Lös problemet genom att uppgradera [till]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) en [agentversion som stöds.]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions)

<a id="-2134351810"></a>**Du har nått lagringsgränsen för Azure-filresursen.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8603e |
| **HRESULT (decimal)** | -2134351810 |
| **Felsträng** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Reparation krävs** | Yes |

Det här felet uppstår när du överskrider lagringsgränsen för Azure-filresursen, och det kan hända om en kvot används för en Azure-filresurs eller om användningen överskrider gränsen för en Azure-filresurs. Mer information finns i aktuella begränsningar [för en Azure-filresurs.](storage-files-scale-targets.md)

1. Gå till synkroniseringsgruppen i tjänsten för synkronisering av lagring.
2. Välj molnslutpunkten i synkroniseringsgruppen.
3. Notera Namnet på Azure-filresursen i det öppna fönstret.
4. Välj det länkade lagringskontot. Om den här länken misslyckas har det refererade lagringskontot tagits bort.

    ![En skärmbild som visar informationsfönstret för molnslutpunkten med en länk till lagringskontot.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Välj **Filer** för att visa listan över filresurser.
6. Klicka på de tre punkterna i slutet av raden för Azure-filresursen som molnslutpunkten refererar till.
7. Kontrollera att **Användning** är lägre än **Kvot**. Observera att om inte en alternativ kvot har angetts matchar kvoten [den maximala storleken för Azure-filresursen](storage-files-scale-targets.md).

    ![En skärmbild av egenskaperna för Azure-filresursen.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Om resursen är full och ingen kvot har angetts kan du åtgärda problemet genom att göra varje undermapp vid den aktuella serverslutpunkten till en egen serverslutpunkt i en egen separat synkroniseringsgrupp. På så vis synkroniseras varje undermapp till enskilda Azure-filresurser.

<a id="-2134351824"></a>**Det går inte att hitta Azure-filresursen.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c86030 |
| **HRESULT (decimal)** | -2134351824 |
| **Felsträng** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Reparation krävs** | Yes |

Felet uppstår när Azure-filresursen inte är tillgänglig. Så här felsöker du:

1. [Kontrollera att lagringskontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att Azure-filresursen finns.](#troubleshoot-azure-file-share)

Om Azure-filresursen har tagits bort måste du skapa en ny filresurs och sedan återskapa synkroniseringsgruppen. 

<a id="-2134364042"></a>**Synkroniseringen pausas när den här Azure-prenumerationen pausas.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80C83076 |
| **HRESULT (decimal)** | -2134364042 |
| **Felsträng** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Reparation krävs** | Yes |

Det här felet uppstår vid uppehåll i Azure-prenumerationen. Synkroniseringen aktiveras igen när Azure-prenumerationen återställs. Mer [information finns i Varför är min Azure-prenumeration](../../cost-management-billing/manage/subscription-disabled.md) inaktiverad och hur återaktiverar jag den?

<a id="-2134375618"></a>**Lagringskontot har en brandvägg eller virtuella nätverk konfigurerade.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8033e |
| **HRESULT (decimal)** | -2134375618 |
| **Felsträng** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Reparation krävs** | Yes |

Felet uppstår när det inte går att nå Azure-filresursen på grund av en brandvägg för lagringskontot eller om lagringskontot tillhör ett virtuellt nätverk. Kontrollera att inställningarna för brandväggen och det virtuella nätverket på lagringskontot är korrekt konfigurerade. Mer information finns i Konfigurera [inställningar för brandvägg och virtuellt nätverk.](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) 

<a id="-2134375911"></a>**Synkroniseringen misslyckades på grund av ett problem med synkroniseringsdatabasen.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c80219 |
| **HRESULT (decimal)** | -2134375911 |
| **Felsträng** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Reparation krävs** | No |

Felet brukar lösas av sig självt och inträffa om det finns:

* Ett stort antal filändringar på servrarna i synkroniseringsgruppen.
* Ett stort antal fel på enskilda filer och kataloger.

Om felet kvarstår i mer än några timmar skapar du en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2146762487"></a>**Servern kunde inte upprätta en säker anslutning. Molntjänsten tog emot ett oväntat certifikat.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x800b0109 |
| **HRESULT (decimal)** | -2146762487 |
| **Felsträng** | CERT_E_UNTRUSTEDROOT |
| **Reparation krävs** | Yes |

Det här felet kan inträffa om din organisation använder en TLS-avslutande proxy eller om en skadlig entitet fångar upp trafiken mellan servern och Azure File Sync tjänsten. Om du är säker på att detta är förväntat (eftersom din organisation använder en TLS-avslutande proxy) hoppar du över certifikatverifiering med en åsidosättning av registret.

1. Skapa registervärdet SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Starta om synkroniseringstjänsten på den registrerade servern.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Genom att ange det här registervärdet accepterar Azure File Sync-agenten alla lokalt betrodda TLS/SSL-certifikat vid överföring av data mellan servern och molntjänsten.

<a id="-2147012894"></a>**Det gick inte att upprätta en anslutning till tjänsten.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80072ee2 |
| **HRESULT (decimal)** | -2147012894 |
| **Felsträng** | WININET_E_TIMEOUT |
| **Reparation krävs** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Sync misslyckades på grund av ett autentiseringsproblem.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c80300 |
| **HRESULT (decimal)** | -2134375680 |
| **Felsträng** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Reparation krävs** | Yes |

Det här felet uppstår vanligtvis på grund av att servertiden är fel. Om servern körs på en virtuell dator kontrollerar du att tiden på värden är korrekt.

<a id="-2134364040"></a>**Sync misslyckades på grund av att certifikatet upphörde att gälla.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c83078 |
| **HRESULT (decimal)** | -2134364040 |
| **Felsträng** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Reparation krävs** | Yes |

Det här felet beror på att det certifikat som används för autentisering har upphört att gälla.

Bekräfta att certifikatet har upphört med hjälp av följande steg:  
1. Öppna MMC-snapin-modulen Certifikat, välj Datorkonto och gå till Certifikat (lokal dator)\Personligt\Certifikat.
2. Kontrollera om certifikatet för klientautentisering har upphört att gälla.

Om certifikatet för klientautentisering har upphört att gälla löser du problemet genom att utföra följande steg:

1. Kontrollera Azure File Sync version 4.0.1.0 eller senare är installerad.
2. Kör följande PowerShell-kommando på servern: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Sync misslyckades på grund av att autentiseringscertifikatet inte hittades.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c80228 |
| **HRESULT (decimal)** | -2134375896 |
| **Felsträng** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Reparation krävs** | Yes |

Det här felet beror på att det inte går att hitta certifikatet som används för autentisering.

Åtgärda problemet genom att följa dessa steg:

1. Kontrollera Azure File Sync version 4.0.1.0 eller senare är installerad.
2. Kör följande PowerShell-kommando på servern: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Sync misslyckades på grund av att autentiseringsidentiteten inte hittades.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c83079 |
| **HRESULT (decimal)** | -2134364039 |
| **Felsträng** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Reparation krävs** | Yes |

Felet beror på att borttagningen av serverslutpunkten misslyckades och att slutpunkten nu är i ett delvis borttaget tillstånd. Lös problemet genom att försöka ta bort serverslutpunkten.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Volymen där serverslutpunkten finns har ont om diskutrymme.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x8e5e0211 |
| **HRESULT (decimal)** | -1906441711 |
| **Felsträng** | JET_errLogDiskFull |
| **Reparation krävs** | Yes |

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8031a |
| **HRESULT (decimal)** | -2134375654 |
| **Felsträng** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Reparation krävs** | Yes |

Det här felet beror på att volymen är full. Felet uppstår vanligtvis när filer utanför serverslutpunkten använder utrymme på volymen. Frigör utrymme på volymen genom att lägga till ytterligare serverslutpunkter, flytta filer till en annan volym eller öka storleken på volymen som serverslutpunkten finns på.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Tjänsten är ännu inte redo att synkroniseras med den här serverslutpunkten.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8300f |
| **HRESULT (decimal)** | -2134364145 |
| **Felsträng** | ECS_E_REPLICA_NOT_READY |
| **Reparation krävs** | No |

Det här felet beror på att molnslutpunkten har skapats med innehåll som redan finns på Azure-filresursen. Azure File Sync måste genomsöka Azure-filresursen efter allt innehåll innan serverslutpunkten kan fortsätta med den inledande synkroniseringen.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synkroniseringen misslyckades på grund av problem med många enskilda filer.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8023b |
| **HRESULT (decimal)** | -2134375877 |
| **Felsträng** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Reparation krävs** | Yes |

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8021c |
| **HRESULT (decimal)** | -2134375908 |
| **Felsträng** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Reparation krävs** | Yes |

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c80253 |
| **HRESULT (decimal)** | -2134375853 |
| **Felsträng** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Reparation krävs** | Yes |

Synkroniseringssessioner misslyckas med något av dessa fel när det finns många filer som inte kan synkroniseras med fel per objekt. Utför stegen som beskrivs i Hur gör jag för att och se om det finns specifika filer eller mappar som inte [synkroniseras?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) för att lösa felen per objekt. Öppna ett support ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED för synkroniseringsfel.

> [!NOTE]
> Azure File Sync skapar en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser.

<a id="-2134376423"></a>**Sync misslyckades på grund av ett problem med sökvägen till serverslutpunkten.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c80019 |
| **HRESULT (decimal)** | -2134376423 |
| **Felsträng** | ECS_E_SYNC_INVALID_PATH |
| **Reparation krävs** | Yes |

Kontrollera att sökvägen finns, finns på en lokal NTFS-volym och inte är en referenspunkt eller befintlig serverslutpunkt.

<a id="-2134375817"></a>**Sync misslyckades eftersom filterdrivrutinens version inte är kompatibel med agentversionen**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80C80277 |
| **HRESULT (decimal)** | -2134375817 |
| **Felsträng** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Reparation krävs** | Yes |

Det här felet beror på att den version av filterdrivrutinen för molnnivåindelning (StorageSync.sys) som lästs in inte är kompatibel med Storage Sync Agent-tjänsten (FileSyncSvc). Om Azure File Sync-agenten har uppgraderats startar du om servern för att slutföra installationen. Om felet fortsätter att inträffa avinstallerar du agenten, startar om servern och installerar om Azure File Sync-agenten.

<a id="-2134376373"></a>**Tjänsten är inte tillgänglig för närvarande.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8004b |
| **HRESULT (decimal)** | -2134376373 |
| **Felsträng** | ECS_E_SERVICE_UNAVAILABLE |
| **Reparation krävs** | No |

Felet beror på att tjänsten Azure File Sync-tjänsten inte är tillgänglig. Det här felet löses automatiskt när Azure File Sync-tjänsten är tillgänglig igen.

<a id="-2146233088"></a>**Sync misslyckades på grund av ett undantag.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80131500 |
| **HRESULT (decimal)** | -2146233088 |
| **Felsträng** | COR_E_EXCEPTION |
| **Reparation krävs** | No |

Felet beror på att synkroniseringen misslyckades på grund av ett undantag. Om felet kvarstår i flera timmar skapar du en supportbegäran.

<a id="-2134364045"></a>**Sync misslyckades eftersom lagringskontot har misslyckats till en annan region.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c83073 |
| **HRESULT (decimal)** | -2134364045 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Reparation krävs** | Yes |

Felet beror på att lagringskontot har redundansväxlat till en annan region. Azure File Sync har inte stöd för redundansväxling av lagringskonton. Lagringskonton med Azure-filresurser som används som molnslutpunkter i Azure File Sync får inte redundansväxlas. Om du gör det slutar synkroniseringen att fungera och det kan leda till oväntade dataförluster för nyligen nivåbaserade filer. Lös problemet genom att flytta lagringskontot till den primära regionen.

<a id="-2134375922"></a>**Sync misslyckades på grund av ett tillfälligt problem med synkroniseringsdatabasen.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8020e |
| **HRESULT (decimal)** | -2134375922 |
| **Felsträng** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Reparation krävs** | No |

Det här felet uppstår på grund av ett internt problem med Sync-databasen. Det här felet löses automatiskt när Sync försöker igen. Om felet kvarstår under en längre tid skapar du en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2134364024"></a>**Sync misslyckades på grund av ändring Azure Active Directory klientorganisation**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c83088 |
| **HRESULT (decimal)** | -2134364024 | 
| **Felsträng** | ECS_E_INVALID_AAD_TENANT |
| **Reparation krävs** | Yes |

Kontrollera att du har den senaste Azure File Sync agenten. Från och med agent V10 Azure File Sync stöd för att flytta prenumerationen till en annan Azure Active Directory klientorganisation.
 
När du har den senaste agentversionen måste du ge Programmet Microsoft.StorageSync åtkomst till lagringskontot (se Kontrollera att Azure File Sync har åtkomst [till lagringskontot](#troubleshoot-rbac)).

<a id="-2134364010"></a>**Synkroniseringen misslyckades på grund av att brandväggen och det virtuella nätverket inte har konfigurerats**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c83096 |
| **HRESULT (decimal)** | -2134364010 | 
| **Felsträng** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Reparation krävs** | Yes |

Det här felet uppstår om inställningarna för brandväggen och det virtuella nätverket är aktiverade på lagringskontot och undantaget "Tillåt betrodda Microsoft-tjänster att komma åt det här lagringskontot" inte är markerat. Lös problemet genom att följa de steg som beskrivs i avsnittet [Konfigurera inställningar för brandvägg och virtuellt nätverk](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) i distributionsguiden.

<a id="-2147024891"></a>**Sync misslyckades eftersom behörigheterna i mappen System Volume Information är felaktiga.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80070005 |
| **HRESULT (decimal)** | -2147024891 |
| **Felsträng** | ERROR_ACCESS_DENIED |
| **Reparation krävs** | Yes |

Det här felet kan inträffa om kontot NT AUTHORITY\SYSTEM inte har behörighet till mappen System Volume Information på den volym där serverslutpunkten finns. Observera att om enskilda filer inte kan synkroniseras med ERROR_ACCESS_DENIED, utför du stegen som beskrivs i avsnittet [Felsökning per fil-/katalogsynkroniseringsfel.](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors)

Åtgärda problemet genom att följa dessa steg:

1. Ladda ned verktyget [Psexec](/sysinternals/downloads/psexec).
2. Kör följande kommando från en upphöjd kommandotolk för att starta en kommandotolk med hjälp av systemkontot: **PsExec.exe -i -s -d cmd** 
3. Kör den kommandotolk som körs under systemkontot kör du följande kommando för att bekräfta att kontot NT AUTHORITY\SYSTEM inte har åtkomst till mappen System Volume Information: **cacls "partitionsbokstav:\system volume information" /T /C**
4. Om kontot NT AUTHORITY\SYSTEM inte har åtkomst till mappen System Volume Information kör du följande kommando: **cacls  "partitionsbokstav:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Om steg 4 misslyckas med nekad åtkomst kör du följande kommando för att bli ägare till mappen System Volume Information och upprepar sedan steg 4: **takeown /A /R /F "partitionsbokstav:\System Volume Information"**

<a id="-2134375810"></a>**Sync misslyckades eftersom Azure-filresursen togs bort och återskapades.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8027e |
| **HRESULT (decimal)** | -2134375810 |
| **Felsträng** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Reparation krävs** | Yes |

Felet beror på att Azure File Sync inte har stöd för borttagning och återskapande av en Azure-filresurs i samma synkroniseringsgrupp. 

Lös problemet genom att ta bort och återskapa synkroniseringsgruppen med hjälp av följande steg:

1. Ta bort alla serverslutpunkter i synkroniseringsgruppen.
2. Ta bort molnslutpunkten. 
3. Ta bort synkroniseringsgruppen.
4. Om molnnivåindelad lagring har aktiverats på en serverslutpunkt tar du bort de överblivna nivåindelade filerna på servern genom att utföra stegen som beskrivs i avsnittet Nivåindelade filer är inte tillgängliga på servern när du har tagit bort [en serverslutpunkt.](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint)
5. Återskapa synkroniseringsgruppen.

<a id="-2145844941"></a>**Sync misslyckades eftersom HTTP-begäran omdirigerades**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80190133 |
| **HRESULT (decimal)** | -2145844941 |
| **Felsträng** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Reparation krävs** | Yes |

Felet uppstår eftersom Azure File Sync inte stöder HTTP-omdirigering (statuskod 3xx). Lös problemet genom att inaktivera HTTP-omdirigering på proxyservern eller nätverksenheten.

<a id="-2134364027"></a>**Det uppstod en timeout vid dataöverföring offline, men den pågår fortfarande.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c83085 |
| **HRESULT (decimal)** | -2134364027 |
| **Felsträng** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Reparation krävs** | No |

Felet uppstår när en datainmatningsåtgärd överskrider tidsgränsen. Det här felet kan ignoreras om synkroniseringen pågår (AppliedItemCount är större än 0). Se [Hur gör jag för att övervaka förloppet för en aktuell synkroniseringssession?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

<a id="-2134375814"></a>**Sync misslyckades eftersom det inte går att hitta sökvägen till serverslutpunkten på servern.**  

| Fel | Kod |
|-|-|
| **Hresult** | 0x80c8027a |
| **HRESULT (decimal)** | -2134375814 |
| **Felsträng** | ECS_E_SYNC_ROOT_DIRECTORY_NOT_FOUND |
| **Reparation krävs** | Yes |

Det här felet uppstår om katalogen som används som sökväg till serverslutpunkten har bytt namn eller tagits bort. Om katalogen har bytt namn byter du namn på katalogen tillbaka till det ursprungliga namnet och startar om tjänsten Storage Sync Agent (FileSyncSvc).

Om katalogen har tagits bort utför du följande steg för att ta bort den befintliga serverslutpunkten och skapa en ny serverslutpunkt med en ny sökväg:

1. Ta bort serverslutpunkten i synkroniseringsgruppen genom att följa stegen i Ta [bort en serverslutpunkt.](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint)
2. Skapa en ny serverslutpunkt i synkroniseringsgruppen genom att följa stegen i Lägg till [en serverslutpunkt.](./storage-sync-files-server-endpoint.md#add-a-server-endpoint)

### <a name="common-troubleshooting-steps"></a>Vanliga felsökningssteg
<a id="troubleshoot-storage-account"></a>**Kontrollera att lagringskontot finns.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Gå till synkroniseringsgruppen i tjänsten för synkronisering av lagring.
2. Välj molnslutpunkten i synkroniseringsgruppen.
3. Notera Namnet på Azure-filresursen i det öppna fönstret.
4. Välj det länkade lagringskontot. Om den här länken misslyckas har det refererade lagringskontot tagits bort.
    ![En skärmbild som visar informationsfönstret för molnslutpunkten med en länk till lagringskontot.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
# Variables for you to populate based on your configuration
$region = "<Az_Region>"
$resourceGroup = "<RG_Name>"
$syncService = "<storage-sync-service>"
$syncGroup = "<sync-group>"

# Log into the Azure account
Connect-AzAccount

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = [System.String[]]@()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the " + `
        " selected Azure Region or the region is mistyped.")
}

# Check to ensure resource group exists
$resourceGroups = [System.String[]]@()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    throw [System.Exception]::new("The provided resource group $resourceGroup does not exist.")
}

# Check to make sure the provided Storage Sync Service
# exists.
$syncServices = [System.String[]]@()

Get-AzStorageSyncService -ResourceGroupName $resourceGroup | ForEach-Object {
    $syncServices += $_.StorageSyncServiceName
}

if ($syncServices -notcontains $syncService) {
    throw [System.Exception]::new("The provided Storage Sync Service $syncService does not exist.")
}

# Check to make sure the provided Sync Group exists
$syncGroups = [System.String[]]@()

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $syncService | ForEach-Object {
    $syncGroups += $_.SyncGroupName
}

if ($syncGroups -notcontains $syncGroup) {
    throw [System.Exception]::new("The provided sync group $syncGroup does not exist.")
}

# Get reference to cloud endpoint
$cloudEndpoint = Get-AzStorageSyncCloudEndpoint `
    -ResourceGroupName $resourceGroup `
    -StorageSyncServiceName $syncService `
    -SyncGroupName $syncGroup

# Get reference to storage account
$storageAccount = Get-AzStorageAccount | Where-Object { 
    $_.Id -eq $cloudEndpoint.StorageAccountResourceId
}

if ($storageAccount -eq $null) {
    throw [System.Exception]::new("The storage account referenced in the cloud endpoint does not exist.")
}
```
---

<a id="troubleshoot-azure-file-share"></a>**Kontrollera att Azure-filresursen finns.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klicka **på** Översikt i den vänstra innehållsförteckningen för att återgå till huvudsidan för lagringskontot.
2. Välj **Filer** för att visa listan över filresurser.
3. Kontrollera att den filresurs som molnslutpunkten refererar till visas i listan över filresurser (du bör ha antecknat detta i steg 1 ovan).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $cloudEndpoint.AzureFileShareName -and
    $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    throw [System.Exception]::new("The Azure file share referenced by the cloud endpoint does not exist")
}
```
---

<a id="troubleshoot-rbac"></a>**Kontrollera Azure File Sync har åtkomst till lagringskontot.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klicka **på Åtkomstkontroll (IAM)** i den vänstra innehållsförteckningen.
1. Klicka på **fliken Rolltilldelningar** i listan över användare och program *(tjänstens huvudnamn)* som har åtkomst till ditt lagringskonto.
1. Kontrollera **att Microsoft.StorageSync** **eller Hybrid File Sync Service** (gammalt programnamn) visas i listan med rollen Läsare och **Dataåtkomst.** 

    ![En skärmbild av Tjänstens huvudnamn File Sync hybridtjänst på fliken åtkomstkontroll för lagringskontot](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Om **Microsoft.StorageSync** **eller Hybrid File Sync Service** inte visas i listan utför du följande steg:

    - Klicka på **Lägg till**.
    - I fältet **Roll** väljer du **Läsare och Dataåtkomst.**
    - I fältet **Välj** skriver du **Microsoft.StorageSync,** väljer rollen och klickar på **Spara.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell    
$role = Get-AzRoleAssignment -Scope $storageAccount.Id | Where-Object { $_.DisplayName -eq "Microsoft.StorageSync" }

if ($role -eq $null) {
    throw [System.Exception]::new("The storage account does not have the Azure File Sync " + `
                "service principal authorized to access the data within the " + ` 
                "referenced Azure file share.")
}
```
---

## <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Det finns två sökvägar för fel i molnnivåindelad lagring:

- Filer kan misslyckas med nivåindelning, vilket innebär Azure File Sync försöker att nivåindelad nivåindelad en fil Azure Files.
- Filer kan inte återkallas, vilket innebär att Azure File Sync-filsystemfiltret (StorageSync.sys) inte kan ladda ned data när en användare försöker komma åt en fil som har nivåindelats.

Det finns två huvudklasser med fel som kan inträffa via någon av felsökvägen:

- Molnlagringsfel
    - *Tillfälliga tillgänglighetsproblem för lagringstjänsten*. Mer information finns i [Serviceavtal (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Otillgänglig Azure-filresurs*. Det här felet inträffar vanligtvis när du tar bort Azure-filresursen när den fortfarande är en molnslutpunkt i en synkroniseringsgrupp.
    - *Otillgängligt lagringskonto*. Det här felet inträffar vanligtvis när du tar bort lagringskontot medan det fortfarande har en Azure-filresurs som är en molnslutpunkt i en synkroniseringsgrupp. 
- Serverfel 
  - *Azure File Sync filsystemfiltret (StorageSync.sys) läses inte in.* För att kunna svara på nivåindelnings-/återkallningsbegäranden måste Azure File Sync filsystemfiltret läsas in. Det finns flera orsaker till att filtret inte läses in, men den vanligaste orsaken är att en administratör tar bort det manuellt. Det Azure File Sync filsystemfiltret måste alltid läsas in för att Azure File Sync fungera korrekt.
  - *Saknad, skadad eller på annat sätt brutenparspunkt*. Enparspunkt är en särskild datastruktur på en fil som består av två delar:
    1. En reparse-tagg, som anger för operativsystemet att Azure File Sync-filsystemfiltret (StorageSync.sys) kan behöva göra vissa åtgärder på I/O i filen. 
    2. Parsa data, vilket indikerar att filsystemet filtrerar filens URI på den associerade molnslutpunkten (Azure-filresursen). 
        
       Det vanligaste sättet som enparspunkt kan skadas är om en administratör försöker ändra taggen eller dess data. 
  - *Problem med nätverksanslutning.* Servern måste ha internetanslutning för att kunna nivåindelad eller återkalla en fil.

Följande avsnitt beskriver hur du felsöker problem med molnnivåindelad lagring och avgör om ett problem är ett problem med molnlagring eller en server.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Övervaka nivåindelning på en server  
Om du vill övervaka nivåindelningsaktivitet på en server använder du händelse-ID 9003, 9016 och 9029 i händelseloggen telemetri (finns under Program och tjänster\Microsoft\FileSync\Agent i Loggboken).

- Händelse-ID 9003 tillhandahåller feldistribution för en serverslutpunkt. Till exempel Totalt antal fel, Felkod osv. Observera att en händelse loggas per felkod.
- Händelse-ID 9016 ger spökresultat för en volym. Till exempel är Ledigt utrymme i procent, Antal filer som har spökats i sessionen, Antal filer som inte har spöktitt osv.
- Händelse-ID 9029 innehåller information om spöksessioner för en serverslutpunkt. Till exempel Antal filer som försöks i sessionen, Antal filer som nivåindelats i sessionen, Antal filer som redan har nivåindelats osv.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Övervaka återkallning på en server
Om du vill övervaka återkallning på en server använder du händelse-ID 9005, 9006, 9009 och 9059 i händelseloggen telemetri (finns under Program och tjänster\Microsoft\FileSync\Agent i Loggboken).

- Händelse-ID 9005 ger återkallelsetillförlitlighet för en serverslutpunkt. Till exempel Totalt antal unika filer som nås, Totalt antal unika filer med misslyckad åtkomst osv.
- Händelse-ID 9006 ger distribution av återkallelsefel för en serverslutpunkt. Till exempel Totalt antal misslyckade begäranden, ErrorCode osv. Observera att en händelse loggas per felkod.
- Händelse-ID 9009 ger information om återkallning av session för en serverslutpunkt. Till exempel DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed osv.
- Händelse-ID 9059 ger distribution av program återkallade för en serverslutpunkt. Till exempel ShareId, Programnamn och TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Så här felsöker du filer som inte kan nivåindelad
Om filerna inte nivåindelats för Azure Files:

1. Granska Loggboken händelseloggar för telemetri, drift och diagnostik under Program och tjänster\Microsoft\FileSync\Agent. 
   1. Kontrollera att filerna finns i Azure-filresursen.

      > [!NOTE]
      > En fil måste synkroniseras till en Azure-filresurs innan den kan nivåindelad.

   2. Kontrollera att servern är ansluten till Internet. 
   3. Kontrollera Azure File Sync filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
       - Kör i en upphöjd `fltmc` kommandotolk. Kontrollera att StorageSync.sys och StorageSyncGuard.sys filterdrivrutiner för filsystemet visas.

> [!NOTE]
> Ett händelse-ID 9003 loggas en gång i timmen i telemetrihändelseloggen om en fil inte kan nivåindelad (en händelse loggas per felkod). Kontrollera avsnittet [Nivåindelad fel och reparation](#tiering-errors-and-remediation) för att se om reparationsstegen visas för felkoden.

### <a name="tiering-errors-and-remediation"></a>Nivåindelad fel och reparation

| Hresult | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | -2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Filen kunde inte nivåindelad eftersom den första uppladdningen pågår. | Ingen åtgärd krävs. Filen nivåindelad när den första uppladdningen är klar. |
| 0x80c86043 | -2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Filen kunde inte nivåindelad eftersom den används. | Ingen åtgärd krävs. Filen kommer att nivåindelas när den inte längre används. |
| 0x80c80241 | -2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Filen kunde inte nivåindelad eftersom den exkluderades av synkroniseringen. | Ingen åtgärd krävs. Filer i listan för synkroniseringsundantag kan inte nivåindelas. |
| 0x80c86042 | -2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Filen kunde inte nivåindelad eftersom den inte hittades på servern. | Ingen åtgärd krävs. Om problemet kvarstår bör du kontrollera om filen finns på servern. |
| 0x80c83053 | -2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Det gick inte att nivåindelade filen eftersom den togs bort i Azure-filresursen. | Ingen åtgärd krävs. Filen bör tas bort från servern när nästa session för nedladdningssynkronisering körs. |
| 0x80c8600e | -2134351858 | ECS_E_AZURE_SERVER_BUSY | Filen kunde inte nivåindelad på grund av ett nätverksproblem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80072ee7 | -2147012889 | WININET_E_NAME_NOT_RESOLVED | Filen kunde inte nivåindelad på grund av ett nätverksproblem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Filen kunde inte nivåindelad på grund av felet åtkomst nekad. Det här felet kan inträffa om filen finns i en skrivskyddad DFS-R-replikeringsmapp. | Azure Files Sync stöder inte serverslutpunkter i skrivskyddade DFS-R-replikeringsmappar. Mer information finns i [planeringsguiden](./storage-sync-files-planning.md#distributed-file-system-dfs). |
| 0x80072efe | -2147012866 | WININET_E_CONNECTION_ABORTED | Filen kunde inte nivåindelad på grund av ett nätverksproblem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80c80261 | -2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Filen kunde inte nivåindelad eftersom filstorleken är mindre än den storlek som stöds. | Om agentversionen är mindre än 9.0 är den minsta filstorlek som stöds 64 kB. Om agentversionen är 9.0 eller senare baseras den minsta filstorlek som stöds på filsystemets klusterstorlek (dubbla filsystemets klusterstorlek). Om filsystemets klusterstorlek till exempel är 4 kB är den minsta filstorleken 8 kB. |
| 0x80c83007 | -2134364153 | ECS_E_STORAGE_ERROR | Filen kunde inte nivåindelad på grund av ett Azure Storage-problem. | Öppna en supportbegäran om felet kvarstår. |
| 0x800703e3 | -2147023901 | ERROR_OPERATION_ABORTED | Filen kunde inte nivåindelade eftersom den återkallades samtidigt. | Ingen åtgärd krävs. Filen kommer att nivåindelas när den har återkallats och inte längre används. |
| 0x80c80264 | -2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Det gick inte att nivåindelade filen eftersom den inte har synkroniserats med Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att nivåindelas när den har synkroniserats med Azure-filresursen. |
| 0x80070001 | -2147942401 | ERROR_INVALID_FUNCTION | Filen kunde inte nivåindelad eftersom filterdrivrutinen för molnnivåindelning (storagesync.sys) inte körs. | Lös problemet genom att öppna en upphöjd kommandotolk och köra följande kommando: `fltmc load storagesync`<br>Om det inte går att läsa in filterdrivrutinen storagesync när du kör kommandot fltmc avinstallerar du Azure File Sync-agenten, startar om servern och installerar Azure File Sync agenten. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Filen kunde inte nivåindelad på grund av otillräckligt diskutrymme på volymen där serverslutpunkten finns. | Åtgärda problemet genom att frigöra minst 100 MB diskutrymme på den volym där serverslutpunkten finns. |
| 0x80070490 | -2147023728 | ERROR_NOT_FOUND | Filen kunde inte nivåindelad eftersom den inte har synkroniserats till Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att nivåindelas när den har synkroniserats med Azure-filresursen. |
| 0x80c80262 | -2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Filen kunde inte nivåindelad eftersom det är en reparspunkt som inte stöds. | Om filen är en referenspunkt för datadeduplicering följer du stegen i [planeringsguiden](./storage-sync-files-planning.md#data-deduplication) för att aktivera stöd för datadeduplicering. Filer med andra referenspunkter än datadeduplicering stöds inte och kommer inte att nivåindelas.  |
| 0x80c83052 | -2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Filen kunde inte nivåindelad eftersom den har ändrats. | Ingen åtgärd krävs. Filen kommer att nivåindelas när den ändrade filen har synkroniserats med Azure-filresursen. |
| 0x80c80269 | -2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Filen kunde inte nivåindelad eftersom den inte har synkroniserats till Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att nivåindelas när den har synkroniserats med Azure-filresursen. |
| 0x80072ee2 | -2147012894 | WININET_E_TIMEOUT | Filen kunde inte nivåindelad på grund av ett nätverksproblem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80c80017 | -2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Filen kunde inte nivåindelad eftersom den har ändrats. | Ingen åtgärd krävs. Filen kommer att nivåindelas när den ändrade filen har synkroniserats med Azure-filresursen. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Filen kunde inte nivåindelad på grund av otillräcklig systemresurser. | Om felet kvarstår bör du undersöka vilken drivrutin för program eller kernelläge som förbrukar systemresurser. |
| 0x8e5e03fe | -1906441218 | JET_errDiskIO | Filen kunde inte nivåindelad på grund av ett I/O-fel vid skrivning till databasen för molnnivåindelning. | Om felet kvarstår kör du chkdsk på volymen och kontrollerar lagringsmaskinvaran. |
| 0x8e5e0442 | -1906441150 | JET_errInstanceUnavailable | Filen kunde inte nivåindelad eftersom databasen för molnnivåindelning inte körs. | Lös problemet genom att starta om FileSyncSvc-tjänsten eller -servern. Om felet kvarstår kör du chkdsk på volymen och kontrollerar lagringsmaskinvaran. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Så här felsöker du filer som inte kan återkallas  
Om filer inte kan återkallas:
1. Granska Loggboken händelseloggar för telemetri, drift och diagnostik under Program och tjänster\Microsoft\FileSync\Agent.
    1. Kontrollera att filerna finns i Azure-filresursen.
    2. Kontrollera att servern är ansluten till Internet. 
    3. Öppna MMC-snapin-modulen Tjänster och kontrollera att tjänsten Storage Sync Agent (FileSyncSvc) körs.
    4. Kontrollera Azure File Sync filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
        - Kör i en upphöjd `fltmc` kommandotolk. Kontrollera att StorageSync.sys och StorageSyncGuard.sys filterdrivrutiner för filsystemet visas.

> [!NOTE]
> Ett händelse-ID 9006 loggas en gång i timmen i telemetrihändelseloggen om en fil inte kan återkallas (en händelse loggas per felkod). Kontrollera [avsnittet Återkalla fel och reparation för att](#recall-errors-and-remediation) se om reparationsstegen visas för felkoden.

### <a name="recall-errors-and-remediation"></a>Återkalla fel och åtgärda

| Hresult | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | -2147942521 | ERROR_SEM_TIMEOUT | Det gick inte att återkalla filen på grund av en I/O-timeout. Det här problemet kan inträffa av flera orsaker: begränsningar i serverresurser, dålig nätverksanslutning eller ett Azure Storage-problem (till exempel begränsning). | Ingen åtgärd krävs. Om felet kvarstår i flera timmar öppnar du ett supportärende. |
| 0x80070036 | -2147024842 | ERROR_NETWORK_BUSY | Filen kunde inte återkallas på grund av ett nätverksproblem.  | Om felet kvarstår kontrollerar du nätverksanslutningen till Azure-filresursen. |
| 0x80c80037 | -2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Det gick inte att återkalla filen eftersom serverslutpunkten togs bort. | Information om hur du löser problemet finns [i Nivåindelade filer är inte tillgängliga på servern när du har tagit bort en serverslutpunkt.](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) |
| 0x80070005 | -2147024891 | ERROR_ACCESS_DENIED | Det gick inte att återkalla filen på grund av ett fel om nekad åtkomst. Det här problemet kan uppstå om inställningarna för brandväggen och det virtuella nätverket på lagringskontot är aktiverade och servern inte har åtkomst till lagringskontot. | Lös problemet genom att lägga till serverns IP-adress eller [](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) virtuella nätverk genom att följa stegen i avsnittet Konfigurera inställningar för brandvägg och virtuellt nätverk i distributionsguiden. |
| 0x80c86002 | -2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Filen kunde inte återkallas eftersom den inte är tillgänglig i Azure-filresursen. | Lös problemet genom att kontrollera att filen finns i Azure-filresursen. Om filen finns i Azure-filresursen uppgraderar du till den senaste Azure File Sync [agentversionen](./storage-files-release-notes.md#supported-versions). |
| 0x80c8305f | -2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Filen kunde inte återkallas på grund av ett auktoriseringsfel på lagringskontot. | Lös problemet genom att kontrollera [Azure File Sync har åtkomst till lagringskontot](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | -2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Det gick inte att återkalla filen eftersom Azure-filresursen inte är tillgänglig. | Kontrollera att filresursen finns och är tillgänglig. Om filresursen har tagits bort och återskapats utför du stegen som beskrivs i avsnittet [Synkroniseringen](?tabs=portal1%252cazure-portal#-2134375810) misslyckades eftersom Azure-filresursen togs bort och återskapades för att ta bort och återskapa synkroniseringsgruppen. |
| 0x800705aa | -2147023446 | ERROR_NO_SYSTEM_RESOURCES | Det gick inte att återkalla filen på grund av otillräcklig systemresurser. | Om felet kvarstår bör du undersöka vilken drivrutin för program eller kernelläge som förbrukar systemresurser. |
| 0x8007000e | -2147024882 | ERROR_OUTOFMEMORY | Det gick inte att återkalla filen på grund av otillräckligt minne. | Om felet kvarstår bör du undersöka vilken drivrutin för program eller kernelläge som orsakar tillståndet med lågt minne. |
| 0x80070070 | -2147024784 | ERROR_DISK_FULL | Filen kunde inte återkallas på grund av otillräckligt diskutrymme. | Lös problemet genom att frigöra utrymme på volymen. Det gör du genom att flytta filer till en annan volym, öka storleken på volymen eller tvinga filer att nivåindelas med hjälp av cmdleten Invoke-StorageSyncCloudTiering. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Nivåindelade filer är inte tillgängliga på servern efter att en serverslutpunkt har tagits bort
Nivåindelade filer på en server blir otillgängliga om filerna inte återkallas innan du tar bort en serverslutpunkt.

Fel loggas om nivåindelade filer inte är tillgängliga
- När du synkroniserar en fil loggas felkoden -2147942467 (0x80070043 - ERROR_BAD_NET_NAME) i händelseloggen ItemResults
- När du återkallar en fil loggas felkoden -2134376393 (0x80c80037 - ECS_E_SYNC_SHARE_NOT_FOUND) i händelseloggen RecallResults

Du kan återställa åtkomsten till dina nivåindelade filer om följande villkor uppfylls:
- Serverslutpunkten har tagits bort inom de senaste 30 dagarna
- Molnslutpunkten har inte tagits bort 
- Filresursen har inte tagits bort
- Sync-gruppen har inte tagits bort

Om ovanstående villkor är uppfyllda kan du återställa åtkomsten till filerna på servern genom att återskapa serverslutpunkten på samma sökväg på servern inom samma Sync-grupp inom 30 dagar. 

Om ovanstående villkor inte uppfylls, kan du inte återställa åtkomsten, eftersom dessa nivåindelade filer nu är överblivna på servern. Följ anvisningarna nedan för att ta bort de överblivna nivåindelade filerna.

**Kommentarer**
- När nivåindelade filer inte är tillgängliga på servern bör den fullständiga filen fortfarande vara tillgänglig om du kommer åt Azure-filresursen direkt.
- Om du vill förhindra överblivna nivåindelade filer i framtiden följer du stegen i Ta bort [en serverslutpunkt när](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) du tar bort en serverslutpunkt.

<a id="get-orphaned"></a>**Hämta listan över överblivna nivåindelade filer** 

1. Kontrollera Azure File Sync version v5.1 eller senare är installerad.
2. Kör följande PowerShell-kommandon för att visa överblivna nivåindelade filer:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Spara den OrphanTieredFiles.txt utdatafilen om filerna behöver återställas från en säkerhetskopia när de har tagits bort.

<a id="remove-orphaned"></a>**Ta bort överblivna nivåindelade filer** 

*Alternativ 1: Ta bort de överblivna nivåindelade filerna*

Det här alternativet tar bort de överblivna nivåindelade filerna på Windows Server, men kräver att serverslutpunkten tas bort om den finns på grund av problem efter 30 dagar eller om den är ansluten till en annan synkroniseringsgrupp. Filkonflikter uppstår om filer uppdateras på Windows Server- eller Azure-filresursen innan serverslutpunkten återskapas.

1. Kontrollera Azure File Sync version v5.1 eller senare är installerad.
2. Säkerhetskopiera Platsen för Azure-filresursen och serverslutpunkten.
3. Ta bort serverslutpunkten i synkroniseringsgruppen (om det finns) genom att följa stegen som beskrivs i [Ta bort en serverslutpunkt](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Om serverslutpunkten inte tas bort innan du använder Remove-StorageSyncOrphanedTieredFiles-cmdleten tas den fullständiga filen i Azure-filresursen bort om du tar bort den överblivna nivåindelade filen på servern. 

4. Kör följande PowerShell-kommandon för att visa överblivna nivåindelade filer:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Spara OrphanTieredFiles.txt utdatafil om filerna behöver återställas från en säkerhetskopia när de har tagits bort.
6. Kör följande PowerShell-kommandon för att ta bort överblivna nivåindelade filer:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Kommentarer** 
- Nivåindelade filer som ändrats på servern som inte är synkroniserade med Azure-filresursen tas bort.
- Nivåindelade filer som är tillgängliga (inte överblivna) tas inte bort.
- Filer som inte är nivåindelade finns kvar på servern.

7. Valfritt: Återskapa serverslutpunkten om den tas bort i steg 3.

*Alternativ 2: Montera Azure-filresursen och kopiera filerna lokalt som är överblivna på servern*

Det här alternativet kräver inte att du tar bort serverslutpunkten, men det krävs tillräckligt med diskutrymme för att kopiera de fullständiga filerna lokalt.

1. [Montera](./storage-how-to-use-files-windows.md) Azure-filresursen på den Windows Server som har överblivna nivåindelade filer.
2. Kör följande PowerShell-kommandon för att visa överblivna nivåindelade filer:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Använd utdatafilen OrphanTieredFiles.txt att identifiera överblivna nivåindelade filer på servern.
4. Skriva över de överblivna nivåindelade filerna genom att kopiera den fullständiga filen från Azure-filresursen till Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Så här felsöker du filer som oväntat återkallas på en server  
Antivirus, säkerhetskopiering och andra program som läser ett stort antal filer orsakar oönskad återkallning om de inte respekterar attributet hoppa över offline och hoppar över läsningen av innehållet i dessa filer. Genom att hoppa över filer för produkter som stöder det här alternativet kan oönskad återkallning undvikas i samband med aktiviteter som virusgenomsökningar eller säkerhetskopieringsjobb.

Kontakta programleverantören för information om hur du konfigurerar deras lösning så att offlinefiler ignoreras.

Oönskad återkallning kan också inträffa i andra scenarier, till exempel när du bläddrar bland filer i Utforskaren. Om en mapp med filer på molnnivå öppnas i Utforskaren på servern kan det leda till oväntad återkallning. Det är ännu mer troligt om en antiviruslösning är aktiverad på servern.

> [!NOTE]
>Använd händelse-ID 9059 i telemetrihändelseloggen för att avgöra vilka program som orsakar återkallanden. Den här händelsen tillhandahåller distribution av program återkallade för en serverslutpunkt och loggas en gång i timmen.

### <a name="tls-12-required-for-azure-file-sync"></a>TLS 1.2 krävs för Azure File Sync

Du kan visa TLS-inställningarna på servern genom att titta på [registerinställningarna](/windows-server/security/tls/tls-registry-settings). 

Om du använder en proxyserver kan du läsa proxyns dokumentation och kontrollera att den är konfigurerad att använda TLS1.2.

## <a name="general-troubleshooting"></a>Allmän felsökning
Om du får problem med Azure File Sync på en server börjar du med att utföra följande steg:
1. Granska Loggboken händelseloggar för telemetri, drift och diagnostik.
    - Problem med synkronisering, nivåindelning och återkallande loggas i telemetri-, diagnostik- och drifthändelseloggarna under Program och tjänster\Microsoft\FileSync\Agent.
    - Problem som rör hantering av en server (till exempel konfigurationsinställningar) loggas i händelseloggarna för drift och diagnostik under Program och tjänster\Microsoft\FileSync\Management.
2. Kontrollera Azure File Sync-tjänsten körs på servern:
    - Öppna snapin-modulen Services MMC och kontrollera att tjänsten Storage Sync Agent (FileSyncSvc) körs.
3. Kontrollera Azure File Sync filterdrivrutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
    - Kör i en upphöjd `fltmc` kommandotolk. Kontrollera att StorageSync.sys och StorageSyncGuard.sys filterdrivrutiner för filsystemet visas.

Om problemet inte är löst kör du VERKTYGET AFSDiag och skickar dess ZIP-filutdata till supportteknikern som har tilldelats ditt ärende för ytterligare diagnos.

Utför stegen nedan för att köra AFSDiag.

För agentversion v11 och senare:
1. Öppna ett upphöjt PowerShell-fönster och kör sedan följande kommandon (tryck på Retur efter varje kommando):

    > [!NOTE]
    >AFSDiag skapar utdatakatalogen och en temp-mapp i den innan loggar samlas in och tar bort temp-mappen efter körningen. Ange en utdataplats som inte innehåller data.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Återskapa problemet. När du är klar anger du **D**.
3. En .zip-fil som innehåller loggar och spårningsfiler sparas i den utdatakatalog som du har angett. 

För agentversion v10 och tidigare:
1. Skapa en katalog där AFSDiag-utdata ska sparas (till exempel C:\Output).
    > [!NOTE]
    >AFSDiag tar bort allt innehåll i utdatakatalogen innan loggar samlas in. Ange en utdataplats som inte innehåller data.
2. Öppna ett upphöjt PowerShell-fönster och kör sedan följande kommandon (tryck på Retur efter varje kommando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. För Azure File Sync för kernelläget anger du **1** (om inget annat anges för att skapa fler utförliga spårningar) och trycker sedan på Retur.
4. För spårningsnivån Azure File Sync användarläge anger du **1** (om inget annat anges för att skapa fler utförliga spårningar) och trycker sedan på Retur.
5. Återskapa problemet. När du är klar anger du **D**.
6. En .zip-fil som innehåller loggar och spårningsfiler sparas i den utdatakatalog som du har angett.


## <a name="see-also"></a>Se även
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
- [Azure Files vanliga frågor och svar](storage-files-faq.md)
- [Felsöka Azure Files-problem i Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsöka Azure Files-problem i Linux](storage-troubleshoot-linux-file-connection-problems.md)
