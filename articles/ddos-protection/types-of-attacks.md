---
title: Typer av attacker Azure DDoS Protection standard åtgärder
description: Lär dig vilka typer av attacker Azure DDoS Protection standard skyddar mot.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 8eac365fc5146f315db8f09a67960aa892fa1c85
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106062"
---
# <a name="types-of-ddos-attacks-overview"></a>Översikt över typer av DDoS-attacker

DDoS Protection standard kan minimera följande typer av attacker:

- **Volym attacker**: de här angreppen överbelastar nätverks lagret med stor mängd som verkar vara legitim trafik. De innehåller UDP-översvämmare, förstärknings belastningar och andra falska paket belastningar. DDoS Protection standard minskar risken för angrepp med flera gigabyte genom att absorbera och rensa dem, med Azures globala nätverks skalning automatiskt.
- **Protokoll attacker**: dessa attacker återger ett mål som inte kan nås genom att utnyttja en svaghet i skikt 3-och lager 4-protokollstacken. De omfattar SYN översvämnings attacker, reflektions attacker och andra protokoll attacker. DDoS Protection standard minimerar dessa attacker, skiljer sig från skadlig och legitim trafik, genom att interagera med klienten och blockera skadlig trafik. 
- **Resurs (program) lager attacker**: dessa attacker riktar sig mot webb program paket, för att avbryta överföringen av data mellan värdar. De omfattar HTTP-protokollfel, SQL-inmatning, skript körning över flera webbplatser och andra nivå 7-attacker. Använd en brand vägg för webbaserade program, t. ex. Azure [Application Gateway brand vägg för webbaserade program](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), samt DDoS Protection standard för att ge skydd mot dessa attacker. Det finns även brand Väggs erbjudanden från tredje part som är tillgängliga på [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

DDoS Protection standard skyddar resurser i ett virtuellt nätverk, inklusive offentliga IP-adresser som är kopplade till virtuella datorer, belastningsutjämnare och programgatewayer. När den är kopplad till Application Gateway brand vägg för webbaserade program eller en tredjeparts brand vägg för webbaserade program som distribueras i ett virtuellt nätverk med en offentlig IP-adress, kan DDoS Protection standard tillhandahålla fullständig nivå 3-till-nivå 7-reducering.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en DDoS-skydds plan](manage-ddos-protection.md).