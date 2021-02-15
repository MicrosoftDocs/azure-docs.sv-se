---
title: Delta kopia från en databas med hjälp av en kontroll tabell
description: Lär dig hur du använder en lösnings mall för att stegvis kopiera nya eller uppdaterade rader från en databas med Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: b3b5679b254a07b275cc7fd1295ba4ca5b405fbc
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362020"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Delta kopia från en databas med en kontroll tabell

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här artikeln beskrivs en mall som är tillgänglig för att stegvis läsa in nya eller uppdaterade rader från en databas tabell till Azure med hjälp av en extern kontroll tabell som lagrar ett värde med hög vatten märket.

Den här mallen kräver att käll databasens schema innehåller en tidsstämpel-kolumn eller en stegvis öknings nyckel för att identifiera nya eller uppdaterade rader.

>[!NOTE]
> Om du har en tidsstämpelkolumn i käll databasen för att identifiera nya eller uppdaterade rader, men du inte vill skapa en extern kontroll tabell som ska användas för delta kopiering, kan du i stället använda [verktyget Azure Data Factory kopiera data](copy-data-tool.md) för att hämta en pipeline. Verktyget använder en utlösare – schemalagd tid som en variabel för att läsa nya rader från käll databasen.

## <a name="about-this-solution-template"></a>Om den här lösnings mal len

Den här mallen hämtar först det gamla värdet för vatten märket och jämför det med det aktuella värdet för vatten märket. Därefter kopieras bara ändringarna från käll databasen baserat på en jämförelse mellan de två värdena för vatten märket. Slutligen lagrar den det nya värdet med hög vatten märket till en extern kontroll tabell för inläsning av delta data nästa tillfälle.

Mallen innehåller fyra aktiviteter:
- **Lookup** hämtar det gamla värdet för hög vatten märket, som lagras i en extern kontroll tabell.
- En annan **Lookup** -aktivitet hämtar det aktuella värdet för hög vatten märket från käll databasen.
- **Kopiera** kopierar endast ändringar från käll databasen till mål lagret. Frågan som identifierar ändringarna i käll databasen liknar "SELECT * FROM Data_Source_Table där TIMESTAMP_Column >" sista övre vatten märket "och TIMESTAMP_Column <=" aktuell övre vatten märke "".
- **SqlServerStoredProcedure** skriver det aktuella värdet för hög vatten märket till en extern kontroll tabell för delta kopiering nästa tillfälle.

Mallen definierar följande parametrar:
- *Data_Source_Table_Name* är tabellen i käll databasen som du vill läsa in data från.
- *Data_Source_WaterMarkColumn* är namnet på kolumnen i käll tabellen som används för att identifiera nya eller uppdaterade rader. Den här kolumnens typ är normalt *datetime*, *int* eller liknande.
- *Data_Destination_Container* är rot Sök vägen till den plats där data kopieras till i mål arkivet.
- *Data_Destination_Directory* är katalog Sök vägen under roten på den plats där data kopieras till i mål arkivet.
- *Data_Destination_Table_Name* är den plats där data kopieras till i mål lagret (gäller när "Azure Synapse Analytics" är valt som data mål).
- *Data_Destination_Folder_Path* är den plats där data kopieras till i mål lagret (gäller när "fil system" eller "Azure Data Lake Storage gen1" är valt som data mål).
- *Control_Table_Table_Name* är den externa kontroll tabell där värdet för hög vatten märket lagras.
- *Control_Table_Column_Name* är kolumnen i den externa kontroll tabell som lagrar värdet för den högsta vatten märket.

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Utforska den käll tabell som du vill läsa in och definiera den övre vatten märkes kolumnen som kan användas för att identifiera nya eller uppdaterade rader. Typen för den här kolumnen kan vara *datetime*, *int* eller liknande. Den här kolumnens värde ökar när nya rader läggs till. I följande exempel käll tabell (data_source_table) kan vi använda kolumnen *LastModifytime* som övre vatten märkes kolumn.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Skapa en kontroll tabell i SQL Server eller Azure SQL Database för att lagra den övre vatten märket för inläsning av delta data. I följande exempel är namnet på kontroll tabellen *watermarktable*. I den här tabellen är *WatermarkValue* den kolumn som lagrar det högsta värdet för vatten märket och dess typ är *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Skapa en lagrad procedur i samma SQL Server-eller Azure SQL Database-instans som du använde för att skapa kontroll tabellen. Den lagrade proceduren används för att skriva det nya värdet med hög vatten märket till den externa kontroll tabellen för inläsning av delta data nästa tillfälle.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Gå till **delta kopian från databas** mal len. Skapa en **ny** anslutning till käll databasen som du vill kopiera data från.

    ![Skapa en ny anslutning till käll tabellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Skapa en **ny** anslutning till mål data lagret som du vill kopiera data till.

    ![Skapa en ny anslutning till mål tabellen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Skapa en **ny** anslutning till den externa kontroll tabellen och den lagrade proceduren som du skapade i steg 2 och 3.

    ![Skapa en ny anslutning till kontroll tabellens data lager](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Välj **Använd den här mallen**.
    
8. Du ser den tillgängliga pipelinen, som du ser i följande exempel:
  
    ![Granska pipelinen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Välj **lagrad procedur**. För **lagrad procedur namn** väljer du **[dbo]. [ update_watermark]**. Välj **import parameter** och välj sedan **Lägg till dynamiskt innehåll**.  

    ![Ange den lagrade procedur aktiviteten](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png)  

10. Skriv innehållet **\@ {Activity (' LookupCurrentWaterMark '). output. FirstRow. NewWatermarkValue}** och välj sedan **Slutför**.  

    ![Skriv innehållet för parametrarna för den lagrade proceduren](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)       
     
11. Välj **Felsök**, ange **parametrarna** och välj sedan **Slutför**.

    ![Välj * * Felsök * *](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Resultat som liknar följande exempel visas:

    ![Granska resultatet](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Du kan skapa nya rader i din käll tabell. Här är ett exempel på SQL-språk för att skapa nya rader:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```

14. Om du vill köra pipelinen igen väljer du **Felsök**, anger **parametrarna** och väljer sedan **Slutför**.

    Du kommer att se att endast nya rader har kopierats till målet.

15. Valfritt Om du väljer Azure Synapse Analytics som data mål måste du också tillhandahålla en anslutning till Azure Blob Storage för mellanlagring, vilket krävs av Azure Synapse Analytics PolyBase. Mallen genererar en behållar Sök väg åt dig. När pipelinen har körts kontrollerar du om behållaren har skapats i Blob Storage.
    
    ![Konfigurera PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Nästa steg

- [Mass kopiering från en databas med hjälp av en kontroll tabell med Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Kopiera filer från flera behållare med Azure Data Factory](solution-template-copy-files-multiple-containers.md)
