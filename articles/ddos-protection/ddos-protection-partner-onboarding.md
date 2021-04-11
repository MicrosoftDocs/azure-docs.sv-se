---
title: Samar beta med Azure DDoS Protection standard
description: Förstå samarbets möjligheter som är aktiverade i Azure DDoS Protection standard.
ms.service: ddos-protection
documentationcenter: na
author: aletheatoh
mms.devlang: na
ms.topic: how-to
ms.date: 08/28/2020
ms.author: kumud
ms.openlocfilehash: 625f77a6d9dbeaf612df884434ab39b943587791
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103206"
---
# <a name="partnering-with-azure-ddos-protection-standard"></a>Samar beta med Azure DDoS Protection standard
I den här artikeln beskrivs de partner möjligheter som är aktiverade i Azure DDoS Protection standard. Den här artikeln är utformad för att hjälpa produkt chefer och affärs utvecklings roller att förstå investerings Sök vägarna och tillhandahålla inblick i de sammanställnings värdena.

## <a name="background"></a>Bakgrund
DDoS-attacker (distributed denial of Service) är ett av de största tillgänglighets-och säkerhets frågor som kunderna kan flytta sina program till molnet. Med extortion och hacktivism är det vanliga motivet bakom DDoS-attacker, men de har konsekvent ökande typ, skalning och frekvens av händelser eftersom de är relativt enkla och billiga att starta.

Azure DDoS Protection ger motåtgärder mot de mest sofistikerade DDoS-hoten, vilket utnyttjar den globala skalningen av Azure-nätverk. Tjänsten tillhandahåller förbättrade funktioner för DDoS-minskning för program och resurser som distribueras i virtuella nätverk.

Teknik partner kan skydda sina kunders resurser internt med Azure DDoS Protection standard för att lösa tillgänglighets-och Tillförlitlighets problem på grund av DDoS-attacker.

## <a name="introduction-to-azure-ddos-protection-standard"></a>Introduktion till Azure DDoS Protection standard
Azure DDoS Protection standard ger förbättrade funktioner för DDoS-minskning mot Layer 3-och Layer 4 DDoS-attacker. Följande är viktiga funktioner i DDoS Protection standard tjänsten.

### <a name="adaptive-real-time-tuning"></a>Anpassad real tids justering
För varje skyddat program justerar Azure DDoS Protection standard automatiskt tröskelvärdena för begränsning av DDoS-tjänsten baserat på programmets trafik profil mönster. Tjänsten utför den här anpassningen genom att använda två insikter:

- Automatisk inlärning av trafik mönster per kund (per IP) för Layer 3 och 4.
- Minimera antalet falska positiva identifieringar, med hänsyn till att Azures skala kan absorbera en betydande mängd trafik.

![Anpassad real tids justering](./media/ddos-protection-partner-onboarding/real-time-tuning.png)

### <a name="attack-analytics-telemetry-monitoring-and-alerting"></a>Attack analys, telemetri, övervakning och aviseringar
Azure DDoS Protection identifierar och minimerar DDoS-attacker utan att användaren behöver vidta några åtgärder.

- Om den skyddade resursen finns i prenumerationen som omfattas Azure Security Center, skickar DDoS Protection standard automatiskt en avisering till Security Center när en DDoS-attack identifieras och minimeras mot det skyddade programmet.
- Om du vill få ett meddelande när det finns en aktiv skydds åtgärd för en skyddad offentlig IP-adress kan du [Konfigurera en avisering](alerts.md) på måttet under DDoS-attack eller inte.
- Du kan också välja att skapa aviseringar för de andra DDoS-måtten och [Konfigurera angrepps telemetri](telemetry.md) för att förstå hur stor attacken är, vilken trafik som släpps, angrepps vektorer, förstklassiga deltagare och annan information.

![DDoS mått](./media/ddos-protection-partner-onboarding/ddos-metrics.png)

### <a name="ddos-rapid-response-drr"></a>DDoS Rapid Response (DRR)
DDoS Protection standard kunder har till gång till [snabba svars grupper](ddos-rapid-response.md) under en aktiv attack. DRR kan hjälpa till med angrepps undersökningen under en attack och analys efter angrepp.

