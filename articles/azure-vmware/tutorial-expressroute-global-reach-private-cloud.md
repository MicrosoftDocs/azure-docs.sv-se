---
title: Självstudie – lokala peer-miljöer i ett privat moln
description: Lär dig hur du skapar ExpressRoute Global Reach peering till ett privat moln i en Azure VMware-lösning.
ms.topic: tutorial
ms.date: 09/21/2020
ms.openlocfilehash: cf209071074fd4546ce64336b2b3b91fbcd4cb3b
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901461"
---
# <a name="tutorial-peer-on-premises-environments-to-a-private-cloud"></a>Självstudie: peer-lokala miljöer till ett privat moln

ExpressRoute Global Reach ansluter din lokala miljö till ditt privata moln i Azure VMware-lösningen. ExpressRoute Global Reach-anslutningen upprättas mellan det privata molnet ExpressRoute-kretsen och en befintlig ExpressRoute-anslutning till dina lokala miljöer. 

ExpressRoute-kretsen som du använder när du [konfigurerar Azure-till-privata moln nätverk](tutorial-configure-networking.md) kräver att du skapar och använder auktoriseringsregler.  Du har redan använt en auktoriseringspost från ExpressRoute-kretsen och i den här självstudien skapar du en andra med en-peer-dator med din lokala ExpressRoute-krets.

I de här självstudierna får du lära dig att

