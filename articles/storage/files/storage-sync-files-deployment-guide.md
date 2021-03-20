---
title: Distribuera Azure File Sync | Microsoft Docs
description: Lär dig hur du distribuerar Azure File Sync, från början till slut, med hjälp av Azure Portal, PowerShell eller Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/05/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4814a12a870d5317ad91c3514327ba0daad7ed69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99225378"
---
# <a name="deploy-azure-file-sync"></a>Distribuera Azure File Sync
Använd Azure File Sync för att centralisera organisationens fil resurser i Azure Files, samtidigt som du behåller flexibilitet, prestanda och kompatibilitet för en lokal fil server. Windows Server omvandlas av Azure File Sync till ett snabbt cacheminne för Azure-filresursen. Du kan använda alla protokoll som är tillgängliga på Windows Server för att komma åt data lokalt, inklusive SMB, NFS och FTPS. Du kan ha så många cacheminnen som du behöver över hela världen.

Vi rekommenderar starkt att du läser [planering för en Azure Files distribution](storage-files-planning.md) och [planering för en Azure File Sync distribution](storage-sync-files-planning.md) innan du slutför stegen som beskrivs i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. En Azure-filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regions tillgänglighet](storage-sync-files-planning.md#azure-file-sync-region-availability) för Azure File Sync.
    - [Skapa en fil resurs](storage-how-to-create-file-share.md) för en steg-för-steg-beskrivning av hur du skapar en fil resurs.
1. Minst en instans av Windows Server eller Windows Server-klustret som stöds för synkronisering med Azure File Sync. Mer information om vilka versioner av Windows Server och rekommenderade system resurser som stöds finns i [Windows fil Server överväganden](storage-sync-files-planning.md#windows-file-server-considerations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. En Azure-filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regions tillgänglighet](storage-sync-files-planning.md#azure-file-sync-region-availability) för Azure File Sync.
    - [Skapa en fil resurs](storage-how-to-create-file-share.md) för en steg-för-steg-beskrivning av hur du skapar en fil resurs.
1. Minst en instans av Windows Server eller Windows Server-klustret som stöds för synkronisering med Azure File Sync. Mer information om vilka versioner av Windows Server och rekommenderade system resurser som stöds finns i [Windows fil Server överväganden](storage-sync-files-planning.md#windows-file-server-considerations).

1. PowerShell-modulen AZ kan användas med antingen PowerShell 5,1 eller PowerShell 6 +. Du kan använda AZ PowerShell-modulen för Azure File Sync på alla system som stöds, inklusive icke-Windows-system, men Server registrerings cmdleten måste alltid köras på den Windows Server-instans som du registrerar (detta kan göras direkt eller via PowerShell-fjärrkommunikation). På Windows Server 2012 R2 kan du kontrol lera att du kör minst PowerShell 5,1. \* genom att titta på värdet för egenskapen **PSVersion** för **$PSVersionTable** -objektet:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Om ditt **PSVersion** -värde är mindre än 5,1. \* , som är fallet med de flesta nya installationer av Windows Server 2012 R2, kan du enkelt uppgradera genom att hämta och installera [Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616). Lämpligt paket för att ladda ned och installera för Windows Server 2012 R2 är **Win 8.1 andw2k12r2-KB \* \* \* \* \* \* \* -x64. msu**. 

    PowerShell 6 + kan användas med alla system som stöds och kan hämtas via dess GitHub- [sida](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Om du planerar att använda användar gränssnittet för Server registrering i stället för att registrera direkt från PowerShell måste du använda PowerShell 5,1.

1. Om du har valt att använda PowerShell 5,1 kontrollerar du att minst .NET 4.7.2 är installerat. Läs mer om [.NET Framework versioner och beroenden](/dotnet/framework/migration-guide/versions-and-dependencies) i systemet.

    > [!Important]  
    > Om du installerar .NET 4.7.2 + på Windows Server Core måste du installera med-och- `quiet` `norestart` flaggorna eller så Miss söker installationen. Om du till exempel installerar .NET 4,8 ser kommandot ut så här:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. AZ PowerShell-modulen som kan installeras genom att följa anvisningarna här: [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > Modulen AZ. StorageSync installeras nu automatiskt när du installerar AZ PowerShell-modulen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. En Azure-filresurs i samma region som du vill distribuera Azure File Sync. Mer information finns i:
    - [Regions tillgänglighet](storage-sync-files-planning.md#azure-file-sync-region-availability) för Azure File Sync.
    - [Skapa en fil resurs](storage-how-to-create-file-share.md) för en steg-för-steg-beskrivning av hur du skapar en fil resurs.
1. Minst en instans av Windows Server eller Windows Server-klustret som stöds för synkronisering med Azure File Sync. Mer information om vilka versioner av Windows Server och rekommenderade system resurser som stöds finns i [Windows fil Server överväganden](storage-sync-files-planning.md#windows-file-server-considerations).

1. [Installera Azure CLI](/cli/azure/install-azure-cli)

   Om du vill kan du också använda Azure Cloud Shell för att slutföra stegen i den här självstudien.  Azure Cloud Shell är en interaktiv gränssnitts miljö som du använder via webbläsaren.  Starta Cloud Shell med någon av följande metoder:

   - Välj **Prova** i det övre högra hörnet av ett kodblock. **Prova** att öppna Azure Cloud Shell, men kopierar inte koden automatiskt till Cloud Shell.

   - Öppna Cloud Shell genom att gå till [https://shell.azure.com](https://shell.azure.com)

   - Välj knappen **Cloud Shell** på Meny raden i det övre högra hörnet i [Azure Portal](https://portal.azure.com)

1. Logga in.

   Logga in med kommandot [az login](/cli/azure/reference-index#az-login) om du använder en lokal CLI-installation.

   ```azurecli
   az login
   ```

    Slutför autentiseringsprocessen genom att följa anvisningarna i terminalen.

1. Installera [AZ FileSync](/cli/azure/ext/storagesync/storagesync) Azure CLI-tillägget.

   ```azurecli
   az extension add --name storagesync
   ```

   När du har installerat **storagesync** -tilläggs referensen visas följande varning.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Förbereda Windows Server för användning med Azure File Sync
Inaktivera **förbättrad säkerhets konfiguration i Internet Explorer** för varje server som du vill använda med Azure File Sync, inklusive varje servernod i ett kluster för växling vid fel. Detta krävs endast för inledande Server registrering. Du kan aktivera det igen när servern har registrerats.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Du kan hoppa över det här steget om du distribuerar Azure File Sync på Windows Server Core.

1. Öppna Serverhanteraren.
2. Klicka på **lokal server**:  
    ![”Lokal server” till vänster i användargränssnittet för Serverhanteraren](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. I underfönstret **Egenskaper** väljer du länken för **Förbättrad säkerhetskonfiguration i Internet Explorer**.  
    ![Fönstret ”Förbättrad säkerhetskonfiguration i Internet Explorer” i användargränssnittet i Serverhanteraren](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. I dialog rutan **förbättrad säkerhets konfiguration i Internet Explorer** väljer du **av** för **Administratörer** och **användare**:  
    ![Popup-fönstret Förbättrad säkerhetskonfiguration i Internet Explorer med ”Av” valt](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill inaktivera Förbättrad säkerhets konfiguration i Internet Explorer kör du följande från en upphöjd PowerShell-session:

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
Distributionen av Azure File Sync börjar med att placera en resurs för **lagrings-synkroniseringstjänsten** i en resurs grupp för den valda prenumerationen. Vi rekommenderar etablering så att det är så viktigt som det behövs. Du kommer att skapa en förtroende relation mellan dina servrar och den här resursen och en server kan bara registreras till en lagrings tjänst för synkronisering. Därför rekommenderar vi att du distribuerar så många tjänster för synkronisering av lagring som du behöver för att skilja grupper av servrar. Tänk på att servrar från olika lagrings Sync-tjänster inte kan synkroniseras med varandra.

> [!Note]
> Tjänsten Storage Sync ärver åtkomst behörigheter från prenumerationen och resurs gruppen som den har distribuerats till. Vi rekommenderar att du noga kontrollerar vem som har åtkomst till den. Entiteter med skriv åtkomst kan börja synkronisera nya uppsättningar av filer från servrar som är registrerade i den här tjänsten för synkronisering av lagring och leda till att data flödar till Azure Storage som är tillgängligt för dem.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill distribuera en tjänst för synkronisering av lagring går du till [Azure Portal](https://portal.azure.com/), klickar på *skapa en resurs* och söker sedan efter Azure File Sync. I Sök resultaten väljer du **Azure File Sync** och väljer sedan **skapa** för att öppna fliken **distribuera synkronisering av lagring** .

I fönstret som öppnas anger du följande information:

- **Namn**: ett unikt namn (per region) för tjänsten Storage Sync.
- **Prenumeration**: den prenumeration där du vill skapa tjänsten för synkronisering av lagring. Beroende på din organisations konfigurations strategi kan du ha åtkomst till en eller flera prenumerationer. En Azure-prenumeration är den mest grundläggande behållaren för fakturering för varje moln tjänst (till exempel Azure Files).
- **Resurs grupp**: en resurs grupp är en logisk grupp av Azure-resurser, till exempel ett lagrings konto eller en tjänst för synkronisering av lagring. Du kan skapa en ny resurs grupp eller använda en befintlig resurs grupp för Azure File Sync. (Vi rekommenderar att du använder resurs grupper som behållare för att isolera resurser logiskt för din organisation, till exempel för att gruppera HR-resurser eller resurser för ett bestämt projekt.)
- **Plats**: den region där du vill distribuera Azure File Sync. Endast regioner som stöds är tillgängliga i den här listan.

När du är färdig väljer du **skapa** för att distribuera tjänsten för synkronisering av lagring.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Ersätt `<Az_Region>` , `<RG_Name>` och `<my_storage_sync_service>` med dina egna värden, och Använd sedan följande kommandon för att skapa och distribuera en tjänst för synkronisering av lagring:

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
Du kan ladda ned agenten från [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). När hämtningen är slutförd, dubbelklickar du på MSI-paketet för att starta installationen av Azure File Sync-agenten.

> [!Important]  
> Om du tänker använda Azure File Sync med ett redundanskluster måste Azure File Syncs agenten installeras på varje nod i klustret. Varje nod i klustret måste vara registrerad för att fungera med Azure File Sync.

Vi rekommenderar att du gör följande:
- Lämna standard installations Sök vägen (C:\Program Files\Azure\StorageSyncAgent) för att förenkla fel sökning och Server underhåll.
- Aktivera Microsoft Update för att hålla Azure File Sync uppdaterat. Alla uppdateringar till Azure File Sync agent, inklusive funktions uppdateringar och snabb korrigeringar, sker från Microsoft Update. Vi rekommenderar att du installerar den senaste uppdateringen för Azure File Sync. Mer information finns i [Azure File Sync Update policy](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

När installationen av Azure File Sync agent har slutförts öppnas användar gränssnittet för Server registrering automatiskt. Du måste ha en tjänst för synkronisering av lagring före registreringen. Se nästa avsnitt om hur du skapar en tjänst för synkronisering av lagring.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell-kod för att ladda ned rätt version av Azure File Sync agent för operativ systemet och installera den på systemet.

> [!Important]  
> Om du tänker använda Azure File Sync med ett redundanskluster måste Azure File Syncs agenten installeras på varje nod i klustret. Varje nod i klustret måste ha registrerats för att fungera med Azure File Sync.

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
> Server registreringen använder dina Azure-autentiseringsuppgifter för att skapa en förtroende relation mellan tjänsten för synkronisering av lagring och Windows Server, men därefter skapar servern och använder sin egen identitet som är giltig så länge servern är registrerad och den aktuella signaturen token för delad åtkomst (lagrings-SAS) är giltig. Det går inte att utfärda en ny SAS-token till servern när servern har avregistrerats, och därför kan du ta bort serverns möjlighet att komma åt dina Azure-filresurser och stoppa eventuell synkronisering.

Administratören som registrerar servern måste vara medlem i hanterings rollens **ägare** eller **deltagare** för den aktuella tjänsten för synkronisering av lagring. Detta kan konfigureras under **Access Control (IAM)** i Azure Portal för tjänsten för synkronisering av lagring.

Det är också möjligt att särskilja administratörer som kan registrera servrar från de som tillåts att även konfigurera synkronisering i en tjänst för synkronisering av lagring. För att du ska kunna skapa en anpassad roll där du listar de administratörer som endast får registrera servrar och ge din anpassade roll följande behörigheter:

* "Microsoft. StorageSync/storageSyncServices/registeredServers/Write"
* "Microsoft. StorageSync/storageSyncServices/Read"
* "Microsoft. StorageSync/storageSyncServices/arbetsflöden/Read"
* "Microsoft. StorageSync/storageSyncServices/arbets flöden/Operations/Read"

# <a name="portal"></a>[Portal](#tab/azure-portal)
Server registrerings gränssnittet bör öppnas automatiskt när du har installerat Azure File Sync agenten. Om det inte gör det kan du öppna det från dess filplats: C:\Program\Azure\StorageSyncAgent\ServerRegistration.exe. När Server registrerings gränssnittet öppnas väljer du **Logga** in för att börja.

När du har loggat in uppmanas du att ange följande information:

![Skärmbild av användargränssnittet för serverregistrering](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Azure-prenumeration**: prenumerationen som innehåller tjänsten för synkronisering av lagring (se [distribuera tjänsten för synkronisering av lagring](#deploy-the-storage-sync-service)). 
- **Resurs grupp**: resurs gruppen som innehåller tjänsten för synkronisering av lagring.
- **Tjänsten för synkronisering av lagring**: namnet på den tjänst för synkronisering av lagring som du vill registrera.

När du har valt lämplig information väljer du **Registrera** för att slutföra Server registreringen. Som en del av registreringsprocessen uppmanas du att logga in en ytterligare inloggning.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Följ anvisningarna för Azure Portal eller PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Skapa en synkroniseringsgrupp och en molnslutpunkt
En synkroniseringsgrupp definierar synkroniseringstopologin för en uppsättning filer. Slutpunkter i en synkroniseringsgrupp synkroniseras med varandra. En synkroniseringsgrupp måste innehålla en molnslutpunkt, som representerar en Azure-filresurs och en eller flera serverslutpunkter. En server slut punkt representerar en sökväg på en registrerad Server. En server kan ha Server slut punkter i flera Sync-grupper. Du kan skapa så många Sync-grupper som du behöver för att beskriva önskad frågetopologi.

En moln slut punkt är en pekare till en Azure-filresurs. Alla Server slut punkter synkroniseras med en moln slut punkt, vilket gör moln slut punkten till hubben. Lagrings kontot för Azure-filresursen måste finnas i samma region som tjänsten för synkronisering av lagring. Hela Azure-filresursen kommer att synkroniseras, med ett undantag: en särskild mapp, jämförbar med den dolda mappen "System Volume Information" på en NTFS-volym, kommer att tillhandahållas. Den här katalogen kallas ". SystemShareInformation". Den innehåller viktiga metadata för synkronisering som inte kommer att synkroniseras med andra slut punkter. Använd inte eller ta inte bort!

> [!Important]  
> Du kan göra ändringar i alla moln slut punkter eller Server slut punkter i synkroniseringsresursen och låta filerna vara synkroniserade med de andra slut punkterna i den synkroniserade gruppen. Om du gör en ändring i moln slut punkten (Azure-filresursen) direkt måste ändringarna först identifieras av ett Azure File Sync ändrings identifierings jobb. Ett ändrings identifierings jobb initieras bara för en moln slut punkt var 24: e timme. Mer information finns i [Azure Files vanliga frågor och svar](storage-files-faq.md#afs-change-detection).

Administratören som skapar moln slut punkten måste vara medlem i hanterings rollens **ägare** för det lagrings konto som innehåller den Azure-filresurs som moln slut punkten pekar på. Detta kan konfigureras under **Access Control (IAM)** i Azure Portal för lagrings kontot.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa en Sync-grupp går du till tjänsten lagrings-synkronisering i [Azure Portal](https://portal.azure.com/)och väljer sedan **+ Sync-grupp**:

![Skapa en ny synkroniseringsgrupp i Azure-portalen](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

I fönstret som öppnas anger du följande information för att skapa en synkroniseringsgrupp med en molnslutpunkt:

- **Sync-gruppens namn**: namnet på den Sync-grupp som ska skapas. Det här namnet måste vara unikt i tjänsten för synkronisering av lagring men kan vara vilket namn som helst som är logiskt för dig.
- **Prenumeration**: den prenumeration där du distribuerade synkroniseringstjänsten för lagring i [distribuera tjänsten för synkronisering av lagring](#deploy-the-storage-sync-service).
- **Lagrings konto**: om du väljer **Välj lagrings konto** visas en annan ruta där du kan välja det lagrings konto som har den Azure-filresurs som du vill synkronisera med.
- **Azure-fil resurs**: namnet på den Azure-filresurs som du vill synkronisera med.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell om du vill skapa en Sync-grupp. Kom ihåg att ersätta `<my-sync-group>` med det önskade namnet på Sync-gruppen.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

När du har skapat Sync-gruppen kan du skapa din moln slut punkt. Se till att ersätta `<my-storage-account>` och `<my-file-share>` med de förväntade värdena.

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

Använd kommandot [AZ storagesync Sync-Group](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) för att skapa en ny Sync-grupp.  Om du vill använda en resurs grupp som standard för alla CLI-kommandon använder du [AZ konfigurera](/cli/azure/reference-index#az-configure).

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Använd kommandot [AZ storagesync Sync-Group Cloud-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) för att skapa en ny moln slut punkt.

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
En serverslutpunkt representerar en viss plats på en registrerad server, till exempel en mapp på en servervolym. En server slut punkt måste vara en sökväg på en registrerad Server (i stället för en monterad resurs) och för att använda moln nivåer måste sökvägen vara på en volym som inte är en system volym. NAS (Network Attached Storage) stöds inte.

> [!NOTE]
> Det går inte att ändra sökvägen eller enhets beteckningen när du har upprättat en server slut punkt på en volym. Kontrol lera att du använder en slutgiltig sökväg på den registrerade servern.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill lägga till en server slut punkt går du till den nya Sync-gruppen och väljer sedan **Lägg till Server slut punkt**.

![Lägga till en serverslutpunkt i fönstret för synkroniseringsgruppen](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

I fönstret **Lägg till serverslutpunkt** anger du följande information för att skapa en serverslutpunkt:

- **Registrerad Server**: namnet på servern eller klustret där du vill skapa server slut punkten.
- **Sökväg**: Windows Server-sökvägen som ska synkroniseras som en del av Sync-gruppen.
- **Moln nivå**: en växel för att aktivera eller inaktivera moln nivåer. Med moln nivåer kan använda filer som används sällan eller som används i nivå till Azure Files.
- **Ledigt utrymme på volym**: mängden ledigt utrymme som ska reserveras på den volym där Server slut punkten finns. Om till exempel volym ledigt utrymme är inställt på 50% på en volym som har en enda server slut punkt, är ungefär hälften av data i nivå till Azure Files. Oavsett om moln nivån är aktive rad, har Azure-filresursen alltid en fullständig kopia av data i Sync-gruppen.
- **Inledande nedladdnings läge**: Detta är ett valfritt val, från och med agent version 11, som kan vara till hjälp när det finns filer i Azure-filresursen men inte på servern. En sådan situation kan till exempel inträffa om du skapar en server slut punkt för att lägga till en annan avdelnings kontors server i en Sync-grupp eller när du gör en katastrof återställning av en misslyckad Server. Om moln nivån är aktive rad är standardvärdet att endast återkalla namn området, inget fil innehåll inlednings vis. Det är användbart om du tror att i stället för förfrågningar om användar åtkomst bör bestämma vilket fil innehåll som återkallas till servern. Om moln skiktning är inaktive rad är standardvärdet att namn området hämtas först och sedan kommer filerna att återkallas baserat på den senast ändrade tidsstämpeln tills den lokala kapaciteten har nåtts. Du kan dock bara ändra det inledande nedladdnings läget till namn område. Ett tredje läge kan bara användas om moln nivåer har inaktiverats för den här server slut punkten. Det här läget förhindrar att namn området anropas först. Filerna visas bara på den lokala servern om de hade chans att laddas ned helt. Det här läget är användbart om till exempel ett program kräver att fullständiga filer finns och inte kan tolerera filer med nivåer i namn området.

Välj **skapa** om du vill lägga till Server slut punkten. Filerna hålls nu synkroniserade över Azure-filresursen och Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Kör följande PowerShell-kommandon för att skapa server slut punkten och se till att ersätta `<your-server-endpoint-path>` `<your-volume-free-space>` och med önskade värden och kontrol lera den valfria inställningen för den valfria inledande nedladdnings principen.

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

Använd kommandot [AZ storagesync Sync-Group Server-Endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) för att skapa en ny server slut punkt.

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

### <a name="portal"></a>Portal
Gör så här om du vill konfigurera Azure File Sync så att den fungerar med brand vägg och inställningar för virtuellt nätverk:

1. Från Azure Portal navigerar du till det lagrings konto som du vill skydda.
1. Välj knappen **brand väggar och virtuella nätverk** på den vänstra menyn.
1. Välj **valda nätverk** under **Tillåt åtkomst från**.
1. Kontrol lera att dina servrars IP-adresser eller virtuella nätverk visas under lämpligt avsnitt.
1. Kontrol lera att **Tillåt att betrodda Microsoft-tjänster har åtkomst till det här lagrings kontot** är markerat.
1. Spara inställningarna genom att välja **Spara** .

![Konfigurera inställningarna för brand väggen och det virtuella nätverket så att de fungerar med Azure File Sync](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Onboarding med Azure File Sync
De rekommenderade stegen för att publicera på Azure File Sync för första gången med noll drift stopp samtidigt som fullständig fil åter givning och åtkomst kontrol lista (ACL) bevaras är följande:
 
1. Distribuera en tjänst för synkronisering av lagring.
1. Skapa en synkroniseringsgrupp.
1. Installera Azure File Sync agent på servern med fullständig data uppsättning.
1. Registrera servern och skapa en server slut punkt på resursen. 
1. Låt synkronisering utföra fullständig uppladdning till Azure-filresursen (moln slut punkt).  
1. När den första överföringen är klar installerar du Azure File Sync agent på var och en av de återstående servrarna.
1. Skapa nya fil resurser på var och en av de återstående servrarna.
1. Skapa server slut punkter på nya fil resurser med princip för moln skikt, om det behövs. (Det här steget kräver att ytterligare lagrings utrymme är tillgängligt för den första installationen.)
1. Låt Azure File Sync-agenten göra en snabb återställning av hela namn området utan den faktiska data överföringen. När den fullständiga namn rymden synkroniseras fyller Synkroniseringsmotorn det lokala disk utrymmet baserat på moln nivå principen för Server slut punkten. 
1. Se till att synkroniseringen är klar och testa din topologi som önskad. 
1. Omdirigera användare och program till den här nya resursen.
1. Du kan också ta bort alla duplicerade resurser på servrarna.
 
Om du inte har extra lagrings utrymme för inledande onboarding och vill ansluta till de befintliga resurserna kan du i förväg dirigera data i Azure Files-resurserna. Den här metoden föreslås, om du och bara om du kan godkänna stillestånds tid och absolut garanti på att inga data ändras på Server resurserna under den inledande onboarding-processen. 
 
1. Se till att data på någon av servrarna inte kan ändras under onboarding-processen.
1. I förväg dirigerar du Azure-filresurser med Server data med hjälp av alla data överförings verktyg via SMB. Robocopy, till exempel. Du kan också använda AzCopy över REST. Se till att använda AzCopy med lämpliga växlar för att bevara tidsstämplar och attribut för ACL: er.
1. Skapa Azure File Sync topologi med önskade Server slut punkter som pekar på de befintliga resurserna.
1. Låt avstämnings processen för synkronisering slutföras på alla slut punkter. 
1. När avstämningen är klar kan du öppna resurser för ändringar.
 
För närvarande har för-seeding-metoden några begränsningar – 
- Data ändringar på servern innan topologin för synkronisering är helt igång kan orsaka konflikter på serverns slut punkter.  
- När moln slut punkten har skapats kör Azure File Sync en process för att identifiera filerna i molnet innan du startar den första synkroniseringen. Den tid det tar att slutföra den här processen varierar beroende på de olika faktorer som nätverks hastighet, tillgänglig bandbredd och antal filer och mappar. För en grov uppskattning i för hands versionen körs identifierings processen ungefär vid 10 filer/SEK.  Även om för indirigering körs snabbt, kan den totala tiden för att få ett fullständigt operativ system vara betydligt längre när data försätts i molnet.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Återställning via självbetjäning genom tidigare versioner och VSS (tjänsten Volume Shadow Copy)

> [!IMPORTANT]
> Följande information kan bara användas med version 9 (eller senare) av agenten för synkronisering av lagring. Versioner som är lägre än 9 kommer inte att ha StorageSyncSelfService-cmdletar.

Tidigare versioner är en Windows-funktion som gör att du kan använda VSS-ögonblicksbilder på Server sidan på en volym för att presentera återställas-versioner av en fil till en SMB-klient.
Detta möjliggör ett kraftfullt scenario, som vanligt vis kallas självbetjänings återställning, direkt för informations anställda i stället för att beroende på återställningen från en IT-administratör.

VSS-ögonblicksbilder och tidigare versioner fungerar oberoende av Azure File Sync. Moln nivåer måste dock vara inställt på ett kompatibelt läge. Många Azure File Sync Server-slutpunkter kan finnas på samma volym. Du måste göra följande PowerShell-anrop per volym som har till och med en server slut punkt där du planerar till eller använder moln nivåer.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

VSS-ögonblicksbilder tas från en hel volym. Som standard kan upp till 64 ögonblicks bilder finnas för en specifik volym, och det finns tillräckligt med utrymme för att lagra ögonblicks bilderna. VSS hanterar detta automatiskt. Standard schema för ögonblicks bilder tar två ögonblicks bilder per dag, måndag till fredag. Schemat kan konfigureras via en schemalagd schemalagd aktivitet i Windows. PowerShell-cmdleten ovan gör två saker:
1. Den konfigurerar Azure File Sync-synkronisering av moln nivåer på den angivna volymen så att den är kompatibel med tidigare versioner och garanterar att en fil kan återställas från en tidigare version, även om den har lagrats i molnet på servern. 
1. Det aktiverar standard schema för VSS. Du kan sedan välja att ändra den senare. 

> [!Note]  
> Det finns två viktiga saker att tänka på:
>- Om du använder parametern-Force och VSS är aktive rad skrivs det aktuella VSS-bildschemat över och ersätts med standard schemat. Se till att du sparar den anpassade konfigurationen innan du kör cmdleten.
> - Om du använder denna cmdlet på en klusternod måste du också köra den på alla andra noder i klustret! 

Du kan köra följande cmdlet för att se om självbetjänings återställnings kompatibilitet är aktiverat.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Den visar alla volymer på servern samt antalet kompatibla dagar för moln nivåer för var och en. Det här talet beräknas automatiskt baserat på maximalt antal möjliga ögonblicks bilder per volym och standard schema för ögonblicks bilder. Som standard kan alla tidigare versioner som presenteras för en informations arbetare användas för att återställa från. Detsamma gäller om du ändrar standardschemat för att ta fler ögonblicks bilder.
Om du däremot ändrar schemat på ett sätt som resulterar i en tillgänglig ögonblicks bild på volymen som är äldre än värdet för den inkompatibla dagen, kan användarna inte använda denna äldre ögonblicks bild (tidigare version) för att återställa från.

> [!Note]
> Aktivering av självbetjänings återställning kan påverka din användning av Azure Storage och faktura. Den här effekten är begränsad till filer som för närvarande skiktas på servern. Genom att aktivera den här funktionen ser du till att det finns en fil version i molnet som kan refereras till via en tidigare version (VSS snapshot)-post.
>
> Om du inaktiverar funktionen avvisas användningen av Azure Storage långsamt tills fönstret kompatibla dagar har slutförts. Det finns inget sätt att påskynda detta. 

Standardvärdet för maximalt antal VSS-ögonblicksbilder per volym (64) och standardschemat som ska vidtas, resulterar i högst 45 dagar i tidigare versioner som en informations anställd kan återställa från, beroende på hur många VSS-ögonblicksbilder du kan lagra på din volym.

Om max. 64 VSS-ögonblicksbilder per volym inte är rätt inställning kan du [ändra värdet via en register nyckel](/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
För att den nya gränsen ska träda i kraft måste du köra cmdleten igen för att aktivera kompatibilitet med tidigare versioner på varje volym som den tidigare var aktive rad, med flaggan-Force att ta det nya maximala antalet VSS-ögonblicksbilder per volym i kontot. Detta leder till ett nyligen beräknat antal kompatibla dagar. Observera att den här ändringen bara träder i kraft på nyligen nivåbaserade filer och skriver över eventuella anpassningar av VSS-schemat som du kan ha gjort.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Återkalla nya och ändrade filer proaktivt från en Azure-filresurs

Med agent version 11 blir ett nytt läge tillgängligt på en server slut punkt. Med det här läget kan globalt distribuerade företag ha serverns cacheminne i en fjärrregion som är förifyllt, även innan lokala användare får åtkomst till några filer. När det är aktiverat på en server slut punkt kommer det här läget orsaka att den här servern återkallar filer som har skapats eller ändrats i Azure-filresursen.

### <a name="scenario"></a>Scenario

Ett globalt distribuerat företag har avdelnings kontor i USA och i Indien. I morgon (oss tid) informations anställda skapar du en ny mapp och nya filer för ett helt nytt projekt och arbetar hela dagen på den. Azure File Sync synkroniserar mapp och filer med Azure-filresursen (moln slut punkten). Informations anställda i Indien fortsätter att arbeta med projektet i sin tidszon. När de anländer till morgon måste den lokala Azure File Sync-aktiverade servern i Indien ha dessa nya filer tillgängliga lokalt, så att Indien-teamet kan arbeta effektivt från en lokal cache. Att aktivera det här läget förhindrar att den initiala fil åtkomsten går långsammare på grund av återkallande på begäran och gör att servern kan återkalla filerna proaktivt så fort de har ändrats eller skapats i Azure-filresursen.

> [!IMPORTANT]
> Det är viktigt att du inser att spårnings ändringar i Azure-filresursen som är nära servern kan öka den utgående trafiken och fakturera från Azure. Om filer som har återkallats till servern inte behövs lokalt, kan onödig återkallning till servern få negativa konsekvenser. Använd det här läget när du vet att förkonfigurera cachen på en server med de senaste ändringarna i molnet kommer att ha en positiv inverkan på användare eller program som använder filerna på den servern.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Aktivera en server slut punkt för att proaktivt återkalla vad som ändrats i en Azure-filresurs

# <a name="portal"></a>[Portal](#tab/proactive-portal)

1. I [Azure Portal](https://portal.azure.com/)går du till tjänsten Storage Sync, väljer rätt Sync-grupp och identifierar sedan Server slut punkten för vilken du vill spåra ändringar i Azure-filresursen (moln slut punkt).
1. I avsnittet Cloud tierion letar du upp avsnittet "Azure File Share Download". Du ser det valda läget och kan ändra det för att spåra ändringar i Azure-filresursen och på så sätt återställa dem till servern.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="En bild som visar hämtnings beteendet för Azure File-resurser för en server slut punkt som för närvarande används och en knapp för att öppna en meny som gör det möjligt att ändra den.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

Du kan ändra egenskaperna för Server slut punkten i PowerShell genom cmdleten [set-AzStorageSyncServerEndpoint](/powershell/module/az.storagesync/set-azstoragesyncserverendpoint) .

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrera en DFS Replication-distribution (DFS-R) till Azure File Sync
Så här migrerar du en DFS-R-distribution till Azure File Sync:

1. Skapa en Sync-grupp som representerar den DFS-R-topologi som du ersätter.
2. Starta på den server som har en fullständig uppsättning data i din DFS-R-topologi för att migrera. Installera Azure File Sync på den servern.
3. Registrera servern och skapa en server slut punkt för den första servern som ska migreras. Aktivera inte moln nivåer.
4. Låt all data synkroniseras med din Azure-filresurs (moln slut punkt).
5. Installera och registrera Azure File Sync-agenten på var och en av de återstående DFS-R-servrarna.
6. Inaktivera DFS-R. 
7. Skapa en server slut punkt på varje DFS-R-Server. Aktivera inte moln nivåer.
8. Se till att synkroniseringen är klar och testa din topologi som önskad.
9. Dra tillbaka DFS-R.
10. Moln nivåer kan nu aktive ras på alla Server slut punkter efter behov.

Mer information finns i [Azure File Sync interop med Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Nästa steg
- [Lägga till eller ta bort en Azure File Sync Server slut punkt](storage-sync-files-server-endpoint.md)
- [Registrera eller avregistrera en server med Azure File Sync](storage-sync-files-server-registration.md)
- [Övervaka Azure File Sync](storage-sync-files-monitoring.md)
