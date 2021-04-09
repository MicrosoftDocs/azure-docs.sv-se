---
title: 'Azure Virtual WAN: om virtuell nätverks installation i hubben'
description: I den här artikeln får du lära dig om virtuella nätverks enheter i den virtuella WAN-hubben.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
Customer intent: As someone with a networking background, I want to learn about Network Virtual Appliances in the Virtual WAN hub.
ms.openlocfilehash: 365ed60e73be9bb2098022fa767f4ae54b93c37c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028088"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Om virtuell nätverks installation i en Azure Virtual WAN Hub (för hands version)

Azure Virtual WAN har arbetat med nätverks partners för att bygga automatisering som gör det enkelt att ansluta sin kund lokala utrustning (CPE) till en Azure VPN-gateway i den virtuella hubben. Azure arbetar med utvalda nätverks partners så att kunder kan distribuera en virtuell nätverks installation (NVA) från tredje part direkt i den virtuella hubben. Detta gör att kunder som vill ansluta sina grenar CPE till samma varumärke NVA i det virtuella navet, så att de kan dra nytta av patentskyddade funktioner från slut punkt till slut punkt för SD-WAN.

Barracuda Networks och Cisco Systems är de första partner som tillhandahåller NVA som kan distribueras direkt till den virtuella WAN-hubben.  Se [Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan) och [Cisco Cloud bearbetar för flera moln](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) för respektive produkt dokumentation. Azure arbetar med fler partners för att se att andra erbjudanden följer.

> [!NOTE]
> Endast NVA-erbjudanden som är tillgängliga för distribution i den virtuella WAN-hubben kan distribueras till den virtuella WAN-hubben. De kan inte distribueras till ett godtyckligt virtuellt nätverk i Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Hur fungerar det?

NVA som är tillgängliga för distribution direkt till Azures virtuella WAN-hubb är utformad för att användas i den virtuella hubben. NVA-erbjudandet publiceras på Azure Marketplace som ett hanterat program och kunder kan distribuera erbjudandet direkt från Azure Marketplace, eller så kan de distribuera erbjudandet från den virtuella hubben via Azure Portal.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Process översikt":::

Varje partners NVA-erbjudande har något annorlunda upplevelse och funktioner baserat på deras distributions krav. Det finns dock några saker som är gemensamma för alla partner erbjudanden för NVA i den virtuella WAN-hubben.

* En hanterad program upplevelse som erbjuds via Azure Marketplace.
* NVA-infrastrukturens enhets kapacitet och fakturering.
* Hälso måtten har flyttats via Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Hanterat program

Alla NVA-erbjudanden som är tillgängliga för distribution i den virtuella WAN-hubben har ett **hanterat program** som är tillgängligt på Azure Marketplace. Med hanterade program kan partners göra följande:

* Bygg en anpassad distributions upplevelse för deras NVA.
* Ange en specialiserad Resource Manager-mall som gör det möjligt för dem att skapa NVA direkt i den virtuella WAN-hubben.
* Fakturera Software License-kostnader direkt eller via Azure Marketplace.
* Visa anpassade egenskaper och resurs mätare.

NVA-partner kan skapa olika resurser beroende på deras installation, konfigurations licensiering och hanterings behov. När en kund skapar en NVA i den virtuella WAN-hubben, t. ex. alla hanterade program, kommer det att finnas två resurs grupper som skapats i prenumerationen.

* **Kund resurs grupp** – detta kommer att innehålla en program plats hållare för det hanterade programmet. Partner kan använda detta för att visa vilka kund egenskaper de väljer här.
* **Hanterad resurs grupp** – kunder kan inte konfigurera eller ändra resurser i den här resurs gruppen direkt, eftersom detta styrs av utgivaren av det hanterade programmet. Den här resurs gruppen kommer att innehålla **NetworkVirtualAppliances** -resursen.

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Resurs grupper för hanterade program":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA infrastruktur enheter

