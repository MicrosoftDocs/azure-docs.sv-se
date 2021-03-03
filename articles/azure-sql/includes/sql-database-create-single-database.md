---
author: MashaMSFT
ms.service: sql-database
ms.subservice: single-database
ms.topic: include
ms.date: 03/10/2020
ms.author: sstein
ms.reviewer: vanto
ms.openlocfilehash: 57f504b15c0a9c72a2cb0f17b486846f44171a25
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101682404"
---
I det här steget skapar du en [logisk SQL-Server](../database/logical-servers.md) och en [enkel databas](../database/single-database-overview.md) som använder AdventureWorksLT exempel data. Du kan skapa databasen med hjälp av Azure Portal menyer och skärmar, eller genom att använda ett Azure CLI-eller PowerShell-skript i Azure Cloud Shell.

Alla metoder är att ställa in en brand Väggs regel på server nivå för att tillåta den offentliga IP-adressen för den dator som du använder för att få åtkomst till servern. Mer information om hur du skapar brand Väggs regler på server nivå finns i [skapa en brand vägg på server nivå](../database/firewall-create-server-level-portal-quickstart.md). Du kan också ange brand Väggs regler på databas nivå. Se [skapa en brand Väggs regel på databas nivå](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Så här skapar du en resurs grupp, server och en enskild databas i Azure Portal:

1. Logga in på [portalen](https://portal.azure.com).
1. I Sök fältet söker du efter och väljer **Azure SQL**.
1. På **Azure SQL** -sidan väljer du **Lägg till**.

   ![Lägg till i Azure SQL](./media/sql-database-create-single-database/sqldbportal.png)

1. På sidan **Välj alternativ för SQL-distribution** väljer du panelen **SQL-databaser** med **en enda databas** under **resurs typ**. Du kan visa mer information om de olika databaserna genom att välja **Visa information**.
1. Välj **Skapa**.

   ![Skapa en enkel databas](./media/sql-database-create-single-database/create-single-database.png)

1. På fliken **grundläggande** i formuläret **skapa SQL-databas** går du till **projekt information** och väljer rätt Azure- **prenumeration** om den inte redan är markerad.
1. Under **resurs grupp** väljer du **Skapa ny**, anger *myResourceGroup* och väljer **OK**.
1. Under **databas information** anger du *mySampleDatabase* som **databas namn** .
1. För **Server** väljer du **Skapa ny** och fyller i det **nya server** formuläret enligt följande:
   - **Server namn**: ange *SQLServer* och några tecken för unikhet.
   - **Inloggning för Server administratör**: ange *azureuser*.
   - **Lösen ord**: Ange ett lösen ord som uppfyller kraven och ange det igen i fältet **Bekräfta lösen ord** .
   - **Plats**: list rutan och välj en plats, t. ex. **(US) östra USA**.

   Välj **OK**.

   ![Ny server](./media/sql-database-create-single-database/new-server.png)

   Registrera inloggning och lösen ord för Server administratören så att du kan logga in på servern och databaserna. Om du glömmer ditt inloggnings namn eller lösen ord kan du hämta inloggnings namnet eller återställa lösen ordet på **SQL Server** -sidan när databasen har skapats. Öppna **SQL Server** -sidan genom att välja Server namnet på sidan databas **Översikt** .

1. Under **Compute + Storage**, om du vill konfigurera om standardvärdena, väljer du **Konfigurera databas**.

   På sidan **Konfigurera** kan du välja att:
   - Ändra **beräknings nivån** från **etablerad** till **Server** lös.
   - Granska och ändra inställningarna för **virtuella kärnor** och **data Max storlek**.
   - Välj **ändra konfiguration** för att ändra maskin varu genereringen.

   När du har gjort ändringarna väljer du **tillämpa**.

1. Välj **Nästa: nätverk** längst ned på sidan.

   ![Ny SQL-databas – fliken grundläggande](./media/sql-database-create-single-database/new-sql-database-basics.png)

1. På fliken **nätverk** under **anslutnings metod** väljer du **offentlig slut punkt**.
1. Under **brand Väggs regler** anger du **Lägg till aktuell klient-IP-adress** till **Ja**.
1. Välj **Nästa: ytterligare inställningar** längst ned på sidan.

   ![Fliken nätverk](./media/sql-database-create-single-database/networking.png)
  
   Mer information om brand Väggs inställningar finns i [ge Azure-tjänster och-resurser åtkomst till den här servern](../database/network-access-controls-overview.md) och [lägga till en privat slut punkt](../database/private-endpoint-overview.md).

1. På fliken **ytterligare inställningar** , i avsnittet **data källa** , för att **använda befintliga data**, väljer du **exempel**.
1. Du kan också aktivera [Azure Defender för SQL](../database/azure-defender-for-sql.md).
1. Du kan också ställa in [underhålls fönstret](../database/maintenance-window.md) så att planerat underhåll utförs på den bästa tiden för din databas.
1. Välj **Granska + skapa** längst ned på sidan.

   ![Fliken Ytterligare inställningar](./media/sql-database-create-single-database/additional-settings.png)

1. När du har granskat inställningarna väljer du **skapa**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Du kan skapa en Azure-resurs grupp, server och en enkel databas med hjälp av Azures kommando rads gränssnitt (Azure CLI). Om du inte vill använda Azure Cloud Shell [installerar du Azure CLI](/cli/azure/install-azure-cli) på datorn.

Om du vill köra följande kod exempel i Azure Cloud Shell väljer du **testa det** i namn listen kod exempel. När Cloud Shell öppnas väljer du **Kopiera** i namn listen kod exempel och klistrar in kod exemplet i Cloud Shells fönstret. Ersätt `<Subscription ID>` med ditt Azure-prenumerations-ID i koden och `$startip` `$endip` Ersätt `0.0.0.0` med den offentliga IP-adressen för den dator som du använder.

Följ anvisningarna på skärmen för att logga in på Azure och kör koden.

Du kan också använda Azure Cloud Shell från Azure Portal genom att välja Cloud Shells ikonen i det översta fältet.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Första gången du använder Cloud Shell i portalen väljer du **bash** i **välkomst** dialog rutan. Efterföljande sessioner kommer att använda Azure CLI i en bash-miljö, eller så kan du välja **bash** från Cloud Shell kontroll fältet.

Följande Azure CLI-kod skapar en resurs grupp, Server, enskild databas och en IP-brandväggsregel på server nivå för åtkomst till servern. Se till att du registrerar de genererade resurs grupperna och Server namnen, så att du kan hantera dessa resurser senare.

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

I föregående kod används följande Azure CLI-kommandon:

| Kommando | Beskrivning |
|---|---|
| [AZ-konto uppsättning](/cli/azure/account?view=azure-cli-latest#az-account-set&preserve-view=true) | Anger att en prenumeration är den aktuella aktiva prenumerationen. |
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az sql server create](/cli/azure/sql/server#az-sql-server-create) | Skapar en server som är värd för databaser och elastiska pooler. |
| [AZ SQL Server-brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-create) | Skapar en brand Väggs regel på server nivå. |
| [az sql db create](/cli/azure/sql/db#az-sql-db-create?view=azure-cli-latest&preserve-view=true) | Skapar en databas. |

Mer Azure SQL Database Azure CLI-exempel finns i [Azure CLI-exempel](../database/az-cli-script-samples-content-guide.md).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan skapa en resurs grupp, server och en enskild databas med Windows PowerShell. Om du inte vill använda Azure Cloud Shell [installerar du modulen Azure PowerShell](/powershell/azure/install-az-ps).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Om du vill köra följande kod exempel i Azure Cloud Shell väljer du **testa det** i kod namn listen. När Cloud Shell öppnas väljer du **Kopiera** i namn listen kod exempel och klistrar in kod exemplet i Cloud Shells fönstret. Ersätt `<Subscription ID>` med ditt Azure-prenumerations-ID i koden och `$startIp` `$endIp` Ersätt `0.0.0.0` med den offentliga IP-adressen för den dator som du använder.

Följ anvisningarna på skärmen för att logga in på Azure och kör koden.

Du kan också använda Azure Cloud Shell från Azure Portal genom att välja Cloud Shells ikonen i det översta fältet.

   ![Azure Cloud Shell](./media/sql-database-create-single-database/cloudshell.png)

Första gången du använder Cloud Shell från portalen väljer du **PowerShell** i **välkomst** dialog rutan. Efterföljande sessioner kommer att använda PowerShell, eller så kan du välja den från Cloud Shell kontroll fältet.

Följande PowerShell-kod skapar en Azure-resurs grupp, Server, enkel databas och brand Väggs regel för åtkomst till servern. Se till att du registrerar de genererade resurs grupperna och Server namnen, så att du kan hantera dessa resurser senare.

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

Föregående kod använder dessa PowerShell-cmdlet: ar:

| Kommando | Kommentarer |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) | Skapar en server som är värd för databaser och elastiska pooler. |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) | Skapar en brand Väggs regel på server nivå för en server. |
| [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) | Skapar en databas. |

Mer Azure SQL Database PowerShell-exempel finns i [Azure PowerShell exempel](../database/powershell-script-content-guide.md).

---
