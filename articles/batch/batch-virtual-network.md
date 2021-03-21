---
title: Etablera en pool i ett virtuellt nätverk
description: Så här skapar du en batch-pool i ett virtuellt Azure-nätverk så att Compute-noder kan kommunicera säkert med andra virtuella datorer i nätverket, till exempel en fil server.
ms.topic: how-to
ms.date: 03/15/2021
ms.custom: seodec18
ms.openlocfilehash: d6e5de75164e098fc95f6c086d9f98a652dcee4a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103561925"
---
# <a name="create-an-azure-batch-pool-in-a-virtual-network"></a>Skapa en Azure Batch pool i ett virtuellt nätverk

När du skapar en Azure Batch pool kan du etablera poolen i ett undernät för ett [virtuellt Azure-nätverk](../virtual-network/virtual-networks-overview.md) (VNet) som du anger. Den här artikeln förklarar hur du konfigurerar en batch-pool i ett VNet.

## <a name="why-use-a-vnet"></a>Varför ska jag använda ett VNet?

Compute-noder i en pool kan kommunicera med varandra, till exempel för att köra aktiviteter med flera instanser, utan att ett separat VNet krävs. Noder i en pool kan dock inte kommunicera med virtuella datorer utanför poolen, till exempel licens servrar eller fil servrar, som standard.

Om du vill tillåta att Compute-noder kommunicerar säkert med andra virtuella datorer, eller med ett lokalt nätverk, kan du etablera poolen i ett undernät för ett Azure VNet.

## <a name="prerequisites"></a>Förutsättningar

- **Autentisering**. Om du vill använda ett Azure VNet-nätverk måste Batch-klientens API använda Azure Active Directory-autentisering (AD). Mer dokumentation om stödet för Azure Batch i Azure Active Directory finns i [Authenticate Batch service solutions with Active Directory](batch-aad-auth.md) (Autentisera lösningar för Batch-tjänsten med Active Directory).

- **Ett Azure VNet**. Se följande avsnitt för krav och konfiguration för VNet. För att förbereda ett VNet med ett eller flera undernät i förväg, kan du använda Azure Portal, Azure PowerShell, gränssnittet i Azure Command-Line-gränssnittet (CLI) eller andra metoder.
  - Information om hur du skapar ett Azure Resource Manager-baserat VNet finns i [skapa ett virtuellt nätverk](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Ett Resource Manager-baserat VNet rekommenderas för nya distributioner och stöds endast på pooler som använder konfiguration av virtuell dator.
  - Om du vill skapa ett klassiskt VNet, se [skapa ett virtuellt nätverk (klassiskt) med flera undernät](/previous-versions/azure/virtual-network/create-virtual-network-classic). Ett klassiskt VNet stöds bara på pooler som använder Cloud Services konfiguration.

## <a name="vnet-requirements"></a>VNet-krav

[!INCLUDE [batch-virtual-network-ports](../../includes/batch-virtual-network-ports.md)]

## <a name="create-a-pool-with-a-vnet-in-the-azure-portal"></a>Skapa en pool med ett VNet i Azure Portal

När du har skapat ditt VNet och tilldelat ett undernät, kan du skapa en batch-pool med det virtuella nätverket. Följ de här stegen för att skapa en pool från Azure Portal: 

1. Navigera till ditt Batch-konto i Azure Portal. Det här kontot måste finnas i samma prenumeration och region som den resurs grupp som innehåller det virtuella nätverk som du vill använda.
2. I fönstret **Inställningar** till vänster väljer du meny alternativet **pooler** .
3. I fönstret **pooler** väljer du **Lägg till**.
4. I fönstret **Lägg till pool** väljer du det alternativ som du vill använda från List rutan **Bildtyp** .
5. Välj rätt **utgivare/erbjudande/SKU** för din anpassade avbildning.
6. Ange de återstående nödvändiga inställningarna, inklusive **nodens storlek**, **dedikerade noder** och **noder med låg prioritet** samt önskade valfria inställningar.
7. I **Virtual Network** väljer du det virtuella nätverk och undernät som du vill använda.

   ![Lägg till poolen med virtuellt nätverk](./media/batch-virtual-network/add-vnet-pool.png)

## <a name="user-defined-routes-for-forced-tunneling"></a>Användardefinierade vägar för Tvingad tunnel trafik

Du kan ha krav i din organisation för att omdirigera (tvinga) Internet-baserad trafik från undernät tillbaka till din lokala plats för inspektion och loggning. Dessutom kan du ha aktiverat Tvingad tunnel trafik för undernät i ditt VNet.

För att säkerställa att noderna i poolen fungerar i ett VNet som har Tvingad tunnel trafik aktive rad måste du lägga till följande [användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md) (UDR) för under nätet:

- Batch-tjänsten måste kommunicera med noder för schemaläggning av aktiviteter. Om du vill aktivera den här kommunikationen lägger du till en UDR för varje IP-adress som används av batch-tjänsten i den region där ditt batch-konto finns. Om du vill hämta en lista över IP-adresser för batch-tjänsten, se [tjänst Taggar lokalt](../virtual-network/service-tags-overview.md).

- Se till att utgående trafik till Azure Storage (särskilt URL-adresser i formuläret `<account>.table.core.windows.net` , `<account>.queue.core.windows.net` och `<account>.blob.core.windows.net` ) inte blockeras av ditt lokala nätverk.

När du lägger till en UDR definierar du vägen för varje relaterat batch-IP-adressprefix och anger **nästa hopp typ** till **Internet**.

![Användardefinierad väg](./media/batch-virtual-network/user-defined-route.png)

> [!WARNING]
> IP-adresser för batch-tjänsten kan ändras med tiden. För att förhindra avbrott på grund av en ändring av IP-adress, skapar du en process för att uppdatera batch-tjänstens IP-adresser automatiskt och hålla dem uppdaterade i routningstabellen.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [batch-tjänstens arbets flöde och primära resurser](batch-service-workflow-features.md) som pooler, noder, jobb och aktiviteter.
- Lär dig hur du [skapar en användardefinierad väg i Azure Portal](../virtual-network/tutorial-create-route-table-portal.md).
