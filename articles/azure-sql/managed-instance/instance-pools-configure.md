---
title: Distribuera SQL-hanterad instans till en instans-pool
titleSuffix: Azure SQL Managed Instance
description: Den här artikeln beskriver hur du skapar och hanterar Azure SQL Managed instance Pools (för hands version).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/05/2019
ms.openlocfilehash: 732a9bab018103321a9a3a759b31495bd67a209e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793117"
---
# <a name="deploy-azure-sql-managed-instance-to-an-instance-pool"></a>Distribuera en hanterad Azure SQL-instans till en instans-pool
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln innehåller information om hur du skapar en [instans-pool](instance-pools-overview.md) och distribuerar en hanterad Azure SQL-instans till den. 

## <a name="instance-pool-operations"></a>Åtgärder för instans-pool

I följande tabell visas tillgängliga åtgärder relaterade till instans-pooler och deras tillgänglighet i Azure Portal och PowerShell.

|Kommando|Azure Portal|PowerShell|
|:---|:---|:---|
|Skapa en instans-pool|Inga|Ja|
|Uppdatera en instans-pool (begränsat antal egenskaper)|Inga |Ja |
|Kontrol lera användning och egenskaper för en instans pool|Inga|Ja |
|Ta bort en instans-pool|Inga|Ja|
|Skapa en hanterad instans i en instans-pool|Inga|Ja|
|Uppdatera resursanvändningen för en hanterad instans|Ja |Ja|
|Kontrol lera användning och egenskaper för en hanterad instans|Ja|Ja|
|Ta bort en hanterad instans från poolen|Ja|Ja|
|Skapa en databas i en instans inom poolen|Ja|Ja|
|Ta bort en databas från SQL-hanterad instans|Ja|Ja|

Tillgängliga [PowerShell-kommandon](/powershell/module/az.sql/):

|Cmdlet |Beskrivning |
|:---|:---|
|[New-AzSqlInstancePool](/powershell/module/az.sql/new-azsqlinstancepool/) | Skapar en SQL-hanterad instans-pool. |
|[Get-AzSqlInstancePool](/powershell/module/az.sql/get-azsqlinstancepool/) | Returnerar information om en instans-pool. |
|[Set-AzSqlInstancePool](/powershell/module/az.sql/set-azsqlinstancepool/) | Anger egenskaper för en instans-pool i SQL-hanterad instans. |
|[Remove-AzSqlInstancePool](/powershell/module/az.sql/remove-azsqlinstancepool/) | Tar bort en instans-pool i SQL-hanterad instans. |
|[Get-AzSqlInstancePoolUsage](/powershell/module/az.sql/get-azsqlinstancepoolusage/) | Returnerar information om användningen av SQL Managed instance pool. |


