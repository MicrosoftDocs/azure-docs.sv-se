---
title: Översikt över Azure Firewall-loggar och-mått
description: Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 8922e6e0d5137a3a900e0f57f685d449c08b3f47
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596953"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Firewall-loggar och mått

Du kan övervaka Azure Firewall med hjälp av brandväggsloggarna. Du kan också använda aktivitetsloggar till att granska åtgärder som utförs på Azure Firewall-resurser.

Du kan komma åt vissa av de här loggarna via portalen. Loggar kan skickas till [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md), lagring och Event Hubs och analyseras i Azure Monitor loggar eller av olika verktyg som Excel och Power BI.

Måtten är lätta att använda och kan ge stöd för scenarier i nästan real tid, vilket gör dem användbara för varningar och snabb problem identifiering.

## <a name="diagnostic-logs"></a>Diagnostikloggar

 Följande diagnostiska loggar är tillgängliga för Azure Firewall:

* **Programregelloggen**

   Program regel loggen sparas till ett lagrings konto, strömmas till händelse nav och/eller skickas till Azure Monitor loggar endast om du har aktiverat det för varje Azure-brandvägg. Varje ny anslutning som matchar en av de konfigurerade programreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, som du ser i följande exempel:

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

   ```json
   {
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": {
         "msg": "HTTPS request from 10.11.2.4:53344 to www.bing.com:443. Action: Allow. Rule Collection: ExampleRuleCollection. Rule: ExampleRule. Web Category: SearchEnginesAndPortals"
     }
   }
   ```

* **Nätverksregellogg**

   Nätverks regel loggen sparas till ett lagrings konto, strömmas till händelse nav och/eller skickas till Azure Monitor loggar endast om du har aktiverat det för varje Azure-brandvägg. Varje ny anslutning som matchar en av de konfigurerade nätverksreglerna genererar en loggpost för den accepterade eller nekade anslutningen. Data loggas i JSON-format, här är ett exempel:

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

* **DNS-proxy-logg**

   DNS-proxykonfigurationen sparas till ett lagrings konto, strömmas till händelse nav och/eller skickas till Azure Monitor loggar endast om du har aktiverat det för varje Azure-brandvägg. Den här loggen spårar DNS-meddelanden till en DNS-server som är konfigurerad med DNS-proxy. Data loggas i JSON-format, som du ser i följande exempel:


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   Resultatet
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   Bröt

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   MSG-format:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

Du har tre alternativ för att lagra dina loggar:

* **Storage-konto**: Storage-konton passar bäst när loggarna ska lagras en längre tid och granskas vid behov.
* **Händelsehubbar**: Händelsehubbar är ett bra alternativ vid integrering med andra verktyg för säkerhetsinformation och händelsehantering (SEIM), när du vill få aviseringar om dina resurser.
* **Azure Monitor loggar**: Azure Monitor loggar används bäst för allmän övervakning i real tid av ditt program eller tittar på trender.

## <a name="activity-logs"></a>Aktivitetsloggar

   Aktivitetsloggposter samlas in som standard, och du kan visa dem i Azure Portal.

   Du kan använda [Azure aktivitets loggar](../azure-resource-manager/management/view-activity-logs.md) (tidigare kallade drift loggar och gransknings loggar) för att visa alla åtgärder som skickats till din Azure-prenumeration.

## <a name="metrics"></a>Mått

Mått i Azure Monitor är numeriska värden som beskriver viss aspekt av ett system vid en viss tidpunkt. Mått samlas in varje minut och är användbara för aviseringar eftersom de kan samplas ofta. En avisering kan utlösas snabbt med relativt enkel logik.

Följande mått är tillgängliga för Azure-brand väggen:

- **Antal träffar för program regler** – antalet gånger som en program regel har nåtts.

    Enhet: antal

- **Antal träffar för nätverks regler** – antalet gånger som en nätverks regel har nåtts.

    Enhet: antal

- **Bearbetade data** – summan av data som passerar brand väggen i en bestämd tids period.

    Enhet: byte

- Data **flödes** frekvens för data som passerar brand väggen per sekund.

    Enhet: bitar per sekund

- **Hälso tillstånd för brand vägg** – anger hälso tillståndet för brand väggen baserat på SNAT-portens tillgänglighet.

    Enhet: procent

   Det här måttet har två dimensioner:
  - Status: möjliga värden är *felfria*, *degraderade*, *felaktiga*.
  - Orsak: anger orsaken till brand väggens aktuella status. 

     Om SNAT-portar används > 95% anses de vara uttömda och hälsan är 50% med status =**degraderad** och orsak =**SNAT-port**. Brandväggen fortsätter att bearbeta trafik och befintliga anslutningar påverkas inte. Nya anslutningar kanske däremot inte upprättas tillfälligt.

     Om SNAT-portar används < 95%, anses brand väggen vara felfri och hälsa visas som 100%.

     Om ingen SNAT-portanvändning visas är hälsan som 0 %. 

- **SNAT-port användning** – procent andelen SNAT-portar som används av brand väggen.

    Enhet: procent

   När du lägger till fler offentliga IP-adresser i brandväggen blir fler SNAT-portar tillgängliga, vilket minskar SNAT-portarnas användningsgrad. När brandväggen skalas ut av olika anledningar (som processoranvändningen eller dataflödet) blir fler SNAT-portar också tillgängliga. I praktiken kan en viss procent andel av SNAT-portarna gå nedåt utan att du behöver lägga till några offentliga IP-adresser, bara för att tjänsten ska skalas ut. Du kan styra antalet offentliga IP-adresser som är tillgängliga för att öka portarna i brand väggen direkt. Men du kan inte styra skalning av brand väggen direkt.


## <a name="next-steps"></a>Nästa steg

- Information om hur du övervakar Azure Firewall-loggar och-mått finns i [självstudie: övervaka Azure Firewall-loggar](./firewall-diagnostics.md).

- Mer information om mått i Azure Monitor finns i [mått i Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md).