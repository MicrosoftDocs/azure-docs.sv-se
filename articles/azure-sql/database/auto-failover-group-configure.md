---
title: Konfigurera en failover-grupp
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Lär dig hur du konfigurerar en grupp för automatisk redundans för en Azure SQL Database (både enkel och pool) och SQL-hanterad instans med hjälp av Azure Portal, Azure CLI och PowerShell.
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 08/14/2019
ms.openlocfilehash: 09bb7cb2344e3e708a64842916e6e483136da3bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94594291"
---
# <a name="configure-a-failover-group-for-azure-sql-database"></a>Konfigurera en failover-grupp för Azure SQL Database
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

I det här avsnittet lär du dig hur du konfigurerar en [grupp för automatisk redundans](auto-failover-group-overview.md) för Azure SQL Database och Azure SQL-hanterad instans.

## <a name="single-database"></a>Enskild databas

Skapa gruppen redundans och Lägg till en enda databas i den med hjälp av Azure Portal eller PowerShell.

### <a name="prerequisites"></a>Förutsättningar

Överväg följande krav:

- Server inloggnings-och brand Väggs inställningarna för den sekundära servern måste matcha den primära serverns.

### <a name="create-failover-group"></a>Skapa redundans grupp

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa din grupp för redundans och Lägg till din enda databas i den med hjälp av Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan Azure SQL i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster.
1. Välj den databas som du vill lägga till i gruppen redundans.
1. Välj namnet på servern under **Server namn** för att öppna inställningarna för servern.

   ![Öppna Server för enskild databas](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan **Lägg till grupp** för att skapa en ny grupp för redundans.

   ![Lägg till ny redundans grupp](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. På sidan **redundans** anger eller väljer du de värden som krävs och väljer sedan **skapa**.

   - **Databaser i gruppen**: Välj den databas som du vill lägga till i gruppen för redundans. Om du lägger till databasen i gruppen för växling vid fel startas automatiskt processen för geo-replikering.

   ![Lägg till SQL Database i gruppen redundans](./media/auto-failover-group-configure/add-sqldb-to-failover-group.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa din grupp för redundans och Lägg till din databas i den med hjälp av PowerShell.

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
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
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

Testa redundans för din failover-grupp med hjälp av Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för din failover-grupp med hjälp av Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan "Azure SQL" i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster.
1. Välj den databas som du vill lägga till i gruppen redundans.

   ![Öppna Server för enskild databas](./media/auto-failover-group-configure/open-sql-db-server.png)

1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan den grupp för växling vid fel som du nyss skapade.
  
   ![Välj gruppen redundans från portalen](./media/auto-failover-group-configure/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär.
1. Välj **redundans** från åtgärds fönstret för att redundansväxla den redundansväxling som innehåller databasen.
1. Välj **Ja** i varningen som meddelar dig att TDS-sessioner kommer att kopplas bort.

   ![Redundansväxla din redundanskonfiguration som innehåller din databas](./media/auto-failover-group-configure/failover-sql-db.png)

1. Granska den server som nu är primär och vilken server som är sekundär. Om redundansväxlingen lyckades måste de två servrarna ha växlade roller.
1. Välj **redundans** igen för att återställa servrarna till deras ursprungliga roller.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testa redundans för din failover-grupp med hjälp av PowerShell.  

Kontrol lera den sekundära replikens roll:

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

Redundansväxla till den sekundära servern:

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

Återställ redundansväxlingen tillbaka till den primära servern:

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
> Om du behöver ta bort den sekundära databasen tar du bort den från gruppen växling vid fel innan du tar bort den. Om du tar bort en sekundär databas innan den tas bort från redundans gruppen kan det orsaka oförutsägbart beteende.

## <a name="elastic-pool"></a>Elastisk pool

Skapa gruppen redundans och Lägg till en elastisk pool i den med hjälp av Azure Portal eller PowerShell.  

### <a name="prerequisites"></a>Förutsättningar

Överväg följande krav:

- Server inloggnings-och brand Väggs inställningarna för den sekundära servern måste matcha den primära serverns.

### <a name="create-the-failover-group"></a>Skapa gruppen redundans

Skapa gruppen redundans för den elastiska poolen med hjälp av Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa din failover-grupp och Lägg till den elastiska poolen i den med hjälp av Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan "Azure SQL" i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster.
1. Välj den elastiska pool som du vill lägga till i gruppen redundans.
1. I **översikts** fönstret väljer du namnet på servern under **Server namn** för att öppna inställningarna för servern.
  
   ![Öppna Server för elastisk pool](./media/auto-failover-group-configure/server-for-elastic-pool.png)

1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan **Lägg till grupp** för att skapa en ny grupp för redundans.

   ![Lägg till ny redundans grupp](./media/auto-failover-group-configure/sqldb-add-new-failover-group.png)

1. På sidan **redundans** anger eller väljer du de värden som krävs och väljer sedan **skapa**. Skapa antingen en ny sekundär server eller Välj en befintlig sekundär server.

1. Välj **databaser i gruppen** och välj sedan den elastiska pool som du vill lägga till i gruppen redundans. Om det inte redan finns en elastisk pool på den sekundära servern visas en varning om att du måste skapa en elastisk pool på den sekundära servern. Välj varningen och välj sedan **OK** för att skapa den elastiska poolen på den sekundära servern.

   ![Lägg till elastisk pool i redundans gruppen](./media/auto-failover-group-configure/add-elastic-pool-to-failover-group.png)

1. Välj **Välj** om du vill tillämpa inställningarna för elastisk pool på gruppen växling vid fel och välj sedan **skapa** för att skapa din grupp för redundans. Om du lägger till den elastiska poolen i gruppen redundans startas geo-replikeringen automatiskt.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa din failover-grupp och Lägg till den elastiska poolen i den med hjälp av PowerShell.

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
   Write-host "Databases added to failover group successfully."
  ```

---

### <a name="test-failover"></a>Redundanstest

Testa redundansväxlingen av den elastiska poolen med hjälp av Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Redundansväxla din redundansväxling till den sekundära servern och växla sedan tillbaka med Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan "Azure SQL" i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster.
1. Välj den elastiska pool som du vill lägga till i gruppen redundans.
1. I **översikts** fönstret väljer du namnet på servern under **Server namn** för att öppna inställningarna för servern.

   ![Öppna Server för elastisk pool](./media/auto-failover-group-configure/server-for-elastic-pool.png)
1. Välj **grupper för växling vid fel** i fönstret **Inställningar** och välj sedan den grupp för växling vid fel som du skapade i avsnitt 2.
  
   ![Välj gruppen redundans från portalen](./media/auto-failover-group-configure/select-failover-group.png)

1. Granska vilken server som är primär och vilken server som är sekundär.
1. Välj **redundans** från åtgärds fönstret för att Redundansväxla din redundansväxling som innehåller den elastiska poolen.
1. Välj **Ja** i varningen som meddelar dig att TDS-sessioner kommer att kopplas bort.

   ![Redundansväxla din redundanskonfiguration som innehåller din databas](./media/auto-failover-group-configure/failover-sql-db.png)

1. Granska vilken server som är primär, vilken server som är sekundär. Om redundansväxlingen lyckades måste de två servrarna ha växlade roller.
1. Välj **redundans** igen om du vill att redundans-gruppen ska växla tillbaka till de ursprungliga inställningarna.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testa redundans för din failover-grupp med hjälp av PowerShell.

Kontrol lera den sekundära replikens roll:

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

Redundansväxla till den sekundära servern:

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
> Om du behöver ta bort den sekundära databasen tar du bort den från gruppen växling vid fel innan du tar bort den. Om du tar bort en sekundär databas innan den tas bort från redundans gruppen kan det orsaka oförutsägbart beteende.

## <a name="sql-managed-instance"></a>SQL-hanterad instans

Skapa en grupp för redundans mellan två hanterade instanser i Azure SQL-hanterad instans med hjälp av Azure Portal eller PowerShell.

Du måste antingen konfigurera [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) eller skapa en gateway för det virtuella nätverket för varje SQL-hanterad instans, ansluta de två gatewayerna och sedan skapa gruppen redundans. 

Distribuera båda hanterade instanser till [kopplade regioner](../../best-practices-availability-paired-regions.md) av prestanda skäl. Hanterade instanser i regioner med geo-par har mycket bättre prestanda jämfört med ej kopplade regioner. 

### <a name="prerequisites"></a>Förutsättningar

Överväg följande krav:

- Den sekundära hanterade instansen måste vara tom.
- Under nätets intervall för det sekundära virtuella nätverket får inte överlappa under näts intervallet för det primära virtuella nätverket.
- Sorteringen och tids zonen för den sekundära hanterade instansen måste matcha den primära hanterade instansen.
- När du ansluter de två gatewayerna ska den **delade nyckeln** vara samma för båda anslutningarna.

### <a name="create-primary-virtual-network-gateway"></a>Skapa primär virtuell nätverksgateway

Om du inte har konfigurerat [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md)kan du skapa den primära virtuella nätverksgatewayen med Azure Portal eller PowerShell.

> [!NOTE]
> SKU: n för gatewayen påverkar data flödes prestanda. I den här artikeln distribueras en gateway med de mest grundläggande SKU: er ( `HwGw1` ). Distribuera en högre SKU (exempel: `VpnGw3` ) för att uppnå högre genomflöde. För alla tillgängliga alternativ, se [Gateway-SKU: er](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) 

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa den primära virtuella Nätverksgatewayen med hjälp av Azure Portal.

1. I [Azure Portal](https://portal.azure.com)går du till din resurs grupp och väljer den **virtuella nätverks** resursen för din primära hanterade instans.
1. Välj **undernät** under **Inställningar** och välj sedan för att lägga till ett nytt **Gateway-undernät**. Låt standardvärdena vara kvar.

   ![Lägg till gateway för primär hanterad instans](./media/auto-failover-group-configure/add-subnet-gateway-primary-vnet.png)

1. När du har skapat en gateway för undernät väljer du **skapa en resurs** i det vänstra navigerings fönstret och skriver sedan `Virtual network gateway` i sökrutan. Välj den **virtuella nätverks-Gateway** -resurs som publicerats av **Microsoft**.

   ![Skapa en ny virtuell nätverksgateway](./media/auto-failover-group-configure/create-virtual-network-gateway.png)

1. Fyll i de obligatoriska fälten om du vill konfigurera gatewayen för den primära hanterade instansen.

   I följande tabell visas de värden som krävs för gatewayen för den primära hanterade instansen:

    | **Fält** | Värde |
    | --- | --- |
    | **Prenumeration** |  Den prenumeration där din primära hanterade instans är. |
    | **Namn** | Namnet på den virtuella Nätverksgatewayen. |
    | **Region** | Den region där din primära hanterade instans är. |
    | **Typ av Gateway** | Välj **VPN**. |
    | **VPN-typ** | Välj **Route-baserad** |
    | **SKU**| Lämna standardvärdet `VpnGw1` . |
    | **Plats**| Den plats där den sekundära hanterade instansen och det sekundära virtuella nätverket är.   |
    | **Virtuellt nätverk**| Välj det virtuella nätverket för din sekundära hanterade instans. |
    | **Offentlig IP-adress**| Välj **Skapa ny**. |
    | **Namn på offentlig IP-adress**| Ange ett namn för din IP-adress. |
    | &nbsp; | &nbsp; |

1. Lämna de andra värdena som standard och välj sedan **Granska + skapa** för att granska inställningarna för din virtuella nätverksgateway.

   ![Inställningar för primär Gateway](./media/auto-failover-group-configure/settings-for-primary-gateway.png)

1. Välj **skapa** för att skapa din nya virtuella nätverksgateway.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa den primära virtuella Nätverksgatewayen med PowerShell.

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

Skapa den sekundära virtuella Nätverksgatewayen med hjälp av Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Upprepa stegen i föregående avsnitt för att skapa det virtuella nätverkets undernät och gateway för den sekundära hanterade instansen. Fyll i de obligatoriska fälten om du vill konfigurera gatewayen för din sekundära hanterade instans.

I följande tabell visas de värden som krävs för gatewayen för den sekundära hanterade instansen:

   | **Fält** | Värde |
   | --- | --- |
   | **Prenumeration** |  Prenumerationen där den sekundära hanterade instansen är. |
   | **Namn** | Namnet på din virtuella nätverksgateway, till exempel `secondary-mi-gateway` . |
   | **Region** | Den region där den sekundära hanterade instansen är. |
   | **Typ av Gateway** | Välj **VPN**. |
   | **VPN-typ** | Välj **Route-baserad** |
   | **SKU**| Lämna standardvärdet `VpnGw1` . |
   | **Plats**| Den plats där den sekundära hanterade instansen och det sekundära virtuella nätverket är.   |
   | **Virtuellt nätverk**| Välj det virtuella nätverk som skapades i avsnitt 2, till exempel `vnet-sql-mi-secondary` . |
   | **Offentlig IP-adress**| Välj **Skapa ny**. |
   | **Namn på offentlig IP-adress**| Ange ett namn för din IP-adress, till exempel `secondary-gateway-IP` . |
   | &nbsp; | &nbsp; |

   ![Inställningar för sekundär Gateway](./media/auto-failover-group-configure/settings-for-secondary-gateway.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa den sekundära virtuella Nätverksgatewayen med PowerShell.

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

### <a name="connect-the-gateways"></a>Anslut gatewayerna

Skapa anslutningar mellan de två gatewayerna med hjälp av Azure Portal eller PowerShell.

Två anslutningar måste skapas – anslutningen från den primära gatewayen till den sekundära gatewayen och anslutningen från den sekundära gatewayen till den primära gatewayen.

Den delade nyckel som används för båda anslutningarna ska vara densamma för varje anslutning.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa anslutningar mellan de två gatewayerna med hjälp av Azure Portal.

1. Välj **skapa en resurs** från [Azure Portal](https://portal.azure.com).
1. Skriv `connection` i sökrutan och tryck sedan på RETUR för att söka, som tar dig till **anslutnings** resursen som publicerats av Microsoft.
1. Välj **skapa** för att skapa din anslutning.
1. På fliken **grundläggande** inställningar väljer du följande värden och väljer sedan **OK**.
    1. Välj `VNet-to-VNet` för **anslutnings typen**.
    1. Välj din prenumeration från listrutan.
    1. Välj resurs grupp för din hanterade instans i list rutan.
    1. Välj platsen för din primära hanterade instans i list rutan.
1. På fliken **Inställningar** väljer eller anger du följande värden och väljer sedan **OK**:
    1. Välj den primära Nätverksgatewayen för den **första virtuella Nätverksgatewayen**, till exempel `Primary-Gateway` .  
    1. Välj den sekundära Nätverksgatewayen för den **andra virtuella Nätverksgatewayen**, till exempel `Secondary-Gateway` .
    1. Markera kryss rutan bredvid **upprätta dubbelriktad anslutning**.
    1. Lämna antingen standard namnet för primär anslutning eller Byt namn på det till önskat värde.
    1. Ange en **delad nyckel (PSK)** för anslutningen, till exempel `mi1m2psk` .

   ![Skapa Gateway-anslutning](./media/auto-failover-group-configure/create-gateway-connection.png)

1. På fliken **Sammanfattning** granskar du inställningarna för din dubbelriktade anslutning och väljer sedan **OK** för att skapa anslutningen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa anslutningar mellan de två gatewayerna med PowerShell.

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

### <a name="create-the-failover-group"></a>Skapa gruppen redundans

Skapa gruppen redundans för dina hanterade instanser med hjälp av Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Skapa gruppen redundans för dina SQL-hanterade instanser med hjälp av Azure Portal.

1. Välj **Azure SQL** i den vänstra menyn i [Azure Portal](https://portal.azure.com). Om **Azure SQL** inte finns i listan väljer du **alla tjänster** och skriver sedan Azure SQL i sökrutan. Valfritt Välj stjärnan bredvid **Azure SQL** för att Favorita den och lägga till den som ett objekt i navigeringen till vänster.
1. Välj den primära hanterade instans som du vill lägga till i gruppen redundans.  
1. Under **Inställningar** navigerar du till **instans grupper för redundans** och väljer sedan att **lägga till grupp** för att öppna sidan **instans redundans grupp** .

   ![Lägg till en grupp för redundans](./media/auto-failover-group-configure/add-failover-group.png)

1. På sidan **instans redundans grupp** anger du namnet på din redundans grupp och väljer sedan den sekundära hanterade instansen i list rutan. Välj **skapa** för att skapa din grupp för redundans.

   ![Skapa redundans grupp](./media/auto-failover-group-configure/create-failover-group.png)

1. När redundansväxlingen är klar kommer du tillbaka till sidan för **redundans** .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Skapa gruppen redundans för dina hanterade instanser med PowerShell.

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

Testa redundans för din failover-grupp med hjälp av Azure Portal eller PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Testa redundans för din failover-grupp med hjälp av Azure Portal.

1. Navigera till den _sekundära_ hanterade instansen i [Azure Portal](https://portal.azure.com) och välj **instans grupper för redundans** under Inställningar.
1. Granska vilken hanterad instans som är primär och vilken hanterad instans som är sekundär.
1. Välj **redundans** och välj sedan **Ja** på varningen om TDS-sessioner som kopplas från.

   ![Redundansväxla failover-gruppen](./media/auto-failover-group-configure/failover-mi-failover-group.png)

1. Granska vilken hanterade-instans som är primär och vilken instans som är sekundär. Om redundansväxlingen lyckades måste de två instanserna ha växlade roller.

   ![Hanterade instanser har växlat roller efter redundans](./media/auto-failover-group-configure/mi-switched-after-failover.png)

1. Gå till den nya _sekundära_ hanterade instansen och välj **redundans** igen så att den primära instansen inte kan återställas till den primära rollen.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Testa redundans för din failover-grupp med hjälp av PowerShell.

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

Med hjälp av en privat länk kan du associera en logisk server med en speciell privat IP-adress inom det virtuella nätverket och under nätet. 

Gör så här om du vill använda en privat länk med din failover-grupp:

1. Se till att dina primära och sekundära servrar finns i en [kopplad region](../../best-practices-availability-paired-regions.md). 
1. Skapa det virtuella nätverket och under nätet i varje region som värd för privata slut punkter för primära och sekundära servrar, så att de har icke-överlappande IP-adressutrymme. Till exempel är det primära virtuella nätverks adress intervallet 10.0.0.0/16 och det sekundära virtuella nätverks adress intervallet 10.0.0.1/16 överlappar. Mer information om adress intervall för virtuella nätverk finns i bloggen [utforma virtuella Azure-nätverk](https://devblogs.microsoft.com/premier-developer/understanding-cidr-notation-when-designing-azure-virtual-networks-and-subnets/).
1. Skapa en [privat slut punkt och Azure privat DNS zon för den primära servern](../../private-link/create-private-endpoint-portal.md#create-a-private-endpoint). 
1. Skapa även en privat slut punkt för den sekundära servern, men den här gången väljer du att återanvända samma Privat DNS zon som skapades för den primära servern. 
1. När den privata länken har upprättats kan du skapa en failover-grupp enligt stegen som beskrivs tidigare i den här artikeln. 


## <a name="locate-listener-endpoint"></a>Hitta lyssnar slut punkt

När du har konfigurerat din failover-grupp uppdaterar du anslutnings strängen för ditt program till lyssnar slut punkten. Detta gör att ditt program är anslutet till avlyssnings gruppens lyssnare i stället för den primära databasen, den elastiska poolen eller instans databasen. På så sätt behöver du inte uppdatera anslutnings strängen manuellt varje gång din databas enhet växlar över, och trafik dirigeras till den enhet som är primär för närvarande.

Lyssnar slut punkten är i form av `fog-name.database.windows.net` och visas i Azure Portal när du visar gruppen redundans:

![Anslutnings sträng för redundans grupp](./media/auto-failover-group-configure/find-failover-group-connection-string.png)

## <a name="remarks"></a>Kommentarer

- Att ta bort en redundans grupp för en databas med en eller flera databaser stoppar inte replikeringen och tar inte bort den replikerade databasen. Du måste stoppa geo-replikering manuellt och ta bort databasen från den sekundära servern om du vill lägga till en enskild eller fristående databas tillbaka till en redundansväxling när den har tagits bort. Om du inte gör det kan det resultera i ett fel som liknar `The operation cannot be performed due to multiple errors` när du försöker lägga till databasen i gruppen för växling vid fel.

## <a name="next-steps"></a>Nästa steg

Detaljerade anvisningar om hur du konfigurerar en grupp för växling vid fel finns i följande Självstudier:

- [Lägga till en enskild databas i en failover-grupp](failover-group-add-single-database-tutorial.md)
- [Lägga till en elastisk pool till en redundansgrupp](failover-group-add-elastic-pool-tutorial.md)
- [Lägga till en hanterad instans i en grupp för redundans](../managed-instance/failover-group-add-instance-tutorial.md)

En översikt över Azure SQL Database alternativ för hög tillgänglighet finns i grupper för [geo-replikering](active-geo-replication-overview.md) och [Automatisk redundans](auto-failover-group-overview.md).