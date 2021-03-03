---
title: Anslutnings inställningar för Azure SQL Database och Azure Synapse Analytics
description: I den här artikeln förklaras alternativen för Transport Layer Security (TLS) och proxyn jämfört med omdirigerings inställningar för Azure SQL Database och Azure Synapse Analytics.
services: sql-database
ms.service: sql-database
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.topic: how-to
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 07/06/2020
ms.openlocfilehash: 84fdca96f2ce42c608e7def98f6a3400964cfe46
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101691934"
---
# <a name="azure-sql-connectivity-settings"></a>Inställningar för Azure SQL-anslutning
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

Den här artikeln beskriver inställningar som styr anslutningen till servern för Azure SQL Database och Azure Synapse Analytics. Dessa inställningar gäller för alla SQL Database-och Azure Synapse Analytics-databaser som är associerade med servern.

> [!IMPORTANT]
> Den här artikeln gäller inte för Azure SQL-hanterade instanser.

Anslutnings inställningarna är tillgängliga från skärmen **brand väggar och virtuella nätverk** , vilket visas på följande skärm bild:

 ![Skärm bild av fönstret anslutnings inställningar.][1]

> [!NOTE]
> De här inställningarna börjar gälla omedelbart efter att de har tillämpats. Dina kunder kan uppleva anslutnings avbrott om de inte uppfyller kraven för varje inställning.

## <a name="deny-public-network-access"></a>Neka åtkomst till offentligt nätverk

Om **nekad åtkomst till offentligt nätverk** är inställt på **Ja**, tillåts bara anslutningar via privata slut punkter. När den här inställningen är **Nej** (standard) kan kunder ansluta genom att använda antingen offentliga slut punkter (med IP-baserade brand Väggs regler eller med virtuella brand Väggs regler) eller privata slut punkter (med hjälp av Azures privata länk), enligt beskrivningen i [Översikt över nätverks åtkomst](network-access-controls-overview.md).

 ![Diagram som visar anslutningen när nekat offentligt nätverks åtkomst är inställt på Ja och när neka offentlig nätverks åtkomst har angetts till Nej.][2]

Försök att ställa in **neka offentlig nätverks åtkomst** till **Ja** utan att befintliga privata slut punkter på den logiska servern kommer att Miss lyckas med ett fel meddelande som liknar:  

```output
Error 42102
Unable to set Deny Public Network Access to Yes since there is no private endpoint enabled to access the server.
Please set up private endpoints and retry the operation.
```

> [!NOTE]
> Ange **neka offentlig nätverks åtkomst** till **Nej** om du vill definiera brand Väggs regler för virtuella nätverk på en logisk server som redan har kon figurer ATS med privata slut punkter.

Om **nekad åtkomst till offentligt nätverk** är inställt på **Ja**, tillåts bara anslutningar via privata slut punkter. Alla anslutningar via offentliga slut punkter kommer att nekas med ett fel meddelande som liknar:  

```output
Error 47073
An instance-specific error occurred while establishing a connection to SQL Server. 
The public network interface on this server is not accessible. 
To connect to this server, use the Private Endpoint from inside your virtual network.
```

Om **nekad åtkomst till offentliga nätverk** är inställt på **Ja** nekas försök att lägga till eller uppdatera brand Väggs regler med ett fel meddelande som liknar följande:

```output
Error 42101
Unable to create or modify firewall rules when public network interface for the server is disabled. 
To manage server or database level firewall rules, please enable the public network interface.
```

## <a name="change-public-network-access-via-powershell"></a>Ändra offentlig nätverks åtkomst via PowerShell

> [!IMPORTANT]
> Azure SQL Database stöder fortfarande PowerShell Azure Resource Manager-modulen, men all framtida utveckling är för AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du `Get` och `Set` den **offentliga nätverks åtkomst** egenskapen på server nivå:

```powershell
# Get the Public Network Access property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).PublicNetworkAccess

# Update Public Network Access to Disabled
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString -PublicNetworkAccess "Disabled"
```

## <a name="change-public-network-access-via-cli"></a>Ändra offentlig nätverks åtkomst via CLI

> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-gränssnitt

Följande CLI-skript visar hur du ändrar inställningen för **offentlig nätverks åtkomst** i ett bash-gränssnitt:

```azurecli-interactive

# Get current setting for Public Network Access
az sql server show -n sql-server-name -g sql-server-group --query "publicNetworkAccess"

# Update setting for Public Network Access
az sql server update -n sql-server-name -g sql-server-group --set publicNetworkAccess="Disabled"
```

## <a name="minimal-tls-version"></a>Lägsta TLS-version 

Inställningen minimalt [Transport Layer Security (TLS)](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) ger kunderna möjlighet att välja vilken version av TLS deras SQL-databas använder.

För närvarande finns stöd för TLS 1.0, 1.1 och 1.2. Genom att ange en lägsta TLS-version kan du vara säker på att senare TLS-versioner stöds. Om du till exempel väljer en TLS-version 1,1 betyder det att endast anslutningar med TLS 1,1 och 1,2 godkänns, och anslutningar med TLS 1,0 avvisas. När du har testat och bekräftat att dina program stöder den, rekommenderar vi att du väljer 1.2 som den lägsta TLS-versionen. Den här versionen har korrigeringar som åtgärdar sårbarheter i tidigare versioner och är den högsta TLS-versionen som stöds i Azure SQL Database.

