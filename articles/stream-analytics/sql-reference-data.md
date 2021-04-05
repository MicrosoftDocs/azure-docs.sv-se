---
title: Använda SQL Database referens data i ett Azure Stream Analytics jobb
description: Den här artikeln beskriver hur du använder en SQL Database som referens data inmatning för ett Azure Stream Analytics jobb i Azure Portal och i Visual Studio.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/29/2019
ms.openlocfilehash: e7d16de8a7a5c6f5353d64e25580b19845ce96c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016413"
---
# <a name="use-reference-data-from-a-sql-database-for-an-azure-stream-analytics-job"></a>Använda referens data från en SQL Database för ett Azure Stream Analytics jobb

Azure Stream Analytics stöder Azure SQL Database som data källa för referens data. Du kan använda SQL Database som referens data för ditt Stream Analytics-jobb i Azure Portal och i Visual Studio med Stream Analytics verktyg. Den här artikeln visar hur du gör båda metoderna.

## <a name="azure-portal"></a>Azure Portal

Använd följande steg för att lägga till Azure SQL Database som referens källa med hjälp av Azure Portal:

### <a name="portal-prerequisites"></a>Portal krav

1. Skapa ett Stream Analytics-jobb.

2. Skapa ett lagrings konto som ska användas av Stream Analyticss jobbet.

3. Skapa din Azure SQL Database med en data uppsättning som ska användas som referens data av Stream Analyticss jobbet.

### <a name="define-sql-database-reference-data-input"></a>Definiera SQL Database referens data inmatning

1. I ditt Stream Analytics jobb väljer du **indata** under **jobb sto pol Ogin**. Klicka på **Lägg till referens inmatare** och välj **SQL Database**.

   ![Indata är markerade i det vänstra navigerings fönstret. Vid indata, + Lägg till referens inmatning är markerat, och en nedrullningsbar listruta visas som visar värdena Blob Storage och SQL Database.](./media/sql-reference-data/stream-analytics-inputs.png)

