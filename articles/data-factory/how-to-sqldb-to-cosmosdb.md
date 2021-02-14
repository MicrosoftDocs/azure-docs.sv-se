---
title: Migrera Azure SQL Database tabeller till Azure-CosmosDB med Azure Data Factory
description: Ta ett befintligt normaliserat databas schema från Azure SQL Database och migrera till en avnormaliserad Azure CosmosDB-behållare med Azure Data Factory.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/29/2020
ms.openlocfilehash: 3d67ac9474704fac39dbe7eb91aead5c4babc4ce
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383950"
---
# <a name="migrate-normalized-database-schema-from-azure-sql-database-to-azure-cosmosdb-denormalized-container"></a>Migrera normaliserat databasschema från Azure SQL Database till avnormaliserad Azure CosmosDB-container

Den här guiden förklarar hur du tar ett befintligt normaliserat databas schema i Azure SQL Database och konverterar det till ett denormaliserat Azure CosmosDB-schema för inläsning till Azure CosmosDB.

SQL-scheman modelleras vanligt vis med hjälp av en tredje normal form, vilket resulterar i normaliserade scheman som ger hög data integritet och färre duplicerade data värden. Frågor kan sammanfoga entiteter i tabeller för läsning. CosmosDB är optimerad för snabb transaktioner och frågor i en samling eller behållare via avnormaliserade scheman med data som finns i ett dokument.

Med hjälp av Azure Data Factory skapar vi en pipeline som använder ett enda mappnings data flöde för att läsa från två Azure SQL Database normaliserade tabeller som innehåller primära och externa nycklar som entitetsrelation. ADF ansluter dessa tabeller till en enda ström med hjälp av data flöde Spark-motorn, samlar ihop kopplade rader i matriser och producerar enskilda rensade dokument för att infoga i en ny Azure CosmosDB-behållare.

Den här guiden skapar en ny behållare i farten med namnet "Orders" som kommer att använda ```SalesOrderHeader``` ```SalesOrderDetail``` tabellerna och från standard SQL Server AdventureWorks exempel databas. Dessa tabeller representerar försäljnings transaktioner som ingår i ```SalesOrderID``` . Varje unik detalj post har sin egen primär nyckel ```SalesOrderDetailID``` . Relationen mellan rubrik och detalj är ```1:M``` . Vi går med ```SalesOrderID``` i ADF och rullar sedan varje relaterad detalj post till en matris med namnet "Detail".

Den representativa SQL-frågan för den här guiden är:

```
  SELECT
  o.SalesOrderID,
  o.OrderDate,
  o.Status,
  o.ShipDate,
  o.SalesOrderNumber,
  o.ShipMethod,
  o.SubTotal,
  (select SalesOrderDetailID, UnitPrice, OrderQty from SalesLT.SalesOrderDetail od where od.SalesOrderID = o.SalesOrderID for json auto) as OrderDetails
FROM SalesLT.SalesOrderHeader o;
```

Den resulterande CosmosDB-containern kommer att bädda in den inre frågan i ett enda dokument och se ut så här:

![Samling](media/data-flow/cosmosb3.png)

## <a name="create-a-pipeline"></a>Skapa en pipeline

1. Välj **+ ny pipeline** för att skapa en ny pipeline.

2. Lägg till en data flödes aktivitet

3. I data flödes aktiviteten väljer du **nytt data flöde för mappning**.

4. Vi kommer att skapa det här data flödes diagrammet nedan

![Data flödes diagram](media/data-flow/cosmosb1.png)

5. Definiera källan för "SourceOrderDetails". För data uppsättning skapar du en ny Azure SQL Database-datauppsättning som pekar på ```SalesOrderDetail``` tabellen.

6. Definiera källan för "SourceOrderHeader". För data uppsättning skapar du en ny Azure SQL Database-datauppsättning som pekar på ```SalesOrderHeader``` tabellen.

7. Lägg till en härledd kolumn omvandling efter "SourceOrderDetails" på den översta källan. Anropa den nya omvandlingen "TypeCast". Vi måste avrunda ```UnitPrice``` kolumnen och omvandla den till en Double-datatyp för CosmosDB. Ange formeln som: ```toDouble(round(UnitPrice,2))``` .

