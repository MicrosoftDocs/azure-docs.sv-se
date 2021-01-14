---
title: Azure DDoS Protection vanliga vanliga frågor och svar
description: Vanliga frågor och svar om Azure DDoS Protection standard, vilket ger försvar mot DDoS-attacker.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/28/2020
ms.author: yitoh
ms.openlocfilehash: e87fd8724ab9a967bf3b75579eb0e375a324a7b1
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183106"
---
# <a name="azure-ddos-protection-standard-frequent-asked-questions"></a>Azure DDoS Protection vanliga vanliga frågor och svar

I den här artikeln besvaras vanliga frågor om Azure DDoS Protection standard. 

## <a name="what-is-a-distributed-denial-of-service-ddos-attack"></a>Vad är en DDoS-attack (distributed denial of Service)?
Distribuerad denial of service eller DDoS är en typ av attack där en angripare skickar fler förfrågningar till ett program än vad programmet kan hantera. Den resulterande inverkan är resurser som släpps, vilket påverkar programmets tillgänglighet och förmåga att betjäna sina kunder. Under de senaste åren har branschen sett en skarp ökning av attacker, med attacker som blir mer sofistikerade och större. DDoS-attacker kan riktas mot vilken slut punkt som helst som är offentligt tillgänglig via Internet.

## <a name="what-is-azure-ddos-protection-standard-service"></a>Vad är Azure DDoS Protection standard tjänsten?
Azure DDoS Protection standard, kombinerat med bästa praxis för program design, innehåller förbättrade DDoS-funktioner för att skydda mot DDoS-attacker. Den justeras automatiskt för att hjälpa till att skydda dina Azure-resurser i ett virtuellt nätverk. Det är enkelt att aktivera skydd på nya eller befintliga virtuella nätverk och det krävs inga program-eller resurs ändringar. Det har flera fördelar jämfört med den grundläggande tjänsten, inklusive loggning, avisering och telemetri. Mer information finns i [Azure DDoS Protection standard översikt](ddos-protection-overview.md) . 

## <a name="how-does-pricing-work"></a>Hur fungerar prissättningen?
DDoS skydds planer har en fast månads avgift på $2 944 per månad som omfattar upp till 100 offentliga IP-adresser. Skydd för ytterligare resurser kostar ytterligare $30 per resurs per månad. 

Under en klient kan en enda DDoS skydds plan användas över flera prenumerationer, så det finns inget behov av att skapa mer än en DDoS-skydds plan.

Se [Azure DDoS Protection standard prissättning](https://azure.microsoft.com/pricing/details/ddos-protection/) för mer information.

## <a name="what-about-protection-at-the-service-layer-layer-7"></a>Vad gäller skydd på tjänst skiktet (Layer 7)?
Kunder kan använda Azure DDoS Protection tjänst i kombination med en brand vägg för webbaserade program (WAF) för att skydda både på nätverks nivå (Layer 3 och 4, som erbjuds av Azure DDoS Protection standard) och på program nivå (Layer 7, som erbjuds av en WAF). WAF-erbjudanden omfattar Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) samt WebApplication-brandväggar från tredje part som är tillgängliga på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="are-services-unsafe-in-azure-without-the-service"></a>Är tjänsterna osäkra i Azure utan tjänsten?
Tjänster som körs på Azure skyddas av Azure DDoS Protection Basic som är på plats för att skydda Azures infrastruktur. Skyddet som skyddar infrastrukturen har dock ett mycket högre tröskelvärde än de flesta program har kapacitet att hantera och ger inte telemetri eller avisering, så medan en trafik volym kan uppfattas som oskadlig av plattformen, kan den vara förödande till programmet som tar emot den. 

Genom att integrera med Azure DDoS Protection standard tjänsten får programmet särskild övervakning för att identifiera attacker och programspecifika tröskelvärden. En tjänst skyddas med en profil som är justerad till den förväntade trafik volymen, vilket ger ett mycket tätt skydd mot DDoS-attacker.

## <a name="what-are-the-supported-protected-resource-types"></a>Vilka är de skyddade resurs typerna som stöds?
Offentliga IP-adresser i ARM-baserade virtuella nätverk är för närvarande den enda typen av skyddad resurs. PaaS Services (Multiklient) stöds inte vid uppvisad. Mer information finns i [Azure DDoS Protection standard referens arkitekturer](ddos-protection-reference-architectures.md) .

## <a name="are-classicrdfe-protected-resources-supported"></a>Stöds klassiska/RDFE-skyddade resurser?
Endast ARM-baserade skyddade resurser stöds i för hands versionen. Virtuella datorer i klassiska/RDFE-distributioner stöds inte. Stöd är för närvarande inte planerat för klassiska/RDFE-resurser. Mer information finns i [Azure DDoS Protection standard referens arkitekturer](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-paas-resources-using-ddos-protection"></a>Kan jag skydda mina PaaS-resurser med hjälp av DDoS Protection?
Offentliga IP-adresser som är kopplade till flera klienter, enkla VIP PaaS-tjänster stöds inte för närvarande. Exempel på resurser som inte stöds är lagrings-VIP, Event Hub VIP-och App/Cloud Services-program. Mer information finns i [Azure DDoS Protection standard referens arkitekturer](ddos-protection-reference-architectures.md) .

## <a name="can-i-protect-my-on-premise-resources-using-ddos-protection"></a>Kan jag skydda lokala resurser med DDoS Protection?
Du måste ha de offentliga slut punkterna för din tjänst som är kopplade till ett VNet i Azure aktiverade för DDoS-skydd. Exempel design är:
- Web Sites (IaaS) i Azure-och backend-databaser i Data Center på lokal. 
- Application Gateway i Azure (DDoS-skydd aktiverat på App Gateway/WAF) och webbplatser i lokal data Center.

Mer information finns i [Azure DDoS Protection standard referens arkitekturer](ddos-protection-reference-architectures.md) .

## <a name="can-i-register-a-domain-outside-of-azure-and-associate-that-to-a-protected-resource-like-vm-or-elb"></a>Kan jag registrera en domän utanför Azure och koppla den till en skyddad resurs som VM eller ELB?
För offentliga IP-scenarier stöder DDoS Protection tjänsten alla program oavsett var den associerade domänen är registrerad eller värdbaserad så länge som den associerade offentliga IP-adressen finns på Azure. 

## <a name="can-i-manually-configure-the-ddos-policy-applied-to-the-vnetspublic-ips"></a>Kan jag manuellt konfigurera DDoS-principen som tillämpas på virtuella nätverk/offentliga IP-adresser?
Nej, princip anpassning är inte tillgänglig just nu.

## <a name="can-i-allowlistblocklist-specific-ip-addresses"></a>Kan jag tillåten/blockeringslistan vissa IP-adresser?
Nej, tyvärr manuell konfiguration är inte tillgänglig just nu.

## <a name="how-can-i-test-ddos-protection"></a>Hur kan jag testa DDoS Protection?
Se [testa genom simuleringar](test-through-simulations.md).

## <a name="how-long-does-it-take-for-the-metrics-to-load-on-portal"></a>Hur lång tid tar det för måtten att läsas in på portalen?
Måtten bör visas på portalen inom 5 minuter. Om din resurs är utsatt för en attack börjar andra mått visas på portalen inom 5-7 minuter. 
    
