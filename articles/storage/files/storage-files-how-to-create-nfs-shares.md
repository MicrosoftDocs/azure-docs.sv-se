---
title: Skapa en NFS-resurs – Azure Files (förhandsversion)
description: Lär dig hur du skapar en Azure-filresurs som kan monteras med hjälp av protokollet Network File System.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: b549c625f0a6ff0480eafc38f84d292e66350950
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717139"
---
# <a name="how-to-create-an-nfs-share"></a>Så här skapar du en NFS-resurs
Azure-filresurser är fullständigt hanterade filresurser som finns i molnet. Den här artikeln beskriver hur du skapar en filresurs som använder NFS-protokollet. Mer information om båda protokollen finns i [Azure-filresursprotokoll.](storage-files-compare-protocols.md)

## <a name="limitations"></a>Begränsningar
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regional tillgänglighet
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Förutsättningar
- NFS-resurser kan bara nås från betrodda nätverk. Anslutningar till din NFS-resurs måste komma från någon av följande källor:
    - Skapa [antingen en privat slutpunkt](storage-files-networking-endpoints.md#create-a-private-endpoint) (rekommenderas) eller begränsa [åtkomsten till din offentliga slutpunkt.](storage-files-networking-endpoints.md#restrict-public-endpoint-access)
    - [Konfigurera en punkt-till-plats-VPN (P2S) i Linux för användning med Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Konfigurera ett plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).
    - Konfigurera [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Om du tänker använda Azure CLI installerar [du den senaste versionen](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Registrera NFS 4.1-protokollet
Om du använder modulen Azure PowerShell Azure CLI registrerar du funktionen med hjälp av följande kommandon:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Använd antingen Azure PowerShell eller Azure CLI för att registrera NFS 4.1-funktionen för Azure Files.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount

# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context

# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login

# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"

az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId

az provider register \
    --namespace Microsoft.Storage
```

---

Registreringsgodkännande kan ta upp till en timme. Använd följande kommandon för att kontrollera att registreringen är klar:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Använd antingen Azure PowerShell eller Azure CLI för att kontrollera registreringen av NFS 4.1-funktionen för Azure Files. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```

---

## <a name="create-a-filestorage-storage-account"></a>Skapa ett FileStorage-lagringskonto
NFS 4.1-resurser är för närvarande endast tillgängliga som premiumfilresurser. Om du vill distribuera en premiumfilresurs med protokollstöd för NFS 4.1 måste du först skapa ett FileStorage-lagringskonto. Ett lagringskonto är ett objekt på den översta nivån i Azure som representerar en delad lagringspool som kan användas för att distribuera flera Azure-filresurser.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa ett FileStorage-lagringskonto går du till Azure Portal.

1. I Azure Portal väljer du **Lagringskonton** på den vänstra menyn.

    ![Azure Portal väljer lagringskonto på huvudsidan.](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
1. Välj den prenumeration där du vill skapa lagringskontot.
1. Välj den resursgrupp där lagringskontot ska skapas
1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en plats för ditt lagringskonto eller använd standardplatsen.
1. För **Prestanda** väljer du **Premium.**

    Du måste välja **Premium** för **att Fildelningar** ska vara ett tillgängligt alternativ i **listrutan Typ av** konto.

1. För **Premium-kontotyp** väljer **du Filresurs.**

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="Skärmbild av premiumprestanda valt.":::

1. Låt **Replikering ha** kvar standardvärdet Lokalt redundant lagring **(LRS).**
1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
1. Välj **Skapa**.

När lagringskontoresursen har skapats navigerar du till den.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill skapa ett FileStorage-lagringskonto öppnar du en PowerShell-kommandotolk och kör följande kommandon. Kom ihåg att ersätta och med `<resource-group>` lämpliga värden för din `<storage-account>` miljö.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill skapa ett FileStorage-lagringskonto öppnar du terminalen och kör följande kommandon. Kom ihåg att ersätta och med `<resource-group>` lämpliga värden för din `<storage-account>` miljö.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="create-an-nfs-share"></a>Skapa en NFS-filresurs

# <a name="portal"></a>[Portal](#tab/azure-portal)

Nu när du har skapat ett FileStorage-konto och konfigurerat nätverket kan du skapa en NFS-filresurs. Processen liknar att skapa en SMB-resurs. Du väljer **NFS i** stället för **SMB** när du skapar resursen.

1. Gå till ditt lagringskonto och välj **Filresurser.**
1. Välj **+ Filresurs för** att skapa en ny filresurs.
1. Ge filresursen ett namn och välj en etablerad kapacitet.
1. För **Protokoll** väljer **du NFS (förhandsversion)**.
1. För **Root Squash** gör du ett val.

    - Rot squash (standard) – Åtkomst för fjärranvändaren (rot) mappas till UID (65534) och GID (65534).
    - Ingen rot mosade – Fjärr-superanvändare (rot) får åtkomst som rot.
    - All mos mos – All användaråtkomst mappas till UID (65534) och GID (65534).
    
1. Välj **Skapa**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="Skärmbild av bladet för att skapa filresurs.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Kontrollera att .NET Framework är installerat. Se [Ladda ned .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Kontrollera att versionen av PowerShell som har installerats `5.1` är eller högre med hjälp av följande kommando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Information om hur du uppgraderar din version av PowerShell [finns i Uppgradera Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)
    
1. Installera den senaste versionen av PowershellGet-modulen.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Stäng och öppna sedan PowerShell-konsolen igen.

1. Installera **az.storage preview-modulen** version **2.5.2-preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Mer information om hur du installerar PowerShell-moduler finns [i Installera Azure PowerShell modulen](/powershell/azure/install-az-ps)
   
1. Om du vill skapa en premiumfilresurs Azure PowerShell modulen använder du cmdleten [New-AzRmStorageShare.](/powershell/module/az.storage/new-azrmstorageshare)

    > [!NOTE]
    > Premium-filresurser faktureras med hjälp av en etablerad modell. Den etablerade storleken på resursen anges av `QuotaGiB` nedan. Mer information finns i [Förstå den etablerade modellen och](understanding-billing.md#provisioned-model) Azure Files [prissättningssidan.](https://azure.microsoft.com/pricing/details/storage/files/)

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill skapa en premiumfilresurs med Azure CLI använder du [kommandot az storage share create.](/cli/azure/storage/share-rm)

> [!NOTE]
> Premium-filresurser faktureras med hjälp av en etablerad modell. Den etablerade storleken på resursen anges av `quota` nedan. Mer information finns i [Förstå den etablerade modellen och](understanding-billing.md#provisioned-model) Azure Files [prissättningssidan.](https://azure.microsoft.com/pricing/details/storage/files/)

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>Nästa steg
Nu när du har skapat en NFS-resurs måste du montera den på Linux-klienten för att kunna använda den. Mer information finns i [Montera en NFS-resurs.](storage-files-how-to-mount-nfs-shares.md)

Om du får problem kan du gå till [Felsöka Azure NFS-filresurser.](storage-troubleshooting-files-nfs.md)