---
title: Hantera registrerade servrar med Azure File Sync | Microsoft Docs
description: Lär dig hur du registrerar och avregistrerar en Windows-Server med en Azure File Sync Storage Sync-tjänst.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 194b0f2ff94197fe11c189e97dbc65c9d0367932
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96013929"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Hantera registrerade servrar med Azure File Sync
Med Azure File Sync kan du centralisera din organisations filresurser i Azure Files med samma flexibilitet, prestanda och kompatibilitet som du får om du använder en lokal filserver. Det gör du genom att omvandla dina Windows-servrar till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt (inklusive SMB, NFS och FTPS) och du kan ha så många cacheminnen som du behöver över hela världen.

Följande artikel visar hur du registrerar och hanterar en server med en tjänst för synkronisering av lagring. Information om hur du distribuerar Azure File Sync slut punkt till slut punkt finns i [distribuera Azure File Sync](storage-sync-files-deployment-guide.md) .

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrera/avregistrera en server med tjänsten Storage Sync
När du registrerar en server med Azure File Sync upprättas en förtroende relation mellan Windows Server och Azure. Den här relationen kan sedan användas för att skapa *Server slut punkter* på servern som representerar vissa mappar som ska synkroniseras med en Azure-filresurs (kallas även för en *moln slut punkt*). 

### <a name="prerequisites"></a>Förutsättningar
Om du vill registrera en server med en lagrings tjänst för synkronisering måste du först förbereda servern med nödvändiga komponenter:

