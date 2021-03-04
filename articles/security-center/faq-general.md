---
title: Azure Security Center vanliga frågor och svar – allmänna frågor
description: Vanliga frågor och svar om Azure Security Center, en produkt som hjälper dig att förhindra, upptäcka och reagera på hot
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 3a8429d9dc6820b1f79c49d325872b61833f988d
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095552"
---
# <a name="faq---general-questions-about-azure-security-center"></a>Vanliga frågor och svar – allmänna frågor om Azure Security Center

## <a name="what-is-azure-security-center"></a>Vad är Azure Security Center?
Azure Security Center hjälper dig att förhindra, identifiera och svara på hot med ökad insyn i och kontroll över säkerheten för dina resurser. Härifrån kan du övervaka och hantera principer för alla prenumerationer på en gång och upptäcka hot som annars kanske skulle förbli oupptäckta. Azure Security Center fungerar tillsammans med ett vittomfattande ekosystem med säkerhetslösningar.

Security Center använder Log Analytics-agenten för att samla in och lagra data. Detaljerad information finns i [data insamling i Azure Security Center](security-center-enable-data-collection.md).


## <a name="how-do-i-get-azure-security-center"></a>Hur gör jag för att hämta Azure Security Center?
Azure Security Center aktive ras med din Microsoft Azure-prenumeration och nås från [Azure Portal](https://azure.microsoft.com/features/azure-portal/). För att få åtkomst till den [loggar du in på portalen](https://portal.azure.com), väljer **Bläddra** och bläddrar till **Security Center**.


## <a name="which-azure-resources-are-monitored-by-azure-security-center"></a>Vilka Azure-resurser övervakas av Azure Security Center?
Azure Security Center övervakar följande Azure-resurser:

* Virtuella datorer (VM) (inklusive [Cloud Services](../cloud-services/cloud-services-choose-me.md))
* Skalningsuppsättningar för virtuella datorer
* Partner lösningar som är integrerade med din Azure-prenumeration, till exempel en brand vägg för webbaserade program på virtuella datorer och på App Service-miljön
* [De många Azure PaaS-tjänster som visas i produkt översikten](features-paas.md)


## <a name="how-can-i-see-the-current-security-state-of-my-azure-resources"></a>Hur kan jag se det aktuella säkerhets läget för mina Azure-resurser?
På sidan **Security Center översikt** visas den övergripande säkerhets position i miljön, uppdelad efter beräkning, nätverk, lagring & data och program. Varje resurs typ har en indikator som visar identifierade säkerhets problem. Om du klickar på varje panel visas en lista med säkerhets problem som identifieras av Security Center, tillsammans med en inventering av resurserna i din prenumeration.



## <a name="what-is-a-security-initiative"></a>Vad är ett säkerhets initiativ?
Ett säkerhets initiativ definierar den uppsättning kontroller (principer) som rekommenderas för resurser i den angivna prenumerationen. I Azure Security Center tilldelar du initiativ för dina Azure-prenumerationer enligt företagets säkerhets krav och typ av program eller känslighet för data i varje prenumeration.

Säkerhets principerna som är aktiverade i Azure Security Center säkerhets rekommendationer och övervakning. Läs mer i [Vad är säkerhets principer, initiativ och rekommendationer?](security-policy-concept.md).


## <a name="who-can-modify-a-security-policy"></a>Vem kan ändra en säkerhets princip?
Om du vill ändra en säkerhets princip måste du vara **säkerhets administratör** eller **ägare** till den prenumerationen.

Information om hur du konfigurerar en säkerhets princip finns [i ställa in säkerhets principer i Azure Security Center](tutorial-security-policy.md).


## <a name="what-is-a-security-recommendation"></a>Vad är en säkerhets rekommendation?
Azure Security Center analyserar säkerhetstillståndet hos dina Azure-resurser. När potentiella säkerhets risker identifieras skapas rekommendationer. Rekommendationerna vägleder dig genom processen med att konfigurera kontrollen som krävs. Några exempel:

* Etablering av program mot skadlig kod för att identifiera och ta bort skadlig program vara
* [Nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md) och regler för att styra trafik till virtuella datorer
* Etablering av en brand vägg för webbaserade program för att skydda mot attacker som riktar sig mot dina webb program
* genomföra alla systemuppdateringar som fattas
* se till att operativsystemen är konfigurerade enligt rekommenderade baslinjer

Endast rekommendationer som är aktiverade i säkerhets principer visas här.


## <a name="what-triggers-a-security-alert"></a>Vad utlöser en säkerhets avisering?
Azure Security Center samlar automatiskt in, analyserar och säkrar loggdata från dina Azure-resurser, nätverket och partner lösningar som program mot skadlig kod och brand väggar. Om hot upptäcks skapas en säkerhetsavisering. Exempel på hot:

* angripna virtuella datorer som kommunicerar med IP-adresser som man vet är skadliga
* Avancerad skadlig kod har identifierats med Windows fel rapportering
* nyckelsökningsangrepp mot virtuella datorer
* Säkerhets aviseringar från integrerade partner säkerhets lösningar som program mot skadlig kod eller brand väggar för webb program


## <a name="whats-the-difference-between-threats-detected-and-alerted-on-by-microsoft-security-response-center-versus-azure-security-center"></a>Vad är skillnaden mellan hot som upptäckts och aviserats av Microsoft Security Response Center jämfört med Azure Security Center?
Microsoft Security Response Center (MSRC) gör det genom att välja säkerhets övervakning av Azure-nätverket och-infrastrukturen och ta emot Hot information och missbruk från tredje part. När MSRC blir medveten om att kunddata har kunnat nås av en olagligt eller obehörig part eller att kundens användning av Azure inte uppfyller villkoren för acceptabel användning, meddelar en säkerhets incident ansvarig kunden. Avisering sker vanligt vis genom att skicka ett e-postmeddelande till de säkerhets kontakter som anges i Azure Security Center eller Azure-Prenumerationens ägare om ingen säkerhets kontakt har angetts.

Security Center är en Azure-tjänst som kontinuerligt övervakar kundens Azure-miljö och använder analyser för att automatiskt identifiera en rad potentiellt skadlig aktivitet. Dessa identifieringar visas som säkerhets aviseringar på instrument panelen för Security Center.