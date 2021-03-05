---
title: Skapa en NFS-resurs – Azure Files (förhands granskning)
description: Lär dig hur du skapar en Azure-filresurs som kan monteras med hjälp av Network File System-protokollet.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 01/22/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 2ff669d0ff3bde791de9bc7773e13d880762f898
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102214430"
---
# <a name="how-to-create-an-nfs-share"></a>Så här skapar du en NFS-resurs
Azure-filresurser är fullständigt hanterade fil resurser som bor i molnet. Den här artikeln beskriver hur du skapar en fil resurs som använder NFS-protokollet. Mer information om båda protokollen finns i [Azure File Share-protokoll](storage-files-compare-protocols.md).

## <a name="limitations"></a>Begränsningar
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regional tillgänglighet
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Förutsättningar
- NFS-resurser kan bara nås från betrodda nätverk. Anslutningar till NFS-resursen måste härstamma från någon av följande källor:
    - [Skapa antingen en privat slut punkt](storage-files-networking-endpoints.md#create-a-private-endpoint) (rekommenderas) eller [begränsa åtkomsten till din offentliga slut punkt](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Konfigurera en punkt-till-plats (P2s) VPN på Linux som ska användas med Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Konfigurera en plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).
    - Konfigurera [ExpressRoute](../../expressroute/expressroute-introduction.md).

- Om du tänker använda Azure CLI [installerar du den senaste versionen](/cli/azure/install-azure-cli).

## <a name="register-the-nfs-41-protocol"></a>Registrera NFS 4,1-protokollet
Om du använder Azure PowerShell-modulen eller Azure CLI registrerar du din funktion med följande kommandon:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Använd antingen Azure PowerShell eller Azure CLI för att registrera NFS 4,1-funktionen för Azure Files.

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

Registreringens godkännande kan ta upp till en timme. Kontrol lera att registreringen är klar med följande kommandon:

# <a name="portal"></a>[Portal](#tab/azure-portal)
Använd antingen Azure PowerShell eller Azure CLI för att kontrol lera registreringen av NFS 4,1-funktionen för Azure Files. 

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

## <a name="create-a-filestorage-storage-account"></a>Skapa ett FileStorage-lagrings konto
För närvarande är NFS 4,1-resurser bara tillgängliga som Premium fil resurser. Om du vill distribuera en Premium-filresurs med stöd för NFS 4,1-protokoll måste du först skapa ett FileStorage-lagrings konto. Ett lagrings konto är ett objekt på översta nivån i Azure som representerar en delad pool av lagrings utrymme som kan användas för att distribuera flera Azure-filresurser.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Om du vill skapa ett FileStorage lagrings konto navigerar du till Azure Portal.

1. I Azure Portal väljer du **lagrings konton** på den vänstra menyn.

    ![Azure Portal huvud sida Välj lagrings konto](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

2. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
3. Välj den prenumeration där du vill skapa lagringskontot.
4. Välj den resurs grupp där lagrings kontot ska skapas

5. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
6. Välj en plats för ditt lagringskonto eller använd standardplatsen.
7. För **prestanda** väljer du **Premium**.

    Du måste välja **Premium** för att **FileStorage** ska vara ett tillgängligt alternativ i list rutan **typ av konto** .

8. Välj **typ av konto** och välj **FileStorage**.
9. Lämna **replikeringsuppsättningen** till standardvärdet för **lokalt REDUNDANT lagring (LRS)**.

    ![Så här skapar du ett lagrings konto för en Premium-filresurs](media/storage-how-to-create-premium-fileshare/create-filestorage-account.png)

10. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.
11. Välj **Skapa**.

När lagrings konto resursen har skapats navigerar du till den.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Om du vill skapa ett FileStorage lagrings konto öppnar du en PowerShell-prompt och kör följande kommandon för att komma ihåg att ersätta `<resource-group>` och `<storage-account>` med lämpliga värden för din miljö.

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
Om du vill skapa ett FileStorage lagrings konto öppnar du terminalen och kör följande kommandon, som är medlem i att ersätta `<resource-group>` och `<storage-account>` med lämpliga värden för din miljö.

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

Nu när du har skapat ett FileStorage-konto och konfigurerat nätverket kan du skapa en NFS-filresurs. Processen påminner om hur du skapar en SMB-resurs genom att välja **NFS** i stället för **SMB** när du skapar resursen.

1. Navigera till ditt lagrings konto och välj **fil resurser**.
1. Välj **+ fil resurs** för att skapa en ny fil resurs.
1. Namnge fil resursen, Välj en etablerad kapacitet.
1. För **protokoll** väljer du **NFS (för hands version)**.
1. För **rot-squash** gör du ett val.

    - Rot-squash (standard) – åtkomst för fjärr-superanvändare (rot) mappas till UID (65534) och GID (65534).
    - Ingen rot-squash – fjärr-superanvändare (root) får åtkomst som rot.
    - Alla squash – all användar åtkomst mappas till UID (65534) och GID (65534).
    
1. Välj **Skapa**.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/create-nfs-file-share.png" alt-text="Skärm bild av bladet för att skapa fil resurs":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Se till att .NET Framework är installerat. Se [Ladda ned .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
 
1. Kontrol lera att PowerShell-versionen som har installerats är `5.1` eller högre genom att använda följande kommando.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Information om hur du uppgraderar din version av PowerShell finns i [uppgradera befintliga Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6&preserve-view=true#upgrading-existing-windows-powershell)
    
1. Installera den senaste versionen av PowershellGet-modulen.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Stäng och öppna sedan PowerShell-konsolen igen.

1. Installera **AZ. Storage** Preview module version **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Mer information om hur du installerar PowerShell-moduler finns i [installera modulen Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.0.0&preserve-view=true)
   
1. Om du vill skapa en Premium-filresurs med Azure PowerShell-modulen använder du cmdleten [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

    > [!NOTE]
    > Premium fil resurser faktureras med hjälp av en etablerad modell. Den tillhandahållna resursens storlek anges `QuotaGiB` nedan. Mer information finns i [förstå den etablerade modellen](understanding-billing.md#provisioned-model) och [sidan Azure Files prissättning](https://azure.microsoft.com/pricing/details/storage/files/).

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Om du vill skapa en Premium-filresurs med Azure CLI använder du kommandot [AZ Storage Share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Premium fil resurser faktureras med hjälp av en etablerad modell. Den tillhandahållna resursens storlek anges `quota` nedan. Mer information finns i [förstå den etablerade modellen](understanding-billing.md#provisioned-model) och [sidan Azure Files prissättning](https://azure.microsoft.com/pricing/details/storage/files/).

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
Nu när du har skapat en NFS-resurs, för att använda den, måste du montera den på Linux-klienten. Mer information finns i [så här monterar du en NFS-resurs](storage-files-how-to-mount-nfs-shares.md).

Om det uppstår några problem kan du läsa [Felsöka Azure NFS-filresurser](storage-troubleshooting-files-nfs.md).