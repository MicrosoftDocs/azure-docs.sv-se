---
title: Översikt över Resource Health för Azure Application Gateway
description: Den här artikeln innehåller en översikt över resurs hälso funktionen för Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: 7e30a93f8270cfaf8910130cc1e2633bb80c2b8e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93397180"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Översikt över Resource Health för Azure Application Gateway

[Azure Resource Health](../service-health/resource-health-overview.md) hjälper dig att diagnostisera och lösa problem med Azure-tjänster som påverkar dina resurser. Det informerar dig om dina resursers aktuella och senaste hälso tillstånd. Och ger teknisk support för att hjälpa dig att åtgärda problem.

För Application Gateway är Resource Health beroende av signaler som genereras av gatewayen för att bedöma om det är felfritt eller inte. Om gatewayen inte är felfri analyseras Resource Health ytterligare information för att fastställa orsaken till problemet. Den identifierar också åtgärder som Microsoft vidtar eller vad du kan göra för att åtgärda problemet.

Mer information om hur hälsa utvärderas finns i den fullständiga listan över resurs typer och hälso kontroller i [Azure Resource Health](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways).


Hälso tillståndet för Application Gateway visas som en av följande statusar:

## <a name="available"></a>Tillgänglig

En **tillgänglig** status innebär att tjänsten inte har identifierat några händelser som påverkar resursens hälso tillstånd. Du ser **nyligen löst** meddelande i fall där gatewayen har återställts från oplanerat avbrott under de senaste 24 timmarna.

![Tillgänglig hälso status](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>Inte tillgänglig

En **otillgänglig** status innebär att tjänsten har identifierat en pågående plattforms-eller icke-plattforms händelse som påverkar gatewayens hälso tillstånd.

### <a name="platform-events"></a>Plattformshändelser

Plattforms händelser utlöses av flera komponenter i Azure-infrastrukturen. De omfattar både schemalagda åtgärder (till exempel planerat underhåll) och oväntade incidenter (till exempel en oplanerad värd omstart).

Resource Health ger ytterligare information om händelsen och återställnings processen. Du kan också kontakta supporten även om du inte har ett aktivt Microsoft support avtal.

![Otillgänglig status](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Okänt

**Okänd** hälso status visar Resource Health inte har tagit emot information om gatewayen i mer än 10 minuter. Denna status är inte en slutgiltig angivelse av gatewayens status. Men det är en viktig data punkt i fel söknings processen.

Om gatewayen körs som förväntat ändras statusen till **tillgänglig** efter några minuter.

Om det uppstår problem kan den **okända** hälso statusen föreslå att en händelse i plattformen påverkar gatewayen.

![Okänd status](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>Degraderad

Den **försämrade** hälso statusen anger att din gateway har identifierat prestanda förlust, även om den fortfarande är tillgänglig för användning.

![Förvarad status](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>Nästa steg

Information om hur du felsöker Application Gateway brand vägg för webbaserade program (WAF) finns i [Felsöka brand vägg för webbaserade program (WAF) för Azure Application Gateway](../web-application-firewall/ag/web-application-firewall-troubleshoot.md).