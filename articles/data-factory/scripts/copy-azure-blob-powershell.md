---
title: Kopiera data i molnet med hjälp av PowerShell
description: Det här PowerShell-skriptet kopierar data från en plats i ett Azure-Blob Storage till en annan plats i samma Blob Storage.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: article
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 169420aee32a49549a71746708bd8377a4c63b5d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100370299"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>Använd PowerShell för att skapa en Data Factory-pipeline för att kopiera data i molnet

Det här exemplet på PowerShell-skriptet skapar en pipeline i Azure Data Factory som kopierar data från en plats till en annan plats i ett Azure-Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Förutsättningar
* **Azure Storage konto**. Du kan använda blob-lagringen både som **källa** och **mottagare** för datalagringen. Om du inte har något Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-account-create.md). 
* Skapa en **blobcontainer** i Blob Storage, skapa en **indatamapp** i containern och ladda upp några filer till mappen. Du kan använda verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) för att ansluta till Azure Blob Storage, skapa en BLOB-behållare, ladda upp indatafilen och verifiera utdatafilen.

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory entiteter (länkad tjänst, data uppsättning och pipeline) på hård disken i mappen c:\ projektbevakningsmappen.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


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

Ytterligare Azure Data Factory PowerShell-skript exempel finns i [Azure Data Factory PowerShell-exempel](../samples-powershell.md).