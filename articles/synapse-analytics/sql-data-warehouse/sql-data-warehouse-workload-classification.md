---
title: Arbets belastnings klassificering för dedikerad SQL-pool
description: Vägledning för att använda klassificering för att hantera fråga samtidighets-, prioritets-och beräknings resurser för dedikerad SQL-pool i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7cd3619aa60f1bd8ac13ff767857b44348989285
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98678431"
---
# <a name="workload-classification-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Arbets belastnings klassificering för dedikerad SQL-pool i Azure Synapse Analytics

Den här artikeln beskriver processen för arbets belastnings klassificering för att tilldela en arbets belastnings grupp och prioritet för inkommande begär Anden med dedikerade SQL-pooler i Azure Synapse.

## <a name="classification"></a>Klassificering

> [!Video https://www.youtube.com/embed/QcCRBAhoXpM]

Klassificeringen av arbets belastnings hantering gör att arbets belastnings principer kan tillämpas på begär Anden genom att tilldela [resurs klasser](resource-classes-for-workload-management.md#what-are-resource-classes) och [prioritet](sql-data-warehouse-workload-importance.md).

Det finns många sätt att klassificera data lager arbets belastningar, den enklaste och mest vanliga klassificeringen är load och fråga. Du läser in data med INSERT-, Update-och Delete-instruktioner.  Du frågar data med hjälp av väljer. En data lager lösning har ofta en arbets belastnings princip för belastnings aktiviteter, till exempel att tilldela en högre resurs klass till fler resurser. En annan arbets belastnings princip kan gälla för frågor, till exempel lägre prioritet jämfört med inläsnings aktiviteter.

Du kan också underklassificera dina belastnings-och fråga-arbetsbelastningar. Med underindelning får du mer kontroll över dina arbets belastningar. Fråge arbets belastningar kan till exempel bestå av kub uppdateringar, frågor om instrument paneler eller ad hoc-frågor. Du kan klassificera var och en av dessa frågor arbets belastningar med olika resurs klasser eller prioritets inställningar. Belastningen kan också dra nytta av underindelning. Stora transformeringar kan tilldelas till större resurs klasser. Högre prioritet kan användas för att se till att viktiga försäljnings data är inläsning före väder data eller sociala data flöden.

Alla instruktioner klassificeras inte eftersom de inte kräver resurser eller är viktiga för att påverka körningen.  DBCC-kommandon, BEGIN, COMMIT och ROLLBACK TRANSACTION-instruktioner klassificeras inte.

## <a name="classification-process"></a>Klassificerings process

Klassificering för dedikerad SQL-pool uppnås idag genom att tilldela användare till en roll som har en motsvarande resurs klass som är tilldelad till den med hjälp av [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Möjligheten att karakterisera begär Anden utöver en inloggning till en resurs klass är begränsad till den här funktionen. En bättre metod för klassificering är nu tillgänglig med [klassificerings metoden skapa arbets belastning](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) .  Med den här syntaxen kan dedikerade SQL-pool användare tilldela prioritet och hur mycket system resurser som tilldelas en begäran via `workload_group` parametern.

> [!NOTE]
> Klassificeringen utvärderas per begäran. Flera begär anden i en enda session kan klassificeras på olika sätt.

## <a name="classification-weighting"></a>Klassificerings viktning

Som en del av klassificerings processen är viktningen på plats för att avgöra vilken arbets belastnings grupp som tilldelas.  Viktningen ser ut så här:

|Klassificerings parameter |Vikt   |
|---------------------|---------|
|MEMBERNAME: ANVÄNDARE      |64       |
|MEMBERNAME: ROLL      |32       |
|WLM_LABEL            |16       |
|WLM_CONTEXT          |8        |
|START_TIME/END_TIME  |4        |

`membername`Parametern är obligatorisk.  Men om det angivna membername är en databas användare i stället för en databas roll, är viktningen för användaren högre och därmed att klassificeraren väljs.

Om en användare är medlem i flera roller med olika resurs klasser tilldelade eller matchade i flera klassificerare får användaren den högsta resurs klass tilldelningen.  Detta beteende är konsekvent med tilldelnings beteendet för den befintliga resurs klassen.

## <a name="system-classifiers"></a>System klassificerare

Arbets belastnings klassificeringen har system arbets belastnings klassificerare. System-klassificeraren mappar befintliga roll medlemskap i resurs klass till resurs klass resurs tilldelningar med normal prioritet. Det går inte att släppa system klassificerare. Om du vill visa system klassificerare kan du köra nedanstående fråga:

```sql
SELECT * FROM sys.workload_management_workload_classifiers where classifier_id <= 12
```

## <a name="mixing-resource-class-assignments-with-classifiers"></a>Blanda resurs klass tilldelningar med klassificerare

System klassificerare som skapats för din räkning ger en enkel sökväg för att migrera till arbets belastnings klassificering. Genom att använda mappningar av resurs klass rollen med klassificerings prioritet kan det leda till att du börjar med att skapa nya klassificerare som är viktiga.

Föreställ dig följande scenario:

- Ett befintligt informations lager har en databas användare som tilldelats rollen largerc resurs klass. Tilldelningen av resurs klassen utfördes med sp_addrolemember.
- Data lagret har nu uppdaterats med hantering av arbets belastning.
- För att testa den nya klassificerings syntaxen, databas rollen DBARole (som DBAUser är medlem i), har en klassificerare som skapats för dem att mappa dem till mediumrc och hög prioritet.
- När DBAUser loggar in och kör en fråga, tilldelas frågan till largerc. Eftersom en användare prioriteras över ett roll medlemskap.

För att under lätta fel sökningen av fel klassificering rekommenderar vi att du tar bort mappningar av resurs klass roller när du skapar arbets belastnings klassificerare.  Koden nedan returnerar befintliga roll medlemskap i resurs klass.  Kör [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för varje medlems namn som returneras från motsvarande resurs klass.

```sql
SELECT  r.name AS [Resource Class]
,       m.name AS membername
FROM    sys.database_role_members rm
JOIN    sys.database_principals AS r ON rm.role_principal_id = r.principal_id
JOIN    sys.database_principals AS m ON rm.member_principal_id = m.principal_id
WHERE   r.name IN ('mediumrc','largerc','xlargerc','staticrc10','staticrc20','staticrc30','staticrc40','staticrc50','staticrc60','staticrc70','staticrc80');

--for each row returned run
sp_droprolemember '[Resource Class]', membername
```

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du skapar en klassificerare finns i [create klassificerare för arbets belastning (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  
- Se snabb start för hur du skapar en arbets belastnings klassificering [skapa en arbets belastnings klassificering](quickstart-create-a-workload-classifier-tsql.md).
- Se instruktions artiklar för att [Konfigurera arbets belastnings prioritet](sql-data-warehouse-how-to-configure-workload-importance.md) och hur du [hanterar och övervakar arbets belastnings hantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Se [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att visa frågor och tilldelade prioritet.
