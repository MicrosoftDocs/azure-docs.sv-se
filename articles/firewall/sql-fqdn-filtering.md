---
title: Konfigurera Azure Firewall-programregler med fullständiga domännamn för SQL
description: I den här artikeln får du lära dig hur du konfigurerar SQL-FQDN i regler för Azure brand Väggs program.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: c65f32cc3ce56ddf3fd235de8c002528e7a3cebd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791450"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Konfigurera Azure Firewall-programregler med fullständiga domännamn för SQL

Nu kan du konfigurera regler för Azure brand Väggs program med SQL-FQDN. På så sätt kan du begränsa åtkomsten från dina virtuella nätverk till endast de angivna SQL Server-instanserna.

Med SQL-FQDN kan du filtrera trafik:

- Från din virtuella nätverk till en Azure SQL Database-eller Azure Synapse-analys. Till exempel: Tillåt endast åtkomst till *SQL-server1.Database.Windows.net*.
- Från lokalt till Azure SQL-hanterade instanser eller SQL-IaaS som körs i din virtuella nätverk.
- Från ekrar till ekrar till Azure SQL-hanterade instanser eller SQL-IaaS som körs i din virtuella nätverk.

SQL-FQDN-filtrering stöds endast i [proxy-läge](../azure-sql/database/connectivity-architecture.md#connection-policy) (port 1433). Om du använder SQL i standard läget för omdirigering kan du filtrera åtkomst med SQL Service-taggen som en del av [nätverks reglerna](features.md#network-traffic-filtering-rules).
Om du använder portar som inte är standard för SQL IaaS-trafik kan du konfigurera portarna i brandväggsreglerna.

## <a name="configure-using-azure-cli"></a>Konfigurera med Azure CLI

1. Distribuera en [Azure-brandvägg med Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, Azure Synapse Analytics eller SQL-hanterad instans, ser du till att SQL Connectivity-läget är inställt på **proxy**. Information om hur du växlar läge för SQL-anslutning finns i [Inställningar för Azure SQL-anslutning](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > SQL- *proxyläge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, som är standardvärdet för klienter som ansluter i Azure, kan du filtrera åtkomst med SQL [service-taggen](service-tags.md) i brand Väggs [nätverks regler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Skapa en ny regel samling med en program regel med hjälp av SQL FQDN för att ge åtkomst till en SQL Server:

   ```azurecli
    az extension add -n azure-firewall
    
    az network firewall application-rule create \ 
    -g FWRG \
    --f azfirewall \ 
    --c sqlRuleCollection \
    --priority 1000 \
    --action Allow \
    --name sqlRule \
    --protocols mssql=1433 \
    --source-addresses 10.0.0.0/24 \
    --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-azure-powershell"></a>Konfigurera med Azure PowerShell

1. Distribuera en [Azure-brandvägg med Azure PowerShell](deploy-ps.md).
2. Om du filtrerar trafik till Azure SQL Database, Azure Synapse Analytics eller SQL-hanterad instans, ser du till att SQL Connectivity-läget är inställt på **proxy**. Information om hur du växlar läge för SQL-anslutning finns i [Inställningar för Azure SQL-anslutning](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).

   > [!NOTE]
   > SQL- *proxyläge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, som är standardvärdet för klienter som ansluter i Azure, kan du filtrera åtkomst med SQL [service-taggen](service-tags.md) i brand Väggs [nätverks regler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Skapa en ny regel samling med en program regel med hjälp av SQL FQDN för att ge åtkomst till en SQL Server:

   ```azurepowershell
   $AzFw = Get-AzFirewall -Name "azfirewall" -ResourceGroupName "FWRG"
    
   $sqlRule = @{
      Name          = "sqlRule"
      Protocol      = "mssql:1433" 
      TargetFqdn    = "sql-serv1.database.windows.net"
      SourceAddress = "10.0.0.0/24"
   }
    
   $rule = New-AzFirewallApplicationRule @sqlRule
    
   $sqlRuleCollection = @{
      Name       = "sqlRuleCollection" 
      Priority   = 1000 
      Rule       = $rule
      ActionType = "Allow"
   }
    
   $ruleCollection = New-AzFirewallApplicationRuleCollection @sqlRuleCollection
    
   $Azfw.ApplicationRuleCollections.Add($ruleCollection)    
   Set-AzFirewall -AzureFirewall $AzFw    
   ```

## <a name="configure-using-the-azure-portal"></a>Konfigurera med hjälp av Azure-portalen
1. Distribuera en [Azure-brandvägg med Azure CLI](deploy-cli.md).
2. Om du filtrerar trafik till Azure SQL Database, Azure Synapse Analytics eller SQL-hanterad instans, ser du till att SQL Connectivity-läget är inställt på **proxy**. Information om hur du växlar läge för SQL-anslutning finns i [Inställningar för Azure SQL-anslutning](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli).  

   > [!NOTE]
   > SQL- *proxyläge* kan resultera i mer latens jämfört med *omdirigering*. Om du vill fortsätta använda omdirigeringsläge, som är standardvärdet för klienter som ansluter i Azure, kan du filtrera åtkomst med SQL [service-taggen](service-tags.md) i brand Väggs [nätverks regler](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Lägg till program regeln med rätt protokoll, port och SQL FQDN och välj sedan **Spara**.
   ![program regel med SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Få åtkomst till SQL från en virtuell dator i ett VNet som filtrerar trafiken genom brand väggen. 
5. Verifiera att [Azure Firewall-loggar](./firewall-workbook.md) visar att trafiken är tillåten.

## <a name="next-steps"></a>Nästa steg

Information om SQL-proxy och omdirigerings lägen finns [Azure SQL Database anslutnings arkitektur](../azure-sql/database/connectivity-architecture.md).