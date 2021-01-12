---
title: 'Snabb start: skapa en dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell'
description: Skapa snabbt en dedikerad SQL-pool (tidigare SQL DW) med en brand Väggs regel på server nivå med hjälp av Azure PowerShell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 4/11/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse    , devx-track-azurepowershell
ms.openlocfilehash: 18fba46e4e8de14d9b3ec43455ba2abcc4218dc1
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98120197"
---
# <a name="quickstart-create-a-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Snabb start: skapa en dedikerad SQL-pool (tidigare SQL DW) med Azure PowerShell

Skapa en dedikerad SQL-pool (tidigare SQL DW) i Azure Synapse Analytics med hjälp av Azure PowerShell.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

> [!IMPORTANT]
> Att skapa en dedikerad SQL-pool (tidigare SQL DW) kan resultera i en ny fakturerbar tjänst.  Mer information finns i [priser för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

Om du vill se vilken prenumeration du använder kör du [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

```powershell
Get-AzSubscription
```

Om du behöver använda en annan prenumeration än standardinställningen kör du [set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

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

Skapa en [Azure-resurs grupp](../../azure-resource-manager/management/overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . En resurs grupp är en behållare där Azure-resurser distribueras och hanteras som en grupp. I följande exempel skapas en resursgrupp med namnet `myResourceGroup` på platsen `westeurope`.

```powershell
New-AzResourceGroup -Name $resourcegroupname -Location $location
```

## <a name="create-a-server"></a>Skapa en server

Skapa en [logisk SQL-Server](../../azure-sql/database/logical-servers.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) med kommandot [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . En server innehåller en grupp med databaser som hanteras som en grupp. I följande exempel skapas en slumpvis namngiven server i resurs gruppen med en administratörs användare med namnet `ServerAdmin` och ett lösen ord för `ChangeYourAdminPassword1` . Ersätt dessa fördefinierade värden efter behov.

```powershell
New-AzSqlServer -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -Location $location `
    -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
```

## <a name="configure-a-server-level-firewall-rule"></a>Konfigurera en brandväggsregel på servernivå

Skapa en [brand Väggs regel på server nivå](../../azure-sql/database/firewall-configure.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) med kommandot [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) . En brand Väggs regel på server nivå gör det möjligt för ett externt program, till exempel SQL Server Management Studio eller SQLCMD-verktyget, att ansluta till en dedikerad SQL-pool (tidigare SQL DW) via den dedikerade SQL-poolens tjänst brand vägg.

I följande exempel öppnas brandväggen bara för andra Azure-resurser. Aktivera extern anslutning, ändra IP-adressen till en adress som är lämplig för din miljö. Öppna alla IP-adresser genom att använda 0.0.0.0 som den första IP-adressen och 255.255.255.255 som slutadress.

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
    -ServerName $servername `
    -FirewallRuleName "AllowSome" -StartIpAddress $startip -EndIpAddress $endip
```

> [!NOTE]
> SQL-slutpunkter kommunicerar via port 1433. Om du försöker ansluta inifrån ett företagsnätverk, kan utgående trafik via port 1433 bli nekad av nätverkets brandvägg. I så fall kan du inte ansluta till servern om inte din IT-avdelning öppnar port 1433.
>

## <a name="create-a-dedicated-sql-pool-formerly-sql-dw"></a>Skapa en dedikerad SQL-pool (tidigare SQL DW)

I följande exempel skapas en dedikerad SQL-pool (tidigare SQL DW) med hjälp av de tidigare definierade variablerna.  Den anger tjänst målet som DW100c, som är en lägre start punkt för din dedikerade SQL-pool (tidigare SQL DW).

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

* **RequestedServiceObjectiveName**: mängden [data lager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md) som du begär. Om du ökar värdet ökar beräknings kostnaden. En lista över värden som stöds finns i [minnes-och samtidiga gränser](memory-concurrency-limits.md).
* **Databasename**: namnet på den dedikerade SQL-pool (tidigare SQL DW) som du skapar.
* **Server** namn: namnet på den server som du använder för att skapa.
* **ResourceGroupName**: resurs gruppen som du använder. Använd Get-AzureResource för att hitta tillgängliga resursgrupper i din prenumeration.
* **Utgåva**: måste vara "DataWarehouse" för att skapa en dedikerad SQL-pool (tidigare SQL DW).

Valfria parametrar är:

* **CollationName**: Standardsortering om inte annat anges är SQL_Latin1_General_CP1_CI_AS. Det går inte att ändra sorteringen för en databas.
* **MaxSizeBytes**: standard Max storleken för en databas är 240TB. Max storleken begränsar rowstore-data. Det finns obegränsad lagring för kolumn data.

Mer information om parameter alternativen finns i [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstartsguiderna i den här samlingen bygger på den här snabbstarten.

> [!TIP]
> Om du planerar att fortsätta att arbeta med senare snabb starts guider ska du inte rensa upp resurserna som du skapade i den här snabb starten. Om du inte planerar att fortsätta kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten i Azure Portal.
>

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="next-steps"></a>Nästa steg

Nu har du skapat en dedikerad SQL-pool (tidigare SQL DW), skapat en brand Väggs regel och anslutit till din dedikerade SQL-pool. Om du vill veta mer kan du fortsätta till artikeln [Läs in data i en särskild SQL-pool](./load-data-from-azure-blob-storage-using-copy.md) .