---
title: Exempel på Azure PowerShell-skript – Ta bort containrar efter prefix | Microsoft Docs
description: Läs ett exempel som visar hur du tar bort Azure Blob Storage baserat på ett prefix i behållar namnet med hjälp av Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 06/13/2017
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b95c5ab243fbd938e8a7eb1d3b9619b0d46fb046
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89072965"
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Ta bort containrar baserat på containerns namnprefix

Det här skriptet tar bort containrar i Azure Blob Storage baserat på ett prefix i containernamnet.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, återstående containrar och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att ta bort containrar baserat på containerns namnprefix. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Hämtar ett angivet lagringskonto eller alla lagringskonton i en resursgrupp eller i prenumerationen. |
| [Get-AzStorageContainer](/powershell/module/az.storage/Get-AzStorageContainer) | Listar de lagringscontainrar som är associerade till ett lagringskonto. |
| [Remove-AzStorageContainer](/powershell/module/az.storage/Remove-AzStorageContainer) | Tar bort den angivna lagringscontainern. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för lagring finns i [PowerShell-exempel för Azure Blob Storage](../blobs/storage-samples-blobs-powershell.md).
