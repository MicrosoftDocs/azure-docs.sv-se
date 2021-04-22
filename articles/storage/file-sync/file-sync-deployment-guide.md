---
title: Distribuera Azure File Sync | Microsoft Docs
description: Lär dig hur du Azure File Sync, från början till slut, med hjälp av Azure Portal, PowerShell eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 64b9ce78f05e1c8d14317f33f21758a86baeabd6
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869193"
---
# <a name="deploy-azure-file-sync"></a>Distribuera Azure File Sync
Använd Azure File Sync för att centralisera organisationens filresurser i Azure Files, samtidigt som du behåller flexibiliteten, prestandan och kompatibiliteten hos en lokal filserver. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Vi rekommenderar starkt att du läser Planera för [en Azure Files-distribution](../files/storage-files-planning.md) och Planera [för en Azure File Sync-distribution](file-sync-planning.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. En Azure-filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regionstillgänglighet](file-sync-planning.md#azure-file-sync-region-availability) för Azure File Sync.
    - [Skapa en filresurs](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) för en stegvis beskrivning av hur du skapar en filresurs.
1. Minst en instans av Windows Server eller Windows Server-kluster som stöds för synkronisering med Azure File Sync. Mer information om versioner av Windows Server som stöds och rekommenderade systemresurser finns i [Windows-filserveröverväganden.](file-sync-planning.md#windows-file-server-considerations)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. En Azure-filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regionstillgänglighet](file-sync-planning.md#azure-file-sync-region-availability) för Azure File Sync.
    - [Skapa en filresurs](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) för en stegvis beskrivning av hur du skapar en filresurs.
1. Minst en instans av Windows Server eller Windows Server-kluster som stöds för synkronisering med Azure File Sync. Mer information om versioner av Windows Server som stöds och rekommenderade systemresurser finns i [Windows-filserveröverväganden.](file-sync-planning.md#windows-file-server-considerations)

1. Az PowerShell-modulen kan användas med antingen PowerShell 5.1 eller PowerShell 6+. Du kan använda Az PowerShell-modulen för Azure File Sync på alla system som stöds, inklusive system som inte är Windows-system, men cmdleten för serverregistrering måste alltid köras på den Windows Server-instans som du registrerar (detta kan göras direkt eller via PowerShell-fjärrkommunikation). I Windows Server 2012 R2 kan du kontrollera att du kör minst PowerShell 5.1. \* genom att titta på värdet för **PSVersion-egenskapen** för **$PSVersionTable objekt:**

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Om **ditt PSVersion-värde** är mindre än 5.1. , vilket är fallet med de flesta nya installationer av Windows Server 2012 R2, kan du enkelt uppgradera genom att ladda ned och installera \* [Windows Management Framework (WMF) 5.1.](https://www.microsoft.com/download/details.aspx?id=54616) Lämpligt paket att ladda ned och installera för Windows Server 2012 R2 är **Win8.1AndW2K12R2-KB \* \* \* \* \* \* \* -x64.msu**. 

    PowerShell 6+ kan användas med alla system som stöds och kan laddas ned via dess [GitHub-sida.](https://github.com/PowerShell/PowerShell#get-powershell) 

    > [!Important]  
    > Om du planerar att använda användargränssnittet för serverregistrering i stället för att registrera dig direkt från PowerShell måste du använda PowerShell 5.1.

1. Om du har valt att använda PowerShell 5.1 måste du kontrollera att minst .NET 4.7.2 är installerat. Läs mer [om .NET Framework och beroenden](/dotnet/framework/migration-guide/versions-and-dependencies) i systemet.

    > [!Important]  
    > Om du installerar .NET 4.7.2+ på Windows Server Core måste du installera med flaggorna och , annars `quiet` `norestart` misslyckas installationen. Om du till exempel installerar .NET 4.8 skulle kommandot se ut så här:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. Az PowerShell-modulen, som kan installeras genom att följa anvisningarna här: [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Modulen Az.StorageSync installeras nu automatiskt när du installerar Az PowerShell-modulen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. En Azure-filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regionstillgänglighet](file-sync-planning.md#azure-file-sync-region-availability) för Azure File Sync.
    - [Skapa en filresurs](../files/storage-how-to-create-file-share.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json) för en stegvis beskrivning av hur du skapar en filresurs.
1. Minst en instans av Windows Server eller Windows Server-kluster som stöds för synkronisering med Azure File Sync. Mer information om versioner av Windows Server som stöds och rekommenderade systemresurser finns i [Windows-filserveröverväganden.](file-sync-planning.md#windows-file-server-considerations)

1. [Installera Azure CLI](/cli/azure/install-azure-cli)

   Om du vill kan du även använda Azure Cloud Shell för att slutföra stegen i den här självstudien.  Azure Cloud Shell är en interaktiv gränssnittsmiljö som du använder via webbläsaren.  Börja Cloud Shell med någon av följande metoder:

   - Välj **Prova** i det övre högra hörnet av ett kodblock. **Prova Det** öppnas Azure Cloud Shell, men den kopierar inte automatiskt koden till Cloud Shell.

   - Öppna Cloud Shell genom att gå till [https://shell.azure.com](https://shell.azure.com)

   - Välj knappen **Cloud Shell** på menyraden i det övre högra hörnet i [Azure Portal](https://portal.azure.com)

1. Logga in.

   Logga in med kommandot [az login](/cli/azure/reference-index#az_login) om du använder en lokal CLI-installation.

   ```azurecli
   az login
   ```

    Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

1. Installera [Azure CLI-tillägget az filesync.](/cli/azure/storagesync)

   ```azurecli
   az extension add --name storagesync
   ```

   När du har installerat **tilläggsreferensen** för storagesync visas följande varning.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Förbereda Windows Server för användning med Azure File Sync
För varje server som du tänker använda med Azure File Sync, inklusive varje servernod i ett redundanskluster, inaktiverar du Internet Explorer **Förbättrad säkerhetskonfiguration.** Detta krävs endast för inledande serverregistrering. Du kan aktivera det igen när servern har registrerats.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Du kan hoppa över det här steget om du distribuerar Azure File Sync på Windows Server Core.

1. Öppna Serverhanteraren.
2. Klicka **på Lokal server:**  
    ![”Lokal server” till vänster i användargränssnittet för Serverhanteraren](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-1.png)
3. I underfönstret **Egenskaper** väljer du länken för **Förbättrad säkerhetskonfiguration i Internet Explorer**.  
    ![Fönstret ”Förbättrad säkerhetskonfiguration i Internet Explorer” i användargränssnittet i Serverhanteraren](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-2.png)
4. I dialogrutan **Internet Explorer Förbättrad säkerhetskonfiguration** väljer du **Av** för **Administratörer** och **användare:**  
    ![Popup-fönstret Förbättrad säkerhetskonfiguration i Internet Explorer med ”Av” valt](media/storage-sync-files-deployment-guide/prepare-server-disable-ieesc-part-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill Internet Explorer förbättrad säkerhetskonfiguration kör du följande från en upphöjd PowerShell-session:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Administrators
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Disable Internet Explorer Enhanced Security Configuration 
    # for Users
    Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force
    
    # Force Internet Explorer closed, if open. This is required to fully apply the setting.
    # Save any work you have open in the IE browser. This will not affect other browsers,
    # including Microsoft Edge.
    Stop-Process -Name iexplore -ErrorAction SilentlyContinue
}
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Följ anvisningarna för Azure Portal eller PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Distribuera tjänsten för synkronisering av lagring 
Distributionen av Azure File Sync börjar med att placera en **tjänstresurs för synkronisering av** lagring i en resursgrupp för den valda prenumerationen. Vi rekommenderar att du etablerar så få av dessa som behövs. Du skapar en förtroenderelation mellan dina servrar och den här resursen och en server kan bara registreras till en tjänst för synkronisering av lagring. Därför rekommenderar vi att du distribuerar så många tjänster för synkronisering av lagring som du behöver för att separera grupper av servrar. Tänk på att servrar från olika lagringssynkroniseringstjänster inte kan synkroniseras med varandra.

> [!Note]
> Tjänsten för synkronisering av lagring ärver åtkomstbehörigheter från prenumerationen och resursgruppen som den har distribuerats till. Vi rekommenderar att du noggrant kontrollerar vem som har åtkomst till den. Entiteter med skrivåtkomst kan börja synkronisera nya uppsättningar med filer från servrar som är registrerade i den här tjänsten för synkronisering av lagring och orsaka att data flödar till Azure Storage som är tillgängliga för dem.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill distribuera en tjänst för synkronisering  av [lagring går du Azure Portal](https://portal.azure.com/), klickar på Skapa en resurs och söker sedan efter Azure File Sync. I sökresultaten väljer du **Azure File Sync** och sedan Skapa för **att** öppna fliken **Distribuera lagringssynkronisering.**

I fönstret som öppnas anger du följande information:

- **Namn:** Ett unikt namn (per region) för tjänsten för synkronisering av lagring.
- **Prenumeration:** Den prenumeration där du vill skapa tjänsten för synkronisering av lagring. Beroende på organisationens konfigurationsstrategi kan du ha åtkomst till en eller flera prenumerationer. En Azure-prenumeration är den mest grundläggande containern för fakturering för varje molntjänst (till exempel Azure Files).
- **Resursgrupp:** En resursgrupp är en logisk grupp med Azure-resurser, till exempel ett lagringskonto eller en tjänst för synkronisering av lagring. Du kan skapa en ny resursgrupp eller använda en befintlig resursgrupp för Azure File Sync. (Vi rekommenderar att du använder resursgrupper som containrar för att logiskt isolera resurser för din organisation, till exempel gruppering av HR-resurser eller resurser för ett visst projekt.)
- **Plats:** Den region där du vill distribuera Azure File Sync. Endast regioner som stöds är tillgängliga i den här listan.

När du är klar väljer du **Skapa för** att distribuera tjänsten för synkronisering av lagring.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ersätt `<Az_Region>` , och med dina egna värden och använd sedan följande kommandon för att skapa och distribuera en `<RG_Name>` tjänst för synkronisering av `<my_storage_sync_service>` lagring:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Följ anvisningarna för Azure Portal eller PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Installera Azure File Sync-agenten
Azure File Sync-agenten är ett nedladdningsbart paket som möjliggör att Windows Server kan synkroniseras med en Azure-filresurs. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Du kan ladda ned agenten från [Microsoft Download Center.](https://go.microsoft.com/fwlink/?linkid=858257) När nedladdningen är klar dubbelklickar du på MSI-paketet för att starta Azure File Sync agentinstallationen.

> [!Important]  
> Om du tänker använda Azure File Sync med ett redundanskluster måste Azure File Sync-agenten installeras på varje nod i klustret. Varje nod i klustret måste vara registrerad för att fungera med Azure File Sync.

Vi rekommenderar att du gör följande:
- Låt standardinstallationssökvägen (C:\Program Files\Azure\StorageSyncAgent) vara kvar för att förenkla felsökning och serverunderhåll.
- Aktivera Microsoft Update att hålla Azure File Sync uppdaterade. Alla uppdateringar, till Azure File Sync-agenten, inklusive funktionsuppdateringar och snabbkorrigeringar, sker från Microsoft Update. Vi rekommenderar att du installerar den senaste uppdateringen för Azure File Sync. Mer information finns i [Azure File Sync uppdateringsprincip](file-sync-planning.md#azure-file-sync-agent-update-policy).

När Azure File Sync agentinstallationen är klar öppnas användargränssnittet för serverregistrering automatiskt. Du måste ha en tjänst för synkronisering av lagring innan du registrerar dig. se nästa avsnitt om hur du skapar en tjänst för synkronisering av lagring.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell-kod för att ladda ned lämplig version Azure File Sync agenten för ditt operativsystem och installera den på datorn.

> [!Important]  
> Om du tänker använda Azure File Sync med ett redundanskluster måste Azure File Sync-agenten installeras på varje nod i klustret. Varje nod i klustret måste registreras för att fungera med Azure File Sync.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Följ anvisningarna för Azure Portal eller PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registrera Windows Server i tjänsten för synkronisering av lagring
När du registrerar Windows Server med en tjänst för synkronisering av lagring etableras en förtroenderelation mellan din server (eller ditt kluster) och tjänsten för synkronisering av lagring. En server kan bara registreras på en tjänst för synkronisering av lagring och kan synkronisera med andra servrar och Azure-filresurser som associeras med samma tjänst för synkronisering av lagring.

> [!Note]
> Serverregistreringen använder dina Azure-autentiseringsuppgifter för att skapa en förtroenderelation mellan tjänsten för synkronisering av lagring och Windows Server, men därefter skapar och använder servern sin egen identitet som är giltig så länge servern förblir registrerad och den aktuella signaturen för delad åtkomst (Storage SAS) är giltig. En ny SAS-token kan inte utfärdas till servern när servern har avregistrerats, vilket innebär att serverns möjlighet att komma åt dina Azure-filresurser tas bort, vilket stoppar synkroniseringen.

Administratören som registrerar servern måste vara medlem i hanteringsrollerna **Ägare** eller **Deltagare för** den angivna tjänsten för synkronisering av lagring. Detta kan konfigureras under **Access Control (IAM)** i Azure Portal för tjänsten för synkronisering av lagring.

Det är också möjligt att skilja administratörer som kan registrera servrar från de som tillåts att även konfigurera synkronisering i en tjänst för synkronisering av lagring. För detta skulle du behöva skapa en anpassad roll där du listar de administratörer som endast tillåts registrera servrar och ge din anpassade roll följande behörigheter:

* "Microsoft.StorageSync/storageSyncServices/registeredServers/write"
* "Microsoft.StorageSync/storageSyncServices/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/operations/read"

# <a name="portal"></a>[Portal](#tab/azure-portal)
Användargränssnittet för serverregistrering bör öppnas automatiskt efter installationen Azure File Sync agenten. Om det inte gör det kan du öppna det från dess filplats: C:\Program\Azure\StorageSyncAgent\ServerRegistration.exe. När användargränssnittet för serverregistrering öppnas väljer du **Logga in för** att börja.

När du har loggat in uppmanas du att ange följande information:

![Skärmbild av användargränssnittet för serverregistrering](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-prenumeration:** Den prenumeration som innehåller tjänsten för synkronisering av lagring (se [Distribuera tjänsten för synkronisering av lagring).](#deploy-the-storage-sync-service) 
- **Resursgrupp:** Den resursgrupp som innehåller tjänsten för synkronisering av lagring.
- **Tjänst för synkronisering av** lagring: Namnet på den tjänst för synkronisering av lagring som du vill registrera.

När du har valt lämplig information väljer du **Registrera för** att slutföra serverregistreringen. Som en del av registreringsprocessen uppmanas du att logga in en ytterligare inloggning.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Följ anvisningarna för Azure Portal eller PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Skapa en synkroniseringsgrupp och en molnslutpunkt
En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. En synkroniseringsgrupp måste innehålla en molnslutpunkt, som representerar en Azure-filresurs och en eller flera serverslutpunkter. En serverslutpunkt representerar en sökväg på en registrerad server. En server kan ha serverslutpunkter i flera synkroniseringsgrupper. Du kan skapa så många synkroniseringsgrupper som du behöver för att korrekt beskriva din önskade synkroniseringstopologi.

En molnslutpunkt är en pekare till en Azure-filresurs. Alla serverslutpunkter synkroniseras med en molnslutpunkt, vilket gör molnslutpunkten till hubben. Lagringskontot för Azure-filresursen måste finnas i samma region som tjänsten för synkronisering av lagring. Hela Azure-filresursen synkroniseras, med ett undantag: En särskild mapp som är jämförbar med den dolda mappen "System Volume Information" på en NTFS-volym etableras. Den här katalogen heter ". SystemShareInformation". Den innehåller viktiga synkroniseringsmetadata som inte synkroniseras till andra slutpunkter. Använd inte eller ta inte bort det!

> [!Important]  
> Du kan göra ändringar i valfri molnslutpunkt eller serverslutpunkt i synkroniseringsgruppen och synkronisera filerna med de andra slutpunkterna i synkroniseringsgruppen. Om du gör en ändring i molnslutpunkten (Azure-filresursen) direkt måste ändringarna först identifieras av en Azure File Sync-jobb för ändringsidentifiering. Ett ändringsidentifieringsjobb initieras endast för en molnslutpunkt en gång var 24:e timme. Mer information finns i [Azure Files vanliga frågor och svar.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json#afs-change-detection)

Administratören som skapar molnslutpunkten måste vara  medlem i hanteringsrollen Ägare för lagringskontot som innehåller den Azure-filresurs som molnslutpunkten pekar på. Detta kan konfigureras under **Access Control (IAM)** i Azure Portal för lagringskontot.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa en synkroniseringsgrupp [går Azure Portal](https://portal.azure.com/)till din tjänst för synkronisering av lagring och väljer sedan **+ Synkroniseringsgrupp:**

![Skapa en ny synkroniseringsgrupp i Azure-portalen](media/storage-sync-files-deployment-guide/create-sync-group-part-1.png)

I fönstret som öppnas anger du följande information för att skapa en synkroniseringsgrupp med en molnslutpunkt:

- **Namn på synkroniseringsgrupp:** Namnet på synkroniseringsgruppen som ska skapas. Det här namnet måste vara unikt i tjänsten för synkronisering av lagring men kan vara vilket namn som helst som är logiskt för dig.
- **Prenumeration:** Prenumerationen där du distribuerade tjänsten för synkronisering av lagring i [Distribuera tjänsten för synkronisering av lagring.](#deploy-the-storage-sync-service)
- **Lagringskonto:** Om du väljer Välj lagringskonto **visas** ett annat fönster där du kan välja det lagringskonto som har den Azure-filresurs som du vill synkronisera med.
- **Azure-filresurs:** Namnet på den Azure-filresurs som du vill synkronisera med.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell för att skapa synkroniseringsgruppen. Kom ihåg att `<my-sync-group>` ersätta med det önskade namnet på synkroniseringsgruppen.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

När synkroniseringsgruppen har skapats kan du skapa molnslutpunkten. Se till att ersätta `<my-storage-account>` och med de förväntade `<my-file-share>` värdena.

```powershell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az storagesync sync-group](/cli/azure/storagesync/sync-group#az_storagesync_sync_group_create) för att skapa en ny synkroniseringsgrupp.  Använd az configure som standard en resursgrupp för alla [CLI-kommandon.](/cli/azure/reference-index#az_configure)

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Använd kommandot [az storagesync sync-group cloud-endpoint](/cli/azure/storagesync/sync-group/cloud-endpoint#az_storagesync_sync_group_cloud_endpoint_create) för att skapa en ny molnslutpunkt.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Skapa en serverslutpunkt
En serverslutpunkt representerar en viss plats på en registrerad server, till exempel en mapp på en servervolym. En serverslutpunkt omfattas av följande villkor:

- En serverslutpunkt måste vara en sökväg på en registrerad server (i stället för en monterad resurs). NAS (Network Attached Storage) stöds inte.
- Serverslutpunkten kan finnas på systemvolymen, men serverslutpunkter på systemvolymen kanske inte använder molnnivåindelad lagring.
- Det går inte att ändra sökvägen eller enhetsbeteckningen efter att du har upprättat en serverslutpunkt på en volym. Kontrollera att du använder en slutlig sökväg på den registrerade servern.
- En registrerad server har stöd för flera serverslutpunkter, men en synkroniseringsgrupp kan bara ha en serverslutpunkt per registrerad server vid en given tidpunkt. Andra serverslutpunkter i synkroniseringsgruppen måste finnas på olika registrerade servrar.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill lägga till en serverslutpunkt går du till den nyligen skapade synkroniseringsgruppen och väljer sedan **Lägg till serverslutpunkt.**

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-deployment-guide/create-sync-group-part-2.png)

I fönstret **Lägg till serverslutpunkt** anger du följande information för att skapa en serverslutpunkt:

- **Registrerad server:** Namnet på den server eller det kluster där du vill skapa serverslutpunkten.
- **Sökväg:** Windows Server-sökvägen som ska synkroniseras som en del av synkroniseringsgruppen.
- **Molnnivåindelad:** En växel för att aktivera eller inaktivera molnnivåindelad lagring. Med molnnivåindelade kan filer som används sällan eller som används kommas åt nivåindelade till Azure Files.
- **Ledigt utrymme för** volym: Mängden ledigt utrymme som ska reserveras på den volym där serverslutpunkten finns. Om det lediga utrymmet för volymen till exempel är inställt på 50 % på en volym som har en enda serverslutpunkt, är ungefär hälften av mängden data nivåindelad till Azure Files. Oavsett om molnnivåindelning är aktiverat har Azure-filresursen alltid en fullständig kopia av data i synkroniseringsgruppen.
- **Inledande nedladdningsläge:** Det här är ett valfritt val, från och med agentversion 11, som kan vara användbart när det finns filer i Azure-filresursen men inte på servern. En sådan situation kan till exempel finnas om du skapar en serverslutpunkt för att lägga till en annan avdelningskontorsserver i en synkroniseringsgrupp eller när du haveriberedskap för en misslyckad server. Om molnnivåindelning är aktiverat är standardinställningen att endast återkalla namnområdet, inget filinnehåll från början. Det är användbart om du tror att användaråtkomstbegäranden i stället ska bestämma vilket filinnehåll som ska återkallas till servern. Om molnnivåindelad är inaktiverad är standardinställningen att namnområdet laddas ned först och att filerna sedan återkallas baserat på tidsstämpeln för senaste ändring tills den lokala kapaciteten har nåtts. Du kan dock ändra det första nedladdningsläget till endast namnområde. Ett tredje läge kan bara användas om molnnivåindelad är inaktiverat för den här serverslutpunkten. I det här läget undviker du att namnområdet återkallas först. Filer visas bara på den lokala servern om de hade möjlighet att ladda ned fullständigt. Det här läget är användbart om till exempel ett program kräver att fullständiga filer finns och inte tolererar nivåindelade filer i namnområdet.

Om du vill lägga till serverslutpunkten väljer du **Skapa**. Dina filer synkroniseras nu i Azure-filresursen och Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell-kommandon för att skapa serverslutpunkten och se till att ersätta , och med önskade värden och kontrollera den valfria inställningen för den valfria `<your-server-endpoint-path>` `<your-volume-free-space>` inledande nedladdningsprincipen.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd kommandot [az storagesync sync-group server-endpoint](/cli/azure/storagesync/sync-group/server-endpoint#az_storagesync_sync_group_server_endpoint_create) för att skapa en ny serverslutpunkt.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Konfigurera inställningar för brandvägg och virtuellt nätverk

### <a name="portal"></a>Portalen
Om du vill konfigurera azure-filsynkronisering så att den fungerar med brandväggs- och virtuella nätverksinställningar gör du följande:

1. Från Azure Portal navigerar du till det lagringskonto som du vill skydda.
1. Välj **Nätverk** på den vänstra menyn.
1. Under **Valda nätverk** under Tillåt åtkomst **från**.
1. Kontrollera att dina servrars IP-adress eller virtuella nätverk visas under **avsnittet Adressintervall.**
1. Kontrollera att **Tillåt betrodda Microsoft-tjänster åtkomst till det här lagringskontot** är markerat.
1. Spara **inställningarna** genom att välja Spara.

    ![Konfigurera brandväggs- och virtuella nätverksinställningar så att de fungerar med Azure File Sync](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Registrering med Azure File Sync
De rekommenderade stegen för att publicera Azure File Sync för första gången med noll avbrottstid samtidigt som fullständig filåtergivning och åtkomstkontrollista (ACL) bevaras är följande:
 
1. Distribuera en tjänst för synkronisering av lagring.
1. Skapa en synkroniseringsgrupp.
1. Installera Azure File Sync agenten på servern med den fullständiga datauppsättningen.
1. Registrera servern och skapa en serverslutpunkt på resursen. 
1. Låt synkroniseringen göra en fullständig uppladdning till Azure-filresursen (molnslutpunkt).  
1. När den första uppladdningen är klar installerar Azure File Sync agenten på var och en av de återstående servrarna.
1. Skapa nya filresurser på var och en av de återstående servrarna.
1. Skapa serverslutpunkter på nya filresurser med en princip för molnnivåindelning om du vill. (Det här steget kräver att ytterligare lagringsutrymme är tillgängligt för den första installationen.)
1. Låt Azure File Sync agenten snabbt återställa det fullständiga namnområdet utan den faktiska dataöverföringen. Efter den fullständiga namnområdessynkronisering fyller synkroniseringsmotorn det lokala diskutrymmet baserat på molnnivåprincipen för serverslutpunkten. 
1. Se till att synkroniseringen slutförs och testa topologin efter behov. 
1. Omdirigera användare och program till den här nya resursen.
1. Du kan också ta bort eventuella dubblettresurser på servrarna.
 
Om du inte har extra lagringsutrymme för inledande registrering och vill ansluta till befintliga resurser kan du förinstallera data i Azure-filresurser. Den här metoden rekommenderas om och endast om du kan acceptera driftstopp och absolut inte garantera några dataändringar på serverresurser under den första registreringsprocessen. 
 
1. Se till att data på någon av servrarna inte kan ändras under registreringsprocessen.
1. Förinstallera Azure-filresurser med serverdata med hjälp av ett dataöverföringsverktyg via SMB. Till exempel Robocopy. Du kan också använda AzCopy via REST. Se till att använda AzCopy med lämpliga växlar för att bevara tidsstämplar och attribut för ACL:er.
1. Skapa Azure File Sync topologi med önskade serverslutpunkter som pekar på befintliga resurser.
1. Låt synkroniseringen slutföra avstämningsprocessen på alla slutpunkter. 
1. När avstämningen är klar kan du öppna resurser för ändringar.
 
För närvarande har för seeding-metoden några begränsningar – 
- Dataändringar på servern innan synkroniseringstopologin är helt igång kan orsaka konflikter på serverslutpunkterna.  
- När molnslutpunkten har skapats kör Azure File Sync process för att identifiera filerna i molnet innan den inledande synkroniseringen påbörjas. Hur lång tid det tar att slutföra den här processen varierar beroende på olika faktorer som nätverkshastighet, tillgänglig bandbredd och antal filer och mappar. För en ungefärlig uppskattning i förhandsversionen körs identifieringsprocessen cirka 10 filer per sekund.  Även om förse seeding körs snabbt kan den övergripande tiden för att få ett fullständigt system vara betydligt längre när data förinstalleras i molnet.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Självåterställning via tidigare versioner och VSS (tjänsten Volume Shadow Copy)

> [!IMPORTANT]
> Följande information kan bara användas med version 9 (eller senare) av lagringssynkroniseringsagenten. Versioner som är lägre än 9 har inte cmdletarna StorageSyncSelfService.

Tidigare versioner är en Windows-funktion som gör att du kan använda VSS-ögonblicksbilder på serversidan av en volym för att presentera återställningsbara versioner av en fil till en SMB-klient.
Detta möjliggör ett kraftfullt scenario, som ofta kallas självbetjäning, direkt för informationsarbetare i stället för beroende på återställningen från en IT-administratör.

VSS-ögonblicksbilder och tidigare versioner fungerar oberoende av Azure File Sync. Molnnivåindelad lagring måste dock vara inställt på ett kompatibelt läge. Många Azure File Sync serverslutpunkter kan finnas på samma volym. Du måste göra följande PowerShell-anrop per volym som även har en serverslutpunkt där du planerar att eller använder molnnivåindelad.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS-ögonblicksbilder tas av en hel volym. Som standard kan det finnas upp till 64 ögonblicksbilder för en viss volym, givet att det finns tillräckligt med utrymme för att lagra ögonblicksbilderna. VSS hanterar detta automatiskt. Standardschemat för ögonblicksbilder tar två ögonblicksbilder per dag, måndag till fredag. Det schemat kan konfigureras via en schemalagd Uppgift i Windows. Ovanstående PowerShell-cmdlet gör två saker:
1. Den konfigurerar Azure File Syncs molnnivåindelning på den angivna volymen så att den är kompatibel med tidigare versioner och garanterar att en fil kan återställas från en tidigare version, även om den nivåindelades till molnet på servern. 
1. Det aktiverar VSS-standardschemat. Du kan sedan välja att ändra den senare. 

> [!Note]  
> Det finns två viktiga saker att notera:
>- Om du använder parametern -Force och VSS för närvarande är aktiverat, kommer det att skriva över det aktuella schemat för VSS-ögonblicksbilder och ersätta det med standardschemat. Se till att spara din anpassade konfiguration innan du kör cmdleten.
> - Om du använder den här cmdleten på en klusternod måste du också köra den på alla andra noder i klustret! 

Om du vill se om kompatibiliteten för självbetjäning har aktiverats kan du köra följande cmdlet.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Den visar alla volymer på servern samt antalet kompatibla dagar för molnnivåindelad lagring för var och en. Det här antalet beräknas automatiskt baserat på maximalt antal möjliga ögonblicksbilder per volym och standardschemat för ögonblicksbilder. Som standard kan alltså alla tidigare versioner som presenteras för ett informationsarbetare användas för att återställa från. Samma sak gäller om du ändrar standardschemat så att det tar fler ögonblicksbilder.
Men om du ändrar schemat på ett sätt som resulterar i en tillgänglig ögonblicksbild på volymen som är äldre än värdet för kompatibla dagar, kommer användarna inte att kunna använda den äldre ögonblicksbilden (tidigare version) att återställa från.

> [!Note]
> Aktivering av självbetjäning av återställning kan påverka din Azure-lagringsförbrukning och -faktura. Den här effekten är begränsad till filer som för närvarande är nivåindelade på servern. Aktivering av den här funktionen säkerställer att det finns en tillgänglig filversion i molnet som kan refereras via en tidigare version (VSS-ögonblicksbild) post.
>
> Om du inaktiverar funktionen minskar Azure-lagringsförbrukningen långsamt tills fönstret med kompatibla dagar har passerat. Det finns inget sätt att påskynda detta. 

Standardvärdet för maximalt antal VSS-ögonblicksbilder per volym (64) samt standardschemat för att ta dem, resulterar i högst 45 dagars tidigare versioner som en informationsarbetare kan återställa från, beroende på hur många VSS-ögonblicksbilder du kan lagra på din volym.

Om max 64 VSS-ögonblicksbilder per volym inte är rätt inställning för dig kan du ändra [det värdet via en registernyckel](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
För att den nya gränsen ska gälla måste du köra cmdleten igen för att aktivera tidigare versionskompatibilitet på alla volymer som den tidigare var aktiverad, med flaggan -Force för att ta hänsyn till det nya maximala antalet VSS-ögonblicksbilder per volym. Detta resulterar i ett nyligen beräknat antal kompatibla dagar. Observera att den här ändringen endast börjar gälla på nyligen nivåindelade filer och skriver över eventuella anpassningar enligt VSS-schemat som du har gjort.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Proaktivt återkalla nya och ändrade filer från en Azure-filresurs

Med agentversion 11 blir ett nytt läge tillgängligt på en serverslutpunkt. Med det här läget kan globalt distribuerade företag ha servercachen i en förifylld fjärrregion även innan lokala användare får åtkomst till några filer. När det här läget är aktiverat på en serverslutpunkt gör det att servern återkallar filer som har skapats eller ändrats i Azure-filresursen.

### <a name="scenario"></a>Scenario

Ett globalt distribuerat företag har avdelningskontor i USA och Indien. På morgonen (amerikansk tid) skapar informationsarbetare en ny mapp och nya filer för ett helt nytt projekt och arbetar hela dagen med det. Azure File Sync mappar och filer till Azure-filresursen (molnslutpunkt). Informationsarbetare i Indien fortsätter att arbeta med projektet i sin tidszon. När de anländer på morgonen måste den lokala Azure File Sync-aktiverade servern i Indien ha dessa nya filer tillgängliga lokalt, så att Indien-teamet effektivt kan arbeta bort från en lokal cache. Aktivering av det här läget förhindrar att den första filåtkomsten går långsammare på grund av återkallelse på begäran och gör att servern proaktivt kan återkalla filerna så fort de har ändrats eller skapats i Azure-filresursen.

> [!IMPORTANT]
> Det är viktigt att komma ihåg att spårning av ändringar i Azure-filresursen som ligger nära servern kan öka utgående trafik och faktureras från Azure. Om filer som återkallas till servern inte faktiskt behövs lokalt kan onödig återkallelse till servern ha negativa konsekvenser. Använd det här läget när du vet att en förifylld cache på en server med de senaste ändringarna i molnet har en positiv effekt på användare eller program som använder filerna på den servern.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Aktivera en serverslutpunkt för att proaktivt återkalla vad som har ändrats i en Azure-filresurs

# <a name="portal"></a>[Portal](#tab/proactive-portal)

1. I [Azure Portal](https://portal.azure.com/)du till tjänsten för synkronisering av lagring, väljer rätt synkroniseringsgrupp och identifierar sedan den serverslutpunkt som du vill spåra ändringar i Azure-filresursen (molnslutpunkt) för.
1. I avsnittet molnnivåindelning letar du reda på avsnittet "Nedladdning av Azure-filresurs". Du ser det valda läget och kan ändra det för att spåra ändringar i Azure-filresursen närmare och proaktivt återkalla dem till servern.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="En bild som visar nedladdningsbeteendet för Azure-filresursen för en aktuell serverslutpunkt och en knapp för att öppna en meny som gör det möjligt att ändra den.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

Du kan ändra egenskaperna för serverslutpunkten i PowerShell via cmdleten [Set-AzStorageSyncServerEndpoint.](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint)

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrera en DFS Replication -distribution (DFS-R) till Azure File Sync
Migrera en DFS-R-distribution till Azure File Sync:

1. Skapa en synkroniseringsgrupp som representerar den DFS-R-topologi som du ersätter.
1. Starta på den server som har en fullständig uppsättning data i din DFS-R-topologi som ska migreras. Installera Azure File Sync på den servern.
1. Registrera servern och skapa en serverslutpunkt för den första servern som ska migreras. Aktivera inte molnnivåindelad.
1. Låt alla data synkroniseras till din Azure-filresurs (molnslutpunkt).
1. Installera och registrera Azure File Sync på var och en av de återstående DFS-R-servrarna.
1. Inaktivera DFS-R. 
1. Skapa en serverslutpunkt på var och en av DFS-R-servrarna. Aktivera inte molnnivåindelad.
1. Se till att synkroniseringen slutförs och testa topologin efter behov.
1. Dra tillbaka DFS-R.
1. Molnnivåindelad lagring kan nu aktiveras på valfri serverslutpunkt efter behov.

Mer information finns i [Azure File Sync interop med Distributed File System (DFS).](file-sync-planning.md#distributed-file-system-dfs)

## <a name="next-steps"></a>Nästa steg
- [Lägga till eller ta bort Azure File Sync serverslutpunkt](file-sync-server-endpoint.md)
- [Registrera eller avregistrera en server med Azure File Sync](file-sync-server-registration.md)
- [Övervaka Azure File Sync](file-sync-monitoring.md)