---
title: Vad är Azure Firewall Manager Security partner providers?
description: Lär dig mer om Azure Firewall Managers leverantörer av säkerhets partner
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: victorh
ms.openlocfilehash: 622fde49a31105b2f66a678d3e55d48fabea9487
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105966919"
---
# <a name="what-are-security-partner-providers"></a>Vad är en säkerhetspartnerprovider?

*Leverantörer av säkerhets partner* i Azure Firewall Manager gör att du kan använda dina välkända, mest beSECaaSa,-erbjudanden (service Security as a Service) för att skydda Internet åtkomsten för dina användare.

Med en snabb konfiguration kan du skydda en hubb med en säkerhets partner som stöds och dirigera och filtrera Internet trafik från dina virtuella nätverk (virtuella nätverk) eller filial platser inom en region. Du kan göra detta med automatiserad väg hantering utan att konfigurera och hantera användardefinierade vägar (UDR).

Du kan distribuera skyddade hubbar som kon figurer ATS med valfri säkerhets partner i flera Azure-regioner för att få anslutning och säkerhet för dina användare överallt i världen. Med möjligheten att använda säkerhets partner erbjudandet för Internet-SaaS program trafik och Azure-brandväggen för privat trafik i de skyddade hubbarna kan du nu börja bygga din säkerhets gräns på Azure som är nära dina globalt distribuerade användare och program.

De säkerhets partner som stöds är **Zscaler**, **[Check Point](check-point-overview.md)** och **iboss**.

![Säkerhetspartnerprovidrar](media/trusted-security-partners/trusted-security-partners.png)

## <a name="key-scenarios"></a>Viktiga scenarier

Du kan använda säkerhets partner för att filtrera Internet trafik i följande scenarier:

- Virtual Network (VNet)-till-Internet

   Använd avancerad användar medveten Internet skydd för dina moln arbets belastningar som körs på Azure.

- Förgrening till Internet

   Använd din Azure-anslutning och global distribution för att enkelt lägga till NSaaS-filtrering från tredje part för filial till Internet scenarier. Du kan bygga ditt globala överförings nätverk och säkerhets Edge med Azure Virtual WAN.

Följande scenarier stöds:
- Två säkerhets leverantörer i hubben

   VNet/Branch-to-Internet via en provider för säkerhets partner och den andra trafiken (eker-till-eker, eker-till-gren, förgrening-till-eker) via Azure Firewall.
- Enskild Provider i hubben

   - All trafik (eker-till-ekrar, ekrar-till-gren, förgrening-till-ekrar, VNet/gren-till-Internet) som skyddas av Azure-brandväggen<br>
      eller
   - VNet/förgrening till Internet via providern för säkerhets partner

## <a name="best-practices-for-internet-traffic-filtering-in-secured-virtual-hubs"></a>Metod tips för Internet trafik filtrering i skyddade virtuella hubbar

Internet trafiken omfattar vanligt vis webb trafik. Men det omfattar också trafik som är avsedd för SaaS-program som Microsoft 365 och offentliga Azure PaaS-tjänster som Azure Storage, Azure SQL och så vidare. Följande är rekommendationer för att hantera trafik till dessa tjänster:

### <a name="handling-azure-paas-traffic"></a>Hantera Azure PaaS-trafik
 
- Använd Azure-brandväggen för skydd om din trafik främst består av Azure-PaaS och resurs åtkomsten för dina program kan filtreras med hjälp av IP-adresser, FQDN: er, service märken eller FQDN-taggar.

- Använd en partner lösning från tredje part i dina hubbar om din trafik består av SaaS-programåtkomst, eller om du behöver användar medveten filtrering (till exempel för dina VDI-arbetsbelastningar för Virtual Desktop Infrastructure) eller om du behöver avancerade funktioner för Internet filtrering.

![Alla scenarier för Azure Firewall Manager](media/trusted-security-partners/all-scenarios.png)

## <a name="handling-microsoft-365-traffic"></a>Hantera Microsoft 365 trafik

I scenarier för globalt distribuerade gren platser bör du omdirigera Microsoft 365 trafik direkt till grenen innan du skickar den återstående Internet trafiken till Azures säkrade hubb.

För Microsoft 365 är nätverks fördröjning och prestanda avgörande för en lyckad användar upplevelse. För att uppnå dessa mål kring bästa prestanda och användar upplevelse måste kunderna implementera Microsoft 365 direkt och lokalt Escape innan de överväger att vidarebefordra resten av Internet trafiken via Azure.

[Microsoft 365 principerna för nätverks anslutning](/microsoft-365/enterprise/microsoft-365-network-connectivity-principles) anropas för nyckel Microsoft 365 nätverks anslutningar dirigeras lokalt från användar grenen eller den mobila enheten och direkt via Internet till närmaste Microsoft-nätverks punkt.

Dessutom är Microsoft 365 anslutningar krypterade för sekretess och använder effektiva, patentskyddade protokoll av prestanda skäl. Detta gör det opraktiskt och påverkat för att omfattas av dessa anslutningar till traditionella säkerhets lösningar på nätverks nivå. Av dessa skäl rekommenderar vi starkt att kunderna skickar Microsoft 365 trafik direkt från grenar innan resten av trafiken skickas via Azure. Microsoft har samarbetat med flera SD-WAN-lösningar, som integrerar med Azure och Microsoft 365 och gör det enkelt för kunderna att aktivera Microsoft 365 Direct och lokala Internet-grupp. Mer information finns i [Vad är Azure Virtual WAN?](../virtual-wan/virtual-wan-about.md)

## <a name="next-steps"></a>Nästa steg

[Distribuera ett säkerhets partner erbjudande i en skyddad hubb med hjälp av Azure Firewall Manager](deploy-trusted-security-partner.md).
