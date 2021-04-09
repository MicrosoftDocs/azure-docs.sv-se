---
title: 'Självstudie: övervaka och finjustera – Azure Database for PostgreSQL-enskild server'
description: Den här självstudien vägleder dig genom övervakning och justering i Azure Database for PostgreSQL-enskild server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: tutorial
ms.date: 5/6/2019
ms.openlocfilehash: a12068259d82e833826bcac5e6c58059fb51c56c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336987"
---
# <a name="tutorial-monitor-and-tune-azure-database-for-postgresql---single-server"></a>Självstudie: övervaka och finjustera Azure Database for PostgreSQL-enskild server

Azure Database for PostgreSQL har funktioner som hjälper dig att förstå och förbättra din serverprestanda. I den här självstudien får du lära dig hur man
> [!div class="checklist"]
> * aktiverar insamling av fråge- och väntestatistik
> * får åtkomst till och använder insamlade data
> * visar frågeprestanda- och väntestatistik över tid
> * analyserar en databas för att få prestandarekommendationer
> * Tillämpa prestandarekommendationer

## <a name="prerequisites"></a>Förutsättningar
Du behöver en Azure Database for PostgreSQL-server med PostgreSQL version 9.6 eller 10. Du kan följa stegen i [självstudien för att skapa en server](tutorial-design-database-using-azure-portal.md) för att skapa en server.

> [!IMPORTANT]
> **Query Store**, **Query Performance Insight** och **prestandarekommendation** finns i en allmänt tillgänglig förhandsversion.

## <a name="enabling-data-collection"></a>Aktivera datainsamling
I [Query Store](concepts-query-store.md) finns en historik över fråge- och väntestatistik på din server och den lagras i databasen **azure_sys** på servern. Det är en valbar funktion. För att aktivera den:

1. Öppna Azure-portalen.

2. Välj din Azure Database for PostgreSQL-server.

3. Välj **Serverparametrar** i avdelningen **Inställningar** på menyn till vänster.

4. Ange **pg_qs.query_capture_mode** till **HÖGSTA** för att börja samla in frågeprestandadata. Ange **pgms_wait_sampling.query_capture_mode** till **ALLA** för att börja samla in väntestatistik. Spara.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-store-parameters.png" alt-text="Query Store-serverparametrar":::

5. Vänta 20 minuter för den första databatchen ska hinna sparas i databasen **azure_sys**.


## <a name="performance-insights"></a>Prestandainsikter
På [Query Performance Insight](concepts-query-performance-insight.md)-vyn i Azure-portalen visas visualiseringar av nyckelinformation från Query Store. 

1. På portalsidan för din Azure Database for PostgreSQL-server väljer du **Information om frågeprestanda** under avdelningen **Support och felsökning** i menyn till vänster.

2. På fliken **Långvariga frågor** visas de 5 främsta frågorna efter genomsnittlig varaktighet per körning, aggregerat i 15-minutersintervall. 
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-landing-page.png" alt-text="Landningssida för Query Performance Insight":::

   Du kan visa fler frågor genom att välja dem i den nedrullningsbara listrutan för **Antal frågor**. Diagrammets färger kan ändras för ett specifikt fråge-ID när du gör detta.

3. Du kan klicka och dra i diagrammet för att begränsa till ett specifikt tidsfönster.

4. Använd in- och utzoomningsikonerna för att visa en kortare respektive längre tidsperiod.

5. Titta i tabellen nedanför diagrammet för att få mer information om de långvariga frågorna i det tidsfönstret.

6. Välj fliken **Väntestatistik** för att visa motsvarande visualiseringar av vänttillfällen på servern.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/query-performance-insight-wait-statistics.png" alt-text="Väntestatistik för Query Performance Insight":::

### <a name="permissions"></a>Behörigheter
Behörighet för **ägare** eller **deltagare** krävs för att visa texten för frågorna i Query Performance Insight. **Läsaren** kan visa diagram och tabeller men inte frågetext.


## <a name="performance-recommendations"></a>Prestandarekommendationer
Funktionen [Prestandarekommendationer](concepts-performance-recommendations.md) analyserar arbetsbelastningar på din server för att identifiera index med potential för att förbättra prestandan.

1. Öppna **Prestandarekommendationer** från avdelningen **Support och felsökning** i menyraden på Azure-portalsidan för din PostgreSQL-server.
   
   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-landing-page.png" alt-text="Landningssida för prestandarekommendationer":::

2. Välj **Analysera** och välj en databas. Detta startar analysen.

3. Det kan ta flera minuter beroende på arbetsbelastningen. När analysen är klar, visas ett meddelande i portalen.

4. I fönstret **Prestandarekommendationer** visas en lista över rekommendationer om sådana hittas. 

5. En rekommendation visar information om relevant **databas**, **tabell**, **kolumn** och **indexstorlek**.

   :::image type="content" source="./media/tutorial-performance-intelligence/performance-recommendations-result.png" alt-text="Resultat av prestandarekommendationer":::

6. Om du vill implementera rekommendationen kopierar du frågetexten och kör den från önskad klient.

### <a name="permissions"></a>Behörigheter
Behörighet som **ägare** eller **deltagare** krävs för att köra analys med funktionen Prestandarekommendationer.

## <a name="clean-up-resources"></a>Rensa resurser

I föregående steg skapade du Azure-resurser i en Server grupp. Om du inte tror att du behöver dessa resurser i framtiden tar du bort Server gruppen. Tryck på knappen *ta bort* på sidan *Översikt* för Server gruppen. När du uppmanas till ett popup-fönster bekräftar du namnet på Server gruppen och klickar på knappen slutlig *borttagning* .

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> Lär dig mer om [övervakning och justering](concepts-monitoring.md) i Azure Database for PostgreSQL.