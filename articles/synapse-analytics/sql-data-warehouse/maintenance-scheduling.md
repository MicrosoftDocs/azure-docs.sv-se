---
title: Underhålls scheman för Synapse SQL-pool
description: Med underhålls planeringen kan kunderna planera kring de nödvändiga schemalagda underhålls händelser som Azure Synapse Analytics använder för att distribuera nya funktioner, uppgraderingar och korrigeringar.
services: synapse-analytics
author: antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/02/2019
ms.author: anvang
ms.reviewer: jrasnick
ms.openlocfilehash: 886e99d6f56a008c694b3bcb366cfb4049f3528e
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590208"
---
# <a name="use-maintenance-schedules-to-manage-service-updates-and-maintenance"></a>Använd underhålls scheman för att hantera uppdateringar och underhåll av tjänster

Funktionen underhålls schema integrerar Service Health planerade underhålls aviseringar, Resource Health kontrol lera övervakning och underhålls planerings tjänsten för SQL-poolen för Synapse (Data Warehouse) i Azure Synapse Analytics.

Du bör använda underhålls schemaläggning för att välja en tids period när det är praktiskt att ta emot nya funktioner, uppgraderingar och korrigeringar. Du måste välja ett primärt och ett sekundärt underhålls fönster inom en sju dagars period. varje fönster måste ligga inom olika dags intervall.

Du kan till exempel schemalägga ett primärt fönster på lördag 22:00 till söndag 01:00 och sedan schemalägga ett sekundärt fönster på onsdag 19:00 till 22:00. Om underhållet inte kan utföras under ditt primära underhålls fönster, kommer det att försöka utföra underhållet igen under det sekundära underhålls fönstret. Underhåll av tjänsten kan uppstå under både den primära och den sekundära Windows. För att säkerställa snabb slut för ande av alla underhålls åtgärder kan DW400c och lägre informations lager nivåer slutföra underhållet utanför ett angivet underhålls fönster.

Alla nyligen skapade informations lager instanser har ett Systemdefinierat underhålls schema som tillämpas under distributionen. Schemat kan redige ras så snart distributionen är klar.

Även om en underhålls period kan vara mellan tre och åtta timmar innebär detta inte att data lagret är offline under hela tiden. Underhåll kan ske när som helst inom det fönstret och du bör förvänta dig att en enskild från koppling under perioden är cirka 5 -6 minuter, eftersom tjänsten distribuerar ny kod till data lagret. DW400c och lägre kan få flera korta förluster i anslutningen vid olika tidpunkter under underhålls perioden. När underhållet startar avbryts alla aktiva sessioner och transaktioner som inte allokeras kommer att återställas. Se till att informations lagret inte har några tids krävande transaktioner innan du väljer underhålls period för att minimera instans stillestånd.

Alla underhålls åtgärder bör slutföras inom de angivna underhålls Fönstren, såvida vi inte behöver distribuera en tids känslig uppdatering. Om informations lagret har pausats under ett schemalagt underhåll uppdateras det under återställnings åtgärden. Du får ett meddelande direkt efter att ditt underhåll av data lagret har slutförts.

## <a name="alerts-and-monitoring"></a>Aviseringar och övervakning

Genom att integrera med Service Health-meddelanden och Resource Health kontroll övervakaren kan kunderna hålla dig informerad om kommande underhålls aktiviteter. Den här automationen utnyttjar Azure Monitor. Du kan välja hur du vill bli meddelad om kommande underhålls händelser. Du kan också välja vilka automatiserade flöden som hjälper dig att hantera drift stopp och minimera drifts påverkan.

Ett meddelande om 24-timmarsformat föregås av alla underhålls händelser som inte är för DWC400c och lägre nivåer.

> [!NOTE]
> I händelse av att vi krävs för att distribuera en tidpunkts kritisk uppdatering kan avancerade aviserings tider minska avsevärt. Detta kan inträffa utanför ett identifierat underhålls fönster på grund av uppdateringens kritiska natur.

Om du fick ett meddelande om att underhåll pågår, men underhållet inte kan utföras under tids perioden i meddelandet, får du ett meddelande om uppsägning. Underhåll kommer sedan att återupptas under nästa schemalagda underhålls period.

Alla aktiva underhålls händelser visas i avsnittet **service Health-planerat underhåll** . Service Health historiken innehåller en fullständig post med tidigare händelser. Du kan övervaka underhåll via Azure Service Health kontrol lera portalens instrument panel under en aktiv händelse.

