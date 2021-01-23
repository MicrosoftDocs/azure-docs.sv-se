---
title: Omvandla resurs klass till en arbets belastnings grupp
description: Lär dig hur du skapar en arbets belastnings grupp som liknar en resurs klass i en dedikerad SQL-pool.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 1207f4856882d8aa0e6d1e41712071536bfecf29
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728564"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Konvertera resurs klasser till arbets belastnings grupper

Arbets belastnings grupper tillhandahåller en mekanism för att isolera och innehålla system resurser.  Dessutom kan du med arbets belastnings grupper ange körnings regler för de begär Anden som körs i dem.  En regel för körning av tids gräns tillåter att omsluts frågor avbryts utan att användaren tillfrågas.  Den här artikeln förklarar hur du tar en befintlig resurs klass och skapar en arbets belastnings grupp med en liknande konfiguration.  Dessutom läggs en valfri tids gräns regel för frågor till.

> [!NOTE]
> Mer information om hur du använder arbets belastnings grupper och resurs klasser finns i avsnittet om hur du [blandar resurs klass tilldelningar med klassificerare](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) i konceptet för [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md) .

## <a name="understanding-the-existing-resource-class-configuration"></a>Förstå den befintliga resurs klass konfigurationen

Arbets belastnings grupper kräver en parameter `REQUEST_MIN_RESOURCE_GRANT_PERCENT` med namnet som anger procent andelen totala system resurser som allokeras per begäran.  Resursallokering görs för [resurs klasser](resource-classes-for-workload-management.md#what-are-resource-classes) genom att allokera samtidiga platser.  Använd sys.dm_workload_management_workload_groups_stats DMV för att avgöra värdet som ska anges för `REQUEST_MIN_RESOURCE_GRANT_PERCENT` <link tbd> .  Frågan nedan returnerar exempelvis ett värde som kan användas för `REQUEST_MIN_RESOURCE_GRANT_PERCENT` parametern för att skapa en arbets belastnings grupp som liknar staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Arbets belastnings grupper arbetar baserat på procent av övergripande system resurser.  

Eftersom arbets belastnings grupper fungerar baserat på procent andelen av övergripande system resurser, när du skalar upp och ned, ändras procent andelen resurser som har allokerats till statiska resurs klasser i förhållande till de totala system resurserna.  Staticrc40 vid DW1000c allokerar till exempel 19,2% av de totala system resurserna.  Vid DW2000c tilldelas 9,6%.  Den här modellen är liknande om du vill skala upp för samtidighet jämfört med att allokera fler resurser per begäran.

## <a name="create-workload-group"></a>Skapa arbets belastnings grupp

Med känd kan `REQUEST_MIN_RESOURCE_GRANT_PERCENT` du använda syntaxen skapa arbets belastnings grupp <link> för att skapa arbets belastnings gruppen.  Du kan också ange en `MIN_PERCENTAGE_RESOURCE` som är större än noll för att isolera resurser för arbets belastnings gruppen.  Du kan också ange `CAP_PERCENTAGE_RESOURCE` mindre än 100 för att begränsa den mängd resurser som arbets belastnings gruppen kan använda.  

Med mediumrc som grund för ett exempel, anger koden nedan för `MIN_PERCENTAGE_RESOURCE` att tilldela 10% av system resurserna till `wgDataLoads` och garanterar att en fråga kan köra alla tider.  Dessutom `CAP_PERCENTAGE_RESOURCE` är inställt på 40% och begränsar den här arbets belastnings gruppen till fyra samtidiga begär Anden.  Genom att ange `QUERY_EXECUTION_TIMEOUT_SEC` parametern till 3600 avbryts alla frågor som körs i mer än en timme automatiskt.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 10
 ,MIN_PERCENTAGE_RESOURCE = 10
 ,CAP_PERCENTAGE_RESOURCE = 40
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Skapa klassificeraren

Tidigare genomfördes mappningen av frågor till resurs klasser med [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Om du vill uppnå samma funktioner och mappa begär anden till arbets belastnings grupper använder du KLASSIFICERINGs-syntaxen [skapa arbets belastning](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Med sp_addrolemember får du bara mappa resurser till en begäran baserat på en inloggning.  En klassificerare innehåller ytterligare alternativ förutom inloggning, till exempel:
    - etikett
    - session
    - tid i exemplet nedan tilldelar frågor från `AdfLogin` inloggningen som också har [alternativ etiketten](sql-data-warehouse-develop-label.md)  inställd till `factloads` den arbets belastnings grupp som du `wgDataLoads` skapade ovan.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Testa med en exempel fråga

Nedan visas en exempel fråga och en DMV-fråga för att se till att arbets belastnings gruppen och klassificeraren är korrekt konfigurerade.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Nästa steg

- [Arbets belastnings isolering](sql-data-warehouse-workload-isolation.md)
- [Så här skapar du en arbets belastnings grupp](quickstart-configure-workload-isolation-tsql.md)
- [Skapa klassificering av arbets belastning (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql??view=azure-sqldw-latest&preserve-view=true)
- [SKAPA arbets BELASTNINGs grupp (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest&preserve-view=true)