> [!IMPORTANT]
> Standardvärdet för den lägsta TLS-versionen är att tillåta alla versioner. När du har tillämpat en version av TLS går det inte att återgå till standardvärdet.

Kunder med program som förlitar sig på äldre versioner av TLS rekommenderas att ställa in den lägsta TLS-versionen baserat på de aktuella programkraven. Kunder som förlitar sig på program för att ansluta via en okrypterad anslutning avråds från att ställa in en lägsta TLS-version.

Mer information finns i [TLS-överväganden för SQL Database anslutning](connect-query-content-reference-guide.md#tls-considerations-for-database-connectivity).

När du har angett den lägsta TLS-versionen kommer inloggnings försöken från kunder som använder en TLS-version som är lägre än den lägsta TLS-versionen av servern att Miss lyckas med följande fel:

```output
Error 47072
Login failed with invalid TLS version
```

## <a name="set-the-minimal-tls-version-via-powershell"></a>Ange den lägsta TLS-versionen via PowerShell

> [!IMPORTANT]
> Azure SQL Database stöder fortfarande PowerShell Azure Resource Manager-modulen, men all framtida utveckling är för AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du `Get` och `Set` egenskapen **minimal TLS-version** på den logiska Server nivån:

```powershell
# Get the Minimal TLS Version property
(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).MinimalTlsVersion

# Update Minimal TLS Version to 1.2
$SecureString = ConvertTo-SecureString "password" -AsPlainText -Force

Set-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group -SqlAdministratorPassword $SecureString  -MinimalTlsVersion "1.2"
```

## <a name="set-the-minimal-tls-version-via-the-azure-cli"></a>Ange den lägsta TLS-versionen via Azure CLI

> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-gränssnitt

Följande CLI-skript visar hur du ändrar inställningen för **minimal TLS-version** i ett bash-gränssnitt:

```azurecli-interactive
# Get current setting for Minimal TLS Version
az sql server show -n sql-server-name -g sql-server-group --query "minimalTlsVersion"

# Update setting for Minimal TLS Version
az sql server update -n sql-server-name -g sql-server-group --set minimalTlsVersion="1.2"
```

## <a name="change-the-connection-policy"></a>Ändra anslutnings principen

[Anslutnings principen](connectivity-architecture.md#connection-policy) avgör hur kunderna ansluter till Azure SQL Database.

## <a name="change-the-connection-policy-via-powershell"></a>Ändra anslutnings principen via PowerShell

> [!IMPORTANT]
> Azure SQL Database stöder fortfarande PowerShell Azure Resource Manager-modulen, men all framtida utveckling är för AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska. Följande skript kräver Azure PowerShell- [modulen](/powershell/azure/install-az-ps).

Följande PowerShell-skript visar hur du ändrar anslutnings principen med hjälp av PowerShell:

```powershell
# Get SQL Server ID
$sqlserverid=(Get-AzSqlServer -ServerName sql-server-name -ResourceGroupName sql-server-group).ResourceId

# Set URI
$id="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
(Get-AzResource -ResourceId $id -ApiVersion 2014-04-01 -Verbose).Properties.ConnectionType

# Update connection policy
Set-AzResource -ResourceId $id -Properties @{"connectionType" = "Proxy"} -f
```

## <a name="change-the-connection-policy-via-the-azure-cli"></a>Ändra anslutnings principen via Azure CLI

> [!IMPORTANT]
> Alla skript i det här avsnittet kräver [Azure CLI](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>Azure CLI i ett bash-gränssnitt

Följande CLI-skript visar hur du ändrar anslutnings principen i ett bash-gränssnitt:

```azurecli-interactive
# Get SQL Server ID
sqlserverid=$(az sql server show -n sql-server-name -g sql-server-group --query 'id' -o tsv)

# Set URI
ids="$sqlserverid/connectionPolicies/Default"

# Get current connection policy
az resource show --ids $ids

# Update connection policy
az resource update --ids $ids --set properties.connectionType=Proxy
```

### <a name="azure-cli-from-a-windows-command-prompt"></a>Azure CLI från en kommando tolk i Windows

Följande CLI-skript visar hur du ändrar anslutnings principen från en kommando tolk i Windows (med Azure CLI installerat):

```azurecli
# Get SQL Server ID and set URI
FOR /F "tokens=*" %g IN ('az sql server show --resource-group myResourceGroup-571418053 --name server-538465606 --query "id" -o tsv') do (SET sqlserverid=%g/connectionPolicies/Default)

# Get current connection policy
az resource show --ids %sqlserverid%

# Update connection policy
az resource update --ids %sqlserverid% --set properties.connectionType=Proxy
```

## <a name="next-steps"></a>Nästa steg

- En översikt över hur anslutningen fungerar i Azure SQL Database finns i [anslutnings arkitektur](connectivity-architecture.md).
- Information om hur du ändrar anslutnings principen för en server finns i avsnittet om att ansluta [-princip](/cli/azure/sql/server/conn-policy).

<!--Image references-->
[1]: media/single-database-create-quickstart/manage-connectivity-settings.png
[2]: media/single-database-create-quickstart/manage-connectivity-flowchart.png
