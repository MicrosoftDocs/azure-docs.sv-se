---
title: Arbeta med JSON-data
description: Med Azure SQL Database och Azure SQL-hanterad instans kan du parsa, fråga och formatera data i JavaScript Object Notation (JSON).
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
ms.date: 04/19/2020
ms.openlocfilehash: 4258c60335a13a6c0f27588aac1c6cdb185fcf6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92782810"
---
# <a name="getting-started-with-json-features-in-azure-sql-database-and-azure-sql-managed-instance"></a>Komma igång med JSON-funktioner i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Med Azure SQL Database och Azure SQL-hanterad instans kan du analysera och fråga data som representeras i JavaScript Object Notation [(JSON)](https://www.json.org/) -format och exportera Relations data som JSON-text. Följande JSON-scenarier är tillgängliga:

- [Formatera Relations data i JSON-format](#formatting-relational-data-in-json-format) med hjälp av `FOR JSON` satsen.
- [Arbeta med JSON-data](#working-with-json-data)
- [Fråga JSON-data](#querying-json-data) med hjälp av JSON skalära funktioner.
- [Transformering av JSON i tabell format](#transforming-json-into-tabular-format) med `OPENJSON` funktion.

## <a name="formatting-relational-data-in-json-format"></a>Formatera Relations data i JSON-format

Om du har en webb tjänst som tar data från databas skiktet och tillhandahåller ett svar i JSON-format eller på klient sidans JavaScript-ramverk eller bibliotek som accepterar data som är formaterade som JSON, kan du formatera databas innehållet som JSON direkt i en SQL-fråga. Du behöver inte längre skriva program kod som formaterar resultat från Azure SQL Database eller Azure SQL-hanterad instans som JSON, eller inkludera vissa JSON-serialiserande bibliotek för att konvertera resultat från tabell frågor och sedan serialisera objekt till JSON-format. I stället kan du använda FOR JSON-satsen för att formatera SQL-frågeresultat som JSON och använda det direkt i ditt program.

I följande exempel formateras rader från tabellen Sales. Custom som JSON med hjälp av FOR JSON-satsen:

```sql
select CustomerName, PhoneNumber, FaxNumber
from Sales.Customers
FOR JSON PATH
```

Satsen FOR JSON PATH formaterar resultatet från frågan som JSON-text. Kolumn namn används som nycklar, medan cell värden genereras som JSON-värden:

```json
[
{"CustomerName":"Eric Torres","PhoneNumber":"(307) 555-0100","FaxNumber":"(307) 555-0101"},
{"CustomerName":"Cosmina Vlad","PhoneNumber":"(505) 555-0100","FaxNumber":"(505) 555-0101"},
{"CustomerName":"Bala Dixit","PhoneNumber":"(209) 555-0100","FaxNumber":"(209) 555-0101"}
]
```

Resultat uppsättningen formateras som en JSON-matris där varje rad formateras som ett separat JSON-objekt.

SÖKVÄG indikerar att du kan anpassa utdataformatet för JSON-resultatet med hjälp av punkt notation i kolumnalias. Följande fråga ändrar namnet på nyckeln "CustomerName" i JSON-formatet för utdata och placerar telefon-och fax nummer i under objekt för "kontakt":

```sql
select CustomerName as Name, PhoneNumber as [Contact.Phone], FaxNumber as [Contact.Fax]
from Sales.Customers
where CustomerID = 931
FOR JSON PATH, WITHOUT_ARRAY_WRAPPER
```

Utdata från den här frågan ser ut så här:

```json
{
    "Name":"Nada Jovanovic",
    "Contact":{
           "Phone":"(215) 555-0100",
           "Fax":"(215) 555-0101"
    }
}
```

I det här exemplet returnerade vi ett enda JSON-objekt i stället för en matris genom att ange alternativet [WITHOUT_ARRAY_WRAPPER](/sql/relational-databases/json/remove-square-brackets-from-json-without-array-wrapper-option) . Du kan använda det här alternativet om du vet att du returnerar ett enskilt objekt som ett resultat av en fråga.

Huvudvärdet för FOR JSON-satsen är att du kan returnera komplexa hierarkiska data från databasen som har formaterats som kapslade JSON-objekt eller matriser. I följande exempel visas hur du tar med rader från `Orders` tabellen som tillhör `Customer` som en kapslad matris med `Orders` :

```sql
select CustomerName as Name, PhoneNumber as Phone, FaxNumber as Fax,
        Orders.OrderID, Orders.OrderDate, Orders.ExpectedDeliveryDate
from Sales.Customers Customer
    join Sales.Orders Orders
        on Customer.CustomerID = Orders.CustomerID
where Customer.CustomerID = 931
FOR JSON AUTO, WITHOUT_ARRAY_WRAPPER
```

I stället för att skicka separata frågor för att hämta kund information och sedan hämta en lista över relaterade order, kan du hämta alla nödvändiga data med en enda fråga, som du ser i följande exempel på utdata:

```json
{
  "Name":"Nada Jovanovic",
  "Phone":"(215) 555-0100",
  "Fax":"(215) 555-0101",
  "Orders":[
    {"OrderID":382,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":395,"OrderDate":"2013-01-07","ExpectedDeliveryDate":"2013-01-08"},
    {"OrderID":1657,"OrderDate":"2013-01-31","ExpectedDeliveryDate":"2013-02-01"}
  ]
}
```

## <a name="working-with-json-data"></a>Arbeta med JSON-data

Om du inte har rent strukturerade data, om du har komplexa underordnade objekt, matriser eller hierarkiska data, eller om dina data strukturer utvecklas över tid, kan JSON-formatet hjälpa dig att representera en komplex data struktur.

JSON är ett text format som kan användas som vilken annan sträng typ som helst i Azure SQL Database och Azure SQL-hanterad instans. Du kan skicka eller lagra JSON-data som en standard NVARCHAR:

```sql
CREATE TABLE Products (
  Id int identity primary key,
  Title nvarchar(200),
  Data nvarchar(max)
)
go
CREATE PROCEDURE InsertProduct(@title nvarchar(200), @json nvarchar(max))
AS BEGIN
    insert into Products(Title, Data)
    values(@title, @json)
END
```

De JSON-data som används i det här exemplet representeras med hjälp av typen NVARCHAR (MAX). JSON kan infogas i den här tabellen eller anges som ett argument för den lagrade proceduren med hjälp av standarden Transact-SQL syntax, som visas i följande exempel:

```sql
EXEC InsertProduct 'Toy car', '{"Price":50,"Color":"White","tags":["toy","children","games"]}'
```

Alla språk eller bibliotek på klient sidan som fungerar med sträng data i Azure SQL Database och Azure SQL-hanterad instans fungerar också med JSON-data. JSON kan lagras i vilken tabell som helst som stöder typen NVARCHAR, till exempel en Minnesoptimerade tabell eller en tabell med System version. JSON introducerar inte någon begränsning antingen i kod på klient sidan eller i databas skiktet.

## <a name="querying-json-data"></a>Fråga JSON-data

Om du har data som är formaterade som JSON-lagrade i Azure SQL-tabeller kan du använda JSON-funktioner för att använda dessa data i alla SQL-frågor.

JSON-funktioner som är tillgängliga i Azure SQL Database och Azure SQL-hanterad instans låter dig behandla data formaterade som JSON som andra SQL-datatyper. Du kan enkelt extrahera värden från JSON-texten och använda JSON-data i alla frågor:

```sql
select Id, Title, JSON_VALUE(Data, '$.Color'), JSON_QUERY(Data, '$.tags')
from Products
where JSON_VALUE(Data, '$.Color') = 'White'

update Products
set Data = JSON_MODIFY(Data, '$.Price', 60)
where Id = 1
```

Funktionen JSON_VALUE extraherar ett värde från JSON-text som lagras i data kolumnen. Den här funktionen använder en JavaScript-liknande sökväg för att referera till ett värde i JSON-text att extrahera. Det extraherade värdet kan användas i valfri del av SQL-frågan.

Funktionen JSON_QUERY liknar JSON_VALUE. Till skillnad från JSON_VALUE extraherar den här funktionen komplexa under objekt, till exempel matriser eller objekt som placeras i JSON-text.

Med funktionen JSON_MODIFY kan du ange sökvägen till värdet i JSON-texten som ska uppdateras, samt ett nytt värde som kommer att skriva över det gamla. På så sätt kan du enkelt uppdatera JSON-text utan att parsa om hela strukturen.

Eftersom JSON lagras i en vanlig text, finns det inga garantier för att värdena som lagras i text kolumner är korrekt formaterade. Du kan kontrol lera att texten som lagras i JSON-kolumnen är korrekt formaterad genom att använda standard Azure SQL Database kontroll begränsningar och funktionen ISJSON:

```sql
ALTER TABLE Products
    ADD CONSTRAINT [Data should be formatted as JSON]
        CHECK (ISJSON(Data) > 0)
```

Om den inmatade texten är korrekt formaterad JSON, returnerar funktionen ISJSON värdet 1. Vid varje infogning eller uppdatering av JSON-kolumnen, verifierar den här begränsningen att det nya textvärdet inte är en felaktig JSON.

## <a name="transforming-json-into-tabular-format"></a>Omvandla JSON till tabell format

Med Azure SQL Database och Azure SQL-hanterad instans kan du också omvandla JSON-samlingar till tabell format och läsa in eller fråga JSON-data.

Openjson är en tabell värdes funktion som analyserar JSON-text, letar upp en matris med JSON-objekt, itererar igenom elementen i matrisen och returnerar en rad i resultatet för varje element i matrisen.

![JSON-tabell](./media/json-features/image_2.png)

I exemplet ovan kan vi ange var du vill hitta den JSON-matris som ska öppnas (i $. Order Sök väg), vilka kolumner som ska returneras som resultat och var du hittar de JSON-värden som ska returneras som celler.

Vi kan omvandla en JSON-matris i @orders variabeln till en rad med rader, analysera den här resultat uppsättningen eller infoga rader i en standard tabell:

```sql
CREATE PROCEDURE InsertOrders(@orders nvarchar(max))
AS BEGIN

    insert into Orders(Number, Date, Customer, Quantity)
    select Number, Date, Customer, Quantity
    FROM OPENJSON (@orders)
     WITH (
            Number varchar(200),
            Date datetime,
            Customer varchar(200),
            Quantity int
     )
END
```

Samlingen med order som formaterats som en JSON-matris och anges som en parameter till den lagrade proceduren kan parsas och infogas i tabellen Orders.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du integrerar JSON i ditt program finns i följande resurser:

Mer information om olika scenarier för integrering av JSON i programmet finns i demonstrationerna i den här [kanalen 9-videon](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/JSON-as-a-bridge-betwen-NoSQL-and-relational-worlds) eller hitta ett scenario som matchar ditt användnings fall i [JSON-blogg inlägg](/archive/blogs/sqlserverstorageengine/json-in-sql-server-use-cases).