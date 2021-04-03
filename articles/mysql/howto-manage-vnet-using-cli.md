---
title: Hantera VNet-slutpunkter – Azure CLI – Azure Database for MySQL
description: Den här artikeln beskriver hur du skapar och hanterar Azure Database for MySQL VNet-tjänstens slut punkter och regler med hjälp av kommando raden i Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.devlang: azurecli
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 237b9bc4d7ac6366a67accb31fdf3c80c778b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94636766"
---
# <a name="create-and-manage-azure-database-for-mysql-vnet-service-endpoints-using-azure-cli"></a>Skapa och hantera Azure Database for MySQL VNet-tjänstens slut punkter med Azure CLI
VNet-tjänstslutpunkter och regler utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure Database for MySQL-server. Med hjälp av ett bekvämt kommando rads gränssnitt (CLI) för kommando tolken kan du skapa, uppdatera, ta bort, lista och Visa VNet-tjänstens slut punkter och regler för att hantera servern. En översikt över Azure Database for MySQL VNet-tjänstens slut punkter, inklusive begränsningar, finns i [Azure Database for MySQL serverns VNet-slutpunkter](concepts-data-access-and-security-vnet.md). VNet-tjänstens slut punkter är tillgängliga i alla regioner som stöds för Azure Database for MySQL.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Du behöver en [Azure Database for MySQL-server och-databas](quickstart-create-mysql-server-database-using-azure-cli.md).
 
- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

> [!NOTE]
> Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.
> Om det gäller VNet-peering, om trafik flödar genom en gemensam VNet-Gateway med tjänst slut punkter och ska flöda till motparten, skapar du en ACL/VNet-regel för att tillåta Azure Virtual Machines i gatewayens VNet att komma åt Azure Database for MySQL-servern.

## <a name="configure-vnet-service-endpoints-for-azure-database-for-mysql"></a>Konfigurera VNet-tjänstens slut punkter för Azure Database for MySQL
[AZ Network VNet](/cli/azure/network/vnet) -kommandon används för att konfigurera virtuella nätverk.

Om du har flera prenumerationer ska du välja lämplig prenumeration där resursen ska debiteras. Välj det specifika prenumerations-ID:t under ditt konto med hjälp av kommandot [az account set](/cli/azure/account#az-account-set). Ersätt **ID-** egenskapen från **AZ inloggnings** resultat för din prenumeration i plats hållaren för prenumerations-ID.

- Kontot måste ha nödvändiga behörigheter för att skapa ett virtuellt nätverk och tjänstslutpunkten.

Tjänst slut punkter kan konfigureras på virtuella nätverk oberoende av en användare med Skriv behörighet till det virtuella nätverket.

För att skydda Azure-tjänstens resurser till ett VNet måste användaren ha behörighet till "Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.

Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md) och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md).

VNet och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet-och Azure-tjänstens resurser finns i olika prenumerationer bör resurserna vara under samma Active Directory-klient (AD). Se till att båda prenumerationerna har **Microsoft. SQL** -Resurshanterarens registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

> [!IMPORTANT]
> Vi rekommenderar starkt att läsa den här artikeln om konfiguration och överväganden för tjänst slut punkter innan du kör exempel skriptet nedan eller konfigurerar tjänst slut punkter. **Virtual Network tjänst slut punkt:** En [Virtual Network tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typ. I VNet Services-slutpunkter används tjänst typs namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database. Den här tjänst tag gen gäller även för Azure SQL Database-, Azure Database for PostgreSQL-och MySQL-tjänster. Det är viktigt att du noterar när du använder service tag-koden för **Microsoft. SQL** på en slut punkt för VNet-tjänsten konfigurerar tjänst slut punkts trafik för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL och Azure Database for MySQL servrar i under nätet. 
> 

### <a name="sample-script-to-create-an-azure-database-for-mysql-database-create-a-vnet-vnet-service-endpoint-and-secure-the-server-to-the-subnet-with-a-vnet-rule"></a>Exempel skript om du vill skapa en Azure Database for MySQL databas skapar du ett VNet, VNet-slutpunkt och skyddar servern till under nätet med en VNet-regel
I det här exempelskriptet ändrar du markerade rader om du vill anpassa administratörens användarnamn och lösenord. Ersätt SubscriptionID som används i `az account set --subscription` kommandot med ditt eget prenumerations-ID.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/create-mysql-server.sh?highlight=5,20 "Create an Azure Database for MySQL, VNet, VNet service endpoint, and VNet rule.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.
[!code-azurecli-interactive[main](../../cli_scripts/mysql/create-mysql-server-vnet/delete-mysql.sh "Delete the resource group.")]

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md
