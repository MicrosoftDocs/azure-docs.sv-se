---
title: Konfigurera underhållsfönstret (förhandsversion)
description: Lär dig hur du anger den tid då planerat underhåll ska utföras på Azure SQL databaser, elastiska pooler och databaser för hanterade instanser.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/23/2021
ms.openlocfilehash: 9771c68dda6f457586f27ea45fbc52aa118e8006
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874791"
---
# <a name="configure-maintenance-window-preview"></a>Konfigurera underhållsfönstret (förhandsversion)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Konfigurera [underhållsfönstret (förhandsversion)](maintenance-window.md) för en Azure SQL databas, elastisk pool eller Azure SQL Managed Instance-databas när resursen skapas eller när som helst efter att en resurs har skapats. 

*Standardunderhållsfönstret för systemet* är 17:00 till 08:00 varje dag (lokal tid i Den Azure-region som resursen finns) för att undvika avbrott under tider med hög belastning. Om *systemstandardunderhållsfönstret* inte är den bästa tiden väljer du någon av de andra tillgängliga underhållsfönstret.

Möjligheten att ändra till en annan underhållsfönstret är inte tillgänglig för varje servicenivå eller i varje region. Mer information om tillgänglighet finns i [Underhållsfönstrets tillgänglighet.](maintenance-window.md#availability)

> [!Important]
> Att konfigurera underhållsfönstret är en långvarig asynkron åtgärd, ungefär som att ändra tjänstnivån för Azure SQL resurs. Resursen är tillgänglig under åtgärden, förutom en kort omkonfiguration som sker i slutet av åtgärden och vanligtvis varar i upp till 8 sekunder även om långvariga transaktioner avbryts. För att minimera effekten av omkonfigurationen bör du utföra åtgärden utanför tider med hög belastning.

## <a name="configure-maintenance-window-during-database-creation"></a>Konfigurera underhållsfönstret när databasen skapas 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill konfigurera underhållsfönstret när du skapar en databas, elastisk pool eller hanterad instans anger du önskat **underhållsfönstret** på **sidan Ytterligare** inställningar. 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Ange underhållsfönstret när du skapar en enkel databas eller elastisk pool

Stegvis information om hur du skapar en ny databas eller pool finns i Skapa en Azure SQL Database [enkel databas.](single-database-create-quickstart.md)

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Fliken Skapa ytterligare databasinställningar":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ange underhållsfönstret när du skapar en hanterad instans

Stegvis information om hur du skapar en ny hanterad instans finns i [Skapa en Azure SQL Managed Instance](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Fliken Skapa ytterligare inställningar för hanterad instans":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du konfigurerar underhållsfönstret med hjälp av Azure PowerShell. Du kan [installera Azure PowerShell](/powershell/azure/install-az-ps)eller använda Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsarflik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="discover-available-maintenance-windows"></a>Identifiera tillgängliga underhållsfönster

När du ställer in underhållsfönstret har varje region sina egna underhållsalternativ som motsvarar tidszonen för den region som databasen eller poolen finns i. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Upptäck SQL Database och elastiska poolunderhållsfönster 

I följande exempel returneras tillgängliga underhållsfönster för *regionen eastus2* med hjälp av cmdleten [Get-AzMaintenancePublicConfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) För databaser och elastiska pooler anger du `MaintenanceScope` till `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Upptäck SQL Managed Instance för underhåll 

I följande exempel returneras tillgängliga underhållsfönster för *regionen eastus2* med hjälp av cmdleten [Get-AzMaintenancePublicConfiguration.](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) För hanterade instanser anger du `MaintenanceScope` till `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Ange underhållsfönstret när du skapar en enkel databas

I följande exempel skapas en ny databas och underhållsfönstret anges med hjälp av cmdleten [New-AzSqlDatabase.](/powershell/module/az.sql/new-azsqldatabase) `-MaintenanceConfigurationId`måste anges till ett giltigt värde för databasens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)


   ```powershell-interactive
    # Set variables for your database
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $databaseName = "your_db_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"

    Write-host "Creating a gen5 2 vCore database with maintenance window ${maintenanceConfig} ..."
    $database = New-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Ange underhållsfönstret när du skapar en elastisk pool

I följande exempel skapas en ny elastisk pool och underhållsfönstret anges med hjälp av cmdleten [New-AzSqlElasticPool.](/powershell/module/az.sql/new-azsqlelasticpool) Underhållsfönstret är inställt på den elastiska poolen, så alla databaser i poolen har poolens underhållsschema. `-MaintenanceConfigurationId`måste anges till ett giltigt värde för poolens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)


   ```powershell-interactive
    # Set variables for your pool
    $resourceGroupName = "your_resource_group_name"
    $serverName = "your_server_name"
    $poolName = "your_pool_name"
    
    # Set selected maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Creating a Standard 50 pool with maintenance window ${maintenanceConfig} ..."
    $pool = New-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -Edition "Standard" `
      -Dtu 50 `
      -DatabaseDtuMin 10 `
      -DatabaseDtuMax 20 `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ange underhållsfönstret när du skapar en hanterad instans

I följande exempel skapas en ny hanterad instans och underhållsfönstret anges med hjälp av cmdleten [New-AzSqlInstance.](/powershell/module/az.sql/new-azsqlinstance) Underhållsfönstret är inställt på instansen, så att alla databaser i instansen har schemat för underhållsfönstret för instansen. För `-MaintenanceConfigurationId` måste *MaintenanceConfigName* vara ett giltigt värde för instansens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

I följande exempel visas hur du konfigurerar underhållsfönstret med Hjälp av Azure CLI. Du kan [installera Azure CLI](/cli/azure/install-azure-cli)eller använda Azure Cloud Shell. 

Det går bara att konfigurera underhållsfönstret med Azure CLI för SQL Managed Instance.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsarflik genom att gå till [https://shell.azure.com/cli](https://shell.azure.com/cli) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="discover-available-maintenance-windows"></a>Identifiera tillgängliga underhållsfönster

När du ställer in underhållsfönstret har varje region sina egna underhållsalternativ som motsvarar tidszonen för den region som databasen eller poolen finns i.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Upptäck SQL Database och underhåll av elastiska pooler

I följande exempel returneras de tillgängliga underhållsfönster för *regionen eastus2* med [kommandot az maintenance public-configuration list.](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) För databaser och elastiska pooler anger du `maintenanceScope` till `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Upptäck SQL Managed Instance för underhåll

I följande exempel returneras de tillgängliga underhållsfönster för *regionen eastus2* med [kommandot az maintenance public-configuration list.](/cli/azure/maintenance/public-configuration#az_maintenance_public_configuration_list) För hanterade instanser anger du `maintenanceScope` till `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Ange underhållsfönstret när du skapar en enkel databas

I följande exempel skapas en ny databas och underhållsfönstret anges med kommandot [az sql db](/cli/azure/sql/db#az_sql_db_create) create. `--maint-config-id`(eller `-m` ) måste anges till ett giltigt värde för databasens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)


   ```azurecli
    # Set variables for your database
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    databaseName="your_db_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Create database
    az sql db create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Ange underhållsfönstret när du skapar en elastisk pool

I följande exempel skapas en ny elastisk pool och underhållsfönstret anges med hjälp av [cmdleten az sql elastic-pool create.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) Underhållsfönstret är inställt på den elastiska poolen, så alla databaser i poolen har poolens underhållsschema. `--maint-config-id`(eller `-m` ) måste anges till ett giltigt värde för poolens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)


   ```azurecli
    # Set variables for your pool
    resourceGroupName="your_resource_group_name"
    serverName="your_server_name"
    poolName="your_pool_name"

    # Set selected maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Create elastic pool
    az sql elastic-pool create \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --edition GeneralPurpose \
      --family Gen5 \
      --capacity 2 \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ange underhållsfönstret när du skapar en hanterad instans

I följande exempel skapas en ny hanterad instans och underhållsfönstret anges med [hjälp av az sql mi create](/cli/azure/sql/mi#az_sql_mi_create). Underhållsfönstret är inställt på instansen, så alla databaser i instansen har schemat för underhållsfönstret för instansen. *MaintenanceConfigName* måste vara ett giltigt värde för instansens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Konfigurera underhållsfönstret för befintliga databaser


När du tillämpar ett val av underhållsfönstret på en databas kan en kort omkonfiguration (flera sekunder) uppstå i vissa fall eftersom Azure tillämpar de ändringar som krävs.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följande steg anger underhållsfönstret för en befintlig databas, elastisk pool eller hanterad instans med hjälp av Azure Portal:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Ange underhållsfönstret för en befintlig databas eller elastisk pool

1. Gå till den SQL-databas eller elastiska pool som du vill ange underhållsfönstret för.
1. På menyn **Inställningar** väljer **du Underhåll** och sedan önskad underhållsfönstret.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Sida för SQL-databasunderhåll":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Ange underhållsfönstret för en befintlig hanterad instans

1. Gå till den hanterade instans som du vill ange underhållsfönstret för.
1. I menyn **Inställningar** väljer **du Underhåll** och sedan önskad underhållsfönstret.

   :::image type="content" source="media/maintenance-window-configure/maintenance-mi.png" alt-text="Underhållssida för SQL-hanterad instans":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Ange underhållsfönstret för en befintlig databas

I följande exempel anges underhållsfönstret för en befintlig databas med hjälp av cmdleten [Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase) `-MaintenanceConfigurationId`måste anges till ett giltigt värde för databasens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_2"

    Write-host "Changing database maintenance window to ${maintenanceConfig} ..."
    $database = Set-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -MaintenanceConfigurationId $maintenanceConfig
    $database
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Ange underhållsfönstret för en befintlig elastisk pool

I följande exempel anges underhållsfönstret för en befintlig elastisk pool med hjälp av cmdleten [Set-AzSqlElasticPool.](/powershell/module/az.sql/set-azsqlelasticpool) Det är viktigt att se till att `$maintenanceConfig` värdet är ett giltigt värde för poolens region.  Information om hur du hämtar giltiga värden för en region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)

   ```powershell-interactive
    # Select different maintenance window
    $maintenanceConfig = "SQL_EastUS2_DB_1"
    
    Write-host "Changing pool maintenance window to ${maintenanceConfig} ..."
    $pool = Set-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName `
      -MaintenanceConfigurationId $maintenanceConfig
    $pool
   ```



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Ange underhållsfönstret för en befintlig hanterad instans

I följande exempel anges underhållsfönstret på en befintlig hanterad instans med hjälp av [cmdleten Set-AzSqlInstance.](/powershell/module/az.sql/set-azsqlinstance) Det är viktigt att se till att `$maintenanceConfig` värdet måste vara ett giltigt värde för instansens region.  Information om hur du hämtar giltiga värden för en region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

I följande exempel visas hur du konfigurerar underhållsfönstret med Hjälp av Azure CLI. Du kan [installera Azure CLI](/cli/azure/install-azure-cli)eller använda Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Ange underhållsfönstret för en befintlig databas

I följande exempel anges underhållsfönstret för en befintlig databas med kommandot [az sql db](/cli/azure/sql/db#az_sql_db_update) update. `--maint-config-id`(eller `-m` ) måste anges till ett giltigt värde för databasens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_2"

    # Update database
    az sql db update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Ange underhållsfönstret för en befintlig elastisk pool

I följande exempel anges underhållsfönstret för en befintlig elastisk pool med kommandot [az sql elastic-pool update.](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) Det är viktigt att se till att `maintenanceConfig` värdet är ett giltigt värde för poolens region.  Information om hur du hämtar giltiga värden för en region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)

   ```azurecli
    # Select different maintenance window
    maintenanceConfig="SQL_EastUS2_DB_1"

    # Update pool
    az sql elastic-pool update \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName \
      --maint-config-id $maintenanceConfig
   ```

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Ange underhållsfönstret för en befintlig hanterad instans

I följande exempel anges underhållsfönstret med [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update). Underhållsfönstret är inställt på instansen, så alla databaser i instansen har schemat för underhållsfönstret för instansen. För `-MaintenanceConfigurationId` måste *MaintenanceConfigName* vara ett giltigt värde för instansens region. Information om hur du hämtar giltiga värden för din region finns [i Identifiera tillgängliga underhållsfönster.](#discover-available-maintenance-windows)

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Rensa resurser

Se till att ta bort resurser som inte behövs när du är klar med dem för att undvika onödiga kostnader.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till DEN SQL-databas eller elastiska pool som du inte längre behöver.
1. På menyn **Översikt** väljer du alternativet för att ta bort resursen.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   # Delete database
   Remove-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName

   # Delete elastic pool
   Remove-AzSqlElasticPool `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -ElasticPoolName $poolName
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

   ```azurecli
   az sql db delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $databaseName

   az sql elastic-pool delete \
      --resource-group $resourceGroupName \
      --server $serverName \
      --name $poolName
   ```

-----

## <a name="next-steps"></a>Nästa steg

- Mer information om underhållsfönstret finns i [Underhållsfönstret (förhandsversion)](maintenance-window.md).
- Mer information finns i Vanliga frågor [och svar om underhållsfönstret.](maintenance-window-faq.yml)
- Mer information om hur du optimerar prestanda finns [i Övervakning och prestandajustering i Azure SQL Database och Azure SQL Managed Instance](monitor-tune-overview.md).
