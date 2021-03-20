---
title: Hantera API-resurser för Azure Cosmos DB Core (SQL) med hjälp av PowerShell
description: Hantera API-resurser för Azure Cosmos DB Core (SQL) med hjälp av PowerShell.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: 2d99a760e3bf44febcee05bf9827479616bf7bd6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333451"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-using-powershell"></a>Hantera API-resurser för Azure Cosmos DB Core (SQL) med PowerShell
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I följande guide beskrivs hur du använder PowerShell för att skripta och automatisera hanteringen av API-resurser i Azure Cosmos DB Core (SQL), inklusive Cosmos-konto, databas, behållare och data flöde. PowerShell-cmdletar för andra API: er i PowerShell-exempel för [Cassandra](powershell-samples-cassandra.md), PowerShell-exempel för [MongoDB-API](powershell-samples-mongodb.md), PowerShell-exempel för [Gremlin](powershell-samples-gremlin.md), [PowerShell-exempel för tabell](powershell-samples-table.md)

> [!NOTE]
> I exemplen i den här artikeln används [AZ. CosmosDB](/powershell/module/az.cosmosdb) hanterings-cmdletar. Se referens sidan för [AZ. CosmosDB](/powershell/module/az.cosmosdb) -API för de senaste ändringarna.

För plattforms oberoende hantering av Azure Cosmos DB kan du använda `Az` `Az.CosmosDB` cmdletarna och med [plattforms oberoende PowerShell](/powershell/scripting/install/installing-powershell), samt [Azure CLI](manage-with-cli.md), [REST API][rp-rest-api]eller [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Komma igång

Följ anvisningarna i [så här installerar och konfigurerar du Azure PowerShell][powershell-install-configure] för att installera och logga in på ditt Azure-konto i PowerShell.

> [!IMPORTANT]
> Det går inte att byta namn på Azure Cosmos DB resurser eftersom detta strider mot hur Azure Resource Manager fungerar med resurs-URI: er.

## <a name="azure-cosmos-accounts"></a>Azure Cosmos-konton

Följande avsnitt visar hur du hanterar Azure Cosmos-kontot, inklusive:

* [Skapa ett Azure Cosmos-konto](#create-account)
* [Uppdatera ett Azure Cosmos-konto](#update-account)
* [Lista alla Azure Cosmos-konton i en prenumeration](#list-accounts)
* [Skaffa ett Azure Cosmos-konto](#get-account)
* [Ta bort ett Azure Cosmos-konto](#delete-account)
* [Uppdatera taggar för ett Azure Cosmos-konto](#update-tags)
* [Lista nycklar för ett Azure Cosmos-konto](#list-keys)
* [Återskapa nycklar för ett Azure Cosmos-konto](#regenerate-keys)
* [Visa en lista med anslutnings strängar för ett Azure Cosmos-konto](#list-connection-strings)
* [Ändra prioritet för redundans för ett Azure Cosmos-konto](#modify-failover-priority)
* [Utlös en manuell redundansväxling för ett Azure Cosmos-konto](#trigger-manual-failover)
* [Visa en lista över resurs lås för ett Azure Cosmos DB konto](#list-account-locks)

### <a name="create-an-azure-cosmos-account"></a><a id="create-account"></a> Skapa ett Azure Cosmos-konto

Det här kommandot skapar ett Azure Cosmos DB Database-konto med [flera regioner][distribute-data-globally], [Automatisk redundans](how-to-manage-database-account.md#automatic-failover) och avbindad-föråldrad [konsekvens princip](consistency-levels.md).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "BoundedStaleness"
$maxStalenessInterval = 300
$maxStalenessPrefix = 100000
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$true `
    -DefaultConsistencyLevel $consistencyLevel `
    -MaxStalenessIntervalInSeconds $maxStalenessInterval `
    -MaxStalenessPrefix $maxStalenessPrefix
```

* `$resourceGroupName` Den Azure-resurs grupp som Cosmos-kontot ska distribueras till. Det måste redan finnas.
* `$locations` Regionerna för databas kontot, regionen med `FailoverPriority 0` är Skriv regionen.
* `$accountName` Namnet på Azure Cosmos-kontot. Måste vara unik, gemener, innehålla enbart alfanumeriska tecken och-tecken, och mellan 3 och 31 tecken.
* `$apiKind` Den typ av Cosmos-konto som ska skapas. Mer information finns i [API: er i Cosmos DB](introduction.md#simplified-application-development).
* `$consistencyPolicy`, `$maxStalenessInterval` , och `$maxStalenessPrefix` standard konsekvens nivån och inställningarna för Azure Cosmos-kontot. Mer information finns i [konsekvens nivåer i Azure Cosmos DB](consistency-levels.md).

Azure Cosmos-konton kan konfigureras med IP-brandvägg, Virtual Network tjänst slut punkter och privata slut punkter. Information om hur du konfigurerar IP-brandväggen för Azure Cosmos DB finns i [Konfigurera IP-](how-to-configure-firewall.md)brandvägg. Information om hur du aktiverar tjänstens slut punkter för Azure Cosmos DB finns i [Konfigurera åtkomst från virtuella nätverk](how-to-configure-vnet-service-endpoint.md). Information om hur du aktiverar privata slut punkter för Azure Cosmos DB finns i [Konfigurera åtkomst från privata slut punkter](how-to-configure-private-endpoints.md).

### <a name="list-all-azure-cosmos-accounts-in-a-resource-group"></a><a id="list-accounts"></a> Lista alla Azure Cosmos-konton i en resurs grupp

Det här kommandot visar alla Azure Cosmos-konton i en resurs grupp.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName
```

### <a name="get-the-properties-of-an-azure-cosmos-account"></a><a id="get-account"></a> Hämta egenskaperna för ett Azure Cosmos-konto

Med det här kommandot kan du hämta egenskaperna för ett befintligt Azure Cosmos-konto.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName
```

### <a name="update-an-azure-cosmos-account"></a><a id="update-account"></a> Uppdatera ett Azure Cosmos-konto

Med det här kommandot kan du uppdatera Azure Cosmos DB databas konto egenskaper. De egenskaper som kan uppdateras är följande:

* Lägga till eller ta bort regioner
* Ändra standard konsekvens princip
* Ändra filter för IP-intervall
* Ändra Virtual Network konfigurationer
* Aktivera skrivningar i flera regioner

> [!NOTE]
> Du kan inte lägga till eller ta bort regioner samtidigt ( `locations` ) och ändra andra egenskaper för ett Azure Cosmos-konto. Att ändra regioner måste utföras som en separat åtgärd från andra ändringar i kontot.
> [!NOTE]
> Med det här kommandot kan du lägga till och ta bort regioner, men du kan inte ändra prioriteter för redundans eller utlösa en manuell redundansväxling. Se [ändra prioritet för redundans](#modify-failover-priority) och [utlösa manuell redundans](#trigger-manual-failover).

```azurepowershell-interactive
# Create account with two regions
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$apiKind = "Sql"
$consistencyLevel = "Session"
$enableAutomaticFailover = $true
$locations = @()
$locations += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locations += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0

# Create the Cosmos DB account
New-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -LocationObject $locations `
    -Name $accountName `
    -ApiKind $apiKind `
    -EnableAutomaticFailover:$enableAutomaticFailover `
    -DefaultConsistencyLevel $consistencyLevel

# Add a region to the account
$locationObject2 = @()
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "West US" -FailoverPriority 1 -IsZoneRedundant 0
$locationObject2 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 2 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject2

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
Write-Host "When region was added, press any key to continue."
$HOST.UI.RawUI.ReadKey("NoEcho,IncludeKeyDown") | OUT-NULL
$HOST.UI.RawUI.Flushinputbuffer()

# Remove West US region from the account
$locationObject3 = @()
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "East US" -FailoverPriority 0 -IsZoneRedundant 0
$locationObject3 += New-AzCosmosDBLocationObject -LocationName "South Central US" -FailoverPriority 1 -IsZoneRedundant 0

Update-AzCosmosDBAccountRegion `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -LocationObject $locationObject3

Write-Host "Update-AzCosmosDBAccountRegion returns before the region update is complete."
Write-Host "Check account in Azure portal or using Get-AzCosmosDBAccount for region status."
```

### <a name="enable-multiple-write-regions-for-an-azure-cosmos-account"></a><a id="multi-region-writes"></a> Aktivera flera Skriv regioner för ett Azure Cosmos-konto

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $false
$enableMultiMaster = $true

# First disable automatic failover - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover

# Now enable multi-region writes
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster
```

### <a name="delete-an-azure-cosmos-account"></a><a id="delete-account"></a> Ta bort ett Azure Cosmos-konto

Det här kommandot tar bort ett befintligt Azure Cosmos-konto.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -PassThru:$true
```

### <a name="update-tags-of-an-azure-cosmos-account"></a><a id="update-tags"></a> Uppdatera taggar för ett Azure Cosmos-konto

Det här kommandot anger [Azures resurs Taggar][azure-resource-tags] för ett Azure Cosmos-konto. Taggar kan ställas in både vid skapande av konto `New-AzCosmosDBAccount` och vid uppdatering av kontot med hjälp av `Update-AzCosmosDBAccount` .

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$tags = @{dept = "Finance"; environment = "Production";}

Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Tag $tags
```

### <a name="list-account-keys"></a><a id="list-keys"></a> Lista konto nycklar

När du skapar ett Azure Cosmos-konto genererar tjänsten två primära åtkomst nycklar som kan användas för autentisering när Azure Cosmos-kontot nås. Skrivskyddade nycklar för att autentisera skrivskyddade åtgärder skapas också.
Genom att tillhandahålla två åtkomst nycklar kan Azure Cosmos DB skapa och rotera en nyckel i taget utan avbrott i ditt Azure Cosmos-konto.
Cosmos DB konton har två Skriv-och skriv nycklar (primära och sekundära) och två skrivskyddade nycklar (primära och sekundära).

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "Keys"
```

### <a name="list-connection-strings"></a><a id="list-connection-strings"></a> Lista anslutnings strängar

Följande kommando hämtar anslutnings strängar för att ansluta appar till Cosmos DB-kontot.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -Type "ConnectionStrings"
```

### <a name="regenerate-account-keys"></a><a id="regenerate-keys"></a> Återskapa konto nycklar

Åtkomst nycklar till ett Azure Cosmos-konto bör återskapas regelbundet för att hålla anslutningarna säkra. Ett primärt och sekundärt åtkomst nycklar tilldelas till kontot. Detta gör att klienter kan upprätthålla åtkomst medan en nyckel i taget återskapas.
Det finns fyra typer av nycklar för ett Azure Cosmos-konto (primär, sekundär, PrimaryReadonly och SecondaryReadonly)

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup" # Resource Group must already exist
$accountName = "mycosmosaccount" # Must be all lower case
$keyKind = "primary" # Other key kinds: secondary, primaryReadOnly, secondaryReadOnly

New-AzCosmosDBAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -KeyKind $keyKind
```

### <a name="enable-automatic-failover"></a><a id="enable-automatic-failover"></a> Aktivera automatisk redundans

Följande kommando anger ett Cosmos DB-konto att redundansväxla automatiskt till den sekundära regionen om den primära regionen blir otillgänglig.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$enableAutomaticFailover = $true
$enableMultiMaster = $false

# First disable multi-region writes - cannot have both automatic
# failover and multi-region writes on an account
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableMultipleWriteLocations:$enableMultiMaster

# Now enable automatic failover
Update-AzCosmosDBAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -EnableAutomaticFailover:$enableAutomaticFailover
```

### <a name="modify-failover-priority"></a><a id="modify-failover-priority"></a> Ändra prioritet för redundans

För konton som har kon figurer ATS med automatisk redundans kan du ändra i vilken ordning Cosmos ska befordra sekundära repliker till primär om den primära blir otillgänglig.

I exemplet nedan antar vi att den aktuella växlings prioriteten är `West US 2 = 0` , `East US 2 = 1` , `South Central US = 2` . Kommandot kommer att ändra detta till `West US 2 = 0` , `South Central US = 1` , `East US 2 = 2` .

> [!CAUTION]
> Om du ändrar platsen för `failoverPriority=0` aktive ras en manuell redundansväxling för ett Azure Cosmos-konto. Eventuella andra prioritets ändringar kommer inte att utlösa redundans.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("West US 2", "South Central US", "East US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="trigger-manual-failover"></a><a id="trigger-manual-failover"></a> Utlös manuell redundans

För konton som kon figurer ATS med manuell redundans kan du redundansväxla och befordra valfri sekundär replik till primär genom att ändra till `failoverPriority=0` . Den här åtgärden kan användas för att starta en haveri beredskap för att testa haveri beredskaps planeringen.

I exemplet nedan antar du att kontot har en aktuell växlings prioritet för `West US 2 = 0` och `East US 2 = 1` och vänder regionerna.

> [!CAUTION]
> Om du ändrar `locationName` för `failoverPriority=0` aktive ras en manuell redundansväxling för ett Azure Cosmos-konto. Alla andra prioritets ändringar kommer inte att utlösa redundans.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$locations = @("East US 2", "West US 2") # Regions ordered by UPDATED failover priority

Update-AzCosmosDBAccountFailoverPriority `
    -ResourceGroupName $resourceGroupName `
    -Name $accountName `
    -FailoverPolicy $locations
```

### <a name="list-resource-locks-on-an-azure-cosmos-db-account"></a><a id="list-account-locks"></a> Visa en lista över resurs lås för ett Azure Cosmos DB konto

Resurs lås kan placeras på Azure Cosmos DB resurser, inklusive databaser och samlingar. Exemplet nedan visar hur du visar en lista över alla Azure-resurs lås på ett Azure Cosmos DB-konto.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceTypeAccount = "Microsoft.DocumentDB/databaseAccounts"
$accountName = "mycosmosaccount"

Get-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceTypeAccount `
    -ResourceName $accountName
```

## <a name="azure-cosmos-db-database"></a>Azure Cosmos DB databas

Följande avsnitt visar hur du hanterar Azure Cosmos DB-databasen, inklusive:

* [Skapa en Azure Cosmos DB-databas](#create-db)
* [Skapa en Azure Cosmos DB databas med delat data flöde](#create-db-ru)
* [Hämta data flödet för en Azure Cosmos DB databas](#get-db-ru)
* [Migrera databas data flöde till autoskalning](#migrate-db-ru)
* [Visa en lista över alla Azure Cosmos DB databaser i ett konto](#list-db)
* [Hämta en enda Azure Cosmos DB databas](#get-db)
* [Ta bort en Azure Cosmos DB databas](#delete-db)
* [Skapa ett resurs lås på en Azure Cosmos DB-databas för att förhindra borttagning](#create-db-lock)
* [Ta bort ett resurs lås på en Azure Cosmos DB-databas](#remove-db-lock)

### <a name="create-an-azure-cosmos-db-database"></a><a id="create-db"></a>Skapa en Azure Cosmos DB-databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-an-azure-cosmos-db-database-with-shared-throughput"></a><a id="create-db-ru"></a>Skapa en Azure Cosmos DB databas med delat data flöde

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$databaseRUs = 400

New-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -Throughput $databaseRUs
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-database"></a><a id="get-db-ru"></a>Hämta data flödet för en Azure Cosmos DB databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabaseThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

## <a name="migrate-database-throughput-to-autoscale"></a><a id="migrate-db-ru"></a>Migrera databas data flöde till autoskalning

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Invoke-AzCosmosDBSqlDatabaseThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName `
    -ThroughputType Autoscale
```

### <a name="get-all-azure-cosmos-db-databases-in-an-account"></a><a id="list-db"></a>Hämta alla Azure Cosmos DB databaser i ett konto

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName
```

### <a name="get-a-single-azure-cosmos-db-database"></a><a id="get-db"></a>Hämta en enda Azure Cosmos DB databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="delete-an-azure-cosmos-db-database"></a><a id="delete-db"></a>Ta bort en Azure Cosmos DB databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Remove-AzCosmosDBSqlDatabase `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -Name $databaseName
```

### <a name="create-a-resource-lock-on-an-azure-cosmos-db-database-to-prevent-delete"></a><a id="create-db-lock"></a>Skapa ett resurs lås på en Azure Cosmos DB-databas för att förhindra borttagning

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-database"></a><a id="remove-db-lock"></a>Ta bort ett resurs lås på en Azure Cosmos DB-databas

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$resourceName = "$accountName/$databaseName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="azure-cosmos-db-container"></a>Azure Cosmos DB behållare

Följande avsnitt visar hur du hanterar Azure Cosmos DB container, inklusive:

* [Skapa en Azure Cosmos DB behållare](#create-container)
* [Skapa en Azure Cosmos DB behållare med autoskalning](#create-container-autoscale)
* [Skapa en Azure Cosmos DB behållare med en stor partitionsnyckel](#create-container-big-pk)
* [Hämta data flödet för en Azure Cosmos DB container](#get-container-ru)
* [Migrera behållarens data flöde till autoskalning](#migrate-container-ru)
* [Skapa en Azure Cosmos DB-behållare med anpassad indexering](#create-container-custom-index)
* [Skapa en Azure Cosmos DB-behållare med indexering inaktive rad](#create-container-no-index)
* [Skapa en Azure Cosmos DB container med unik nyckel och TTL](#create-container-unique-key-ttl)
* [Skapa en Azure Cosmos DB behållare med konflikt lösning](#create-container-lww)
* [Visa en lista över alla Azure Cosmos DB behållare i en databas](#list-containers)
* [Hämta en enda Azure Cosmos DB behållare i en databas](#get-container)
* [Ta bort en Azure Cosmos DB-behållare](#delete-container)
* [Skapa ett resurs lås på en Azure Cosmos DB behållare för att förhindra borttagning](#create-container-lock)
* [Ta bort ett resurs lås på en Azure Cosmos DB-behållare](#remove-container-lock)

### <a name="create-an-azure-cosmos-db-container"></a><a id="create-container"></a>Skapa en Azure Cosmos DB behållare

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$throughput = 400 #minimum = 400

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -Throughput $throughput
```

### <a name="create-an-azure-cosmos-db-container-with-autoscale"></a><a id="create-container-autoscale"></a>Skapa en Azure Cosmos DB behållare med autoskalning

```azurepowershell-interactive
# Create an Azure Cosmos DB container with default indexes and autoscale throughput at 4000 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$autoscaleMaxThroughput = 4000 #minimum = 4000

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -AutoscaleMaxThroughput $autoscaleMaxThroughput
```

### <a name="create-an-azure-cosmos-db-container-with-a-large-partition-key-size"></a><a id="create-container-big-pk"></a>Skapa en Azure Cosmos DB behållare med en stor nyckel storlek

```azurepowershell-interactive
# Create an Azure Cosmos DB container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -PartitionKeyVersion 2
```

### <a name="get-the-throughput-of-an-azure-cosmos-db-container"></a><a id="get-container-ru"></a>Hämta data flödet för en Azure Cosmos DB container

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainerThroughput `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="migrate-container-throughput-to-autoscale"></a><a id="migrate-container-ru"></a>Migrera behållarens data flöde till autoskalning

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Invoke-AzCosmosDBSqlContainerThroughputMigration `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -ThroughputType Autoscale
```

### <a name="create-an-azure-cosmos-db-container-with-custom-index-policy"></a><a id="create-container-custom-index"></a>Skapa en Azure Cosmos DB behållare med en anpassad index princip

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$indexPathIncluded = "/*"
$indexPathExcluded = "/myExcludedPath/*"

$includedPathIndex = New-AzCosmosDBSqlIncludedPathIndex -DataType String -Kind Range
$includedPath = New-AzCosmosDBSqlIncludedPath -Path $indexPathIncluded -Index $includedPathIndex

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IncludedPath $includedPath `
    -ExcludedPath $indexPathExcluded `
    -IndexingMode Consistent `
    -Automatic $true

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-indexing-turned-off"></a><a id="create-container-no-index"></a>Skapa en Azure Cosmos DB-behållare med indexering inaktive rad

```azurepowershell-interactive
# Create an Azure Cosmos DB container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"

$indexingPolicy = New-AzCosmosDBSqlIndexingPolicy `
    -IndexingMode None

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -IndexingPolicy $indexingPolicy
```

### <a name="create-an-azure-cosmos-db-container-with-unique-key-policy-and-ttl"></a><a id="create-container-unique-key-ttl"></a>Skapa en Azure Cosmos DB container med unik nyckel princip och TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL of one day
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$uniqueKeyPath = "/myUniqueKeyPath"
$ttlInSeconds = 86400 # Set this to -1 (or don't use it at all) to never expire

$uniqueKey = New-AzCosmosDBSqlUniqueKey `
    -Path $uniqueKeyPath

$uniqueKeyPolicy = New-AzCosmosDBSqlUniqueKeyPolicy `
    -UniqueKey $uniqueKey

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -UniqueKeyPolicy $uniqueKeyPolicy `
    -TtlInSeconds $ttlInSeconds
```

### <a name="create-an-azure-cosmos-db-container-with-conflict-resolution"></a><a id="create-container-lww"></a>Skapa en Azure Cosmos DB behållare med konflikt lösning

För att skriva alla konflikter till ConflictsFeed och hantera separat, pass `-Type "Custom" -Path ""` .

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionPath = "/myResolutionPath"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type LastWriterWins `
    -Path $conflictResolutionPath

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```

Om du vill skapa en konflikt lösnings princip för att använda en lagrad procedur, anropa `New-AzCosmosDBSqlConflictResolutionPolicy` och skicka parametrar `-Type` och `-ConflictResolutionProcedure` .

```azurepowershell-interactive
# Create container with custom conflict resolution policy using a stored procedure
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$partitionKeyPath = "/myPartitionKey"
$conflictResolutionSprocName = "mysproc"

$conflictResolutionSproc = "/dbs/$databaseName/colls/$containerName/sprocs/$conflictResolutionSprocName"

$conflictResolutionPolicy = New-AzCosmosDBSqlConflictResolutionPolicy `
    -Type Custom `
    -ConflictResolutionProcedure $conflictResolutionSproc

New-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName `
    -PartitionKeyKind Hash `
    -PartitionKeyPath $partitionKeyPath `
    -ConflictResolutionPolicy $conflictResolutionPolicy
```


### <a name="list-all-azure-cosmos-db-containers-in-a-database"></a><a id="list-containers"></a>Visa en lista över alla Azure Cosmos DB behållare i en databas

```azurepowershell-interactive
# List all Azure Cosmos DB containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName
```

### <a name="get-a-single-azure-cosmos-db-container-in-a-database"></a><a id="get-container"></a>Hämta en enda Azure Cosmos DB behållare i en databas

```azurepowershell-interactive
# Get a single Azure Cosmos DB container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Get-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```

### <a name="delete-an-azure-cosmos-db-container"></a><a id="delete-container"></a>Ta bort en Azure Cosmos DB-behållare

```azurepowershell-interactive
# Delete an Azure Cosmos DB container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"

Remove-AzCosmosDBSqlContainer `
    -ResourceGroupName $resourceGroupName `
    -AccountName $accountName `
    -DatabaseName $databaseName `
    -Name $containerName
```
### <a name="create-a-resource-lock-on-an-azure-cosmos-db-container-to-prevent-delete"></a><a id="create-container-lock"></a>Skapa ett resurs lås på en Azure Cosmos DB behållare för att förhindra borttagning

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"
$lockLevel = "CanNotDelete"

New-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName `
    -LockLevel $lockLevel
```

### <a name="remove-a-resource-lock-on-an-azure-cosmos-db-container"></a><a id="remove-container-lock"></a>Ta bort ett resurs lås på en Azure Cosmos DB-behållare

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$resourceType = "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers"
$accountName = "mycosmosaccount"
$databaseName = "myDatabase"
$containerName = "myContainer"
$resourceName = "$accountName/$databaseName/$containerName"
$lockName = "myResourceLock"

Remove-AzResourceLock `
    -ResourceGroupName $resourceGroupName `
    -ResourceType $resourceType `
    -ResourceName $resourceName `
    -LockName $lockName
```

## <a name="next-steps"></a>Nästa steg

* [Alla PowerShell-exempel](powershell-samples.md)
* [Hantera Azure Cosmos-konto](how-to-manage-database-account.md)
* [Skapa en Azure Cosmos DB behållare](how-to-create-container.md)
* [Konfigurera Time-to-Live i Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: /powershell/azure/
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: ../azure-resource-manager/management/overview.md#resource-groups
[azure-resource-tags]: ../azure-resource-manager/management/tag-resources.md
[rp-rest-api]: /rest/api/cosmos-db-resource-provider/