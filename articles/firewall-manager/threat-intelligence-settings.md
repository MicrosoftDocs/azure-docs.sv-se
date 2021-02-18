---
title: Konfiguration av Azure Firewall Threat Intelligence
description: Lär dig hur du konfigurerar Hot information-baserad filtrering för din Azure Firewall-princip för att varna och neka trafik från och till kända skadliga IP-adresser och domäner.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 7ede1c917bb44dd31aa59855a0b7c83eb478700a
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100651734"
---
# <a name="azure-firewall-threat-intelligence-configuration"></a>Konfiguration av Azure Firewall Threat Intelligence

Hot information-baserad filtrering kan konfigureras för din Azure Firewall-princip för att varna och neka trafik från och till kända skadliga IP-adresser och domäner. IP-adresserna och domänerna hämtas från Microsoft Threat Intelligence-flödet. [Intelligent Security graphs](https://www.microsoft.com/security/operations/intelligence) befogenheter Microsoft Threat intelligence och används av flera tjänster, inklusive Azure Security Center.<br>

Om du har konfigurerat Threat Intelligence-baserad filtrering bearbetas de associerade reglerna före någon av NAT-reglerna, nätverks reglerna eller program reglerna.

:::image type="content" source="media/threat-intelligence-settings/threat-intelligence-policy.png" alt-text="Hot informations princip":::

## <a name="threat-intelligence-mode"></a>Hot informations läge

Du kan konfigurera Hot information i något av de tre lägena som beskrivs i följande tabell. Som standard är Hot information-baserad filtrering aktive rad i varnings läge.

|Läge |Description  |
|---------|---------|
|`Off`     | Funktionen Hot information är inte aktive rad för brand väggen. |
|`Alert only`     | Du får aviseringar med hög exakthet för trafik som går genom brand väggen till eller från kända skadliga IP-adresser och domäner. |
|`Alert and deny`     | Trafiken blockeras och du får aviseringar med hög exakthet när trafik upptäcks försöker gå igenom brand väggen till eller från kända skadliga IP-adresser och domäner. |

> [!NOTE]
> Hot informations läget ärvs från överordnade principer till underordnade principer. En underordnad princip måste konfigureras med samma eller striktare läge än den överordnade principen.

## <a name="allowlist-addresses"></a>Tillåten-adresser

Hot information kan utlösa falska positiva identifieringar och blockera trafik som faktiskt är giltig. Du kan konfigurera en lista över tillåtna IP-adresser så att hot information inte filtrerar de adresser, intervall eller undernät som du anger.  

![Tillåten-adresser](media/threat-intelligence-settings/allow-list.png)

Du kan uppdatera tillåten med flera poster samtidigt genom att ladda upp en CSV-fil. CSV-filen får bara innehålla IP-adresser och intervall. Filen får inte innehålla rubriker.

> [!NOTE]
> Hot information tillåten-adresser ärvs från överordnade principer till underordnade principer. Alla IP-adresser eller intervall som läggs till i en överordnad princip gäller även för alla underordnade principer.

## <a name="logs"></a>Loggar

Följande logg utdrag visar en utlöst regel för utgående trafik till en skadlig plats:

```json
{
    "category": "AzureFirewallNetworkRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallThreatIntelLog",
    "properties": {
         "msg": "HTTP request from 10.0.0.5:54074 to somemaliciousdomain.com:80. Action: Alert. ThreatIntel: Bot Networks"
    }
}
```

## <a name="testing"></a>Testning

- **Utgående testning** -utgående trafik aviseringar bör vara en sällsynt förekomst, vilket innebär att din miljö har komprometterats. För att testa utgående aviseringar fungerar, har ett test-FQDN skapats som utlöser en avisering. Använd **testmaliciousdomain.eastus.cloudapp.Azure.com** för dina utgående tester.

- **Inkommande testning** – du kan vänta på att se aviseringar på inkommande trafik om DNAt regler har kon figurer ATS i brand väggen. Detta gäller även om endast vissa källor tillåts i DNAT-regeln och trafik nekas annars. Azure-brandväggen varnas inte för alla kända port skannrar. endast på skannrar som är kända för att även delta i skadlig aktivitet.

## <a name="next-steps"></a>Nästa steg

- Granska [Microsoft Security Intelligence-rapporten](https://www.microsoft.com/en-us/security/operations/security-intelligence-report)