* Servern måste köra en version av Windows Server som stöds. Mer information finns i [Azure File Sync system krav och interoperabilitet](storage-sync-files-planning.md#windows-file-server-considerations).
* Se till att en tjänst för synkronisering av lagring har distribuerats. Mer information om hur du distribuerar en tjänst för synkronisering av lagring finns i [så här distribuerar du Azure File Sync](storage-sync-files-deployment-guide.md).
* Kontrol lera att servern är ansluten till Internet och att Azure är tillgängligt.
* Inaktivera Förbättrad säkerhets konfiguration i IE för administratörer med Serverhanteraren användar gränssnitt.
    
    ![Serverhanteraren användar gränssnitt med förbättrad säkerhets konfiguration i IE markerat](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Se till att Azure PowerShell-modulen är installerad på servern. Om din server är medlem i ett redundanskluster kräver varje nod i klustret AZ-modulen. Mer information om hur du installerar AZ-modulen finns på sidan [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

    > [!Note]  
    > Vi rekommenderar att du använder den senaste versionen av AZ PowerShell-modulen för att registrera/avregistrera en server. Om AZ-paketet redan har installerats på den här servern (och PowerShell-versionen på den här servern är 5. * eller högre) kan du använda `Update-Module` cmdleten för att uppdatera det här paketet. 
* Om du använder en nätverks-proxyserver i din miljö konfigurerar du proxyinställningar på servern så att den synkroniserade agenten kan använda den.
    1. Ta reda på din IP-adress och port nummer för proxyservern
    2. Redigera dessa två filer:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Lägg till raderna i bild 1 (under det här avsnittet) under/System.ServiceModel i ovanstående två filer som ändrar 127.0.0.1:8888 till rätt IP-adress (Ersätt 127.0.0.1) och rätt port nummer (Ersätt 8888):
    4. Ange inställningar för WinHTTP-proxy via kommando raden:
        * Visa proxy: netsh WinHTTP show proxy
        * Ange proxy: netsh WinHTTP set proxy 127.0.0.1:8888
        * Återställ proxyn: netsh WinHTTP reset proxy
        * om detta är konfigurerat efter att agenten har installerats startar du om vår Sync-agent: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrera en server med tjänsten Storage Sync
Innan en server kan användas som en *Server slut punkt* i en Azure File Sync *Sync-grupp* måste den registreras med en *tjänst för synkronisering av lagring*. En server kan bara registreras med en lagrings tjänst för synkronisering i taget.

#### <a name="install-the-azure-file-sync-agent"></a>Installera Azure File Sync-agenten
1. [Ladda ned Azure File Sync agenten](https://go.microsoft.com/fwlink/?linkid=858257).
2. Starta installations programmet för Azure File Sync-agenten.
    
    ![Det första fönstret i installations programmet för Azure File Sync agent](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Se till att aktivera uppdateringar av Azure File Sync agenten med Microsoft Update. Det är viktigt att viktiga säkerhets korrigeringar och funktions förbättringar i server paketet levereras via Microsoft Update.

    ![Se till att Microsoft Update är aktiverat i fönstret Microsoft Update i installations programmet för Azure File Sync agent](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Om servern inte har registrerats tidigare kommer Server registrerings gränssnittet att visas direkt efter att installationen har slutförts.

> [!Important]  
> Om servern är medlem i ett redundanskluster måste den Azure File Sync agenten installeras på varje nod i klustret.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrera servern med hjälp av användar gränssnittet för Server registrering
1. Om användar gränssnittet för Server registrering inte startade direkt efter att installationen av Azure File Sync-agenten har slutförts kan du starta den manuellt genom att köra `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` .
2. Klicka på *Logga in* för att få åtkomst till din Azure-prenumeration. 

    ![Öppnar dialog rutan för Server registrerings gränssnittet](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Välj rätt prenumeration, resurs grupp och synkroniseringstjänst för lagring i dialog rutan.

    ![Information om Storage Sync-tjänsten](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. I för hands versionen krävs en mer inloggning för att slutföra processen. 

    ![Dialog rutan logga in](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Om servern är medlem i ett redundanskluster måste varje server köra Server registreringen. När du visar registrerade servrar i Azure-portalen identifierar Azure File Sync automatiskt varje nod som en medlem i samma redundanskluster och grupperar dem tillsammans.

#### <a name="register-the-server-with-powershell"></a>Registrera servern med PowerShell
Du kan också utföra Server registrering via PowerShell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Avregistrera servern med tjänsten Storage Sync
Det finns flera steg som krävs för att avregistrera en server med en tjänst för synkronisering av lagring. Låt oss ta en titt på hur du avregistrerar en server korrekt.

> [!Warning]  
> Försök inte att felsöka problem med synkronisering, moln nivåer eller någon annan aspekt av Azure File Sync genom att avregistrera och registrera en server, eller ta bort och återskapa Server slut punkterna, om inget annat uttryckligen anges av en Microsoft-tekniker. Att avregistrera en server och ta bort Server slut punkter är en destruktiv åtgärd och skiktade filer på volymerna med Server slut punkter kommer inte att återanslutas till deras platser på Azure-filresursen efter att den registrerade servern och Server slut punkterna har återskapats, vilket leder till synkroniseringsfel. Observera också att skiktade filer som finns utanför ett Server slut punkts namn område kan gå förlorade permanent. Skiktade filer kan finnas i Server slut punkter även om moln nivåer aldrig har Aktiver ATS.

#### <a name="optional-recall-all-tiered-data"></a>Valfritt Återkalla alla data på nivå
Om du vill att filer som för närvarande är på nivå av ska vara tillgängliga efter att du tagit bort Azure File Sync (d.v.s. det här är en produktion, inte ett test, en miljö), kan du återställa alla filer på varje volym som innehåller Server slut punkter. Inaktivera moln nivåer för alla Server slut punkter och kör sedan följande PowerShell-cmdlet:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Om den lokala volym som är värd för Server slut punkten inte har tillräckligt med ledigt utrymme för att återkalla alla data på nivån, `Invoke-StorageSyncFileRecall` kommer cmdleten att Miss bete.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Ta bort servern från alla Sync-grupper
Innan du avregistrerar servern på synkroniseringstjänsten måste alla Server slut punkter på servern tas bort. Detta kan göras via Azure Portal:

1. Navigera till tjänsten Storage Sync där servern är registrerad.
2. Ta bort alla Server slut punkter för den här servern i varje Sync-grupp i tjänsten för synkronisering av lagring. Detta kan åstadkommas genom att högerklicka på relevant Server slut punkt i fönstret synkronisera grupp.

    ![Ta bort en server slut punkt från en synkroniseringsresurs](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Detta kan också utföras med ett enkelt PowerShell-skript:

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
Nu när alla data har återkallats och servern har tagits bort från alla Sync-grupper kan servern avregistreras. 

1. I Azure Portal navigerar du till avsnittet *registrerade servrar* i tjänsten för synkronisering av lagring.
2. Högerklicka på den server som du vill avregistrera och klicka på "avregistrera servern".

    ![Avregistrera Server](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Att säkerställa Azure File Sync är en lämplig granne i ditt data Center 
Eftersom Azure File Sync sällan är den enda tjänst som körs i ditt data Center, kanske du vill begränsa nätverks-och lagrings användningen för Azure File Sync.

> [!Important]  
> Om du ställer in gränser för låg påverkas prestandan för Azure File Sync synkronisering och återkallande.

### <a name="set-azure-file-sync-network-limits"></a>Ange Azure File Sync nätverks gränser
Du kan begränsa nätverks användningen för Azure File Sync med hjälp av `StorageSyncNetworkLimit` cmdletarna.

> [!Note]  
> Nätverks gränser gäller inte när en fil med flera nivåer nås.

Du kan till exempel skapa en ny begränsnings gräns för att säkerställa att Azure File Sync inte använder mer än 10 Mbit/s mellan 9 am och 5 PM (17:00h) under arbets veckan: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Du kan se din gräns med hjälp av följande cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Använd om du vill ta bort nätverks begränsningar `Remove-StorageSyncNetworkLimit` . Följande kommando tar till exempel bort alla nätverks gränser:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Använd Windows Server Storage QoS 
När Azure File Sync finns på en virtuell dator som körs på en Windows Server-Virtualiseringsvärd, kan du använda QoS för lagring (tjänst kvalitet för lagring) för att reglera lagring i/o-förbrukning. QoS-principen för lagring kan anges antingen som en högsta (eller begränsas, t. ex. hur StorageSyncNetwork-gränsen tillämpas ovan) eller som ett minimum (eller reservation). Om du ställer in ett minimum i stället för maximalt kan Azure File Sync till burst använda tillgänglig lagrings bandbredd om andra arbets belastningar inte använder den. Mer information finns i [tjänst kvalitet för lagring](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Se även
- [Planera för distribution av Azure File Sync](storage-sync-files-planning.md)
- [Distribuera Azure File Sync](storage-sync-files-deployment-guide.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
- [Felsöka Azure File Sync](storage-sync-files-troubleshoot.md)