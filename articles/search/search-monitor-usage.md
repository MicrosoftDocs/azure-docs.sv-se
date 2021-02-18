---
title: Övervaka åtgärder och aktivitet
titleSuffix: Azure Cognitive Search
description: Aktivera loggning, Hämta aktivitets statistik, resursanvändning och andra system data från en Azure Kognitiv sökning-tjänst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3f348a5005f28fdd1d02be706ca2742d77368288
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592376"
---
# <a name="monitor-operations-and-activity-of-azure-cognitive-search"></a>Övervaka åtgärder och aktiviteter i Azure Kognitiv sökning

Den här artikeln är en översikt över hur du övervakar koncept och verktyg för Azure Kognitiv sökning. För holistisk övervakning kan du använda en kombination av inbyggda funktioner och tilläggs tjänster som Azure Monitor.

Du kan helt följa följande:

* Tjänst: hälso tillstånd/tillgänglighet och ändringar i tjänst konfigurationen.
* Lagring: både Använd och tillgänglig, med antal för varje innehålls typ i förhållande till den kvot som tillåts för tjänst nivån.
* Fråga aktivitet: volym, svars tid och begränsade eller avbrutna frågor. Loggade förfrågningar kräver [Azure Monitor](#add-azure-monitor).
* Indexerings aktivitet: kräver [diagnostisk loggning](#add-azure-monitor) med Azure Monitor.

En Sök tjänst stöder inte autentisering per användare, så ingen identitets information finns i loggarna.

## <a name="built-in-monitoring"></a>Inbyggd övervakning

Inbyggd övervakning avser aktiviteter som loggas av en Sök tjänst. Med undantag för diagnostik krävs ingen konfiguration för den här övervaknings nivån.

Azure Kognitiv sökning hanterar interna data på ett rullande 30-dagars schema för rapportering av service hälsa och Frågeregler, som du hittar i portalen eller via dessa REST-API: [er](#monitoring-apis).

På följande skärm bild kan du hitta övervaknings information i portalen. Data blir tillgängliga så fort du börjar använda tjänsten. Portal sidor uppdateras en gång i minuten.

* Fliken **övervakning** på sidan huvud översikt visar fråga volym, svars tid och om tjänsten är under tryck.
* **Aktivitets loggen** i det vänstra navigerings fönstret är ansluten till Azure Resource Manager. Aktivitets logg rapporter om åtgärder som utförs av Resource Manager: tjänstens tillgänglighet och status, ändringar av kapacitet (repliker och partitioner) och API-relaterade aktiviteter.
* **Övervaknings** inställningar, mer om, innehåller konfigurerbara aviseringar, mått och diagnostikloggar. Skapa dem när du behöver dem. När data har samlats in och lagrats kan du fråga eller visualisera informationen för insikter.

![Azure Monitor integrering i en Sök tjänst](./media/search-monitor-usage/azure-monitor-search.png
 "Azure Monitor integrering i en Sök tjänst")

> [!NOTE]
> Eftersom Portal sidorna uppdateras med några minuters mellanrum är de siffror som rapporteras ungefärliga och ger dig en allmän uppfattning om hur väl systemet hanterar begär Anden. Faktiska mått, till exempel frågor per sekund (frågor per sekund) kan vara högre eller lägre än det tal som visas på sidan. Om precisionen är ett krav bör du överväga att använda API: er.

<a name="monitoring-apis"> </a>

### <a name="apis-useful-for-monitoring"></a>API: er som är användbara för övervakning

Du kan använda följande API: er för att hämta samma information som finns på flikarna övervakning och användning i portalen.

* [Hämta tjänst statistik](/rest/api/searchservice/get-service-statistics)
* [Hämta index statistik](/rest/api/searchservice/get-index-statistics)
* [Hämta dokument antal](/rest/api/searchservice/count-documents)
* [Hämta indexerings status](/rest/api/searchservice/get-indexer-status)

### <a name="activity-logs-and-service-health"></a>Aktivitets loggar och service hälsa

På sidan [**aktivitets logg**](../azure-monitor/essentials/activity-log.md#view-the-activity-log) i portalen samlas information från Azure Resource Manager och rapporter om ändringar av tjänstens hälsa. Du kan övervaka aktivitets loggen för kritiska, fel och varnings villkor relaterade till tjänstens hälsa.

Vanliga poster innehåller referenser till API-nycklar – allmänna informations meddelanden som *Hämta administratörs nyckel* och *Hämta frågeinställningar*. Dessa aktiviteter visar begär Anden som har gjorts med hjälp av administratörs nyckeln (skapa eller ta bort objekt) eller frågeinställningar, men som inte visar själva förfrågningen. För information om denna kornig het måste du konfigurera diagnostisk loggning.

Du kan komma åt **aktivitets loggen** i det vänstra navigerings fönstret eller från meddelanden i det övre fönstrets kommando fält eller på sidan **diagnosticera och lösa problem** .

### <a name="monitor-storage-in-the-usage-tab"></a>Övervaka lagring på fliken användning

För visuell övervakning i portalen visar fliken **användning** resurs tillgänglighet i förhållande till de aktuella [begränsningar](search-limits-quotas-capacity.md) som angetts av tjänst nivån. Om du slutför beslut om [vilken nivå som ska användas för produktions arbets belastningar](search-sku-tier.md), eller om du vill [Justera antalet aktiva repliker och partitioner](search-capacity-planning.md), kan dessa mått hjälpa dig med dessa beslut genom att visa hur snabbt resurser förbrukas och hur väl den aktuella konfigurationen hanterar den befintliga belastningen.

Följande bild är avsedd för den kostnads fria tjänsten som är ett tak på 3 objekt av varje typ och 50 MB lagrings utrymme. En Basic-eller standard-tjänst har högre gränser, och om du ökar antalet partitioner, hamnar det maximala lagrings utrymmet proportionellt.

![Användnings status relativt nivå gränser](./media/search-monitor-usage/usage-tab.png
 "Användnings status relativt nivå gränser")

> [!NOTE]
> Aviseringar relaterade till lagring är inte tillgängliga för närvarande. lagrings förbrukningen är inte aggregerad eller inloggad i **AzureMetrics** -tabellen i Azure Monitor. För att få lagrings aviseringar måste du [skapa en anpassad lösning som utvecklar](../azure-monitor/insights/solutions.md) resursbaserade meddelanden, där koden söker efter lagrings storlek och hanterar svaret.

<a name="add-azure-monitor"></a>

## <a name="add-on-monitoring-with-azure-monitor"></a>Övervakning av tillägg med Azure Monitor

Många tjänster, inklusive Azure Kognitiv sökning, integreras med [Azure Monitor](../azure-monitor/index.yml) för ytterligare aviseringar, mått och loggning av diagnostikdata. 

[Aktivera diagnostisk loggning](search-monitor-logs.md) för en Sök tjänst om du vill ha kontroll över insamling och lagring av data. Loggade händelser som registrerats av Azure Monitor lagras i tabellen **AzureDiagnostics** och består av drift data som rör frågor och indexering.

Azure Monitor tillhandahåller flera lagrings alternativ och ditt val avgör hur du kan använda data:

* Välj Azure Blob Storage om du vill [visualisera loggdata](search-monitor-logs-powerbi.md) i en Power BI rapport.
* Välj Log Analytics om du vill utforska data via Kusto-frågor.

Azure Monitor har en egen fakturerings struktur och diagnostiska loggar som refereras i det här avsnittet har en associerad kostnad. Mer information finns [i användning och uppskattade kostnader i Azure Monitor](../azure-monitor//usage-estimated-costs.md).

## <a name="monitor-user-access"></a>Övervaka användar åtkomst

Eftersom Sök index är en komponent i ett större klient program finns det ingen inbyggd metod för att styra eller övervaka åtkomst till ett index per användare. Förfrågningar antas komma från ett klient program, för antingen administratörs-eller fråge förfrågningar. Administratörens Läs-och skriv åtgärder omfattar att skapa, uppdatera och ta bort objekt i hela tjänsten. Skrivskyddade åtgärder är frågor mot dokument samlingen som är begränsade till ett enda index. 

Det du ser i aktivitets loggarna är till exempel referenser till anrop med hjälp av administratörs nycklar eller frågeinställningar. Lämplig nyckel ingår i begär Anden som härstammar från klient koden. Tjänsten är inte utrustad för att hantera identitets-tokens eller personifiering.

När det finns affärs krav för auktorisering per användare, är rekommendationen integrerad med Azure Active Directory. Du kan använda $filter-och användar identiteter för att [trimma Sök Resultat](search-security-trimming-for-azure-search-with-aad.md) för dokument som en användare inte bör se. 

Det finns inget sätt att logga den här informationen separat från frågesträngen som innehåller parametern $filter. Se [övervaka frågor](search-monitor-queries.md) för information om rapportering av frågesträngar.

## <a name="next-steps"></a>Nästa steg

Fluency med Azure Monitor är viktigt för att kunna ta hänsyn till alla Azure-tjänster, inklusive resurser som Azure Kognitiv sökning. Om du inte är bekant med Azure Monitor tar du tid att granska artiklar som är relaterade till resurser. Förutom självstudierna är följande artikel en bra plats att börja med.

> [!div class="nextstepaction"]
> [Övervaka Azure-resurser med Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md)