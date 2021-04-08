---
title: Date_Bucket (Transact-SQL) – Azure SQL Edge
description: Lär dig mer om att använda Date_Bucket i Azure SQL Edge
keywords: Date_Bucket, SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 9d81419721e94a2e181f094c0e0e64b1b23544a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93073527"
---
# <a name="date_bucket-transact-sql"></a>Date_Bucket (Transact-SQL)

Den här funktionen returnerar det datetime-värde som motsvarar början av varje datetime-Bucket, från den tidstämpel som definieras av `origin` parametern eller som standard ursprung svärdet för `1900-01-01 00:00:00.000` om ursprungs parametern inte har angetts. 

Se [datum-och tids data typer och funktioner &#40;Transact-SQL&#41;](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql/) för en översikt över alla data typer och funktioner för Transact-SQL-datum och-tid.

[Konventioner för Transact-SQL-syntax](/sql/t-sql/language-elements/transact-sql-syntax-conventions-transact-sql/)

## <a name="syntax"></a>Syntax

```sql
DATE_BUCKET (datePart, number, date, origin)
```

## <a name="arguments"></a>Argument

*datePart*

Den del av *datumet* som används med parametern "tal". t.ex. År, månad, minut, sekund osv.

> [!NOTE]
> `DATE_BUCKET` accepterar inte användardefinierade variabel motsvarigheter för *datepPart* -argumenten.
  
|*datePart*|Förkortningar|  
|---|---|
|**dagen**|**DD**, **d**|  
|**vecka**|**veckor**, **WW**| 
|**månad**|**mm**, **m**|
|**början**|**QQ**, **q**|  
|**år**|**yy**, **åååå**|  
|**timkostnad**|**hh**|  
|**minut**|**mi**, **n**|  
|**senare**|**SS**, **s**|  
|**tiden**|**millisekund**|  

*många*

Det heltals värde som bestämmer bredden på Bucket tillsammans med argumentet *DatumDel* . Detta representerar bredden på dataPart-buckets från ursprungs tiden. **`This argument cannot be a negative integer value`**. 

*date*

Ett uttryck som kan matcha till något av följande värden:

+ **date**
+ **datetime**
+ **DateTimeOffset**
+ **datetime2**
+ **smalldatetime**
+ **tid**

För *datum* `DATE_BUCKET` accepterar att ett kolumn uttryck, ett uttryck eller en användardefinierad variabel om de matchar någon av de data typer som anges ovan.

**Kommer** 

Ett valfritt uttryck som kan matcha till något av följande värden:

+ **date**
+ **datetime**
+ **DateTimeOffset**
+ **datetime2**
+ **smalldatetime**
+ **tid**

Data typen för `Origin` måste matcha `Date` Parameterns datatyp. 

`DATE_BUCKET` använder ett standardvärdet för ursprungligt datum `1900-01-01 00:00:00.000` , d.v.s. 12:00 am på måndagen, januari 1 1900, om inget ursprungs värde har angetts för funktionen.

## <a name="return-type"></a>Returtyp

Data typen returnerat värde för den här metoden är dynamisk. Retur typen beror på vilket argument som anges `date` . Om en giltig Indatatyp anges `date` `DATE_BUCKET` returneras samma datatyp. `DATE_BUCKET` genererar ett fel om en stränglitteral anges för `date` parametern.

## <a name="return-values"></a>Retur värden

### <a name="understanding-the-output-from-date_bucket"></a>Förstå utdata från `DATE_BUCKET`

`Date_Bucket` Returnerar det senaste datum-eller tids värdet, som motsvarar DatumDel och Number-parametern. I uttrycken nedan returneras till exempel `Date_Bucket` utdata-värdet för `2020-04-13 00:00:00.0000000` , eftersom utdata beräknas baserat på en veckas buckets från standard ursprungs tiden för `1900-01-01 00:00:00.000` . Värdet `2020-04-13 00:00:00.0000000` är 6276 veckor från ursprung svärdet för `1900-01-01 00:00:00.000` . 

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 1, @date)
```

För alla uttryck nedan returneras samma utdata-värde `2020-04-13 00:00:00.0000000` . Detta beror på att `2020-04-13 00:00:00.0000000` är 6276 veckor från start datumet och 6276 är delbar med 2, 3, 4 och 6.

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 2, @date)
Select DATE_BUCKET(wk, 3, @date)
Select DATE_BUCKET(wk, 4, @date)
Select DATE_BUCKET(wk, 6, @date)
```

