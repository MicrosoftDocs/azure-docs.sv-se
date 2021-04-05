---
title: Läs in data stegvis med PowerShell
description: Det här PowerShell-skriptet visar hur du använder Azure Data Factory för att kopiera data stegvis från en Azure SQL Database till en Azure-Blob Storage.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 736403696ec340e547547458cb62e243e6e660b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100389849"
---
# <a name="powershell-script---incrementally-load-data-by-using-azure-data-factory"></a>PowerShell-skript – Läs in data stegvis med hjälp av Azure Data Factory

Det här exemplet på PowerShell-skriptet läser bara in nya eller uppdaterade poster från ett käll data lager till ett data lager för mottagare efter den första fullständiga kopian av data från källan till mottagaren.  

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Se [självstudie: stegvis kopiering](../tutorial-incremental-copy-powershell.md#prerequisites) för förutsättningarna för att köra det här exemplet. 

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory entiteter (länkad tjänst, data uppsättning och pipeline) på hård disken i mappen c:\ projektbevakningsmappen.

:::code language="powershell" source="~/powershell_scripts/data-factory/incremental-copy-from-azure-sql-to-blob/incremental-copy-from-azure-sql-to-blob.ps1":::

## <a name="clean-up-deployment"></a>Rensa distribution

När du har kört exempel skriptet kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Om du vill ta bort data fabriken från resurs gruppen kör du följande kommando: 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon: 

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Skapa en datafabrik. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Skapar en länkad tjänst i data fabriken. En länkad tjänst länkar ett data lager eller en beräkning till en data fabrik. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Skapar en data uppsättning i data fabriken. En data uppsättning representerar indata/utdata för en aktivitet i en pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Skapar en pipeline i data fabriken. En pipeline innehåller en eller flera aktiviteter som utför en viss åtgärd. I den här pipelinen kopierar en kopierings aktivitet data från en plats till en annan plats i ett Azure-Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Skapar en körning för pipelinen. Med andra ord körs pipelinen. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Hämtar information om körningen av aktiviteten (aktivitets körning) i pipelinen. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Ytterligare Azure Data Factory PowerShell-skript exempel finns i [Azure Data Factory PowerShell-skript](../samples-powershell.md).