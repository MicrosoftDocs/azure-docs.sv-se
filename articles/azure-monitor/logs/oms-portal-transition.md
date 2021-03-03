---
title: OMS-portalen flyttar till Azure | Microsoft Docs
description: OMS-portalen sunsetted med alla funktioner som flyttas till Azure Portal. Den här artikeln innehåller information om den här över gången.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/22/2019
ms.openlocfilehash: ab7d4bf0b8d8ca32dafe6f19b46047eca89a7734
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101733983"
---
# <a name="oms-portal-moving-to-azure"></a>OMS-portalen flyttar till Azure

> [!NOTE]
> Den här artikeln gäller både Azures offentliga moln och myndighets moln, om inget annat anges.

**OMS-portalen för det offentliga Azure-molnet har dragits tillbaka officiellt. OMS-portalen för Azures moln för amerikanska myndigheter drogs tillbaka officiellt den 15 maj 2019.** Vi är glada att gå över till Azure Portal och förväntar dig att över gången är enkel. Men vi förstår att ändringar är svåra och kan vara störande. Resten av den här artikeln går igenom de viktigaste scenarierna och översikten över över gången.

Azure Portal är hubben för alla Azure-tjänster och erbjuder en omfattande hanterings upplevelse med funktioner som instrument paneler för att fästa resurser, intelligent sökning för att hitta resurser och tagga för resurs hantering. För att konsolidera och effektivisera arbets flödet för övervakning och hantering började vi lägga till OMS-portalens funktioner i Azure Portal. Alla funktioner i OMS-portalen är nu en del av Azure Portal. I själva verket är några av de nya funktionerna som Trafikanalys endast tillgängliga i Azure Portal. Du kommer att kunna utföra allt du gjorde i OMS-portalen med Azure Portal med mera. Om du inte redan har gjort det, bör du börja använda Azure Portal idag!

## <a name="what-is-changing"></a>Vad ändras? 
Följande ändringar meddelas i OMS-portalen. Vart och ett av dessa ändringar beskrivs mer ingående i avsnitten nedan.