När du skapar en NVA i den virtuella WAN-hubben måste du välja det antal NVA som du vill distribuera den med. En **NVA infrastruktur enhet** är en enhet med sammanställd bandbredds kapacitet för en NVA i den virtuella WAN-hubben. En **NVA-infrastruktur enhet** liknar en VPN- [Scale-enhet](pricing-concepts.md#scale-unit) på samma sätt som du tycker om kapacitet och storlek.

* 1 NVA Infrastructure Unit representerar 500 Mbit/s med sammanställd bandbredd för alla filial plats anslutningar som kommer till denna NVA, med en kostnad på $0,25/timme.
* Azure stöder från 1-80 NVA infrastrukturs enheter för en specifik distribution av virtuella NVA-nav.
* Varje partner kan erbjuda olika NVA infrastruktur enhets paket som är en delmängd av alla konfigurationer för NVA som stöds av infrastruktur enheten.

På samma sätt som för VPN-Scale-enheter, om du väljer *1 NVA infrastruktur enhet = 500 Mbit/s*, innebär det att två instanser för redundans skapas, var och en har ett maximalt data flöde på 500 Mbit/s. Om du till exempel har fem grenar, var och en med 10 Mbit/s på grenen, behöver du en agg regering på 50 Mbit/s vid Head-slutet. Planeringen för NVA bör göras efter att du bedömt kapaciteten som krävs för att stödja antalet grenar till hubben.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Konfigurations process för virtuell nätverks installation

Partner har arbetat med att tillhandahålla en upplevelse som konfigurerar NVA automatiskt som en del av distributions processen. När NVA har etablerats i den virtuella hubben måste all ytterligare konfiguration som krävs för NVA göras via NVA partners portal eller hanterings program. Direkt åtkomst till NVA är inte tillgänglig.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Plats-och anslutnings resurser med NVA

Till skillnad från Azure VPN Gateway-konfigurationer behöver du inte skapa **plats** resurser, **plats-till-plats-anslutningar** eller **punkt-till-plats-anslutningar** för att ansluta dina förgreningar till din NVA i den virtuella WAN-hubben. Detta hanteras via NVA-partnern.

Du måste fortfarande skapa hubb-till-VNet-anslutningar för att ansluta din virtuella WAN-hubb till dina virtuella Azure-nätverk.

## <a name="supported-regions"></a><a name="regions"></a>Regioner som stöds

NVA i den virtuella hubben är tillgänglig för för hands version i följande regioner:

|Geopolitisk region | Azure-regioner|
|---|---|
| Nordamerika| Västra USA, södra centrala USA, östra USA 2   |
| Sydamerika | Brasilien, södra |
| Europa | Europa, västra Storbritannien, södra|
|  Mellanöstern | Förenade Arabemiraten, norra |
| Asien | Japan, östra |
| Australien | Australien, östra |

## <a name="faq"></a>Vanliga frågor

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Jag är en partner för nätverks installationer och vill få våra NVA i hubben.  Kan jag ansluta till det här partner programmet?

Tyvärr har vi inte kapacitet för att skapa nya partner erbjudanden på kort sikt för tillfället. Kom tillbaka med oss i november!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Kan jag distribuera alla NVA från Azure Marketplace till den virtuella WAN-hubben?

För närvarande finns endast [Barracuda CLOUDGEN WAN](https://aka.ms/BarracudaMarketPlaceOffer) -och [Cisco Cloud vWAN-program](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) tillgängliga för distribution i den virtuella WAN-hubben.

### <a name="what-is-the-cost-of-the-nva"></a>Vad kostar NVA?

Du måste köpa en licens för NVA från NVA-leverantören.  För din Barracuda CloudGen WAN-NVA från Barracuda-licensen, se [Barracuda s CLOUDGEN WAN-sida](https://www.barracuda.com/products/cloudgenwan). Cisco erbjuder för närvarande endast BYOL (ta med din egen licens) som måste införskaffas direkt från Cisco. Dessutom kommer du också att debiteras från Microsoft för de NVA-infrastruktur enheter du använder, och andra resurser som du använder. Mer information finns i [prissättnings begrepp](pricing-concepts.md).

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Kan jag distribuera en NVA till en grundläggande hubb?

Nej. Du måste använda ett standard nav om du vill distribuera en NVA.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Kan jag distribuera en NVA till en säker hubb?

Ja. Barracuda CloudGen WAN kan distribueras till ett nav med Azure-brandväggen.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Kan jag ansluta en CPE-enhet på mitt avdelnings kontor till Barracuda CloudGen WAN-NVA i hubben?

Nej. Barracuda CloudGen WAN är bara kompatibelt med Barracuda CPE-enheter. Mer information om CloudGen WAN-krav finns i [Barracuda s CLOUDGEN WAN-sida](https://www.barracuda.com/products/cloudgenwan). För Cisco finns det ett flertal SD-WAN CPE-enheter som är kompatibla. Se [Cisco Cloud bearbetar för enhetsplattformar för flera moln](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) för att kunna använda CPEs.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Vilka routningsgränssnitt stöds med NVA i hubben?

Alla routnings scenarier som stöds av Virtual WAN stöds med NVA i hubben.

## <a name="next-steps"></a>Nästa steg

Mer information om virtuellt WAN finns i artikeln [Översikt över virtuella WAN-nätverk](virtual-wan-about.md) .
