---
title: Kopiera en tabell stegvis med Azure Portal
description: I den här självstudien skapar du en Azure Data Factory med en pipeline som läser in delta data från en tabell i Azure SQL Database till Azure Blob Storage.
services: data-factory
author: dearandyxu
ms.author: yexu
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 11/09/2020
ms.openlocfilehash: d1a7f47b1cdccb02952bd7d9d333855f5eec27d5
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508534"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-the-azure-portal"></a>Läs in data stegvis från Azure SQL Database till Azure Blob Storage med hjälp av Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien skapar du en Azure Data Factory med en pipeline som läser in delta data från en tabell i Azure SQL Database till Azure Blob Storage.

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbered datalagringen för att lagra värdet för vattenstämpeln.
> * Skapa en datafabrik.
> * Skapa länkade tjänster.
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa en pipeline.
> * Köra en pipeline.
> * Övervaka pipelinekörningen.
> * Granska resultat
> * Lägg till mer data i källan.
> * Kör pipelinen igen.
> * Övervaka den andra pipelinekörningen
> * Granska resultatet från den andra körningen


## <a name="overview"></a>Översikt
Här är det avancerade diagrammet:

![Läsa in data stegvis](media/tutorial-Incremental-copy-portal/incrementally-load.png)

Här är några viktiga steg för att skapa den här lösningen:

1. **Markera vattenstämpelkolumnen**.
    Markera en kolumn i källdatalagringen som går att använda för att dela upp de nya eller uppdaterade posterna för varje körning. Vanligtvis ökar data i den markerade kolumnen (till exempel last_modify_time elle ID) när rader skapas eller uppdateras. Det maximala värdet i den här kolumnen används som vattenstämpel.

2. **Förbered datalagringen för att lagra värdet för vattenstämpeln**. I den här självstudien lagrar du storleksgränsen i en SQL-databas.

3. **Skapa en pipeline med följande arbets flöde**:

    Pipelinen i den här lösningen har följande aktiviteter:

    * Skapa två sökningsaktiviteter. Använd den första sökningsaktiviteten för att hämta det sista vattenstämpelvärdet. Använd den andra sökningsaktiviteten för att hämta det nya vattenstämpelvärdet. Vattenstämpelvärdena skickas till kopieringsaktiviteten.
    * Skapa en {0}kopieringsaktivitet{0} som kopierar raderna från källdatalagringen med värdet för vattenstämpelkolumnen som är större än det gamla värdet och mindre än det nya. Sedan kopieras deltadata från källdatalagringen till Blob-lagring som en ny fil.
    * Skapa en StoredProcedure-aktivitet som uppdaterar vattenstämpelvärdet för den pipeline som körs nästa gång.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Krav
* **Azure SQL Database**. Du använder databasen som källa för datalagringen. Om du inte har en databas i Azure SQL Database, se [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) för att skapa en.
* **Azure Storage**. Du kan använda blob-lagringen som mottagare för datalagringen. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett lagringskonto](../storage/common/storage-account-create.md). Skapa en container med namnet adftutorial. 

### <a name="create-a-data-source-table-in-your-sql-database"></a>Skapa en datatabell i din SQL-databas
1. Öppna SQL Server Management Studio. I **Server Explorer** högerklickar du på databasen och väljer **Ny fråga**.

2. Kör följande SQL-kommando mot din SQL-databas för att skapa en tabell med namnet `data_source_table` som datakällagringen:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    I den här självstudien ska du använda LastModifytime som vattenstämpelkolumn. Data i datakällagringen visas i följande tabell:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Skapa en annan tabell i SQL-databasen för att lagra värdet för högvattenmärket