### <a name="maintenance-schedule-availability"></a>Tillgänglighet för underhålls schema

Även om underhålls planeringen inte är tillgänglig i den valda regionen kan du när som helst Visa och redigera ditt underhålls schema. När underhålls planeringen blir tillgänglig i din region blir det identifierade schemat omedelbart aktivt i Synapse SQL-poolen.

## <a name="view-a-maintenance-schedule"></a>Visa ett underhålls schema

Som standard har alla nyligen skapade informations lager instanser ett primärt och sekundärt underhålls fönster på åtta timmar som tillämpas under distributionen. Som anges ovan kan du ändra Windows så snart distributionen är klar. Inget underhåll sker utanför de angivna underhållsperioderna utan föregående meddelande.

Utför följande steg för att visa det underhålls schema som har tillämpats på din Synapse-SQL-pool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj den Synapse-SQL-pool som du vill visa.
3. Den valda Synapse-SQL-poolen öppnas på bladet översikt. Det underhålls schema som tillämpas på data lagret visas under **underhålls schema**.

![Översikts blad](./media/maintenance-scheduling/clear-overview-blade.PNG)

## <a name="change-a-maintenance-schedule"></a>Ändra ett underhålls schema

Ett underhålls schema kan uppdateras eller ändras när som helst. Om den valda instansen går genom en aktiv underhålls cykel sparas inställningarna. De blir aktiva under nästa identifierade underhålls period. [Lär dig mer](../../service-health/resource-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om att övervaka ditt informations lager under en aktiv underhålls händelse.

## <a name="identifying-the-primary-and-secondary-windows"></a>Identifiera de primära och sekundära Fönstren

De primära och sekundära Fönstren måste ha separata dags intervall. Ett exempel är ett primärt fönster på tisdag – torsdag och ett sekundärt fönster på lördag – söndag.

Utför följande steg för att ändra underhålls schema för din Synapse SQL-pool:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Välj den Synapse-SQL-pool som du vill uppdatera. Sidan öppnas på bladet översikt.
Öppna sidan för underhålls schema inställningar genom att välja länken **underhålls schema Sammanfattning** på bladet översikt. Alternativt väljer du alternativet **underhålls schema** på resurs menyn i den vänstra sidan.

    ![Översikt över blad alternativ](./media/maintenance-scheduling/maintenance-change-option.png)

3. Identifiera det önskade dags intervallet för ditt primära underhålls fönster med hjälp av alternativen överst på sidan. Det här valet avgör om ditt primära fönster kommer att ske på en veckodag eller över helgen. Ditt val uppdaterar List Rute värden.
Under för hands versionen kanske vissa regioner ännu inte har stöd för den fullständiga uppsättningen tillgängliga **dags** alternativ.

   ![Bladet underhålls inställningar](./media/maintenance-scheduling/maintenance-settings-page.png)

4. Välj önskat primärt och sekundärt underhålls fönster med hjälp av list rutorna:
   - **Dag**: prioriterad dag för att utföra underhåll under den valda perioden.
   - **Start tid**: önskad start tid för underhålls perioden.
   - **Tids** period: önskad varaktighet för din tidsperiod.

   Platsen för **schema Sammanfattning** längst ned på bladet uppdateras baserat på de värden som du har valt.
  
5. Välj **Spara**. Ett meddelande visas som bekräftar att ditt nya schema nu är aktivt.

   Om du sparar ett schema i en region som inte har stöd för underhålls planeringen visas följande meddelande. Inställningarna sparas och aktive ras när funktionen blir tillgänglig i den valda regionen.

   ![Meddelande om region tillgänglighet](./media/maintenance-scheduling/maintenance-not-active-toast.png)

## <a name="next-steps"></a>Nästa steg

- [Lär dig mer](../../azure-monitor/alerts/alerts-metric.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om att skapa, Visa och hantera aviseringar med hjälp av Azure Monitor.
- [Läs mer](../..//azure-monitor/alerts/alerts-log-webhook.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om webhook-åtgärder för logg aviserings regler.
- [Läs mer](../..//azure-monitor/alerts/action-groups.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Skapa och hantera åtgärds grupper.
- [Läs mer](../../service-health/service-health-overview.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) om Azure Service Health.
