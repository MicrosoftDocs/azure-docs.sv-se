---
title: Hantera VNet-slutpunkter – Azure CLI – Azure Database for MySQL
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MySQL VNet-tjänstslutpunkter och regler med hjälp av Azure CLI-kommandoraden.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 0bc686efbd07cf39d7932b175b6f9800b1ff185f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774673"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Skapa och hantera Azure Database for MySQL VNet-tjänstslutpunkter med Hjälp av Azure CLI
VNet-tjänstslutpunkter och regler utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure Database for MySQL-server. Med hjälp av praktiska Azure CLI-kommandon (Command Line Interface) kan du skapa, uppdatera, ta bort, lista och visa VNet-tjänstslutpunkter och regler för att hantera servern. En översikt över hur Azure Database for MySQL VNet-tjänstslutpunkter, inklusive begränsningar, finns i [Azure Database for MySQL Server VNet-tjänstslutpunkter](concepts-data-access-and-security-vnet.md). VNet-tjänstslutpunkter är tillgängliga i alla regioner som stöds för Azure Database for MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Du behöver en [Azure Database for MySQL-server och databas](quickstart-create-mysql-server-database-using-azure-cli.md).
 
- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

> [!NOTE]
> Stöd för VNet-tjänstslutpunkter gäller endast för Generell användning och minnesoptimerade servrar.
> Om trafik flödar genom en gemensam VNet Gateway med tjänstslutpunkter och ska flöda till peer-servern, skapar du en ACL/VNet-regel för att tillåta att Azure Virtual Machines i det virtuella gatewaynätverket får åtkomst till Azure Database for MySQL-servern.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurera VNet-tjänstslutpunkter för Azure Database for MySQL
Kommandona [az network vnet](/cli/azure/network/vnet) används för att konfigurera virtuella nätverk.

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az_account_set). Ersätt **id-egenskapen** från **az login-utdata** för din prenumeration i platshållaren för prenumerations-ID.

- Kontot måste ha nödvändiga behörigheter för att skapa ett virtuellt nätverk och tjänstslutpunkten.

Tjänstslutpunkter kan konfigureras i virtuella nätverk oberoende av en användare med skrivåtkomst till det virtuella nätverket.

För att skydda Azure-tjänstresurser i ett VNet måste användaren ha behörighet till "Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md) och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md).

VNet och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet- och Azure-tjänstresurserna finns i olika prenumerationer ska resurserna finnas under samma Active Directory-klientorganisation (AD). Kontrollera att båda prenumerationerna har **resursprovidern Microsoft.Sql** registrerad. Mer information finns i [resource-manager-registration][resource-manager-portal]

> [!IMPORTANT]
> Vi rekommenderar starkt att du läser den här artikeln om konfigurationer och överväganden för tjänstslutpunkter innan du kör exempelskriptet nedan eller konfigurerar tjänstslutpunkter. **Virtual Network tjänstslutpunkt:** En [Virtual Network tjänstslutpunkt är](../virtual-network/virtual-network-service-endpoints-overview.md) ett undernät vars egenskapsvärden innehåller ett eller flera formella namn på Azure-tjänsttyper. VNet-tjänstslutpunkter använder tjänsttypen **Microsoft.Sql**, som refererar till Azure-tjänsten med namnet SQL Database. Den här tjänsttaggen gäller även för Azure SQL Database, Azure Database for PostgreSQL och MySQL. Det är viktigt att observera när du tillämpar **Microsoft.Sql-tjänsttaggen** på en VNet-tjänstslutpunkt konfigureras tjänstslutpunktstrafik för alla Azure Database-tjänster, inklusive Azure SQL Database-, Azure Database for PostgreSQL- och Azure Database for MySQL-servrar i undernätet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exempelskript för att skapa en Azure Database for MySQL-databas, skapa en VNet-, VNet-tjänstslutpunkt och skydda servern till undernätet med en VNet-regel
I det här exempelskriptet ändrar du markerade rader om du vill anpassa administratörens användarnamn och lösenord. Ersätt SubscriptionID som används i kommandot `az account set --subscription` med din egen prenumerations-ID.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
