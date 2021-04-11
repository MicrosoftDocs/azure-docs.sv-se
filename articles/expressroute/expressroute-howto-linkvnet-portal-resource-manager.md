---
title: 'Självstudie: länka ett VNet till en ExpressRoute-krets – Azure Portal'
description: Den här självstudien visar hur du skapar en anslutning för att länka ett virtuellt nätverk till en Azure ExpressRoute-krets med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: a9bfb4beea2c2bec1b819d228215cfff65e37fe4
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106110265"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Självstudie: ansluta ett virtuellt nätverk till en ExpressRoute-krets med hjälp av portalen

> [!div class="op_single_selector"]
> * [Azure-portalen](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassisk)](expressroute-howto-linkvnet-classic.md)
> 

Den här självstudien hjälper dig att skapa en anslutning för att länka ett virtuellt nätverk till en Azure ExpressRoute-krets med hjälp av Azure Portal. De virtuella nätverk som du ansluter till din Azure ExpressRoute-krets kan antingen finnas i samma prenumeration eller vara en del av en annan prenumeration.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> - Anslut ett virtuellt nätverk till en krets i samma prenumeration.
> - Anslut ett virtuellt nätverk till en krets i en annan prenumeration.
> - Ta bort länken mellan det virtuella nätverket och ExpressRoute-kretsen.

## <a name="prerequisites"></a>Förutsättningar

* Granska kraven [](expressroute-prerequisites.md), kraven för [routning](expressroute-routing.md)och [arbets flöden](expressroute-workflows.md) innan du påbörjar konfigurationen.

* Du måste ha en aktiv ExpressRoute-krets.
  * Följ anvisningarna för att [skapa en ExpressRoute-krets](expressroute-howto-circuit-portal-resource-manager.md) och ha den krets som Aktiver ATS av anslutnings leverantören.
  * Se till att du har konfigurerat Azures privata peering för din krets. Se artikeln [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-portal-resource-manager.md) för peering-och routningsmetod.
  * Se till att Azures privata peering konfigureras och upprättar BGP-peering mellan ditt nätverk och Microsoft för anslutning från slut punkt till slut punkt.
  * Se till att du har ett virtuellt nätverk och en virtuell nätverksgateway som skapats och är helt etablerad. Följ anvisningarna för att [skapa en virtuell nätverksgateway för ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). En virtuell nätverksgateway för ExpressRoute använder GatewayType "ExpressRoute", inte VPN.

* Du kan länka upp till 10 virtuella nätverk till en standard ExpressRoute-krets. Alla virtuella nätverk måste finnas i samma naturpolitisk region när man använder en standard ExpressRoute-krets.

* Ett enda VNet kan länkas till upp till 16 ExpressRoute-kretsar. Använd följande process för att skapa ett nytt anslutnings objekt för varje ExpressRoute-krets som du ansluter till. ExpressRoute-kretsarna kan vara i samma prenumeration, olika prenumerationer eller en blandning av båda.

* Om du aktiverar ExpressRoute Premium-tillägget kan du länka virtuella nätverk utanför det politiska området för ExpressRoute-kretsen. Med Premium-tillägget kan du också ansluta fler än 10 virtuella nätverk till din ExpressRoute-krets beroende på vilken bandbredd som valts. Läs [vanliga frågor och svar](expressroute-faqs.md) om du vill ha mer information om Premium-tillägget.

* Du kan [Visa en video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) innan du börjar för att bättre förstå stegen.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Ansluta ett VNet till en krets-samma prenumeration

> [!NOTE]
> BGP-konfigurationsinformation visas inte om Layer 3-providern har konfigurerat dina peer-konfigurationer. Om kretsen är i ett etablerings läge, bör du kunna skapa anslutningar.
>

### <a name="to-create-a-connection"></a>Skapa en anslutning

1. Se till att din ExpressRoute-krets och Azures privata peering har kon figurer ATS korrekt. Följ anvisningarna i [skapa en ExpressRoute-krets](expressroute-howto-circuit-arm.md) och [skapa och ändra peering för en ExpressRoute-krets](expressroute-howto-routing-arm.md). Din ExpressRoute-krets bör se ut som på följande bild:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Skärm bild av ExpressRoute-krets":::

1. Nu kan du starta etableringen av en anslutning för att länka din virtuella nätverksgateway till ExpressRoute-kretsen. Välj **anslutning**  >  **Lägg** till för att öppna sidan **Lägg till anslutning** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Skärm bild för Lägg till anslutning":::

1. Ange ett namn för anslutningen och välj sedan **Nästa: inställningar >**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="Sidan Skapa anslutning, bassida":::

1. Välj den gateway som tillhör det virtuella nätverk som du vill länka till kretsen och välj **Granska + skapa**. Välj sedan **skapa** När valideringen är klar.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="Sidan skapa anslutnings inställningar":::