> [!div class="checklist"]
> * Skapa en andra auktoriseringsregel för _krets 2_, den privata molnets ExpressRoute-krets
> * Använd antingen [Azure Portal](#azure-portal-method) eller [Azure CLI i en Cloud Shell-metod](#azure-cli-in-a-cloud-shell-method) i prenumerationen på _krets 1_ för att aktivera lokal-till-privat-moln ExpressRoute Global Reach-peering


## <a name="before-you-begin"></a>Innan du börjar

Innan du aktiverar anslutning mellan två ExpressRoute-kretsar med ExpressRoute Global Reach bör du läsa dokumentationen om hur du [aktiverar anslutningar i olika Azure-prenumerationer](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).  


## <a name="prerequisites"></a>Förutsättningar

- Upprättad anslutning till och från ett privat moln i Azure VMware-lösningen med sin ExpressRoute-krets med en ExpressRoute-gateway i ett virtuellt Azure-nätverk (VNet), som är _krets 2_ från peering-procedurer.  
- En separat, fungerande ExpressRoute-krets som används för att ansluta lokala miljöer till Azure, som är _krets 1_ från peering-procedurens perspektiv.
- Ett/29 icke-överlappande [nätverks adress block](../expressroute/expressroute-routing.md#ip-addresses-used-for-peerings) för ExpressRoute Global Reach-peering.
- Se till att alla routrar som inkluderar ExpressRoute-leverantörens tjänst stöder autonomt system nummer (ASN) med 4 byte. Azure VMware-lösningen använder en ASN: er på 4 byte för att annonsera vägar.

> [!TIP]
> I samband med dessa förutsättningar är din lokala ExpressRoute-krets _krets 1_, och din privata moln ExpressRoute-krets är i en annan prenumeration och har märkt _krets 2_. 


## <a name="create-an-expressroute-authorization-key-in-the-private-cloud"></a>Skapa en ExpressRoute-auktoriseringsregel i det privata molnet

1. I **översikten över** det privata molnet under hantera väljer du **anslutnings > ExpressRoute > begära en nyckel för autentisering**.

   :::image type="content" source="media/expressroute-global-reach/start-request-auth-key.png" alt-text="Välj anslutnings > ExpressRoute > begära en autentiseringsnyckel för att starta en ny begäran.":::

2. Ange namnet på verifierings nyckeln och välj **skapa**. 

   :::image type="content" source="media/expressroute-global-reach/create-global-reach-auth-key.png" alt-text="Välj Skapa för att skapa en ny nyckel för autentisering. ":::

   När den nya nyckeln har skapats visas den i listan över auktoriseringsregler för det privata molnet. 

   :::image type="content" source="media/expressroute-global-reach/show-global-reach-auth-key.png" alt-text="Bekräfta att den nya verifierings nyckeln visas i listan över nycklar för det privata molnet. ":::

3. Anteckna auktoriseringsregeln och ExpressRoute-ID, tillsammans med adress blocket/29. Du ska använda dem i nästa steg för att slutföra peer kopplingen. 

## <a name="peer-private-cloud-to-on-premises-using-authorization-key"></a>Peer-privat moln till lokalt med hjälp av auktoriseringsregeln

Nu när du har skapat en auktoriseringsregel för ExpressRoute-kretsen för det privata molnet kan du koppla den till din lokala ExpressRoute-krets.  Peer koppling görs från den lokala ExpressRoute-kretsens perspektiv i antingen [Azure Portal](#azure-portal-method) eller med hjälp av [Azure CLI i en Cloud Shell](#azure-cli-in-a-cloud-shell-method). Med båda metoderna använder du resurs-ID och verifierings nyckel för din privata moln ExpressRoute-krets för att slutföra peer kopplingen.

### <a name="azure-portal-method"></a>Azure Portal metod

1. Logga in på [Azure Portal](https://portal.azure.com) med samma prenumeration som den lokala ExpressRoute-kretsen.

1. I **översikten över** det privata molnet under hantera väljer du **anslutnings > ExpressRoute Global Reach > Lägg till**.

   :::image type="content" source="./media/expressroute-global-reach/expressroute-global-reach-tab.png" alt-text="Från menyn väljer du anslutning, fliken ExpressRoute Global Reach och sedan Lägg till.":::

1. Du kan skapa en lokal moln anslutning genom att göra något av följande val:

   - Välj ExpressRoute-kretsen i listan.
   - Om du har ett krets-ID kopierar du och klistrar in det.

1. Välj **Anslut**. Den nya anslutningen visas i listan lokal moln anslutning.  

>[!TIP]
>Du kan ta bort eller koppla från en anslutning från listan genom att välja **mer**.  
>
> :::image type="content" source="./media/expressroute-global-reach/on-premises-connection-disconnect.png" alt-text="Koppla från eller ta bort en lokal anslutning":::

### <a name="azure-cli-in-a-cloud-shell-method"></a>Azure CLI i en Cloud Shell metod

Vi har förstärkt CLI- [kommandona](../expressroute/expressroute-howto-set-global-reach-cli.md) med detaljerad information och exempel som hjälper dig att konfigurera ExpressRoute-Global Reach peering mellan lokala miljöer till ett privat moln i Azure VMware-lösningen.  

> [!TIP]  
> För det kortfattat i Azure CLI-kommandot utdata kan de här instruktionerna använda ett [ `–query` argument för att köra en JMESPath-fråga för att endast visa de nödvändiga resultaten](/cli/azure/query-azure-cli).


1. Logga in på Azure Portal med samma prenumeration som den lokala ExpressRoute-kretsen och öppna en Cloud Shell. Lämna gränssnittet som bash.
 
   :::image type="content" source="media/expressroute-global-reach/open-cloud-shell.png" alt-text="Logga in på Azure Portal och öppna en Cloud Shell.":::
 
2. Skapa peer koppling genom att ange Azure CLI-kommandot. Använd din speciella information och resurs-ID, auktoriseringskod och/29 CIDR-Adressblock. 

   Bilden visar ett exempel på kommandot som du kommer att använda och utdata som indikerar en lyckad peering. Exempel kommandot baseras på kommandot som används i [steg 3 i "aktivera anslutning mellan ExpressRoute-kretsar i olika Azure-prenumerationer"](../expressroute/expressroute-howto-set-global-reach-cli.md#enable-connectivity-between-expressroute-circuits-in-different-azure-subscriptions).

   :::image type="content" source="media/expressroute-global-reach/azure-command-with-results.png" alt-text="Skapa ExpressRoute Global Reach peering med Azure CLI-kommandot i en Cloud Shell.":::
 
   Du kan ansluta från lokala miljöer till ditt privata moln via ExpressRoute Global Reach-peering.

> [!TIP]
> Du kan ta bort peering som du nyss skapade genom att följa anvisningarna [för att inaktivera anslutningen mellan dina lokala nätverk](../expressroute/expressroute-howto-set-global-reach-cli.md#disable-connectivity-between-your-on-premises-networks) .


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en andra Authorization-nyckel för den privata moln ExpressRoute kretsen. Du har också lärt dig hur du aktiverar Global Reach ExpressRoute-peering lokalt till privat moln. 

Fortsätt till nästa självstudie och lär dig hur du distribuerar och konfigurerar VMware HCX-lösningen för ditt privata moln i Azure VMware-lösningen.

> [!div class="nextstepaction"]
> [Distribuera och konfigurera VMware HCX](tutorial-deploy-vmware-hcx.md)


<!-- LINKS - external-->

<!-- LINKS - internal -->