2. Fyll i Stream Analytics inmatade konfigurationer. Välj databas namn, Server namn, användar namn och lösen ord. Om du vill att referens data indata ska uppdateras regelbundet väljer du "på" för att ange uppdaterings frekvensen i DD: HH: MM. Om du har stora data mängder med en kort uppdaterings frekvens kan du använda en [delta fråga](sql-reference-data.md#delta-query).

   ![När SQL Database har valts visas sidan SQL Database nya indatamängden. Det finns ett konfigurations formulär i det vänstra fönstret och en ögonblicks bild fråga i den högra rutan.](./media/sql-reference-data/sql-input-config.png)

3. Testa ögonblicks bild frågan i SQL-Frågeredigeraren. Mer information finns i [använda Azure Portal SQL-Frågeredigeraren för att ansluta och fråga efter data](../azure-sql/database/connect-query-portal.md)

### <a name="specify-storage-account-in-job-config"></a>Ange lagrings konto i jobb konfigurationen

Gå till **Inställningar för lagrings kontot** under **Konfigurera** och välj **Lägg till lagrings konto**.

   ![Inställningarna för lagrings kontot är markerat i den vänstra rutan. Det finns ett Lägg till lagrings konto-knappen i den högra rutan.](./media/sql-reference-data/storage-account-settings.png)

### <a name="start-the-job"></a>Starta jobbet

När du har konfigurerat andra indata, utdata och frågor kan du starta Stream Analytics jobbet.

## <a name="tools-for-visual-studio"></a>Verktyg för Visual Studio

Använd följande steg för att lägga till Azure SQL Database som referens källa med Visual Studio:

### <a name="visual-studio-prerequisites"></a>Krav för Visual Studio

1. [Installera Stream Analytics verktyg för Visual Studio](stream-analytics-tools-for-visual-studio-install.md). Följande versioner av Visual Studio stöds:

   * Visual Studio 2015
   * Visual Studio 2019

2. Bekanta dig med [Stream Analytics Tools för Visual Studio](stream-analytics-quick-create-vs.md) snabb start.

3. Skapa ett lagringskonto.

### <a name="create-a-sql-database-table"></a>Skapa en SQL Database tabell

Använd SQL Server Management Studio för att skapa en tabell för att lagra dina referens data. Se [utforma din första Azure SQL Database med SSMS](../azure-sql/database/design-first-database-tutorial.md) för mer information.

Exempel tabellen som används i följande exempel skapades från följande instruktion:

```SQL
create table chemicals(Id Bigint,Name Nvarchar(max),FullName Nvarchar(max));
```

### <a name="choose-your-subscription"></a>Välj din prenumeration

1. Gå till menyn **Visa** och välj **Server Explorer** i Visual Studio.

2. Högerklicka på **Azure**, Välj **Anslut till Microsoft Azure prenumeration** och logga in med ditt Azure-konto.

### <a name="create-a-stream-analytics-project"></a>Skapa ett Stream Analytics-projekt

1. Välj **Arkiv > Nytt projekt**. 

2. I listan över mallar till vänster väljer du **Stream Analytics** och sedan **Azure Stream Analytics-programmet**. 

3. Ange projekt **namn**, **plats** och **lösnings namn** och välj **OK**.

   ![Stream Analytics mall väljs, Azure Stream Analytics programmet är markerat och rutorna namn, plats och lösnings namn är markerade.](./media/sql-reference-data/stream-analytics-vs-new-project.png)

### <a name="define-sql-database-reference-data-input"></a>Definiera SQL Database referens data inmatning

1. Skapa en ny indatamängd.

   ![Vid Lägg till nytt objekt väljs Indatatyp.](./media/sql-reference-data/stream-analytics-vs-input.png)

2. Dubbelklicka på **Input.js** i **Solution Explorer**.

3. Fyll i **konfigurationen för Stream Analytics Indatamängden**. Välj databasens namn, Server namn, uppdaterings typ och uppdaterings frekvens. Ange uppdaterings frekvensen i formatet `DD:HH:MM` .

   ![I Stream Analytics konfiguration av indatamängden anges värden eller väljs i list rutorna.](./media/sql-reference-data/stream-analytics-vs-input-config.png)

   Om du väljer "kör bara en gång" eller "kör regelbundet", skapas en SQL CodeBehind-fil med namnet **[indata Ali Aset]. snapshot. SQL** i projektet under **Input.jspå** filnoden.

   ![SQL-CodeBehind fil kemikalier. snapshot. SQL har marker ATS.](./media/sql-reference-data/once-or-periodically-codebehind.png)

   Om du väljer att uppdatera regelbundet med delta kommer två SQL CodeBehind-filer att genereras: **[Indataområde]. snapshot. SQL** och **[Indataområde]. delta. SQL**.

   ![SQL CodeBehind-filernas kemikalier. delta. SQL och kemikalier. snapshot. SQL har marker ATS.](./media/sql-reference-data/periodically-delta-codebehind.png)

4. Öppna SQL-filen i redigeraren och Skriv SQL-frågan.

5. Om du använder Visual Studio 2019 och har installerat SQL Server data verktyg kan du testa frågan genom att klicka på **Kör**. Ett guide fönster visas för att hjälpa dig att ansluta till SQL Database och frågeresultatet visas i fönstret längst ned.

### <a name="specify-storage-account"></a>Ange lagrings konto

Öppna **JobConfig.jspå** för att ange lagrings kontot för lagring av ögonblicks bilder av SQL-referens.

   ![Stream Analytics konfiguration av jobb konfigureras visas med standardvärden. De globala lagrings inställningarna är markerade.](./media/sql-reference-data/stream-analytics-job-config.png)

### <a name="test-locally-and-deploy-to-azure"></a>Testa lokalt och distribuera till Azure

Innan du distribuerar jobbet till Azure kan du testa frågans logik lokalt mot Live-indata. Mer information om den här funktionen finns i [testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio (för hands version)](stream-analytics-live-data-local-testing.md). När du är klar med testningen klickar du på **Skicka till Azure**. Referera till [skapa en Stream Analytics med hjälp av Azure Stream Analytics verktyg för Visual Studio](stream-analytics-quick-create-vs.md) snabb start för att lära dig hur du startar jobbet.

## <a name="delta-query"></a>Delta fråga

När du använder delta frågan rekommenderas [temporala tabeller i Azure SQL Database](../azure-sql/temporal-tables.md) .

1. Skapa en temporal tabell i Azure SQL Database.
   
   ```SQL 
      CREATE TABLE DeviceTemporal 
      (  
         [DeviceId] int NOT NULL PRIMARY KEY CLUSTERED 
         , [GroupDeviceId] nvarchar(100) NOT NULL
         , [Description] nvarchar(100) NOT NULL 
         , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
         , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
         , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
      )  
      WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.DeviceHistory));  -- DeviceHistory table will be used in Delta query
   ```
2. Redigera ögonblicks bild frågan. 

   Använd parametern **\@ snapshotTime** för att instruera Stream Analytics runtime att hämta referens data uppsättningen från SQL Database temporal tabell som är giltig vid system tiden. Om du inte anger den här parametern riskerar du att få en felaktig bas referens data uppsättning på grund av klock skevar. Ett exempel på en fullständig ögonblicks bild fråga visas nedan:
   ```SQL
      SELECT DeviceId, GroupDeviceId, [Description]
      FROM dbo.DeviceTemporal
      FOR SYSTEM_TIME AS OF @snapshotTime
   ```
 
2. Redigera delta frågan. 
   
   Den här frågan hämtar alla rader i SQL Database som infogades eller togs bort inom en start tid, **\@ deltaStartTime** och en slut tid **\@ deltaEndTime**. Delta frågan måste returnera samma kolumner som ögonblicks bild frågan och kolumn **_åtgärden_**. Den här kolumnen definierar om raden infogas eller tas bort mellan **\@ deltaStartTime** och **\@ deltaEndTime**. De resulterande raderna flaggas som **1** om posterna infogades eller **2** om de tagits bort. Frågan måste också lägga till **vatten märket** från SQL Server sidan för att säkerställa att alla uppdateringar i delta-perioden samlas in på rätt sätt. Om du använder delta fråga utan **vattenstämpel** kan det resultera i felaktig referens data uppsättning.  

   För poster som har uppdaterats bokförs den temporala tabellen genom att en infognings-och borttagnings åtgärd fångas. Den Stream Analytics körningen använder sedan resultatet av delta frågan till föregående ögonblicks bild för att hålla referens data uppdaterade. Ett exempel på en delta fråga visas nedan:

   ```SQL
      SELECT DeviceId, GroupDeviceId, Description, ValidFrom as _watermark_, 1 as _operation_
      FROM dbo.DeviceTemporal
      WHERE ValidFrom BETWEEN @deltaStartTime AND @deltaEndTime   -- records inserted
      UNION
      SELECT DeviceId, GroupDeviceId, Description, ValidTo as _watermark_, 2 as _operation_
      FROM dbo.DeviceHistory   -- table we created in step 1
      WHERE ValidTo BETWEEN @deltaStartTime AND @deltaEndTime     -- record deleted
   ```
 
   Observera att Stream Analytics runtime kan köra ögonblicks bilds frågan med jämna mellanrum förutom delta-frågan för att lagra kontroll punkter.

## <a name="test-your-query"></a>Testa frågan
   Det är viktigt att kontrol lera att din fråga returnerar den förväntade data uppsättning som Stream Analyticss jobbet kommer att använda som referens data. Om du vill testa din fråga går du till ininformation under jobb sto sektion på portalen. Du kan sedan välja exempel data på SQL Database referens indata. När exemplet blir tillgängligt kan du hämta filen och kontrol lera om de data som returneras är som förväntat. Om du vill optimera dina utvecklings-och test iterationer rekommenderar vi att du använder [Stream Analytics verktyg för Visual Studio](./stream-analytics-tools-for-visual-studio-install.md). Du kan också använda andra verktyg för att först se till att frågan returnerar rätt resultat från dig Azure SQL Database och sedan använder du den i ditt Stream Analytics-jobb. 

### <a name="test-your-query-with-visual-studio-code"></a>Testa din fråga med Visual Studio Code

   Installera [Azure Stream Analytics verktyg](https://marketplace.visualstudio.com/items?itemName=ms-bigdatatools.vscode-asa) och [SQL Server (MSSQL)](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql) i Visual Studio Code och konfigurera ditt ASA-projekt. Mer information finns i [snabb start: skapa ett Azure Stream Analytics jobb i Visual Studio Code](./quick-create-visual-studio-code.md) och [själv studie kursen för SQL Server (MSSQL)](/sql/tools/visual-studio-code/sql-server-develop-use-vscode).

1. Konfigurera dina SQL-referenser för data inmatning.
   
   ![Ett Visual Studio Code Editor (fliken) visar ReferenceSQLDatabase.jspå.](./media/sql-reference-data/configure-sql-reference-data-input.png)

2. Välj ikonen SQL Server och klicka på **Lägg till anslutning**.
   
   ![+ Lägg till anslutning visas i det vänstra fönstret och är markerat.](./media/sql-reference-data/add-sql-connection.png)

3. Fyll i anslutnings informationen.
   
   ![De två rutorna för databas-och Server information är markerade.](./media/sql-reference-data/fill-connection-information.png)

4. Högerklicka på referens-SQL och välj **Kör fråga**.
   
   ![Kör fråga är markerat i snabb menyn.](./media/sql-reference-data/execute-query.png)

5. Välj din anslutning.
   
   ![I dialog rutan står det "skapa en anslutnings profil från listan nedan", och listan har en post, som är hightlighted.](./media/sql-reference-data/choose-connection.png)

6. Granska och verifiera resultatet av frågan.
   
   ![Sök resultatet för sökningen är i en VS Code Editor-flik.](./media/sql-reference-data/verify-result.png)


## <a name="faqs"></a>Vanliga frågor och svar

**Debiteras jag ytterligare kostnader genom att använda indata från SQL-referenser i Azure Stream Analytics?**

Det finns inga ytterligare [kostnader per strömnings enhet](https://azure.microsoft.com/pricing/details/stream-analytics/) i Stream Analyticss jobbet. Stream Analyticss jobbet måste dock ha ett associerat Azure Storage-konto. Stream Analyticss jobbet frågar SQL DB (under jobb start och uppdaterings intervall) för att hämta referens data uppsättningen och lagrar ögonblicks bilden i lagrings kontot. Genom att lagra dessa ögonblicks bilder debiteras ytterligare avgifter på [sidan med priser](https://azure.microsoft.com/pricing/details/storage/) för Azure Storage-kontot.

**Hur gör jag för att vet du att ögonblicks bilden av referens data från SQL DB och används i Azure Stream Analyticss jobbet?**

Det finns två mått som filtreras efter logiskt namn (under mått Azure Portal) som du kan använda för att övervaka hälsan för SQL Database referens data inmatning.

   * InputEvents: det här måttet mäter antalet poster som läses in från SQL Database referens data uppsättning.
   * InputEventBytes: det här måttet mäter storleken på den referens data ögonblicks bild som lästs in i minnet för Stream Analytics jobbet. 

Kombinationen av båda dessa mått kan användas för att härleda om jobbet frågar SQL Database att hämta referens data uppsättningen och sedan läsa in den till minnet.

**Måste jag ha en särskild typ av Azure SQL Database?**

Azure Stream Analytics kommer att fungera med vilken typ av Azure SQL Database som helst. Det är dock viktigt att förstå att uppdaterings frekvensen som angetts för dina data indata kan påverka inläsningen av frågan. Om du vill använda delta-frågealternativet rekommenderar vi att du använder temporala tabeller i Azure SQL Database.

**Varför lagrar Azure Stream Analytics ögonblicks bilder i Azure Storage konto?**

Stream Analytics garanterar händelsebearbetning exakt en gång och leverans av händelser minst en gång. I de fall där tillfälliga problem påverkar ditt jobb är en liten mängd omuppspelning nödvändig för att återställa status. Om du vill aktivera uppspelning måste du ha dessa ögonblicks bilder lagrade i ett Azure Storage-konto. Mer information om kontroll punkts återuppspelning finns i avsnittet [om kontroll punkter och omuppspelning av begrepp i Azure Stream Analytics jobb](stream-analytics-concepts-checkpoint-replay.md).

## <a name="next-steps"></a>Nästa steg

* [Använda referens data för sökningar i Stream Analytics](stream-analytics-use-reference-data.md)
* [Snabbstart: Skapa ett Stream Analytics-jobb med hjälp av Azure Stream Analytics-verktygen för Visual Studio](stream-analytics-quick-create-vs.md)
* [Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio (för hands version)](stream-analytics-live-data-local-testing.md)