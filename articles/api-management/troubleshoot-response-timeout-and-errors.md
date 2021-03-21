---
title: Felsöka timeout för klient svar och fel med API Management
description: Felsök tillfälliga anslutnings fel och relaterade latens problem i API Management
author: vladvino
ms.topic: troubleshooting
ms.date: 12/04/2020
ms.author: apimpm
ms.service: api-management
ms.openlocfilehash: 6cace4a02c8d45cacbbc34e9778b5c4a78ada27f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100576520"
---
# <a name="troubleshooting-client-response-timeouts-and-errors-with-api-management"></a>Felsöka timeout för klient svar och fel med API Management

Den här artikeln hjälper dig att felsöka tillfälliga anslutnings fel och relaterade svars tids problem i [Azure API Management](./api-management-key-concepts.md). Mer specifikt ger den här artikeln information och fel sökning för utbelastningen av SNAT-portar (Source Address Network Translation). Om du behöver mer hjälp kan du kontakta Azure-experterna på [Azure community support](https://azure.microsoft.com/support/community/) eller skicka en support förfrågan till [Azure-supporten](https://azure.microsoft.com/support/options/).

## <a name="symptoms"></a>Symtom

Klient program som anropar API: er via din API Management-tjänst (APIM) kan ha ett eller flera av följande problem:

* Tillfälliga HTTP 500-fel
* Fel meddelanden för tids gräns

De här symptomen är manifest som instanser av `BackendConnectionFailure` i [Azure Monitor resurs loggar](../azure-monitor/essentials/resource-logs.md).

## <a name="cause"></a>Orsak

Det här problemet beror ofta på Network Address Translation (SNAT) port gränser med din APIM-tjänst.

När en klient anropar en av dina APIM-API: er öppnar Azure API Management Service en SNAT-port för att få åtkomst till Server dels-API: et Som diskuteras i [utgående anslutningar i Azure](../load-balancer/load-balancer-outbound-connections.md)använder azure käll Network Address TRANSLATION (SNAT) och en Load Balancer (visas inte för kunder) för att kommunicera med slut punkter utanför Azure i det offentliga IP-adressutrymmet, samt slut punkter interna till Azure som inte använder [Virtual Network tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md). Den här situationen gäller endast Server dels-API: er som exponeras på offentliga IP-adresser.

Varje instans av API Managements tjänsten tilldelas ursprungligen ett förallokerat antal SNAT-portar. Den gränsen påverkar öppnande av anslutningar till samma värd-och port kombination. SNAT-portar används när du upprepade samtal till samma adress-och port kombination. När en SNAT-Port har frigjorts är porten tillgänglig för åter användning. Azure Network Load Balancer frigör SNAT-portar från stängda anslutningar endast efter att ha väntat fyra minuter.

En snabb följd av klient begär anden till dina API: er kan överskrida den förallokerade kvoten på SNAT-portar om de här portarna inte är stängda och återvinns tillräckligt snabbt, så att APIM-tjänsten inte kan bearbeta klient förfrågningar inom rimlig tid.

## <a name="mitigations-and-solutions"></a>Begränsningar och lösningar

För att kunna lösa problemet med SNAT-porten måste du först diagnostisera och optimera prestandan för Server dels tjänsterna.

Allmänna strategier för att minska SNAT-portens belastning beskrivs i [Felsöka utgående anslutnings fel](../load-balancer/troubleshoot-outbound-connection.md) från *Azure Load Balancer* -dokumentationen. Av dessa strategier gäller följande för API Management.

### <a name="scale-your-apim-instance"></a>Skala din APIM-instans

Varje API Management instans tilldelas ett antal SNAT-portar, baserat på APIM-enheter. Du kan allokera ytterligare SNAT-portar genom att skala API Management-instansen med ytterligare enheter. Mer information finns i [skala din API Management-tjänst](upgrade-and-scale.md#scale-your-api-management-service)

> [!NOTE]
> SNAT-port användningen är för närvarande inte tillgänglig som mått för autoskalning API Management enheter.

### <a name="use-multiple-ips-for-your-backend-urls"></a>Använd flera IP-adresser för URL: er för Server delen

Varje anslutning från din APIM-instans till samma mål-IP och mål Port för Server dels tjänsten kommer att använda en SNAT-port för att upprätthålla ett distinkt trafikflöde. Utan andra SNAT-portar för retur trafiken från din bakgrunds tjänst har APIM inte något sätt att skilja ett svar från en annan.

Eftersom SNAT-portar kan återanvändas om mål-IP-adressen eller mål porten är olika, är det ett annat sätt att undvika att överbelastningen på SNAT är genom att använda flera IP-adresser för URL: er för Server delen

Mer information finns i [utgående proxy Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md).

### <a name="place-your-apim-and-backend-service-in-the-same-vnet"></a>Placera din APIM-och backend-tjänst i samma VNet

Om Server dels-API: et finns på en Azure-tjänst som stöder *tjänst slut punkter* som app service, kan du undvika problemen med SNAT-portar genom att placera APIM-instansen och backend-tjänsten i samma virtuella nätverk och exponera den via [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md) eller [privata slut punkter](../private-link/private-endpoint-overview.md). När du använder ett gemensamt VNet och placerar tjänst slut punkter i integrations under nätet, kringgår utgående trafik från APIM-instansen till dessa tjänster Internet, så att SNAT-port begränsningar undviks. Om du använder ett virtuellt nätverk och privata slut punkter får du inte heller några utgående SNAT-port problem till det målet.

Mer information finns i [så här använder du Azure API Management med virtuella nätverk](api-management-using-with-vnet.md) och [integrerar app service med ett virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).

### <a name="place-your-apim-in-a-virtual-network-and-route-outbound-calls-to-azure-firewall"></a>Placera din APIM i ett virtuellt nätverk och dirigera utgående samtal till Azure-brandväggen

Precis som att placera dina APIM-och backend-tjänster i ett virtuellt nätverk kan du använda Azure-brandväggen i ett VNet med din APIM-tjänst och sedan dirigera utgående APIM-anrop till Azure-brandväggen. Mellan APIM och Azure-brandväggen (som finns i samma VNet) krävs inga SNAT-portar. För SNAT-anslutningar till Server dels tjänsterna har Azure-brandväggen 64 000 tillgängliga portar, en mycket högre mängd än vad som allokerats till APIM-instanser.

Mer information hittar du i dokumentationen för [Azure Firewall](../firewall/overview.md) .

### <a name="consider-response-caching-and-other-backend-performance-tuning"></a>Överväg cachelagring av svar och annan prestanda justering för Server delen

En annan möjlig minskning är att förbättra bearbetnings tiderna för dina Server dels-API: er. Ett sätt att göra detta är genom att konfigurera vissa API: er med cachelagring av svar för att minska svars tiden mellan klient program som anropar API: et och din APIM-Server

Mer information finns i [lägga till cachelagring för att förbättra prestanda i Azure API Management](api-management-howto-cache.md).

### <a name="consider-implementing-access-restriction-policies"></a>Överväg att implementera principer för begränsning av åtkomst

Om det passar ditt affärs scenario kan du implementera principer för åtkomst begränsning för din API Management-produkt. Principen kan till exempel `rate-limit-by-key` användas för att förhindra att API-användningen upprättar per nyckel genom att begränsa anrops frekvensen per angiven tids period.

Mer information finns i [principer för begränsning av API Management åtkomst](api-management-access-restriction-policies.md) .

## <a name="see-also"></a>Se även

* [Azure Load Balancer: Felsöka fel vid utgående anslutningar](../load-balancer/troubleshoot-outbound-connection.md)
* [Azure App Service: fel sökning av återkommande utgående anslutnings fel](../app-service/troubleshoot-intermittent-outbound-connection-errors.md)
