---
title: Felsöka Azure File Sync | Microsoft Docs
description: Felsök vanliga problem i en distribution på Azure File Sync, som du kan använda för att transformera Windows Server till ett snabbt cacheminne för Azure-filresursen.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 1/13/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: b84256188cf5df3ddf389f763e669a2b2ca00852
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183344"
---
# <a name="troubleshoot-azure-file-sync"></a>Felsök Azure File Sync
Använd Azure File Sync för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Den här artikeln är utformad för att hjälpa dig att felsöka och lösa problem som kan uppstå med din Azure File Sync-distribution. Vi beskriver också hur du samlar in viktiga loggar från systemet om det krävs en djupare undersökning av problemet. Om du inte ser svaret på din fråga kan du kontakta oss genom följande kanaler (i eskalerad ordning):

1. [Sidan Microsoft Q&en fråga för Azure Storage](/answers/products/azure?product=storage).
2. [Azure Files UserVoice](https://feedback.azure.com/forums/217298-storage/category/180670-files).
3. Microsoft Support. Om du vill skapa en ny supportbegäran går du till fliken **Hjälp** i Azure Portal, väljer **Hjälp + Support** -knappen och väljer sedan **ny supportbegäran**.

## <a name="im-having-an-issue-with-azure-file-sync-on-my-server-sync-cloud-tiering-etc-should-i-remove-and-recreate-my-server-endpoint"></a>Jag har problem med Azure File Sync på min server (synkronisering, moln nivå osv.). Bör jag ta bort och återskapa min server slut punkt?
[!INCLUDE [storage-sync-files-remove-server-endpoint](../../../includes/storage-sync-files-remove-server-endpoint.md)]

## <a name="agent-installation-and-server-registration"></a>Agent installation och Server registrering
<a id="agent-installation-failures"></a>**Felsöka agent installations fel**  
Om installationen av Azure File Sync Agent Miss lyckas, kör du följande kommando i en upphöjd kommando tolk för att aktivera loggning under Agent installationen:

```
StorageSyncAgent.msi /l*v AFSInstaller.log
```

Ta reda på orsaken till installations felet genom att granska Installer. log.

<a id="agent-installation-on-DC"></a>**Det går inte att installera agenten på Active Directory-domän styrenhet**  
Om du försöker installera Sync-agenten på en Active Directory domänkontrollant där PDC-Rollens ägare finns på en Windows Server 2008 R2 eller lägre OS-version kan du trycka på problemet där Sync-agenten inte kan installeras.

Du löser problemet genom att överföra PDC-rollen till en annan domänkontrollant som kör Windows Server 2012 R2 eller senare och sedan installera Sync.

<a id="parameter-is-incorrect"></a>**Det går inte att komma åt en volym på Windows Server 2012 R2 med felet: parametern är felaktig**  
När du har skapat en server slut punkt på Windows Server 2012 R2 uppstår följande fel vid åtkomst till volymen:

DriveLetter: \ är inte tillgänglig.  
Parametern är felaktig.

Lös problemet genom att installera [KB2919355](https://support.microsoft.com/help/2919355/windows-rt-8-1-windows-8-1-windows-server-2012-r2-update-april-2014) och starta om servern. Om den här uppdateringen inte installeras eftersom en senare uppdatering redan har installerats går du till Windows Update, installerar de senaste uppdateringarna för Windows Server 2012 R2 och startar om servern.

<a id="server-registration-missing-subscriptions"></a>**Server registreringen visar inte alla Azure-prenumerationer**  
När du registrerar en server med hjälp av ServerRegistration.exe, saknas prenumerationer när du klickar på list rutan för Azure-prenumeration.

Det här problemet beror på att ServerRegistration.exe för närvarande inte stöder miljöer med flera klienter. Det här problemet åtgärdas i en framtida Azure File Sync agent uppdatering.

Du kan lösa problemet genom att använda följande PowerShell-kommandon för att registrera servern:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzureRmStorageSync -SubscriptionID "<guid>" -TenantID "<guid>"
Register-AzureRmStorageSyncServer -SubscriptionId "<guid>" -ResourceGroupName "<string>" -StorageSyncServiceName "<string>"
```

<a id="server-registration-prerequisites"></a>**Följande meddelande visas i Server registreringen: "krav saknas"**  
Det här meddelandet visas om AZ eller AzureRM PowerShell-modulen inte är installerad på PowerShell 5,1. 

> [!Note]  
> ServerRegistration.exe stöder inte PowerShell 6. x. Du kan registrera servern med hjälp av Register-AzStorageSyncServer-cmdleten på PowerShell 6. x.

Utför följande steg för att installera AZ-eller AzureRM-modulen på PowerShell 5,1:

1. Skriv **PowerShell** från en upphöjd kommando tolk och tryck på RETUR.
2. Installera den senaste AZ-eller AzureRM-modulen genom att följa dokumentationen:
    - [AZ-modul (kräver .NET-4.7.2)](/powershell/azure/install-az-ps?viewFallbackFrom=azps-1.1.0)
    - [AzureRM-modul]( https://go.microsoft.com/fwlink/?linkid=856959)
3. Kör ServerRegistration.exe och Slutför guiden för att registrera servern med en tjänst för synkronisering av lagring.

<a id="server-already-registered"></a>**Server registreringen visar följande meddelande: "den här servern har redan registrerats"** 

![En skärm bild av dialog rutan för Server registrering med fel meddelandet "servern har redan registrerats"](media/storage-sync-files-troubleshoot/server-registration-1.png)

Det här meddelandet visas om servern tidigare registrerades med en tjänst för synkronisering av lagring. Om du vill avregistrera servern från den aktuella tjänsten för synkronisering av lagring och registrera dig med en ny tjänst för synkronisering av lagring, slutför du stegen som beskrivs i [avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

Om servern inte visas under **registrerade servrar** i tjänsten för synkronisering av lagring, kör du följande PowerShell-kommandon på den server som du vill avregistrera:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Reset-StorageSyncServer
```

> [!Note]  
> Om servern är en del av ett kluster kan du använda den valfria parametern *Reset-StorageSyncServer-CleanClusterRegistration* för att också ta bort kluster registreringen.

<a id="web-site-not-trusted"></a>**När jag registrerar en server ser jag flera svar på en webbplats som inte är betrodd. Varför?**  
Det här problemet uppstår när den **förbättrade säkerhets principen i Internet Explorer** aktive ras under Server registrering. Mer information om hur du inaktiverar den **förbättrade säkerhets principen i Internet Explorer** finns i [förbereda Windows Server för användning med Azure File Sync](storage-sync-files-deployment-guide.md#prepare-windows-server-to-use-with-azure-file-sync) och [hur du distribuerar Azure File Sync](storage-sync-files-deployment-guide.md).

<a id="server-registration-missing"></a>**Servern visas inte under registrerade servrar i Azure Portal**  
Om en server inte visas under **registrerade servrar** för en tjänst för synkronisering av lagring:
1. Logga in på den server som du vill registrera.
2. Öppna Utforskaren och gå sedan till installations katalogen för Storage Sync-agenten (standard platsen är C:\Program Files\Azure\StorageSyncAgent). 
3. Kör ServerRegistration.exe och Slutför guiden för att registrera servern med en tjänst för synkronisering av lagring.

## <a name="sync-group-management"></a>Synkronisera grupp hantering

### <a name="cloud-endpoint-creation-errors"></a>Fel vid skapande av moln slut punkt

<a id="cloud-endpoint-using-share"></a>**Det gick inte att skapa moln slut punkten, med det här felet: "den angivna Azure-FileShare används redan av en annan CloudEndpoint"**  
Det här felet uppstår om Azure-filresursen redan används av en annan molnslutpunkt. 

Om du ser det här meddelandet och Azure-filresursen för närvarande inte används av en moln slut punkt, slutför du följande steg för att rensa Azure File Sync metadata på Azure-filresursen:

> [!Warning]  
> Om du tar bort metadata på en Azure-filresurs som för närvarande används av en moln slut punkt uppstår Azure File Sync åtgärder för att Miss kunna. 

1. Gå till Azure-filresursen i Azure Portal.  
2. Högerklicka på Azure-filresursen och välj sedan **Redigera metadata**.
3. Högerklicka på **SyncService** och välj sedan **ta bort**.

<a id="cloud-endpoint-authfailed"></a>**Det gick inte att skapa moln slut punkten, med det här felet: "AuthorizationFailed"**  
Det här felet uppstår om ditt användar konto inte har behörighet att skapa en moln slut punkt. 

Ditt användar konto måste ha följande Microsoft-behörighet för att skapa en moln slut punkt:  
* Läsa: Hämta roll definition
* Skriva: skapa eller uppdatera anpassad roll definition
* Läsa: Hämta roll tilldelning
* Skriva: skapa roll tilldelning

Följande inbyggda roller har de nödvändiga Microsoft Authorization-behörigheterna:  
* Ägare
* Administratör för användaråtkomst

För att avgöra om användar konto rollen har de behörigheter som krävs:  
1. I Azure Portal väljer du **resurs grupper**.
2. Välj den resurs grupp där lagrings kontot finns och välj sedan **åtkomst kontroll (IAM)**.
3. Välj fliken **roll tilldelningar** .
4. Välj **rollen** (till exempel ägare eller deltagare) för ditt användar konto.
5. Välj **Microsoft Authorization** i listan **resurs leverantör** . 
    * **Roll tilldelningen** ska ha **Läs** -och **Skriv** behörighet.
    * **Roll definitionen** måste ha **Läs** -och **Skriv** behörighet.

### <a name="server-endpoint-creation-and-deletion-errors"></a>Fel vid skapande och borttagning av Server slut punkt

<a id="-2134375898"></a>**Det gick inte att skapa server slut punkten, med följande fel: "MgmtServerJobFailed" (felkod:-2134375898 eller 0x80c80226)**  
Felet uppstår om sökvägen till serverslutpunkten finns på systemvolymen och molnnivåindelning är aktiverat. Molnnivåindelning stöds inte på systemvolymen. Om du vill skapa en serverslutpunkt på systemvolymen inaktiverar du molnnivåindelning när du skapar serverslutpunkten.

<a id="-2147024894"></a>**Det gick inte att skapa server slut punkten, med följande fel: "MgmtServerJobFailed" (felkod:-2147024894 eller 0x80070002)**  
Det här felet uppstår om den angivna sökvägen till serverslutpunkten är ogiltig. Kontrollera att sökvägen till serverslutpunkten är en lokalt ansluten NTFS-volym. Obs! Azure File Sync stöder inte mappade enheter som en sökväg till en server slut punkt.

<a id="-2134375640"></a>**Det gick inte att skapa server slut punkten, med följande fel: "MgmtServerJobFailed" (felkod:-2134375640 eller 0x80c80328)**  
Det här felet uppstår om den angivna Server slut punkten inte är en NTFS-volym. Kontrollera att sökvägen till serverslutpunkten är en lokalt ansluten NTFS-volym. Obs! Azure File Sync stöder inte mappade enheter som en sökväg till en server slut punkt.

<a id="-2134347507"></a>**Det gick inte att skapa server slut punkten, med följande fel: "MgmtServerJobFailed" (felkod:-2134347507 eller 0x80c8710d)**  
Det här felet beror på att Azure File Sync inte stöder serverslutpunkter på volymer som har en komprimerad System Volume Information-mapp. Lös problemet genom att expandera mappen System Volume Information. Om mappen System Volume information är den enda mappen som komprimeras på volymen utför du följande steg:

1. Ladda ned [PsExec](/sysinternals/downloads/psexec) -verktyget.
2. Kör följande kommando från en upphöjd kommando tolk för att starta en kommando tolk som körs under system-kontot: **PsExec.exe-i-s-d cmd**
3. Från kommandotolken som körs under systemkontot skriver du följande kommandon och trycker på RETUR:   
    **CD/d "enhets beteckning: \ System Volume Information"**  
    **Compact/u/s**

<a id="-2134376345"></a>**Det gick inte att skapa server slut punkten, med följande fel: "MgmtServerJobFailed" (felkod:-2134376345 eller 0x80C80067)**  
Det här felet uppstår om gränsen för serverslutpunkter per server uppnås. Azure File Sync stöder för närvarande upp till 30 serverslutpunkter per server. Mer information finns i [Azure File Sync skala mål](./storage-files-scale-targets.md#azure-file-sync-scale-targets).

<a id="-2134376427"></a>**Det gick inte att skapa server slut punkten, med följande fel: "MgmtServerJobFailed" (felkod:-2134376427 eller 0x80c80015)**  
Det här felet uppstår om en annan serverslutpunkt redan synkroniserar den angivna serversökvägen. Azure File Sync har inte stöd för att flera serverslutpunkter synkroniserar samma katalog eller volym.

<a id="-2160590967"></a>**Det gick inte att skapa server slut punkten, med följande fel: "MgmtServerJobFailed" (felkod:-2160590967 eller 0x80c80077)**  
Det här felet uppstår om sökvägen till Server slut punkten innehåller överblivna filer på nivån. Om en server slut punkt nyligen har tagits bort väntar du tills rensningen av filer på den överblivna nivån har slutförts. Händelse-ID 6662 loggas i händelse loggen för telemetri när den överblivna nivån filer rensa har startat. Händelse-ID 6661 loggas när rensningen av filer på den överblivna nivån har slutförts och en server slut punkt kan återskapas med hjälp av sökvägen. Om det inte går att skapa en server slut punkt efter att nivån rensas har slutförts, eller om händelse-ID 6661 inte kan hittas i händelse loggen för telemetri på grund av händelse logg förnyelse, tar du bort de överblivna filerna genom att följa stegen som dokumenteras i de [skiktade filerna är inte tillgängliga på servern efter att du har tagit bort ett Server slut punkt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) avsnitt.

<a id="-2134347757"></a>**Det gick inte att ta bort Server slut punkten, med det här felet: "MgmtServerJobExpired" (felkod:-2134347757 eller 0x80c87013)**  
Det här felet uppstår om servern är offline eller inte har någon nätverksanslutning. Om servern inte längre är tillgänglig avregistrerar du servern i portalen, vilket tar bort serverslutpunkterna. Om du vill ta bort Server slut punkterna följer du stegen som beskrivs i [avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md#unregister-the-server-with-storage-sync-service).

### <a name="server-endpoint-health"></a>Server slut punkts hälsa

<a id="server-endpoint-provisioningfailed"></a>**Det gick inte att öppna sidan Egenskaper för Server slut punkt eller uppdatera princip för moln skikt**  
Det här problemet kan inträffa om en hanterings åtgärd på Server slut punkten Miss lyckas. Om sidan Egenskaper för Server slut punkt inte öppnas i Azure Portal kan det lösa problemet genom att uppdatera Server slut punkten med PowerShell-kommandon från servern. 

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
<a id="server-endpoint-noactivity"></a>**Server slut punkten har hälso status "ingen aktivitet" eller "väntar" och Server tillståndet på det registrerade Server bladet är "visas offline"**  

Det här problemet kan uppstå om övervaknings processen för synkroniseringstjänsten (AzureStorageSyncMonitor.exe) inte körs eller om servern inte har åtkomst till Azure File Syncs tjänsten.

På den server som visas som "visas offline" i portalen tittar du på händelse-ID 9301 i händelse loggen för telemetri (finns under program och Services\Microsoft\FileSync\Agent i Loggboken) för att avgöra varför servern inte kan komma åt tjänsten Azure File Sync. 

- Om **GetNextJob slutfördes med status: 0** loggas kan servern kommunicera med Azure File Sync-tjänsten. 
    - Öppna Aktivitetshanteraren på servern och kontrollera att Storage Sync Monitor-processen (AzureStorageSyncMonitor.exe) körs. Om processen inte körs provar du först att starta om servern. Om det inte går att lösa problemet genom att starta om servern uppgraderar du till den senaste [agentversionen](./storage-files-release-notes.md) för Azure File Sync. 

- Om **GetNextJob slutfördes med status:-2134347756** har loggats kan servern inte kommunicera med Azure File Sync tjänsten på grund av en brand vägg, proxy eller TLS-chiffrering. 
    - Om servern finns bakom en brandvägg kontrollerar du att port 443 för utgående trafik tillåts. Om brand väggen begränsar trafik till vissa domäner kontrollerar du att de domäner som anges i brand Väggs [dokumentationen](./storage-sync-files-firewall-and-proxy.md#firewall) är tillgängliga.
    - Om servern finns bakom en proxyserver konfigurerar du de datorövergripande proxyinställningarna genom att följa anvisningarna i proxy- [dokumentationen](./storage-sync-files-firewall-and-proxy.md#proxy).
    - Använd Test-StorageSyncNetworkConnectivity-cmdlet för att kontrol lera nätverks anslutningen till tjänstens slut punkter. Läs mer i [testa nätverks anslutningen till tjänstens slut punkter](./storage-sync-files-firewall-and-proxy.md#test-network-connectivity-to-service-endpoints).
    - Om du vill lägga till chiffersviter på servern använder du grup princip eller TLS-cmdlet: ar:
        - Information om hur du använder en grup princip finns i [Konfigurera TLS-chiffrering av TLS-paket med hjälp av Grupprincip](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-group-policy).
        - Information om hur du använder TLS-cmdlets finns i [Konfigurera TLS-chiffrering av TLS-paket med hjälp av TLS PowerShell-cmdletar](https://docs.microsoft.com/windows-server/security/tls/manage-tls#configuring-tls-cipher-suite-order-by-using-tls-powershell-cmdlets).
    
        Azure File Sync stöder för närvarande följande chiffersviter för TLS 1,2-protokollet:  
        - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384_P384  
        - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256_P256  
        - TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384_P384  
        - TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256_P256  
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P256  
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256  
        - TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P256  
        - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256  
        - TLS_RSA_WITH_AES_256_GCM_SHA384  
        - TLS_RSA_WITH_AES_128_GCM_SHA256  
        - TLS_RSA_WITH_AES_256_CBC_SHA256  
        - TLS_RSA_WITH_AES_128_CBC_SHA256  

- Om **GetNextJob slutfördes med status:-2134347764** är inloggad kan servern inte kommunicera med Azure File Sync tjänsten på grund av ett utgånget eller Borttaget certifikat.  
    - Kör följande PowerShell-kommando på servern för att återställa certifikatet som används för autentisering:
    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```
<a id="endpoint-noactivity-sync"></a>**Server slut punkten har hälso statusen "ingen aktivitet" och Server tillståndet på det registrerade Server bladet är "online"**  

Hälsostatusen ”Ingen aktivitet” för serverslutpunkten betyder att serverslutpunkten inte har loggat synkroniseringsaktivitet under de senaste två timmarna.

Om du vill kontrol lera den aktuella synkroniseringen på en server, se [Hur gör jag för att övervaka förloppet för en pågående synkronisering?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

En server slut punkt kan inte logga synkroniseringsåtgärden under flera timmar på grund av ett fel eller otillräckliga system resurser. Kontrol lera att den senaste versionen av Azure File Sync [agent](./storage-files-release-notes.md) är installerad. Öppna en support förfrågan om problemet kvarstår.

> [!Note]  
> Om Server tillståndet på bladet registrerade servrar är "verkar offline", så har de steg som dokumenterats i [Server slut punkten statusen "ingen aktivitet" eller "väntande" och Server tillståndet på bladet registrerade servrar "visas offline"](#server-endpoint-noactivity) .

## <a name="sync"></a>Synkronisera
<a id="afs-change-detection"></a>**Om jag har skapat en fil direkt i min Azure-filresurs via SMB eller via portalen, hur lång tid tar det för filen att synkroniseras med servrar i den synkroniserade gruppen?**  
[!INCLUDE [storage-sync-files-change-detection](../../../includes/storage-sync-files-change-detection.md)]

<a id="serverendpoint-pending"></a>**Server slut punktens hälso tillstånd är i ett väntande tillstånd i flera timmar**  
Det här problemet förväntas om du skapar en moln slut punkt och använder en Azure-filresurs som innehåller data. Ändrings uppräknings jobbet som söker efter ändringar i Azure-filresursen måste slutföras innan filer kan synkroniseras mellan moln-och Server slut punkter. Tiden för att slutföra jobbet beror på storleken på namn området i Azure-filresursen. Server slut punktens hälsa bör uppdateras när ändrings uppräknings jobbet har slutförts.

### <a name="how-do-i-monitor-sync-health"></a><a id="broken-sync"></a>Vill du Hur gör jag för att övervaka Sync-hälsa?
# <a name="portal"></a>[Portal](#tab/portal1)
I varje Sync-grupp kan du öka detalj nivån för de enskilda Server slut punkterna för att se status för de senaste slutförda Sync-sessionerna. En grön hälso kolumn och filer som inte synkroniserar värdet 0 anger att synkroniseringen fungerar som förväntat. Om detta inte är fallet, se nedan för en lista över vanliga synkroniseringsfel och hur du hanterar filer som inte synkroniseras. 

![En skärm bild av Azure Portal](media/storage-sync-files-troubleshoot/portal-sync-health.png)

# <a name="server"></a>[Server](#tab/server)
Gå till serverns telemetridata, som du hittar i Loggboken på `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` . Händelse 9102 motsvarar en slutförd Sync-session. för den senaste synkroniseringens synkroniseringsstatus söker du efter den senaste händelsen med ID 9102. SyncDirection anger om den här sessionen har laddats upp eller laddats ned. Om HResult är 0 lyckades Sync-sessionen. Ett HResult som inte är noll innebär att det uppstod ett fel under synkroniseringen. nedan visas en lista över vanliga fel. Om PerItemErrorCount är större än 0 innebär det att vissa filer eller mappar inte har synkroniserats korrekt. Det är möjligt att ha ett HResult på 0, men ett PerItemErrorCount som är större än 0.

Nedan visas ett exempel på en lyckad uppladdning. För det kortfattat är det bara vissa av de värden som finns i varje 9102-händelse i listan nedan. 

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: 0, 
SyncFileCount: 2, SyncDirectoryCount: 0,
AppliedFileCount: 2, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0,
TransferredFiles: 2, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

I motsatt grad kan en misslyckad uppladdning se ut så här:

```
Replica Sync session completed.
SyncDirection: Upload,
HResult: -2134364065,
SyncFileCount: 0, SyncDirectoryCount: 0, 
AppliedFileCount: 0, AppliedDirCount: 0, AppliedTombstoneCount 0, AppliedSizeBytes: 0.
PerItemErrorCount: 0, 
TransferredFiles: 0, TransferredBytes: 0, FailedToTransferFiles: 0, FailedToTransferBytes: 0.
```

Synkronisering av sessioner fungerar ibland inte generellt eller har en PerItemErrorCount som inte är noll, men ändå fortsätter att vidarebefordra, medan vissa filer synkroniseras korrekt. Detta kan ses i de använda * fälten (AppliedFileCount, AppliedDirCount, AppliedTombstoneCount och AppliedSizeBytes), som visar hur mycket av sessionen som lyckas. Om du ser flera Sync-sessioner i en rad som inte fungerar, men som har ett ökande använt * antal, bör du ge synkroniseringstid att försöka igen innan du öppnar ett support ärende.

---

### <a name="how-do-i-monitor-the-progress-of-a-current-sync-session"></a>Hur övervakar jag förloppet för en pågående synkronisering?
# <a name="portal"></a>[Portal](#tab/portal1)
I Sync-gruppen går du till Server slut punkten i fråga och tittar på avsnittet Synkronisera aktivitet för att se hur många filer som har överförts eller laddats ned i den aktuella synkroniseringen. Observera att denna status fördröjs med cirka 5 minuter och om synkroniseringssessionen är tillräckligt liten att slutföras inom den tiden kanske den inte rapporteras i portalen. 

# <a name="server"></a>[Server](#tab/server)
Titta på den senaste 9302 händelsen i telemetri-loggen på servern (i Loggboken går du till program och tjänster Logs\Microsoft\FileSync\Agent\Telemetry). Den här händelsen anger statusen för den aktuella synkroniseringssessionen. TotalItemCount anger hur många filer som ska synkroniseras, AppliedItemCount antalet filer som har synkroniserats hittills och PerItemErrorCount antalet filer som inte kan synkroniseras (se nedan för att hantera detta).

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
Kontrol lera följande för varje server i en specifik Sync-grupp:
- Tidsstämplar för senaste försök till synkronisering för både överföring och nedladdning har nyligen gjorts.
- Statusen är grön för både överföring och hämtning.
- Fältet Synkronisera aktivitet visar väldigt få eller inga filer kvar för synkronisering.
- Fältet filer som inte synkroniseras är 0 för både överföring och hämtning.

# <a name="server"></a>[Server](#tab/server)
Titta på de slutförda synkroniseringarna, som är markerade med 9102 händelser i händelse loggen för telemetri för varje server (i Loggboken går du till `Applications and Services Logs\Microsoft\FileSync\Agent\Telemetry` ). 

1. På en specifik server vill du se till att de senaste överförings-och nedladdnings sessionerna har slutförts. Det gör du genom att kontrol lera att HResult-och PerItemErrorCount är 0 för både överföring och hämtning (fältet SyncDirection visar om en specifik session är en överförings-eller hämtnings session). Observera att om du inte ser en nyligen slutförd svarsomgång är det troligt att en Sync-session pågår, vilket är förväntat om du precis har lagt till eller ändrat en stor mängd data.
2. När en server är helt uppdaterad med molnet och inte har några ändringar att synkronisera i båda riktningarna, visas tomma Sync-sessioner. Dessa anges genom att ladda upp och ladda ned händelser där alla Sync *-fält (SyncFileCount, SyncDirCount, SyncTombstoneCount och SyncSizeBytes) är noll, vilket innebär att det inte fanns något att synkronisera. Observera att dessa tomma Sync-sessioner inte kan förekomma på servrar med hög omsättning eftersom det alltid finns något nytt att synkronisera. Om det inte finns någon Sync-aktivitet bör de inträffa var 30: e minut. 
3. Om alla servrar är uppdaterade med molnet, vilket innebär att de senaste överförings-och nedladdnings sessionerna är tomma, kan du säga med rimlig säkerhet att hela systemet är synkroniserat. 
    
Observera att om du har gjort ändringar direkt i Azure-filresursen kommer Azure File Sync inte att identifiera ändringen förrän ändrings uppräkningen körs, vilket sker var 24: e timme. Det är möjligt att en-server säger att den är uppdaterad med molnet när det faktiskt har gjorts nya ändringar som gjorts direkt i Azure-filresursen. 

---

### <a name="how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing"></a>Hur gör jag för att se om det finns vissa filer eller mappar som inte synkroniseras?
Om din PerItemErrorCount på servern eller filerna som inte synkroniserar antalet i portalen är större än 0 för en viss Sync-session, innebär det att vissa objekt inte kan synkroniseras. Filer och mappar kan ha egenskaper som hindrar dem från att synkronisera. Dessa egenskaper kan vara beständiga och kräver en uttrycklig åtgärd för att återuppta synkroniseringen, till exempel ta bort tecken som inte stöds från fil-eller mappnamnet. De kan också vara tillfälliga, vilket innebär att filen eller mappen automatiskt återupptar synkroniseringen. till exempel kommer filer med öppna referenser automatiskt att återuppta synkronisering när filen stängs. När Azure File Sync-motorn identifierar ett sådant problem skapas en fellogg som kan parsas för att lista de objekt som inte synkroniseras korrekt.

Om du vill se de här felen kör du **FileSyncErrorsReport.ps1** PowerShell-skriptet (finns i katalogen agent installation i Azure File Sync agent) för att identifiera filer som inte synkroniserades på grund av öppna referenser, tecken som inte stöds eller andra problem. I fältet ItemPath visas platsen för filen i förhållande till rot katalogen för synkronisering. Se listan över vanliga synkroniseringsfel nedan för reparations steg.

> [!Note]  
> Om FileSyncErrorsReport.ps1-skriptet returnerar "det fanns inga filfel" eller inte visar fel meddelanden per objekt för synkroniseringsresursen, är orsaken antingen:
>
>- Orsak 1: den senaste slutförda synkroniseringen saknade fel per objekt. Portalen bör uppdateras snart för att visa 0 filer som inte synkroniseras. 
>    - Kontrol lera [händelse-ID 9102](?tabs=server%252cazure-portal#broken-sync) i händelse loggen för telemetri för att bekräfta att PerItemErrorCount är 0. 
>
>- Orsak 2: händelse loggen för ItemResults på servern omsluts på grund av för många fel per objekt och händelse loggen innehåller inte längre fel för den här synkroniseringsresursen.
>    - För att förhindra det här problemet ökar du storleken på händelse loggen för ItemResults. Händelse loggen ItemResults finns under "program och tjänster Logs\Microsoft\FileSync\Agent" i Loggboken. 

#### <a name="troubleshooting-per-filedirectory-sync-errors"></a>Fel sökning per fil/katalog synkroniseringsfel
**ItemResults logg per objekt, synkroniseringsfel**  

| HRESULT | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80070043 | – 2147942467 | ERROR_BAD_NET_NAME | Det går inte att komma åt den skiktade filen på servern. Det här problemet uppstår om den nivåindelade filen inte återkallades innan du tog bort en serverslutpunkt. | Information om hur du löser det här problemet finns i [skiktade filer är inte tillgängliga på servern när du har tagit bort en server slut punkt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80c80207 | – 2134375929 | ECS_E_SYNC_CONSTRAINT_CONFLICT | Det går inte att synkronisera fil-eller katalog ändringen än eftersom en beroende mapp inte har synkroniserats ännu. Det här objektet kommer att synkroniseras när de beroende ändringarna har synkroniserats. | Ingen åtgärd krävs. Om felet kvarstår under flera dagar kan du använda FileSyncErrorsReport.ps1 PowerShell-skriptet för att avgöra varför den beroende mappen ännu inte har synkroniserats. |
| 0x80C8028A | – 2134375798 | ECS_E_SYNC_CONSTRAINT_CONFLICT_ON_FAILED_DEPENDEE | Det går inte att synkronisera fil-eller katalog ändringen än eftersom en beroende mapp inte har synkroniserats ännu. Det här objektet kommer att synkroniseras när de beroende ändringarna har synkroniserats. | Ingen åtgärd krävs. Om felet kvarstår under flera dagar kan du använda FileSyncErrorsReport.ps1 PowerShell-skriptet för att avgöra varför den beroende mappen ännu inte har synkroniserats. |
| 0x80c80284 | – 2134375804 | ECS_E_SYNC_CONSTRAINT_CONFLICT_SESSION_FAILED | Det går inte att synkronisera fil-eller katalog ändringen än eftersom en beroende mapp inte har synkroniserats ännu och synkroniseringen misslyckades. Det här objektet kommer att synkroniseras när de beroende ändringarna har synkroniserats. | Ingen åtgärd krävs. Om felet kvarstår bör du undersöka synkroniseringsfel. |
| 0x8007007b | – 2147024773 | ERROR_INVALID_NAME | Fil-eller katalog namnet är ogiltigt. | Byt namn på filen eller katalogen i fråga. Mer information finns i [hantera tecken som inte stöds](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x80c80255 | – 2134375851 | ECS_E_XSMB_REST_INCOMPATIBILITY | Fil-eller katalog namnet är ogiltigt. | Byt namn på filen eller katalogen i fråga. Mer information finns i [hantera tecken som inte stöds](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x80c80018 | – 2134376424 | ECS_E_SYNC_FILE_IN_USE | Filen kan inte synkroniseras eftersom den används. Filen synkroniseras när den inte längre används. | Ingen åtgärd krävs. Azure File Sync skapar en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser. |
| 0x80c8031d | – 2134375651 | ECS_E_CONCURRENCY_CHECK_FAILED | Filen har ändrats, men ändringen har ännu inte identifierats i synkroniseringen. Synkroniseringen återställs efter att den här ändringen har upptäckts. | Ingen åtgärd krävs. |
| 0x80070002 | – 2147024894 | ERROR_FILE_NOT_FOUND | Filen togs bort och synkroniseringen är inte medveten om ändringen. | Ingen åtgärd krävs. Synkroniseringen kommer att sluta logga det här felet när ändrings identifieringen upptäcker att filen har tagits bort. |
| 0x80070003 | – 2147942403 | ERROR_PATH_NOT_FOUND | Borttagning av en fil eller katalog kan inte synkroniseras eftersom objektet redan har tagits bort från målet och synkroniseringen inte är medveten om ändringen. | Ingen åtgärd krävs. Synkroniseringen kommer att sluta logga det här felet när ändrings identifieringen körs på målet och synkroniseringen upptäcker att objektet har tagits bort. |
| 0x80c80205 | – 2134375931 | ECS_E_SYNC_ITEM_SKIP | Filen eller katalogen hoppades över men kommer att synkroniseras under nästa Sync-session. Om det här felet rapporteras när objektet hämtas, är fil-eller katalog namnet mer troligt än vad som är tillåtet. | Ingen åtgärd krävs om det här felet rapporteras när filen laddas upp. Om felet rapporteras när du laddar ned filen byter du namn på den aktuella filen eller katalogen. Mer information finns i [hantera tecken som inte stöds](?tabs=portal1%252cazure-portal#handling-unsupported-characters) . |
| 0x800700B7 | – 2147024713 | ERROR_ALREADY_EXISTS | Det går inte att synkronisera en fil eller katalog eftersom objektet redan finns i målet och synkroniseringen inte är medveten om ändringen. | Ingen åtgärd krävs. Synkroniseringen kommer att sluta logga det här felet när ändrings identifieringen körs på målet och synkroniseringen är medveten om detta nya objekt. |
| 0x80c8603e | – 2134351810 | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED | Det går inte att synkronisera filen eftersom gränsen för Azure-filresursen har nåtts. | Information om hur du löser det här problemet finns i avsnittet [lagrings gräns för Azure-filresurs](?tabs=portal1%252cazure-portal#-2134351810) i fel söknings guiden. |
| 0x80c8027C | – 2134375812 | ECS_E_ACCESS_DENIED_EFS | Filen krypteras av en lösning som inte stöds (t. ex. NTFS EFS). | Dekryptera filen och Använd en krypterings lösning som stöds. En lista över lösningar som stöds finns i avsnittet [Krypteringslösningar](./storage-sync-files-planning.md#encryption) i planeringsguiden. |
| 0x80c80283 | – 2160591491 | ECS_E_ACCESS_DENIED_DFSRRO | Filen finns i en skrivskyddad DFS-R-replikmapp. | Filen finns i en skrivskyddad DFS-R-replikmapp. Azure Files Sync stöder inte serverslutpunkter i skrivskyddade DFS-R-replikeringsmappar. Mer information finns i [planerings guiden](./storage-sync-files-planning.md#distributed-file-system-dfs) . |
| 0x80070005 | – 2147024891 | ERROR_ACCESS_DENIED | Filen har statusen borttagning väntar. | Ingen åtgärd krävs. Filen tas bort när alla öppna fil referenser är stängda. |
| 0x80c86044 | – 2134351804 | ECS_E_AZURE_AUTHORIZATION_FAILED | Filen kan inte synkroniseras eftersom inställningarna för brand väggen och det virtuella nätverket på lagrings kontot är aktiverade och servern inte har åtkomst till lagrings kontot. | Lägg till serverns IP-adress eller virtuella nätverk genom att följa stegen som beskrivs i avsnittet [Konfigurera brand vägg och virtuella nätverks inställningar](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) i distributions guiden. |
| 0x80c80243 | – 2134375869 | ECS_E_SECURITY_DESCRIPTOR_SIZE_TOO_LARGE | Det går inte att synkronisera filen eftersom säkerhets beskrivnings storleken överskrider gränsen på 64 KiB. | Lös problemet genom att ta bort filens åtkomstkontrollposter så att säkerhetsbeskrivningen minskar i storlek. |
| 0x8000ffff | -2147418113 | E_UNEXPECTED | Det går inte att synkronisera filen på grund av ett oväntat fel. | Om felet kvarstår i flera dagar öppnar du ett support ärende. |
| 0x80070020 | – 2147024864 | ERROR_SHARING_VIOLATION | Filen kan inte synkroniseras eftersom den används. Filen synkroniseras när den inte längre används. | Ingen åtgärd krävs. |
| 0x80c80017 | – 2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Filen ändrades under synkroniseringen, så den måste synkroniseras igen. | Ingen åtgärd krävs. |
| 0x80070017 | – 2147024873 | ERROR_CRC | Det går inte att synkronisera filen på grund av CRC-fel. Det här felet kan inträffa om en fil med skikt inte återkallades innan en server slut punkt togs bort eller om filen är skadad. | Information om hur du löser det här problemet finns i [skiktade filer är inte tillgängliga på servern när du har tagit bort en server slut punkt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) för att ta bort filer som är överblivna. Om felet fortsätter att inträffa efter att du tagit bort överblivna filer på nivån, kör [chkdsk](/windows-server/administration/windows-commands/chkdsk) på volymen. |
| 0x80c80200 | – 2134375936 | ECS_E_SYNC_CONFLICT_NAME_EXISTS | Det går inte att synkronisera filen eftersom det maximala antalet konfliktskapande filer har nåtts. Azure File Sync stöder 100-konfliktskapande filer per fil. Mer information om fil konflikter finns i Azure File Sync [vanliga frågor och svar](./storage-files-faq.md#afs-conflict-resolution). | Lös problemet genom att minska antalet konfliktskapande filer. Filen kommer att synkroniseras när antalet konfliktskapande filer är mindre än 100. |

#### <a name="handling-unsupported-characters"></a>Hantera tecken som inte stöds
Om **FileSyncErrorsReport.ps1** PowerShell-skriptet visar synkroniseringsfel per objekt på grund av tecken som inte stöds (felkod 0x8007007B eller 0x80c80255), bör du ta bort eller byta namn på tecknen vid fel från respektive fil namn. PowerShell kommer förmodligen att skriva ut dessa tecken som frågetecken eller tomma rektanglar eftersom de flesta av dessa tecken inte har någon standardiserad visuell kodning. 
> [!Note]  
> [Utvärderings verktyget](storage-sync-files-planning.md#evaluation-cmdlet) kan användas för att identifiera tecken som inte stöds. Om data uppsättningen har flera filer med ogiltiga tecken använder du [ScanUnsupportedChars](https://github.com/Azure-Samples/azure-files-samples/tree/master/ScanUnsupportedChars) -skriptet för att byta namn på filer som innehåller tecken som inte stöds.

Tabellen nedan innehåller alla Unicode-tecken Azure File Sync ännu inte har stöd för.

| Teckenuppsättning | Antal bokstäver |
|---------------|-----------------|
| <ul><li>0x0000009D (OSC operativ system kommando)</li><li>0x00000090 (DCS enhets kontroll sträng)</li><li>0x0000008F (SS3 Single Shift tre)</li><li>0x00000081 (för inställning för hög oktett)</li><li>0x0000007F (del Delete)</li><li>0x0000008D (RI omvänd linje matning)</li></ul> | 6 |
| 0x0000FDD0-0x0000FDEF (arabiska presentations formulär – a) | 32 |
| 0x0000FFF0 – 0x0000FFFF (Special) | 16 |
| <ul><li>0x0001FFFE-0x0001FFFF = 2 (ej Character)</li><li>0x0002FFFE-0x0002FFFF = 2 (ej Character)</li><li>0x0003FFFE-0x0003FFFF = 2 (ej Character)</li><li>0x0004FFFE-0x0004FFFF = 2 (ej Character)</li><li>0x0005FFFE-0x0005FFFF = 2 (ej Character)</li><li>0x0006FFFE-0x0006FFFF = 2 (ej Character)</li><li>0x0007FFFE-0x0007FFFF = 2 (ej Character)</li><li>0x0008FFFE-0x0008FFFF = 2 (ej Character)</li><li>0x0009FFFE-0x0009FFFF = 2 (ej Character)</li><li>0x000AFFFE-0x000AFFFF = 2 (ej Character)</li><li>0x000BFFFE-0x000BFFFF = 2 (ej Character)</li><li>0x000CFFFE-0x000CFFFF = 2 (ej Character)</li><li>0x000DFFFE-0x000DFFFF = 2 (ej Character)</li><li>0x000EFFFE-0x000EFFFF = 2 (odefinierat)</li><li>0x000FFFFE-0x000FFFFF = 2 (extra privat användnings yta)</li></ul> | 30 |
| 0x0010FFFE, 0x0010FFFF | 2 |

### <a name="common-sync-errors"></a>Vanliga synkroniseringsfel
<a id="-2147023673"></a>**Den synkroniserade sessionen avbröts.**  

| | |
|-|-|
| **HRESULT** | 0x800704c7 |
| **HRESULT (decimal)** | – 2147023673 | 
| **Felsträng** | ERROR_CANCELLED |
| **Reparation krävs** | No |

Sync-sessioner kan Miss lyckas av olika orsaker, inklusive servern som startas om eller uppdateras, VSS-ögonblicksbilder osv. Även om felet ser ut som kräver uppföljning, är det säkert att ignorera det här felet om det inte behålls under en period på flera timmar.

<a id="-2147012889"></a>**Det gick inte att upprätta en anslutning till tjänsten.**    

| | |
|-|-|
| **HRESULT** | 0x80072EE7 |
| **HRESULT (decimal)** | – 2147012889 | 
| **Felsträng** | WININET_E_NAME_NOT_RESOLVED |
| **Reparation krävs** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134376372"></a>**Användarbegäran begränsades av tjänsten.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004c |
| **HRESULT (decimal)** | – 2134376372 |
| **Felsträng** | ECS_E_USER_REQUEST_THROTTLED |
| **Reparation krävs** | No |

Ingen åtgärd krävs. servern kommer att försöka igen. Om felet kvarstår i flera timmar kan du skapa en supportförfrågan.

<a id="-2134364043"></a>**Synkroniseringen blockeras tills ändrings identifieringen har slutförts efter återställning**  

| | |
|-|-|
| **HRESULT** | 0x80c83075 |
| **HRESULT (decimal)** | – 2134364043 |
| **Felsträng** | ECS_E_SYNC_BLOCKED_ON_CHANGE_DETECTION_POST_RESTORE |
| **Reparation krävs** | No |

Ingen åtgärd krävs. När en fil-eller fil resurs (moln slut punkt) återställs med hjälp av Azure Backup blockeras synkronisering tills ändrings identifieringen är klar på Azure-filresursen. Den här identifieringen körs direkt när återställningen är färdig och hur lång tid det tar beror på antalet filer i filresursen.

<a id="-2147216747"></a>**Det gick inte att synkronisera eftersom Sync-databasen har inaktiverats.**  

| | |
|-|-|
| **HRESULT** | 0x80041295 |
| **HRESULT (decimal)** | – 2147216747 |
| **Felsträng** | SYNC_E_METADATA_INVALID_OPERATION |
| **Reparation krävs** | No |

Det här felet uppstår vanligtvis när ett säkerhetskopieringsprogram skapar en VSS-ögonblicksbild och Sync-databasen tas bort från minnet. Om felet kvarstår i flera timmar kan du skapa en supportförfrågan.

<a id="-2134364065"></a>**Synkroniseringen har inte åtkomst till den Azure-filresurs som anges i moln slut punkten.**  

| | |
|-|-|
| **HRESULT** | 0x80c8305f |
| **HRESULT (decimal)** | – 2134364065 |
| **Felsträng** | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED |
| **Reparation krävs** | Yes |

Det här felet beror på att Azure File Sync-agenten inte kommer åt Azure-filresursen, vilket kan bero på att Azure-filresursen eller lagringskontot som är värd inte längre finns. Du kan felsöka det här felet genom att utföra följande steg:

1. [Kontrol lera att lagrings kontot finns.](#troubleshoot-storage-account)
2. [Se till att Azure-filresursen finns.](#troubleshoot-azure-file-share)
3. [Se till att Azure File Sync har åtkomst till lagrings kontot.](#troubleshoot-rbac)
4. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134351804"></a>**Det gick inte att synkronisera eftersom begäran inte har behörighet att utföra den här åtgärden.**  

| | |
|-|-|
| **HRESULT** | 0x80c86044 |
| **HRESULT (decimal)** | – 2134351804 |
| **Felsträng** | ECS_E_AZURE_AUTHORIZATION_FAILED |
| **Reparation krävs** | Yes |

Felet beror på att Azure File Sync agent inte har behörighet att komma åt Azure-filresursen. Du kan felsöka det här felet genom att utföra följande steg:

1. [Kontrol lera att lagrings kontot finns.](#troubleshoot-storage-account)
2. [Se till att Azure-filresursen finns.](#troubleshoot-azure-file-share)
3. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)
4. [Se till att Azure File Sync har åtkomst till lagrings kontot.](#troubleshoot-rbac)

<a id="-2134364064"></a><a id="cannot-resolve-storage"></a>**Det gick inte att matcha det lagrings konto namn som används.**  

| | |
|-|-|
| **HRESULT** | 0x80C83060 |
| **HRESULT (decimal)** | – 2134364064 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_NAME_UNRESOLVED |
| **Reparation krävs** | Yes |

1. Kontrol lera att du kan matcha lagrings-DNS-namnet från servern.

    ```powershell
    Test-NetConnection -ComputerName <storage-account-name>.file.core.windows.net -Port 443
    ```
2. [Kontrol lera att lagrings kontot finns.](#troubleshoot-storage-account)
3. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364022"></a><a id="storage-unknown-error"></a>**Ett okänt fel uppstod vid åtkomst till lagrings kontot.**  

| | |
|-|-|
| **HRESULT** | 0x80c8308a |
| **HRESULT (decimal)** | – 2134364022 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_UNKNOWN_ERROR |
| **Reparation krävs** | Yes |

1. [Kontrol lera att lagrings kontot finns.](#troubleshoot-storage-account)
2. [Kontrollera att lagringskontots brandvägg och virtuella nätverk har rätt inställningar (om de är aktiverade)](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings)

<a id="-2134364014"></a>**Synkroniseringen misslyckades på grund av att lagrings kontot är låst.**  

| | |
|-|-|
| **HRESULT** | 0x80c83092 |
| **HRESULT (decimal)** | – 2134364014 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_LOCKED |
| **Reparation krävs** | Yes |

Felet beror på att lagrings kontot har ett skrivskyddat [resurs lås](../../azure-resource-manager/management/lock-resources.md). Lös problemet genom att ta bort det skrivskyddade resurs låset på lagrings kontot. 

<a id="-1906441138"></a>**Synkroniseringen misslyckades på grund av ett problem med Sync-databasen.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e044e |
| **HRESULT (decimal)** | – 1906441138 |
| **Felsträng** | JET_errWriteConflict |
| **Reparation krävs** | Yes |

Felet uppstår när det finns ett problem med den interna databasen som används av Azure File Sync. När det här problemet uppstår skapar du en support förfrågan så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2134364053"></a>**Den Azure File Sync agent-version som är installerad på servern stöds inte.**  

| | |
|-|-|
| **HRESULT** | 0x80C8306B |
| **HRESULT (decimal)** | – 2134364053 |
| **Felsträng** | ECS_E_AGENT_VERSION_BLOCKED |
| **Reparation krävs** | Yes |

Det här felet inträffar om den Azure File Sync-agentversion som är installerad på servern inte stöds. Lös problemet genom att [Uppgradera]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#upgrade-paths) till en [agent version som stöds]( https://docs.microsoft.com/azure/storage/files/storage-files-release-notes#supported-versions).

<a id="-2134351810"></a>**Du har nått lagrings gränsen för Azure-filresursen.**  

| | |
|-|-|
| **HRESULT** | 0x80c8603e |
| **HRESULT (decimal)** | – 2134351810 |
| **Felsträng** | ECS_E_AZURE_STORAGE_SHARE_SIZE_LIMIT_REACHED |
| **Reparation krävs** | Yes |

Det här felet uppstår när du överskrider lagringsgränsen för Azure-filresursen, och det kan hända om en kvot används för en Azure-filresurs eller om användningen överskrider gränsen för en Azure-filresurs. Mer information finns i den [aktuella gränsen för en Azure-filresurs](storage-files-scale-targets.md).

1. Navigera till Sync-gruppen i tjänsten för synkronisering av lagring.
2. Välj moln slut punkten i Sync-gruppen.
3. Notera namnet på Azure-filresursen i fönstret öppna.
4. Välj det länkade lagringskontot. Om den här länken Miss lyckas har det refererade lagrings kontot tagits bort.

    ![En skärm bild som visar informations fönstret för moln slut punkter med en länk till lagrings kontot.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

5. Välj **filer** om du vill visa listan över fil resurser.
6. Klicka på de tre punkterna i slutet av raden för den Azure-filresurs som refereras av moln slut punkten.
7. Kontrollera att **Användning** är lägre än **Kvot**. Observera att om en alternativ kvot har angetts matchar kvoten den [maximala storleken på Azure-filresursen](storage-files-scale-targets.md).

    ![En skärm bild av egenskaperna för Azure-filresursen.](media/storage-sync-files-troubleshoot/file-share-limit-reached-1.png)

Om resursen är full och ingen kvot har angetts kan du åtgärda problemet genom att göra varje undermapp vid den aktuella serverslutpunkten till en egen serverslutpunkt i en egen separat synkroniseringsgrupp. På så vis synkroniseras varje undermapp till enskilda Azure-filresurser.

<a id="-2134351824"></a>**Det går inte att hitta Azure-filresursen.**  

| | |
|-|-|
| **HRESULT** | 0x80c86030 |
| **HRESULT (decimal)** | – 2134351824 |
| **Felsträng** | ECS_E_AZURE_FILE_SHARE_NOT_FOUND |
| **Reparation krävs** | Yes |

Felet uppstår när Azure-filresursen inte är tillgänglig. Så här felsöker du:

1. [Kontrol lera att lagrings kontot finns.](#troubleshoot-storage-account)
2. [Se till att Azure-filresursen finns.](#troubleshoot-azure-file-share)

Om Azure-filresursen har tagits bort måste du skapa en ny fil resurs och sedan återskapa gruppen. 

<a id="-2134364042"></a>**Synkroniseringen pausas medan den här Azure-prenumerationen pausas.**  

| | |
|-|-|
| **HRESULT** | 0x80C83076 |
| **HRESULT (decimal)** | – 2134364042 |
| **Felsträng** | ECS_E_SYNC_BLOCKED_ON_SUSPENDED_SUBSCRIPTION |
| **Reparation krävs** | Yes |

Det här felet uppstår vid uppehåll i Azure-prenumerationen. Synkroniseringen aktiveras igen när Azure-prenumerationen återställs. Se [Varför är min Azure-prenumeration inaktive rad och hur återaktiverar jag den?](../../cost-management-billing/manage/subscription-disabled.md) för mer information.

<a id="-2134375618"></a>**Lagrings kontot har en brand vägg eller virtuella nätverk som har kon figurer ATS.**  

| | |
|-|-|
| **HRESULT** | 0x80c8033e |
| **HRESULT (decimal)** | – 2134375618 |
| **Felsträng** | ECS_E_SERVER_BLOCKED_BY_NETWORK_ACL |
| **Reparation krävs** | Yes |

Felet uppstår när det inte går att nå Azure-filresursen på grund av en brandvägg för lagringskontot eller om lagringskontot tillhör ett virtuellt nätverk. Kontrol lera att inställningarna för brand vägg och virtuellt nätverk på lagrings kontot är korrekt konfigurerade. Mer information finns i [Konfigurera inställningar för brand vägg och virtuellt nätverk](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings). 

<a id="-2134375911"></a>**Synkroniseringen misslyckades på grund av ett problem med Sync-databasen.**  

| | |
|-|-|
| **HRESULT** | 0x80c80219 |
| **HRESULT (decimal)** | – 2134375911 |
| **Felsträng** | ECS_E_SYNC_METADATA_WRITE_LOCK_TIMEOUT |
| **Reparation krävs** | No |

Felet brukar lösas av sig självt och inträffa om det finns:

* Ett stort antal fil ändringar på servrarna i Sync-gruppen.
* Ett stort antal fel på enskilda filer och kataloger.

Om felet kvarstår i fler timmar än några timmar kan du skapa en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2146762487"></a>**Servern kunde inte upprätta en säker anslutning. Moln tjänsten tog emot ett oväntat certifikat.**  

| | |
|-|-|
| **HRESULT** | 0x800b0109 |
| **HRESULT (decimal)** | – 2146762487 |
| **Felsträng** | CERT_E_UNTRUSTEDROOT |
| **Reparation krävs** | Yes |

Det här felet kan inträffa om din organisation använder en TLS-terminering av proxy eller om en skadlig enhet fångar upp trafiken mellan servern och tjänsten Azure File Sync. Om du är säker på att detta är förväntat (eftersom din organisation använder en TLS-terminering proxy) hoppar du över certifikat verifiering med en åsidosättande register.

1. Skapa register värde för SkipVerifyingPinnedRootCertificate.

    ```powershell
    New-ItemProperty -Path HKLM:\SOFTWARE\Microsoft\Azure\StorageSync -Name SkipVerifyingPinnedRootCertificate -PropertyType DWORD -Value 1
    ```

2. Starta om synkroniseringstjänsten på den registrerade servern.

    ```powershell
    Restart-Service -Name FileSyncSvc -Force
    ```

Genom att ange det här registervärdet accepterar Azure File Sync-agenten alla lokalt betrodda TLS/SSL-certifikat när data överförs mellan servern och moln tjänsten.

<a id="-2147012894"></a>**Det gick inte att upprätta en anslutning till tjänsten.**  

| | |
|-|-|
| **HRESULT** | 0x80072ee2 |
| **HRESULT (decimal)** | – 2147012894 |
| **Felsträng** | WININET_E_TIMEOUT |
| **Reparation krävs** | Yes |

[!INCLUDE [storage-sync-files-bad-connection](../../../includes/storage-sync-files-bad-connection.md)]

<a id="-2134375680"></a>**Synkroniseringen misslyckades på grund av ett problem med autentiseringen.**  

| | |
|-|-|
| **HRESULT** | 0x80c80300 |
| **HRESULT (decimal)** | – 2134375680 |
| **Felsträng** | ECS_E_SERVER_CREDENTIAL_NEEDED |
| **Reparation krävs** | Yes |

Det här felet uppstår vanligtvis på grund av att servertiden är fel. Om servern körs på en virtuell dator kontrollerar du att tiden på värden är korrekt.

<a id="-2134364040"></a>**Synkroniseringen misslyckades på grund av att certifikatet upphör att gälla.**  

| | |
|-|-|
| **HRESULT** | 0x80c83078 |
| **HRESULT (decimal)** | – 2134364040 |
| **Felsträng** | ECS_E_AUTH_SRV_CERT_EXPIRED |
| **Reparation krävs** | Yes |

Det här felet beror på att det certifikat som används för autentisering har upphört att gälla.

Bekräfta att certifikatet har upphört med hjälp av följande steg:  
1. Öppna MMC-snapin-modulen certifikat, Välj dator konto och gå till certifikat (lokal dator) \Personal\Certificates.
2. Kontrol lera om certifikatet för klientautentisering har upphört att gälla.

Om certifikatet för klientautentisering har upphört att gälla löser du problemet genom att utföra följande steg:

1. Verifiera Azure File Sync agent version 4.0.1.0 eller senare är installerad.
2. Kör följande PowerShell-kommando på servern: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134375896"></a>**Det gick inte att synkronisera eftersom det inte gick att hitta autentiseringscertifikatet.**  

| | |
|-|-|
| **HRESULT** | 0x80c80228 |
| **HRESULT (decimal)** | – 2134375896 |
| **Felsträng** | ECS_E_AUTH_SRV_CERT_NOT_FOUND |
| **Reparation krävs** | Yes |

Det här felet beror på att det inte går att hitta certifikatet som används för autentisering.

Åtgärda problemet genom att följa dessa steg:

1. Verifiera Azure File Sync agent version 4.0.1.0 eller senare är installerad.
2. Kör följande PowerShell-kommando på servern: 

    ```powershell
    Reset-AzStorageSyncServerCertificate -ResourceGroupName <string> -StorageSyncServiceName <string>
    ```

<a id="-2134364039"></a>**Det gick inte att synkronisera eftersom det inte gick att hitta autentiserings-ID.**  

| | |
|-|-|
| **HRESULT** | 0x80c83079 |
| **HRESULT (decimal)** | – 2134364039 |
| **Felsträng** | ECS_E_AUTH_IDENTITY_NOT_FOUND |
| **Reparation krävs** | Yes |

Felet beror på att borttagningen av serverslutpunkten misslyckades och att slutpunkten nu är i ett delvis borttaget tillstånd. Lös problemet genom att försöka ta bort serverslutpunkten.

<a id="-1906441711"></a><a id="-2134375654"></a><a id="doesnt-have-enough-free-space"></a>**Den volym där Server slut punkten finns har ont om disk utrymme.**  

| | |
|-|-|
| **HRESULT** | 0x8e5e0211 |
| **HRESULT (decimal)** | – 1906441711 |
| **Felsträng** | JET_errLogDiskFull |
| **Reparation krävs** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c8031a |
| **HRESULT (decimal)** | – 2134375654 |
| **Felsträng** | ECS_E_NOT_ENOUGH_LOCAL_STORAGE |
| **Reparation krävs** | Yes |

Det här felet beror på att volymen är full. Felet uppstår vanligtvis när filer utanför serverslutpunkten använder utrymme på volymen. Frigör utrymme på volymen genom att lägga till ytterligare Server slut punkter, flytta filer till en annan volym eller öka storleken på volymen som server slut punkten finns på.

<a id="-2134364145"></a><a id="replica-not-ready"></a>**Tjänsten är ännu inte klar för synkronisering med den här server slut punkten.**  

| | |
|-|-|
| **HRESULT** | 0x80c8300f |
| **HRESULT (decimal)** | – 2134364145 |
| **Felsträng** | ECS_E_REPLICA_NOT_READY |
| **Reparation krävs** | No |

Felet beror på att moln slut punkten skapades med innehåll som redan finns på Azure-filresursen. Azure File Sync måste genomsöka Azure-filresursen för allt innehåll innan Server slut punkten kan fortsätta med den första synkroniseringen.

<a id="-2134375877"></a><a id="-2134375908"></a><a id="-2134375853"></a>**Synkroniseringen misslyckades på grund av problem med många enskilda filer.**  

| | |
|-|-|
| **HRESULT** | 0x80c8023b |
| **HRESULT (decimal)** | – 2134375877 |
| **Felsträng** | ECS_E_SYNC_METADATA_KNOWLEDGE_SOFT_LIMIT_REACHED |
| **Reparation krävs** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c8021c |
| **HRESULT (decimal)** | – 2134375908 |
| **Felsträng** | ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED |
| **Reparation krävs** | Yes |

| | |
|-|-|
| **HRESULT** | 0x80c80253 |
| **HRESULT (decimal)** | – 2134375853 |
| **Felsträng** | ECS_E_TOO_MANY_PER_ITEM_ERRORS |
| **Reparation krävs** | Yes |

Det går inte att synkronisera sessioner med ett av dessa fel när det finns många filer som inte kan synkroniseras med per objekt-fel. Utför stegen som beskrivs i [Hur gör jag för att se om det finns specifika filer eller mappar som inte synkroniseras?](?tabs=portal1%252cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing) avsnittet för att lösa felen per objekt. Öppna ett support ärende om du ECS_E_SYNC_METADATA_KNOWLEDGE_LIMIT_REACHED sync-fel.

> [!NOTE]
> Azure File Sync skapar en tillfällig VSS-ögonblicksbild en gång om dagen på servern för att synkronisera filer som har öppna referenser.

<a id="-2134376423"></a>**Synkroniseringen misslyckades på grund av ett problem med Server slut punktens sökväg.**  

| | |
|-|-|
| **HRESULT** | 0x80c80019 |
| **HRESULT (decimal)** | – 2134376423 |
| **Felsträng** | ECS_E_SYNC_INVALID_PATH |
| **Reparation krävs** | Yes |

Se till att sökvägen finns på en lokal NTFS-volym och inte är en referens punkt eller en befintlig server slut punkt.

<a id="-2134375817"></a>**Det gick inte att synkronisera eftersom filter driv Rutinens version inte är kompatibel med agent versionen**  

| | |
|-|-|
| **HRESULT** | 0x80C80277 |
| **HRESULT (decimal)** | – 2134375817 |
| **Felsträng** | ECS_E_INCOMPATIBLE_FILTER_VERSION |
| **Reparation krävs** | Yes |

Det här felet beror på att den version av filterdrivrutinen för molnnivåindelning (StorageSync.sys) som lästs in inte är kompatibel med Storage Sync Agent-tjänsten (FileSyncSvc). Om Azure File Sync-agenten har uppgraderats startar du om servern för att slutföra installationen. Om felet fortsätter att inträffa avinstallerar du agenten, startar om servern och installerar om Azure File Sync-agenten.

<a id="-2134376373"></a>**Tjänsten är inte tillgänglig för tillfället.**  

| | |
|-|-|
| **HRESULT** | 0x80c8004b |
| **HRESULT (decimal)** | – 2134376373 |
| **Felsträng** | ECS_E_SERVICE_UNAVAILABLE |
| **Reparation krävs** | No |

Felet beror på att tjänsten Azure File Sync-tjänsten inte är tillgänglig. Det här felet löses automatiskt när Azure File Sync-tjänsten är tillgänglig igen.

<a id="-2146233088"></a>**Det gick inte att synkronisera på grund av ett undantag.**  

| | |
|-|-|
| **HRESULT** | 0x80131500 |
| **HRESULT (decimal)** | – 2146233088 |
| **Felsträng** | COR_E_EXCEPTION |
| **Reparation krävs** | No |

Felet beror på att synkroniseringen misslyckades på grund av ett undantag. Om felet kvarstår i flera timmar kan du skapa en support förfrågan.

<a id="-2134364045"></a>**Synkroniseringen misslyckades eftersom lagrings kontot har redundansväxlats till en annan region.**  

| | |
|-|-|
| **HRESULT** | 0x80c83073 |
| **HRESULT (decimal)** | – 2134364045 |
| **Felsträng** | ECS_E_STORAGE_ACCOUNT_FAILED_OVER |
| **Reparation krävs** | Yes |

Felet beror på att lagringskontot har redundansväxlat till en annan region. Azure File Sync har inte stöd för redundansväxling av lagringskonton. Lagringskonton med Azure-filresurser som används som molnslutpunkter i Azure File Sync får inte redundansväxlas. Om du gör det slutar synkroniseringen att fungera och det kan leda till oväntade dataförluster för nyligen nivåbaserade filer. Lös problemet genom att flytta lagringskontot till den primära regionen.

<a id="-2134375922"></a>**Synkroniseringen misslyckades på grund av ett tillfälligt problem med Sync-databasen.**  

| | |
|-|-|
| **HRESULT** | 0x80c8020e |
| **HRESULT (decimal)** | – 2134375922 |
| **Felsträng** | ECS_E_SYNC_METADATA_WRITE_LEASE_LOST |
| **Reparation krävs** | No |

Det här felet uppstår på grund av ett internt problem med Sync-databasen. Det här felet löses automatiskt när Sync försöker igen. Om felet fortsätter under en längre tid kan du skapa en supportbegäran så kontaktar vi dig för att hjälpa dig att lösa problemet.

<a id="-2134364024"></a>**Synkroniseringen misslyckades på grund av ändringar i Azure Active Directory klient organisationen**  

| | |
|-|-|
| **HRESULT** | 0x80c83088 |
| **HRESULT (decimal)** | – 2134364024 | 
| **Felsträng** | ECS_E_INVALID_AAD_TENANT |
| **Reparation krävs** | Yes |

Kontrol lera att du har den senaste Azure File Sync agenten. Från och med agent v10 stöder Azure File Sync flytta prenumerationen till en annan Azure Active Directory klient.
 
När du har den senaste agent versionen måste du ge programmet Microsoft. StorageSync åtkomst till lagrings kontot (se [Se till att Azure File Sync har åtkomst till lagrings kontot](#troubleshoot-rbac)).

<a id="-2134364010"></a>**Det gick inte att synkronisera eftersom brand väggen och inget virtuellt nätverks undantag har kon figurer ATS**  

| | |
|-|-|
| **HRESULT** | 0x80c83096 |
| **HRESULT (decimal)** | – 2134364010 | 
| **Felsträng** | ECS_E_MGMT_STORAGEACLSBYPASSNOTSET |
| **Reparation krävs** | Yes |

Det här felet uppstår om inställningarna för brand vägg och virtuellt nätverk är aktiverade på lagrings kontot och undantaget "Tillåt betrodda Microsoft-tjänster för att komma åt det här lagrings kontot" inte är markerat. Lös problemet genom att följa de steg som beskrivs i avsnittet [Konfigurera inställningar för brandvägg och virtuellt nätverk](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) i distributionsguiden.

<a id="-2147024891"></a>**Synkroniseringen misslyckades eftersom behörigheterna för mappen System Volume information är felaktiga.**  

| | |
|-|-|
| **HRESULT** | 0x80070005 |
| **HRESULT (decimal)** | – 2147024891 |
| **Felsträng** | ERROR_ACCESS_DENIED |
| **Reparation krävs** | Yes |

Det här felet kan inträffa om kontot NT AUTHORITY\SYSTEM inte har behörighet till mappen System Volume Information på den volym där serverslutpunkten finns. OBS! om enskilda filer inte kan synkroniseras med ERROR_ACCESS_DENIED utför du stegen som beskrivs i avsnittet [fel sökning per fil/katalog synkroniseringsfel](?tabs=portal1%252cazure-portal#troubleshooting-per-filedirectory-sync-errors) .

Åtgärda problemet genom att följa dessa steg:

1. Ladda ned verktyget [Psexec](/sysinternals/downloads/psexec).
2. Kör följande kommando från en upphöjd kommando tolk för att starta en kommando tolk med system-kontot: **PsExec.exe-i-s-d cmd** 
3. Kör den kommandotolk som körs under systemkontot kör du följande kommando för att bekräfta att kontot NT AUTHORITY\SYSTEM inte har åtkomst till mappen System Volume Information: **cacls "partitionsbokstav:\system volume information" /T /C**
4. Om kontot NT AUTHORITY\SYSTEM inte har åtkomst till mappen System Volume Information kör du följande kommando: **cacls  "partitionsbokstav:\system volume information" /T /E /G "NT AUTHORITY\SYSTEM:F"**
    - Om steg 4 misslyckas med nekad åtkomst kör du följande kommando för att bli ägare till mappen System Volume Information och upprepar sedan steg 4: **takeown /A /R /F "partitionsbokstav:\System Volume Information"**

<a id="-2134375810"></a>**Synkroniseringen misslyckades eftersom Azure-filresursen togs bort och återskapades.**  

| | |
|-|-|
| **HRESULT** | 0x80c8027e |
| **HRESULT (decimal)** | – 2134375810 |
| **Felsträng** | ECS_E_SYNC_REPLICA_ROOT_CHANGED |
| **Reparation krävs** | Yes |

Felet beror på att Azure File Sync inte har stöd för borttagning och återskapande av en Azure-filresurs i samma synkroniseringsgrupp. 

Lös problemet genom att ta bort och återskapa synkroniseringsgruppen med hjälp av följande steg:

1. Ta bort alla Server slut punkter i Sync-gruppen.
2. Ta bort moln slut punkten. 
3. Ta bort synkroniseringsgruppen.
4. Om moln skiktning har Aktiver ATS på en server slut punkt tar du bort de överblivna filerna på servern genom att utföra stegen som dokumenteras i de [skiktade filerna är inte tillgängliga på servern när du har tagit bort ett Server slut punkts](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint) avsnitt.
5. Återskapa Sync-gruppen.

<a id="-2145844941"></a>**Synkroniseringen misslyckades eftersom HTTP-begäran omdirigerades**  

| | |
|-|-|
| **HRESULT** | 0x80190133 |
| **HRESULT (decimal)** | – 2145844941 |
| **Felsträng** | HTTP_E_STATUS_REDIRECT_KEEP_VERB |
| **Reparation krävs** | Yes |

Felet beror på att Azure File Sync inte stöder HTTP-omdirigering (3xx status kod). Lös problemet genom att inaktivera HTTP-omdirigering på proxyservern eller nätverks enheten.

<a id="-2134364027"></a>**En timeout uppstod under data överföringen offline, men den pågår fortfarande.**  

| | |
|-|-|
| **HRESULT** | 0x80c83085 |
| **HRESULT (decimal)** | – 2134364027 |
| **Felsträng** | ECS_E_DATA_INGESTION_WAIT_TIMEOUT |
| **Reparation krävs** | No |

Felet uppstår när en data inmatnings åtgärd överskrider tids gränsen. Det här felet kan ignoreras om synkroniseringen gör förlopp (AppliedItemCount är större än 0). Se [Hur gör jag för att övervaka förloppet för en aktuell Sync-session?](#how-do-i-monitor-the-progress-of-a-current-sync-session).

### <a name="common-troubleshooting-steps"></a>Vanliga fel söknings steg
<a id="troubleshoot-storage-account"></a>**Kontrol lera att lagrings kontot finns.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigera till Sync-gruppen i tjänsten för synkronisering av lagring.
2. Välj moln slut punkten i Sync-gruppen.
3. Notera namnet på Azure-filresursen i fönstret öppna.
4. Välj det länkade lagringskontot. Om den här länken Miss lyckas har det refererade lagrings kontot tagits bort.
    ![En skärm bild som visar informations fönstret för moln slut punkter med en länk till lagrings kontot.](media/storage-sync-files-troubleshoot/file-share-inaccessible-1.png)

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

<a id="troubleshoot-azure-file-share"></a>**Se till att Azure-filresursen finns.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klicka på **Översikt** i den vänstra innehålls förteckningen för att gå tillbaka till huvud sidan för lagrings kontot.
2. Välj **filer** om du vill visa listan över fil resurser.
3. Kontrol lera att fil resursen som moln slut punkten refererar till visas i listan över fil resurser (du bör notera detta i steg 1 ovan).

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

<a id="troubleshoot-rbac"></a>**Se till att Azure File Sync har åtkomst till lagrings kontot.**  
# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Klicka på **åtkomst kontroll (IAM)** i den vänstra innehålls förteckningen.
1. Klicka på fliken **roll tilldelningar** i listan över användare och program (*tjänstens huvud namn*) som har åtkomst till ditt lagrings konto.
1. Kontrol lera att **Microsoft. StorageSync** eller **hybrid File syncs tjänsten** (gammalt program namn) visas i listan med rollen **läsare och data åtkomst** . 

    ![En skärm bild av tjänsten hybrid File Sync tjänstens huvud namn på fliken åtkomst kontroll för lagrings kontot](media/storage-sync-files-troubleshoot/file-share-inaccessible-3.png)

    Om **Microsoft. StorageSync** eller **hybrid File syncs tjänsten** inte visas i listan utför du följande steg:

    - Klicka på **Lägg till**.
    - I fältet **roll** väljer du **läsare och data åtkomst**.
    - I fältet **Välj** skriver du **Microsoft. StorageSync**, väljer rollen och klickar på **Spara**.

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

### <a name="how-do-i-prevent-users-from-creating-files-containing-unsupported-characters-on-the-server"></a>Hur gör jag för att hindra användare från att skapa filer som innehåller tecken som inte stöds på servern?
Du kan använda fil gallret i Hanteraren för [fil server resurser](/windows-server/storage/fsrm/file-screening-management) för att blockera filer med tecken som inte stöds i deras namn från att skapas på servern. Du kan behöva göra detta med hjälp av PowerShell eftersom de flesta tecken som inte stöds inte kan skrivas ut och så att du måste omvandla sina hexadecimala representationer som tecken först.

Skapa först en FSRM-filgrupp med [cmdleten New-FsrmFileGroup](/powershell/module/fileserverresourcemanager/new-fsrmfilegroup). I det här exemplet definieras gruppen som bara innehåller två av de tecken som inte stöds, men du kan inkludera så många tecken som behövs i fil gruppen.

```powershell
New-FsrmFileGroup -Name "Unsupported characters" -IncludePattern @(("*"+[char]0x00000090+"*"),("*"+[char]0x0000008F+"*"))
```

När du har definierat en FSRM-filgrupp kan du skapa en FSRM-filskärm med hjälp av New-FsrmFileScreen-cmdleten.

```powershell
New-FsrmFileScreen -Path "E:\AFSdataset" -Description "Filter unsupported characters" -IncludeGroup "Unsupported characters"
```

> [!Important]  
> Observera att fil gallringar bara ska användas för att blockera skapandet av tecken som inte stöds av Azure File Sync. Om fil gallringar används i andra scenarier försöker synkroniseringen fort löp ande hämta filerna från Azure-filresursen till servern och kommer att blockeras på grund av filgallret, vilket leder till att hög utgående data skickas. 

## <a name="cloud-tiering"></a>Lagringsnivåer för moln 
Det finns två sökvägar för problem i moln skiktning:

- Filer kan Miss lyckas på nivån, vilket innebär att Azure File Sync Miss lyckas med att Azure Files en fil.
- Det går inte att återkalla filerna, vilket innebär att det Azure File Sync fil system filtret (StorageSync.sys) inte kan hämta data när en användare försöker få åtkomst till en fil som har flyttats på nivå.

Det finns två huvud klasser av problem som kan inträffa via en felsökväg:

- Moln lagrings problem
    - *Tillgänglighets problem med tillfällig lagrings tjänst*. Mer information finns i [serviceavtal (SLA) för Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_2/).
    - *Otillgänglig Azure-filresurs*. Det här felet uppstår vanligt vis när du tar bort Azure-filresursen när den fortfarande är en moln slut punkt i en Sync-grupp.
    - *Otillgängligt lagrings konto*. Det här felet uppstår vanligt vis när du tar bort lagrings kontot medan det fortfarande har en Azure-filresurs som är en moln slut punkt i en Sync-grupp. 
- Server problem 
  - *Azure File Sync fil system filter (StorageSync.sys) har inte lästs in*. För att kunna svara på nivå-/återställnings begär Anden måste det Azure File Sync fil system filtret läsas in. Det går inte att läsa in filtret eftersom det kan bero på flera orsaker, men det vanligaste skälet är att en administratör har inaktiverat den manuellt. Det Azure File Sync fil system filtret måste läsas in hela tiden för att Azure File Sync ska fungera korrekt.
  - *Saknad, skadad eller på annat sätt bruten referens punkt*. En referens punkt är en särskild data struktur för en fil som består av två delar:
    1. En referens tag gen som anger det operativ system som Azure File Sync fil system filter (StorageSync.sys) kan behöva utföra en åtgärd på IO till filen. 
    2. Parsa data, vilket indikerar fil systemet filtrera URI för filen på den associerade moln slut punkten (Azure-filresursen). 
        
       Det vanligaste sättet att en referens punkt kan bli skadad är om en administratör försöker ändra antingen taggen eller dess data. 
  - *Problem med nätverks anslutningen*. Servern måste ha Internet anslutning för att kunna skikta eller återkalla en fil.

Följande avsnitt visar hur du felsöker problem med moln skiktning och avgör om ett problem är ett problem med moln lagring eller ett Server problem.

### <a name="how-to-monitor-tiering-activity-on-a-server"></a>Övervaka nivåindelning på en server  
Om du vill övervaka nivå aktivitet på en server använder du händelse-ID 9003, 9016 och 9029 i händelse loggen för telemetri (finns under program och Services\Microsoft\FileSync\Agent i Loggboken).

- Händelse-ID 9003 ger fel distribution för en server slut punkt. Till exempel, totalt antal fel, felkod, osv. Obs! en händelse loggas per felkod.
- Händelse-ID 9016 ger ghosting-resultat för en volym. Till exempel är ledigt utrymme i procent, antalet filer som är avbildade i sessionen och antalet filer som inte kunde skickas till Ghost, osv.
- Händelse-ID 9029 tillhandahåller ghosting-sessionsinformation för en server slut punkt. Till exempel antal filer som har försökts i sessionen, antalet filer som skiktas i sessionen, antalet filer som redan har delats i nivå och så vidare.

### <a name="how-to-monitor-recall-activity-on-a-server"></a>Övervaka återkallning på en server
Om du vill övervaka återställnings aktivitet på en server använder du händelse-ID 9005, 9006, 9009 och 9059 i händelse loggen för telemetri (finns under program och Services\Microsoft\FileSync\Agent i Loggboken).

- Händelse-ID 9005 ger åter kallelse tillförlitlighet för en server slut punkt. Till exempel, totalt antal unika filer som används, totalt antal unika filer med misslyckad åtkomst, osv.
- Händelse-ID 9006 ger återställnings fel distribution för en server slut punkt. Till exempel totalt antal misslyckade förfrågningar, ErrorCode osv. Obs! en händelse loggas per felkod.
- Händelse-ID 9009 ger information om återkallande-sessionsinformation för en server slut punkt. Till exempel DurationSeconds, CountFilesRecallSucceeded, CountFilesRecallFailed osv.
- Händelse-ID 9059 tillhandahåller distribution av program åter användning för en server slut punkt. Till exempel ShareId, program namn och TotalEgressNetworkBytes.

### <a name="how-to-troubleshoot-files-that-fail-to-tier"></a>Så här felsöker du filer som inte kan skiktas
Om filer inte kan Azure Files till nivån:

1. I Loggboken granskar du händelse loggarna för telemetri, drift och diagnostik, som finns under program och Services\Microsoft\FileSync\Agent. 
   1. Kontrol lera att filerna finns i Azure-filresursen.

      > [!NOTE]
      > En fil måste synkroniseras med en Azure-filresurs innan den kan skiktas.

   2. Kontrol lera att servern är ansluten till Internet. 
   3. Verifiera Azure File Sync filter driv rutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
       - Kör i en upphöjd kommando tolk `fltmc` . Kontrol lera att driv rutinerna för StorageSync.sys och StorageSyncGuard.sys fil system filter visas.

> [!NOTE]
> Händelse-ID 9003 loggas en gång i timmen i händelse loggen för telemetri om en fil inte går att skikta (en händelse loggas per felkod). Kontrol lera avsnittet [nivåer fel och reparation](#tiering-errors-and-remediation) för att se om reparations stegen visas för felkoden.

### <a name="tiering-errors-and-remediation"></a>Fel i skiktning och reparation

| HRESULT | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80c86045 | – 2134351803 | ECS_E_INITIAL_UPLOAD_PENDING | Filen kunde inte skiktas eftersom den inledande överföringen pågår. | Ingen åtgärd krävs. Filen kommer att placeras i nivå när den inledande överföringen är klar. |
| 0x80c86043 | – 2134351805 | ECS_E_GHOSTING_FILE_IN_USE | Filen kunde inte skiktas eftersom den används. | Ingen åtgärd krävs. Filen kommer att placeras i nivå av när den inte längre används. |
| 0x80c80241 | – 2134375871 | ECS_E_GHOSTING_EXCLUDED_BY_SYNC | Filen kunde inte skiktas eftersom den exkluderas av synkroniseringen. | Ingen åtgärd krävs. Filer i undantags listan för synkronisering kan inte skiktas. |
| 0x80c86042 | – 2134351806 | ECS_E_GHOSTING_FILE_NOT_FOUND | Filen kunde inte skiktas eftersom den inte hittades på servern. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du om filen finns på servern. |
| 0x80c83053 | – 2134364077 | ECS_E_CREATE_SV_FILE_DELETED | Filen kunde inte skiktas eftersom den togs bort i Azure-filresursen. | Ingen åtgärd krävs. Filen bör tas bort från servern vid nästa hämtning av Sync-sessionen. |
| 0x80c8600e | – 2134351858 | ECS_E_AZURE_SERVER_BUSY | Filen kunde inte skiktas på grund av ett nätverks problem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverks anslutningen till Azure-filresursen. |
| 0x80072EE7 | – 2147012889 | WININET_E_NAME_NOT_RESOLVED | Filen kunde inte skiktas på grund av ett nätverks problem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverks anslutningen till Azure-filresursen. |
| 0x80070005 | – 2147024891 | ERROR_ACCESS_DENIED | Filen kunde inte skiktas på grund av nekad åtkomst. Det här felet kan inträffa om filen finns i en skrivskyddad DFS-R-replikmapp. | Azure Files Sync stöder inte serverslutpunkter i skrivskyddade DFS-R-replikeringsmappar. Mer information finns i [planerings guiden](./storage-sync-files-planning.md#distributed-file-system-dfs) . |
| 0x80072efe | – 2147012866 | WININET_E_CONNECTION_ABORTED | Filen kunde inte skiktas på grund av ett nätverks problem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverks anslutningen till Azure-filresursen. |
| 0x80c80261 | – 2134375839 | ECS_E_GHOSTING_MIN_FILE_SIZE | Filen kunde inte skiktas eftersom fil storleken är mindre än den storlek som stöds. | Om agent versionen är mindre än 9,0 är den minsta fil storlek som stöds 64 kB. Om agent versionen är 9,0 och senare, baseras den minsta fil storlek som stöds på fil systemets kluster storlek (kluster storlek med dubbel fil system). Om fil systemets kluster storlek till exempel är 4kb, är den minsta fil storleken 8 KB. |
| 0x80c83007 | – 2134364153 | ECS_E_STORAGE_ERROR | Filen kunde inte skiktas på grund av ett problem med Azure Storage. | Om felet kvarstår öppnar du en support förfrågan. |
| 0x800703e3 | – 2147023901 | ERROR_OPERATION_ABORTED | Filen kunde inte skiktas på grund av att den har återkallats på samma tidpunkt. | Ingen åtgärd krävs. Filen kommer att skiktas när återställningen är klar och filen inte längre används. |
| 0x80c80264 | – 2134375836 | ECS_E_GHOSTING_FILE_NOT_SYNCED | Filen kunde inte skiktas eftersom den inte har synkroniserats med Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att-nivå när den har synkroniserats till Azure-filresursen. |
| 0x80070001 | – 2147942401 | ERROR_INVALID_FUNCTION | Filen kunde inte skiktas eftersom driv rutinen för moln skikts filtret (storagesync.sys) inte körs. | Lös problemet genom att öppna en upphöjd kommando tolk och köra följande kommando: `fltmc load storagesync`<br>Om storagesync filter driv rutinen inte kan läsas in när du kör in FLTMC-kommandot avinstallerar du Azure File Sync agenten, startar om servern och installerar om Azure File Sync agenten. |
| 0x80070070 | – 2147024784 | ERROR_DISK_FULL | Filen kunde inte skiktas på grund av otillräckligt disk utrymme på den volym där Server slut punkten finns. | Lös problemet genom att frigöra minst 100 MB disk utrymme på den volym där Server slut punkten finns. |
| 0x80070490 | – 2147023728 | ERROR_NOT_FOUND | Filen kunde inte skiktas eftersom den inte har synkroniserats med Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att-nivå när den har synkroniserats till Azure-filresursen. |
| 0x80c80262 | – 2134375838 | ECS_E_GHOSTING_UNSUPPORTED_RP | Filen kunde inte skiktas eftersom den är en referens punkt som inte stöds. | Om filen är en referens punkt för datadeduplicering följer du stegen i [planerings guiden](./storage-sync-files-planning.md#data-deduplication) för att aktivera stöd för datadeduplicering. Filer med andra referens punkter än datadeduplicering stöds inte och kommer inte att skiktas.  |
| 0x80c83052 | – 2134364078 | ECS_E_CREATE_SV_STREAM_ID_MISMATCH | Filen kunde inte skiktas eftersom den har ändrats. | Ingen åtgärd krävs. Filen kommer att skiktas när den ändrade filen har synkroniserats med Azure-filresursen. |
| 0x80c80269 | – 2134375831 | ECS_E_GHOSTING_REPLICA_NOT_FOUND | Filen kunde inte skiktas eftersom den inte har synkroniserats med Azure-filresursen. | Ingen åtgärd krävs. Filen kommer att-nivå när den har synkroniserats till Azure-filresursen. |
| 0x80072ee2 | – 2147012894 | WININET_E_TIMEOUT | Filen kunde inte skiktas på grund av ett nätverks problem. | Ingen åtgärd krävs. Om felet kvarstår kontrollerar du nätverks anslutningen till Azure-filresursen. |
| 0x80c80017 | – 2134376425 | ECS_E_SYNC_OPLOCK_BROKEN | Filen kunde inte skiktas eftersom den har ändrats. | Ingen åtgärd krävs. Filen kommer att skiktas när den ändrade filen har synkroniserats med Azure-filresursen. |
| 0x800705aa | – 2147023446 | ERROR_NO_SYSTEM_RESOURCES | Filen kunde inte skiktas på grund av otillräckliga system resurser. | Om felet kvarstår bör du undersöka vilken program-eller kernel-lägesinstallation som förbrukar system resurser. |
| 0x8e5e03fe | – 1906441218 | JET_errDiskIO | Filen kunde inte skiktas på grund av ett I/O-fel vid skrivning till moln skikts databasen. | Om felet kvarstår kan du köra Chkdsk på volymen och kontrol lera lagrings maskin varan. |
| 0x8e5e0442 | – 1906441150 | JET_errInstanceUnavailable | Filen kunde inte skiktas eftersom moln skikts databasen inte körs. | Lös problemet genom att starta om FileSyncSvc-tjänsten eller servern. Om felet kvarstår kan du köra Chkdsk på volymen och kontrol lera lagrings maskin varan. |



### <a name="how-to-troubleshoot-files-that-fail-to-be-recalled"></a>Så här felsöker du filer som inte kan återkallas  
Om filerna inte kan återkallas:
1. I Loggboken granskar du händelse loggarna för telemetri, drift och diagnostik, som finns under program och Services\Microsoft\FileSync\Agent.
    1. Kontrol lera att filerna finns i Azure-filresursen.
    2. Kontrol lera att servern är ansluten till Internet. 
    3. Öppna snapin-modulen tjänster i MMC och kontrol lera att FileSyncSvc (Storage Sync Agent Service) körs.
    4. Verifiera Azure File Sync filter driv rutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
        - Kör i en upphöjd kommando tolk `fltmc` . Kontrol lera att driv rutinerna för StorageSync.sys och StorageSyncGuard.sys fil system filter visas.

> [!NOTE]
> Händelse-ID 9006 loggas en gång per timme i händelse loggen för telemetri om en fil inte kan återkallas (en händelse loggas per felkod). Kontrol lera avsnittet [återkalla fel och reparation](#recall-errors-and-remediation) för att se om reparations stegen visas för felkoden.

### <a name="recall-errors-and-remediation"></a>Återkalla fel och reparation

| HRESULT | HRESULT (decimal) | Felsträng | Problem | Åtgärder |
|---------|-------------------|--------------|-------|-------------|
| 0x80070079 | – 2147942521 | ERROR_SEM_TIMEOUT | Det gick inte att återkalla filen på grund av en I/O-tidsgräns. Det här problemet kan bero på flera orsaker: begränsningar för server resurser, dålig nätverks anslutning eller ett problem med Azure Storage (till exempel begränsning). | Ingen åtgärd krävs. Om felet kvarstår i flera timmar öppnar du ett supportärende. |
| 0x80070036 | – 2147024842 | ERROR_NETWORK_BUSY | Det gick inte att återkalla filen på grund av ett nätverks problem.  | Om felet kvarstår kontrollerar du nätverks anslutningen till Azure-filresursen. |
| 0x80c80037 | – 2134376393 | ECS_E_SYNC_SHARE_NOT_FOUND | Det gick inte att återkalla filen eftersom Server slut punkten har tagits bort. | Information om hur du löser det här problemet finns i [skiktade filer är inte tillgängliga på servern när du har tagit bort en server slut punkt](?tabs=portal1%252cazure-portal#tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint). |
| 0x80070005 | – 2147024891 | ERROR_ACCESS_DENIED | Det gick inte att återkalla filen på grund av ett nekat åtkomst fel. Det här problemet kan uppstå om inställningarna för brand väggen och det virtuella nätverket på lagrings kontot är aktiverade och servern inte har åtkomst till lagrings kontot. | Lös problemet genom att lägga till serverns IP-adress eller virtuella nätverk genom att följa stegen som beskrivs i avsnittet [Konfigurera brand vägg och inställningar för virtuella nätverk](./storage-sync-files-deployment-guide.md?tabs=azure-portal#configure-firewall-and-virtual-network-settings) i distributions guiden. |
| 0x80c86002 | – 2134351870 | ECS_E_AZURE_RESOURCE_NOT_FOUND | Det gick inte att återkalla filen eftersom den inte är tillgänglig i Azure-filresursen. | Lös problemet genom att kontrol lera att filen finns i Azure-filresursen. Om filen finns i Azure-filresursen uppgraderar du till den senaste versionen av Azure File Sync [agent](./storage-files-release-notes.md#supported-versions). |
| 0x80c8305f | – 2134364065 | ECS_E_EXTERNAL_STORAGE_ACCOUNT_AUTHORIZATION_FAILED | Det gick inte att återkalla filen på grund av ett auktoriseringsfel till lagrings kontot. | Lös problemet genom att kontrol lera [Azure File Sync har åtkomst till lagrings kontot](?tabs=portal1%252cazure-portal#troubleshoot-rbac). |
| 0x80c86030 | – 2134351824 | ECS_E_AZURE_FILE_SHARE_NOT_FOUND | Det gick inte att återkalla filen eftersom Azure-filresursen inte är tillgänglig. | Kontrol lera att fil resursen finns och att den är tillgänglig. Om fil resursen har tagits bort och återskapats utför du stegen som dokumenteras i [synkroniseringen eftersom Azure-filresursen togs bort och återskapas](?tabs=portal1%252cazure-portal#-2134375810) för att ta bort och återskapa synkroniseringsresursen. |
| 0x800705aa | – 2147023446 | ERROR_NO_SYSTEM_RESOURCES | Det gick inte att återkalla filen på grund av otillräckliga system resurser. | Om felet kvarstår bör du undersöka vilken program-eller kernel-lägesinstallation som förbrukar system resurser. |
| 0x8007000e | – 2147024882 | ERROR_OUTOFMEMORY | Det gick inte att återkalla filen på grund av otillräckligt minne. | Om felet kvarstår bör du undersöka vilken program-eller kernel-läge driv rutin som orsakar det låga minnes tillståndet. |
| 0x80070070 | – 2147024784 | ERROR_DISK_FULL | Filen kunde inte återkallas på grund av otillräckligt disk utrymme. | Lös problemet genom att frigöra utrymme på volymen genom att flytta filer till en annan volym, öka storleken på volymen eller tvinga filer till nivån genom att använda Invoke-StorageSyncCloudTiering cmdlet. |

### <a name="tiered-files-are-not-accessible-on-the-server-after-deleting-a-server-endpoint"></a>Nivåindelade filer är inte tillgängliga på servern efter att en serverslutpunkt har tagits bort
Nivåbaserade filer på en server blir otillgängliga om filerna inte återkallas innan du tar bort en server slut punkt.

Fel loggas om det inte går att komma åt filer på nivån
- Vid synkronisering av en fil loggas felkoden-2147942467 (0x80070043-ERROR_BAD_NET_NAME) i händelse loggen ItemResults
- Vid återställning av en fil loggas felkoden-2134376393 (0x80c80037-ECS_E_SYNC_SHARE_NOT_FOUND) i händelse loggen RecallResults

Du kan återställa åtkomsten till dina nivåindelade filer om följande villkor uppfylls:
- Serverslutpunkten har tagits bort inom de senaste 30 dagarna
- Molnslutpunkten har inte tagits bort 
- Filresursen har inte tagits bort
- Sync-gruppen har inte tagits bort

Om ovanstående villkor är uppfyllda kan du återställa åtkomsten till filerna på servern genom att återskapa serverslutpunkten på samma sökväg på servern inom samma Sync-grupp inom 30 dagar. 

Om ovanstående villkor inte uppfylls, kan du inte återställa åtkomsten, eftersom dessa nivåindelade filer nu är överblivna på servern. Följ anvisningarna nedan om du vill ta bort de överblivna filernas nivå.

**Kommentarer**
- Om det inte går att komma åt filer på nivån på servern, bör den fullständiga filen fortfarande vara tillgänglig om du får åtkomst till Azure-filresursen direkt.
- Om du vill förhindra överblivna filer i framtiden följer du stegen som beskrivs i [ta bort en server slut punkt](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint) när du tar bort en server slut punkt.

<a id="get-orphaned"></a>**Hämta listan över överblivna filer på nivån** 

1. Verifiera att Azure File Sync agent version v 5.1 eller senare är installerad.
2. Kör följande PowerShell-kommandon för att visa en lista över överblivna filer på nivån:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Spara OrphanTieredFiles.txt utdatafilen i Case-filer måste återställas från en säkerhets kopia när de har tagits bort.

<a id="remove-orphaned"></a>**Ta bort överblivna filer på nivån** 

*Alternativ 1: ta bort de överblivna filerna*

Det här alternativet tar bort de överblivna filer som finns på Windows Server, men kräver att Server slut punkten tas bort om den finns på grund av fritid efter en fritid efter 30 dagar eller är ansluten till en annan Sync-grupp. Fil konflikter uppstår om filer uppdateras på Windows Server eller Azure-filresursen innan Server slut punkten återskapas.

1. Verifiera att Azure File Sync agent version v 5.1 eller senare är installerad.
2. Säkerhetskopiera Azure-filresursen och Server slut punkts platsen.
3. Ta bort Server slut punkten i Sync-gruppen (om den finns) genom att följa stegen som beskrivs i [ta bort en server slut punkt](./storage-sync-files-server-endpoint.md#remove-a-server-endpoint).

> [!Warning]  
> Om Server slut punkten inte tas bort innan du använder Remove-StorageSyncOrphanedTieredFiles-cmdlet: en tar bort den överblivna skikt filen på servern, tas den fullständiga filen bort i Azure-filresursen. 

4. Kör följande PowerShell-kommandon för att visa en lista över överblivna filer på nivån:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
5. Spara OrphanTieredFiles.txt utdatafilen i Case-filer måste återställas från en säkerhets kopia när de har tagits bort.
6. Kör följande PowerShell-kommandon för att ta bort överblivna filer på nivån:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFilesRemoved = Remove-StorageSyncOrphanedTieredFiles -Path <folder path containing orphaned tiered files> -Verbose
$orphanFilesRemoved.OrphanedTieredFiles > DeletedOrphanFiles.txt
```
**Kommentarer** 
- Skiktade filer som har ändrats på den server som inte är synkroniserade med Azure-filresursen tas bort.
- Skiktade filer som är tillgängliga (inte föräldralös) tas inte bort.
- Filer som inte är på nivån finns kvar på servern.

7. Valfritt: återskapa Server slut punkten om den tas bort i steg 3.

*Alternativ 2: montera Azure-filresursen och kopiera filerna lokalt som är överblivna på servern*

Det här alternativet kräver inte att Server slut punkten tas bort men det krävs tillräckligt med disk utrymme för att kopiera de fullständiga filerna lokalt.

1. [Montera](./storage-how-to-use-files-windows.md) Azure-filresursen på Windows-servern som har överblivna filer på nivån.
2. Kör följande PowerShell-kommandon för att visa en lista över överblivna filer på nivån:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
$orphanFiles = Get-StorageSyncOrphanedTieredFiles -path <server endpoint path>
$orphanFiles.OrphanedTieredFiles > OrphanTieredFiles.txt
```
3. Använd OrphanTieredFiles.txt-utdatafilen för att identifiera överblivna filer på servern.
4. Skriv över de överblivna filerna på nivån genom att kopiera den fullständiga filen från Azure-filresursen till Windows Server.

### <a name="how-to-troubleshoot-files-unexpectedly-recalled-on-a-server"></a>Så här felsöker du filer som återkallats på en server  
Antivirus program, säkerhets kopiering och andra program som läser ett stort antal filer orsakar oönskade återanrop om de inte respekterar attributet Skip offline och hoppar över att läsa innehållet i dessa filer. Genom att hoppa över filer för produkter som stöder det här alternativet kan oönskad återkallning undvikas i samband med aktiviteter som virusgenomsökningar eller säkerhetskopieringsjobb.

Kontakta programleverantören för information om hur du konfigurerar deras lösning så att offlinefiler ignoreras.

Oavsiktliga återanrop kan också uppstå i andra scenarier, t. ex. När du bläddrar i filer i Utforskaren. Om en mapp med filer på molnnivå öppnas i Utforskaren på servern kan det leda till oväntad återkallning. Det är ännu mer troligt om en antiviruslösning är aktiverad på servern.

> [!NOTE]
>Använd händelse-ID 9059 i händelse loggen för telemetri för att avgöra vilka program som orsakar återanrop. Den här händelsen ger åter användning av program för en server slut punkt och loggas en gång i timmen.

### <a name="tls-12-required-for-azure-file-sync"></a>TLS 1,2 krävs för Azure File Sync

Du kan visa TLS-inställningarna på servern genom att titta på [register inställningarna](/windows-server/security/tls/tls-registry-settings). 

Om du använder en proxyserver kan du läsa dokumentationen för proxyn och se till att den är konfigurerad att använda TLS 1.2.

## <a name="general-troubleshooting"></a>Allmän fel sökning
Om du stöter på problem med Azure File Sync på en server börjar du med att utföra följande steg:
1. I Loggboken granskar du händelse loggarna för telemetri, drift och diagnostik.
    - Synkroniserings-, nivå-och återställnings problem loggas i händelse loggarna för telemetri, diagnostik och drift under program och Services\Microsoft\FileSync\Agent.
    - Problem som rör hantering av en server (t. ex. konfigurations inställningar) loggas i händelse loggarna för drift och diagnostik under program och Services\Microsoft\FileSync\Management.
2. Kontrol lera att tjänsten Azure File Sync körs på servern:
    - Öppna snapin-modulen tjänster i MMC och kontrol lera att tjänsten Storage Sync agent (FileSyncSvc) körs.
3. Verifiera Azure File Sync filter driv rutiner (StorageSync.sys och StorageSyncGuard.sys) körs:
    - Kör i en upphöjd kommando tolk `fltmc` . Kontrol lera att driv rutinerna för StorageSync.sys och StorageSyncGuard.sys fil system filter visas.

Om problemet inte är löst kör du AFSDiag-verktyget och skickar dess zip-fil till support teknikern som har tilldelats ditt ärende för ytterligare diagnos.

Utför stegen nedan för att köra AFSDiag.

För agent version V11 och senare:
1. Öppna ett upphöjt PowerShell-fönster och kör sedan följande kommandon (tryck på RETUR efter varje kommando):

    > [!NOTE]
    >AFSDiag skapar utdata-katalogen och en tillfällig mapp i den innan loggar samlas in och tar bort Temp-mappen efter körning. Ange en utmatnings plats som inte innehåller data.
    
    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-AFS -OutputDirectory C:\output -KernelModeTraceLevel Verbose -UserModeTraceLevel Verbose
    ```

2. Återskapa problemet. När du är klar anger du **D**.
3. En. zip-fil som innehåller loggar och spårningsfiler sparas i den utgående katalogen som du har angett. 

För agent version v10 och tidigare:
1. Skapa en katalog där AFSDiag-utdata ska sparas (till exempel C:\Output).
    > [!NOTE]
    >AFSDiag tar bort allt innehåll i utdatakatalogen innan loggar samlas in. Ange en utmatnings plats som inte innehåller data.
2. Öppna ett upphöjt PowerShell-fönster och kör sedan följande kommandon (tryck på RETUR efter varje kommando):

    ```powershell
    cd "c:\Program Files\Azure\StorageSyncAgent"
    Import-Module .\afsdiag.ps1
    Debug-Afs c:\output # Note: Use the path created in step 1.
    ```

3. För spårnings nivån Azure File Sync kernelläge anger du **1** (om inget annat anges för att skapa mer utförliga spår) och trycker sedan på RETUR.
4. För spårnings nivån i Azure File Sync-användarläge anger du **1** (om inget annat anges för att skapa mer utförliga spår) och trycker sedan på RETUR.
5. Återskapa problemet. När du är klar anger du **D**.
6. En. zip-fil som innehåller loggar och spårningsfiler sparas i den utgående katalogen som du har angett.


## <a name="see-also"></a>Se även
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
- [Vanliga frågor och svar om Azure Files](storage-files-faq.md)
- [Felsöka Azure Files-problem i Windows](storage-troubleshoot-windows-file-connection-problems.md)
- [Felsöka Azure Files problem i Linux](storage-troubleshoot-linux-file-connection-problems.md)
