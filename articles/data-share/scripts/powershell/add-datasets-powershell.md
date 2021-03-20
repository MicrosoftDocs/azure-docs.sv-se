---
title: 'PowerShell-skript: lägga till en BLOB-datauppsättning till en Azure-Dataresurs'
description: Det här PowerShell-skriptet lägger till en BLOB-datauppsättning till en befintlig resurs.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 7f2a2dded0b9c817e0700f4f93782d3b5c11ac3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92221391"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Använd PowerShell för att skapa en data resurs i Azure

Det här PowerShell-skriptet lägger till en BLOB-datauppsättning till en befintlig data resurs.

## <a name="sample-script"></a>Exempelskript

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$blobDatasetName = "<Dataset name>"
$blobContainer = "<Blob container name>"
$blobFilePath = "<Blob file path>"
$storageAccountResourceId = "<Storage account resource id>"

#Add a blob dataset to the datashare
New-AzDataShareDataSet -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $blobDataSetName -StorageAccountResourceId $storageAccountResourceId -FilePath $blobFilePath

```


## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Kommentarer |
|---|---|
| [New-AzDataShareDataSet](/powershell/module/az.datashare/new-azdatasharedataset) | Lägger till en data uppsättning till en data resurs. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Ytterligare exempel för Azure Data Share PowerShell-skript finns i [PowerShell-exemplen för Azure Data Share](../../samples-powershell.md).
