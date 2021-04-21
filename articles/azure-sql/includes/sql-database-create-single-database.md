---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: sstein
ms.reviewer: vanto
ms.openlocfilehash: 0568860c387aa1239ec56005e404606272ae8275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799851"
---
I det här steget skapar du en [logisk SQL-server](../database/logical-servers.md) och en [enkel databas](../database/single-database-overview.md) som använder AdventureWorksLT-exempeldata. Du kan skapa databasen med hjälp Azure Portal menyer och skärmar, eller med hjälp av ett Azure CLI- eller PowerShell-skript i Azure Cloud Shell.

Alla metoder omfattar att konfigurera en brandväggsregel på servernivå som tillåter den offentliga IP-adressen för den dator som du använder för att få åtkomst till servern. Mer information om hur du skapar brandväggsregler på servernivå finns [i Skapa en brandvägg på servernivå.](../database/firewall-create-server-level-portal-quickstart.md) Du kan också ange brandväggsregler på databasnivå. Se [Skapa en brandväggsregel på databasnivå.](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här skapar du en resursgrupp, server och enkel databas i Azure Portal:

1. Logga in på [portalen](https://portal.azure.com).
1. I sökfältet söker du efter och väljer **Azure SQL**.
1. På sidan **Azure SQL** väljer du Lägg **till**.

   ![Lägg till i Azure SQL](./media/sql-database-create-single-database/sqldbportal.png)

1. På sidan **Välj sql-distributionsalternativ** väljer du **panelen SQL-databaser,** med **Enkel databas** under **Resurstyp.** Du kan visa mer information om de olika databaserna genom att välja **Visa information.**
1. Välj **Skapa**.

   ![Skapa en enkel databas](./media/sql-database-create-single-database/create-single-database.png)

1. På fliken Grundläggande inställningar i formuläret **Skapa SQL-databas** **går** du till Projektinformation **och** väljer rätt **Azure-prenumeration** om den inte redan är markerad.
1. Under **Resursgrupp** väljer du **Skapa ny,** *anger myResourceGroup* och väljer **OK.**
1. Under **Databasinformation** för **Databasnamn anger** du *mySampleDatabase*.
1. För **Server** väljer **du Skapa ny** och fyller i formuläret Ny **server** enligt följande:
   - **Servernamn:** Ange *mysqlserver* och vissa tecken för unikhet.
   - **Inloggning för serveradministratör:** Ange *azureuser*.
   - **Lösenord:** Ange ett lösenord som uppfyller kraven och ange det igen i **fältet Bekräfta** lösenord.
   - **Plats:** Listrutan och välj en plats, till exempel **(USA) USA, östra.**

   Välj **OK**.

   ![Ny server](./media/sql-database-create-single-database/new-server.png)

   Registrera inloggningen och lösenordet för serveradministratören så att du kan logga in på servern och dess databaser. Om du glömmer din inloggning eller ditt lösenord kan du hämta inloggningsnamnet eller återställa lösenordet på **SQL-serversidan** när databasen har skapats. Öppna **SQL-serversidan** genom att välja servernamnet på **databasens översiktssida.**

1. Under **Beräkning + lagring** väljer du Konfigurera databas om du vill konfigurera om **standardinställningarna.**

   På sidan **Konfigurera** kan du välja att:
   - Ändra **beräkningsnivån från** **Etablerad till** **Serverlös**.
   - Granska och ändra inställningarna för **virtuella kärnor och** maximal **datastorlek.**
   - Välj **Ändra konfiguration för** att ändra maskinvarugenereringen.

   När du har gjort några ändringar väljer du **Tillämpa**.

1. Välj **Nästa:** Nätverk längst ned på sidan.

   ![Ny SQL-databas – fliken Grundläggande](./media/sql-database-create-single-database/new-sql-database-basics.png)

1. På fliken **Nätverk** går du till **Anslutningsmetod och** väljer **Offentlig slutpunkt.**
1. Under **Brandväggsregler anger** du **Lägg till aktuell klient-IP-adress** till **Ja.**
1. Välj **Nästa: Ytterligare** inställningar längst ned på sidan.

   ![Fliken Nätverk](./media/sql-database-create-single-database/networking.png)
  
   Mer information om brandväggsinställningar finns i [Ge Azure-tjänster och -resurser åtkomst till den här servern och](../database/network-access-controls-overview.md) Lägga till en privat [slutpunkt.](../database/private-endpoint-overview.md)

1. På fliken **Ytterligare inställningar** går du till **avsnittet Datakälla** och väljer Exempel för Använd **befintliga data.** 
1. Du kan också aktivera [Azure Defender för SQL](../database/azure-defender-for-sql.md).
1. Du kan också ange [underhållsfönstret så](../database/maintenance-window.md) att planerat underhåll utförs vid den bästa tidpunkten för din databas.
1. Välj **Granska +** skapa längst ned på sidan.

   ![Fliken Ytterligare inställningar](./media/sql-database-create-single-database/additional-settings.png)

1. När du har granskat inställningarna väljer du **Skapa**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan skapa en Azure-resursgrupp, server och en enkel databas med hjälp av Azure-kommandoradsgränssnittet (Azure CLI). Om du inte vill använda Azure Cloud Shell installerar [du Azure CLI](/cli/azure/install-azure-cli) på datorn.

Om du vill köra följande kodexempel Azure Cloud Shell väljer du **Prova i** namnlisten för kodexerollen. När Cloud Shell öppnas väljer du **Kopiera** i namnlisten för kodexerollen och klistrar in kodexerollen i Cloud Shell fönstret. I koden ersätter du med ditt Azure-prenumerations-ID och för och , ersätter med den offentliga `<Subscription ID>` `$startip` `$endip` `0.0.0.0` IP-adressen för den dator som du använder.

Följ anvisningarna på skärmen för att logga in på Azure och köra koden.

Du kan också använda Azure Cloud Shell från Azure Portal genom att välja ikonen Cloud Shell i det översta fältet.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Första gången du använder Cloud Shell i portalen väljer du **Bash** i **dialogrutan** Välkommen. Efterföljande sessioner använder Azure CLI i en Bash-miljö, eller så kan du välja **Bash** från Cloud Shell kontrollfältet.

Följande Azure CLI-kod skapar en IP-brandväggsregel för resursgrupp, server, enkel databas och servernivå för åtkomst till servern. Se till att registrera de genererade resursgrupps- och servernamnen så att du kan hantera dessa resurser senare.

```azurecli-interactive
#!/bin/bash

# Sign in to Azure and set execution context (if necessary)
az login
az account set --subscription <Subscription ID>

# Set the resource group name and location for your server
resourceGroupName=myResourceGroup-$RANDOM
location=westus2

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
servername=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a server in the resource group
az sql server create \
    --name $servername \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password

# Configure a server-level firewall rule for the server
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $servername \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip

# Create a gen5 2 vCore database in the server
az sql db create \
    --resource-group $resourceGroupName \
    --server $servername \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --family Gen5 \
    --capacity 2 \
```

Koden ovan använder följande Azure CLI-kommandon:

| Kommando | Beskrivning |
|---|---|
| [az account set](/cli/azure/account#az_account_set) | Anger att en prenumeration ska vara den aktuella aktiva prenumerationen. |
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az_sql_server_create) | Skapar en server som är värd för databaser och elastiska pooler. |
| [az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_create) | Skapar en brandväggsregel på servernivå. |
| [az sql db create](/cli/azure/sql/db#az_sql_db_create) | Skapar en databas. |

Mer information Azure SQL Database Azure CLI-exempel finns i [Azure CLI-exempel.](../database/az-cli-script-samples-content-guide.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan skapa en resursgrupp, server och en enkel databas med hjälp av Windows PowerShell. Om du inte vill använda Azure Cloud Shell installerar [du Azure PowerShell modulen](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill köra följande kodexempel i Azure Cloud Shell väljer du **Prova i** namnlisten för kod. När Cloud Shell öppnas väljer du **Kopiera** i namnlisten för kodexerollen och klistrar in kodexerollen i Cloud Shell fönster. I koden ersätter du med ditt Azure-prenumerations-ID och för och , ersätter med den offentliga `<Subscription ID>` `$startIp` `$endIp` `0.0.0.0` IP-adressen för den dator som du använder.

Följ anvisningarna på skärmen för att logga in på Azure och köra koden.

Du kan också använda Azure Cloud Shell från Azure Portal genom att välja ikonen Cloud Shell det översta fältet.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Första gången du använder Cloud Shell från portalen väljer du **PowerShell** i **dialogrutan** Välkommen. Efterföljande sessioner använder PowerShell, eller så kan du välja det från Cloud Shell kontrollfältet.

Följande PowerShell-kod skapar en Azure-resursgrupp, server, enkel databas och brandväggsregel för åtkomst till servern. Se till att registrera de genererade resursgrupps- och servernamnen så att du kan hantera dessa resurser senare.

   ```powershell-interactive
   # Set variables for your server and database
   $subscriptionId = '<SubscriptionID>'
   $resourceGroupName = "myResourceGroup-$(Get-Random)"
   $location = "West US"
   $adminLogin = "azureuser"
   $password = "Azure1234567"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName

   # Connect to Azure
   Connect-AzAccount

   # Set subscription ID
   Set-AzContext -SubscriptionId $subscriptionId

   # Create a resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup

   # Create a server with a system wide unique server name
   Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server

   # Create a server firewall rule that allows access from the specified IP range
   Write-host "Configuring firewall for primary server..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule

   # Create General Purpose Gen4 database with 1 vCore
   Write-host "Creating a gen5 2 vCore database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -VCore 2 `
      -ComputeGeneration Gen5 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
   ```

Koden ovan använder följande PowerShell-cmdlets:

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brandväggsregel på servernivå för en server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en databas. |

Mer information Azure SQL Database PowerShell-exempel finns i [Azure PowerShell exempel](../database/powershell-script-content-guide.md).

---