8. Lägg till en annan härledd kolumn och anropa den "MakeStruct". Det är här som vi ska skapa en hierarkisk struktur för att lagra värdena från tabellen information. Kom ihåg att information är en ```M:1``` relation till huvudet. Namnge den nya strukturen ```orderdetailsstruct``` och skapa hierarkin på det här sättet, och ange varje under kolumn till det inkommande kolumn namnet:

![Skapa struktur](media/data-flow/cosmosb9.png)

9. Nu ska vi gå till försäljnings huvudets källa. Lägg till en kopplings omvandling. För den högra sidan väljer du "MakeStruct". Lämna det inställt på inre koppling och välj ```SalesOrderID``` för båda sidor av kopplings villkoret.

10. Klicka på fliken Data förhands granskning i den nya koppling som du har lagt till så att du kan se resultatet fram till den här punkten. Du bör se alla rubrik rader som är kopplade till detalj raderna. Detta är resultatet av kopplingen som skapas från ```SalesOrderID``` . Härnäst ska vi kombinera detaljerna från vanliga rader i detalj strukturen och aggregera vanliga rader.

![Slå ihop](media/data-flow/cosmosb4.png)

11. Innan vi kan skapa matriser för att avnormalisera dessa rader måste du först ta bort oönskade kolumner och se till att datavärdena stämmer överens med CosmosDB data typer.

12. Lägg till en SELECT-omvandling härnäst och ange fält mappningen så att den ser ut så här:

![Kolumn skrubbare](media/data-flow/cosmosb5.png)

13. Nu ska vi omvandla en valuta kolumn igen, den här gången ```TotalDue``` . Som vi gjorde ovan i steg 7 ställer du in formeln på: ```toDouble(round(TotalDue,2))``` .

14. Här kommer vi att normalisera raderna genom att gruppera efter den gemensamma nyckeln ```SalesOrderID``` . Lägg till en sammanställd transformering och ange gruppen genom till ```SalesOrderID``` .

15. I mängd formeln lägger du till en ny kolumn med namnet "information" och använder den här formeln för att samla in värdena i strukturen som vi skapade tidigare anropade ```orderdetailsstruct``` : ```collect(orderdetailsstruct)``` .

16. Den sammanställda omvandlingen matar bara ut kolumner som ingår i mängd eller gruppera efter formler. Därför måste vi även inkludera kolumnerna från försäljnings huvudet. Det gör du genom att lägga till ett kolumn mönster i samma aggregerade omvandling. Det här mönstret kommer att innehålla alla andra kolumner i utdata:

```instr(name,'OrderQty')==0&&instr(name,'UnitPrice')==0&&instr(name,'SalesOrderID')==0```

17. Använd "denna"-syntax i de andra egenskaperna så att vi behåller samma kolumn namn och använder ```first()``` funktionen som en mängd:

![Aggregera](media/data-flow/cosmosb6.png)

18. Vi är redo att slutföra migreringen genom att lägga till en Sink-omvandling. Klicka på "nytt" bredvid data uppsättning och Lägg till en CosmosDB-datauppsättning som pekar på CosmosDB-databasen. För samlingen kallar vi det "Orders" och har inget schema och inga dokument eftersom det kommer att skapas i farten.

19. I mottagar inställningar, åtgärd för att partitionera nyckel till ```\SalesOrderID``` och samla in till "återskapa". Se till att fliken mappning ser ut så här:

![Skärm bild som visar fliken mappning.](media/data-flow/cosmosb7.png)

20. Klicka på Förhandsgranska data för att se till att du ser dessa 32 rader som ska infogas som nya dokument i din nya behållare:

![Skärm bild som visar fliken Data förhands granskning.](media/data-flow/cosmosb8.png)

Om allting ser bra ut är du nu redo att skapa en ny pipeline, lägga till den här data flödes aktiviteten i den pipelinen och köra den. Du kan köra från fel sökning eller en utlöst körning. Efter några minuter bör du ha en ny avnormaliserad behållare med order som kallas "Orders" i CosmosDB-databasen.

## <a name="next-steps"></a>Nästa steg

* Skapa resten av data flödes logiken med hjälp av [omvandlingar](concepts-data-flow-overview.md)av data flöden.
* [Hämta den slutförda pipeline-mallen](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/SQL%20Orders%20to%20CosmosDB.zip) för den här självstudien och importera mallen till din fabrik.
