---
title: 'Azure Virtual WAN: Om virtuell nätverksinstallation i hubben'
description: I den här artikeln får du lära dig mer om virtuella nätverksapparater i Virtual WAN hubben.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: scottnap
ms.openlocfilehash: 7c3ae14cd409e7bfc9be77c1a593964b73a12ddc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791057"
---
# <a name="about-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Om virtuell nätverksinstallation i en Azure Virtual WAN (förhandsversion)

Azure Virtual WAN har arbetat med nätverkspartner för att skapa automatisering som gör det enkelt att ansluta sin kundlokal utrustning (CPE) till en Azure VPN-gateway i den virtuella hubben. Azure samarbetar med utvalda nätverkspartner för att göra det möjligt för kunder att distribuera en virtuell nätverksinstallation (NVA) från tredje part direkt till den virtuella hubben. Detta gör det möjligt för kunder som vill ansluta sin gren-CPE till samma nva-varumärke i den virtuella hubben så att de kan dra nytta av egenutvecklade SD-WAN-funktioner.

Barracuda Networks och Cisco Systems är de första partner som tillhandahåller NVA:er som kan distribueras direkt till Virtual WAN hubben.  Se [Barracuda CloudGen WAN,](https://www.barracuda.com/products/cloudgenwan) [Cisco Cloud OnRamp for Multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) och [VMware SD-WAN](https://kb.vmware.com/s/article/82746) för deras respektive produktdokumentation. Azure samarbetar med fler partner, så förvänta dig att se andra erbjudanden följa.

> [!NOTE]
> Endast NVA-erbjudanden som är tillgängliga för distribution till Virtual WAN-hubben kan distribueras till Virtual WAN hubben. De kan inte distribueras till ett godtyckligt virtuellt nätverk i Azure.

## <a name="how-does-it-work"></a><a name="how"></a>Hur fungerar det?

NVA:erna som kan distribueras direkt Azure Virtual WAN hubben är särskilt utformade för att användas i den virtuella hubben. NVA-erbjudandet publiceras till Azure Marketplace som ett hanterat program och kunder kan distribuera erbjudandet direkt från Azure Marketplace eller distribuera erbjudandet från den virtuella hubben via Azure Portal.

:::image type="content" source="./media/about-nva-hub/high-level-process.png" alt-text="Processöversikt":::

Varje partners NVA-erbjudande har en något annorlunda upplevelse och funktionalitet baserat på deras distributionskrav. Det finns dock vissa saker som är gemensamma för alla partnererbjudanden för NVA i Virtual WAN hubben.

* Ett hanterat program som erbjuds via Azure Marketplace.
* Enhetsbaserad kapacitet och fakturering för NVA-infrastruktur.
* Hälsomått som har Azure Monitor.

### <a name="managed-application"></a><a name="managed"></a>Hanterat program

Alla NVA-erbjudanden som är tillgängliga för distribution till Virtual WAN-hubben har ett hanterat program som är tillgängligt i Azure Marketplace.  Med hanterade program kan partner göra följande:

* Skapa en anpassad distributionsupplevelse för deras NVA.
* Ange en Resource Manager mall som gör att de kan skapa NVA direkt i Virtual WAN hubben.
* Fakturera programvarulicensieringskostnader direkt eller via Azure Marketplace.
* Exponera anpassade egenskaper och resursmätare.

NVA-partner kan skapa olika resurser beroende på deras installationsdistribution, konfigurationslicensiering och hanteringsbehov. När en kund skapar en NVA i Virtual WAN-hubben, precis som alla hanterade program, skapas två resursgrupper i prenumerationen.

* **Kundresursgrupp** – Detta innehåller en programplatshållare för det hanterade programmet. Partner kan använda detta för att exponera de kundegenskaper som de väljer här.
* **Hanterad resursgrupp** – Kunder kan inte konfigurera eller ändra resurser i den här resursgruppen direkt, eftersom detta styrs av utgivaren av det hanterade programmet. Den här resursgruppen innehåller **resursen NetworkVirtualAppliances.**

:::image type="content" source="./media/about-nva-hub/managed-app.png" alt-text="Resursgrupper för hanterade program":::

### <a name="nva-infrastructure-units"></a><a name="units"></a>NVA-infrastrukturenheter

När du skapar en NVA i Virtual WAN-hubben måste du välja det antal NVA-infrastrukturenheter som du vill distribuera den med. En **NVA-infrastrukturenhet** är en enhet med sammanställd bandbreddskapacitet för en NVA i Virtual WAN hubben. En **NVA-infrastrukturenhet** liknar en [VPN-skalningsenhet](pricing-concepts.md#scale-unit) när det gäller hur du tänker på kapacitet och storleksändring.

* 1 NVA-infrastrukturenhet representerar 500 Mbit/s aggregerad bandbredd för alla grenplatsanslutningar som kommer till denna NVA, till en kostnad på 0,25 USD per timme.
* Azure stöder från 1–80 NVA-infrastrukturenheter för en viss distribution av en virtuell NVA-hubb.
* Varje partner kan erbjuda olika nva-infrastrukturenhetspaket som är en delmängd av alla nva-infrastrukturenhetskonfigurationer som stöds.

Om du väljer *1 NVA-infrastrukturenhet = 500* Mbit/s, innebär det att två instanser för redundans skapas, där var och en har ett maximalt dataflöde på 500 Mbit/s. Om du till exempel har fem grenar, där var och en gör 10 Mbit/s på -grenen, behöver du en aggregering på 50 Mbit/s i huvudänden. Planering för sammanställd kapacitet för den virtuella nva-dataenheten bör göras efter utvärdering av den kapacitet som behövs för att stödja antalet grenar till hubben.

## <a name="network-virtual-appliance-configuration-process"></a><a name="configuration"></a>Konfigurationsprocess för virtuell nätverksinstallation

Partner har arbetat med att tillhandahålla en upplevelse som konfigurerar nva automatiskt som en del av distributionsprocessen. När NVA har etablerats i den virtuella hubben måste eventuell ytterligare konfiguration som krävs för NVA göras via NVA-partnerportalen eller hanteringsprogrammet. Direktåtkomst till NVA är inte tillgängligt.

## <a name="site-and-connection-resources-with-nvas"></a><a name="resources"></a>Plats- och anslutningsresurser med NVA:er

Till skillnad från Azure VPN Gateway-konfigurationer behöver  du inte skapa platsresurser, **plats-till-plats-anslutningsresurser** eller **punkt-till-plats-anslutningsresurser** för att ansluta dina grenplatser till din NVA i Virtual WAN-hubben. Allt detta hanteras via NVA-partnern.

Du måste fortfarande skapa hubb-till-VNet-anslutningar för att ansluta din Virtual WAN till dina virtuella Azure-nätverk.

## <a name="supported-regions"></a><a name="regions"></a>Regioner som stöds

NVA i den virtuella hubben är tillgänglig för förhandsversion i följande regioner:

|Geopolitisk region | Azure-regioner|
|---|---|
| Nordamerika| Kanada, centrala, Kanada, östra, USA, centrala, USA, östra, USA, östra 2, USA, norra centrala, USA, västra centrala, USA, västra, USA, västra 2 |
| Sydamerika | Brasilien, södra, Brasilien, sydöstra |
| Europa | Frankrike, centrala, Frankrike, södra, Tyskland, norra, Tyskland, västra centrala, Europa, norra, Östra Tyskland, västra Schweiz, norra, Schweiz, västra, Storbritannien, södra, Storbritannien, västra, Europa, västra|
|  Mellanöstern | Förenade Arabemiraten, norra |
| Asien |  Asien, östra, Japan, östra, Japan, västra, Sydkorea, centrala, Sydkorea, södra Sydostasien | 
| Australien | Australien, sydöstra, Australien, östra, Australien, centrala, Australien, centrala 2|
| Afrika | Sydafrika, norra |
| Indien | Indien, södra, Indien, västra, Indien, centrala | 
## <a name="faq"></a>Vanliga frågor

### <a name="i-am-a-network-appliance-partner-and-want-to-get-our-nva-in-the-hub--can-i-join-this-partner-program"></a>Jag är en nätverksinstallationspartner och vill ha vår NVA i hubben.  Kan jag delta i det här partnerprogrammet?

Tyvärr har vi inte kapacitet att ta med några nya partnererbjudanden just nu. Kom tillbaka till oss i november!

### <a name="can-i-deploy-any-nva-from-azure-marketplace-into-the-virtual-wan-hub"></a>Kan jag distribuera en NVA från Azure Marketplace till Virtual WAN hubben?

För närvarande är det bara [Barracuda CloudGen WAN](https://aka.ms/BarracudaMarketPlaceOffer)  [Cisco Cloud vWAN Application](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cisco.cisco_cloud_vwan_app?tab=Overview) och [VMware Sd-WAN](https://aka.ms/vmwareMarketplaceLink) som kan distribueras till Virtual WAN hubben.

### <a name="what-is-the-cost-of-the-nva"></a>Vad är kostnaden för nva?

Du måste köpa en licens för NVA från NVA-leverantören.  Din Barracuda CloudGen WAN NVA från Barracuda-licens finns [på Barracudas CloudGen WAN-sida.](https://www.barracuda.com/products/cloudgenwan) Cisco erbjuder för närvarande endast licensieringsmodellen BYOL (Bring Your Own License) som behöver köpas direkt från Cisco. Dessutom debiteras du också avgifter från Microsoft för de NVA-infrastrukturenheter som du använder och andra resurser som du använder. Mer information finns i [Prisbegrepp.](pricing-concepts.md)

### <a name="can-i-deploy-an-nva-to-a-basic-hub"></a>Kan jag distribuera en NVA till en Basic-hubb?

Nej. Du måste använda en Standard-hubb om du vill distribuera en NVA.

### <a name="can-i-deploy-an-nva-into-a-secure-hub"></a>Kan jag distribuera en NVA till en säker hubb?

Ja. Partner-NVA:er kan distribueras till en hubb med Azure Firewall.

### <a name="can-i-connect-any-cpe-device-in-my-branch-office-to-barracuda-cloudgen-wan-nva-in-the-hub"></a>Kan jag ansluta valfri CPE-enhet på mitt avdelningskontor till Barracuda CloudGen WAN NVA i hubben?

Nej. Barracuda CloudGen WAN är endast kompatibelt med Barracuda CPE-enheter. Mer information om kraven för CloudGen WAN finns på [Barracudas CloudGen WAN-sida.](https://www.barracuda.com/products/cloudgenwan) För Cisco finns det flera kompatibla SD-WAN CPE-enheter. Information om [kompatierbara CPE:er finns](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701) i Information om Cisco Cloud OnRamp för flera moln.

### <a name="what-routing-scenarios-are-supported-with-nva-in-the-hub"></a>Vilka routningsscenarier stöds med NVA i hubben?

Alla routningsscenarier som Virtual WAN stöds med NVA:er i hubben.

## <a name="next-steps"></a>Nästa steg

Mer information om Virtual WAN finns i artikeln [Virtual WAN Översikt.](virtual-wan-about.md)
