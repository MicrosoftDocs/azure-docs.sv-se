---
title: Utföra Azure Table Storage-åtgärder med PowerShell | Microsoft Docs
description: Lär dig hur du kör vanliga uppgifter som att skapa, fråga och ta bort data från Azure Table Storage-kontot med hjälp av PowerShell.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a7502d48095fe18b983c1971d5145f51e766c95
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93306912"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Utför Azure Table Storage-åtgärder med Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage är ett NoSQL-datalager som du kan använda för att lagra och fråga enorma mängder strukturerade, icke-relationella data. Huvud komponenterna i tjänsten är tabeller, entiteter och egenskaper. En tabell är en samling entiteter. En entitet är en uppsättning egenskaper. Varje entitet kan ha upp till 252 egenskaper, vilket är alla namn/värde-par. Den här artikeln förutsätter att du redan är bekant med Azure Table Storage-tjänstens koncept. Detaljerad information finns i [förstå tabell tjänstens data modell](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) och [komma igång med Azure Table Storage med hjälp av .net](../../cosmos-db/tutorial-develop-table-dotnet.md).

Den här instruktions artikeln beskriver vanliga åtgärder för Azure Table Storage. Lär dig att: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägg till tabell enheter
> * Fråga en tabell
> * Ta bort tabell enheter
> * Ta bort en tabell

Den här instruktions artikeln visar hur du skapar ett nytt Azure Storage konto i en ny resurs grupp så att du enkelt kan ta bort det när du är klar. Om du hellre vill använda ett befintligt lagrings konto kan du göra det i stället.

I exemplen krävs AZ PowerShell-moduler `Az.Storage (1.1.0 or greater)` och `Az.Resources (1.2.0 or greater)` . Kör för att hitta versionen i ett PowerShell-fönster `Get-Module -ListAvailable Az*` . Om inget visas, eller om du behöver uppgradera, se [installera Azure PowerShell modul](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Om du använder den här Azure-funktionen från PowerShell måste du ha `Az` installerat modulen. Den aktuella versionen av `AzTable` är inte kompatibel med den äldre AzureRM-modulen.
> Följ de [senaste installations anvisningarna för att installera AZ-modulen](/powershell/azure/install-az-ps) om det behövs.

När Azure PowerShell har installerats eller uppdaterats måste du installera module **AzTable**, som har kommandon för att hantera entiteterna. Om du vill installera den här modulen kör du PowerShell som administratör och använder kommandot **install-module** .

> [!IMPORTANT]
> För kompatibilitetsproblem kan vi fortfarande publicera samma modul under det gamla namnet `AzureRmStorageTables` i PowerShell-galleriet. Det här dokumentet refererar endast till det nya namnet.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Add-AzAccount` och följ anvisningarna på skärmen.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta lista över platser

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. I de här exemplen används **öster**. Lagra det här värdet på variabel **platsen** för framtida användning.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Lagra resurs grupps namnet i en variabel för framtida användning. I det här exemplet skapas en resurs grupp med namnet *pshtablesrg* i regionen *östra* .

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa ett allmänt standard lagrings konto med lokalt redundant lagring (LRS) med [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Se till att ange ett unikt lagrings konto namn. Sedan hämtar du den kontext som representerar lagrings kontot. När du agerar på ett lagrings konto kan du referera till kontexten i stället för att flera gånger ange dina autentiseringsuppgifter.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Skapa en ny tabell

Använd cmdleten [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) för att skapa en tabell. I det här exemplet kallas tabellen `pshtesttable` .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Hämta en lista över tabeller i lagrings kontot

Hämta en lista över tabeller i lagrings kontot med hjälp av [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Hämta en referens till en speciell tabell

Om du vill utföra åtgärder i en tabell behöver du en referens till den aktuella tabellen. Hämta en referens med [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Referens CloudTable-egenskap för en speciell tabell

> [!IMPORTANT]
> Användningen av CloudTable är obligatorisk när du arbetar med **AzTable** PowerShell-modulen. Anropa kommandot **Get-AzStorageTable** för att hämta referensen till det här objektet. Det här kommandot skapar även tabellen om den inte redan finns.

Om du vill utföra åtgärder för en tabell med **AzTable** måste du ha en referens till egenskapen CloudTable för en speciell tabell.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Ta bort en tabell

Om du vill ta bort en tabell använder du [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). Den här cmdleten tar bort tabellen, inklusive alla dess data.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du har skapat en ny resurs grupp och ett lagrings konto i början av den här instruktionen kan du ta bort alla till gångar som du har skapat i den här övningen genom att ta bort resurs gruppen. Det här kommandot tar bort alla resurser som ingår i gruppen samt själva resurs gruppen.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln har du lärt dig om vanliga Azure Table Storage-åtgärder med PowerShell, inklusive hur du: 

> [!div class="checklist"]
> * Skapa en tabell
> * Hämta en tabell
> * Lägg till tabell enheter
> * Fråga en tabell
> * Ta bort tabell enheter
> * Ta bort en tabell

Mer information finns i följande artiklar

* [Storage PowerShell cmdletar](/powershell/module/az.storage#storage)

* [Arbeta med Azure-tabeller från PowerShell-AzureRmStorageTable/AzTable PS module v 2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.