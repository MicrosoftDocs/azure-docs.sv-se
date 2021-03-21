---
title: Machine Learning Services i Azure SQL-hanterad instans
description: Den här artikeln innehåller en översikt eller Machine Learning Services i Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: garyericson
ms.author: garye
ms.reviewer: sstein, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: 94495144c64b3770995a5f67e9129b3ba86e741e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104599569"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Machine Learning Services i Azure SQL-hanterad instans

Machine Learning Services är en funktion i Azure SQL-hanterad instans som tillhandahåller maskin inlärning i databasen med stöd för både python-och R-skript. Funktionen innehåller Microsoft python-och R-paket för förutsägelse analys och maskin inlärning med höga prestanda. Relations data kan användas i skript genom lagrade procedurer, T-SQL-skript som innehåller python-eller R-uttryck, eller python-eller R-kod som innehåller T-SQL.

## <a name="what-is-machine-learning-services"></a>Vad är Machine Learning Services?

Machine Learning Services i Azure SQL-hanterad instans kan du köra python-och R-skript i-databasen. Du kan använda den för att förbereda och rensa data, utföra funktions teknik och träna, utvärdera och distribuera maskin inlärnings modeller i en databas. Funktionen kör skripten där data finns och eliminerar överföringen av data över nätverket till en annan server.

Använd Machine Learning Services med R/python-stöd i Azure SQL-hanterad instans för att:

- **Kör r-och Python-skript för förberedelse av data och generell användning av data bearbetning** – du kan nu hämta R/Python-skript till Azure SQL-hanterad instans där dina data finns, i stället för att behöva flytta ut data till en annan server för att köra R-och Python-skript. Du kan eliminera behovet av data förflyttning och relaterade problem som rör svars tid, säkerhet och efterlevnad.

- **Träna maskin inlärnings modeller i databasen** – du kan träna modeller med alla algoritmer för öppen källkod. Du kan enkelt skala din utbildning till hela data uppsättningen i stället för att förlita dig på exempel data uppsättningar som hämtas från databasen.

- **Distribuera modeller och skript till produktion i lagrade procedurer** – skripten och de tränade modellerna kan användas helt enkelt genom att de bäddas in i T-SQL-lagrade procedurer. Appar som ansluter till en hanterad Azure SQL-instans kan dra nytta av förutsägelser och intelligens i dessa modeller genom att bara anropa en lagrad procedur. Du kan också använda den inbyggda T-SQL PREDICT-funktionen för att operationalisera modeller för snabba resultat i scenarier med real tids resultat i real tid.

Bas distributioner av python och R ingår i Machine Learning Services. Du kan installera och använda paket och ramverk med öppen källkod, till exempel PyTorch, TensorFlow och scikit-lär, förutom Microsoft-paketen [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) och [microsoftml](/sql/machine-learning/python/ref-py-microsoftml) för python, och [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [microsoftml](/sql/machine-learning/r/ref-r-microsoftml), [OLAP](/sql/machine-learning/r/ref-r-olapr)och [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils) för R.

## <a name="how-to-enable-machine-learning-services"></a>Så här aktiverar du Machine Learning Services

Du kan aktivera Machine Learning Services i Azure SQL-hanterad instans genom att aktivera utöknings barhet med följande SQL-kommandon (SQL-hanterad instans startas om och är inte tillgänglig i några sekunder):

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

Mer information om hur det här kommandot påverkar SQL-hanterade instans resurser finns i [resurs styrning](machine-learning-services-differences.md#resource-governance).

### <a name="enable-machine-learning-services-in-a-failover-group"></a>Aktivera Machine Learning Services i en grupp för redundans

I en [failover-grupp](failover-group-add-instance-tutorial.md)replikeras inte system databaser till den sekundära instansen (mer information finns i [begränsningar av failover-grupper](../database/auto-failover-group-overview.md#limitations-of-failover-groups) ).

Om den hanterade instansen som du använder ingår i en failover-grupp gör du följande:

- Kör `sp_configure` `RECONFIGURE` kommandona och på varje instans av gruppen redundans för att aktivera Machine Learning Services.

- Installera R/python-biblioteken i en användar databas i stället för huvud databasen.

## <a name="next-steps"></a>Nästa steg

- Se [viktiga skillnader från SQL Server Machine Learning Services](machine-learning-services-differences.md).
- Information om hur du använder python i Machine Learning Services finns i [köra Python-skript](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Information om hur du använder R i Machine Learning Services finns i [Kör R-skript](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true).
- Mer information om Machine Learning på andra SQL-plattformar finns i [dokumentationen för SQL Machine Learning](/sql/machine-learning/index).
