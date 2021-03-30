---
title: VMware-lösning av CloudSimple – Azure nätverks anslutningar
description: Lär dig hur du ansluter ditt virtuella Azure-nätverk till ditt CloudSimple regions nätverk
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: cfd4d65b07cf255ac2b60d6bf8376723a997374e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "77025086"
---
# <a name="azure-network-connections-overview"></a>Översikt över Azure nätverks anslutningar

När du skapar en CloudSimple-tjänst i en region och skapar noder kan du:

* Begär en Azure ExpressRoute-krets och koppla den till CloudSimple-nätverket i den regionen.
* Anslut ditt CloudSimple region nätverk till ditt virtuella Azure-nätverk eller ditt lokala nätverk med Azure ExpressRoute.
* Ge åtkomst till tjänster som körs i din Azure-prenumeration eller ditt lokala nätverk från din privata moln miljö.

ExpressRoute-anslutningen har hög bandbredd med låg latens.

## <a name="benefits"></a>Fördelar

Med Azure nätverks anslutning kan du:

* Använd Azure som ett säkerhets kopierings mål för virtuella datorer i ditt privata moln.
* Distribuera KMS-servrar i din Azure-prenumeration för att kryptera ditt privata moln virtuellt San data lager.
* Använd hybrid program där webb nivån för programmet körs i det offentliga molnet medan program-och databas nivåerna körs i ditt privata moln.

## <a name="azure-virtual-network-connection"></a>Azure Virtual Network-anslutning

Privata moln kan anslutas till dina Azure-resurser med hjälp av ExpressRoute.  Med ExpressRoute-anslutningen kan du komma åt resurser som körs i din Azure-prenumeration från ditt privata moln.  Med den här anslutningen kan du utöka ditt privata moln nätverk till ditt virtuella Azure-nätverk.  Vägar från CloudSimple nätverk kommer att bytas ut mot ditt virtuella Azure-nätverk via BGP.  Om du har konfigurerat det virtuella nätverkets peering kan alla peer-kopplade virtuella nätverk nås från CloudSimple-nätverket.

![Azure ExpressRoute-anslutning till virtuellt nätverk](media/cloudsimple-azure-network-connection.png)

## <a name="expressroute-connection-to-on-premises-network"></a>ExpressRoute anslutning till lokalt nätverk

Du kan ansluta din befintliga Azure ExpressRoute-krets till din CloudSimple-region. ExpressRoute-funktionen Global Reach används för att ansluta de två kretsarna med varandra.  En anslutning upprättas mellan lokala och CloudSimple ExpressRoute-kretsar.  Med den här anslutningen kan du utöka ditt lokala nätverk till ett privat moln nätverk. Vägar från ditt CloudSimple-nätverk kommer att bytas via BGP med ditt lokala nätverk.

![Anslutning till lokalt ExpressRoute – Global Reach](media/cloudsimple-global-reach-connection.png)

## <a name="connection-to-on-premises-network-and-azure-virtual-network"></a>Anslutning till lokalt nätverk och Azure Virtual Network

Anslutningar till det lokala nätverket och det virtuella Azure-nätverket kan samverka från CloudSimple-nätverket.  Anslutningen använder BGP för att utväxla vägar mellan lokala nätverk, Azure Virtual Network och CloudSimple Network.  När du ansluter ditt CloudSimple-nätverk till ditt virtuella Azure-nätverk i närvaro av Global Reach anslutning, kommer Azures virtuella nätverks vägar att synas i ditt lokala nätverk.  Route Exchange sker i Azure mellan de yttre routrarna.

![Lokal ExpressRoute-anslutning med Azure Virtual Network-anslutning](media/cloudsimple-global-reach-and-vnet-connection.png)

### <a name="important-considerations"></a>Att tänka på

Genom att ansluta till CloudSimple-nätverk från det lokala nätverket och från Azure Virtual Network kan du dirigera Exchange mellan alla nätverk.

* Det virtuella Azure-nätverket kommer att synas från både det lokala nätverket och CloudSimple-nätverket.
* Om du har anslutit till ditt virtuella Azure-nätverk från det lokala nätverket kan anslutningen till CloudSimple-nätverket med Global Reach ge åtkomst till virtuella nätverk från CloudSimple-nätverket.
* Under näts adresserna **får inte** överlappa något av nätverken som är anslutna.
* CloudSimple meddelar **inte** standard vägen till ExpressRoute-anslutningarna
* Om din lokala router annonserar standard vägen, kommer trafiken från CloudSimple-nätverket och det virtuella Azure-nätverket använda den annonserade standard vägen.  Därför går det inte att komma åt virtuella datorer på Azure med offentliga IP-adresser.

## <a name="next-steps"></a>Nästa steg

* [Anslut Azure Virtual Network till CloudSimple med ExpressRoute](virtual-network-connection.md)
* [Ansluta från den lokala platsen till CloudSimple med ExpressRoute](on-premises-connection.md)
