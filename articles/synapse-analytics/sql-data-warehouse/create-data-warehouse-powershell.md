---
title: 'Snabbstart: Skapa en dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell'
description: Skapa snabbt en dedikerad SQL-pool (tidigare SQL DW) med en brandväggsregel på servernivå med Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
ms.author: xiaoyul
manager: craigg
ms.reviewer: igorstan
ms.date: 4/11/2019
ms.topic: quickstart
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.custom:
- seo-lt-2019
- azure-synapse
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 65bf509c8eb654a9f7712fdf7b94ff7fa26a3d32
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537456"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Snabbstart: Skapa en dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell

Skapa en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!IMPORTANT]
> Att skapa en dedikerad SQL-pool (tidigare SQL DW) kan resultera i en ny fakturerbar tjänst.  Mer information finns i [Azure Synapse Analytics priser.](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration [med kommandot Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör du [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standardprenumerationen kör [du Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="create-variables"></a>Skapa variabler

Definiera variabler för användning i skripten i den här snabbstartsguiden.

```powershell
# The data center and resource name for your resources
$resourcegroupname = "myResourceGroup"
$location = "WestEurope"
# The server name: Use a random value or replace with your own value (don't capitalize)
$servername = "server-$(Get-Random)"
# Set an admin name and password for your database
# The sign-in information for the server
$adminlogin = "ServerAdmin"
$password = "ChangeYourAdminPassword1"
# The ip address range that you want to allow to access your server - change as appropriate
$startip = "0.0.0.0"
$endip = "0.0.0.0"
# The database name
$databasename = "mySampleDataWarehouse"
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resursgrupp](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) med [kommandot New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) En resursgrupp är en container där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Skapa en server

Skapa en [logisk SQL-server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) med [kommandot New-AzSqlServer.](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) En server innehåller en grupp med databaser som hanteras som en grupp. I följande exempel skapas en slumpmässigt namngiven server i resursgruppen med en administratörsanvändare med `ServerAdmin` namnet och lösenordet `ChangeYourAdminPassword1` . Ersätt dessa fördefinierade värden efter behov.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en [brandväggsregel på servernivå](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) med [kommandot New-AzSqlServerFirewallRule.](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) En brandväggsregel på servernivå gör att externa program, till exempel SQL Server Management Studio eller SQLCMD-verktyget, kan ansluta till en dedikerad SQL-pool (tidigare SQL DW) via den dedikerade SQL-pooltjänstens brandvägg.

I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL-slutpunkter kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte IT-avdelningen öppnar port 1433.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Skapa en dedikerad SQL-pool (tidigare SQL DW)

I följande exempel skapas en dedikerad SQL-pool (tidigare SQL DW) med hjälp av de tidigare definierade variablerna.  Det anger tjänstmålet som DW100c, vilket är en startpunkt till lägre kostnad för din dedikerade SQL-pool (tidigare SQL DW).

```Powershell
New-AzSqlDatabase `
    -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -DatabaseName $databasename `
    -Edition "DataWarehouse" `
    -RequestedServiceObjectiveName "DW100c" `
    -CollationName "SQL_Latin1_General_CP1_CI_AS" `
    -MaxSizeBytes 10995116277760
```

Erfordrade parametrar är:

* **RequestedServiceObjectiveName:** Mängden [informationslagerenheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) som du begär. Om du ökar den här mängden ökar beräkningskostnaden. En lista över värden som stöds finns i [minnes- och samtidighetsgränser.](memory-concurrency-limits.md)
* **DatabaseName:** Namnet på den dedikerade SQL-pool (tidigare SQL DW) som du skapar.
* **ServerName:** Namnet på den server som du använder för att skapa.
* **ResourceGroupName:** Resursgrupp som du använder. Använd Get-AzureResource för att hitta tillgängliga resursgrupper i din prenumeration.
* **Utgåva:** Måste vara "DataWarehouse" för att skapa en dedikerad SQL-pool (tidigare SQL DW).

Valfria parametrar är:

* **CollationName**: Standardsortering om inte annat anges är SQL_Latin1_General_CP1_CI_AS. Sortering kan inte ändras i en databas.
* **MaxSizeBytes:** Den maximala standardstorleken för en databas är 240 TB. Maximal storlek begränsar radlagringsdata. Det finns obegränsad lagring för kolumndata.

Mer information om parameteralternativen finns i [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten.

> [!TIP]
> Om du planerar att fortsätta att arbeta med senare snabbstartsguider ska du inte rensa resurserna som skapades i den här snabbstarten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats av den här snabbstarten i Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

Nu har du skapat en dedikerad SQL-pool (tidigare SQL DW), skapat en brandväggsregel och anslutit till din dedikerade SQL-pool. Om du vill veta mer kan du fortsätta till [artikeln Läsa in data till en dedikerad SQL-pool.](./load-data-from-azure-blob-storage-using-copy.md)
