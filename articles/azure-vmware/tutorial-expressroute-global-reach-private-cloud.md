---
title: Självstudie – lokala peer-miljöer i ett privat moln
description: Lär dig hur du skapar ExpressRoute Global Reach peering till ett privat moln i en Azure VMware-lösning.
ms.topic: tutorial
ms.date: 03/17/2021
ms.openlocfilehash: ae92bf89a08c5fade8757e3ee596c4ed4a5e6389
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103494193"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Självstudie: peer-lokala miljöer till ett privat moln

ExpressRoute Global Reach ansluter din lokala miljö till ditt privata moln i Azure VMware-lösningen. ExpressRoute Global Reach-anslutningen upprättas mellan det privata molnet ExpressRoute-kretsen och en befintlig ExpressRoute-anslutning till dina lokala miljöer. 

ExpressRoute-kretsen som du använder när du [konfigurerar nätverk för ditt privata VMware-moln i Azure](tutorial-configure-networking.md) kräver att du skapar och använder nycklar för autentisering.  Du har redan använt en auktoriseringspost från ExpressRoute-kretsen och i den här självstudien skapar du en andra auktoriseringskod till peer med din lokala ExpressRoute-krets.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en andra auktoriseringsregel för _krets 2_, den privata molnets ExpressRoute-krets.
> * Använd antingen Azure Portal eller Azure CLI i en Cloud Shell-metod i prenumerationen på _krets 1_ för att aktivera lokal-till-privat-moln ExpressRoute Global Reach-peering.


## <a name="before-you-begin"></a>Innan du börjar

Innan du aktiverar anslutning mellan två ExpressRoute-kretsar med ExpressRoute Global Reach bör du läsa dokumentationen om hur du [aktiverar anslutningar i olika Azure-prenumerationer](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  

## <a name="prerequisites"></a>Förutsättningar

- Upprättad anslutning till och från ett privat moln i Azure VMware-lösningen med sin ExpressRoute-krets med en ExpressRoute-gateway i ett virtuellt Azure-nätverk (VNet), som är krets 2 från peering-procedurer.
- En separat, fungerande ExpressRoute-krets som används för att ansluta lokala miljöer till Azure, som är krets 1 från peering-procedurens perspektiv.
- Ett/29 icke-överlappande [nätverks adress block](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) för ExpressRoute Global Reach-peering.
- Se till att alla gatewayer, inklusive ExpressRoute-leverantörens tjänst, har stöd för ett autonomt system nummer (ASN) med 4 byte. Azure VMware-lösningen använder 4 bytes offentliga ASN: er för annonserings vägar.

>[!IMPORTANT]
>I samband med dessa förutsättningar är din lokala ExpressRoute-krets _krets 1_, och din privata moln ExpressRoute-krets är i en annan prenumeration och har märkt _krets 2_.

## <a name="create-an-expressroute-authorization-key-in-the-on-premises-circuit"></a>Skapa en ExpressRoute-auktoriseringsregel i den lokala kretsen

[!INCLUDE [request-authorization-key](includes/request-authorization-key.md)]
 
## <a name="peer-private-cloud-to-on-premises-with-authorization-key"></a>Peer-privat moln till lokal med nyckel för autentisering
Nu när du har skapat en auktoriseringsregel för ExpressRoute-kretsen för det privata molnet kan du koppla den till din lokala ExpressRoute-krets. Peer koppling görs från den lokala ExpressRoute-kretsens perspektiv i antingen **Azure Portal** eller med hjälp av **Azure CLI i en Cloud Shell**. Med båda metoderna använder du resurs-ID och verifierings nyckel för din privata moln ExpressRoute-krets för att slutföra peer kopplingen.

### <a name="portal"></a>[Portal](#tab/azure-portal)
 
1. Logga in på [Azure Portal](https://portal.azure.com) med samma prenumeration som den lokala ExpressRoute-kretsen.

1. I **översikten över** det privata molnet under hantera väljer du **anslutnings**  >  **ExpressRoute Global Reach**  >  **Lägg till**.

    :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Skärm bild som visar ExpressRoute-fliken Global Reach i det privata molnet Azure VMware-lösning.":::

1. Skapa en lokal moln anslutning. Gör något av följande och välj sedan **skapa**:

   - Välj **ExpressRoute-kretsen** i listan eller
   - Om du har krets-ID: t klistrar du in det i fältet och anger auktoriseringsregeln.

   :::image type="content" source="./media/expressroute-global-reach/on-premises-cloud-connections.png" alt-text="Ange ExpressRoute-ID och verifierings nyckel och välj sedan skapa.":::   
   
   Den nya anslutningen visas i listan lokal moln anslutning.

>[!TIP]
>Du kan ta bort eller koppla från en anslutning från listan genom att välja **mer**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Koppla från eller ta bort en lokal anslutning":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Vi har förstärkt CLI- [kommandona](../expressroute/expressroute-howto-set-global-reach-cli.md) med detaljerad information och exempel som hjälper dig att konfigurera ExpressRoute-Global Reach peering mellan lokala miljöer till ett privat moln i Azure VMware-lösningen.

>[!TIP]
>För det kortfattat i Azure CLI-kommandot utdata kan de här instruktionerna använda ett [ `–query` argument](https://docs.microsoft.com/cli/azure/query-azure-cli) för att köra en JMESPath-fråga för att endast visa de nödvändiga resultaten.

1. Logga in på [Azure Portal](https://portal.azure.com) med samma prenumeration som den lokala ExpressRoute-kretsen. 

1. Öppna en Cloud Shell och lämna gränssnittet som bash.

   :::image type="content" source="media/expressroute-global-reach/azure-portal-cloud-shell.png" alt-text="Skärm bild som visar Azure Portal Cloud Shell.":::

1. Skapa peering mot krets 1, och skicka i krets 2 resurs-ID och auktoriseringskod. 

   ```azurecli-interactive
   az network express-route peering connection create -g <ResourceGroupName> --circuit-name <Circuit1Name> --peering-name AzurePrivatePeering -n <ConnectionName> --peer-circuit <Circuit2ResourceID> --address-prefix <__.__.__.__/29> --authorization-key <authorizationKey>
   ```

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Skärm bild som visar kommandot och resultatet av en lyckad peering mellan krets 1 och krets 2.":::

Du kan ansluta från lokala miljöer till ditt privata moln via ExpressRoute Global Reach-peering.

>[!TIP]
>Du kan ta bort peer koppling genom att följa anvisningarna [för att inaktivera anslutningar mellan dina lokala nätverk](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


---

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar det lokala molnets ExpressRoute Global Reach peering. 

Fortsätt till nästa självstudie och lär dig hur du distribuerar och konfigurerar VMware HCX-lösningen för ditt privata moln i Azure VMware-lösningen.

> [!div class="nextstepaction"]
> [Distribuera och konfigurera VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->
