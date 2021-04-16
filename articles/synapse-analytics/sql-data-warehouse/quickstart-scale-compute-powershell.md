---
title: 'Snabbstart: Skala beräkning för dedikerad SQL-pool (tidigare SQL DW) (Azure PowerShell)'
description: Du kan skala beräkning för dedikerad SQL-pool (tidigare SQL DW) med hjälp av Azure PowerShell.
services: synapse-analytics
author: Antvgski
ms.author: anvang
manager: craigg
ms.reviewer: igorstan
ms.date: 04/17/2018
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 73718f0e8e82e7d35ed1ced98f45c1e125a55a00
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107534235"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Snabbstart: Skala beräkning för dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell

Du kan skala beräkning för dedikerad SQL-pool (tidigare SQL DW) med hjälp av Azure PowerShell. [Skala ut beräkning](sql-data-warehouse-manage-compute-overview.md) för bättre prestanda eller skala tillbaka beräkning för att spara kostnader.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Den här snabbstarten förutsätter att du redan har en dedikerad SQL-pool (tidigare SQL DW) som du kan skala. Om du behöver skapa en använder du Skapa och anslut [– portalen](create-data-warehouse-portal.md) för att skapa en dedikerad SQL-pool (tidigare SQL DW) med namnet **mySampleDataWarehouse.**

## <a name="log-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration [med kommandot Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör [du Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standardprenumerationen kör [du Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Leta upp information om informationslager

Leta upp databasens namn, servernamnet och resursgruppen för det informationslager som du tänker pausa och återuppta.

Följ de här anvisningarna för att hitta platsen för ditt informationslager.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Klicka **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra navigeringssidan i Azure Portal.
3. Välj **mySampleDataWarehouse på** sidan **Azure Synapse Analytics (tidigare SQL DW)** för att öppna informationslagret.

    ![Servernamn och resursgrupp](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Anteckna namnet på informationslagret. Detta ska användas som databasnamn. Kom ihåg att ett informationslager är en typ av databas. Anteckna även servernamnet och resursgruppen. Du använder servernamnet och resursgruppens namn i kommandona pausa och återuppta.
5. Använd bara den första delen av servernamnet i PowerShell-cmdletarna. I föregående bild är det fullständiga servernamnet sqlpoolservername.database.windows.net. Vi använder **sqlpoolservername** som servernamn i PowerShell-cmdleten.

## <a name="scale-compute"></a>Skala beräkning

I dedikerad SQL-pool (tidigare SQL DW) kan du öka eller minska beräkningsresurser genom att justera informationslagerenheter. I [Skapa och ansluta – portal](create-data-warehouse-portal.md) skapades **mySampleDataWarehouse** och initierades med 400 DWU. Följande steg justerar DWU för **mySampleDataWarehouse**.

Om du vill ändra informationslagerenheter använder [du PowerShell-cmdleten Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) I följande exempel anges informationslagerenheterna till DW300c för databasen **mySampleDataWarehouse**, som finns i resursgruppen **resourcegroupname** på servern **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Kontrollera tillstånd för informationslager

Om du vill se det aktuella tillståndet för informationslagret använder du [PowerShell-cmdleten Get-AzSqlDatabase.](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Denna cmdlet visar tillståndet för **databasen mySampleDataWarehouse** i ResourceGroup **resourcegroupname** och servern **sqlpoolservername.database.windows.net**.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Resultatet liknar detta:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

Du kan se **Status** för databasen i dina utdata. I det här fallet kan du se att den här databasen är online.  När du kör kommandot bör du få statusvärdet Online, Pausad, Återupptar, Skalning eller Pausad.

Kör följande kommando om du bara vill se status:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du skalar beräkning för dedikerad SQL-pool (tidigare SQL DW). Om du vill veta mer om dedikerad SQL-pool (tidigare SQL DW) fortsätter du till självstudien för att läsa in data.

> [!div class="nextstepaction"]
>[Läsa in data till en dedikerad SQL-pool](load-data-from-azure-blob-storage-using-copy.md)