1. Kör följande SQL-kommando mot din SQL-databas för att skapa en tabell med namnet `watermarktable` för att lagra värdet för högvattenmärket:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Ställ in standardvärdet för högvattenmärket med tabellnamnet på källdatalagret. I den här självstudien är tabellnamnet: data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Granska informationen i tabellen `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Utdata:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Skapa en lagrad procedur i din SQL-databas

Kör följande kommando för att skapa en lagrad procedur i din SQL-databas:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. På den vänstra menyn väljer du **skapa en resurs**  >  **integrations**  >  **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

3. På sidan **Ny datafabrik** anger du **ADFIncCopyTutorialDF** som **namn**.

   Namnet på Azure Data Factory måste vara **globalt unikt**. Om du ser ett rött utropstecken med följande fel ändrar du namnet på datafabriken (till exempel dittnamnADFIncCopyTutorialDF) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.

    *Data fabriks namnet "ADFIncCopyTutorialDF" är inte tillgängligt*
4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
5. För **resursgruppen** utför du något av följande steg:

      - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.
      - Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
        Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
6. Välj **V2** för **versionen**.
7. Välj **plats** för datafabriken. Endast platser som stöds visas i listrutan. Data lag ren (Azure Storage, Azure SQL Database, Azure SQL-hanterad instans osv.) och beräkningarna (HDInsight osv.) som används av Data Factory kan finnas i andra regioner.
8. Klicka på **Skapa**.      
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.

   ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik.

## <a name="create-a-pipeline"></a>Skapa en pipeline
I den här självstudien skapar du en pipeline med två sökningsaktiviteter, en kopieringsaktivitet och en aktivitet för lagrad procedur i en länkad pipeline.

1. På sidan **Kom igång** i användargränssnittet för Data Factory klickar du på panelen **Skapa pipeline**.

   ![Sidan Kom igång i Data Factory-användargränssnittet](./media/doc-common-process/get-started-page.png)    
3. I panelen Allmänt under **Egenskaper** anger du **IncrementalCopyPipeline** som **namn**. Komprimera sedan panelen genom att klicka på egenskaps ikonen i det övre högra hörnet.

4. Vi lägger till den första sökningsaktiviteten för att hämta det gamla vattenstämpelvärdet. I verktygslådan **Aktiviteter** expanderar du **Allmänt** och drar och släpper **sökningen** på pipelinedesignytan. Ändra aktivitetens namn till **LookupOldWaterMarkActivity**.

   ![Den första sökningsaktiviteten – namn](./media/tutorial-incremental-copy-portal/first-lookup-name.png)
5. Växla till fliken **Inställningar** och klicka på **+ Ny** för **källdatauppsättningen**. I det här steget skapar du en datauppsättning för att representera data i **watermarktable**. Den här tabellen innehåller den gamla vattenstämpeln som användes i den tidigare kopieringen.

6. I fönstret **ny data uppsättning** väljer du **Azure SQL Database** och klickar på **Fortsätt**. Du ser ett nytt fönster som har öppnats för data uppsättningen.

7. I fönstret **Ange egenskaper** för data uppsättningen anger du **WatermarkDataset** som **namn**.

8. För **länkad tjänst** väljer du **ny** och utför sedan följande steg:

    1. Ange **AzureSqlDatabaseLinkedService** som **namn**.
    2. Välj servern som **Server namn**.
    3. Välj ditt **databas namn** i list rutan.
    4. Ange lösen ordet för **användar namnet**  &  .
    5. Om du vill testa anslutningen till din SQL-databas klickar du på **Testa anslutning**.
    6. Klicka på **Finish**.
    7. Bekräfta att **AzureSqlDatabaseLinkedService** har valts för den **länkade tjänsten**.

        ![Fönster för ny länkad tjänst](./media/tutorial-incremental-copy-portal/azure-sql-linked-service-settings.png)
    8. Välj **Slutför**.
9. På fliken **anslutning** väljer du **[dbo]. [ watermarktable]** för **tabell**. Om du vill förhandsgranska data i tabellen klickar du på **Förhandsgranska data**.

    ![Datauppsättning för vattenstämpel – anslutningsinställningar](./media/tutorial-incremental-copy-portal/watermark-dataset-connection-settings.png)
10. Växla till pipeline-redigeringsprogrammet genom att klicka på pipelinefliken högst upp eller på pipelinenamnet i trädvyn till vänster. I egenskapsfönstret för **sökningsaktiviteten** bekräftar du att **WatermarkDataset** är valt för fältet **Source Dataset** (Källdatauppsättning).

11. I verktygslådan **Aktiviteter** expanderar du **Allmänt** och drar och släpper ytterligare en **sökning** på pipelinedesignytan och anger namnet **LookupNewWaterMarkActivity** på fliken **Allmänt** i egenskapsfönstret. Med den här sökningsaktiviteten kopieras det nya vattenstämpelvärdet från tabellen med källdata till målet.

12. I egenskapsfönstret för den andra **sökningsaktiviteten** växlar du till fliken **Inställningar** och klickar på **New** (Nytt). Du skapar en datauppsättning för att peka på källtabellen som innehåller det nya vattenstämpelvärdet (det högsta värdet för LastModifyTime).

13. I fönstret **ny data uppsättning** väljer du **Azure SQL Database** och klickar på **Fortsätt**.
14. I fönstret **Ange egenskaper** anger du **SourceDataset** som **namn**. Välj **AzureSqlDatabaseLinkedService** som **Länkad tjänst**.
15. Välj **[dbo]. [ data_source_table]** för tabell. Du kan ange en fråga för den här datauppsättningen senare under kursen. Frågan åsidosätter den tabell som du anger i det här steget.
16. Välj **Slutför**.
17. Växla till pipeline-redigeringsprogrammet genom att klicka på pipelinefliken högst upp eller på pipelinenamnet i trädvyn till vänster. I egenskapsfönstret för **sökningsaktiviteten** bekräftar du att **SourceDataset** är valt för fältet **Source Dataset** (Källdatauppsättning).
18. Välj **Fråga** i fältet **Använd fråga** och ange följande fråga: du väljer endast det högsta värdet för **LastModifytime** från **data_source_table**. Kontrol lera att du också har markerat **enbart första raden**.

    ```sql
    select MAX(LastModifytime) as NewWatermarkvalue from data_source_table
    ```

    ![Den andra sökningsaktiviteten – fråga](./media/tutorial-incremental-copy-portal/query-for-new-watermark.png)
19. I verktygs lådan **aktiviteter** expanderar du **Flytta & Transform** och drar och släpper **kopierings** aktiviteten i aktivitets verktygs lådan och anger namnet till **IncrementalCopyActivity**.

20. **Anslut båda sökningsaktiviteterna till kopieringsaktiviteten** genom att dra den **gröna knappen** som är ansluten till sökningsaktiviteterna till kopieringsaktiviteten. Släpp musknappen när du ser att kantlinjefärgen för kopieringsaktiviteten ändras till blått.

    ![Anslutning för sökningsaktiviteter till kopieringsaktivitet](./media/tutorial-incremental-copy-portal/connection-lookups-to-copy.png)
21. Välj **kopieringsaktiviteten** och bekräfta att du ser egenskaperna för aktiviteten i fönstret **Egenskaper**.

22. Växla till fliken **Källa** i fönstret **Egenskaper** och utför följande steg:

    1. Markera **SourceDataset** för fältet för **källdatauppsättning**.
    2. Välj **Fråga** i fältet **Använd fråga**.
    3. Ange följande SQL-fråga för fältet **Fråga**.

        ```sql
        select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'
        ```

        ![Kopiera aktivitet – källa](./media/tutorial-incremental-copy-portal/copy-activity-source.png)
23. Växla till fliken **Sink** (Mottagare) och klicka på **+ Ny** för fältet för **datauppsättning för mottagare**.

24. I den här självstudien är datalager för mottagare av typen Azure Blob Storage. Välj därför **Azure Blob Storage** och klicka på **Fortsätt** i fönstret **ny data uppsättning** .
25. I fönstret **Välj format** väljer du format typ för dina data och klickar på **Fortsätt**.
25. I fönstret **Ange egenskaper** anger du **SinkDataset** som **namn**. För **länkad tjänst** väljer du **+ ny**. I det här steget skapar du en anslutning (länkad tjänst) till **Azure Blob Storage**.
26. Utför följande steg i fönstret **ny länkad tjänst (Azure Blob Storage)** :

    1. Ange **AzureStorageLinkedService** som **namn**.
    2. Välj ditt Azure-lagringskonto som **Lagringskontonamn**.
    3. Testa anslutningen och klicka sedan på **Slutför**.

27. I fönstret **Ange egenskaper** bekräftar du att **AzureStorageLinkedService** har valts för den **länkade tjänsten**. Välj sedan **Slutför**.
28. Gå till fliken **anslutning** i SinkDataset och utför följande steg:
    1. I fältet **fil Sök väg** anger du **adftutorial/incrementalcopy**. **adftutorial** är blobcontainerns namn och **incrementalcopy** är mappens namn. Det här kodfragmentet förutsätter att du har en blobcontainer med namnet adftutorial i din blob-lagring. Skapa containern om den inte finns, eller ställ in den för namnet på en befintlig. Azure Data Factory skapar automatiskt utdatamappen **incrementalcopy** om den inte finns. Du kan också använda knappen **Bläddra** för **Filsökväg** för att navigera till en mapp i en blobcontainer.
    2. För **fil** delen av fältet **fil Sök väg** väljer du **Lägg till dynamiskt innehåll [ALT + P]** och anger sedan `@CONCAT('Incremental-', pipeline().RunId, '.txt')` i det öppnade fönstret. Välj sedan **Slutför**. Filnamnet genereras dynamiskt med uttrycket. Varje pipelinekörning har ett unikt ID. Kopieringsaktiviteten använder körnings-ID för att generera filnamnet.

28. Växla till **pipeline**-redigeringsprogrammet genom att klicka på pipelinefliken högst upp eller på pipelinenamnet i trädvyn till vänster.
29. I verktygslådan **Aktiviteter** expanderar du **Allmänt** och drar och släpper aktiviteten **Lagrad procedur** från verktygslådan **Aktiviteter** till pipelinedesignerytan. **Anslut** gröna utdata (lyckades) från aktiviteten **Kopiera** till den **lagrade proceduraktiviteten**.

24. Välj **Lagrad proceduraktivitet** i pipelinedesignern och ändra dess namn till **StoredProceduretoWriteWatermarkActivity**.

25. Växla till fliken **SQL-konto** och välj **AzureSqlDatabaseLinkedService** för **länkad tjänst**.

26. Växla till fliken **Lagrad procedur** och gör följande:

    1. Som **Namn på lagrad procedur** väljer du **usp_write_watermark**.
    2. När du ska ange värden för parametrarna för lagrad procedur klickar du på **Importera parameter** och anger följande värden för parametern:

        | Namn | Typ | Värde |
        | ---- | ---- | ----- |
        | LastModifiedtime | DateTime | @{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue} |
        | TableName | Sträng | @{activity('LookupOldWaterMarkActivity').output.firstRow.TableName} |

        ![Lagrad proceduraktivitet – inställningar för lagrad procedur](./media/tutorial-incremental-copy-portal/sproc-activity-stored-procedure-settings.png)
27. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga fönstret med **verifieringsrapporten för pipeline** klickar du på >>.   

28. Publicera entiteter (länkade tjänster, datauppsättningar och pipeliner) till Azure Data Factory-tjänsten genom att välja knappen **Publicera alla**. Vänta tills du ser ett meddelande om att publiceringen är klar.


## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning
1. Klicka på **Lägg till utlösare** i verktygsfältet och klicka på **Utlös nu**.

2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Växla till fliken **Övervaka** till vänster. Du ser status för pipeline-körningen utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **pipeline-namn** om du vill visa körnings information och köra pipelinen igen.

2. Om du vill se aktivitets körningar som är associerade med pipeline-körningen väljer du länken i kolumnen **pipeline-namn** . Om du vill ha mer information om aktivitets körningarna väljer du länken **information** (glasögon ikonen) under kolumnen **aktivitets namn** . Välj **alla pipelines** längst upp för att gå tillbaka till vyn pipelines-körningar. Välj **Uppdatera** för att uppdatera vyn.


## <a name="review-the-results"></a>Granska resultaten
1. Anslut till Azure Storage-kontot med verktyg som [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). Kontrollera att en utdatafil har skapats i mappen **incrementalcopy** i **adftutorial**-containern.

    ![Den första utdatafilen](./media/tutorial-incremental-copy-portal/first-output-file.png)
2. Öppna utdatafilen och notera att alla data har kopierats från **data_source_table** till blobfilen.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
3. Kontrollera det senaste värdet från `watermarktable`. Du kan se att vattenstämpeln har uppdaterats.

    ```sql
    Select * from watermarktable
    ```

    Här är utdata:

    | TableName | WatermarkValue |
    | --------- | -------------- |
    | data_source_table | 2017-09-05    8:06:00.000 |

## <a name="add-more-data-to-source"></a>Lägg till mer data i källan

Infoga nya data i databasen (data käll arkivet).

```sql
INSERT INTO data_source_table
VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