- Du kan bara skapa nya [arbets ytor](#new-workspaces) i Azure Portal.
- Den nya aviserings hanterings upplevelsen [ersätter aviseringshantering-lösningen](#changes-to-alerts).
- [Hantering av användar åtkomst](#user-access-and-role-migration) görs nu i Azure Portal med hjälp av rollbaserad åtkomst kontroll i Azure.
- [Application Insights-anslutningsprogram krävs inte längre](#application-insights-connector-and-solution) eftersom samma funktion har Aktiver ATS via frågor över olika arbets ytor.
- [OMS-mobilappen](#oms-mobile-app) är inaktuell. 
- [NSG-lösningen ersätts](#azure-network-security-group-analytics) med förbättrade funktioner som är tillgängliga via trafikanalys lösning.
- Nya anslutningar från System Center Operations Manager till Log Analytics kräver [uppdaterade hanterings paket](#system-center-operations-manager).
- Se [migrera dina OMS-uppdaterings distributioner till Azure](../../automation/migrate-oms-update-deployments.md) om du vill ha mer information om ändringar i [uppdateringshantering](../../automation/update-management/overview.md).


## <a name="what-should-i-do-now"></a>Vad ska jag göra nu?
De flesta funktioner fortsätter att fungera utan att utföra någon migrering, men du måste utföra följande uppgifter:

- Du måste [migrera dina användar behörigheter](#user-access-and-role-migration) till Azure Portal.
- Se [migrera dina OMS-uppdaterings distributioner till Azure](../../automation/migrate-oms-update-deployments.md) för mer information om att överföra uppdateringshantering-lösningen.

Se [vanliga frågor om över gången från OMS-portalen till Azure Portal för Log Analytics användare](../overview.md) för information om hur du övergår till Azure Portal. 

## <a name="user-access-and-role-migration"></a>Användar åtkomst och roll migrering
Azure Portal åtkomst hantering är rikare och kraftfullare än åtkomst hantering i OMS-portalen. Se [utforma arbets ytan för Azure Monitor loggar](../logs/design-logs-deployment.md) för information om åtkomst hantering i Log Analytics.

> [!NOTE]
> Tidigare versioner av den här artikeln angav att behörigheterna automatiskt skulle konverteras från OMS-portalen till Azure Portal. Den automatiska konverteringen är inte längre planerad och du måste utföra konverteringen själv.

Du kanske redan har rätt åtkomst i Azure Portal i vilket fall du inte behöver göra några ändringar. Det finns ett par fall där du kanske inte har rätt åtkomst i så fall måste administratören tilldela dig behörigheter.

- Du har skrivskyddade användar behörigheter i OMS-portalen men inga behörigheter i Azure Portal. 
- Du har deltagar behörighet i OMS-portalen men endast läsar åtkomst i Azure Portal.
 
I båda fallen måste administratören manuellt tilldela dig rätt roll från följande tabell. Vi rekommenderar att du tilldelar rollen till resurs gruppen eller prenumerations nivån.  Mer vägledning om vägledning kommer att tillhandahållas inom kort för båda dessa fall.

| Behörighet för OMS-portalen | Azure-roll |
|:---|:---|
| ReadOnly | Log Analytics Reader |
| Deltagare | Log Analytics Contributor |
| Administratör | Ägare | 
 

## <a name="new-workspaces"></a>Nya arbets ytor
Du kan inte längre skapa nya arbets ytor med OMS-portalen. Följ anvisningarna i [skapa en Log Analytics arbets yta i Azure Portal](./quick-create-workspace.md) för att skapa en ny arbets yta i Azure Portal.

## <a name="changes-to-alerts"></a>Ändringar av aviseringar

### <a name="alert-extension"></a>Aviserings tillägg  

Aviseringar har [utökats till Azure Portal](../alerts/alerts-unified-log.md) befintliga aviseringar visas fortfarande i OMS-portalen, men du kan bara hantera dem i Azure Portal. Om du får åtkomst till aviseringar via programmering med hjälp av Log Analytics varnings REST API eller Log Analytics aviserings resurs mal len, måste du använda åtgärds grupper i stället för åtgärder i API-anrop, Azure Resource Manager mallar och PowerShell-kommandon.

### <a name="alert-management-solution"></a>Lösning för aviserings hantering
Som en ändring från ett tidigare meddelande fortsätter [aviserings hanterings lösningen](../insights/alert-management-solution.md) att vara tillgänglig och fullständigt stöd i Azure Portal. Du kan fortsätta att installera lösningen från Azure Marketplace.

Även om aviserings hanterings lösningen fortfarande är tillgänglig, rekommenderar vi att du använder [Azure Monitors enhetliga aviserings gränssnitt](../alerts/alerts-overview.md) för att visualisera och hantera alla aviseringar i Azure. Med den här nya upplevelsen samlas aviseringar internt in från flera källor i Azure, inklusive logg aviseringar från Log Analytics. Om du använder Azure Monitors enhetliga aviserings gränssnitt krävs endast aviserings hanterings lösningen för att aktivera integrering av aviseringar från System Center Operation Manager till Azure. I Azure Monitors enhetliga aviserings gränssnitt kan du se distributioner av aviseringar, dra nytta av automatiserad gruppering av relaterade aviseringar via smarta grupper och visa aviseringar över flera prenumerationer samtidigt som du använder omfattande filter. Framtida framsteg i aviserings hantering kommer främst att vara tillgängligt i den här nya miljön. 

Data som samlas in av aviserings hanterings lösningen (poster med en typ av varning) fortsätter att finnas i Log Analytics så länge lösningen har installerats för arbets ytan. 

## <a name="oms-mobile-app"></a>OMS-mobilapp
OMS-mobilappen kommer att sunsetted tillsammans med OMS-portalen. I stället för OMS-mobilappen kan du komma åt information om din IT-infrastruktur, instrument paneler och sparade frågor. du kan komma åt Azure Portal direkt från din webbläsare på din mobila enhet. För att få aviseringar bör du konfigurera [Azure-åtgärds grupper](../alerts/action-groups.md) för att ta emot meddelanden i form av SMS eller ett röst samtal

## <a name="application-insights-connector-and-solution"></a>Application Insights-anslutningsprogram och lösning
[Application Insights-anslutningsprogram](../logs/app-insights-connector.md) är ett sätt att ta med Application Insights data till en Log Analytics-arbetsyta. Den här datareplikeringen krävdes för att möjliggöra insyn i infrastruktur-och program data. Med Application Insights stöd för utökade datakvarhållning i mars, 2019 och möjligheten att utföra [kors resurs frågor](../logs/cross-workspace-query.md) , förutom att kunna [visa flera Azure Monitor Application Insights resurser](./unify-app-resource-data.md), behöver du inte duplicera data från dina Application Insights resurser och skicka dem till Log Analytics. Dessutom skickar kopplingen en delmängd av program egenskaperna till Log Analytics, medan frågor över flera resurser ger dig förbättrad flexibilitet.  

På så sätt har Application Insights-anslutningsprogram föråldrats och tagits bort från Azure Marketplace tillsammans med OMS-portalen föråldrad 30 mars 2019. Befintliga anslutningar fortsätter att fungera fram till den 30 juni 2019. Med OMS-portalen är det inte möjligt att konfigurera och ta bort befintliga anslutningar från portalen. Detta kommer att stödjas med REST API som kommer att göras tillgängligt i januari 2019 och ett meddelande publiceras på [Azure updates](https://azure.microsoft.com/updates/). 

## <a name="azure-network-security-group-analytics"></a>Azure-nätverkssäkerhetsgruppanalys
[Azure-nätverkssäkerhetsgruppanalys lösningen](../insights/azure-networking-analytics.md#azure-network-security-group-analytics-solution-in-azure-monitor) ersätts med nyligen startade [trafikanalys](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) som ger insyn i användar-och program aktivitet i moln nätverk. Trafikanalys hjälper dig att granska organisationens nätverks aktivitet, säkra program och data, optimera arbets Belastningens prestanda och hålla koll på kraven. 

Den här lösningen analyserar NSG Flow-loggar och ger insikter om följande.

- Trafik flödar över nätverk mellan Azure och Internet, offentliga moln regioner, virtuella nätverk och undernät.
- Program och protokoll i nätverket, utan att behöva Sniffer eller dedikerade flödes insamlings utrustning.
- Främsta pratare, samtals program, virtuella dator konversationer i molnet, trafik hotspots.
- Källor och mål för trafik över virtuella nätverk, mellan relationer mellan kritiska företags tjänster och program.
- Säkerhet, inklusive skadlig trafik, portar öppna för Internet, program eller virtuella datorer som försöker ansluta till Internet.
- Kapacitets användning, som hjälper dig att undvika problem med att använda etableringen eller under användningen.

Du kan fortsätta att förlita dig på diagnostikinställningar för att skicka NSG-loggar till Log Analytics så att dina befintliga sparade sökningar, aviseringar och instrument paneler fortsätter att fungera. Kunder som redan har installerat lösningen kan fortsätta att använda den tills vidare. Från och med 5 september tas lösningen för nätverks säkerhets grupp analys bort från Marketplace och görs tillgänglig via communityn som en [Azure snabb starts mall](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Operationalinsights).

## <a name="system-center-operations-manager"></a>System Center Operations Manager
Om du har [anslutit din Operations Manager hanterings grupp till Log Analytics](../agents/om-agents.md)fortsätter den att fungera utan ändringar. För nya anslutningar måste du följa anvisningarna i [hanterings paketet för Microsoft System Center Operations Manager för att konfigurera Operations Management Suite](https://techcommunity.microsoft.com/t5/system-center-blog/bg-p/SystemCenterBlog).

## <a name="next-steps"></a>Nästa steg
- I [vanliga frågor och svar om över gången från OMS-portalen till Azure Portal för Log Analytics användare](../overview.md) att flytta från OMS-portalen till Azure Portal.