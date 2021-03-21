---
title: 'Snabb start: skapa och konfigurera Route server med hjälp av Azure Portal'
description: I den här snabb starten får du lära dig hur du skapar och konfigurerar en Route-server med hjälp av Azure Portal.
services: route-server
author: duongau
ms.service: route-server
ms.topic: quickstart
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: f76c48af4f5ebc8013daad457f9973cf7792c7c6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548008"
---
# <a name="quickstart-create-and-configure-route-server-using-the-azure-portal"></a>Snabb start: skapa och konfigurera Route server med hjälp av Azure Portal

Den här artikeln hjälper dig att konfigurera Azure Route Server för peer-koppling med en virtuell nätverks installation (NVA) i det virtuella nätverket med hjälp av Azure Portal. Azure Route server kommer att lära sig vägar från NVA och program på de virtuella datorerna i det virtuella nätverket. Azure Route server kommer också att annonsera de virtuella nätverks vägarna till NVA. Mer information finns i [Azure Route Server](overview.md).

> [!IMPORTANT]
> Azure Route Server (för hands version) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Granska [tjänst gränserna för Azure Route Server](route-server-faq.md#limitations).

## <a name="create-a-route-server"></a>Skapa en Route-Server

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Logga in på ditt Azure-konto och välj din prenumeration.

Öppna en webbläsare, navigera till [Azure Portal](https://portal.azure.com) och logga in med ditt Azure-konto.

### <a name="create-a-route-server"></a>Skapa en Route-Server

1. Gå till https://aka.ms/routeserver.

1. Välj **+ Skapa ny väg Server**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-landing-page.png" alt-text="Skärm bild av landnings sidan för Route Server."::: 

1. På sidan **skapa en Route-Server** anger du eller väljer den information som krävs.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/create-route-server-page.png" alt-text="Skärm bild av sidan Skapa Route Server.":::     

    | Inställningar | Värde |
    |----------|-------|
    | Prenumeration | Välj den Azure-prenumeration som du vill använda för att distribuera väg servern. |
    | Resursgrupp | Välj en resurs grupp som du vill skapa väg servern i. Om du inte har en befintlig resurs grupp kan du skapa en ny. |
    | Name | Ange ett namn på väg servern. |
    | Region | Välj den region som flödes servern ska skapas i. Välj samma region som det virtuella nätverk som du skapade tidigare för att se det virtuella nätverket i list rutan. |
    | Virtual Network | Välj det virtuella nätverk där väg servern ska skapas. Du kan skapa ett nytt virtuellt nätverk eller använda ett befintligt virtuellt nätverk. Om du använder ett befintligt virtuellt nätverk kontrollerar du att det befintliga virtuella nätverket har tillräckligt med utrymme för minst ett/27-undernät för att uppfylla kraven för Dirigerings serverns undernät. Om du inte ser det virtuella nätverket i list rutan ser du till att du har valt rätt resurs grupp eller region. |
    | Undernät | När du har skapat eller valt ett virtuellt nätverk visas fältet undernät. Det här under nätet är endast avsett för att dirigera Server. Välj **Hantera under näts konfiguration** och skapa Azure Route Server-undernätet. Välj **+ undernät** och skapa ett undernät med hjälp av följande rikt linjer:</br><br>-Under nätet måste ha namnet *RouteServerSubnet*.</br><br>-Under nätet måste vara minst/27 eller större.</br> |

1. Välj **Granska + skapa**, Granska sammanfattningen och välj sedan **skapa**. 

    > [!NOTE]
    > Distributionen av Route-servern tar cirka 20 minuter.

## <a name="set-up-peering-with-nva"></a>Konfigurera peering med NVA

Avsnittet hjälper dig att konfigurera BGP-peering med din NVA.

1. Gå till [Route Server](https://aka.ms/routeserver) i Azure Portal och välj den Route-server som du vill konfigurera.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/select-route-server.png" alt-text="Skärm bild av listan Route Server."::: 

1. Välj **peer-datorer** under *Inställningar* i den vänstra navigerings panelen. Välj sedan **+ Lägg** till för att lägga till en ny peer.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/peers-landing-page.png" alt-text="Skärm bild av landnings sidan för peer-datorer."::: 

1. Ange följande information om din NVA-peer.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/add-peer-page.png" alt-text="Skärm bild av sidan Lägg till peer.":::

    | Inställningar | Värde |
    |----------|-------|
    | Namn | Ange ett namn för peering mellan din Route-Server och NVA. |
    | ASN |  Ange din NVA autonoma system nummer (ASN). |
    | IPv4-adress | Ange IP-adressen för NVA som Route-servern ska kommunicera med för att upprätta BGP. |

1. Välj **Lägg** till för att lägga till denna peer.

## <a name="complete-the-configuration-on-the-nva"></a>Slutför konfigurationen av NVA

Du behöver Azure Route servers peer-IP och ASN för att slutföra konfigurationen på din NVA för att upprätta en BGP-session. Du kan hämta den här informationen från översikts sidan till din Route-Server.

:::image type="content" source="./media/quickstart-configure-route-server-portal/route-server-overview.png" alt-text="Skärm bild av översikts sidan för Route Server.":::

## <a name="configure-route-exchange"></a>Konfigurera Route Exchange

Om du har en ExpressRoute-gateway och/eller VPN-gateway och du vill att de ska utbyta vägar med Route-servern, kan du aktivera routning av Exchange.

1. Gå till [Route Server](https://aka.ms/routeserver) i Azure Portal och välj den Route-server som du vill konfigurera.

1. Välj **konfiguration** under *Inställningar* i den vänstra navigerings panelen.

1. Välj **Aktivera** för **gren-till-gren** -inställningen och välj sedan **Spara**.

    :::image type="content" source="./media/quickstart-configure-route-server-portal/enable-route-exchange.png" alt-text="Skärm bild av hur du aktiverar Route Exchange.":::

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver Azure Route-servern väljer du **ta bort** från översikts sidan för att avetablera väg servern.

:::image type="content" source="./media/quickstart-configure-route-server-portal/delete-route-server.png" alt-text="Skärm bild av hur du tar bort väg servern.":::

## <a name="next-steps"></a>Nästa steg

När du har skapat Azure Route Server kan du fortsätta med att lära dig hur Azure Route Server samverkar med ExpressRoute och VPN-gatewayer: 

> [!div class="nextstepaction"]
> [Azure-ExpressRoute och Azure VPN-support](expressroute-vpn-support.md)
