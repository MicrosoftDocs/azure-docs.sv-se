---
title: Query Performance Insight
description: Fråga prestanda övervakningen identifierar de mest processor krävande och långvariga frågorna för enkla och grupperade databaser i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 1/14/2021
ms.openlocfilehash: db24f280f66e567572821297cfc9bb9b1e19743b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222351"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Query Performance Insight för Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Query Performance Insight ger intelligent frågekörning för enskilda databaser och pooldatabaser. Verktyget hjälper dig att identifiera de resurser i arbetsbelastningen som har högst resursanvändning och som är mest tidskrävande. På så sätt blir det enklare att hitta frågorna som kan optimera arbetsbelastningens prestanda, samt att använda den resurs som du betalar för så effektivt som möjligt. Query Performance Insight hjälper dig att ägna mindre tid åt att felsöka databas prestanda genom att tillhandahålla:

* Djupare insikt i förbrukningen av dina databas resurser (DTU)
* Information om de främsta databas frågorna efter processor, varaktighet och antal körningar (möjliga justerings förslag för prestanda förbättringar)
* Möjlighet att öka detalj nivån till information om en fråga för att visa frågetexten och historik för resursutnyttjande
* Anteckningar som visar prestanda rekommendationer från [databas rådgivare](database-advisor-implement-performance-recommendations.md)