Utdata för uttrycket nedan är `2020-04-06 00:00:00.0000000` , vilket är 6275 veckor från standard start tiden `1900-01-01 00:00:00.000` .

```sql
declare @date datetime2 = '2020-04-15 21:22:11'
Select DATE_BUCKET(wk, 5, @date)
```

Utdata för uttrycket nedan är `2020-06-09 00:00:00.0000000` , vilket är 75 veckor från den angivna ursprungs tiden `2019-01-01 00:00:00` .

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(wk, 5, @date, @origin)
```

## <a name="datepart-argument"></a>DatumDel-argument

**DAYOFYEAR**, **Day** och **veckodag** returnerar samma värde. Varje *DatumDel* och dess förkortningar returnerar samma värde.
  
## <a name="number-argument"></a>tal argument

Argumentet *Number* får inte överskrida intervallet med positiva **heltals** värden. I följande instruktioner överskrider argumentet för *tal* intervallet **int** med 1. Följande fel meddelande returneras i följande instruktion: "`Msg 8115, Level 16, State 2, Line 2. Arithmetic overflow error converting expression to data type int."`
  
```sql
declare @date datetime2 = '2020-04-30 00:00:00'
Select DATE_BUCKET(dd, 2147483648, @date)
```  

Om ett negativt värde för tal skickas till `Date_Bucket` funktionen returneras följande fel. 

```sql
Msg 9834, Level 16, State 1, Line 1
Invalid bucket width value passed to date_bucket function. Only positive values are allowed.
````

## <a name="date-argument"></a>datum argument  

`DATE_BUCKET` returnera bas värdet som motsvarar `date` argumentets datatyp. I följande exempel returneras ett output-värde med datetime2-datatype. 

```sql
Select DATE_BUCKET(dd, 10, SYSUTCDATETIME())
```

## <a name="origin-argument"></a>ursprungs argument  

Data typen för `origin` `date` argumenten och i måste vara samma. Om olika data typer används genereras ett fel.

## <a name="remarks"></a>Kommentarer

Använd `DATE_BUCKET` i följande satser:

+ GROUP BY
+ HAVING
+ ORDNA EFTER
+ SELECT \<list>
+ WHERE

## <a name="examples"></a>Exempel

### <a name="a-calculating-date_bucket-with-a-bucket-width-of-1-from-the-origin-time"></a>A. Beräkna Date_Bucket med en Bucket-bredd på 1 från ursprungs tiden

Var och en av dessa uttryck ökar *date_bucket* med en Bucket-bredd på 1 från ursprungs tiden:

```sql
declare @date datetime2 = '2020-04-30 21:21:21'
Select 'Week',  DATE_BUCKET(wk, 1, @date)
Union All
Select 'Day',  DATE_BUCKET(dd, 1, @date)
Union All
Select 'Hour',  DATE_BUCKET(hh, 1, @date)
Union All
Select 'Minutes',  DATE_BUCKET(mi, 1, @date)
Union All
Select 'Seconds',  DATE_BUCKET(ss, 1, @date)
```

Här är resultatuppsättningen.

```sql
Week    2020-04-27 00:00:00.0000000
Day     2020-04-30 00:00:00.0000000
Hour    2020-04-30 21:00:00.0000000
Minutes 2020-04-30 21:21:00.0000000
Seconds 2020-04-30 21:21:21.0000000
```

### <a name="b-using-expressions-as-arguments-for-the-number-and-date-parameters"></a>B. Använda uttryck som argument för tal-och datum parametrarna

De här exemplen använder olika typer av uttryck som argument för parametrarna för *tal* och *datum* . Dessa exempel skapas med hjälp av databasen "AdventureWorksDW2017".
  
#### <a name="specifying-user-defined-variables-as-number-and-date"></a>Ange användardefinierade variabler som tal och datum  

I det här exemplet anges användardefinierade variabler som argument för *tal* och *datum*:
  
```sql
DECLARE @days int = 365,
        @datetime datetime2 = '2000-01-01 01:01:01.1110000'; /* 2000 was a leap year */;  
