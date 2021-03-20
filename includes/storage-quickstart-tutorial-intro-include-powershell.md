---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "67187567"
---
## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Visa listan över platser med hjälp av följande kodexempel och hitta det du vill använda. I det här exemplet används **eastus**. Lagra platsen i en variabel och använd variabeln så att du kan ändra den på en enda plats.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Skapa ett standardmässigt lagringskonto för generell användning med LRS-replikering med hjälp av [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Hämta sedan lagringskontokontexten som definierar det lagringskonto som du vill använda. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger. Använd följande exempel för att skapa ett lagringskonto med namnet *mystorageaccount* med lokalt redundant lagring (LRS) och blobkryptering (aktiverat som standard).

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
