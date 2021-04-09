---
title: Använda PowerShell för att hantera data i Azures oberoende moln
titleSuffix: Azure Storage
description: Hantering av lagring i molnet i Kina, i det offentliga molnet i molnet och i Tyskland med Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e924a5f6c765b5b964fe3b1492393b063d9d23b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783580"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Hantera lagring i Azures oberoende moln med PowerShell

De flesta människor använder Azures offentliga moln för sin globala Azure-distribution. Det finns också några oberoende distributioner av Microsoft Azure av suveränitets skäl och så vidare. Dessa oberoende distributioner kallas "miljöer". I följande lista beskrivs de oberoende moln som är tillgängliga för närvarande.

* [Azure Government molnet](https://azure.microsoft.com/features/gov/)
* [Azure Kina 21Vianet-moln som drivs av 21Vianet i Kina](http://www.windowsazure.cn/)
* [Azure tyskt-moln](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Använda ett oberoende moln

Om du vill använda Azure Storage i ett av de oberoende molnen ansluter du till det molnet i stället för Azure Public. Så här använder du ett av de oberoende molnen i stället för Azure Public:

* Du anger *miljön* som du vill ansluta till.
* Du fastställer och använder de tillgängliga regionerna.
* Du använder rätt slut punkts suffix, vilket skiljer sig från Azure Public.

Exemplen kräver Azure PowerShell module AZ version 0,7 eller senare. Kör för att hitta versionen i ett PowerShell-fönster `Get-Module -ListAvailable Az` . Om inget anges eller om du behöver uppgradera, se [installera Azure PowerShell-modulen](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Logga in på Azure

Kör cmdleten [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) för att se tillgängliga Azure-miljöer:

```powershell
Get-AzEnvironment
```

Logga in på ditt konto som har åtkomst till molnet som du vill ansluta till och ange miljön. Det här exemplet visar hur du loggar in på ett konto som använder Azure Government molnet.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Använd miljön **AzureChinaCloud** för att få åtkomst till Kina-molnet. Använd **AzureGermanCloud** för att få åtkomst till det tyska molnet.

Om du behöver listan över platser för att skapa ett lagrings konto eller en annan resurs kan du nu fråga de platser som är tillgängliga för det valda molnet med hjälp av [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

I följande tabell visas de platser som returneras för det tyska molnet.

|Location | Visningsnamn |
|----|----|
| `germanycentral` | Tyskland, centrala|
| `germanynortheast` | Tyskland, nordöstra |


## <a name="endpoint-suffix"></a>Slut punkts suffix

Slut punktens suffix för var och en av dessa miljöer skiljer sig från den offentliga Azure-slutpunkten. Till exempel är BLOB Endpoint-suffixet för Azure Public **blob.Core.Windows.net**. För det offentliga molnet är BLOB-slutpunktens suffix **blob.Core.usgovcloudapi.net**.

### <a name="get-endpoint-using-get-azenvironment"></a>Hämta slut punkt med Get-AzEnvironment

Hämta slut punktens suffix med [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Slut punkten är miljöns *StorageEndpointSuffix* -egenskap.

Följande kodfragment visar hur du hämtar slut punktens suffix. Alla dessa kommandon returnerar något som "core.cloudapp.net" eller "core.cloudapi.de" osv. Lägg till suffixet till lagrings tjänsten för att komma åt tjänsten. Exempel: "queue.core.cloudapi.de" kommer att ha åtkomst till kötjänsten i tyskt moln.

Det här kodfragmentet hämtar alla miljöer och slut punktens suffix för var och en.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

Det här kommandot returnerar följande resultat.

| Name| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Om du vill hämta alla egenskaper för den angivna miljön anropar du **Get-AzEnvironment** och anger moln namnet. Det här kodfragmentet returnerar en lista med egenskaper. Leta efter **StorageEndpointSuffix** i listan. Följande exempel gäller för det tyska molnet.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Resultatet liknar följande värden:

|Egenskapens namn|Värde|
|----|----|
| Namn | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |

Om du bara vill hämta lagrings slut punktens suffix-egenskap hämtar du det aktuella molnet och frågar efter bara den ena egenskapen.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

Det här kommandot returnerar följande information:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Hämta slut punkt från ett lagrings konto

Du kan också granska egenskaperna för ett lagrings konto för att hämta slut punkterna:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

För ett lagrings konto i det offentliga molnet returnerar det här kommandot följande utdata:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>När du har angett miljön

Nu kan du använda PowerShell för att hantera dina lagrings konton och komma åt BLOB-, Queue-, fil-och tabell data. Mer information finns i [AZ. Storage](/powershell/module/az.storage).

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resurs grupp och ett lagrings konto för den här övningen kan du ta bort båda till gångarna genom att ta bort resurs gruppen. Alla resurser som ingår i gruppen tas bort om resursgruppen tas bort.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

* [Bevara användarinloggningar mellan PowerShell-sessioner](/powershell/azure/context-persistence)
* [Azure Government lagring](../../azure-government/compare-azure-government-global-azure.md)
* [Guide för Microsoft Azure Government utvecklare](../../azure-government/documentation-government-developer-guide.md)
* [Developer-kommentarer för Azure Kina 21Vianet-program](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentation om Azure Germany](../../germany/germany-welcome.md)