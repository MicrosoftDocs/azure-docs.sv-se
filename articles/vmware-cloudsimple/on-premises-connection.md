---
title: Azure VMware-lösning av CloudSimple-on-premises-anslutning med ExpressRoute
description: Beskriver hur du begär en lokal anslutning med ExpressRoute från CloudSimple regions nätverk
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 27d1d9e54838d9f45a28d634fa1c24fb8785aee1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899243"
---
# <a name="connect-from-on-premises-to-cloudsimple-using-expressroute"></a>Ansluta från den lokala platsen till CloudSimple med ExpressRoute

Om du redan har en Azure ExpressRoute-anslutning från en extern plats (till exempel lokalt) till Azure kan du ansluta den till din CloudSimple-miljö. Du kan göra det via en Azure-funktion som gör att två ExpressRoute-kretsar kan ansluta till varandra. Den här metoden upprättar en säker, privat, hög bandbredds anslutning med låg latens mellan de två miljöerna.

[![Anslutning till lokalt ExpressRoute – Global Reach](media/cloudsimple-global-reach-connection.png)](media/cloudsimple-global-reach-connection.png)

## <a name="before-you-begin"></a>Innan du börjar

Ett **/29** nätverks adress block krävs för att upprätta Global Reach anslutning lokalt.  Adress utrymmet/29 används för överförings nätverk mellan ExpressRoute-kretsar.  Överförings nätverket bör inte överlappa något av dina virtuella Azure-nätverk, lokala nätverk eller CloudSimple privata moln nätverk.

## <a name="prerequisites"></a>Förutsättningar

* En Azure ExpressRoute-krets krävs innan du kan upprätta anslutningen mellan kretsen och CloudSimple privata moln nätverk.
* En användare måste ha behörighet att skapa auktoriseringsarkiv på en ExpressRoute-krets.

## <a name="scenarios"></a>Scenarier

Genom att ansluta ditt lokala nätverk till ditt privata moln nätverk kan du använda det privata molnet på olika sätt, inklusive följande scenarier:

* Få åtkomst till ditt privata moln nätverk utan att skapa en plats-till-plats-VPN-anslutning.
* Använd din lokala Active Directory som identitets källa i ditt privata moln.
* Migrera virtuella datorer som körs lokalt till ditt privata moln.
* Använd ditt privata moln som en del av en katastrof återställnings lösning.
* Använd lokala resurser på dina virtuella datorer med privat moln arbets belastning.

## <a name="connecting-expressroute-circuits"></a>Ansluta ExpressRoute-kretsar

För att upprätta ExpressRoute-anslutningen måste du skapa en auktorisering på din ExpressRoute-krets och ange auktoriseringsinformation till CloudSimple.


### <a name="create-expressroute-authorization"></a>Skapa ExpressRoute-auktorisering

1. Logga in på Azure-portalen.

2. Sök efter **ExpressRoute-krets** i det övre Sök fältet och klicka på **ExpressRoute-kretsar** under **tjänster**.
    [![ExpressRoute-kretsar](media/azure-expressroute-transit-search.png)](media/azure-expressroute-transit-search.png)

3. Välj den ExpressRoute-krets som du vill ansluta till ditt CloudSimple-nätverk.

4. På sidan ExpressRoute klickar du på **auktoriseringar**, anger ett namn för auktoriseringen och klickar på **Spara**.
    [![Auktorisering av ExpressRoute-krets](media/azure-expressroute-transit-authorizations.png)](media/azure-expressroute-transit-authorizations.png)

5. Kopiera resurs-ID och verifierings nyckel genom att klicka på kopierings ikonen. Klistra in ID och nyckel i en textfil.
    [![ExpressRoute krets-auktorisering kopia](media/azure-expressroute-transit-authorization-copy.png)](media/azure-expressroute-transit-authorization-copy.png)

    > [!IMPORTANT]
    > **Resurs-ID** måste kopieras från användar gränssnittet och ska vara i formatet ```/subscriptions/<subscription-ID>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/expressRouteCircuits/<express-route-circuit-name>``` när du ger det stöd.

6. File a Ticket med <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">stöd</a> för anslutningen som ska skapas.
    * Typ av problem: **teknisk**
    * Prenumeration: **prenumeration där CloudSimple-tjänsten har distribuerats**
    * Tjänst: **VMware-lösning av CloudSimple**
    * Problem typ: **tjänstbegäran**
    * Problem under typ: **skapa ExpressRoute-anslutning till lokalt**
    * Ange resurs-ID och den auktoriseringskod som du kopierade och sparade i informations fönstret.
    * Ange ett/29 nätverks adress utrymme för överförings nätverket.
    * Skickar du standard väg via ExpressRoute?
    * Ska trafiken i det privata molnet använda standard vägen som skickas via ExpressRoute?

    > [!IMPORTANT]
    > Genom att skicka standard väg kan du skicka all Internet trafik från det privata molnet med hjälp av din lokala Internet anslutning.  Om du vill inaktivera standard vägen som kon figurer ATS i det privata molnet och använda den lokala anslutningens standard väg anger du informationen i support ärendet.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om Azures nätverks anslutningar](cloudsimple-azure-network-connection.md)  