INSERT INTO data_source_table
VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
```

De uppdaterade data i databasen är:

```
PersonID | Name | LastModifytime
-------- | ---- | --------------
1 | aaaa | 2017-09-01 00:56:00.000
2 | bbbb | 2017-09-02 05:23:00.000
3 | cccc | 2017-09-03 02:36:00.000
4 | dddd | 2017-09-04 03:21:00.000
5 | eeee | 2017-09-05 08:06:00.000
6 | newdata | 2017-09-06 02:23:00.000
7 | newdata | 2017-09-07 09:01:00.000
```

## <a name="trigger-another-pipeline-run"></a>Utlös ytterligare en pipelinekörning

1. Växla till fliken **Redigera** . Klicka på pipelinen i trädvyn om den inte är öppen i designern.

2. Klicka på **Lägg till utlösare** i verktygsfältet och klicka på **Utlös nu**.


## <a name="monitor-the-second-pipeline-run"></a>Övervaka den andra pipelinekörningen

1. Växla till fliken **Övervaka** till vänster. Du ser status för pipeline-körningen utlöst av en manuell utlösare. Du kan använda länkar i kolumnen **pipeline-namn** om du vill visa aktivitets information och köra pipelinen igen.

2. Om du vill se aktivitets körningar som är associerade med pipeline-körningen väljer du länken i kolumnen **pipeline-namn** . Om du vill ha mer information om aktivitets körningarna väljer du länken **information** (glasögon ikonen) under kolumnen **aktivitets namn** . Välj **alla pipelines** längst upp för att gå tillbaka till vyn pipelines-körningar. Välj **Uppdatera** för att uppdatera vyn.


## <a name="verify-the-second-output"></a>Verifiera den andra utdata

1. I blob-lagringen ser du att en annan fil har skapats. I den här självstudien är det nya filnamnet `Incremental-<GUID>.txt`. När du öppnar filen ser du två rader med poster i den.

    ```
    6,newdata,2017-09-06 02:23:00.0000000
    7,newdata,2017-09-07 09:01:00.0000000    
    ```
2. Kontrollera det senaste värdet från `watermarktable`. Du kan se att vattenstämpeln har uppdaterats igen.

    ```sql
    Select * from watermarktable
    ```
    exempel på utdata:

    | TableName | WatermarkValue |
    | --------- | --------------- |
    | data_source_table | 2017-09-07 09:01:00.000 |



## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen fick du:

> [!div class="checklist"]
> * Förbered datalagringen för att lagra värdet för vattenstämpeln.
> * Skapa en datafabrik.
> * Skapa länkade tjänster.
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa en pipeline.
> * Köra en pipeline.
> * Övervaka pipelinekörningen.
> * Granska resultat
> * Lägg till mer data i källan.
> * Kör pipelinen igen.
> * Övervaka den andra pipelinekörningen
> * Granska resultatet från den andra körningen

I den här självstudien kopierade pipelinen data från en enskild tabell i SQL Database till Blob Storage. Gå vidare till följande självstudie för att lära dig hur du kopierar data från flera tabeller i en SQL Server databas till SQL Database.

> [!div class="nextstepaction"]
>[Läs in data stegvist från flera tabeller i SQL Server till Azure SQL Database](tutorial-incremental-copy-multiple-tables-portal.md)
