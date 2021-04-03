---
title: Synkronisera data från Azure SQL Edge med hjälp av Azure Data Factory
description: Lär dig mer om att synkronisera data mellan Azure SQL Edge och Azure Blob Storage
keywords: SQL Edge, synkronisera data från SQL Edge, SQL Edge Data Factory
services: sql-edge
ms.service: sql-edge
ms.topic: tutorial
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: b83201ae864d1f1eb9124af5268360bb1748f6c8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97507616"
---
# <a name="tutorial-sync-data-from-sql-edge-to-azure-blob-storage-by-using-azure-data-factory"></a>Självstudie: synkronisera data från SQL Edge till Azure Blob Storage med hjälp av Azure Data Factory

I den här självstudien använder du Azure Data Factory för att stegvis synkronisera data till Azure Blob Storage från en tabell i en instans av Azure SQL Edge.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte redan har skapat en databas eller tabell i din Azure SQL Edge-distribution använder du någon av följande metoder för att skapa en:

* Använd [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/) eller [Azure Data Studio](/sql/azure-data-studio/download/) för att ansluta till SQL Edge. Kör ett SQL-skript för att skapa databasen och tabellen.
* Skapa en databas och tabell genom att använda [SQLCMD](/sql/tools/sqlcmd-utility/) genom att ansluta direkt till SQL Edge-modulen. Mer information finns i [ansluta till databas motorn med hjälp av SQLCMD](/sql/ssms/scripting/sqlcmd-connect-to-the-database-engine/).
* Använd SQLPackage.exe för att distribuera en DAC-paketfil till SQL Edge-behållaren. Du kan automatisera processen genom att ange SqlPackage-fil-URI som en del av modulens önskade egenskaper-konfiguration. Du kan också använda SqlPackage.exe klient verktyget direkt för att distribuera ett DAC-paket till SQL Edge.

    Information om hur du hämtar SqlPackage.exe finns i [Hämta och installera sqlpackage](/sql/tools/sqlpackage-download/). Här följer några exempel kommandon för SqlPackage.exe. Mer information finns i SqlPackage.exe-dokumentationen.

    **Skapa ett DAC-paket**

    ```cmd
    sqlpackage /Action:Extract /SourceConnectionString:"Data Source=<Server_Name>,<port>;Initial Catalog=<DB_name>;User ID=<user>;Password=<password>" /TargetFile:<dacpac_file_name>
    ```

    **Tillämpa ett DAC-paket**

    ```cmd
    sqlpackage /Action:Publish /Sourcefile:<dacpac_file_name> /TargetServerName:<Server_Name>,<port> /TargetDatabaseName:<DB_Name> /TargetUser:<user> /TargetPassword:<password>
    ```

## <a name="create-a-sql-table-and-procedure-to-store-and-update-the-watermark-levels"></a>Skapa en SQL-tabell och procedur för att lagra och uppdatera vatten märkes nivåerna

En vatten märkes tabell används för att lagra den sista tidsstämpeln som data redan har synkroniserats med Azure Storage. En lagrad procedur i Transact-SQL (T-SQL) används för att uppdatera vatten märkes tabellen efter varje synkronisering.

Kör de här kommandona på SQL Edge-instansen:

```sql
    Create table [dbo].[watermarktable]
    (
    TableName varchar(255),
    WatermarkValue datetime,
    )
    GO

    CREATE PROCEDURE usp_write_watermark @timestamp datetime, @TableName varchar(50)  
    AS  
    BEGIN
    
    UPDATE [dbo].[watermarktable]
    SET [WatermarkValue] = @timestamp
    WHERE [TableName] = @TableName

    END
    Go
```

## <a name="create-a-data-factory-pipeline"></a>Skapa en Data Factory pipeline

I det här avsnittet skapar du en Azure Data Factory pipeline för att synkronisera data till Azure Blob Storage från en tabell i Azure SQL Edge.

### <a name="create-a-data-factory-by-using-the-data-factory-ui"></a>Skapa en data fabrik med hjälp av Data Factory gränssnittet

Skapa en data fabrik genom att följa anvisningarna i [den här självstudien](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).

### <a name="create-a-data-factory-pipeline"></a>Skapa en Data Factory pipeline

1. På sidan för att **komma igång** i Data Factory UI väljer du **skapa pipeline**.

    ![Skapa en Data Factory pipeline](media/tutorial-sync-data-factory/data-factory-get-started.png)

2. På sidan **Allmänt** i fönstret **Egenskaper** för pipelinen anger du **PeriodicSync** som namn.

3. Lägg till lookup-aktiviteten för att hämta det gamla värdet för vatten märket. I fönstret **aktiviteter** expanderar du **Allmänt** och drar **söknings** aktiviteten till pipelinens designer-yta. Ändra namnet på aktiviteten till **OldWatermark**.

    ![Lägg till den gamla Sök efter vatten märket](media/tutorial-sync-data-factory/create-old-watermark-lookup.png)

