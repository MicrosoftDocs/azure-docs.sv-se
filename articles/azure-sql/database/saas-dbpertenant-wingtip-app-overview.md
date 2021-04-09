---
title: Exempel på flera klient organisationer – Wingtip SaaS
description: Lär dig genom att använda ett exempel program för flera innehavare som använder Azure SQL Database, Wingtip SaaS-exemplet
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/24/2018
ms.openlocfilehash: 0efc8105295ca7f97e6f465071c31b193173c5cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100588725"
---
# <a name="introduction-to-a-multitenant-saas-app-that-uses-the-database-per-tenant-pattern-with-azure-sql-database"></a>Introduktion till en SaaS-app med flera innehavare som använder mönstret för databas per klient organisation med Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Wingtip SaaS-programmet är ett exempel på en app för flera klienter. Appen använder program mönstret databas-per-innehavare SaaS för att betjäna flera klienter. Appen visar funktioner i Azure SQL Database som möjliggör SaaS-scenarier genom att använda flera design-och hanterings mönster för SaaS. För att snabbt komma igång distribuerar Wingtip SaaS-appen på mindre än fem minuter.

Program käll kod och hanterings skript är tillgängliga i [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub lagrings platsen. Innan du börjar, se den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ned och avblockera hanterings skripten för Wingtip Ticket.

## <a name="application-architecture"></a>Programmets arkitektur

Wingtip SaaS-appen använder modellen för databas per klient. Den använder elastiska SQL-pooler för att maximera effektiviteten. För att kunna konfigurera och mappa klienter till deras data används en katalog databas. Core Wingtip SaaS-programmet använder en pool med tre exempel klienter, plus katalog databasen. Katalogen och klient servrarna har etablerats med DNS-alias. Dessa alias används för att upprätthålla en referens till de aktiva resurser som används av Wingtip-programmet. Dessa alias uppdateras för att peka på återställnings resurser i självstudierna om haveri beredskap. Att slutföra många av Wingtip SaaS-självstudierna leder till tillägg i den första distributionen. Tillägg som analys databaser och schema hantering över databaser införs.


![Wingtip SaaS-arkitektur](./media/saas-dbpertenant-wingtip-app-overview/app-architecture.png)


När du går igenom självstudierna och arbetar med appen kan du fokusera på SaaS-mönstren när de relaterar till data nivån. Du kan med andra ord fokusera på data nivån och inte analysera själva appen. Att förstå implementeringen av dessa SaaS-mönster är nyckeln till att implementera dessa mönster i dina program. Överväg även eventuella nödvändiga ändringar för dina specifika affärs behov.

## <a name="sql-database-wingtip-saas-tutorials"></a>Självstudier om SQL Database Wingtip SaaS

När du har distribuerat appen kan du utforska följande självstudier som bygger på den första distributionen. De här självstudierna visar vanliga SaaS-mönster som utnyttjar inbyggda funktioner i SQL Database, Azure Synapse Analytics och andra Azure-tjänster. Självstudier innehåller PowerShell-skript med detaljerade förklaringar. Förklaringarna fören klar förståelse och implementering av samma SaaS hanterings mönster i dina program.


| Självstudie | Beskrivning |
|:--|:--|
| [Vägledning och tips för SQL Database SaaS-exempel för flera klient organisationer](saas-tenancy-wingtip-app-guidance-tips.md) | Hämta och kör PowerShell-skript för att förbereda delar av programmet. |
|[Distribuera och utforska Wingtip SaaS-programmet](./saas-dbpertenant-get-started-deploy.md)|  Distribuera och utforska Wingtip SaaS-programmet med din Azure-prenumeration. |
|[Etablera och katalogisera klienter](./saas-dbpertenant-provision-and-catalog.md)| Lär dig hur programmet ansluter till klienter med hjälp av en katalog databas och hur katalogen mappar klienter till sina data. |
|[Övervaka och hantera prestanda](./saas-dbpertenant-performance-monitoring.md)| Lär dig hur du använder övervaknings funktioner i SQL Database och ställer in aviseringar när tröskelvärden för prestanda överskrids. |
|[Övervaka med Azure Monitor-loggar](./saas-dbpertenant-log-analytics.md) | Lär dig hur du använder [Azure Monitor loggar](../../azure-monitor/logs/log-query-overview.md) för att övervaka stora mängder resurser över flera pooler. |
|[Återställa en enskild klientorganisation](./saas-dbpertenant-restore-single-tenant.md)| Lär dig hur du återställer en klient databas till en tidigare tidpunkt. Lär dig också hur du återställer till en parallell databas som lämnar den befintliga klient databasen online. |
|[Hantera klient organisationens databas schema](saas-tenancy-schema-management.md)| Lär dig hur du uppdaterar schema och uppdaterar referens data över alla klient databaser. |
|[Kör distribuerade frågor mellan klienter](saas-tenancy-cross-tenant-reporting.md) | Skapa en ad hoc Analytics-databas och kör distribuerade frågor i real tid över alla klienter.  |
|[Köra analyser på extraherade klient data](saas-tenancy-tenant-analytics.md) | Extrahera klient data till en Analytics-databas eller ett informations lager för offline Analytics-frågor. |


## <a name="next-steps"></a>Nästa steg

- [Allmänna rikt linjer och tips när du distribuerar och använder Wingtip Ticket SaaS app-exempel](saas-tenancy-wingtip-app-guidance-tips.md)
- [Distribuera Wingtip SaaS-programmet](./saas-dbpertenant-get-started-deploy.md)