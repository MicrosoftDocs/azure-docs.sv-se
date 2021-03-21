---
title: 'Azure-ExpressRoute: återställa krets-peering med hjälp av Azure Portal'
description: Lär dig hur du inaktiverar och aktiverar peering för en Azure ExpressRoute-krets med hjälp av Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 432ecedbbb8965926499380eb1165fdf43018426
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680287"
---
# <a name="reset-expressroute-circuit-peerings-by-using-the-azure-portal"></a>Återställa ExpressRoute krets-peering med hjälp av Azure Portal

Den här artikeln beskriver hur du inaktiverar och aktiverar peering för en Azure ExpressRoute-krets med hjälp av Azure Portal. När du inaktiverar en peering stängs Border Gateway Protocol-sessionen (BGP) för både den primära och den sekundära anslutningen av ExpressRoute-kretsen. När du aktiverar en peering återställs BGP-sessionen på både den primära och den sekundära anslutningen av ExpressRoute-kretsen.

> [!Note]
> Första gången du konfigurerar peer-kopplingarna på ExpressRoute-kretsen är peer-kopplingarna aktiverade som standard.

Det kan vara praktiskt att återställa dina ExpressRoute-peerar i följande scenarier:

* Du testar din design och implementering av haveri beredskap. Anta till exempel att du har två ExpressRoute-kretsar. Du kan inaktivera peering-kopplingar för en krets och tvinga nätverks trafiken att använda den andra kretsen.

* Du vill aktivera upptäckt av dubbelriktad vidarebefordran (BFD) på Azures privata peering eller Microsoft-peering. Om din ExpressRoute-krets skapades före den 1 augusti 2018, på Azures privata peering eller före 10 januari 2020, på Microsoft-peering, var BFD inte aktiverat som standard. Återställ peering för att aktivera BFD.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Från en webbläsare går du till [Azure Portal](https://portal.azure.com)och loggar sedan in med ditt Azure-konto.

## <a name="reset-a-peering"></a>Återställa en peer koppling

Du kan återställa Microsoft-peering och Azures privata peering på en ExpressRoute-krets oberoende av varandra.

1. Välj den krets som du vill ändra.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="Skärm bild som visar hur man väljer en krets i ExpressRoute krets-listan.":::

1. Välj den peering-konfiguration som du vill återställa.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="Skärm bild som visar hur du väljer en peering i ExpressRoute krets-översikten.":::

1. Avmarkera kryss rutan **Aktivera peering** och välj sedan **Spara** för att inaktivera peering-konfigurationen.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Skärm bild som visar avmarkerar kryss rutan Aktivera peering.":::

1. Markera kryss rutan **Aktivera peering** och välj sedan **Spara** för att återaktivera peering-konfigurationen.

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker ExpressRoute-problem finns i följande artiklar:

* [Verifiera ExpressRoute-anslutning](expressroute-troubleshooting-expressroute-overview.md)
* [Felsöka nätverks prestanda](expressroute-troubleshooting-network-performance.md)