![Query Performance Insight](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Förutsättningar

För Query Performance Insight krävs att [frågearkivet](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) är aktivt på databasen. Den aktive ras automatiskt för alla databaser i Azure SQL Database som standard. Om Frågearkivet inte körs uppmanas du i Azure Portal att aktivera det.

> [!NOTE]
> Om meddelandet "Frågearkivet har inte kon figurer ATS korrekt på den här databasen" visas i portalen, se [optimera konfigurationen för frågearkivet](#optimize-the-query-store-configuration).

## <a name="permissions"></a>Behörigheter

Du behöver följande [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) för att kunna använda Query Performance Insight:

* Behörigheter för **läsare**, **ägare**, **deltagare**, **SQL DB-deltagare** eller **SQL Server deltagare** krävs för att visa de främsta resurs krävande frågorna och diagrammen.
* Behörigheter för **ägare**, **deltagare**, **SQL DB-deltagare** eller **SQL Server deltagare** krävs för att Visa frågetext.

## <a name="use-query-performance-insight"></a>Använda Query Performance Insight

Query Performance Insight är enkelt att använda:

1. Öppna [Azure Portal](https://portal.azure.com/) och leta upp en databas som du vill undersöka.
2. Öppna **intelligenta prestanda** Query Performance Insight på menyn på den vänstra sidan  >  .
  
   ![Query Performance Insight på menyn](./media/query-performance-insight-use/tile.png)

3. På den första fliken granskar du listan över de främsta resurs krävande frågorna.
4. Välj en enskild fråga om du vill visa information om den.
5. Öppna prestanda rekommendationer för **intelligent prestanda**  >   och kontrol lera om det finns några prestanda rekommendationer. Mer information om inbyggda prestanda rekommendationer finns [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md).
6. Använd skjutreglage eller zooma ikoner för att ändra det observerade intervallet.

   ![Instrument panel för prestanda](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> För att Azure SQL Database ska kunna återge informationen i Query Performance Insight måste Query Store samla in några timmar data. Om databasen inte har någon aktivitet eller om Frågearkivet inte var aktiv under en viss period, kommer diagrammet att vara tomt när Query Performance Insight visar tidsintervallet. Du kan aktivera Query Store när som helst om det inte körs. Mer information finns i [metod tips för Query Store](/sql/relational-databases/performance/best-practice-with-the-query-store).
>

För rekommendationer för databas prestanda väljer du [rekommendationer](database-advisor-implement-performance-recommendations.md) på bladet Query Performance Insight navigering.

![Fliken rekommendationer](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Granska de mest processor krävande frågorna

Som standard visar Query Performance Insight de fem främsta processor krävande frågorna första gången du öppnar den.

1. Markera eller avmarkera enskilda frågor för att inkludera eller exkludera dem från diagrammet med hjälp av kryss rutor.

   Den översta raden visar den totala DTU-procenten för databasen. Staplarna visar processor procent som de valda frågorna förbrukade under det valda intervallet. Om t. ex. den **senaste veckan** har marker ATS representerar varje stapel en dag.

   ![Vanligaste frågorna](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > Den DTU-rad som visas sammanställs till ett maximalt förbruknings värde inom en timmes period. Den är avsedd för en hög nivå jämförelse endast med statistik för körning av frågor. I vissa fall kan DTU-användningen verka för hög jämfört med de frågor som körs, men det kanske inte är fallet.
   >
   > Om t. ex. en fråga överutnyttjade ut DTU till 100% under bara några minuter, visar DTU-raden i Query Performance Insight hela timmen av förbrukningen som 100% (resultatet av det maximala sammanlagda värdet).
   >
   > Om du vill ha en bättre jämförelse (upp till en minut) kan du skapa ett anpassat DTU-användnings diagram:
   >
   > 1. Välj **Azure SQL Database**  >  **övervakning** i Azure Portal.
   > 2. Välj **Mått**.
   > 3. Välj **+ Lägg till diagram**.
   > 4. Välj procentuellt DTU i diagrammet.
   > 5. Välj dessutom de **senaste 24 timmarna** på den övre vänstra menyn och ändra den till en minut.
   >
   > Använd det anpassade DTU-diagrammet med en mer detaljerad detalj nivå för att jämföra med diagrammet för frågekörningen.

   Det nedre rutnätet visar samlad information för de synliga frågorna:

   * Fråge-ID, som är en unik identifierare för frågan i databasen.
   * PROCESSOR per fråga under ett observerbart intervall som är beroende av agg regerings funktionen.
   * Varaktighet per fråga, vilket även beror på agg regerings funktionen.
   * Totalt antal körningar för en speciell fråga.

2. Om dina data blir inaktuella väljer du knappen **Uppdatera** .

3. Använd skjutreglage och zoomnings knappar för att ändra observations intervallet och undersöka förbruknings toppar:

   ![Skjutreglage och zoomnings knappar för att ändra intervallet](./media/query-performance-insight-use/zoom.png)

4. Du kan också välja fliken **anpassad** om du vill anpassa vyn för:

   * Mått (processor, varaktighet, antal körningar).
   * Tidsintervall (senaste 24 timmarna, föregående vecka eller föregående månad).
   * Antal frågor.
   * Agg regerings funktion.
  
   ![Anpassad flik](./media/query-performance-insight-use/custom-tab.png)
  
5. Välj knappen **gå >** för att se den anpassade vyn.

   > [!IMPORTANT]
   > Query Performance Insight är begränsat till att visa de 5-20 främsta frågorna, beroende på ditt val. Databasen kan köra många fler frågor utöver de överst som visas, och dessa frågor kommer inte att ingå i diagrammet.
   >
   > Det kan finnas en databas arbets belastnings typ där många mindre frågor, utöver de mest visade, körs ofta och använder majoriteten av DTU. Dessa frågor visas inte i prestanda diagrammet.
   >
   > En fråga kan till exempel ha förbrukat en stor mängd DTU för ett tag, trots att den totala förbrukningen i den observerade perioden är mindre än de andra Top-krävande frågorna. I sådana fall visas inte resursanvändningen i diagrammet för resursutnyttjande.
   >
   > Om du behöver förstå de vanligaste körningarna utanför Query Performance Insight bör du överväga att använda [Azure SQL-analys](../../azure-monitor/insights/azure-sql.md) för avancerad övervakning av databas prestanda och fel sökning.
   >

## <a name="view-individual-query-details"></a>Visa information om enskilda frågor

Så här visar du Frågedetaljer:

1. Välj en fråga i listan över de vanligaste frågorna.

    ![Lista över vanligaste frågor](./media/query-performance-insight-use/details.png)

   En detaljerad vy öppnas. Det visar processor förbrukning, varaktighet och antalet körningar över tid.

2. Välj diagram funktioner för mer information.

   * Det översta diagrammet visar en linje med den övergripande procent andelen för databas-DTU. Staplarna är den CPU-procent som den valda frågan har förbrukat.
   * Det andra diagrammet visar den totala varaktigheten för den valda frågan.
   * Det nedre diagrammet visar det totala antalet körningar av den valda frågan.

   ![Frågedetaljer](./media/query-performance-insight-use/query-details.png)

3. Du kan också använda skjutreglage, använda zoomnings knappar eller välja **Inställningar** för att anpassa hur frågedata visas, eller för att välja ett annat tidsintervall.

   > [!IMPORTANT]
   > Query Performance Insight fångar inte in några DDL-frågor. I vissa fall kanske det inte fångar alla ad hoc-frågor.
   >

## <a name="review-top-queries-per-duration"></a>Granska de vanligaste frågorna per varaktighet

Två mått i Query Performance Insight kan hjälpa dig att hitta potentiella Flask halsar: varaktighet och antal körningar.

Tids krävande frågor har störst potential för att låsa resurser längre, blockera andra användare och begränsa skalbarheten. De är också de bästa deltagarna för optimering. Mer information finns i [förstå och lösa problem med att blockera Azure SQL](understand-resolve-blocking.md).

Så här identifierar du tids krävande frågor:

1. Öppna fliken **anpassad** i Query Performance Insight för den valda databasen.
2. Ändra måtten till **duration**.
3. Välj antal frågor och observations intervallet.
4. Välj agg regerings funktion:

   * **Sum** lägger till all frågans körnings tid för hela observations intervallet.
   * **Max** söker efter frågor där körnings tiden är max för hela observations intervallet.
   * **AVG** hittar den genomsnittliga körnings tiden för alla fråge körningar och visar de översta för dessa genomsnitt.

   ![Frågans varaktighet](./media/query-performance-insight-use/top-duration.png)

5. Välj knappen **gå >** för att se den anpassade vyn.

   > [!IMPORTANT]
   > När du justerar frågevyn uppdateras inte DTU-linjen. DTU-raden visar alltid det maximala förbrukning svärdet för intervallet.
   >
   > Om du vill förstå databasen DTU-förbrukning med mer information (upp till en minut) kan du överväga att skapa ett anpassat diagram i Azure Portal:
   >
   > 1. Välj **Azure SQL Database**  >  **övervakning**.
   > 2. Välj **Mått**.
   > 3. Välj **+ Lägg till diagram**.
   > 4. Välj procentuellt DTU i diagrammet.
   > 5. Välj dessutom de **senaste 24 timmarna** på den övre vänstra menyn och ändra den till en minut.
   >
   > Vi rekommenderar att du använder det anpassade DTU-diagrammet för att jämföra med resultat diagrammet för frågor.
   >

## <a name="review-top-queries-per-execution-count"></a>Granska de vanligaste frågorna per antal körningar

Ett användar program som använder-databasen kan bli långsamt, även om ett stort antal körningar kanske inte påverkar själva databasen och resurserna har låg belastning.

I vissa fall kan ett högt antal körnings försök leda till fler nätverks fördröjningar. Tur och svar påverkar prestanda. De är underkastade nätverks svars tid och till underordnad server svars tid.

Till exempel har många data drivna webbplatser stor åtkomst till databasen för varje Användarbegäran. Även om anslutningspoolen bidrar till kan den ökade nätverks trafiken och belastningen på servern sakta prestanda. I allmänhet ska du hålla tur och ordning i tur och ordning till ett minimum.

Så här identifierar du vanliga frågor ("chatty"):

1. Öppna fliken **anpassad** i Query Performance Insight för den valda databasen.
2. Ändra måtten till **antal körningar**.
3. Välj antal frågor och observations intervallet.
4. Välj knappen **gå >** för att se den anpassade vyn.

   ![Antal frågekörning](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Förstå prestanda justerings anteckningar

Medan du utforskar din arbets belastning i Query Performance Insight kan du märka ikoner med en lodrät linje ovanpå diagrammet.

Dessa ikoner är kommentarer. De visar prestanda rekommendationer från [Azure SQL Database Advisor](database-advisor-implement-performance-recommendations.md). Genom att hovra över en anteckning kan du få sammanfattande information om prestanda rekommendationer.

   ![Fråga-anteckning](./media/query-performance-insight-use/annotation.png)

Om du vill veta mer eller tillämpa rekommendationen för rådgivare väljer du ikonen för att öppna information om den rekommenderade åtgärden. Om detta är en aktiv rekommendation kan du använda den direkt från portalen.

   ![Information om frågans anteckningar](./media/query-performance-insight-use/annotation-details.png)

I vissa fall, på grund av zoomnings nivån, är det möjligt att anteckningarna nära varandra komprimeras till en enda anteckning. Query Performance Insight representerar detta som en grupp antecknings ikon. Om du väljer grupp antecknings ikonen öppnas ett nytt blad med en lista över anteckningarna.

Att korrelera frågor och prestanda justerings åtgärder kan hjälpa dig att bättre förstå din arbets belastning.

## <a name="optimize-the-query-store-configuration"></a>Optimera konfigurationen för Frågearkivet

När du använder Query Performance Insight kan du se följande fel meddelanden i Frågearkivet:

* "Frågearkivet har inte kon figurer ATS korrekt på den här databasen. Klicka här om du vill veta mer. "
* "Frågearkivet har inte kon figurer ATS korrekt på den här databasen. Klicka här om du vill ändra inställningarna. "

Dessa meddelanden visas vanligt vis när Query Store inte kan samla in nya data.

Det första fallet inträffar när Frågearkivet är i skrivskyddat läge och parametrarna är optimalt inställda. Du kan åtgärda detta genom att öka storleken på data lagret eller genom att ta bort Frågearkivet. (Om du rensar Frågearkivet går all tidigare insamlad telemetri förlorad.)

   ![Information om frågearkivet](./media/query-performance-insight-use/qds-off.png)

Det andra fallet inträffar när Frågearkivet inte är aktiverat, eller om parametrarna inte är optimalt inställda. Du kan ändra principen för kvarhållning och insamling, och även aktivera Frågearkivet, genom att köra följande kommandon som tillhandahålls från [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) eller Azure Portal.

### <a name="recommended-retention-and-capture-policy"></a>Rekommenderad kvarhållning och avbildnings princip

Det finns två typer av bevarande principer:

* **Storlek baserad**: om den här principen är inställd på **Auto** rensas data automatiskt när den maximala storleken uppnås i närheten.
* **Tidsbaserad**: som standard är den här principen inställt på 30 dagar. Om Query Store tar slut på utrymme, tar den bort information som är äldre än 30 dagar.

Du kan ställa in avbildnings principen på:

* **Alla**: Query Store fångar alla frågor.
* **Auto**: Query Store ignorerar ovanliga frågor och frågor med obetydlig kompilering och körnings tid. Tröskelvärden för antal körningar, kompileringstid och varaktighet för körning fastställs internt. Detta är standard alternativet.
* **Ingen**: Query Store slutar att samla in nya frågor, men körnings statistik för redan infångade frågor samlas fortfarande in.

Vi rekommenderar att du ställer in alla principer för att **automatiskt** och rensa principen till 30 dagar genom att köra följande kommandon från [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) eller Azure Portal. (Ersätt `YourDB` med databasens namn.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Öka storleken på Frågearkivet genom att ansluta till en databas via [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) eller Azure Portal och köra följande fråga. (Ersätt `YourDB` med databasens namn.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

När du använder de här inställningarna kommer du slutligen att göra Frågearkivet samla in telemetri för nya frågor. Om du vill att Query Store ska fungera direkt kan du välja att rensa Frågearkivet genom att köra följande fråga via SSMS eller Azure Portal. (Ersätt `YourDB` med databasens namn.)

> [!NOTE]
> Om du kör följande fråga raderas alla tidigare insamlade telemetri i Frågearkivet.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Nästa steg

Överväg att använda [Azure SQL-analys](../../azure-monitor/insights/azure-sql.md) för avancerad prestanda övervakning av en stor flotta av databaser med enkel och pool, elastiska pooler, hanterade instanser och instans databaser.