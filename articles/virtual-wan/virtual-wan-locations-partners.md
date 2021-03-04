---
title: Azure virtuella WAN-partners och platser | Microsoft Docs
description: Den här artikeln innehåller en lista över virtuella Azure-partners och nav platser i Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: cherylmc
ms.custom: references_regions
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 1e8a8aa632d4c91cdfc6753d988a439d2de8938e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040555"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuella WAN-partner och platser för virtuella hubbar

Den här artikeln innehåller information om regioner och partner för virtuella WAN-regioner som stöds för anslutning till virtuellt nav.

Azure Virtual WAN är en nätverkstjänst som tillhandahåller optimerade och automatiserade anslutningar mellan olika platser via Azure. Med Virtual WAN kan du ansluta och konfigurera platsspecifika enheter så att de kommunicerar med Azure. Anslutning och konfiguration kan göras antingen manuellt eller genom att använda leverantörs enheter via en virtuell WAN-partner. Genom att använda partner enheter kan du enkelt använda, förenkla anslutning och konfigurations hantering.

Anslutning från den lokala enheten upprättas på ett automatiserat sätt till den virtuella hubben. En virtuell hubb är ett Microsoft-hanterat virtuellt nätverk. Navet innehåller olika tjänstslutpunkter för anslutning från ditt lokala nätverk (vpnsite). Du kan bara ha en hubb per region.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Förgrening av IPsec-anslutning från partners

Enheter som ansluter till Azure Virtual WAN har inbyggd Automation för att ansluta. Detta är vanligt vis konfigurerat i användar gränssnittet för enhets hantering (eller motsvarande), som konfigurerar anslutnings-och konfigurations hanteringen mellan VPN-huvudenheten och VPN-slutpunkten för en virtuell Azure-hubb (VPN-gateway).

Följande avancerade automatiserings funktioner har kon figurer ATS i enhets konsolen/hanterings centret:

* Lämpliga behörigheter för enheten för åtkomst till Azure Virtual WAN-resurs gruppen
* Överföring av förgrenings enhet till Azure Virtual WAN
* Automatisk nedladdning av Azures anslutnings information
* Konfiguration av lokal lokal enhet 

Vissa anslutnings partner kan utöka automatiseringen så att du kan ta med att skapa Azure Virtual Hub VNet och VPN Gateway. Om du vill veta mer om automation, se [automatiserings rikt linjer för virtuella WAN-partner](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Filialer för IPsec-anslutning

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Följande partner är planerad på vår översikt som baseras på en avtals rapport som är signerad mellan företag som visar omfattningen av arbete för att automatisera IPsec-anslutningen mellan partner enheten och Azure Virtual WAN VPN-gatewayer: 128 Technologies, Arista, F5-nätverk, Oracle SD-WAN (Talari) och SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Partner med integrerade virtuella Hubbs erbjudanden

Förutom att ha automatiserad IPsec-anslutning för avdelnings kontor, erbjuder vissa partners **virtuella nätverks installationer (NVA)** som kan integreras direkt i Azure Virtual WAN-hubben.  Detta gör det möjligt för kunderna att säga upp sina filial anslutningar till en kompatibel tredjepartsprogram i den virtuella hubben.  

Partner som erbjuder NVA i den virtuella WAN-hubben måste:

* Ha implementerat IPsec-anslutning från sin gren enhet och ha installerat sitt NVA-erbjudande till Azure Virtual WAN Hub.
* Det finns ett befintligt virtuellt nätverk för virtuell installation i Azure Marketplace.

Om du är partner och har frågor om de hanterade NVA i det virtuella hubb erbjudandet, kan du kontakta oss genom att skicka e-post till vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>Integrerade NVA-partner för virtuella hubbar

Dessa partner har **hanterade program** erbjudanden som nu är tillgängliga för distribution till den virtuella WAN-hubben.

|Partner|Guide för konfiguration/anvisningar/distribution|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Distributions guide för Barracuda CloudGen WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[VWAN för Cisco Cloud Service router (CSR)](https://aka.ms/ciscoMarketPlaceOffer)| Under den offentliga för hands versionen av Cisco Cloud Services (CSR) WAN till VWAN Hub kräver Cisco att kunden registrerar sig som en Cisco EFT-kund (tidig fält utvärdering) genom att skicka ett e-postmeddelande till vwan_public_preview@external.cisco.com och begära distributions guiden för vManage. |

Följande partner är planerad för att ta NVA i den virtuella hubben i en snar framtid: Aviatrix, Citrix, VeloCloud och versa-nätverk.

## <a name="locations"></a><a name="locations"></a>Platser

[!INCLUDE [Virtual WAN regions file](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Nästa steg

* Mer information om virtuellt WAN finns i [vanliga frågor och svar om virtuella](virtual-wan-faq.md)WAN.

* Mer information om hur du automatiserar anslutningen till Azure Virtual WAN finns i [automatiserings rikt linjer för virtuella WAN-partner](virtual-wan-configure-automation-providers.md).
