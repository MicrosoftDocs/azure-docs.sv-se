---
title: 'Självstudie: lägga till en elastisk pool i en failover-grupp'
description: Lägg till en Azure SQL Database elastisk pool i en failover-grupp med hjälp av Azure Portal, PowerShell eller Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: seo-lt-2019 sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/27/2019
ms.openlocfilehash: cdbc44158de2f24d7d33d68311979c3b8bdda85d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94593985"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Självstudie: lägga till en Azure SQL Database elastisk pool i en failover-grupp
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Konfigurera en failover-grupp för en Azure SQL Database elastisk pool och testa redundans med hjälp av Azure Portal.  I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
>
> - Skapa en enskild databas.
> - Lägg till databasen i en elastisk pool.
> - Skapa en [grupp för växling vid fel](auto-failover-group-overview.md) för två elastiska pooler mellan två servrar.
> - Redundanstest.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa den här självstudien måste du ha:

- En Azure-prenumeration. [Skapa ett kostnads fritt konto](https://azure.microsoft.com/free/) om du inte redan har ett.

## <a name="1---create-a-single-database"></a>1 – Skapa en enskild databas

[!INCLUDE [sql-database-create-single-database](../includes/sql-database-create-single-database.md)]

## <a name="2---add-the-database-to-an-elastic-pool"></a>2 – Lägg till databasen i en elastisk pool

I det här steget ska du skapa en elastisk pool och lägga till din databas i den.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa den elastiska poolen med hjälp av Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i Azure Portal. Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan "Azure SQL" i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster.
1. Välj **+ Lägg** till för att öppna **alternativ sidan Välj SQL-distribution** . Du kan visa ytterligare information om de olika databaserna genom att välja Visa information på panelen databaser.
1. Välj **elastisk pool** i list rutan **resurs typ** i panelen SQL- **databaser** . Välj **skapa** för att skapa en elastisk pool.

    ![Välj elastisk pool](./media/failover-group-add-elastic-pool-tutorial/select-azure-sql-elastic-pool.png)

1. Konfigurera den elastiska poolen med följande värden:
   - **Namn**: Ange ett unikt namn för den elastiska poolen, till exempel `myElasticPool` .
   - **Prenumeration**: Välj din prenumeration från List rutan.
   - **ResourceGroup**: Välj `myResourceGroup` i list rutan, resurs gruppen du skapade i avsnitt 1.
   - **Server**: Välj den server som du skapade i avsnitt 1 i list rutan.  

       ![Skapa en ny server för elastisk pool](./media/failover-group-add-elastic-pool-tutorial/use-existing-server-for-elastic-pool.png)

   - **Compute + Storage**: Välj **Konfigurera elastisk pool** för att konfigurera din beräkning, lagring och lägga till din enda databas i den elastiska poolen. På fliken **Inställningar för pool** lämnar du standardvärdet Gen5, med 2 virtuella kärnor och 32 GB.

1. Välj fliken **databaser** på sidan **Konfigurera** och välj sedan att **lägga till databas**. Välj den databas som du skapade i avsnitt 1 och välj sedan **tillämpa** för att lägga till den i den elastiska poolen. Välj **tillämpa** igen för att tillämpa inställningarna för elastisk pool och Stäng sidan **Konfigurera** .

    ![Lägg till databas i elastisk pool](./media/failover-group-add-elastic-pool-tutorial/add-database-to-elastic-pool.png)

1. Välj **Granska + skapa** för att granska inställningarna för elastisk pool och välj sedan **skapa** för att skapa en elastisk pool.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa dina elastiska pooler och den sekundära servern med hjälp av PowerShell.

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

I den här delen av självstudien används följande PowerShell-cmdletar:

| Kommando | Kommentarer |
|---|---|
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en Elastic Database-pool för en Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool. |

---

## <a name="3---create-the-failover-group"></a>3 – skapa redundans gruppen

I det här steget ska du skapa en [redundans grupp](auto-failover-group-overview.md) mellan en befintlig server och en ny server i en annan region. Lägg sedan till den elastiska poolen i gruppen redundans.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa din failover-grupp med hjälp av Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan Azure SQL i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster.
1. Välj den elastiska pool som skapades i föregående avsnitt, till exempel `myElasticPool` .
1. I **översikts** fönstret väljer du namnet på servern under **Server namn** för att öppna inställningarna för servern.
  
    ![Öppna Server för elastisk pool](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan **Lägg till grupp** för att skapa en ny grupp för redundans.

    ![Lägg till ny redundans grupp](./media/failover-group-add-elastic-pool-tutorial/elastic-pool-failover-group.png)

1. På sidan **redundans** anger eller väljer du följande värden och väljer sedan **skapa**:
    - **Namn på redundans grupp**: Ange ett unikt namn på redundans grupp, till exempel `failovergrouptutorial` .
    - **Sekundär server**: Välj alternativet för att *Konfigurera nödvändiga inställningar* och välj sedan att **skapa en ny server**. Alternativt kan du välja en redan befintlig server som den sekundära servern. När du har angett följande värden för den nya sekundära servern väljer du **Välj**.
        - **Server namn**: Ange ett unikt namn på den sekundära servern, till exempel `mysqlsecondary` .
        - **Inloggning för Server administratör**: typ `azureuser`
        - **Lösen ord**: Ange ett komplext lösen ord som uppfyller lösen ords kraven.
        - **Plats**: Välj en plats i list rutan, till exempel `East US` . Den här platsen kan inte vara samma plats som den primära servern.

       > [!NOTE]
       > Inställningarna för Server inloggning och brand vägg måste matcha den primära servern.

       ![Skapa en sekundär server för redundans gruppen](./media/failover-group-add-elastic-pool-tutorial/create-secondary-failover-server.png)

1. Välj **databaser i gruppen** och välj sedan den elastiska pool som du skapade i avsnitt 2. En varning bör visas, så att du snabbt kan skapa en elastisk pool på den sekundära servern. Välj varningen och välj sedan **OK** för att skapa den elastiska poolen på den sekundära servern. 

   ![Lägg till elastisk pool i redundans gruppen](./media/failover-group-add-elastic-pool-tutorial/add-elastic-pool-to-failover-group.png)

1. Välj **Välj** om du vill tillämpa inställningarna för elastisk pool på gruppen växling vid fel och välj sedan **skapa** för att skapa din grupp för redundans. Om du lägger till den elastiska poolen i gruppen redundans startas geo-replikeringen automatiskt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa din failover-grupp med hjälp av PowerShell.

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
     –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
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

I den här delen av självstudien används följande PowerShell-cmdletar:

| Kommando | Kommentarer |
|---|---|
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brand Väggs regel för en server. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en elastisk pool för en Azure SQL Database.|
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny grupp för redundans. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL-databaser i en failover-grupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Azure SQL Database redundans grupper. |

---

## <a name="4---test-failover"></a>4 – redundanstest

I det här steget kommer du inte att kunna redundansväxla gruppen till den sekundära servern och sedan växla tillbaka med Azure Portal.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för din failover-grupp med hjälp av Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan Azure SQL i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster.
1. Välj den elastiska pool som skapades i föregående avsnitt, till exempel `myElasticPool` .
1. Välj namnet på servern under **Server namn** för att öppna inställningarna för servern.

    ![Öppna Server för elastisk pool](./media/failover-group-add-elastic-pool-tutorial/server-for-elastic-pool.png)

1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan den grupp för växling vid fel som du skapade i avsnitt 2.
  
   ![Välj gruppen redundans från portalen](./media/failover-group-add-elastic-pool-tutorial/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär.
1. Välj **redundans** från åtgärds fönstret för att Redundansväxla din redundansväxling som innehåller den elastiska poolen.
1. Välj **Ja** i varningen som meddelar dig att TDS-sessioner kommer att kopplas bort.

   ![Redundansväxla din redundanskonfiguration som innehåller din databas](./media/failover-group-add-elastic-pool-tutorial/failover-sql-db.png)

1. Granska vilken server som är primär, vilken server som är sekundär. Om redundansväxlingen lyckades måste de två servrarna ha växlade roller.
1. Välj **redundans** igen om du vill att redundans-gruppen ska växla tillbaka till de ursprungliga inställningarna.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testa redundans för din failover-grupp med hjälp av PowerShell.

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

Redundansväxla din redundans till den sekundära servern och växla sedan tillbaka med hjälp av PowerShell.

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

I den här delen av självstudien används följande PowerShell-cmdletar:

| Kommando | Kommentarer |
|---|---|
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Azure SQL Database redundans grupper. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundansväxling av en Azure SQL Database redundans grupp. |

---

## <a name="clean-up-resources"></a>Rensa resurser

Rensa resurser genom att ta bort resurs gruppen.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Navigera till din resurs grupp i [Azure Portal](https://portal.azure.com).
1. Välj  **ta bort resurs grupp** för att ta bort alla resurser i gruppen, samt själva resurs gruppen.
1. Skriv namnet på resurs gruppen, `myResourceGroup` i text rutan och välj sedan **ta bort** för att ta bort resurs gruppen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Rensa dina resurser med PowerShell.

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
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resurs grupp |

---

> [!IMPORTANT]
> Om du vill behålla resurs gruppen men ta bort den sekundära databasen tar du bort den från gruppen växling vid fel innan du tar bort den. Om du tar bort en sekundär databas innan den tas bort från redundans gruppen kan det orsaka oförutsägbart beteende.

## <a name="full-script"></a>Fullständigt skript

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-elastic-pool-to-failover-group-az-ps.ps1 "Add elastic pool to a failover group")]

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brand Väggs regel för en server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en databas. |
| [New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool) | Skapar en Elastic Database-pool för en Azure SQL Database.|
| [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) | Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool. |
| [New-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/new-azsqldatabasefailovergroup) | Skapar en ny grupp för redundans. |
| [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase) | Hämtar en eller flera databaser i SQL Database. |
| [Add-AzSqlDatabaseToFailoverGroup](/powershell/module/az.sql/add-azsqldatabasetofailovergroup) | Lägger till en eller flera Azure SQL-databaser i en failover-grupp. |
| [Get-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/get-azsqldatabasefailovergroup) | Hämtar eller listar Azure SQL Database redundans grupper. |
| [Switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup)| Kör en redundansväxling av en Azure SQL Database redundans grupp. |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resurs grupp |

# <a name="portal"></a>[Portal](#tab/azure-portal)

Det finns inga tillgängliga skript för Azure Portal.

---

## <a name="next-steps"></a>Nästa steg

I den här självstudien lade du till en Azure SQL Database elastisk pool till en failover-grupp och testade redundans. Du har lärt dig att:

> [!div class="checklist"]
>
> - Skapa en enskild databas.
> - Lägg till databasen i en elastisk pool.
> - Skapa en [grupp för växling vid fel](auto-failover-group-overview.md) för två elastiska pooler mellan två servrar.
> - Redundanstest.

Gå vidare till nästa självstudie om hur du migrerar med DMS.

> [!div class="nextstepaction"]
> [Självstudie: Migrera SQL Server till en poolad databas med DMS](../../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
