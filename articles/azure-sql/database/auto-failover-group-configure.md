---
title: Konfigurera en redundansgrupp
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig hur du konfigurerar en automatisk redundansgrupp för en Azure SQL Database (både enkel och poolad) och SQL Managed Instance med hjälp av Azure Portal, Azure CLI och PowerShell.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: a2f0cb683669aa092493c8080d5e4646cf9706c3
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477945"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Konfigurera en redundansgrupp för Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

I det här avsnittet lär du dig hur du konfigurerar en automatisk [redundansgrupp](auto-failover-group-overview.md) för Azure SQL Database och Azure SQL Managed Instance.

## <a name="single-database"></a>Enskild databas

Skapa redundansgruppen och lägg till en enkel databas i den med hjälp av Azure Portal eller PowerShell.

### <a name="prerequisites"></a>Förutsättningar

Tänk på följande:

- Inställningarna för serverinloggning och brandvägg för den sekundära servern måste matcha inställningarna för din primära server.

### <a name="create-failover-group"></a>Skapa redundansgrupp

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa redundansgruppen och lägg till din enkla databas i den med hjälp av Azure Portal.

1. Välj **Azure SQL** på menyn till vänster i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **Alla** tjänster och skriver Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid den **Azure SQL** favorit och lägg till den som ett objekt i det vänstra navigeringsfönstret.
1. Välj den databas som du vill lägga till i redundansgruppen.
1. Välj namnet på servern under **Servernamn för** att öppna inställningarna för servern.

   ![Öppna server för enskild databas](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Välj **Redundansgrupper** under **fönstret Inställningar** och välj sedan Lägg till grupp **för** att skapa en ny redundansgrupp.

   ![Lägg till ny redundansgrupp](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. På sidan **Redundansgrupp** anger eller väljer du de värden som krävs och väljer sedan **Skapa.**

   - **Databaser i gruppen :** Välj den databas som du vill lägga till i redundansgruppen. Om du lägger till databasen i redundansgruppen startar den geo-replikeringen automatiskt.

   ![Lägga SQL Database i redundansgrupp](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa redundansgruppen och lägg till databasen i den med hjälp av PowerShell.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer

   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      â€“ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      â€“FailoverGroupName $failoverGroupName `
      â€“FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup

   # Add the database to the failover group
   Write-host "Adding the database to the failover group..."
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..."
   ```

---

### <a name="test-failover"></a>Redundanstest

Testa redundans för din redundansgrupp med hjälp Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för din redundansgrupp med hjälp av Azure Portal.

1. Välj **Azure SQL** på menyn till vänster i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **Alla** tjänster och skriver sedan "Azure SQL" i sökrutan. (Valfritt) Välj stjärnan bredvid Azure SQL **favorit** och lägg till den som ett objekt i det vänstra navigeringsfönstret.
1. Välj den databas som du vill lägga till i redundansgruppen.

   ![Öppna servern för enkel databas](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Välj **Redundansgrupper** under **fönstret** Inställningar och välj sedan den redundansgrupp som du nyss skapade.
  
   ![Välj redundansgruppen från portalen](./media/auto-failover-group-configure/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär.
1. Välj **Redundans** i åtgärdsfönstret för att redundans redundans grupp som innehåller din databas.
1. Välj **Ja** i varningen som meddelar dig att TDS-sessioner kommer att kopplas från.

   ![Redundans redundansgruppen som innehåller din databas](./media/auto-failover-group-configure/failover-sql-db.png)

1. Granska vilken server som nu är primär och vilken server som är sekundär. Om redundansväxlingen lyckades bör de två servrarna ha växlade roller.
1. Välj **Redundans** igen för att växla tillbaka servrarna till sina ursprungliga roller.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testa redundans för din redundansgrupp med PowerShell.  

Kontrollera den sekundära replikens roll:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Redundans till den sekundära servern:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

Återställ redundansgruppen tillbaka till den primära servern:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group successfully to back to" $serverName
   ```

---

> [!IMPORTANT]
> Om du behöver ta bort den sekundära databasen tar du bort den från redundansgruppen innan du tar bort den. Att ta bort en sekundär databas innan den tas bort från redundansgruppen kan orsaka oförutsägbart beteende.

## <a name="elastic-pool"></a>Elastisk pool

Skapa redundansgruppen och lägg till en elastisk pool till den med hjälp Azure Portal eller PowerShell.  

### <a name="prerequisites"></a>Förutsättningar

Tänk på följande:

- Inställningarna för serverinloggning och brandvägg för den sekundära servern måste matcha inställningarna för din primära server.

### <a name="create-the-failover-group"></a>Skapa redundansgruppen

Skapa redundansgruppen för din elastiska pool med hjälp av Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa redundansgruppen och lägg till den elastiska poolen med hjälp av Azure Portal.

1. Välj **Azure SQL** på menyn till vänster i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **Alla** tjänster och skriver sedan "Azure SQL" i sökrutan. (Valfritt) Välj stjärnan bredvid den **Azure SQL** favorit och lägg till den som ett objekt i det vänstra navigeringsfönstret.
1. Välj den elastiska pool som du vill lägga till i redundansgruppen.
1. I fönstret **Översikt** väljer du namnet på servern under **Servernamn för** att öppna inställningarna för servern.
  
   ![Öppna servern för elastisk pool](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Välj **Redundansgrupper** under **fönstret Inställningar** och välj sedan Lägg till grupp **för** att skapa en ny redundansgrupp.

   ![Lägg till ny redundansgrupp](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. På sidan **Redundansgrupp** anger eller väljer du de värden som krävs och väljer sedan **Skapa.** Skapa antingen en ny sekundär server eller välj en befintlig sekundär server.

1. Välj **Databaser i gruppen och** välj sedan den elastiska pool som du vill lägga till i redundansgruppen. Om det inte redan finns en elastisk pool på den sekundära servern visas en varning som uppmanar dig att skapa en elastisk pool på den sekundära servern. Välj varningen och välj sedan **OK för att** skapa den elastiska poolen på den sekundära servern.

   ![Lägga till elastisk pool i redundansgrupp](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Välj **Välj** för att tillämpa dina elastiska poolinställningar på redundansgruppen och välj sedan **Skapa för** att skapa redundansgruppen. Om du lägger till den elastiska poolen i redundansgruppen startar den geo-replikeringsprocessen automatiskt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa redundansgruppen och lägg till din elastiska pool i den med hjälp av PowerShell.

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $poolName = "myElasticPool"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

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
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Redundanstest

Testa redundans för din elastiska pool med hjälp Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Växla över redundansgruppen till den sekundära servern och växla sedan tillbaka med hjälp av Azure Portal.

1. Välj **Azure SQL** på menyn till vänster i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **Alla** tjänster och skriver sedan "Azure SQL" i sökrutan. (Valfritt) Välj stjärnan bredvid Azure SQL **favorit** och lägg till den som ett objekt i det vänstra navigeringsfönstret.
1. Välj den elastiska pool som du vill lägga till i redundansgruppen.
1. I fönstret **Översikt** väljer du namnet på servern under **Servernamn för** att öppna inställningarna för servern.

   ![Öppna servern för elastisk pool](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. Välj **Redundansgrupper** i **fönstret** Inställningar och välj sedan den redundansgrupp som du skapade i avsnitt 2.
  
   ![Välj redundansgruppen från portalen](./media/auto-failover-group-configure/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär.
1. Välj **Redundans** i åtgärdsfönstret för att redundans redundans grupp som innehåller den elastiska poolen.
1. Välj **Ja** i varningen som meddelar dig att TDS-sessioner kommer att kopplas från.

   ![Redundans redundansgruppen som innehåller din databas](./media/auto-failover-group-configure/failover-sql-db.png)

1. Granska vilken server som är primär, vilken server som är sekundär. Om redundansväxlingen lyckades bör de två servrarna ha växlat roller.
1. Välj **Redundans** igen för att växla tillbaka redundansgruppen till de ursprungliga inställningarna.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testa redundans för redundansgruppen med Hjälp av PowerShell.

Kontrollera rollen för den sekundära repliken:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...."
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```

Redundans till den sekundära servern:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..."
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName
   ```

---

> [!IMPORTANT]
> Om du behöver ta bort den sekundära databasen tar du bort den från redundansgruppen innan du tar bort den. Att ta bort en sekundär databas innan den tas bort från redundansgruppen kan orsaka oförutsägbart beteende.

## <a name="sql-managed-instance"></a>SQL-hanterad instans

Skapa en redundansgrupp mellan två hanterade instanser Azure SQL Managed Instance med hjälp av Azure Portal eller PowerShell.

Du måste antingen konfigurera [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) eller skapa en gateway för det virtuella nätverket för varje SQL Managed Instance, ansluta de två gatewayerna och sedan skapa redundansgruppen. 

Distribuera båda hanterade instanserna [till parkopplade regioner](../../best-practices-availability-paired-regions.md) av prestandaskäl. Hanterade instanser som finns i geo-parade regioner har mycket bättre prestanda jämfört med icke-parkopplade regioner. 

### <a name="prerequisites"></a>Förutsättningar

Tänk på följande:

- Den sekundära hanterade instansen måste vara tom.
- Undernätsintervallet för det sekundära virtuella nätverket får inte överlappa det primära virtuella nätverkets undernätsintervall.
- Sorteringen och tidszonen för den sekundära hanterade instansen måste matcha den primära hanterade instansen.
- När du ansluter de två gatewayerna ska **den delade nyckeln** vara samma för båda anslutningarna.

### <a name="create-primary-virtual-network-gateway"></a>Skapa en primär virtuell nätverksgateway

Om du inte har konfigurerat [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)kan du skapa den primära virtuella nätverksgatewayen med Azure Portal eller PowerShell.

> [!NOTE]
> Gatewayens SKU påverkar dataflödesprestandan. Den här artikeln distribuerar en gateway med den mest grundläggande SKU:n ( `HwGw1` ). Distribuera en högre SKU (exempel: `VpnGw3` ) för att uppnå högre dataflöde. Alla tillgängliga alternativ finns i [Gateway-SKU:er](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa den primära virtuella nätverksgatewayen med hjälp av Azure Portal.

1. I den [Azure Portal](https://portal.azure.com)går du till resursgruppen och väljer resursen **Virtuellt nätverk** för din primära hanterade instans.
1. Välj **Undernät under Inställningar** **och** välj sedan för att lägga till ett nytt **Gateway-undernät.** Låt standardvärdena vara kvar.

   ![Lägga till gateway för primär hanterad instans](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. När undernätsgatewayen har skapats **väljer du** Skapa en resurs i det vänstra navigeringsfönstret och skriver sedan `Virtual network gateway` i sökrutan. Välj resursen **Virtuell nätverksgateway** som publicerats av **Microsoft**.

   ![Skapa en ny virtuell nätverksgateway](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Fyll i de obligatoriska fälten för att konfigurera gatewayen till din primära hanterade instans.

   I följande tabell visas de värden som krävs för gatewayen för den primära hanterade instansen:

    | **Fält** | Värde |
    | --- | --- |
    | **Prenumeration** |  Den prenumeration där din primära hanterade instans finns. |
    | **Namn** | Namnet på din virtuella nätverksgateway. |
    | **Region** | Den region där din primära hanterade instans finns. |
    | **Gatewaytyp** | Välj **VPN**. |
    | **VPN-typ** | Välj **Ruttbaserad** |
    | **SKU**| Lämna standardvärdet `VpnGw1` . |
    | **Plats**| Platsen där den sekundära hanterade instansen och det sekundära virtuella nätverket finns.   |
    | **Virtuellt nätverk**| Välj det virtuella nätverket för din sekundära hanterade instans. |
    | **Offentlig IP-adress**| Välj **Skapa ny**. |
    | **Namn på offentlig IP-adress**| Ange ett namn för din IP-adress. |
    | &nbsp; | &nbsp; |

1. Lämna de andra värdena som standard och välj sedan **Granska + skapa för** att granska inställningarna för din virtuella nätverksgateway.

   ![Inställningar för primär gateway](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Välj **Skapa för** att skapa din nya virtuella nätverksgateway.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa den primära virtuella nätverksgatewayen med PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryVnetName = "<Primary-Virtual-Network-Name>"
   $primaryGWName = "<Primary-Gateway-Name>"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000

   # Get the primary virtual network
   $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
   $primaryLocation = $vnet1.Location

   # Create primary gateway
   Write-host "Creating primary gateway..."
   $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
   $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
            -Location $primaryLocation -AllocationMethod Dynamic
   $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id

   $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $gw1
   ```

---

### <a name="create-secondary-virtual-network-gateway"></a>Skapa en sekundär virtuell nätverksgateway

Skapa den sekundära virtuella nätverksgatewayen med hjälp Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Upprepa stegen i föregående avsnitt för att skapa det virtuella nätverkets undernät och gateway för den sekundära hanterade instansen. Fyll i de obligatoriska fälten för att konfigurera gatewayen för din sekundära hanterade instans.

I följande tabell visas de värden som krävs för gatewayen för den sekundära hanterade instansen:

   | **Fält** | Värde |
   | --- | --- |
   | **Prenumeration** |  Prenumerationen där den sekundära hanterade instansen finns. |
   | **Namn** | Namnet på din virtuella nätverksgateway, till exempel `secondary-mi-gateway` . |
   | **Region** | Den region där den sekundära hanterade instansen finns. |
   | **Gatewaytyp** | Välj **VPN**. |
   | **VPN-typ** | Välj **Ruttbaserad** |
   | **SKU**| Lämna standardvärdet `VpnGw1` . |
   | **Plats**| Den plats där den sekundära hanterade instansen och det sekundära virtuella nätverket finns.   |
   | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i avsnitt 2, till exempel `vnet-sql-mi-secondary` . |
   | **Offentlig IP-adress**| Välj **Skapa ny**. |
   | **Namn på offentlig IP-adress**| Ange ett namn för din IP-adress, till exempel `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Inställningar för sekundär gateway](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa den sekundära virtuella nätverksgatewayen med PowerShell.

   ```powershell-interactive
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
   $secondaryGWName = "<Secondary-Gateway-Name>"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000

   # Get the secondary virtual network
   $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
   $secondaryLocation = $vnet2.Location

   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
   $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
            -Location $secondaryLocation -AllocationMethod Dynamic
   $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id

   $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn

   $gw2
   ```

---

### <a name="connect-the-gateways"></a>Ansluta gatewayerna

Skapa anslutningar mellan de två gatewayerna med hjälp Azure Portal eller PowerShell.

Två anslutningar måste skapas – anslutningen från den primära gatewayen till den sekundära gatewayen och sedan anslutningen från den sekundära gatewayen till den primära gatewayen.

Den delade nyckeln som används för båda anslutningarna ska vara samma för varje anslutning.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa anslutningar mellan de två gatewayerna med hjälp av Azure Portal.

1. Välj **Skapa en resurs** från [Azure Portal](https://portal.azure.com).
1. Skriv `connection` i sökrutan och tryck sedan på Retur för att söka, vilket tar dig till **anslutningsresursen** som publicerats av Microsoft.
1. Välj **Skapa** för att skapa anslutningen.
1. På **fliken Grundläggande inställningar** väljer du följande värden och sedan **OK.**
    1. Välj `VNet-to-VNet` **anslutningstypen**.
    1. Välj din prenumeration från listrutan.
    1. Välj resursgruppen för den hanterade instansen i listrutan.
    1. Välj platsen för din primära hanterade instans i listrutan.
1. På fliken **Inställningar** väljer eller anger du följande värden och väljer sedan **OK:**
    1. Välj den primära nätverksgatewayen för **den första virtuella nätverksgatewayen,** till exempel `Primary-Gateway` .  
    1. Välj den sekundära nätverksgatewayen för **den andra virtuella nätverksgatewayen,** till exempel `Secondary-Gateway` .
    1. Markera kryssrutan bredvid Upprätta **dubbelriktad anslutning.**
    1. Lämna antingen det primära standardanslutningsnamnet eller byt namn på det till val annat värde.
    1. Ange en **delad nyckel (PSK)** för anslutningen, till exempel `mi1m2psk` .

   ![Skapa gatewayanslutning](./media/auto-failover-group-configure/create-gateway-connection.png)

1. På fliken **Sammanfattning** granskar du inställningarna för den dubbelriktade anslutningen och väljer sedan **OK för** att skapa anslutningen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa anslutningar mellan de två gatewayerna med hjälp av PowerShell.

   ```powershell-interactive
   $vpnSharedKey = "mi1mi2psk"
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $primaryGWConnection = "<Primary-connection-name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $secondaryGWConnection = "<Secondary-connection-name>"
   $secondaryLocation = "<Secondary-Region>"
  
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway"
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
       -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection

   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway"

   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
       -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

---

### <a name="create-the-failover-group"></a>Skapa redundansgruppen

Skapa redundansgruppen för dina hanterade instanser med hjälp av Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa redundansgruppen för dina SQL Managed Instances med hjälp av Azure Portal.

1. Välj **Azure SQL** på menyn till vänster i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **Alla** tjänster och skriver Azure SQL i sökrutan. (Valfritt) Välj stjärnan bredvid den **Azure SQL** favorit och lägg till den som ett objekt i det vänstra navigeringsfönstret.
1. Välj den primära hanterade instans som du vill lägga till i redundansgruppen.  
1. Under **Inställningar** navigerar du till **Redundansgrupper för instanser** och väljer sedan Lägg till **grupp** för att öppna sidan **Instans redundansgrupp.**

   ![Lägga till en redundansgrupp](./media/auto-failover-group-configure/add-failover-group.png)

1. På sidan **Instans redundansgrupp** skriver du namnet på redundansgruppen och väljer sedan den sekundära hanterade instansen i listrutan. Välj **Skapa för** att skapa redundansgruppen.

   ![Skapa redundansgrupp](./media/auto-failover-group-configure/create-failover-group.png)

1. När distributionen av redundansgruppen är klar kommer du tillbaka till sidan **Redundansgrupp.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa redundansgruppen för dina hanterade instanser med hjälp av PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Create failover group
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
        -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

---

### <a name="test-failover"></a>Redundanstest

Testa redundans för din redundansgrupp med hjälp Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för din redundansgrupp med hjälp av Azure Portal.

1. Gå till den _sekundära hanterade_ instansen i [Azure Portal](https://portal.azure.com) och välj **Instans redundansgrupper** under Inställningar.
1. Granska vilken hanterad instans som är den primära och vilken hanterad instans som är den sekundära.
1. Välj **Redundans** och sedan **Ja för varningen** om att TDS-sessioner kopplas från.

   ![Redundans för redundansgruppen](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Granska vilken som är den primära och vilken instans som är den sekundära. Om redundansen lyckas bör de två instanserna ha växlade roller.

   ![Hanterade instanser har växlat roller efter redundans](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Gå till den nya _sekundära hanterade_ instansen och välj **Redundans** igen för att växla tillbaka den primära instansen till den primära rollen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testa redundans för redundansgruppen med Hjälp av PowerShell.

   ```powershell-interactive
   $primaryResourceGroupName = "<Primary-Resource-Group>"
   $secondaryResourceGroupName = "<Secondary-Resource-Group>"
   $failoverGroupName = "<Failover-Group-Name>"
   $primaryLocation = "<Primary-Region>"
   $secondaryLocation = "<Secondary-Region>"
   $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
   $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Failover the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName

   # Fail primary managed instance back to primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"

   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
       -Location $secondaryLocation -Name $failoverGroupName
   ```

---

## <a name="use-private-link"></a>Använda Private Link

Med en privat länk kan du associera en logisk server med en specifik privat IP-adress i det virtuella nätverket och undernätet. 

Om du vill använda en privat länk med redundansgruppen gör du följande:

1. Se till att dina primära och sekundära servrar finns i [en länkad region.](../../best-practices-availability-paired-regions.md) 
1. Skapa det virtuella nätverket och undernätet i varje region som värd för privata slutpunkter för primära och sekundära servrar så att de inte har överlappande IP-adressutrymmen. Adressintervallet för det primära virtuella nätverket 10.0.0.0/16 och adressintervallet för det sekundära virtuella nätverket 10.0.0.1/16 överlappar. Mer information om adressintervall för virtuella nätverk finns i [bloggdesignen för virtuella Azure-nätverk.](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/)
1. Skapa en [privat slutpunkt och Azure Privat DNS zon för den primära servern](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint). 
1. Skapa även en privat slutpunkt för den sekundära servern, men den här gången väljer du att återanvända samma Privat DNS som skapades för den primära servern. 
1. När den privata länken har upprättats kan du skapa redundansgruppen genom att följa stegen som beskrevs tidigare i den här artikeln. 


## <a name="locate-listener-endpoint"></a>Hitta lyssnarslutpunkt

När redundansgruppen har konfigurerats uppdaterar du anslutningssträngen för ditt program till lyssnarslutpunkten. Det gör att programmet är anslutet till redundansgruppens lyssnare i stället för den primära databasen, den elastiska poolen eller instansdatabasen. På så sätt behöver du inte uppdatera anslutningssträngen manuellt varje gång din databasentitet går över och trafiken dirigeras till den entitet som för närvarande är primär.

Lyssnarslutpunkten är i form av och visas i Azure Portal `fog-name.database.windows.net` när du visar redundansgruppen:

![Anslutningssträng för redundansgrupp](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Kommentarer

- Om du tar bort en redundansgrupp för en enkel databas eller en pooldatabas stoppas inte replikeringen och den replikerade databasen tas inte bort. Du måste stoppa geo-replikeringen manuellt och ta bort databasen från den sekundära servern om du vill lägga till en enkel databas eller en pooldatabas tillbaka till en redundansgrupp när den har tagits bort. Om du inte gör något av dem kan det resultera i ett fel som liknar `The operation cannot be performed due to multiple errors` det när du försöker lägga till databasen i redundansgruppen.

## <a name="next-steps"></a>Nästa steg

Detaljerade anvisningar om hur du konfigurerar en redundansgrupp finns i följande självstudier:

- [Lägga till en enkel databas i en redundansgrupp](failover-group-add-single-database-tutorial.md)
- [Lägga till en elastisk pool till en redundansgrupp](failover-group-add-elastic-pool-tutorial.md)
- [Lägga till en hanterad instans i en redundansgrupp](../managed-instance/failover-group-add-instance-tutorial.md)

En översikt över alternativ Azure SQL Database hög tillgänglighet finns i [avsnittet om geo-replikering](active-geo-replication-overview.md) och [automatiska redundansgrupper.](auto-failover-group-overview.md)