1. När anslutningen har kon figurer ATS visas informationen för anslutningen i anslutnings objektet.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Skärm bild för anslutnings objekt":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Ansluta ett VNet till en krets-annan prenumeration

Du kan dela en ExpressRoute-krets över flera prenumerationer. Följande bild visar en enkel Schematisk över hur delning fungerar för ExpressRoute-kretsar över flera prenumerationer.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Anslutning mellan prenumerationer":::

Vart och ett av de mindre molnen i det stora molnet används för att representera prenumerationer som tillhör olika avdelningar i en organisation. Varje avdelning inom organisationen använder sin egen prenumeration för att distribuera tjänsterna – men de kan dela en enda ExpressRoute-krets för att ansluta till ditt lokala nätverk. En enda avdelning (i det här exemplet: IT) kan äga ExpressRoute-kretsen. Andra prenumerationer i organisationen kan använda ExpressRoute-kretsen.

  > [!NOTE]
  > Anslutnings-och bandbredds avgifter för den dedikerade kretsen kommer att användas för ExpressRoute-kretsens ägare. Alla virtuella nätverk delar samma bandbredd.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administration – om krets ägare och krets användare

Krets ägaren är en behörig privilegie rad användare av ExpressRoute-krets resursen. Krets ägaren kan skapa auktoriseringar som kan lösas av "krets användare". Krets användare är ägare till virtuella nätverksgateway som inte är inom samma prenumeration som ExpressRoute-kretsen. Krets användare kan lösa in auktoriseringar (en auktorisering per virtuellt nätverk).

Krets ägaren har möjlighet att ändra och återkalla auktorisering när som helst. Att återkalla ett auktoriserings resultat i alla länk anslutningar tas bort från prenumerationen vars åtkomst har återkallats.

### <a name="circuit-owner-operations"></a>Krets ägare åtgärder

**Så här skapar du en anslutnings auktorisering**

Krets ägaren skapar en auktorisering, som skapar en auktoriseringsregel som ska användas av en krets användare för att ansluta sina virtuella nätverksgateway till ExpressRoute-kretsen. En auktorisering är endast giltig för en anslutning.

> [!NOTE]
> Varje anslutning kräver en separat auktorisering.
>

1. På sidan ExpressRoute väljer du **auktoriseringar** och anger sedan ett **namn** för auktoriseringen och väljer **Spara**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Auktoriseringar":::

2. När konfigurationen har sparats kopierar du **resurs-ID** och **behörighets nyckel**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Auktoriseringsregel":::

**Ta bort en auktorisering för anslutning**

Du kan ta bort en anslutning genom att välja ikonen **ta bort** för din anslutnings nyckel.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="Ta bort auktoriseringsregel":::

Om du vill ta bort anslutningen men behålla verifierings nyckeln kan du ta bort anslutningen från anslutnings sidan för kretsen.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="Ta bort anslutningens ägande krets":::

### <a name="circuit-user-operations"></a>Krets användar åtgärder

Krets användaren behöver resurs-ID och en auktoriseringskod från krets ägaren.

**Så här löser du en auktorisering av anslutningen**

1. Välj knappen **+ skapa en resurs** . Sök efter **anslutning** och välj **skapa**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Skapa nya resurser":::

1. Kontrol lera att *anslutnings typen* är inställd på **ExpressRoute**. Välj *resurs grupp* och *plats* och välj sedan **OK** på sidan grundläggande.

    > [!NOTE]
    > Platsen *måste* matcha platsen för den virtuella Nätverksgatewayen som du skapar anslutningen för.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Sidan Grundinställningar":::

1. På sidan **Inställningar** väljer du den *virtuella Nätverksgatewayen* och markerar kryss rutan **Lös in auktorisering** . Ange *verifierings nyckeln* och *peer-kretsens URI* och ge anslutningen ett namn. Välj **OK**. 
 
    > [!NOTE]
    > *Peer-kretsens URI* är resurs-ID för ExpressRoute-kretsen (som du hittar under egenskaps inställnings fönstret i ExpressRoute-kretsen).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Sidan Inställningar":::

1. Granska informationen på sidan **Sammanfattning** och välj **OK**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="Sammanfattningssida":::

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort en anslutning och ta bort länken mellan ditt VNet och en ExpressRoute-krets genom att välja ikonen **ta bort** på sidan för din anslutning.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="Ta bort anslutning":::

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du ansluter ett virtuellt nätverk till en krets i samma prenumeration och en annan prenumeration. Mer information om ExpressRoute-gatewayen finns i: 

> [!div class="nextstepaction"]
> [Om ExpressRoute-gatewayer för virtuella nätverk](expressroute-about-virtual-network-gateways.md)
