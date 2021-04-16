---
title: 'Självstudie: Lägga till en elastisk pool i en redundansgrupp'
description: Lägg till Azure SQL Database elastisk pool i en redundansgrupp med hjälp av Azure Portal, PowerShell eller Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: dd0532469cd4390d9a72900dcebc22994239325b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479176"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Självstudie: Lägga till Azure SQL Database elastisk pool i en redundansgrupp
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Konfigurera en redundansgrupp för en Azure SQL Database elastisk pool och testa redundans med hjälp av Azure Portal.  I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
>
> - Skapa en enkel databas.
> - Lägg till databasen i en elastisk pool.
> - Skapa en [redundansgrupp](auto-failover-group-overview.md) för två elastiska pooler mellan två servrar.
> - Testa redundans.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha:

- En Azure-prenumeration. [Skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) om du inte redan har ett.

## <a name="1---create-a-single-database"></a>1 – Skapa en enkel databas

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 – Lägga till databasen i en elastisk pool

I det här steget skapar du en elastisk pool och lägger till databasen i den.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa din elastiska pool med hjälp av Azure Portal.

1. Välj **Azure SQL** på menyn till vänster i Azure Portal. Om **Azure SQL** inte finns i listan väljer du **Alla** tjänster och skriver sedan "Azure SQL" i sökrutan. (Valfritt) Välj stjärnan bredvid den **Azure SQL** favorit och lägg till den som ett objekt i det vänstra navigeringsfönstret.
1. Välj **+ Lägg till** för att öppna sidan Välj alternativ för **SQL-distribution.** Du kan visa ytterligare information om de olika databaserna genom att välja Visa information på panelen Databaser.
1. Välj **Elastisk pool** i **listrutan** Resurstyp på panelen **SQL-databaser.** Välj **Skapa för** att skapa din elastiska pool.

    ![Välj elastisk pool](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Konfigurera din elastiska pool med följande värden:
   - **Namn:** Ange ett unikt namn för den elastiska poolen, till exempel `myElasticPool` .
   - **Prenumeration:** Välj din prenumeration i listrutan.
   - **ResourceGroup:** Välj den resursgrupp som du skapade i avsnitt `myResourceGroup` 1 i listrutan.
   - **Server:** Välj den server som du skapade i avsnitt 1 i listrutan.  

       ![Skapa en ny server för elastisk pool](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Beräkning och lagring:** Välj **Konfigurera elastisk pool för** att konfigurera beräkning, lagring och lägga till din enkla databas i den elastiska poolen. På fliken **Poolinställningar** lämnar du standardvärdet Gen5 med 2 virtuella kärnor och 32 GB.

1. På sidan **Konfigurera** väljer du fliken **Databaser** och väljer sedan Lägg till **databas.** Välj den databas som du skapade i avsnitt 1 och välj sedan **Använd för** att lägga till den i din elastiska pool. Välj **Tillämpa** igen för att tillämpa inställningarna för den elastiska poolen och stäng **sidan** Konfigurera.

    ![Lägga till databas i elastisk pool](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Välj **Granska + skapa för** att granska inställningarna för den elastiska poolen och välj sedan Skapa **för** att skapa din elastiska pool.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa dina elastiska pooler och sekundära servrar med PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   $poolName = "myElasticPool"
   $databaseName = "mySampleDatabase"
   $drLocation = "West US"
   $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # The ip address range that you want to allow to access your server
   # Leaving at 0.0.0.0 will prevent outside-of-azure connections
   # $startIp = "0.0.0.0"
   # $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "DR Server name is" $drServerName
   Write-host "Failover group name is" $failoverGroupName

   # Create primary Gen5 elastic 2 vCore pool
   Write-host "Creating elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Add single db into elastic pool
   Write-host "Creating elastic pool..."
   $addDatabase = Set-AzSqlDatabase -ResourceGroupName $resourceGroupName `
       -ServerName $serverName `
       -DatabaseName $databaseName `
       -ElasticPoolName $poolName
   $addDatabase
   ```

I den här delen av självstudien används följande PowerShell-cmdlets:

| Kommando | Kommentarer |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk databaspool för en Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool. |

---

## <a name="3---create-the-failover-group"></a>3 – Skapa redundansgruppen

I det här steget skapar du en [redundansgrupp mellan](auto-failover-group-overview.md) en befintlig server och en ny server i en annan region. Lägg sedan till den elastiska poolen i redundansgruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa redundansgruppen med hjälp av Azure Portal.

1. Välj **Azure SQL** på menyn till vänster i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **Alla** tjänster och skriver Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid den **Azure SQL** favorit och lägg till den som ett objekt i det vänstra navigeringsfönstret.
1. Välj den elastiska pool som skapades i föregående avsnitt, till exempel `myElasticPool` .
1. I fönstret **Översikt** väljer du namnet på servern under **Servernamn för** att öppna inställningarna för servern.
  
    ![Öppna servern för elastisk pool](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Välj **Redundansgrupper** under **fönstret Inställningar** och välj sedan Lägg till grupp **för** att skapa en ny redundansgrupp.

    ![Lägg till ny redundansgrupp](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. På sidan **Redundansgrupp** anger eller väljer du följande värden och väljer sedan **Skapa:**
    - **Namn på redundansgrupp:** Ange ett unikt namn på redundansgruppen, till exempel `failovergrouptutorial` .
    - **Sekundär server:** Välj alternativet för att *konfigurera nödvändiga inställningar* och välj sedan Skapa en ny **server.** Du kan också välja en redan befintlig server som sekundär server. När du har angett följande värden för den nya sekundära servern väljer du **Välj**.
        - **Servernamn:** Ange ett unikt namn för den sekundära servern, till exempel `mysqlsecondary` .
        - **Inloggning för serveradministratör:** Typ `azureuser`
        - **Lösenord:** Ange ett komplext lösenord som uppfyller lösenordskraven.
        - **Plats:** Välj en plats i listrutan, till exempel `East US` . Den här platsen får inte vara samma plats som den primära servern.

       > [!NOTE]
       > Inställningarna för serverinloggning och brandvägg måste matcha inställningarna för den primära servern.

       ![Skapa en sekundär server för redundansgruppen](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Välj **Databaser i gruppen och** välj sedan den elastiska pool som du skapade i avsnitt 2. En varning bör visas som uppmanar dig att skapa en elastisk pool på den sekundära servern. Välj varningen och välj sedan **OK för att** skapa den elastiska poolen på den sekundära servern. 

   ![Lägga till elastisk pool i redundansgrupp](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Välj **Välj** för att tillämpa dina elastiska poolinställningar på redundansgruppen och välj sedan **Skapa för** att skapa redundansgruppen. Om du lägger till den elastiska poolen i redundansgruppen startar den geo-replikeringsprocessen automatiskt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa en redundansgrupp med Hjälp av PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   Write-host "Secondary server =" $drServerName

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for secondary server..."
   New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   Write-host "Firewall configured"

   # Create secondary Gen5 elastic 2 vCore pool
   Write-host "Creating secondary elastic pool..."
   $elasticPool = New-AzSqlElasticPool -ResourceGroupName $resourceGroupName `
       -ServerName $drServerName `
       -ElasticPoolName $poolName `
       -Edition "GeneralPurpose" `
       -vCore 2 `
       -ComputeGeneration Gen5
   $elasticPool

   # Create a failover group between the servers
   Write-host "Creating failover group..."
   New-AzSqlDatabaseFailoverGroup `
     â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   Write-host "Failover group created successfully."

   # Add elastic pool to the failover group
   Write-host "Enumerating databases in elastic pool...."
   $FailoverGroup = Get-AzSqlDatabaseFailoverGroup `
                    -ResourceGroupName $resourceGroupName `
                    -ServerName $serverName `
                    -FailoverGroupName $failoverGroupName
   $databases = Get-AzSqlElasticPoolDatabase `
                  -ResourceGroupName $resourceGroupName `
                  -ServerName $serverName `
                  -ElasticPoolName $poolName
   Write-host "Adding databases to failover group..."
   $failoverGroup = $failoverGroup | Add-AzSqlDatabaseToFailoverGroup `
                                     -Database $databases
   $failoverGroup
   ```

I den här delen av självstudien används följande PowerShell-cmdlets:

| Kommando | Kommentarer |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för en server. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk pool för en Azure SQL Database.|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny redundansgrupp. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL databaser i en redundansgrupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller visar Azure SQL Database redundansgrupper. |

---

## <a name="4---test-failover"></a>4 – Testa redundans

I det här steget redundans redundans redundansar du redundansgruppen till den sekundära servern och sedan tillbaka med hjälp av Azure Portal.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för redundansgruppen med hjälp av Azure Portal.

1. Välj **Azure SQL** på menyn till vänster i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **Alla** tjänster och skriver Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid den **Azure SQL** favorit och lägg till den som ett objekt i det vänstra navigeringsfönstret.
1. Välj den elastiska pool som skapades i föregående avsnitt, till exempel `myElasticPool` .
1. Välj namnet på servern under **Servernamn för** att öppna inställningarna för servern.

    ![Öppna servern för elastisk pool](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Välj **Redundansgrupper** under **fönstret** Inställningar och välj sedan den redundansgrupp som du skapade i avsnitt 2.
  
   ![Välj redundansgruppen från portalen](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär.
1. Välj **Redundans** i åtgärdsfönstret för att redundans redundansgruppen som innehåller din elastiska pool.
1. Välj **Ja** i varningen som meddelar dig att TDS-sessioner kommer att kopplas från.

   ![Redundans redundansgruppen som innehåller din databas](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Granska vilken server som är primär, vilken server som är sekundär. Om redundansväxlingen lyckades bör de två servrarna ha växlat roller.
1. Välj **Redundans** igen för att växla tillbaka redundansgruppen till de ursprungliga inställningarna.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testa redundans för redundansgruppen med Hjälp av PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $drServerName
   ```

Växla över redundansgruppen till den sekundära servern och växla sedan tillbaka med Hjälp av PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $subscriptionId = '<SubscriptionID>'
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"
   # $location = "East US"
   # $adminLogin = "azureuser"
   # $password = "PWD27!"+(New-Guid).Guid
   # $serverName = "mysqlserver-$(Get-Random)"
   # $poolName = "myElasticPool"
   # $databaseName = "mySampleDatabase"
   # $drLocation = "West US"
   # $drServerName = "mysqlsecondary-$(Get-Random)"
   # $failoverGroupName = "failovergrouptutorial-$(Get-Random)"

   # Check role of secondary replica
   Write-host "Confirming the secondary server is now primary"
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failover group failed over to" $serverName
   ```

I den här delen av självstudien används följande PowerShell-cmdlets:

| Kommando | Kommentarer |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller visar Azure SQL Database redundansgrupper. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundans av en Azure SQL Database redundansgrupp. |

---

## <a name="clean-up-resources"></a>Rensa resurser

Rensa resurser genom att ta bort resursgruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Gå till resursgruppen i [Azure Portal](https://portal.azure.com).
1. Välj  **Ta bort resursgrupp** för att ta bort alla resurser i gruppen samt själva resursgruppen.
1. Skriv namnet på resursgruppen, `myResourceGroup` , i textrutan och välj sedan Ta bort **för** att ta bort resursgruppen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rensa dina resurser med Hjälp av PowerShell.

   ```powershell-interactive
   # Set variables for your server and database
   # $resourceGroupName = "myResourceGroup-$(Get-Random)"

   # Clean up resources by removing the resource group
   Write-host "Removing resource group..."
   Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
   Write-host "Resource group removed =" $resourceGroupName
   ```

I den här delen av självstudien används följande PowerShell-cmdlet:

| Kommando | Kommentarer |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp |

---

> [!IMPORTANT]
> Om du vill behålla resursgruppen men ta bort den sekundära databasen tar du bort den från redundansgruppen innan du tar bort den. Att ta bort en sekundär databas innan den tas bort från redundansgruppen kan orsaka oförutsägbart beteende.

## <a name="full-script"></a>Fullständigt skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel för en server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en databas. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk databaspool för en Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny redundansgrupp. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hämtar en eller flera databaser i SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL databaser i en redundansgrupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller visar Azure SQL Database redundansgrupper. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundans av en Azure SQL Database redundansgrupp. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Det finns inga tillgängliga skript för Azure Portal.

---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lagt Azure SQL Database en elastisk pool i en redundansgrupp och testat redundans. Du har lärt dig att:

> [!div class="checklist"]
>
> - Skapa en enkel databas.
> - Lägg till databasen i en elastisk pool.
> - Skapa en [redundansgrupp](auto-failover-group-overview.md) för två elastiska pooler mellan två servrar.
> - Testa redundans.

Gå vidare till nästa självstudie om hur du migrerar med DMS.

> [!div class="nextstepaction"]
> [Självstudie: Migrera SQL Server till en pooldatabas med DMS](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
