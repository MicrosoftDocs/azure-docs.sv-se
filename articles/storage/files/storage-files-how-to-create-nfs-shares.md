---
title: Skapa en NFS-resurs – Azure Files (förhands granskning)
description: Lär dig hur du skapar en Azure-filresurs som kan monteras med hjälp av Network File System-protokollet.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2020
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 323eed77d6f7a6ccfcdd0a7c7aecff3a125300dc
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602665"
---
# <a name="how-to-create-an-nfs-share"></a>Så här skapar du en NFS-resurs

Azure-filresurser är fullständigt hanterade fil resurser som bor i molnet. De kan nås via Server Message Block-protokollet eller NFS-protokollet (Network File System). Den här artikeln beskriver hur du skapar en fil resurs som använder NFS-protokollet. Mer information om båda protokollen finns i [Azure File Share-protokoll](storage-files-compare-protocols.md).

## <a name="limitations"></a>Begränsningar

[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]

### <a name="regional-availability"></a>Regional tillgänglighet

[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>Förutsättningar

- Skapa ett [FileStorage-konto](storage-how-to-create-premium-fileshare.md).

    > [!IMPORTANT]
    > NFS-resurser kan bara nås från betrodda nätverk. Anslutningar till NFS-resursen måste härstamma från någon av följande källor:

    - [Skapa antingen en privat slut punkt](storage-files-networking-endpoints.md#create-a-private-endpoint) (rekommenderas) eller [begränsa åtkomsten till din offentliga slut punkt](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Konfigurera en punkt-till-plats (P2s) VPN på Linux som ska användas med Azure Files](storage-files-configure-p2s-vpn-linux.md).
    - [Konfigurera en plats-till-plats-VPN för användning med Azure Files](storage-files-configure-s2s-vpn.md).
    - Konfigurera [ExpressRoute](../../expressroute/expressroute-introduction.md).
- Om du tänker använda Azure CLI [installerar du den senaste versionen](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="register-the-nfs-41-protocol"></a>Registrera NFS 4,1-protokollet

Om du använder Azure PowerShell-modulen eller Azure CLI registrerar du din funktion med följande kommandon:

### <a name="powershell"></a>PowerShell

```azurepowershell
Connect-AzAccount
$context = Get-AzSubscription -SubscriptionId <yourSubscriptionIDHere>
Set-AzContext $context
Register-AzProviderFeature -FeatureName AllowNfsFileShares -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az login
az feature register --name AllowNfsFileShares \
                    --namespace Microsoft.Storage \
                    --subscription <yourSubscriptionIDHere>
az provider register --namespace Microsoft.Storage
```

## <a name="verify-feature-registration"></a>Verifiera funktions registrering

Registreringens godkännande kan ta upp till en timme. Kontrol lera att registreringen är klar med följande kommandon:

### <a name="powershell"></a>PowerShell

```azurepowershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage -FeatureName AllowNfsFileShares
```

### <a name="azure-cli"></a>Azure CLI

```azurecli
az feature show --name AllowNfsFileShares --namespace Microsoft.Storage --subscription <yourSubscriptionIDHere>
```

## <a name="verify-storage-account-kind"></a>Verifiera typ av lagrings konto

För närvarande kan endast FileStorage-konton skapa NFS-resurser. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Du kan kontrol lera vilken typ av lagrings konto du har genom att navigera till det i Azure Portal. Välj sedan **Egenskaper** från ditt lagrings konto. På bladet egenskaper kontrollerar du värdet under **Kontotyp**, värdet ska vara **FileStorage**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Du kan använda följande kommando för att kontrol lera att du har ett FileStorage-konto:

```azurepowershell
$accountKind=Get-AzStorageAccount -ResourceGroupName "yourResourceGroup" -Name "yourStorageAccountName"
$accountKind.Kind
```

Utdata ska vara **FileStorage**, om det inte är det är lagrings kontot av fel typ. Information om hur du skapar ett **FileStorage** -konto finns i [så här skapar du en Azure Premium-filresurs](storage-how-to-create-premium-fileshare.md).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Du kan använda följande kommando för att kontrol lera att du har ett FileStorage-konto:

```azurecli
az storage account show -g yourResourceGroup -n yourStorageAccountName
```

Utdata ska innehålla **"Natura": "FileStorage"**, om inte, så är lagrings kontot av fel typ. Information om hur du skapar ett **FileStorage** -konto finns i [så här skapar du en Azure Premium-filresurs](storage-how-to-create-premium-fileshare.md).

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
    
   Information om hur du uppgraderar din version av PowerShell finns i [uppgradera befintliga Windows PowerShell](/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
1. Installera den senaste versionen av PowershellGet-modulen.

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force  
   ```

1. Stäng och öppna sedan PowerShell-konsolen igen.

1. Installera **AZ. Storage** Preview module version **2.5.2-Preview**.

   ```powershell
   Install-Module Az.Storage -Repository PsGallery -RequiredVersion 2.5.2-preview -AllowClobber -AllowPrerelease -Force  
   ```

   Mer information om hur du installerar PowerShell-moduler finns i [installera modulen Azure PowerShell](/powershell/azure/install-az-ps?view=azps-3.0.0)
   
1. Om du vill skapa en Premium-filresurs med Azure PowerShell-modulen använder du cmdleten [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) .

> [!NOTE]
> Etablerade resurs storlekar anges av resurs kvoten och fil resurserna debiteras enligt den allokerade storleken. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage/files/).

  ```powershell
  New-AzRmStorageShare `
   -ResourceGroupName $resourceGroupName `
   -StorageAccountName $storageAccountName `
   -Name myshare `
   -EnabledProtocol NFS `
   -RootSquash RootSquash `
   -Context $storageAcct.Context
  ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill skapa en Premium-filresurs med Azure CLI använder du kommandot [AZ Storage Share Create](/cli/azure/storage/share-rm) .

> [!NOTE]
> Etablerade resurs storlekar anges av resurs kvoten och fil resurserna debiteras enligt den allokerade storleken. Mer information finns på sidan med [priser](https://azure.microsoft.com/pricing/details/storage/files/).

```azurecli-interactive
az storage share-rm create \
    --storage-account $STORAGEACCT \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --name "myshare" 
```
---

## <a name="next-steps"></a>Nästa steg

Nu när du har skapat en NFS-resurs, för att använda den, måste du montera den på Linux-klienten. Mer information finns i [så här monterar du en NFS-resurs](storage-files-how-to-mount-nfs-shares.md).

Om det uppstår några problem kan du läsa [Felsöka Azure NFS-filresurser](storage-troubleshooting-files-nfs.md).
