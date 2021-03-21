---
title: Beräkna storleken på en BLOB-behållare med PowerShell
titleSuffix: Azure Storage
description: Beräkna storleken på en behållare i Azure Blob Storage genom att summera storleken på varje blob.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 87ef18530c549396b7d8fe1ec4ff0e08cb8535e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98784282"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Beräkna storleken på en BLOB-behållare med PowerShell

Det här skriptet beräknar storleken på en behållare i Azure Blob Storage genom att summera blobbarnas storlek i behållaren.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Det här PowerShell-skriptet ger en uppskattad storlek för containern och ska inte användas för faktureringsberäkningar. Information om ett skript som beräknar containerstorlek för faktureringsändamål finns i [Beräkna storleken på en Blob Storage-container för faktureringsändamål](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, containern och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att beräkna storleken på Blob-lagringscontainern. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Hämtar ett angivet lagringskonto eller alla lagringskonton i en resursgrupp eller i prenumerationen. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Visar en lista över blobar i en container. |

## <a name="next-steps"></a>Nästa steg

Information om ett skript som beräknar containerstorlek för faktureringsändamål finns i [Beräkna storleken på en Blob Storage-container för faktureringsändamål](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för lagring finns i [PowerShell-exempel för Azure Storage](../blobs/storage-samples-blobs-powershell.md).
