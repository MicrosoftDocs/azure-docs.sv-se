---
title: Hantera virtuella nätverk – Azure Portal-Azure Database for PostgreSQL – flexibel Server
description: Skapa och hantera virtuella nätverk för Azure Database for PostgreSQL flexibel server med hjälp av Azure Portal
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: how-to
ms.date: 09/22/2020
ms.openlocfilehash: d5b60cfeb3bfce1253ece3708e6b48ce6bcb0b4f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226913"
---
# <a name="create-and-manage-virtual-networks-for-azure-database-for-postgresql---flexible-server-using-the-azure-portal"></a>Skapa och hantera virtuella nätverk för Azure Database for PostgreSQL flexibel server med hjälp av Azure Portal

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Azure Database for PostgreSQL-flexibel Server stöder två typer av ömsesidigt exklusiva nätverks anslutnings metoder för att ansluta till din flexibla Server. De två alternativen är:

* Offentlig åtkomst (tillåtna IP-adresser)
* Privat åtkomst (VNet-integrering)

I den här artikeln fokuserar vi på att skapa en PostgreSQL-server med **privat åtkomst (VNet-integrering)** med hjälp av Azure Portal. Med privat åtkomst (VNet-integrering) kan du distribuera din flexibla server till din egen [Azure-Virtual Network](../../virtual-network/virtual-networks-overview.md). Virtuella Azure-nätverk tillhandahåller privat och säker nätverkskommunikation. Med privat åtkomst är anslutningarna till PostgreSQL-servern begränsade till ditt virtuella nätverk. Mer information om det finns i [privat åtkomst (VNet-integrering)](./concepts-networking.md#private-access-vnet-integration).

Du kan distribuera din flexibla server till ett virtuellt nätverk och undernät när servern skapas. När den flexibla servern har distribuerats kan du inte flytta den till ett annat virtuellt nätverk, undernät eller till *Offentlig åtkomst (tillåtna IP-adresser)* .

## <a name="prerequisites"></a>Förutsättningar
Om du vill skapa en flexibel server i ett virtuellt nätverk behöver du:
- Ett [virtuellt nätverk](../../virtual-network/quick-create-portal.md#create-a-virtual-network)
    > [!Note]
    > Det virtuella nätverket och under nätet måste finnas i samma region och prenumeration som din flexibla Server.

-  [Delegera ett undernät](../../virtual-network/manage-subnet-delegation.md#delegate-a-subnet-to-an-azure-service) till **Microsoft. DBforPostgreSQL/flexibleServers**. Den här delegeringen innebär att endast Azure Database for PostgreSQL flexibla servrar kan använda det under nätet. Inga andra Azure-resurstyper kan finnas i det delegerade undernätet.
-  Lägg till i `Microsoft.Storage` tjänst slut punkten för under nätet delegerad till flexibla servrar. Detta görs genom att utföra följande steg:
     1. Gå till sidan för ditt virtuella nätverk.
     2. Välj det VNET som du planerar att distribuera den flexibla servern i.
     3. Välj det undernät som är delegerat för flexibel Server.
     4. Välj från List rutan under **tjänst slut punkt** på hämtnings skärmen `Microsoft.storage` .
     5. Spara ändringarna.


## <a name="create-azure-database-for-postgresql---flexible-server-in-an-already-existing-virtual-network"></a>Skapa Azure Database for PostgreSQL-flexibel server i ett redan befintligt virtuellt nätverk

1. Välj **skapa en resurs** (+) i det övre vänstra hörnet i portalen.
2. Välj **databaser**  >  **Azure Database for PostgreSQL**. Du kan också ange **postgresql** i sökrutan för att hitta tjänsten.
3. Välj **flexibel Server** som distributions alternativ.
4. Fyll i formuläret **grundläggande** information.
5. Gå till fliken **nätverk** för att konfigurera hur du vill ansluta till servern.
6. I **anslutnings metoden** väljer du **privat åtkomst (VNet-integrering)**. Gå till **Virtual Network** och välj det redan befintliga *virtuella nätverket* och *under nätet* som skapats som en del av kraven ovan.
7. Välj **Granska + skapa** för att granska din flexibla Server konfiguration.
8. Välj **Skapa** för att etablera servern. Etableringen kan ta några minuter.

>[!Note]
> När den flexibla servern har distribuerats till ett virtuellt nätverk och undernät kan du inte flytta den till offentlig åtkomst (tillåtna IP-adresser).
## <a name="next-steps"></a>Nästa steg
- [Skapa och hantera Azure Database for PostgreSQL-flexibla Server nätverk med Azure CLI](./how-to-manage-virtual-network-cli.md).
- Lär dig mer om [nätverk i Azure Database for PostgreSQL-flexibel Server](./concepts-networking.md)
- Lär dig mer om det [virtuella nätverk som Azure Database for PostgreSQL – flexibelt Server](./concepts-networking.md#private-access-vnet-integration).