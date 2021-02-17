---
title: Azure Monitor loggar med en app för flera klient organisationer
description: Konfigurera och använda Azure Monitor loggar med flera innehavare Azure SQL Database SaaS-appen
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 029e3ba799e5f239bde0ef049316dd268ebe4c8f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588790"
---
# <a name="set-up-and-use-azure-monitor-logs-with-a-multitenant-azure-sql-database-saas-app"></a>Konfigurera och använda Azure Monitor loggar med flera innehavare Azure SQL Database SaaS-appen
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

I den här självstudien ställer du in och använder [Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md) för att övervaka elastiska pooler och databaser. Den här självstudien bygger på [självstudien om prestanda övervakning och hantering](saas-dbpertenant-performance-monitoring.md). Det visar hur du använder Azure Monitor loggar för att förstärka övervakningen och aviseringarna som finns i Azure Portal. Azure Monitor loggar har stöd för övervakning av tusentals elastiska pooler och hundratals tusentals databaser. Azure Monitor loggar tillhandahåller en enda övervaknings lösning som kan integrera övervakning av olika program och Azure-tjänster över flera Azure-prenumerationer.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Installera och konfigurera Azure Monitor loggar.
> * Använd Azure Monitor loggar för att övervaka pooler och databaser.

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip-biljetterna SaaS Database-per-klient-app distribueras. Mer information om hur du distribuerar på mindre än fem minuter finns i [distribuera och utforska Wingtip Ticket SaaS Database-användarspecifika program](./saas-dbpertenant-get-started-deploy.md).
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](/powershell/azure/get-started-azureps).

I [självstudien om prestanda övervakning och hantering](saas-dbpertenant-performance-monitoring.md) får du en beskrivning av SaaS-scenarier och mönster och hur de påverkar kraven på en övervaknings lösning.

## <a name="monitor-and-manage-database-and-elastic-pool-performance-with-azure-monitor-logs"></a>Övervaka och hantera prestanda för databaser och elastiska pooler med Azure Monitor loggar

För Azure SQL Database är övervakning och avisering tillgänglig på databaser och pooler i Azure Portal. Den här inbyggda övervakningen och aviseringen är praktisk, men det är också resurs särskilt. Det innebär att det är mindre bra att övervaka stora installationer eller att tillhandahålla en enhetlig vy över resurser och prenumerationer.

I scenarier med stora volymer kan du använda Azure Monitor loggar för övervakning och avisering. Azure Monitor är en separat Azure-tjänst som möjliggör analys över loggar som samlats in i en arbets yta från potentiellt många tjänster. Azure Monitor-loggar innehåller ett inbyggt frågespråk och data visualiserings verktyg som tillåter drift data analys. SQL Analytics-lösningen innehåller flera fördefinierade elastiska pooler och databas övervakning och aviseringar och frågor. Azure Monitor loggar innehåller också en anpassad vy designer.

OMS-arbetsytor kallas nu för Log Analytics-arbetsytor. Log Analytics arbets ytor och analys lösningar som är öppna i Azure Portal. Azure Portal är den nyare åtkomst punkten, men det kan vara vad som ligger bakom Operations Management Suite-portalen i vissa områden.

### <a name="create-performance-diagnostic-data-by-simulating-a-workload-on-your-tenants"></a>Skapa prestanda diagnostikdata genom att simulera en arbets belastning på klienterna 

1. Öppna i PowerShell ISE *. \\ WingtipTicketsSaaS – MultiTenantDb-Master \\ Learning modules \\ , prestanda övervakning och hantering \\Demo-PerformanceMonitoringAndManagement.ps1*. Se till att det här skriptet är öppet eftersom du kanske vill köra flera av scenarierna för inläsnings generering under den här självstudien.
1. Om du inte redan har gjort det kan du etablera en batch med klienter för att göra övervaknings kontexten mer intressant. Den här processen tar några minuter.

   a. Ange **$DemoScenario = 1**, _etablera en batch med klienter_.

   b. Tryck på F5 för att köra skriptet och distribuera ytterligare 17 klienter.

1. Starta nu belastnings generatorn för att köra en simulerad belastning på alla klienter.

    a. Ange **$DemoScenario = 2**, _generera normal belastnings belastning (cirka 30 DTU)_.

    b. Tryck på F5 för att köra skriptet.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Hämta Wingtip-biljetterna SaaS Database-användarspecifika program skript

Wingtip biljetter SaaS-skript för flera klient organisationer och program käll kod är tillgängliga i [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub lagrings platsen. Anvisningar för hur du hämtar och avblockerar PowerShell-skripten Wingtip Tick finns i den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md).

## <a name="install-and-configure-log-analytics-workspace-and-the-azure-sql-analytics-solution"></a>Installera och konfigurera Log Analytics-arbetsyta och Azure SQL-analys-lösningen

Azure Monitor är en separat tjänst som måste konfigureras. Azure Monitor loggar samlar in loggdata, telemetri och mått i en Log Analytics arbets yta. Precis som andra resurser i Azure måste en Log Analytics arbets yta skapas. Arbets ytan behöver inte skapas i samma resurs grupp som de program som den övervakar. Det är ofta bäst att göra det. För Wingtip biljetter-appen använder du en enda resurs grupp för att se till att arbets ytan tas bort med programmet.

