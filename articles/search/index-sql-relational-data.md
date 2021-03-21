---
title: Utforma SQL-relationella data för import och indexering
titleSuffix: Azure Cognitive Search
description: Lär dig att modellera Relations data, normaliserade till en fast resultat uppsättning, för indexering och fullständig texts ökning i Azure Kognitiv sökning.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88924528"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Så här modellerar du Relations-SQL-data för import och indexering i Azure Kognitiv sökning

Azure Kognitiv sökning accepterar en planad rad uppsättning som inmatad till [indexerings pipelinen](search-what-is-an-index.md). Om dina källdata härstammar från kopplade tabeller i en SQL Server Relations databas, förklarar den här artikeln hur du skapar resultat uppsättningen och hur du skapar en överordnad-underordnad relation i ett Azure Kognitiv sökning-index.

Som en illustration kommer vi att referera till en hypotetisk hotell databas, baserat på [demo data](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Anta att databasen består av en hotell $-tabell med 50 hotell och en rum $-tabell med utrymmen av varierande typer, priser och bekvämligheterna, för totalt 750-rum. Det finns en 1-till-många-relation mellan tabellerna. I vårt tillvägagångs sätt kommer en vy att tillhandahålla den fråga som returnerar 50 rader, en rad per hotell, med associerad rums information inbäddad i varje rad.

   ![Tabeller och Visa i hotell databasen](media/index-sql-relational-data/hotels-database-tables-view.png "Tabeller och Visa i hotell databasen")


## <a name="the-problem-of-denormalized-data"></a>Problemet med denormaliserade data

En av utmaningarna i att arbeta med en-till-många-relationer är att standard frågor som bygger på kopplade tabeller kommer att returnera avnormaliserade data, som inte fungerar bra i ett Azure Kognitiv sökning-scenario. Tänk på följande exempel som ansluter till hotell och rum.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
Resultat från den här frågan returnerar alla hotell fält, följt av alla rums fält, med preliminär information om hotellet som upprepas för varje rums värde.

   ![Avnormaliserade data, redundanta hotell data när rums fält läggs till](media/index-sql-relational-data/denormalize-data-query.png "Avnormaliserade data, redundanta hotell data när rums fält läggs till")


Även om den här frågan lyckas på ytan (vilket ger alla data i en fast rad uppsättning), går det inte att leverera rätt dokument struktur till den förväntade Sök upplevelsen. Under indexeringen skapar Azure Kognitiv sökning ett sökdokument för varje rad som infogas. Om dina Sök dokument visas som ovan, skulle du ha uppfattat dubbletter-sju separata dokument för det dubbla välvda hotellet. En fråga på "hotell i Florida" skulle returnera sju resultat för bara det dubbla välvda hotellet, och sedan skicka andra relevanta hotell till Sök resultaten.

Om du vill få en förväntad upplevelse av ett dokument per hotell bör du tillhandahålla en rad uppsättning i rätt kornig het, men med fullständig information. Lyckligt vis kan du enkelt göra detta genom att använda teknikerna i den här artikeln.

## <a name="define-a-query-that-returns-embedded-json"></a>Definiera en fråga som returnerar inbäddad JSON

För att leverera den förväntade Sök upplevelsen bör din data uppsättning bestå av en rad för varje sökdokument i Azure Kognitiv sökning. I vårt exempel vill vi ha en rad för varje hotell, men vi vill även att våra användare ska kunna söka efter andra rums fält som de bryr sig om, till exempel natt pris, storlek och antal förekomster, eller en vy av stranden, som är en del av en rums information.

Lösningen är att samla in rums information som kapslad JSON och sedan infoga JSON-strukturen i ett fält i en vy, som du ser i det andra steget. 

1. Anta att du har två anslutna tabeller, hotell $ och-rum $, som innehåller information om 50 hotell-och 750-rum och som är kopplade till HotelID-fältet. Individuellt innehåller tabellerna 50 hotell och 750 relaterade rum.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Skapa en vy bestående av alla fält i den överordnade tabellen ( `SELECT * from dbo.Hotels$` ), med tillägg av fältet nytt *rum* som innehåller utdata från en kapslad fråga. En **for JSON Auto** -sats på `SELECT * from dbo.Rooms$` strukturer utdata som JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   Följande skärm bild visar den resulterande vyn med *rummen* nvarchar-fältet längst ned. Fältet *rum* finns bara i vyn HotelRooms.

   ![HotelRooms vy](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms vy")

1. Kör `SELECT * FROM dbo.HotelRooms` för att hämta rad uppsättningen. Den här frågan returnerar 50 rader, en per hotell, med associerad rums information som en JSON-samling. 

   ![Rad uppsättning från HotelRooms vy](media/index-sql-relational-data/hotelrooms-rowset.png "Rad uppsättning från HotelRooms vy")

Den här rad uppsättningen är nu redo att importeras till Azure Kognitiv sökning.

> [!NOTE]
> Den här metoden förutsätter att Embedded JSON är under den [maximala kolumn storleks gränsen på SQL Server](/sql/sql-server/maximum-capacity-specifications-for-sql-server). 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Använd en komplex samling för "många"-sidan i en en-till-många-relation

På Azure Kognitiv sökning-sidan skapar du ett index schema som modellerar en-till-många-relation med kapslad JSON. Resultat uppsättningen som du skapade i föregående avsnitt motsvarar vanligt vis det index schema som anges nedan (vi har klippt ut några fält för det kortfattat).

Följande exempel liknar exemplet i [hur du kan modellera komplexa data typer](search-howto-complex-data-types.md#creating-complex-fields). *Rummens* struktur, som har fokus till den här artikeln, finns i fält samlingen för ett index med namnet *Hotels*. I det här exemplet visas också en komplex typ för *adress*, som skiljer sig från *rummen* i att den består av en fast uppsättning objekt, i stället för det flera, godtyckliga antal objekt som tillåts i en samling.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Med hänsyn till föregående resultat uppsättning och index schemat ovan har du alla nödvändiga komponenter för en lyckad indexerings åtgärd. Den sammanställda data uppsättningen uppfyller indexerings kraven men bevarar detaljerad information. I Azure Kognitiv sökning-indexet är Sök Resultat lätt att hitta i hotellbaserade entiteter, samtidigt som sammanhanget för enskilda rum och deras attribut bevaras.

## <a name="next-steps"></a>Nästa steg

Med din egen data uppsättning kan du använda [guiden Importera data](search-import-data-portal.md) för att skapa och läsa in indexet. Guiden identifierar inbäddad JSON-samling, till exempel den som finns i *rummen*, och härleder ett index schema som innehåller en komplex typ samling. 

  ![Index som härletts av guiden Importera data](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Index som härletts av guiden Importera data")

Prova följande snabb start för att lära dig de grundläggande stegen i guiden Importera data.

> [!div class="nextstepaction"]
> [Snabb start: skapa ett Sök index med Azure Portal](search-get-started-portal.md)