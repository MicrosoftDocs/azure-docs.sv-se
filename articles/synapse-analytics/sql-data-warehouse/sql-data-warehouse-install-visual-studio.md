---
title: Installera Visual Studio 2019
description: Installera Visual Studio och SQL Server Development Tools (SSDT) för Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 56a2cbf88cec47417e992990c16a76ec84c6433d
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98117817"
---
# <a name="getting-started-with-visual-studio-2019"></a>Kom igång med Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) är ett enda verktyg som gör att du kan göra följande:

- Ansluta, fråga och utveckla program
- Använd en objekt Utforskare för att visuellt utforska alla objekt i din data modell, inklusive tabeller, vyer, lagrade procedurer och så vidare.
- Generera T-SQL Data Definition Language-skript (DDL) för dina objekt
- Utveckla ditt informations lager med hjälp av en tillstånds-baserad metod med SSDT databas projekt
- Integrera ditt databas projekt med käll kontroll system som git med Azure databaser
- Konfigurera kontinuerliga integrerings-och distributions pipeliner med Automation-servrar som Azure DevOps

## <a name="install-visual-studio-2019"></a>Installera Visual Studio 2019

Se [Ladda ned Visual studio 2019](https://visualstudio.microsoft.com/downloads/) för att ladda ned och installera visual Studio **16,3 och senare**. Under installationen väljer du arbets belastningen lagring och bearbetning av data. Installation av fristående SSDT krävs inte längre i Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funktioner som inte stöds i SSDT

Det finns tillfällen när funktions versioner för Synapse SQL inte innehåller stöd för SSDT. Följande funktioner stöds för närvarande inte:


- [Arbets belastnings hantering](sql-data-warehouse-workload-management.md) -arbets belastnings grupper och klassificerare
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) (inklusive tabell värdes funktioner)
  - Skicka in ett [support ärende eller rösta](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) för att få den funktion som stöds.
  - Skicka in ett [support ärende eller rösta](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) för att få den funktion som stöds.
- Tabeller med en [identitets kolumn](/sql/t-sql/statements/create-table-transact-sql-identity-property?view=sql-server-ver15)
- Vissa T-SQL-funktioner, till exempel:
   - I *Group* -satsen i funktionen [STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) sträng.

## <a name="next-steps"></a>Nästa steg

Nu när du har den senaste versionen av SSDT är du redo att [ansluta](sql-data-warehouse-query-visual-studio.md) till din SQL-pool.