1. Öppna i PowerShell ISE *. \\ WingtipTicketsSaaS-MultiTenantDb-Master \\ Learning-moduler för \\ prestanda övervakning och hantering \\ Log Analytics \\Demo-LogAnalytics.ps1*.
1. Tryck på F5 för att köra skriptet.

Nu kan du öppna Azure Monitor loggar i Azure Portal. Det tar några minuter att samla in telemetri i Log Analytics arbets ytan och göra det synligt. Det är längre du lämnar systemet med data insamlings diagnostikdata, desto mer intressant är upplevelsen. 

## <a name="use-log-analytics-workspace-and-the-sql-analytics-solution-to-monitor-pools-and-databases"></a>Använd Log Analytics-arbetsyta och SQL Analytics-lösningen för att övervaka pooler och databaser


I den här övningen öppnar du Log Analytics arbets ytan i Azure Portal för att titta på den telemetri som samlats in för databaser och pooler.

1. Bläddra till [Azure Portal](https://portal.azure.com). Välj **alla tjänster** som ska öppnas Log Analytics arbets ytan. Sök sedan efter Log Analytics.

   ![Öppna Log Analytics arbets yta](./media/saas-dbpertenant-log-analytics/log-analytics-open.png)

1. Välj arbets ytan med namnet _wtploganalytics &lt; - &gt; User_.

1. Välj **Översikt** för att öppna Log Analytics-lösningen i Azure Portal.

   ![Översikt](./media/saas-dbpertenant-log-analytics/click-overview.png)

    > [!IMPORTANT]
    > Det kan ta några minuter innan lösningen är aktiv. 

1. Välj panelen **Azure SQL-analys** för att öppna den.

    ![Översikts panel](./media/saas-dbpertenant-log-analytics/overview.png)

1. Vyerna i lösningen rullas åt sidan, med sin egen inre rullnings List längst ned. Uppdatera sidan om det behövs.

1. Om du vill utforska sammanfattnings sidan väljer du panelerna eller de enskilda databaserna för att öppna en gransknings Utforskare.

    ![Log Analytics-instrumentpanel](./media/saas-dbpertenant-log-analytics/log-analytics-overview.png)

1. Ändra filter inställningen för att ändra tidsintervallet. I den här självstudien väljer du **senaste 1 timma**.

    ![Tids filter](./media/saas-dbpertenant-log-analytics/log-analytics-time-filter.png)

1. Välj en enskild databas för att utforska frågans användning och mått för den databasen.

    ![Databas analys](./media/saas-dbpertenant-log-analytics/log-analytics-database.png)

1. Om du vill se användnings statistik rullar du sidan Analytics till höger.
 
     ![Databas mått](./media/saas-dbpertenant-log-analytics/log-analytics-database-metrics.png)

1. Rulla Analytics-sidan till vänster och välj server panelen i **resurs informations** listan.  

    ![Resurs informations lista](./media/saas-dbpertenant-log-analytics/log-analytics-resource-info.png)

    En sida öppnas som visar pooler och databaser på servern.

    ![Server med pooler och databaser](./media/saas-dbpertenant-log-analytics/log-analytics-server.png)

1. Välj en pool. På sidan pool som öppnas rullar du åt höger för att se poolens mått. 

    ![Pool mått](./media/saas-dbpertenant-log-analytics/log-analytics-pool-metrics.png)


1. Gå tillbaka till arbets ytan Log Analytics och välj **OMS-portalen** för att öppna arbets ytan där.

    ![Log Analytics-arbetsyta](./media/saas-dbpertenant-log-analytics/log-analytics-workspace-oms-portal.png)

I arbets ytan Log Analytics kan du utforska logg-och mått data ytterligare. 

Övervakning och avisering i Azure Monitor loggar baseras på frågor över data i arbets ytan, till skillnad från aviseringar som definierats för varje resurs i Azure Portal. Genom att basera aviseringar på frågor kan du definiera en enskild avisering som söker över alla databaser, i stället för att definiera en per databas. Frågor begränsas endast av de data som är tillgängliga på arbets ytan.

Mer information om hur du använder Azure Monitor loggar för att fråga och ange aviseringar finns [i arbeta med varnings regler i Azure Monitor loggar](../../azure-monitor/alerts/alerts-metric.md).

Azure Monitor loggar för SQL Database avgifter baserat på data volymen i arbets ytan. I den här självstudien har du skapat en kostnads fri arbets yta, som är begränsad till 500 MB per dag. När gränsen har nåtts läggs data inte längre till i arbets ytan.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Installera och konfigurera Azure Monitor loggar.
> * Använd Azure Monitor loggar för att övervaka pooler och databaser.

Prova [själv studie kursen om klient analys](saas-dbpertenant-log-analytics.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Ytterligare självstudier som bygger på de första Wingtip-biljetterna SaaS Database-per-klient program distribution](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Azure Monitor-loggar](../../azure-monitor/insights/azure-sql.md)