SELECT Date_Bucket(day, @days, @datetime);
```

Här är resultatuppsättningen.

```sql
---------------------------
1999-12-08 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-a-column-as-date"></a>Ange en kolumn som datum

I exemplet nedan beräknar vi summan av OrderQuantity och summan av enhets pris grupperade per vecko dags datum buckets.
  
```sql
SELECT
    Date_Bucket(week, 1 ,cast(Shipdate as datetime2)) AS ShippedDateBucket
    ,Sum(OrderQuantity)  As SumOrderQuantity
    ,Sum(UnitPrice) As SumUnitPrice
FROM dbo.FactInternetSales FIS
where Shipdate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
Group by Date_Bucket(week, 1 ,cast(Shipdate as datetime2))
order by 1
```  

Här är resultatuppsättningen.
  
```sql
ShippedDateBucket           SumOrderQuantity SumUnitPrice
--------------------------- ---------------- ---------------------
2011-01-03 00:00:00.0000000 21               65589.7546
2011-01-10 00:00:00.0000000 27               89938.5464
2011-01-17 00:00:00.0000000 31               104404.9064
2011-01-24 00:00:00.0000000 36               118525.6846
2011-01-31 00:00:00.0000000 39               123555.431
2011-02-07 00:00:00.0000000 35               109342.351
2011-02-14 00:00:00.0000000 32               107804.8964
2011-02-21 00:00:00.0000000 37               119456.3428
2011-02-28 00:00:00.0000000 9                28968.6982
```  

#### <a name="specifying-scalar-system-function-as-date"></a>Ange skalär system funktion som datum

I det här exemplet anges `SYSDATETIME` *datum*. Det exakta värde som returneras beror på dag och tidpunkt för körning av instruktionen:
  
```sql
SELECT Date_Bucket(wk, 10, SYSDATETIME());  
```  

Här är resultatuppsättningen.

```sql
---------------------------
2020-03-02 00:00:00.0000000

(1 row affected)
```  

#### <a name="specifying-scalar-subqueries-and-scalar-functions-as-number-and-date"></a>Ange skalära under frågor och skalära funktioner som tal och datum

I det här exemplet används skalära `MAX(OrderDate)` under frågor, som argument för *tal* och *datum*. `(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100)` fungerar som ett artificiellt argument för parametern Number, för att visa hur du väljer ett *tal* argument från en värde lista.
  
```sql
SELECT DATE_BUCKET(week,(SELECT top 1 CustomerKey FROM dbo.DimCustomer where GeographyKey > 100),  
    (SELECT MAX(OrderDate) FROM dbo.FactInternetSales));  
```  
  
#### <a name="specifying-numeric-expressions-and-scalar-system-functions-as-number-and-date"></a>Ange numeriska uttryck och skalära system funktioner som tal och datum

I det här exemplet används ett numeriskt uttryck ((10/2)) och skalära system funktioner (SYSDATETIME) som argument för tal och datum.
  
```sql
SELECT Date_Bucket(week,(10/2), SYSDATETIME());
```

#### <a name="specifying-an-aggregate-window-function-as-number"></a>Ange en sammansatt fönster funktion som antal

I det här exemplet används en mängd fönster funktion som ett argument för *tal*.
  
```sql
Select 
    DISTINCT DATE_BUCKET(day, 30, Cast([shipdate] as datetime2)) as DateBucket,
    First_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as First_Value_In_Bucket,
    Last_Value([SalesOrderNumber]) OVER (Order by DATE_BUCKET(day, 30, Cast([shipdate] as datetime2))) as Last_Value_In_Bucket
    from [dbo].[FactInternetSales]
Where ShipDate between '2011-01-03 00:00:00.000' and '2011-02-28 00:00:00.000'
order by DateBucket
GO  
``` 
### <a name="c-using-a-non-default-origin-value"></a>C. Använd ett värde som inte är standard ursprung

I det här exemplet används ett icke-Orgin värde för att generera datum-buckets. 

```sql
declare @date datetime2 = '2020-06-15 21:22:11'
declare @origin datetime2 = '2019-01-01 00:00:00'
Select DATE_BUCKET(hh, 2, @date, @origin)
```

## <a name="see-also"></a>Se även

[OMVANDLA och konvertera &#40;Transact-SQL&#41;](/sql/t-sql/functions/cast-and-convert-transact-sql/)
