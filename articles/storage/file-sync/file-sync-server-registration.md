---
title: Hantera registrerade servrar med Azure File Sync | Microsoft Docs
description: Lär dig hur du registrerar och avregistrerar en Windows Server med en Azure File Sync Storage Sync Service.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a85fb653636333beec5f53912a646b3e5619e37b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797227"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Hantera registrerade servrar med Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla Dina Windows-servrar till ett snabbt cacheminne för din Azure-filresurs. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Följande artikel visar hur du registrerar och hanterar en server med en tjänst för synkronisering av lagring. Se [Distribuera Azure File Sync](file-sync-deployment-guide.md) information om hur du distribuerar Azure File Sync från end-to-end.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrera/avregistrera en server med tjänsten för synkronisering av lagring
Registrering av en server med Azure File Sync upprättar en förtroenderelation mellan Windows Server och Azure. Den här relationen kan sedan användas för att skapa serverslutpunkter på servern, som representerar specifika mappar som ska *synkroniseras* med en Azure-filresurs (kallas även *molnslutpunkt).* 

### <a name="prerequisites"></a>Förutsättningar
Om du vill registrera en server med en tjänst för synkronisering av lagring måste du först förbereda servern med nödvändiga krav:

* Servern måste köra en version av Windows Server som stöds. Mer information finns i [Azure File Sync systemkrav och samverkan.](file-sync-planning.md#windows-file-server-considerations)
* Kontrollera att en tjänst för synkronisering av lagring har distribuerats. Mer information om hur du distribuerar en tjänst för synkronisering av lagring finns [i Så här distribuerar du Azure File Sync](file-sync-deployment-guide.md).
* Kontrollera att servern är ansluten till Internet och att Azure är tillgängligt.
* Inaktivera förbättrad säkerhetskonfiguration i IE för administratörer med Serverhanteraren användargränssnitt.
    
    ![Serverhanteraren användargränssnittet med förbättrad säkerhetskonfiguration i IE markerat](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Kontrollera att Azure PowerShell modulen är installerad på servern. Om servern är medlem i ett redundanskluster kräver varje nod i klustret Az-modulen. Mer information om hur du installerar Az-modulen finns i Installera [och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

    > [!Note]  
    > Vi rekommenderar att du använder den senaste versionen av Az PowerShell-modulen för att registrera/avregistrera en server. Om Az-paketet tidigare har installerats på den här servern (och PowerShell-versionen på den här servern är 5.* eller senare) kan du använda cmdleten för att uppdatera det här `Update-Module` paketet. 
* Om du använder en nätverksproxyserver i din miljö konfigurerar du proxyinställningarna på servern så att synkroniseringsagenten kan använda den.
    1. Fastställ din proxy-IP-adress och portnummer
    2. Redigera dessa två filer:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Lägg till raderna i bild 1 (under det här avsnittet) under /System.ServiceModel i de två filerna ovan som ändrar 127.0.0.1:8888 till rätt IP-adress (ersätt 127.0.0.1) och rätt portnummer (ersätt 8888):
    4. Ange WinHTTP-proxyinställningarna via kommandoraden:
        * Visa proxyn: netsh winhttp show proxy
        * Ange proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Återställ proxyn: netsh winhttp reset proxy
        * Om detta har installerats efter att agenten har installerats startar du om synkroniseringsagenten: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrera en server med tjänsten för synkronisering av lagring
Innan en server kan användas som en *serverslutpunkt* i en Azure File Sync *synkroniseringsgrupp* måste den registreras med en tjänst för synkronisering *av lagring.* En server kan bara registreras med en tjänst för synkronisering av lagring i taget.

#### <a name="install-the-azure-file-sync-agent"></a>Installera Azure File Sync-agenten
1. [Ladda ned Azure File Sync agenten](https://go.microsoft.com/fwlink/?linkid=858257).
2. Starta installationsprogrammet Azure File Sync agenten.
    
    ![Det första fönstret i Azure File Sync agentinstallationsprogrammet](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Se till att aktivera uppdateringar Azure File Sync agenten med Microsoft Update. Det är viktigt eftersom kritiska säkerhetskorrigeringar och funktionsförbättringar av serverpaketet levereras via Microsoft Update.

    ![Kontrollera Microsoft Update är aktiverat i Microsoft Update i installationsprogrammet Azure File Sync agenten](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Om servern inte har registrerats tidigare visas användargränssnittet för serverregistrering omedelbart efter att installationen har slutförts.

> [!Important]  
> Om servern är medlem i ett redundanskluster måste Azure File Sync-agenten installeras på varje nod i klustret.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrera servern med hjälp av användargränssnittet för serverregistrering
1. Om användargränssnittet för serverregistrering inte startade omedelbart efter att installationen av Azure File Sync agenten kan den startas manuellt genom att köra `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` .
2. Klicka på Logga in för att få åtkomst till din *Azure-prenumeration.* 

    ![Dialogrutan Öppnas i användargränssnittet för serverregistrering](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Välj rätt prenumeration, resursgrupp och tjänst för synkronisering av lagring i dialogrutan.

    ![Tjänstinformation för synkronisering av lagring](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. I förhandsversionen krävs ytterligare en inloggning för att slutföra processen. 

    ![Dialogrutan Logga in](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Om servern är medlem i ett redundanskluster måste varje server köra Serverregistrering. När du visar de registrerade servrarna i Azure Portal Azure File Sync automatiskt varje nod som medlem i samma redundanskluster och grupperar dem på lämpligt sätt.

#### <a name="register-the-server-with-powershell"></a>Registrera servern med PowerShell
Du kan också utföra serverregistrering via PowerShell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Avregistrera servern med tjänsten för synkronisering av lagring
Det finns flera steg som krävs för att avregistrera en server med en tjänst för synkronisering av lagring. Låt oss ta en titt på hur du avregistrerar en server på rätt sätt.

> [!Warning]  
> Försök inte att felsöka problem med synkronisering, molnnivåindelad lagring eller någon annan aspekt av Azure File Sync genom att avregistrera och registrera en server, eller ta bort och återskapa serverslutpunkterna om du inte uttryckligen uppmanas av en Microsoft-tekniker. Att avregistrera en server och ta bort serverslutpunkter är en destruktiv åtgärd, och nivåindelade filer på volymerna med serverslutpunkter "återansluts" inte till deras platser på Azure-filresursen efter att de registrerade server- och serverslutpunkterna har återskapats, vilket resulterar i synkroniseringsfel. Observera också att nivåindelade filer som finns utanför en serverslutpunkts namnrymd kan förloras permanent. Nivåindelade filer kan finnas i serverslutpunkter även om molnnivåindelad lagring aldrig har aktiverats.

#### <a name="optional-recall-all-tiered-data"></a>(Valfritt) Återkalla alla nivåindelade data
Om du vill att filer som för närvarande är nivåindelade ska vara tillgängliga efter borttagning av Azure File Sync (dvs. detta är en produktionsmiljö, inte en testmiljö) kan du återkalla alla filer på varje volym som innehåller serverslutpunkter. Inaktivera molnnivåindelad för alla serverslutpunkter och kör sedan följande PowerShell-cmdlet:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Om den lokala volymen som är värd för serverslutpunkten inte har tillräckligt med ledigt utrymme för att återkalla alla nivåindelade data, misslyckas `Invoke-StorageSyncFileRecall` cmdleten.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Ta bort servern från alla synkroniseringsgrupper
Innan du avregistrerar servern på tjänsten för synkronisering av lagring måste alla serverslutpunkter på den servern tas bort. Detta kan göras via Azure Portal:

1. Gå till tjänsten för synkronisering av lagring där servern är registrerad.
2. Ta bort alla serverslutpunkter för den här servern i varje synkroniseringsgrupp i tjänsten för synkronisering av lagring. Detta kan åstadkommas genom att högerklicka på relevant serverslutpunkt i fönstret synkroniseringsgrupp.

    ![Ta bort en serverslutpunkt från en synkroniseringsgrupp](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Detta kan också åstadkommas med ett enkelt PowerShell-skript:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Avregistrera servern
Nu när alla data har återkallats och servern har tagits bort från alla synkroniseringsgrupper kan servern avregistreras. 

1. I Azure Portal går du till avsnittet *Registrerade servrar* i tjänsten för synkronisering av lagring.
2. Högerklicka på den server som du vill avregistrera och klicka på "Avregistrera server".

    ![Avregistrera server](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Se Azure File Sync är en bra granne i ditt datacenter 
Eftersom Azure File Sync sällan är den enda tjänsten som körs i ditt datacenter kanske du vill begränsa nätverks- och lagringsanvändningen för Azure File Sync.

> [!Important]  
> Om du anger för låga gränser påverkas prestandan för Azure File Sync synkronisering och återkallning.

### <a name="set-azure-file-sync-network-limits"></a>Ange Azure File Sync nätverksgränser
Du kan begränsa nätverksanvändningen för Azure File Sync med hjälp av `StorageSyncNetworkLimit` cmdletarna.

> [!Note]  
> Nätverksbegränsningar gäller inte när en nivåindelad fil används.

Du kan till exempel skapa en ny begränsningsgräns för att se till att Azure File Sync inte använder mer än 10 Mbit/s mellan 09.00 och 17.00 (17.00) under arbets veckan: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Du kan se din gräns med hjälp av följande cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Om du vill ta bort nätverksbegränsningar använder du `Remove-StorageSyncNetworkLimit` . Följande kommando tar till exempel bort alla nätverksgränser:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Använda QoS för Windows Server-lagring 
När Azure File Sync finns på en virtuell dator som körs på en Windows Server-virtualiseringsvärd kan du använda QoS för lagring (tjänstens lagringskvalitet) för att reglera lagrings-I/M-förbrukningen. QoS-principen för lagring kan antingen anges som en maxgräns (eller gräns, t.ex. hur StorageSyncNetwork-gränsen tillämpas ovan) eller som ett minimum (eller en reservation). Om du anger ett minimum i stället för ett maximum Azure File Sync att burst-värdet använder tillgänglig lagringsbandbredd om andra arbetsbelastningar inte använder den. Mer information finns i [Tjänstkvalitet för lagring.](/windows-server/storage/storage-qos/storage-qos-overview)

## <a name="see-also"></a>Se även
- [Planera för distribution av Azure File Sync](file-sync-planning.md)
- [Distribuera Azure File Sync](file-sync-deployment-guide.md)
- [Övervaka Azure File Sync](file-sync-monitoring.md)
- [Felsöka Azure File Sync](file-sync-troubleshoot.md)