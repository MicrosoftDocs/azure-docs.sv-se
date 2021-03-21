---
title: Integrera Log Analytics och Excel
description: Hämta en Log Analytics-fråga till Excel och uppdatera resultatet i Excel.
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: f2834e9bd91ecbbf32e0321179c2359862a5b605
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041118"
---
# <a name="integrate-log-analytics-and-excel"></a>Integrera Log Analytics och Excel

Du kan integrera Azure Monitor Log Analytics och Microsoft Excel med M-fråga och Log Analytics API. Med den här integreringen kan du skicka upp till 500 000 poster till Excel så länge den totala volymen av resultaten inte överstiger 61MiB.

> [!NOTE]
> Eftersom Excel är ett lokalt klient program påverkar den lokala maskin-och program varu begränsningarna prestanda och möjlighet att bearbeta stora mängder data.

## <a name="create-your-m-query-in-log-analytics"></a>Skapa din M-fråga i Log Analytics 

1. **Skapa och kör din fråga** i Log Analytics precis som vanligt. Oroa dig inte om du träffar begränsningen för 10 000-poster i användar gränssnittet.  Vi rekommenderar att du använder relativa datum, t. ex. funktionen sedan eller UI-tidsväljaren, så att Excel uppdaterar rätt data uppsättning.
  
2. **Exportera fråga** – när du är nöjd med frågan och dess resultat exporterar du frågan till M med hjälp av Log Analytics **Exportera till Power BI (M fråga)** -meny val under menyn *Exportera* :

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics fråga med alternativet data och export" border="true":::



Om du väljer det här alternativet hämtas en. txt-fil som innehåller M-koden som du kan använda i Excel.

Frågan som visas ovan exporterar följande M-kod. Här är ett exempel på M-koden som exporter ATS för frågan i vårt exempel:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Anslut fråga till Excel 

För att importera frågan. 

1. Öppna Microsoft Excel. 
1. I menyfliksområdet går du till **data** -menyn. Välj **Hämta data**. Från **andra källor** väljer du **Tom fråga**:
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Alternativet importera från tomt i Excel" border="true":::

1. I fönstret Power Query väljer du **avancerad redigerare**:

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Avancerad Frågeredigeraren i Excel" border="true":::

 
1. Ersätt texten i avancerad redigerare med den fråga som exporter ATS från Log Analytics:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Skapa en tom fråga" border="true":::
 
1. Välj **färdig** och **Läs in och Stäng**. Excel kör frågan med Log Analytics-API: et och resultat uppsättningen visas sedan.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Frågeresultat i Excel" border="true":::

> [!Note]
> Om antalet poster är mindre än förväntat, kan volymens resultat överskrida gränsen för 61MiB. Prova att använda `project` eller `project-away` i din fråga för att begränsa kolumnerna till den som du behöver.

##  <a name="refreshing--data"></a>Uppdaterar data

Du kan uppdatera dina data direkt från Excel. I meny gruppen **data** i menyfliksområdet i Excel väljer du knappen **Uppdatera** .
 
## <a name="next-steps"></a>Nästa steg

Mer information om Excel-integration med externa data källor finns i [Importera data från externa data källor (Power Query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a)