4. Växla till fliken **Inställningar** och välj **nytt** för **käll data uppsättning**. Nu ska du skapa en data uppsättning som representerar data i vatten märkes tabellen. Den här tabellen innehåller den gamla vattenstämpeln som användes i den tidigare kopieringen.

5. I fönstret **ny data uppsättning** väljer du **Azure SQL Server** och väljer sedan **Fortsätt**.  

6. I fönstret **Ange egenskaper** för data uppsättningen under **namn** anger du **WatermarkDataset**.

7. För **länkad tjänst** väljer du **ny** och slutför sedan de här stegen:

    1. Under **namn** anger du **SQLDBEdgeLinkedService**.

    2. Under **Server namn** anger du information om SQL Edge-servern.

    3. Välj ditt **databas namn** i listan.

    4. Ange ditt **användar namn** och **lösen ord**.

    5. Om du vill testa anslutningen till SQL Edge-instansen väljer du **Testa anslutning**.

    6. Välj **Skapa**.

    ![Skapa en länkad tjänst](media/tutorial-sync-data-factory/create-linked-service.png)

    7. Välj **OK**.

8. På fliken **Inställningar** väljer du **Redigera**.

9. På fliken **anslutning** väljer du **[dbo]. [ watermarktable]** för **tabell**. Om du vill förhandsgranska data i tabellen väljer du **Förhandsgranska data**.

10. Växla till pipeline-redigeraren genom att välja fliken pipeline överst eller genom att välja namnet på pipelinen i trädvyn till vänster. I fönstret Egenskaper för söknings aktiviteten bekräftar du att **WatermarkDataset** är markerat i listan **käll data uppsättning** .

11. I fönstret **aktiviteter** expanderar du **Allmänt** och drar en annan **Lookup** -aktivitet till pipelinens designer-yta. Ange namnet **NewWatermark** på fliken **Allmänt** i fönstret Egenskaper. Den här söknings aktiviteten hämtar det nya värdet för vattenstämpeln från den tabell som innehåller källdata så att den kan kopieras till målet.

12. I fönstret Egenskaper för den andra söknings aktiviteten växlar du till fliken **Inställningar** och väljer **ny** för att skapa en data uppsättning som pekar på den käll tabell som innehåller det nya värdet för vattenstämpeln.

13. I fönstret **ny data uppsättning** väljer du **SQL Edge-instans** och väljer sedan **Fortsätt**.

    1. I fönstret **Ange egenskaper** , under **namn**, anger du **SourceDataset**. Under **länkad tjänst** väljer du **SQLDBEdgeLinkedService**.

    2. Under **tabell** väljer du den tabell som du vill synkronisera. Du kan också ange en fråga för den här data uppsättningen, enligt beskrivningen längre fram i den här självstudien. Frågan prioriteras framför tabellen som du anger i det här steget.

    3. Välj **OK**.

14. Växla till pipeline-redigeraren genom att välja fliken pipeline överst eller genom att välja namnet på pipelinen i trädvyn till vänster. I fönstret Egenskaper för söknings aktiviteten bekräftar du att **SourceDataset** är markerat i listan **käll data uppsättning** .

15. Välj **fråga** under **Använd fråga**. Uppdatera tabell namnet i följande fråga och ange sedan frågan. Du väljer bara det maximala värdet för `timestamp` från tabellen. Var noga med att välja **enbart första raden**.

    ```sql
    select MAX(timestamp) as NewWatermarkvalue from [TableName]
    ```

    ![Välj fråga](media/tutorial-sync-data-factory/select-query-data-factory.png)

16. I fönstret **aktiviteter** expanderar du **Flytta & Transform** och drar **kopierings** aktiviteten från fönstret **aktiviteter** till design ytan. Ange namnet på aktiviteten till **IncrementalCopy**.

17. Anslut båda sökningsaktiviteterna till kopieringsaktiviteten genom att dra den gröna knappen som är ansluten till sökningsaktiviteterna till kopieringsaktiviteten. Släpp mus knappen när du ser att kant linje färgen för kopierings aktiviteten ändras till blå.

18. Välj kopieringsaktiviteten och bekräfta att du ser egenskaperna för aktiviteten i fönstret **Egenskaper**.

19. Växla till fliken **källa** i fönstret **Egenskaper** och utför följande steg:

    1. I rutan **käll data uppsättning** väljer du **SourceDataset**.

    2. Under **Använd fråga** väljer du **fråga**.

    3. Ange SQL-frågan i rutan **fråga** . Här är en exempel fråga:

    ```sql
    select * from TemperatureSensor where timestamp > '@{activity('OldWaterMark').output.firstRow.WatermarkValue}' and timestamp <= '@{activity('NewWaterMark').output.firstRow.NewWatermarkvalue}'
    ```

