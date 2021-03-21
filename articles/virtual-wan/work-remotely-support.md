---
title: Azure Virtual WAN och arbeta fjärran slutet
description: Den här sidan beskriver hur du kan utnyttja Azure Virtual WAN för att kunna arbeta med fjärr anslutning på grund av COVID-19 Pandemic.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/22/2020
ms.author: cherylmc
ms.openlocfilehash: fc048f3da3156f5e17cfa32479b834b7320a60a2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023506"
---
# <a name="azure-virtual-wan-and-supporting-remote-work"></a>Azure Virtual WAN och stöd för fjärran sluten arbete

>[!NOTE]
>Den här artikeln beskriver hur du kan utnyttja Azure Virtual WAN, Azure, Microsoft Network och Azure-partnerns eko system för att fjärrans luta till och minimera nätverks problem som du står inför på grund av COVID-19-kris.
>

Är du förvrängda för att tillhandahålla anslutning för fjärran vändare?
Ser du plötsligt ett behov av att stödja en överspänning av användare utöver vad du hade planerat för?
Behöver användaren ansluta hemifrån och inte bara komma åt molnet men kan även nå lokalt?
Behöver dina fjärran vändare kunna komma åt resurser bakom ett privat WAN?
Har du ett behov av att användare kommer åt moln resurser utan att behöva konfigurera anslutning mellan regioner?
Eftersom den här globala Pandemic skapar oöverträffade förändringar runt oss, finns Azures virtuella WAN-team här för att du ska kunna tillgodose dina anslutnings behov.

Azure Virtual WAN är en nätverks tjänst som ger många funktioner för nätverk, säkerhet och routning tillsammans för att tillhandahålla ett enda drift gränssnitt. Dessa funktioner inkluderar anslutning till förgrening (via anslutnings automatisering från virtuella WAN-partner enheter som SD-WAN eller VPN CPE), plats-till-plats-VPN-anslutning, fjärran sluten användare VPN-anslutning (punkt-till-plats), privat (ExpressRoute) anslutning (transitiv anslutning för virtuella nätverk), VPN-ExpressRoute, routning, Azure-brandvägg, kryptering för privat anslutning Du behöver inte ha alla dessa användnings fall för att börja använda Virtual WAN. Du kan komma igång med bara ett användnings fall och justera nätverket när det utvecklas.

![Virtual WAN-diagram](./media/virtual-wan-about/virtualwan1.png)

Nu pratar om fjärran vändare, så att du kan se vad du behöver för att komma igång med ditt nätverk:

## <a name="set-up-remote-user-connectivity"></a><a name="connectivity"></a>Konfigurera fjärran sluten användar anslutning

Du kan ansluta till dina resurser i Azure via en IPsec/IKE (IKEv2) eller OpenVPN-anslutning. Den här typen av anslutning kräver att en VPN-klient konfigureras för fjärran vändaren. Den här klienten kan vara [Azure VPN-klienten](https://go.microsoft.com/fwlink/?linkid=2117554) eller OpenVPN-klienten eller en klient som stöder IKEv2. Mer information finns i [skapa en punkt-till-plats-anslutning](virtual-wan-point-to-site-portal.md).

## <a name="connectivity-from-the-remote-user-to-on-premises"></a><a name="remote user connectivity"></a>Anslutning från fjärran vändaren till lokalt

Du kan välja mellan två alternativ:

* Konfigurera plats-till-plats-anslutning med en befintlig VPN-enhet. När du ansluter IPsec VPN-enheten till Azure Virtual WAN Hub, är anslutningen mellan punkt-till-plats-användarens VPN (fjärran vändare) och VPN för plats-till-plats automatisk. Mer information om hur du konfigurerar plats-till-plats-VPN från din lokala VPN-enhet till Azure Virtual WAN finns i [skapa en plats-till-plats-anslutning med hjälp av virtuellt WAN](virtual-wan-site-to-site-portal.md).

* Anslut din ExpressRoute-krets till den virtuella WAN-hubben. Att ansluta en ExpressRoute-krets kräver att du distribuerar en ExpressRoute-gateway i virtuella WAN-nätverk. Så snart som du har distribuerat en anslutning mellan den punkt-till-plats-användarens VPN-och ExpressRoute-användare är automatisk. Information om hur du skapar ExpressRoute-anslutningen finns i [skapa en ExpressRoute-anslutning med Virtual WAN](virtual-wan-expressroute-portal.md). Du kan använda en befintlig ExpressRoute-krets för att ansluta till Azure Virtual WAN.

## <a name="existing-basic-virtual-wan-customer"></a><a name="basic vWAN"></a>Befintlig grundläggande virtuell WAN-kund

Basic Virtual WAN tillhandahåller endast plats-till-plats-VPN. För att fjärran vändare ska kunna ansluta måste du uppgradera det virtuella WAN-nätverket till standard virtuella WAN. Anvisningar för hur du uppgraderar ett virtuellt WAN finns i [uppgradera ett virtuellt WAN-nätverk från Basic till standard](upgrade-virtual-wan.md)

## <a name="additional-information"></a><a name="other considerations"></a>Ytterligare information

Virtuellt WAN stöder ett hubb per region/plats. Plats information finns i artikeln [virtuella WAN-partners och platser](virtual-wan-locations-partners.md) . Varje hubb har stöd för upp till 10 000 fjärran vändare, 1 000 gren anslutning, fyra ExpressRoute-kretsar och upp till 500 Virtual Network anslutningar. När du skalar upp fjärranslutna användare, om du har några frågor, behöver du inte ovilliga för att söka efter hjälp genom att skicka ett e-postmeddelande till azurevirtualwan@microsoft.com . Om du behöver teknisk support måste du öppna ett support ärende från Azure Portal så kommer hjälpen att vara på väg.

## <a name="faq"></a><a name="faq"></a>Vanliga frågor

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i [Översikt över virtuella WAN](virtual-wan-about.md)