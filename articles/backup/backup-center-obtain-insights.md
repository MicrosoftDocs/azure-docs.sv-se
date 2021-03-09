---
title: Få insikter med Backup Center
description: Lär dig att analysera historiska trender och få djupare insikter om dina säkerhets kopior med Backup Center.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c48173749a9b47be7eeb906e9f8eec716e0cb200
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102506024"
---
# <a name="obtain-insights-using-backup-center"></a>Få insikter med Backup Center

För att analysera historiska trender och få djupare insikter om dina säkerhets kopior, tillhandahåller backup Center ett gränssnitt för [säkerhets kopiering av rapporter](configure-reports.md), som använder [Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md) och [Azure-arbetsböcker](../azure-monitor/visualize/workbooks-overview.md). Säkerhets kopierings rapporter innehåller följande funktioner:

- Allokering och Prognosticering av moln lagring förbrukat.

- Granskning av säkerhets kopiering och återställning.

- Identifiera viktiga trender på olika nivåer av granularitet.

- Få insyn och insikter om kostnads optimerings möjligheter för dina säkerhets kopieringar.

## <a name="supported-scenarios"></a>Scenarier som stöds

- Säkerhets kopierings rapporter är för närvarande inte tillgängliga för Azure Database for PostgreSQL Server säkerhets kopiering.

- Se [support mat ris](backup-center-support-matrix.md) för en detaljerad lista över scenarier som stöds och som inte stöds.

## <a name="get-started"></a>Kom igång

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Konfigurera dina valv för att skicka data till en Log Analytics arbets yta

[Lär dig hur du konfigurerar diagnostikinställningar i skala för dina valv](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Visa säkerhets kopierings rapporter i Backup Center-portalen

Om du väljer meny alternativet **säkerhetskopiera rapporter** i Backup Center öppnas rapporterna. Välj en eller flera Log Analytics arbets ytor för att visa och analysera viktig information om dina säkerhets kopior.

![Säkerhets kopierings rapporter i Backup Center](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

Nedan visas tillgängliga vyer:

1. **Sammanfattning** – Använd den här fliken för att få en översikt över din reserv egendom. [Läs mer](./configure-reports.md#summary)

2. **Säkerhets kopierings objekt** – Använd den här fliken för att se information och trender i moln lagring som förbrukas på en säkerhets kopierings nivå. [Läs mer](./configure-reports.md#backup-items)

3. **Användning** – Använd den här fliken för att Visa nyckel fakturerings parametrar för dina säkerhets kopior. [Läs mer](./configure-reports.md#usage)

4. **Jobb** – Använd den här fliken för att Visa tids krävande trender för jobb, till exempel antalet misslyckade jobb per dag och de vanligaste orsakerna till jobbfel. [Läs mer](./configure-reports.md#jobs)

5. **Principer** – Använd den här fliken om du vill visa information om alla dina aktiva principer, till exempel antalet associerade objekt och den totala moln lagring som förbrukas av objekt som har säkerhetskopierats under en specifik princip. [Läs mer](./configure-reports.md#policies)

6. **Optimera** – Använd den här fliken för att få insyn i potentiella kostnads optimerings möjligheter för dina säkerhets kopieringar. [Läs mer](./configure-reports.md#optimize)

7. **Princip** ökning – Använd den här fliken för att få insyn i om varje säkerhets kopierings instans har minst en lyckad säkerhets kopiering per dag. [Läs mer](./configure-reports.md#policy-adherence)

Du kan också konfigurera e-postmeddelanden för alla dessa rapporter med hjälp av funktionen för [e-postrapporten](backup-reports-email.md) .

## <a name="next-steps"></a>Nästa steg

- [Övervaka och hantera säkerhets kopior](backup-center-monitor-operate.md)
- [Styr din reserv egendom](backup-center-govern-environment.md)
- [Utföra åtgärder med Backup Center](backup-center-actions.md)