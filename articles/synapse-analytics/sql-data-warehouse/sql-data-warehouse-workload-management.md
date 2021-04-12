---
title: Arbetsbelastningshantering
description: Vägledning för att implementera arbets belastnings hantering i Azure Synapse Analytics.
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
ms.openlocfilehash: 5640c1e254c4738ab53881544a09808b4894a462
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676977"
---
# <a name="what-is-workload-management"></a>Vad är arbets belastnings hantering?

Att köra blandade arbets belastningar kan medföra resurs utmaningar på upptagna system.  Lösnings arkitekter söker efter olika klassiska data lager aktiviteter (till exempel att läsa in, transformera och fråga data) för att se till att det finns tillräckligt med resurser för att kunna trycka service avtal.  

Den fysiska server isoleringen kan leda till en infrastruktur som är underutnyttjad, förbokad eller i ett tillstånd där cacheminnen ständigt definieras med maskin vara som startar och stoppar.  Ett lyckat hanterings schema för arbets belastningen hanterar resurser, garanterar mycket effektiv resursutnyttjande och maximerar avkastningen på investeringen (ROI).

Ett informations lager arbets belastning avser alla åtgärder som utförs i förhållande till ett informations lager. Djupet och bredden på dessa komponenter är beroende av data lagrets förfallo nivå.  Arbets belastningen för informations lagret omfattar:

- Hela processen med att läsa in data till lagret
- Utföra analys och rapportering av data lager
- Hantera data i informations lagret
- Exportera data från data lagret

Prestanda kapaciteten för ett informations lager bestäms av [data lager enheterna](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Om du vill visa de resurser som har allokerats för alla prestanda profiler, se [minnes-och samtidiga gränser](memory-concurrency-limits.md).
- För att justera kapaciteten kan du [skala upp eller ned](quickstart-scale-compute-portal.md).

## <a name="workload-management-concepts"></a>Koncept för arbets belastnings hantering

Tidigare var du Synapse SQL i Azure Synapse och du hanterade frågeresultaten genom [resurs klasser](resource-classes-for-workload-management.md).  Resurs klasser som tillåts att tilldela minne till en fråga baserat på roll medlemskap.  Den primära utmaningen med resurs klasser är att, när den har kon figurer ATS, inte har någon styrning eller möjlighet att styra arbets belastningen.  

Om du till exempel beviljar ett ad hoc användar roll medlemskap till smallrc tillåts användaren att använda 100% av minnet i systemet.  Med resurs klasser finns det inget sätt att reservera och se till att resurserna är tillgängliga för kritiska arbets belastningar.

Dedikerad arbets belastnings hantering i SQL-pool i Azure Synapse består av tre koncept på hög nivå: [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md), [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md)och [arbets belastnings isolering](sql-data-warehouse-workload-isolation.md).  Med de här funktionerna får du mer kontroll över hur arbets belastningen använder system resurser.

Arbets belastnings klassificering är begreppet att tilldela en begäran till en arbets belastnings grupp och ange prioritets nivåer.  Tidigare genomfördes den här tilldelningen via roll medlemskap med hjälp av [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Den här åtgärden kan nu utföras via [CLASSIFER skapa arbets belastning](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Klassificerings funktionen ger en mer omfattande uppsättning alternativ, till exempel etikett, session och tid för klassificering av begär Anden.

Arbets belastnings prioriteten påverkar i vilken ordning en begäran får åtkomst till resurser.  På ett upptaget system har en begäran med högre prioritet först åtkomst till resurser.  Prioriteten kan också se till att åtkomsten till lås har beställts.

Arbets belastnings isolering reserverar resurser för en arbets belastnings grupp.  Resurser som är reserverade i en arbets belastnings grupp hålls exklusivt för den arbets belastnings gruppen för att säkerställa körningen.  Med arbets belastnings grupper kan du också definiera mängden resurser som tilldelas per begäran, ungefär som resurs klasser gör.  Arbets belastnings grupper ger dig möjlighet att reservera eller lägga till en mängd resurser som en uppsättning begär Anden kan förbruka.  Slutligen är arbets belastnings grupper en mekanism för att tillämpa regler, t. ex. tids gräns för frågor, för förfrågningar.  

## <a name="next-steps"></a>Nästa steg

- Mer information om arbets belastnings klassificering finns i avsnittet om [arbets belastnings klassificering](sql-data-warehouse-workload-classification.md).  
- Mer information om arbets belastnings isolering finns i [arbets belastnings isolering](sql-data-warehouse-workload-isolation.md).  
- Mer information om arbets belastnings prioritet finns i [arbets belastnings prioritet](sql-data-warehouse-workload-importance.md).  
- Mer information om övervakning av arbets belastnings hantering finns i [hanteringsportal övervakning av arbets belastningar](sql-data-warehouse-workload-management-portal-monitor.md).  
