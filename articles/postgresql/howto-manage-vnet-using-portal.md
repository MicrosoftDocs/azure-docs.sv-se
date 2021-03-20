---
title: Använda regler för virtuella nätverk – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Skapa och hantera VNet-tjänstens slut punkter och regler Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 377f8eba179253ca6187b10a22970d0eadcda2f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489837"
---
# <a name="create-and-manage-vnet-service-endpoints-and-vnet-rules-in-azure-database-for-postgresql---single-server-by-using-the-azure-portal"></a>Skapa och hantera VNet-tjänstens slut punkter och VNet-regler i Azure Database for PostgreSQL-enskild server med hjälp av Azure Portal
Virtual Network (VNet) tjänsternas slut punkter och regler utökar det privata adress utrymmet för en Virtual Network till Azure Database for PostgreSQL-servern. En översikt över Azure Database for PostgreSQL VNet-tjänstens slut punkter, inklusive begränsningar, finns i [Azure Database for PostgreSQL serverns VNet-slutpunkter](concepts-data-access-and-security-vnet.md). VNet-tjänstens slut punkter är tillgängliga i alla regioner som stöds för Azure Database for PostgreSQL.

> [!NOTE]
> Stöd för VNet-tjänstens slut punkter är bara för Generell användning och minnesoptimerade servrar.
> Om det gäller VNet-peering, om trafik flödar genom en gemensam VNet-Gateway med tjänst slut punkter och ska flöda till motparten, skapar du en ACL/VNet-regel för att tillåta Azure Virtual Machines i gatewayens VNet att komma åt Azure Database for PostgreSQL-servern.


## <a name="create-a-vnet-rule-and-enable-service-endpoints-in-the-azure-portal"></a>Skapa en VNet-regel och aktivera tjänstens slut punkter i Azure Portal

1. På sidan PostgreSQL-Server går du till rubriken inställningar och klickar på **anslutnings säkerhet** för att öppna fönstret anslutnings säkerhet för Azure Database for PostgreSQL. 

2. Se till att alternativet Tillåt åtkomst till Azure-tjänster är inställt på **av**.

> [!Important]
> Om du lämnar kontrollen inställd på på, accepterar din Azure PostgreSQL-databasserver kommunikation från vilket undernät som helst. Om du lämnar kontrollen till på aktive rad kan det vara för hög åtkomst från en säkerhets punkt. Den Microsoft Azure Virtual Network tjänstens slut punkts funktion, i samordning med funktionen för regel för virtuella nätverk i Azure Database for PostgreSQL, kan minska området för säkerhets ytan.

3. Klicka sedan på **+ Lägg till befintligt virtuellt nätverk**. Om du inte har ett befintligt VNet kan du klicka på **+ skapa ett nytt virtuellt nätverk** för att skapa ett. Se [snabb start: skapa ett virtuellt nätverk med hjälp av Azure Portal](../virtual-network/quick-create-portal.md)

   :::image type="content" source="./media/howto-manage-vnet-using-portal/1-connection-security.png" alt-text="Azure Portal på anslutnings säkerhet":::

4. Ange ett VNet-regel namn, Välj prenumerationen, det virtuella nätverket och under nätets namn och klicka sedan på **Aktivera**. Detta aktiverar automatiskt VNet-tjänstens slut punkter i under nätet med hjälp av service tag-koden för **Microsoft. SQL** .

   :::image type="content" source="./media/howto-manage-vnet-using-portal/2-configure-vnet.png" alt-text="Azure Portal-konfigurera VNet":::

    Kontot måste ha nödvändiga behörigheter för att skapa ett virtuellt nätverk och tjänstslutpunkten.

    Tjänst slut punkter kan konfigureras på virtuella nätverk oberoende av en användare med Skriv behörighet till det virtuella nätverket.
    
    För att skydda Azure-tjänstens resurser till ett VNet måste användaren ha behörighet till "Microsoft. Network/virtualNetworks/subnets/joinViaServiceEndpoint/" för de undernät som läggs till. Den här behörigheten ingår som standard i de inbyggda tjänstadministratörsrollerna och kan ändras genom att skapa anpassade roller.
    
    Lär dig mer om [inbyggda roller](../role-based-access-control/built-in-roles.md) och att tilldela specifika behörigheter till [anpassade roller](../role-based-access-control/custom-roles.md).
    
    VNet och Azure-tjänstresurser kan finnas i samma eller olika prenumerationer. Om VNet-och Azure-tjänstens resurser finns i olika prenumerationer bör resurserna vara under samma Active Directory-klient (AD). Se till att båda prenumerationerna har **Microsoft. SQL** -Resurshanterarens registrerad. Mer information hittar du i [Resource Manager-Registration][resource-manager-portal]

   > [!IMPORTANT]
   > Vi rekommenderar starkt att läsa den här artikeln om konfiguration och överväganden för tjänst slut punkter innan du konfigurerar tjänst slut punkter. **Virtual Network tjänst slut punkt:** En [Virtual Network tjänst slut punkt](../virtual-network/virtual-network-service-endpoints-overview.md) är ett undernät vars egenskaps värden innehåller ett eller flera formella namn för Azure-tjänst typ. I VNet Services-slutpunkter används tjänst typs namnet **Microsoft. SQL**, som refererar till Azure-tjänsten med namnet SQL Database. Den här tjänst tag gen gäller även för Azure SQL Database-, Azure Database for PostgreSQL-och MySQL-tjänster. Det är viktigt att du noterar när du använder service tag-koden för **Microsoft. SQL** på en slut punkt för VNet-tjänsten konfigurerar tjänst slut punkts trafik för alla Azure Database-tjänster, inklusive Azure SQL Database, Azure Database for PostgreSQL och Azure Database for MySQL servrar i under nätet. 
   > 

5. När den är aktive rad klickar du på **OK** så visas att VNet-tjänstens slut punkter aktive ras tillsammans med en VNet-regel.

   :::image type="content" source="./media/howto-manage-vnet-using-portal/3-vnet-service-endpoints-enabled-vnet-rule-created.png" alt-text="Virtuella nätverks slut punkter som är aktiverade och VNet-regel har skapats":::

## <a name="next-steps"></a>Nästa steg
- På samma sätt kan du skript för att [Aktivera VNet-tjänstens slut punkter och skapa en VNet-regel för Azure Database for PostgreSQL med Azure CLI](howto-manage-vnet-using-cli.md).
- Information om hur du ansluter till en Azure Database for PostgreSQL-Server finns i [anslutnings bibliotek för Azure Database for PostgreSQL](./concepts-connection-libraries.md)

<!-- Link references, to text, Within this same GitHub repo. --> 
[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md