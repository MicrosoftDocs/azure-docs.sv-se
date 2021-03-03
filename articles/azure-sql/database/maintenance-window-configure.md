---
title: Konfigurera underhålls fönstret (förhands granskning)
description: Lär dig hur du ställer in tiden när planerat underhåll ska utföras på dina Azure SQL-databaser, elastiska pooler och hanterade instans databaser.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/02/2021
ms.openlocfilehash: ae31b329cbea82ac664af24e42ec58d094b3a7ae
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664011"
---
# <a name="configure-maintenance-window-preview"></a>Konfigurera underhålls fönstret (förhands granskning)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]


Konfigurera [underhålls perioden (förhands granskning)](maintenance-window.md) för en Azure SQL-databas, elastisk pool eller Azure SQL-hanterad instans databas under skapandet av en resurs, eller när en resurs har skapats. 

*Systemets standard* underhålls period är 17 till 8.00 dagligen (lokal tid för den Azure-region där resursen finns) för att undvika avbrott i arbets tiden. Om *systemets standard* underhålls period inte är den bästa tiden väljer du någon av de andra tillgängliga underhålls Fönstren.

Möjligheten att ändra till en annan underhålls period är inte tillgänglig för varje service nivå eller i varje region. Mer information om tillgänglighet finns i [tillgänglighet för underhålls fönster](maintenance-window.md#availability).


## <a name="configure-maintenance-window-during-database-creation"></a>Konfigurera underhålls fönstret när databasen skapas 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Konfigurera underhålls fönstret när du skapar en databas, en elastisk pool eller en hanterad instans genom att ange önskad **underhålls period** på sidan **ytterligare inställningar** . 

## <a name="set-the-maintenance-window-while-creating-a-single-database-or-elastic-pool"></a>Ange underhålls perioden när du skapar en enskild databas eller elastisk pool

Steg-för-steg-information om hur du skapar en ny databas eller pool finns i [skapa en Azure SQL Database enskild databas](single-database-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings.png" alt-text="Fliken Skapa ytterligare databas inställningar":::


## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ange underhålls perioden när du skapar en hanterad instans

Steg för steg-information om hur du skapar en ny hanterad instans finns i [skapa en hanterad Azure SQL-instans](../managed-instance/instance-create-quickstart.md).

   :::image type="content" source="media/maintenance-window-configure/additional-settings-mi.png" alt-text="Fliken Skapa hanterad instans ytterligare inställningar":::




# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

I följande exempel visas hur du konfigurerar underhålls fönstret med hjälp av Azure PowerShell. Du kan [installera Azure PowerShell](/powershell/azure/install-az-ps)eller använda Azure Cloud Shell.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.


## <a name="discover-available-maintenance-windows"></a>Identifiera tillgängliga underhålls fönster

När du ställer in underhålls fönstret har varje region ett eget alternativ för underhålls perioden som motsvarar timezone för regionen som databasen eller poolen finns i. 

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Identifiera SQL Database underhålls fönster för elastisk pool 

I följande exempel returneras de tillgängliga underhålls Fönstren för *eastus2* -regionen med hjälp av [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) -cmdleten. För databaser och elastiska pooler anger `MaintenanceScope` du till `SQLDB` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLDB"}
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Identifiera underhålls fönster för SQL-hanterad instans 

I följande exempel returneras de tillgängliga underhålls Fönstren för *eastus2* -regionen med hjälp av [Get-AzMaintenancePublicConfiguration](/powershell/module/az.maintenance/get-azmaintenancepublicconfiguration) -cmdleten. För hanterade instanser anger `MaintenanceScope` du till `SQLManagedInstance` .

   ```powershell-interactive
   $location = "eastus2"

   Write-Host "Available maintenance schedules in ${location}:"
   $configurations = Get-AzMaintenancePublicConfiguration
   $configurations | ?{ $_.Location -eq $location -and $_.MaintenanceScope -eq "SQLManagedInstance"}
   ```


## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Ange underhålls perioden när du skapar en enskild databas

I följande exempel skapas en ny databas och underhålls fönstret anges med cmdleten [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) . `-MaintenanceConfigurationId`Måste anges till ett giltigt värde för databasens region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).


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



## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Ange underhålls perioden när du skapar en elastisk pool

I följande exempel skapas en ny elastisk pool och underhålls fönstret anges med cmdleten [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) . Underhålls perioden är inställd på den elastiska poolen, så alla databaser i poolen har en Pools schema för underhålls fönster. `-MaintenanceConfigurationId`Måste anges till ett giltigt värde för poolens region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ange underhålls perioden när du skapar en hanterad instans

I följande exempel skapas en ny hanterad instans och underhålls fönstret anges med cmdleten [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) . Underhålls fönstret är inställt på instansen, så alla databaser i instansen har instansen underhålls fönster schema. För `-MaintenanceConfigurationId` måste *MaintenanceConfigName* vara ett giltigt värde för din instanss region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).


   ```powershell
   New-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -Location "your_mi_location" `
     -SubnetId /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

