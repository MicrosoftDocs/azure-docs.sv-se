---
title: Hantera VNet-slutpunkter – Azure CLI – Azure Database for MariaDB
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MariaDB VNet-tjänstslutpunkter och regler med hjälp av Azure CLI-kommandoraden.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8eaf87865fb2fc70251e1e417361333cfd750d6e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783673"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-using-azure-cli"></a>Skapa och hantera Azure Database for MariaDB VNet-tjänstslutpunkter med Azure CLI

VNet-tjänstslutpunkter och regler utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure Database for MariaDB-server. Med hjälp av praktiska CLI-kommandon (Azure Command Line Interface) kan du skapa, uppdatera, ta bort, lista och visa VNet-tjänstslutpunkter och regler för att hantera servern. En översikt över Azure Database for MariaDB VNet-tjänstslutpunkter, inklusive begränsningar, finns [i Azure Database for MariaDB Server VNet-tjänstslutpunkter](concepts-data-access-security-vnet.md). VNet-tjänstslutpunkter är tillgängliga i alla regioner som stöds för Azure Database for MariaDB.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Du behöver en [Azure Database for MariaDB-server och databas](quickstart-create-mariadb-server-database-using-azure-cli.md).

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

> [!NOTE]
> Stöd för VNet-tjänstslutpunkter gäller endast för Generell användning och minnesoptimerade servrar.

## <a name="configure-vnet-service-endpoints"></a>Konfigurera VNet-tjänstslutpunkter

Kommandona [az network vnet](/cli/azure/network/vnet) används för att konfigurera virtuella nätverk.

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az_account_set). Ersätt egenskapen **ID** från **az login**-utdata för din prenumeration i platshållaren för prenumerations-ID.

- Kontot måste ha nödvändiga behörigheter för att skapa ett virtuellt nätverk och tjänstslutpunkten.

Tjänstslutpunkter kan konfigureras i virtuella nätverk oberoende av en användare med skrivåtkomst till det virtuella nätverket.

För att skydda Azure-tjänstresurser i ett virtuellt nätverk måste användaren ha behörighet till "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md) och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md).

VNet och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet- och Azure-tjänstresurserna finns i olika prenumerationer ska resurserna finnas under samma Active Directory-klientorganisation (AD). Kontrollera att båda prenumerationerna har **resursprovidern Microsoft.Sql** registrerad. Mer information finns i [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> Vi rekommenderar starkt att du läser den här artikeln om konfigurationer och överväganden för tjänstslutpunkter innan du konfigurerar tjänstslutpunkter. **Virtual Network tjänstslutpunkt:** En [Virtual Network tjänstslutpunkt är](../virtual-network/virtual-network-service-endpoints-overview.md) ett undernät vars egenskapsvärden innehåller ett eller flera formella namn på Azure-tjänsttyper. VNet-tjänstslutpunkter använder tjänsttypen **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL Database. Den här tjänsttaggen gäller även för Azure SQL Database-, Azure Database for MariaDB-, PostgreSQL- och MySQL-tjänster. Det är viktigt att observera när du använder **Microsoft.Sql-tjänsttaggen** på en VNet-tjänstslutpunkt konfigureras tjänstslutpunktstrafik för alla Azure Database-tjänster, inklusive Azure SQL Database-, Azure Database for PostgreSQL-, Azure Database for MariaDB- och Azure Database for MySQL-servrar i undernätet.

### <a name="sample-script"></a>Exempelskript

Det här exempelskriptet används för att skapa en Azure Database for MariaDB server, skapa ett VNet, en VNet-tjänstslutpunkt och skydda servern till undernätet med en VNet-regel. I det här exempelskriptet ändrar du administratörens användarnamn och lösenord. Ersätt SubscriptionID som används i kommandot `az account set --subscription` med din egen prenumerations-ID.

```azurecli-interactive
# To find the name of an Azure region in the CLI run this command: az account list-locations
# Substitute  <subscription id> with your identifier
az account set --subscription <subscription id>

# Create a resource group
az group create \
--name myresourcegroup \
--location westus

# Create a MariaDB server in the resource group
# Name of a server maps to DNS name and is thus required to be globally unique in Azure.
# Substitute the <server_admin_password> with your own value.
az mariadb server create \
--name mydemoserver \
--resource-group myresourcegroup \
--location westus \
--admin-user mylogin \
--admin-password <server_admin_password> \
--sku-name GP_Gen5_2

# Get available service endpoints for Azure region output is JSON
# Use the command below to get the list of services supported for endpoints, for an Azure region, say "westus".
az network vnet list-endpoint-services \
-l westus

# Add Azure SQL service endpoint to a subnet *mySubnet* while creating the virtual network *myVNet* output is JSON
az network vnet create \
-g myresourcegroup \
-n myVNet \
--address-prefixes 10.0.0.0/16 \
-l westus

# Creates the service endpoint
az network vnet subnet create \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet \
--address-prefix 10.0.1.0/24 \
--service-endpoints Microsoft.SQL

# View service endpoints configured on a subnet
az network vnet subnet show \
-g myresourcegroup \
-n mySubnet \
--vnet-name myVNet

# Create a VNet rule on the sever to secure it to the subnet Note: resource group (-g) parameter is where the database exists. VNet resource group if different should be specified using subnet id (URI) instead of subnet, VNet pair.
az mariadb server vnet-rule create \
-n myRule \
-g myresourcegroup \
-s mydemoserver \
--vnet-name myVNet \
--subnet mySubnet
```

<!-- 
In this sample script, change the highlighted lines to customize the admin username and password. Replace the SubscriptionID used in the `az account set --subscription` command with your own subscription identifier.
[!code-azurecli-interactive[main](../../cli_scripts/mariadb/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MariaDB, VNet, VNet service endpoint, and VNet rule.")]
-->

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name myresourcegroup
```


<!--
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]
-->

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md