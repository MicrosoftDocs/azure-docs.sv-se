---
title: Vanliga frågor och svar om prissättning för Azure Övervakare av nätverksprestanda | Microsoft Docs
description: Vanliga frågor och svar – Azure Övervakare av nätverksprestanda
ms.topic: conceptual
author: agummadi
ms.author: agummadi
ms.date: 04/02/2018
ms.openlocfilehash: 56163d3267a69099cbf6a18266168e9198ea04c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101717442"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Pris ändringar för Azure Övervakare av nätverksprestanda

> [!IMPORTANT]
> Från och med 1 juli 2021 kommer du inte att kunna lägga till nya tester på en befintlig arbets yta eller aktivera en ny arbets yta i Övervakare av nätverksprestanda. Du kan fortsätta att använda testerna som skapats före den 1 juli 2021. [Migrera dina tester från övervakare av nätverksprestanda till den nya anslutnings övervakaren](../../network-watcher/migrate-to-connection-monitor-from-network-performance-monitor.md) i Azure Network Watcher före den 29 februari 2024 för att minimera avbrott i tjänsten för dina aktuella arbets belastningar.

Vi har lyssnat på din feedback och nyligen introducerade en [ny prissättnings upplevelse](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) för olika övervaknings tjänster i Azure. Den här artikeln samlar in pris ändringar som rör Azure [övervakare av nätverksprestanda](../../networking/network-monitoring-overview.md) (NPM) i ett lättläst fråge-och svars format.

Övervakare av nätverksprestanda består av tre komponenter:
* [Prestandaövervakaren](../../networking/network-monitoring-overview.md#performance-monitor)
* [Övervakare av tjänst slut punkt](../../networking/network-monitoring-overview.md)
* [Övervakaren ExpressRoute](../../networking/network-monitoring-overview.md#expressroute-monitor)

I följande avsnitt förklaras pris ändringarna för NPM-komponenterna.

## <a name="performance-monitor"></a>Prestandaövervakaren

**Hur fakturerades användningen av prestanda övervakaren i den gamla modellen?**

Faktureringen för NPM baseras på användning och förbrukning av två komponenter:
* **Noder**: alla syntetiska transaktioner kommer från och avslutas på noderna. Noder kallas även agenter eller Microsofts hanterings agenter.
* **Data**: resultaten av de olika nätverks testerna lagras i arbets ytan Log Analytics.

Under den gamla modellen beräknades fakturan baserat på antalet noder och den data volym som genererades. 

**Hur debiteras användningen av prestanda övervakaren under den nya modellen?**

Prestanda övervaknings funktionen i NPM faktureras nu baserat på en kombination av: 

* Övervakade under näts länkar
* Datavolym

**Vad är en under näts länk?**

Prestanda övervakaren övervakar anslutningen mellan två eller flera platser i nätverket. Anslutningen mellan en grupp noder eller agenter i ett undernät, och en grupp noder i ett annat undernät, kallas för en under näts länk.

**Jag har två undernät (A och B) och jag har flera agenter på varje undernät. Prestanda övervakaren övervakar anslutningar från alla agenter på undernät A till alla agenter på undernät B. Kommer jag att debiteras baserat på antalet anslutningar mellan undernät?**

Nej. För fakturerings syfte grupperas alla anslutningar från undernät A till undernät B samman i en under näts länk. Du faktureras för en enda anslutning. Prestanda Övervakaren fortsätter att övervaka anslutningen mellan olika agenter på varje undernät.

**Vilka är kostnaderna för att övervaka en under näts länk?**

För kostnaden för att övervaka en enskild under näts länk för hela månaden, se avsnittet [ping-nät](https://azure.microsoft.com/pricing/details/network-watcher/) .

**Vilka avgifter används för data som skapas i prestanda övervakaren?**

Avgiften för inmatning (överföring av data till Log Analytics arbets yta i Azure Monitor, bearbetning och indexering) finns på [sidan prissättning](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics i avsnittet data inmatning. Avgiften för datakvarhållning (det vill säga data som behålls på kundens alternativ, utöver den första månaden) är också tillgängliga på [sidan prissättning](https://azure.microsoft.com/pricing/details/log-analytics/)i avsnittet data kvarhållning.


## <a name="expressroute-monitor"></a>Övervakaren ExpressRoute

**Vilka är avgifterna för användning av ExpressRoute-övervakaren?**

Avgifter för ExpressRoute-övervakaren faktureras baserat på den data volym som genereras under övervakningen. Mer information finns i "Vad är avgifterna för data som prestanda övervakaren genererar?"

**Jag använder ExpressRoute Monitor för att övervaka flera ExpressRoute-kretsar. Debiteras jag baserat på antalet kretsar som övervakas?**

Du debiteras inte utifrån antingen antalet kretsar eller typen av peering (till exempel privat peering, Microsoft-peering). Du debiteras baserat på mängden data som förklaras tidigare.

**Vilken volym av data genereras när ExpressRoute övervakar en enda krets?**

Den data volym som genereras per månad, när ExpressRoute övervakar en privat peering-anslutning, är följande:

|Percentil      |Data/månad (MB)|
| :---:          |           ---:|
|50<sup></sup> |            192|
|60<sup></sup> |            256|
|70<sup></sup> |            360|
|80<sup></sup> |            498|
|90<sup></sup> |            870|
|95<sup></sup> |           1560|


Enligt den här tabellen betalar kunderna vid den 50: e percentilen för 192 MB data. Per USD $2.30/GB för den första månaden är kostnaden för övervakning av en krets USD $0,43 (= 192 * 2,30/1024).

**Vad är några orsaker till variationer i data mängden?**

Mängden övervaknings data som genereras beror på flera faktorer, till exempel:
* Antal agenter. Fel isoleringens exakthet ökar med en ökning av antalet agenter.
* Antal hopp i nätverket.
* Antalet sökvägar mellan källan och målet.

Kunder som har högre percentiler (i föregående tabell) övervakar vanligt vis sina kretsar från flera Vantage punkter i det lokala nätverket. Flera agenter placeras också djupare i nätverket, längre bort från tjänst leverantörens Edge-router. Agenterna placeras ofta på flera användar platser, grenar och rack i Data Center.

## <a name="service-endpoint-monitor"></a>Övervakare av tjänst slut punkt

**Vilka avgifter används för användning av tjänst slut punkts övervakaren?**

Avgifter för användning av tjänst slut punkts övervakaren beräknas baserat på:
* Antal anslutningar
* Data volym

**Vad är en anslutning?**

En anslutning är ett test av nåbarhet till en slut punkt (URL eller nätverks tjänst) från en enda agent under hela månaden. Till exempel är övervakning av en anslutning till bing.com från tre agenter tre anslutningar.

**Vilka är kostnaderna för övervakning av tjänst slut punkter?**

Se avsnittet [anslutnings övervakning](https://azure.microsoft.com/pricing/details/network-watcher/) för kostnaden för att övervaka en slut punkt för hela månaden. Avgiften för data finns på [prissättnings sidan](https://azure.microsoft.com/pricing/details/log-analytics/) för Log Analytics i avsnittet data inmatning.

## <a name="references"></a>Referenser

[Vanliga frågor och svar om Log Analytics prissättning](https://azure.microsoft.com/pricing/details/log-analytics/): avsnittet Vanliga frågor och svar innehåller information om kostnads fri nivå, pris per nod och annan pris information.