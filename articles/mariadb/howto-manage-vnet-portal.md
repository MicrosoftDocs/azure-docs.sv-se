---
title: Hantera VNet-slutpunkter – Azure Portal-Azure Database for MariaDB
description: Skapa och hantera Azure Database for MariaDB VNet-tjänstens slut punkter och regler med hjälp av Azure Portal
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 5eaa7821c61010b322d8f9032c439df28c297f3d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665029"
---
# <a name="create-and-manage-azure-database-for-mariadb-vnet-service-endpoints-and-vnet-rules-by-using-the-azure-portal"></a>Skapa och hantera Azure Database for MariaDB VNet-tjänstens slut punkter och VNet-regler med hjälp av Azure Portal

VNet-tjänstslutpunkter och regler utökar det privata adressutrymmet för ett virtuellt nätverk till din Azure Database for MariaDB-server. En översikt över Azure Database for MariaDB VNet-tjänstens slut punkter, inklusive begränsningar, finns i [Azure Database for MariaDB serverns VNet-slutpunkter](concepts-data-access-security-vnet.md). VNet-tjänstens slut punkter är tillgängliga i alla regioner som stöds för Azure Database for MariaDB.

> [!NOTE]
> Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.

## <a name="create-a-vnet-rule-and-enable-service-endpoints"></a>Skapa en VNet-regel och aktivera tjänstens slut punkter

1. På sidan MariaDB-Server går du till rubriken inställningar och klickar på **anslutnings säkerhet** för att öppna fönstret anslutnings säkerhet för Azure Database for MariaDB.

2. Se till att alternativet Tillåt åtkomst till Azure-tjänster är inställt på **av**.

> [!Important]
> Om du ställer in det på på, godkänner din Azure MariaDB-databas servern kommunikation från ett undernät. Om du lämnar kontrollen till på aktive rad kan det vara för hög åtkomst från en säkerhets punkt. Den Microsoft Azure Virtual Network tjänstens slut punkts funktion, i samordning med funktionen för regel för virtuella nätverk i Azure Database for MariaDB, kan minska området för säkerhets ytan.

3. Klicka sedan på **+ Lägg till befintligt virtuellt nätverk**. Om du inte har ett befintligt VNet kan du klicka på **+ skapa ett nytt virtuellt nätverk** för att skapa ett. Se [snabb start: skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md)

   ![Azure Portal på anslutnings säkerhet](./media/howto-manage-vnet-portal/1-connection-security.png)

4. Ange ett VNet-regel namn, Välj prenumerationen, det virtuella nätverket och under nätets namn och klicka sedan på **Aktivera**. Detta aktiverar automatiskt VNet-tjänstens slut punkter i under nätet med hjälp av service tag-koden för **Microsoft. SQL** .

   ![Azure Portal-konfigurera VNet](./media/howto-manage-vnet-portal/2-configure-vnet.png)

   Kontot måste ha nödvändiga behörigheter för att skapa ett virtuellt nätverk och tjänstslutpunkten.

   Tjänst slut punkter kan konfigureras på virtuella nätverk oberoende av en användare med Skriv behörighet till det virtuella nätverket.
    
   För att skydda Azure-tjänstens resurser till ett VNet måste användaren ha behörighet till "Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.
    
   Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md) och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md).
    
   VNet och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet-och Azure-tjänstens resurser finns i olika prenumerationer bör resurserna vara under samma Active Directory-klient (AD). Se till att båda prenumerationerna har **Microsoft. SQL** -Resurshanterarens registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

   > [!IMPORTANT]
   > Vi rekommenderar starkt att läsa den här artikeln om konfiguration och överväganden för tjänst slut punkter innan du konfigurerar tjänst slut punkter. **Virtual Network tjänst slut punkt:** En [Virtual Network tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typ. I VNet Services-slutpunkter används tjänst typs namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database. Den här tjänst tag gen gäller även för Azure SQL Database-, Azure Database for MariaDB-, PostgreSQL-och MySQL-tjänster. Det är viktigt att du noterar när du använder service tag-taggen **Microsoft. SQL** för en VNet-tjänst slut punkt som konfigurerar tjänst slut punkts trafik för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL, Azure Database for MariaDB och Azure Database for MySQL servrar i under nätet.
   > 

5. När den är aktive rad klickar du på **OK** så visas att VNet-tjänstens slut punkter aktive ras tillsammans med en VNet-regel.

   ![Virtuella nätverks slut punkter som är aktiverade och VNet-regel har skapats](./media/howto-manage-vnet-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png)

## <a name="next-steps"></a>Nästa steg
- Läs mer om hur du [konfigurerar SSL på Azure Database for MariaDB](howto-configure-ssl.md)
- På samma sätt kan du skript för att [Aktivera VNet-tjänstens slut punkter och skapa en VNet-regel för Azure Database for MariaDB med Azure CLI](howto-manage-vnet-cli.md).

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md