### <a name="sla-guarantee-and-cost-protection"></a>SLA-garanti och kostnads skydd
DDoS Protection standard tjänsten omfattas av ett service avtal på 99,99% och kostnads skydd ger resurs krediter för att skala ut under en dokumenterad attack. Mer information finns i [SLA för Azure DDoS Protection](https://azure.microsoft.com/support/legal/sla/ddos-protection/v1_0/).

## <a name="featured-partner-scenarios"></a>Scenarier med aktuella partner
Följande är viktiga fördelar som du kan härleda genom att integrera med Azure DDoS Protection standard:

- Partners som erbjuds tjänster (belastningsutjämnare, brand vägg för webb program, brand vägg osv.) till sina kunder skyddas automatiskt (vita etiketterade) genom Azure DDoS Protection standard i Server delen.
- Partner har åtkomst till Azure DDoS Protection standard attack analys och telemetri som de kan integrera med sina egna produkter, vilket ger en enhetlig kund upplevelse.  
- Partner har åtkomst till DDoS Rapid Response support även om Azure Rapid Response saknas, för DDoS-relaterade problem.
- Partners skyddade program backas upp av en DDoS SLA-garanti och ett kostnads skydd i händelse av DDoS-attacker.

## <a name="technical-integration-overview"></a>Teknisk integrering – översikt
Azure DDoS Protection standard möjligheter för samarbets partner görs tillgängliga via Azure Portal, API: er och CLI/PS.

### <a name="integrate-with-ddos-protection-standard"></a>Integrera med DDoS Protection standard
Följande steg krävs för partner för att konfigurera integration med Azure DDoS Protection standard:
1. Skapa en DDoS Protection Plan i din önskade (partner) prenumeration. Stegvisa instruktioner finns i [skapa en DDoS standard skydds plan](manage-ddos-protection.md#create-a-ddos-protection-plan).
   > [!NOTE]
   > Endast 1 DDoS Protection-plan måste skapas för en specifik klient. 
2. Distribuera en tjänst med en offentlig slut punkt i dina (partner) prenumerationer, till exempel belastningsutjämnare, brand väggar och brand vägg för webbaserade program. 
3. Aktivera Azure DDoS Protection standard på det virtuella nätverket för den tjänst som har offentliga slut punkter som använder DDoS Protection plan som skapats i det första steget. Stpe-instruktioner finns i [Aktivera DDoS standard Protection Plan](manage-ddos-protection.md#enable-ddos-protection-for-an-existing-virtual-network)
   > [!IMPORTANT] 
   > När Azure DDoS Protection standard har Aktiver ATS i ett virtuellt nätverk skyddas alla offentliga IP-adresser i det virtuella nätverket automatiskt. Ursprunget för dessa offentliga IP-adresser kan antingen vara inom Azure (klient prenumerationen) eller utanför Azure. 
4. Du kan också integrera Azure DDoS Protection standard telemetri och angrepps analys på den programspecifika kund instrument panelen. Mer information om hur du använder telemetri finns i [Visa och konfigurera DDoS Protection-telemetri](telemetry.md). 

### <a name="onboarding-guides-and-technical-documentation"></a>Registrerings guider och teknisk dokumentation

- [Sidan Azure DDoS Protection produkt](https://azure.microsoft.com/services/ddos-protection/)
- [Azure DDoS Protection dokumentation](ddos-protection-overview.md)
- [Azure DDoS Protection API-referens](/rest/api/virtualnetwork/ddosprotectionplans)
- [API-referens för Azure Virtual Network](/rest/api/virtualnetwork/virtualnetworks)

### <a name="get-help"></a>Få hjälp

- Om du har frågor om program, tjänster eller produkt integreringar med Azure DDoS Protection standard kan du kontakta [Azures säkerhets community](https://techcommunity.microsoft.com/t5/security-identity/bd-p/Azure-Security).
- Följ diskussioner på [Stack Overflow](https://stackoverflow.com/tags/azure-ddos/).

### <a name="get-to-market"></a>Kom till marknaden

- Det främsta programmet för att samar beta med Microsoft är [Microsoft Partner Network](https://partner.microsoft.com/). – Microsoft Graph säkerhets integreringar i [ISV-spåret (MPN Independent Software Vendor)](https://partner.microsoft.com/saas-solution-guide) .
- [Microsoft Intelligent Security Association](https://www.microsoft.com/security/business/intelligent-security-association?rtc=1) är det program som är specifikt för Microsofts säkerhets partner för att hjälpa till att utöka dina säkerhets produkter och förbättra kund identifieringen av integreringarna med Microsofts säkerhets produkter.

## <a name="next-steps"></a>Nästa steg
Visa befintliga partner integrationer:

- [Barracuda WAF-as-a-service](https://www.barracuda.com/waf-as-a-service)
- [Azure Cloud WAF från Radware](https://www.radware.com/resources/microsoft-azure/)