20. På fliken **mottagare** väljer du **ny** under **data uppsättning för mottagare**.

21. I den här självstudien är mottagar data lagret ett Azure Blob Storage-data lager. Välj **Azure Blob Storage** och välj sedan **Fortsätt** i fönstret **ny data uppsättning** .

22. I fönstret **Välj format** väljer du formatet för dina data och väljer sedan **Fortsätt**.

23. I fönstret **Ange egenskaper** , under **namn**, anger du **SinkDataset**. Under **länkad tjänst** väljer du **ny**. Nu ska du skapa en anslutning (en länkad tjänst) till Azure Blob Storage.

24. I fönstret **ny länkad tjänst (Azure Blob Storage)** utför du följande steg:

    1. I rutan **namn** anger du **AzureStorageLinkedService**.

    2. Under **lagrings konto namn** väljer du Azure Storage-kontot för din Azure-prenumeration.

    3. Testa anslutningen och välj sedan **Slutför**.

25. I fönstret **Ange egenskaper** bekräftar du att **AzureStorageLinkedService** är markerat under **länkad tjänst**. Välj **skapa** och **OK**.

26. På fliken **mottagare** väljer du **Redigera**.

27. Gå till fliken **anslutning** i SinkDataset och utför följande steg:

    1. Under **fil Sök väg** anger du *asdedatasync/incrementalcopy*, där *asdedatasync* är BLOB-containerns namn och *incrementalcopy* är mappnamnet. Skapa behållaren om den inte finns eller Använd namnet på en befintlig. Azure Data Factory skapar automatiskt mappen utdata *incrementalcopy* om den inte finns. Du kan också använda knappen **Bläddra** för **Filsökväg** för att navigera till en mapp i en blobcontainer.

    2. För **fil** delen av **fil Sök vägen** väljer du **Lägg till dynamiskt innehåll [ALT + P]** och anger sedan **@CONCAT ("stegvis-", pipeline (). RunId, '. txt ')** i fönstret som öppnas. Välj **Slutför**. Fil namnet genereras dynamiskt av uttrycket. Varje pipelinekörning har ett unikt ID. Kopieringsaktiviteten använder körnings-ID för att generera filnamnet.

28. Växla till pipeline-redigeraren genom att välja fliken pipeline överst eller genom att välja namnet på pipelinen i trädvyn till vänster.

29. I fönstret **aktiviteter** expanderar du **Allmänt** och drar aktiviteten **lagrad procedur** från fönstret **aktiviteter** till ytan pipeline designer. Anslut det gröna (framgångna) resultatet av kopierings aktiviteten till aktiviteten lagrad procedur.

30. Välj **aktivitet för lagrad procedur** i pipeline-designern och ändra dess namn till **SPtoUpdateWatermarkActivity**.

31. Växla till fliken **SQL-konto** och välj **_QLDBEdgeLinkedService_* under **länkad tjänst**.

32. Växla till fliken **lagrad procedur** och slutför de här stegen:

    1. Under **lagrad procedur namn** väljer du **[dbo]. [ usp_write_watermark]**.

    2. Om du vill ange värden för parametrarna för den lagrade proceduren väljer du **Importera parameter** och anger följande värden för parametrarna:

    |Namn|Typ|Värde|
    |-----|----|-----|
    |LastModifiedtime|DateTime|@ {Activity (' NewWaterMark '). output. firstRow. NewWatermarkvalue}|
    |TableName|Sträng|@ {Activity (' OldWaterMark '). output. firstRow. TableName}|

33. Verifiera inställningarna för pipelinen genom att välja **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Stäng fönstret **validerings rapport för pipeline** genom att välja **>>** .

34. Publicera entiteter (länkade tjänster, data uppsättningar och pipeliner) till Azure Data Factory tjänsten genom att välja knappen **publicera alla** . Vänta tills du ser ett meddelande som bekräftar att publicerings åtgärden har slutförts.

## <a name="trigger-a-pipeline-based-on-a-schedule"></a>Utlös en pipeline baserat på ett schema

1. I pipeline-verktygsfältet väljer du **Lägg till utlösare**, Välj **ny/redigera** och välj sedan **ny**.

2. Ge utlösaren **HourlySync**. Välj **schema** under **typ**. Ställ in **upprepningen** på varannan timme.

3. Välj **OK**.

4. Välj **Publicera alla**.

5. Välj **utlösare nu**.

6. Växla till fliken **Övervaka** till vänster. Du kan se status för den pipelinekörning som utlöstes av den manuella utlösaren. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).

## <a name="next-steps"></a>Nästa steg

Azure Data Factory pipelinen i den här självstudien kopierar data från en tabell på en SQL Edge-instans till en plats i Azure Blob Storage en gång i timmen. Mer information om hur du använder Data Factory i andra scenarier finns i de här [självstudierna](../data-factory/tutorial-copy-data-portal.md).
