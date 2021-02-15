---
title: Kopiera data i bulk med PowerShell
description: Det här PowerShell-skriptet visar hur du använder Azure Data Factory för att kopiera data från ett käll data lager till ett mål data lager i bulk.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 16cb08d49efcd46e2746c5031793af8b747a2b31
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373580"
---
# <a name="powershell-script---copy-multiple-tables-in-bulk-by-using-azure-data-factory"></a>PowerShell-skript – kopiera flera tabeller i bulk genom att använda Azure Data Factory

Det här exemplet på PowerShell-skriptet kopierar data från flera tabeller i Azure SQL Database till Azure Synapse Analytics.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

Se [självstudie: Mass kopiering](../tutorial-bulk-copy.md#prerequisites) för krav för att köra det här exemplet.

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory entiteter (länkad tjänst, data uppsättning och pipeline) på hård disken i mappen c:\ projektbevakningsmappen.

[!code-powershell[main](../../../powershell_scripts/data-factory/bulk-copy-from-sql-databse-to-sql-data-warehouse/bulk-copy-from-sql-database-to-sql-data-warehouse.ps1 "Bulk copy from Azure SQL Database => Azure Synapse Analytics")]

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
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-azdatafactoryv2) | Skapa en datafabrik. |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-azdatafactoryv2linkedservice) | Skapar en länkad tjänst i data fabriken. En länkad tjänst länkar ett data lager eller en beräkning till en data fabrik. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/set-azdatafactoryv2dataset) | Skapar en data uppsättning i data fabriken. En data uppsättning representerar indata/utdata för en aktivitet i en pipeline. | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-azdatafactoryv2pipeline) | Skapar en pipeline i data fabriken. En pipeline innehåller en eller flera aktiviteter som utför en viss åtgärd. I den här pipelinen kopierar en kopierings aktivitet data från en plats till en annan plats i ett Azure-Blob Storage. |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-azdatafactoryv2pipeline) | Skapar en körning för pipelinen. Med andra ord körs pipelinen. |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-azdatafactoryv2activityrun) | Hämtar information om körningen av aktiviteten (aktivitets körning) i pipelinen. 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).

Ytterligare Azure Data Factory PowerShell-skript exempel finns i [Azure Data Factory PowerShell-skript](../samples-powershell.md).