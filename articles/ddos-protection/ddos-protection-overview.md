---
title: Azure DDoS Protection standard översikt
description: Lär dig hur Azure DDoS Protection Standard tillhandahåller skydd mot DDOS-attacker när du kombinerar det med bästa praxis för dina program.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 71eada0dcd7f7684e6c6be935bcb44405eb95322
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626214"
---
# <a name="azure-ddos-protection-standard-overview"></a>Översikt över Azure DDoS Protection Standard

Distribuerade överbelastningsattacker (DDoS) är några av de största tillgänglighets- och säkerhetsproblemen för kunder som flyttar sina program till molnet. Ett DDoS-angrepp försöker att belasta ett programs resurser, vilket gör programmet otillgängligt för legitima användare. DDoS-attacker kan riktas mot valfri slutpunkt som kan nås offentligt via Internet.

Varje egenskap i Azure skyddas av Azures infrastruktur DDoS (grundläggande) skydd utan extra kostnad. Skalan och kapaciteten för det globalt distribuerade Azure-nätverket ger skydd mot vanliga attacker på nätverks nivå genom att alltid övervaka trafik övervakning och i real tid. För DDoS Protection Basic krävs inga användar konfigurations-eller program ändringar. DDoS Protection Basic hjälper till att skydda alla Azure-tjänster, inklusive PaaS-tjänster som Azure DNS.

Azure DDoS Protection standard, kombinerat med bästa praxis för program design, innehåller förbättrade DDoS-funktioner för att skydda mot DDoS-attacker. Den justeras automatiskt för att hjälpa till att skydda dina Azure-resurser i ett virtuellt nätverk. Det är enkelt att aktivera skydd på nya eller befintliga virtuella nätverk och det krävs inga program-eller resurs ändringar. Det har flera fördelar jämfört med den grundläggande tjänsten, inklusive loggning, avisering och telemetri. 

![Jämförelse av Azure DDoS Protections tjänst](./media/ddos-protection-overview/ddos-comparison.png)

Azure DDoS Protection lagrar inte kund information.

## <a name="features"></a>Funktioner

- **Inbyggd plattforms integrering:** Inbyggt i Azure. Inkluderar konfiguration via Azure Portal. DDoS Protection standard förstår dina resurser och resurs konfigurationen.
- **Nyckel färdigt skydd:** Förenklad konfiguration skyddar omedelbart alla resurser i ett virtuellt nätverk så snart DDoS Protection standard har Aktiver ATS. Ingen åtgärd eller användar definition krävs. 
- **Övervaka alltid trafik:** Dina program trafik mönster övervakas 24 timmar per dag, 7 dagar i veckan, och letar efter indikatorer för DDoS-attacker. DDoS Protection standard och minimerar risken automatiskt när den har identifierats.
- **Anpassningsbar justering:** Intelligent trafik profilering lär sig programmets trafik över tid och väljer och uppdaterar den profil som är lämplig för din tjänst. Profilen justeras när trafiken förändras över tid.
- **Skydd med flera skikt:** När den distribueras med en brand vägg för webbaserade program (WAF), skyddar DDoS Protection-standarden både på nätverks nivå (Layer 3 och 4, som erbjuds av Azure DDoS Protection standard) och på program nivå (Layer 7, som erbjuds av en WAF). WAF-erbjudanden omfattar Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) samt WebApplication-brandväggar från tredje part som är tillgängliga på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).
- **Omfattande minsknings skala:** Över 60 olika typer av attacker kan begränsas, med global kapacitet, för att skydda mot de största kända DDoS-attacker.
- **Attack analys:** Få detaljerade rapporter i steg om fem minuter under en attack och en fullständig sammanfattning efter att attacken har avslut ATS. Strömmande flödes loggar till [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) eller ett SIEM-system (offline Security information and Event Management) för övervakning i nära real tid under ett angrepp.
- **Angrepps mått:** Sammanfattade mått från varje attack är tillgängliga via Azure Monitor.
- **Attack avisering:** Aviseringar kan konfigureras vid start och stopp av ett angrepp och under angriparens varaktighet, med hjälp av inbyggda angrepps mått. Aviseringar integreras i din operativa program vara, t. ex. Microsoft Azure övervaka loggar, Splunk, Azure Storage, e-post och Azure Portal.
- **DDoS Rapid Response**: engagera DDoS Protection Rapid Response-teamet (DRR) för hjälp med angrepps undersökning och analys. Läs mer i [DDoS Rapid Response](ddos-rapid-response.md).
- **Kostnads garanti:** Ta emot data överföring och programskalbara service krediter för resurs kostnader som uppstår till följd av dokumenterade DDoS-attacker.

## <a name="pricing"></a>Prissättning

DDoS skydds planer har en fast månads avgift på $2 944 per månad som omfattar upp till 100 offentliga IP-adresser. Skydd för ytterligare resurser kostar ytterligare $30 per resurs per månad.

Under en klient kan en enda DDoS skydds plan användas över flera prenumerationer, så det finns inget behov av att skapa mer än en DDoS-skydds plan.

Mer information om Azure DDoS Protection standard priser finns i [Azure DDoS Protection standard prissättning](https://azure.microsoft.com/pricing/details/ddos-protection/).

## <a name="reference-architectures"></a>Referensarkitekturer

DDoS Protection standard är utformad för [tjänster som distribueras i ett virtuellt nätverk](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services). För andra tjänster gäller standard tjänsten för DDoS Protection Basic. Mer information om arkitekturer som stöds finns i [DDoS Protection referens arkitekturer](https://docs.microsoft.com/azure/ddos-protection/ddos-protection-reference-architectures). 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en DDoS Protection Plan](manage-ddos-protection.md)