Om du vill använda PowerShell [installerar du den senaste versionen av PowerShell Core](/powershell/scripting/install/installing-powershell#powershell)och följer anvisningarna för att [Installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

För åtgärder relaterade till instanser både i pooler och enskilda instanser använder du standard [kommandon för hanterade instanser](api-references-create-manage-instance.md#powershell-create-and-configure-managed-instances), men egenskapen *namn på instanss bassäng* måste fyllas i när du använder dessa kommandon för en instans i en pool.

## <a name="deployment-process"></a>Distributionsprocess

Om du vill distribuera en hanterad instans i en instans, måste du först distribuera instansen, som är en tids krävande åtgärd där varaktigheten är samma som att distribuera en [enskild instans som skapats i ett tomt undernät](sql-managed-instance-paas-overview.md#management-operations). Därefter kan du distribuera en hanterad instans till poolen, vilket är en relativt snabb åtgärd som vanligt vis tar upp till fem minuter. Parametern för entitetsinstansen måste anges explicit som en del av den här åtgärden.

I offentlig för hands version stöds båda åtgärderna endast med PowerShell och Azure Resource Manager mallar. Den Azure Portal upplevelsen är inte tillgänglig för närvarande.

När en hanterad instans har distribuerats till en pool *kan* du använda Azure Portal för att ändra dess egenskaper på sidan pris nivå.

## <a name="create-a-virtual-network-with-a-subnet"></a>Skapa ett virtuellt nätverk med ett undernät 

Information om hur du placerar flera instanser i samma virtuella nätverk finns i följande artiklar:

- [Bestäm storleken på VNet-undernät för Azure SQL-hanterad instans](vnet-subnet-determine-size.md).
- Skapa ett nytt virtuellt nätverk och undernät med [Azure Portal-mallen](virtual-network-subnet-create-arm-template.md) eller följ instruktionerna för att [förbereda ett befintligt virtuellt nätverk](vnet-existing-add-subnet.md).
 

## <a name="create-an-instance-pool"></a>Skapa en instans-pool 

När du har slutfört föregående steg är du redo att skapa en instans-pool.

Följande begränsningar gäller för instans pooler:

- Endast Generell användning och Gen5 är tillgängliga i offentlig för hands version.
- Poolnamn får bara innehålla gemena bokstäver, siffror och bindestreck, och får inte börja med ett bindestreck.
- Om du vill använda Azure Hybrid-förmån tillämpas den på instans nivå. Du kan ange licens typ när du skapar en pool eller uppdatera den när den har skapats.

> [!IMPORTANT]
> Distribution av en instans-pool är en tids krävande åtgärd som tar cirka 4,5 timmar.

Hämta nätverks parametrar:

```powershell
$virtualNetwork = Get-AzVirtualNetwork -Name "miPoolVirtualNetwork" -ResourceGroupName "myResourceGroup"
$subnet = Get-AzVirtualNetworkSubnetConfig -Name "miPoolSubnet" -VirtualNetwork $virtualNetwork
```

Så här skapar du en instans-pool:

```powershell
$instancePool = New-AzSqlInstancePool `
  -ResourceGroupName "myResourceGroup" `
  -Name "mi-pool-name" `
  -SubnetId $subnet.Id `
  -LicenseType "LicenseIncluded" `
  -VCore 8 `
  -Edition "GeneralPurpose" `
  -ComputeGeneration "Gen5" `
  -Location "westeurope"
```

> [!IMPORTANT]
> Eftersom distributionen av en instans är en tids krävande åtgärd måste du vänta tills den har slutförts innan du kör något av följande steg i den här artikeln.

## <a name="create-a-managed-instance"></a>Skapa en hanterad instans

När du har slutfört distributionen av instansen är det dags att skapa en hanterad instans inuti den.

Kör följande kommando för att skapa en hanterad instans:

```powershell
$instanceOne = $instancePool | New-AzSqlInstance -Name "mi-one-name" -VCore 2 -StorageSizeInGB 256
```

Det tar några minuter att distribuera en instans i en pool. När den första instansen har skapats kan ytterligare instanser skapas:

```powershell
$instanceTwo = $instancePool | New-AzSqlInstance -Name "mi-two-name" -VCore 4 -StorageSizeInGB 512
```

## <a name="create-a-database"></a>Skapa en databas 

Om du vill skapa och hantera databaser i en hanterad instans i en pool använder du kommandona för den enskilda instansen.

Så här skapar du en databas inuti en hanterad instans:

```powershell
$poolinstancedb = New-AzSqlInstanceDatabase -Name "mipooldb1" -InstanceName "poolmi-001" -ResourceGroupName "myResourceGroup"
```


## <a name="get-pool-usage"></a>Hämta användning av pool 
 
Så här hämtar du en lista över instanser i en pool:

```powershell
$instancePool | Get-AzSqlInstance
```


Så här hämtar du resursanvändningen för poolen:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage
```


För att få detaljerad användnings översikt över poolen och instanserna i den:

```powershell
$instancePool | Get-AzSqlInstancePoolUsage –ExpandChildren
```

Visa en lista över databaser i en instans:

```powershell
$databases = Get-AzSqlInstanceDatabase -InstanceName "pool-mi-001" -ResourceGroupName "resource-group-name"
```


> [!NOTE]
> Det finns en gräns på 100 databaser per pool (inte per instans).


## <a name="scale"></a>Skala 


När du har fyllt i en hanterad instans med databaser kan du träffa instans gränser för lagring eller prestanda. I så fall kan du skala instansen om pool användningen inte har överskridits.
Att skala en hanterad instans i en pool är en åtgärd som tar några minuter. Förutsättningen för skalning är tillgänglig virtuella kärnor och lagring på instance Poolnivå.

Så här uppdaterar du antalet virtuella kärnor och lagrings storlek:

```powershell
$instanceOne | Set-AzSqlInstance -VCore 8 -StorageSizeInGB 512 -InstancePoolName "mi-pool-name"
```


Så här uppdaterar du endast lagrings storlek:

```powershell
$instance | Set-AzSqlInstance -StorageSizeInGB 1024 -InstancePoolName "mi-pool-name"
```

## <a name="connect"></a>Ansluta 

Följande två steg krävs för att ansluta till en hanterad instans i en pool:

1. [Aktivera den offentliga slut punkten för instansen](#enable-the-public-endpoint).
2. [Lägg till en regel för inkommande trafik i nätverks säkerhets gruppen (NSG)](#add-an-inbound-rule-to-the-network-security-group).

När båda stegen har slutförts kan du ansluta till instansen med hjälp av en offentlig slut punkts adress, port och autentiseringsuppgifter som angavs när instansen skapades. 

### <a name="enable-the-public-endpoint"></a>Aktivera den offentliga slut punkten

Aktivering av den offentliga slut punkten för en instans kan göras via Azure Portal eller med hjälp av följande PowerShell-kommando:


```powershell
$instanceOne | Set-AzSqlInstance -InstancePoolName "pool-mi-001" -PublicDataEndpointEnabled $true
```

Den här parametern kan också anges när instansen skapas.

### <a name="add-an-inbound-rule-to-the-network-security-group"></a>Lägg till en regel för inkommande trafik i nätverks säkerhets gruppen 

Det här steget kan göras via Azure Portal eller med hjälp av PowerShell-kommandon och kan göras när under nätet har förberetts för den hanterade instansen.

Mer information finns i [Tillåt offentlig slut punkts trafik i nätverks säkerhets gruppen](public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group).


## <a name="move-an-existing-single-instance-to-a-pool"></a>Flytta en befintlig enskild instans till en pool
 
Att flytta instanser in och ut ur en pool är en av de offentliga begränsningarna för för hands versionen. En lösning förlitar sig på tidpunkts återställning av databaser från en instans utanför en pool till en instans som redan finns i en pool. 

Båda instanserna måste finnas i samma prenumeration och region. Återställning mellan regioner och över prenumerationer stöds inte för närvarande.

Den här processen har en drifts tid.

Flytta befintliga databaser:

1. Pausa arbets belastningar på den hanterade instans som du migrerar från.
2. Skapa skript för att skapa system databaser och köra dem på instansen i instansen.
3. Gör en tidpunkts återställning av varje databas från den enskilda instansen till instansen i poolen.

    ```powershell
    $resourceGroupName = "my resource group name"
    $managedInstanceName = "my managed instance name"
    $databaseName = "my source database name"
    $pointInTime = "2019-08-21T08:51:39.3882806Z"
    $targetDatabase = "name of the new database that will be created"
    $targetResourceGroupName = "resource group of instance pool"
    $targetInstanceName = "pool instance name"
       
    Restore-AzSqlInstanceDatabase -FromPointInTimeBackup `
      -ResourceGroupName $resourceGroupName `
      -InstanceName $managedInstanceName `
      -Name $databaseName `
      -PointInTime $pointInTime `
      -TargetInstanceDatabaseName $targetDatabase `
      -TargetResourceGroupName $targetResourceGroupName `
      -TargetInstanceName $targetInstanceName
    ```

4. Peka ditt program till den nya instansen och återuppta dess arbets belastningar.

Upprepa processen för varje databas om det finns flera databaser.


## <a name="next-steps"></a>Nästa steg

- En funktion och en jämförelse lista finns i [vanliga SQL-funktioner i SQL](../database/features-comparison.md).
- Mer information om VNet-konfiguration finns i [konfiguration av SQL Managed instance VNet](connectivity-architecture-overview.md).
- En snabb start som skapar en hanterad instans och återställer en databas från en säkerhets kopia finns i [skapa en hanterad instans](instance-create-quickstart.md).
- En själv studie kurs om hur du använder Azure Database Migration Service för migrering finns i [migrering av SQL-hanterad instans med hjälp av Database migration service](../../dms/tutorial-sql-server-to-managed-instance.md).
- Avancerad övervakning av SQL-hanterad instans databas prestanda med inbyggd fel söknings information finns i [övervaka Azure SQL-hanterad instans med Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).
- Pris information finns i [priser för SQL-hanterad instans](https://azure.microsoft.com/pricing/details/sql-database/managed/).