I följande exempel visas hur du konfigurerar underhålls fönstret med Azure CLI. Du kan [Installera Azure CLI](/cli/azure/install-azure-cli)eller använda Azure Cloud Shell. 

Att konfigurera underhålls fönstret med Azure CLI är bara tillgängligt för SQL-hanterad instans.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/cli](https://shell.azure.com/cli) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="discover-available-maintenance-windows"></a>Identifiera tillgängliga underhålls fönster

När du ställer in underhålls fönstret har varje region ett eget alternativ för underhålls perioden som motsvarar timezone för regionen som databasen eller poolen finns i.

### <a name="discover-sql-database-and-elastic-pool-maintenance-windows"></a>Identifiera SQL Database underhålls fönster för elastisk pool

I följande exempel returneras de tillgängliga underhålls Fönstren för regionen *eastus2* med kommandot [AZ Maintenance Public-Configuration List](/cli/azure/ext/maintenance/maintenance/public-configuration?view=azure-cli-latest&preserve-view=true#ext_maintenance_az_maintenance_public_configuration_list) . För databaser och elastiska pooler anger `maintenanceScope` du till `SQLDB` .

   ```azurecli
   location="eastus2"

   az maintenance public-configuration list --query "[?location=='$location'&&contains(maintenanceScope,'SQLDB')]"
   ```

### <a name="discover-sql-managed-instance-maintenance-windows"></a>Identifiera underhålls fönster för SQL-hanterad instans

I följande exempel returneras de tillgängliga underhålls Fönstren för regionen *eastus2* med kommandot [AZ Maintenance Public-Configuration List](/cli/azure/ext/maintenance/maintenance/public-configuration?view=azure-cli-latest&preserve-view=true#ext_maintenance_az_maintenance_public_configuration_list) . För hanterade instanser anger `maintenanceScope` du till `SQLManagedInstance` .

   ```azurecli
   az maintenance public-configuration list --query "[?location=='eastus2'&&contains(maintenanceScope,'SQLManagedInstance')]"
   ```

## <a name="set-the-maintenance-window-while-creating-a-single-database"></a>Ange underhålls perioden när du skapar en enskild databas

I följande exempel skapas en ny databas och underhålls fönstret anges med kommandot [AZ SQL DB Create](/cli/azure/sql/db#az_sql_db_create) . `--maint-config-id`(Eller `-m` ) måste anges till ett giltigt värde för databasens region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).


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

## <a name="set-the-maintenance-window-while-creating-an-elastic-pool"></a>Ange underhålls perioden när du skapar en elastisk pool

I följande exempel skapas en ny elastisk pool och underhålls fönstret används med [AZ SQL Elastic-pool Create-](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create) cmdlet. Underhålls perioden är inställd på den elastiska poolen, så alla databaser i poolen har en Pools schema för underhålls fönster. `--maint-config-id`(Eller `-m` ) måste anges till ett giltigt värde för poolens region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).


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

## <a name="set-the-maintenance-window-while-creating-a-managed-instance"></a>Ange underhålls perioden när du skapar en hanterad instans

I följande exempel skapas en ny hanterad instans och underhålls fönstret anges med [AZ SQL mi Create](/cli/azure/sql/mi#az_sql_mi_create). Underhålls fönstret är inställt på instansen, så alla databaser i instansen har instansen underhålls fönster schema. *MaintenanceConfigName* måste vara ett giltigt värde för din instanss region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi create -g mygroup -n myinstance -l mylocation -i -u myusername -p mypassword --subnet /subscriptions/{SubID}/resourceGroups/{ResourceGroup}/providers/Microsoft.Network/virtualNetworks/{VNETName}/subnets/{SubnetName} -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}
   ```

-----

## <a name="configure-maintenance-window-for-existing-databases"></a>Konfigurera underhålls perioden för befintliga databaser


När du tillämpar ett val av en underhålls period i en databas, kan en kort växling (flera sekunder) uppstå i vissa fall när Azure tillämpar de nödvändiga ändringarna.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Följande steg anger underhålls perioden för en befintlig databas, elastisk pool eller hanterad instans med hjälp av Azure Portal:


## <a name="set-the-maintenance-window-for-an-existing-database-or-elastic-pool"></a>Ange underhålls perioden för en befintlig databas eller elastisk pool

1. Gå till den SQL-databas eller elastiska pool som du vill ange underhålls perioden för.
1. Välj **Underhåll** på menyn **Inställningar** och välj sedan önskad underhålls period.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Sidan underhåll av SQL-databas":::


## <a name="set-the-maintenance-window-for-an-existing-managed-instance"></a>Ange underhålls perioden för en befintlig hanterad instans

1. Navigera till den hanterade instans som du vill ange underhålls perioden för.
1. Välj **Underhåll** på menyn **Inställningar** och välj sedan önskad underhålls period.

   :::image type="content" source="media/maintenance-window-configure/maintenance.png" alt-text="Sidan underhåll av SQL-databas":::



# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Ange underhålls perioden för en befintlig databas

I följande exempel anges underhålls perioden för en befintlig databas med hjälp av cmdleten [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) . `-MaintenanceConfigurationId`Måste anges till ett giltigt värde för databasens region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).

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

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Ange underhålls perioden för en befintlig elastisk pool

I följande exempel anges underhålls fönstret för en befintlig elastisk pool med cmdleten [set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool) . Det är viktigt att se till att `$maintenanceConfig` värdet är ett giltigt värde för poolens region.  För att få giltiga värden för en region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).

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



## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Ange underhålls perioden för en befintlig hanterad instans

I följande exempel anges underhålls fönstret för en befintlig hanterad instans med cmdleten [set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) . Det är viktigt att se till att `$maintenanceConfig` värdet måste vara ett giltigt värde för din instanss region.  För att få giltiga värden för en region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).


   ```powershell-interactive
   Set-AzSqlInstance -Name "your_mi_name" `
     -ResourceGroupName "your_resource_group_name" `
     -MaintenanceConfigurationId "/subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MaintenanceConfigName}" `
     -AsJob
   ```

# <a name="cli"></a>[CLI](#tab/azure-cli)

I följande exempel visas hur du konfigurerar underhålls fönstret med Azure CLI. Du kan [Installera Azure CLI](/cli/azure/install-azure-cli)eller använda Azure Cloud Shell.

## <a name="set-the-maintenance-window-for-an-existing-database"></a>Ange underhålls perioden för en befintlig databas

I följande exempel anges underhålls perioden för en befintlig databas med hjälp av kommandot [AZ SQL DB Update](/cli/azure/sql/db#az_sql_db_update) . `--maint-config-id`(Eller `-m` ) måste anges till ett giltigt värde för databasens region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).

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

## <a name="set-the-maintenance-window-on-an-existing-elastic-pool"></a>Ange underhålls perioden för en befintlig elastisk pool

I följande exempel anges underhålls fönstret för en befintlig elastisk pool med kommandot [AZ SQL Elastic-pool Update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update) . Det är viktigt att se till att `maintenanceConfig` värdet är ett giltigt värde för poolens region.  För att få giltiga värden för en region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).

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

## <a name="set-the-maintenance-window-on-an-existing-managed-instance"></a>Ange underhålls perioden för en befintlig hanterad instans

I följande exempel anges underhålls fönstret med [AZ SQL mi Update](/cli/azure/sql/mi#az_sql_mi_update). Underhålls fönstret är inställt på instansen, så alla databaser i instansen har instansen underhålls fönster schema. För `-MaintenanceConfigurationId` måste *MaintenanceConfigName* vara ett giltigt värde för din instanss region. För att få giltiga värden för din region, se [identifiera tillgängliga underhålls fönster](#discover-available-maintenance-windows).

   ```azurecli
   az sql mi update -g mygroup  -n myinstance -m /subscriptions/{SubID}/providers/Microsoft.Maintenance/publicMaintenanceConfigurations/SQL_{Region}_{MainteanceConfigName}
   ```

-----

## <a name="cleanup-resources"></a>Rensa resurser

Se till att ta bort resurser som inte behövs när du är klar med dem för att undvika onödiga kostnader.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigera till den SQL-databas eller elastisk pool som du inte längre behöver.
1. I **översikts** menyn väljer du alternativet för att ta bort resursen.


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

- Mer information om underhålls fönstret finns i [underhålls period (för hands version)](maintenance-window.md).
- Mer information finns i [vanliga frågor och svar om underhålls fönster](maintenance-window-faq.yml).
- Mer information om hur du optimerar prestanda finns [i övervakning och prestanda justering i Azure SQL Database och Azure SQL-hanterad instans](monitor-tune-overview.md).
