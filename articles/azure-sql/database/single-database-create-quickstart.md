---
title: Skapa en enkel databas
description: Skapa en enda databas i Azure SQL Database med hjälp av Azure Portal, PowerShell eller Azure CLI.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/27/2021
ms.openlocfilehash: c0e86cad252da1b5484944c12351d4ee1e39edbe
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101656316"
---
# <a name="quickstart-create-an-azure-sql-database-single-database"></a>Snabb start: skapa en Azure SQL Database enskild databas

I den här snabb starten skapar du en [enskild databas](single-database-overview.md) i Azure SQL Database med antingen Azure Portal, ett PowerShell-skript eller ett Azure CLI-skript. Sedan frågar du databasen med hjälp av **Frågeredigeraren** i Azure Portal.



## <a name="prerequisite"></a>Förutsättning

- En aktiv Azure-prenumeration. Om du inte har en [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).

## <a name="create-a-single-database"></a>Skapa en enkel databas

Den här snabb starten skapar en enskild databas i [Server lös beräknings nivån](serverless-tier-overview.md).

# <a name="portal"></a>[Portal](#tab/azure-portal)

Om du vill skapa en enskild databas i Azure Portal den här snabb starten starta på Azure SQL-sidan.

1. Bläddra till [alternativ sidan Välj SQL-distribution](https://portal.azure.com/#create/Microsoft.AzureSQL) .
1. Under **SQL-databaser**, lämna **resurs typ** inställt på **en enskild databas** och välj **skapa**.

   ![Lägg till i Azure SQL](./media/single-database-create-quickstart/select-deployment.png)

1. På fliken **grundläggande** i formuläret **skapa SQL Database** går du till **projekt information** och väljer önskad Azure- **prenumeration**.
1. För **resurs grupp** väljer du **Skapa ny**, anger *myResourceGroup* och väljer **OK**.
1. Ange *mySampleDatabase* för **databas namnet** .
1. För **Server** väljer du **Skapa ny** och fyller i formuläret för den **nya servern** med följande värden:
   - **Server namn**: ange *SQLServer* och Lägg till några tecken för unikhet. Vi kan inte ange ett exakt Server namn som ska användas eftersom Server namn måste vara globalt unika för alla servrar i Azure, inte bara unika inom en prenumeration. Därför kan du ange något som mysqlserver12345, så kan du använda portalen för att se om den är tillgänglig eller inte.
   - **Inloggning för Server administratör**: ange *azureuser*.
   - **Lösen ord**: Ange ett lösen ord som uppfyller kraven och ange det igen i fältet **Bekräfta lösen ord** .
   - **Plats**: Välj en plats i list rutan.

   Välj **OK**.

1. Om **du inte vill använda elastisk SQL-pool** väljer du **Nej**.
1. Under **Beräkning och lagring** väljer du **Konfigurera databas**.
1. Den här snabb starten använder en server lös databas, så välj **Server** lös, och välj sedan **Använd**. 

      ![Konfigurera Server lös databas](./media/single-database-create-quickstart/configure-database.png)

1. Välj **Nästa: nätverk** längst ned på sidan.

   ![Ny SQL-databas – fliken grundläggande](./media/single-database-create-quickstart/new-sql-database-basics.png)

1. På fliken **nätverk** , för **anslutnings metod**, väljer du **offentlig slut punkt**.
1. För **brand Väggs regler** anger du **Lägg till aktuell klient-IP-adress** till **Ja**. Lämna **Tillåt att Azure-tjänster och-resurser har åtkomst till den här servern** **.**
1. Välj **Nästa: ytterligare inställningar** längst ned på sidan.

   ![Fliken nätverk](./media/single-database-create-quickstart/networking.png)
  

1. På fliken **ytterligare inställningar** , i avsnittet **data källa** , för att **använda befintliga data**, väljer du **exempel**. Detta skapar en AdventureWorksLT-exempel databas så att det finns några tabeller och data att fråga och experimentera med, i stället för en tom tom databas.
1. Du kan också aktivera [Azure Defender för SQL](../database/azure-defender-for-sql.md).
1. Du kan också ställa in [underhålls fönstret](../database/maintenance-window.md) så att planerat underhåll utförs på den bästa tiden för din databas.
1. Välj **Granska + skapa** längst ned på sidan:

   ![Fliken Ytterligare inställningar](./media/single-database-create-quickstart/additional-settings.png)

1. På sidan **Granska och skapa** efter granskning väljer du **skapa**.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com](https://shell.azure.com) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och tryck på **RETUR** för att köra den.

## <a name="set-parameter-values"></a>Ange parameter värden

Följande värden används i efterföljande kommandon för att skapa databasen och nödvändiga resurser. Server namn måste vara globalt unika i hela Azure, så $RANDOM funktionen används för att skapa Server namnet. Ersätt värdena 0.0.0.0 i IP-adressintervallet för att matcha din speciella miljö.

```azurecli-interactive
# Set the resource group name and location for your server
resourceGroupName=myResourceGroup
location=eastus

# Set an admin login and password for your database
adminlogin=azureuser
password=Azure1234567!

# Set a server name that is unique to Azure DNS (<server_name>.database.windows.net)
serverName=server-$RANDOM

# Set the ip address range that can access your database
startip=0.0.0.0
endip=0.0.0.0
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli-interactive
az group create --name $resourceGroupName --location $location
```

## <a name="create-a-server"></a>Skapa en server

Skapa en server med kommandot [AZ SQL Server Create](/cli/azure/sql/server) .

```azurecli-interactive
az sql server create \
    --name $serverName \
    --resource-group $resourceGroupName \
    --location $location  \
    --admin-user $adminlogin \
    --admin-password $password
```


## <a name="configure-a-firewall-rule-for-the-server"></a>Konfigurera en brand Väggs regel för servern

Skapa en brand Väggs regel med kommandot [AZ SQL Server Firewall-Rule Create](/cli/azure/sql/server/firewall-rule) .

```azurecli-interactive
az sql server firewall-rule create \
    --resource-group $resourceGroupName \
    --server $serverName \
    -n AllowYourIp \
    --start-ip-address $startip \
    --end-ip-address $endip
```


## <a name="create-a-single-database-with-azure-cli"></a>Skapa en enkel databas med Azure CLI

Skapa en databas med kommandot [AZ SQL DB Create](/cli/azure/sql/db) . Följande kod skapar


```azurecli-interactive
az sql db create \
    --resource-group $resourceGroupName \
    --server $serverName \
    --name mySampleDatabase \
    --sample-name AdventureWorksLT \
    --edition GeneralPurpose \
    --compute-model Serverless \
    --family Gen5 \
    --capacity 2
```


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Du kan skapa en resurs grupp, server och en enskild databas med Windows PowerShell.

## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com](https://shell.azure.com) . Välj **Kopiera** för att kopiera kod blocken, klistra in den i Cloud Shell och tryck på **RETUR** för att köra den.

## <a name="set-parameter-values"></a>Ange parameter värden

Följande värden används i efterföljande kommandon för att skapa databasen och nödvändiga resurser. Server namn måste vara globalt unika i hela Azure, så Get-Random cmdlet används för att skapa Server namnet. Ersätt värdena 0.0.0.0 i IP-adressintervallet för att matcha din speciella miljö.

```azurepowershell-interactive
   # Set variables for your server and database
   $resourceGroupName = "myResourceGroup"
   $location = "eastus"
   $adminLogin = "azureuser"
   $password = "Azure1234567!"
   $serverName = "mysqlserver-$(Get-Random)"
   $databaseName = "mySampleDatabase"

   # The ip address range that you want to allow to access your server
   $startIp = "0.0.0.0"
   $endIp = "0.0.0.0"

   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Server name is" $serverName
```


## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en Azure-resursgrupp med [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras.

```azurepowershell-interactive
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
```


## <a name="create-a-server"></a>Skapa en server

Skapa en server med cmdleten [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) .

```azurepowershell-interactive
  Write-host "Creating primary server..."
   $server = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -Location $location `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
      -ArgumentList $adminLogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $server
```

## <a name="create-a-firewall-rule"></a>Skapa en brandväggsregel

Skapa en server brand Väggs regel med cmdleten [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) .

```azurepowershell-interactive
   Write-host "Configuring server firewall rule..."
   $serverFirewallRule = New-AzSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FirewallRuleName "AllowedIPs" -StartIpAddress $startIp -EndIpAddress $endIp
   $serverFirewallRule
```


## <a name="create-a-single-database-with-powershell"></a>Skapa en enskild databas med PowerShell

Skapa en enskild databas med cmdleten [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase) .

```azurepowershell-interactive
   Write-host "Creating a gen5 2 vCore serverless database..."
   $database = New-AzSqlDatabase  -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName `
      -Edition GeneralPurpose `
      -ComputeModel Serverless `
      -ComputeGeneration Gen5 `
      -VCore 2 `
      -MinimumCapacity 2 `
      -SampleName "AdventureWorksLT"
   $database
```

---



## <a name="query-the-database"></a>Fråga databasen

När databasen har skapats kan du använda **Frågeredigeraren (förhands granskning)** i Azure Portal för att ansluta till databasen och fråga efter data.

1. I portalen söker du efter och väljer **SQL-databaser** och väljer sedan din databas i listan.
1. På sidan för din databas väljer du **Frågeredigeraren (för hands version)** på den vänstra menyn.
1. Ange inloggnings information för Server administratören och välj **OK**.

   ![Logga in på Frågeredigeraren](./media/single-database-create-quickstart/query-editor-login.png)

1. Skriv följande fråga i fönstret **Frågeredigeraren**.

   ```sql
   SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

1. Välj **Kör** och granska sedan frågeresultaten i fönstret **Resultat**.

   ![Resultat från Frågeredigeraren](./media/single-database-create-quickstart/query-editor-results.png)

1. Stäng sidan med **Frågeredigeraren** och klicka på **OK** för att ta bort de ändringar som inte har sparats.

## <a name="clean-up-resources"></a>Rensa resurser

Behåll resurs gruppen, servern och den enskilda databasen för att gå vidare till nästa steg och lär dig hur du ansluter och frågar databasen med olika metoder.

När du är klar med de här resurserna kan du ta bort resurs gruppen som du skapade, vilket även tar bort servern och en enskild databas i den.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Så här tar du bort **myResourceGroup** och alla dess resurser med hjälp av Azure Portal:

1. I portalen söker du efter och väljer **resurs grupper** och väljer sedan **myResourceGroup** i listan.
1. På sidan resurs grupp väljer du **ta bort resurs grupp**.
1. Under **Ange resurs gruppens namn** skriver du *myResourceGroup* och väljer sedan **ta bort**.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill ta bort resurs gruppen och alla dess resurser kör du följande Azure CLI-kommando med namnet på din resurs grupp:

```azurecli-interactive
az group delete --name $resourceGroupName
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill ta bort resurs gruppen och alla dess resurser kör du följande PowerShell-cmdlet med namnet på din resurs grupp:

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

---

## <a name="next-steps"></a>Nästa steg

[Anslut och fråga](connect-query-content-reference-guide.md) databasen med olika verktyg och språk:
> [!div class="nextstepaction"]
> [Ansluta och köra frågor med SQL Server Management Studio](connect-query-ssms.md)
>
> [Ansluta och köra frågor med Azure Data Studio](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)

Vill du optimera och Spara på dina moln utgifter?

> [!div class="nextstepaction"]
> [Börja analysera kostnaderna med Cost Management](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)