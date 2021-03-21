---
title: Beroende analys i Azure Migrate Server-utvärdering
description: Beskriver hur du använder beroende analys för utvärdering med Azure Migrate Server bedömning.
ms.topic: conceptual
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.date: 09/15/2020
ms.openlocfilehash: f5304e7634cfb7b4d5c3c05036c0606ba03295ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589053"
---
# <a name="dependency-analysis"></a>Beroende analys

I den här artikeln beskrivs beroende analys i Azure Migrate: Server utvärdering.


Beroende analys identifierar beroenden mellan identifierade lokala datorer. Det ger följande fördelar: 

- Du kan samla in datorer i grupper för utvärdering, mer exakt, med större exakthet.
- Du kan identifiera datorer som måste migreras tillsammans. Detta är särskilt användbart om du inte är säker på vilka datorer som ingår i en app-distribution som du vill migrera till Azure.
- Du kan identifiera om datorer används och vilka datorer som kan tas ur bruk i stället för att migreras.
- Att analysera beroenden hjälper till att se till att ingenting är kvar bakom och undviker därför oväntade avbrott efter migreringen.
- [Läs](common-questions-discovery-assessment.md#what-is-dependency-visualization) vanliga frågor om beroende analyser.


## <a name="analysis-types"></a>Analys typer

Det finns två alternativ för att distribuera beroende analyser

**Alternativ** | **Information** | **Offentligt moln** | **Azure Government**
----  |---- | ---- 
**Utan agent** | Avsöker data från virtuella VMware-datorer med vSphere-API: er.<br/><br/> Du behöver inte installera agenter på virtuella datorer.<br/><br/> Det här alternativet är för närvarande en för hands version, endast för virtuella VMware-datorer. | Stöds. | Stöds.
**Agent-baserad analys** | Använder [tjänstkarta lösning](../azure-monitor/vm/service-map.md) i Azure Monitor för att aktivera beroende visualisering och analys.<br/><br/> Du måste installera agenter på varje lokal dator som du vill analysera. | Stöds | Stöds inte.


## <a name="agentless-analysis"></a>Analys utan agent

Analytiska beroende analyser fungerar genom att samla in TCP-anslutningsfel från datorer som den är aktive rad för. Inga agenter är installerade på virtuella datorer. Anslutningar med samma käll Server och process, och mål servern, processen och porten grupperas logiskt i ett beroende. Du kan visualisera insamlade beroende data i en Map-vy eller exportera den som en CSV. Inga agenter är installerade på datorer som du vill analysera.

### <a name="dependency-data"></a>Beroende data

Efter identifieringen av beroende data börjar avsökningen:

- Azure Migrates apparaten avsöker TCP-anslutningsfel från datorer var femte minut för att samla in data.
- Data samlas in från virtuella gäst datorer via vCenter Server med hjälp av vSphere-API: er.
- Avsökningen samlar in dessa data:

    - Namn på processer som har aktiva anslutningar.
    - Namnet på programmet som kör processer som har aktiva anslutningar.
    - Målport på aktiva anslutningar.

- Insamlade data bearbetas på Azure Migrates enheten, för att härleda identitets information och skickas till Azure Migrate var sjätte timme


## <a name="agent-based-analysis"></a>Agent-baserad analys

Vid en agent-baserad analys använder Server utvärderingen [tjänstkarta](../azure-monitor/vm/service-map.md) lösning i Azure Monitor. Du installerar [Microsoft Monitoring Agent/Log Analytics-agenten](../azure-monitor/agents/agents-overview.md#log-analytics-agent) och [beroende agenten](../azure-monitor/agents/agents-overview.md#dependency-agent)på varje dator som du vill analysera.

### <a name="dependency-data"></a>Beroende data

Med en agent-baserad analys får du följande data:

- Käll datorns Server namn, process, program namn.
- Mål datorns Server namn, process, program namn och port.
- Antalet anslutningar, svars tid och data överförings information samlas in och är tillgängliga för Log Analytics frågor. 



## <a name="compare-agentless-and-agent-based"></a>Jämför agent utan agent och agent-baserad

Skillnaderna mellan agent utan visualisering och agentbaserade visualiseringar sammanfattas i tabellen.

**Krav** | **Utan agent** | **Agent-baserad**
--- | --- | ---
**Support** | I för hands version för virtuella VMware-datorer. [Granska](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agentless) operativ system som stöds. | Allmän tillgänglighet (GA).
**Gent** | Inga agenter behövs på de datorer som du vill analysera. | Agenter som krävs på varje lokal dator som du vill analysera.
**Log Analytics** | Krävs inte. | Azure Migrate använder [tjänstkarta](../azure-monitor/vm/service-map.md) -lösningen i [Azure Monitor loggar](../azure-monitor/logs/log-query-overview.md) för beroende analys.<br/><br/> Du kopplar en Log Analytics arbets yta till ett Azure Migrate-projekt. Arbets ytan måste ligga i regionerna östra USA, Sydostasien eller Västeuropa. Arbets ytan måste vara i en region där [tjänstkarta stöds](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
**Process** | Fångar in TCP-anslutningsfel. Efter identifieringen samlar den in data i intervall om fem minuter. | Tjänstkarta agenter som installerats på en dator samla in data om TCP-processer och inkommande/utgående anslutningar för varje process.
**Data** | Käll datorns Server namn, process, program namn.<br/><br/> Mål datorns Server namn, process, program namn och port. | Käll datorns Server namn, process, program namn.<br/><br/> Mål datorns Server namn, process, program namn och port.<br/><br/> Antalet anslutningar, svars tid och data överförings information samlas in och är tillgängliga för Log Analytics frågor. 
**Visualisering** | Beroende karta för enskild server kan visas över en varaktighet på en timme till 30 dagar. | Beroende karta för en enskild server.<br/><br/> Beroende karta för en grupp med servrar.<br/><br/>  Kartan kan endast visas över en timme.<br/><br/> Lägga till och ta bort servrar i en grupp från MAP-vyn.
Dataexport | De senaste 30 dagarna kan hämtas i CSV-format. | Data kan frågas med Log Analytics.



## <a name="next-steps"></a>Nästa steg

- [Konfigurera](how-to-create-group-machine-dependencies.md) en agent-baserad beroende visualisering.
- [Testa övervakning utan](how-to-create-group-machine-dependencies-agentless.md) agent beroende för virtuella VMware-datorer.
- Läs [vanliga frågor](common-questions-discovery-assessment.md#what-is-dependency-visualization) om beroende visualisering.
