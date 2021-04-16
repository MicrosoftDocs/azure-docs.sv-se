---
title: Installera Visual Studio 2019
description: Installera Visual Studio och SQL Server Development Tools (SSDT) för Synapse SQL
services: synapse-analytics
ms.custom: vs-azure, azure-synapse
ms.workload: azure-vs
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 05/11/2020
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: c8c07997b3ef8cb050ea4609a650a3e3b1bd21fb
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568258"
---
# <a name="getting-started-with-visual-studio-2019"></a>Kom igång med Visual Studio 2019

Visual Studio **2019** SQL Server Data Tools (SSDT) är ett enda verktyg som gör att du kan göra följande:

- Ansluta, köra frågor mot och utveckla program
- Använd en objektutforskare för att visuellt utforska alla objekt i datamodellen, inklusive tabeller, vyer, lagrade procedurer och så vidare.
- Generera DDL-skript (T-SQL Data Definition Language) för dina objekt
- Utveckla ditt informationslager med hjälp av en tillståndsbaserad metod med SSDT Database Projects
- Integrera ditt databasprojekt med källkontrollsystem som Git med Azure Repos
- Konfigurera pipelines för kontinuerlig integrering och distribution med automationsservrar som Azure DevOps

## <a name="install-visual-studio-2019"></a>Installera Visual Studio 2019

Se [Ladda ned Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) för att ladda ned och installera Visual Studio **16.3 och högre.** Under installationen väljer du arbetsbelastningen för datalagring och bearbetning. Fristående SSDT-installation krävs inte längre i Visual Studio 2019.

## <a name="unsupported-features-in-ssdt"></a>Funktioner som inte stöds i SSDT

Det finns tillfällen när funktionsutgåningar för Synapse SQL inte har stöd för SSDT. Följande funktioner stöds inte för närvarande:


- [Arbetsbelastningshantering](sql-data-warehouse-workload-management.md) – arbetsbelastningsgrupper och klassificerare
- [Säkerhet på radnivå](/sql/relational-databases/security/row-level-security?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) (inklusive tabellvärdesfunktioner)
  - Skicka en [supportbiljett eller rösta för](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040057-ssdt-row-level-security) att få funktionen stödd.
  - Skicka en [supportbiljett eller rösta för](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/39040048-ssdt-support-dynamic-data-masking) att få funktionen stödd.
- Vissa T-SQL-funktioner, till exempel:
   - *WITHIN* GROUP-satsen [i STRING_AGG](/sql/t-sql/functions/string-agg-transact-sql) strängfunktionen.

## <a name="next-steps"></a>Nästa steg

Nu när du har den senaste versionen av SSDT är du redo att [ansluta till](sql-data-warehouse-query-visual-studio.md) DIN SQL-pool.