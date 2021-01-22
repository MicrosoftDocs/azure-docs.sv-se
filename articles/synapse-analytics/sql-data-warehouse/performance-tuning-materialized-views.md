---
title: Prestanda justering med materialiserade vyer
description: Lär dig mer om rekommendationer och överväganden som du bör känna till när du använder materialiserade vyer för att förbättra din frågas prestanda.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick; azure-synapse
ms.openlocfilehash: e137611809e2d2beefecfeaea11b4295bf6ba141
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678499"
---
# <a name="performance-tune-with-materialized-views"></a>Prestanda justering med materialiserade vyer

Materialiserade vyer i Azure Synapse SQL-poolen ger en låg underhålls metod för komplexa analytiska frågor för att få snabba prestanda utan någon ändring i frågan. Den här artikeln beskriver den allmänna vägledningen om hur du använder materialiserade vyer.

## <a name="materialized-views-vs-standard-views"></a>Materialiserade vyer jämfört med standardvyer

SQL-poolen i Azure Synapse stöder standard-och materialiserade vyer.  Båda är virtuella tabeller som skapats med SELECT-uttryck och visas för frågor som logiska tabeller.  Vyer kapslar in komplexiteten för vanlig data beräkning och lägger till ett abstraktions lager för beräknings ändringar så att du inte behöver skriva om frågor.  

En standardvy beräknar data varje gång som vyn används.  Det finns inga data lagrade på disken. Användarna använder vanligt vis standardvyer som ett verktyg som hjälper dig att ordna de logiska objekten och frågorna i en SQL-pool.  Om du vill använda en standardvy måste en fråga hänvisa till den.

En materialiserad vy för beräkning, lager och underhåll av data i SQL-poolen precis som en tabell.  Ingen omberäkning krävs varje gång en materialiserad vy används.  Det är anledningen till att frågor som använder alla eller delmängd av data i materialiserade vyer kan få snabbare prestanda.  Även bättre, frågor kan använda en materialiserad vy utan att hänvisa till den, så du behöver inte ändra program koden.  

De flesta av kraven för en standardvy gäller fortfarande för en materialiserad vy. Mer information om syntaxen för materialiserade vyer och andra krav finns i [skapa materialiserad vy som Välj](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)

| Jämförelse                     | Visa                                         | Materialiserad vy
|:-------------------------------|:---------------------------------------------|:--------------------------------------------------------------|
|Visa definition                 | Lagras i SQL-poolen.              | Lagras i SQL-poolen.
|Visa innehåll                    | Genereras varje gång som vyn används.   | Förbehandlade och lagrade i SQL-poolen när du skapar vyn. Uppdateras när data läggs till i de underliggande tabellerna.
|Datauppdatering                    | Alltid uppdaterad                               | Alltid uppdaterad
|Hastighet för att hämta visnings data från komplexa frågor     | Långsam                                         | Snabb  
|Extra lagrings utrymme                   | Nej                                           | Ja
|Syntax                          | CREATE VIEW                                  | SKAPA MATERIALISERAD VY SOM VÄLJ

## <a name="benefits-of-using-materialized-views"></a>Fördelar med att använda materialiserade vyer

En korrekt utformad materialiserad vy ger följande fördelar:

- Minska körnings tiden för komplexa frågor med kopplingar och mängd funktioner. Ju mer komplexa frågan, desto högre potential för körnings sparande. Den mest förmånen erhålls när en frågas beräknings kostnad är hög och den resulterande data uppsättningen är liten.  
- Optimeringen i SQL-poolen kan automatiskt använda distribuerade materialiserade vyer för att förbättra fråge körnings planer.  Den här processen är transparent för användare som tillhandahåller snabbare frågeresultat och kräver inte frågor för att hänvisa till de materialiserade vyerna.
- Kräv lite underhåll i vyerna.  Alla stegvisa data ändringar från bas tabellerna läggs automatiskt till i de materialiserade vyerna på ett synkront sätt.  Med den här designen kan du skicka frågor till materialiserade vyer för att returnera samma data som direkt efter fråga bas tabellerna.
- Data i en materialiserad vy kan distribueras annorlunda än bas tabellerna.  
- Data i materialiserade vyer får samma hög tillgänglighet och återhämtnings förmåner som data i vanliga tabeller.  

De materialiserade vyerna som implementeras i SQL-poolen ger också följande ytterligare fördelar:

Jämfört med andra data lager leverantörer ger de materialiserade vyerna som implementeras i Azure Synapse Analytics även följande ytterligare fördelar:

- Automatisk och synkron data uppdatering med data ändringar i bas tabeller. Ingen användar åtgärd krävs.
- Brett stöd för mängd funktioner. Se [skapa materialiserad vy som Select (Transact-SQL)](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).
- Support för fråga-Specific materialiserad View-rekommendation.  Se [förklaring (Transact-SQL)](/sql/t-sql/queries/explain-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).

## <a name="common-scenarios"></a>Vanliga scenarier  

Materialiserade vyer används vanligt vis i följande scenarier:

**Behovet av att förbättra prestanda för komplexa analytiska frågor mot stora data storlekar**

Komplexa analytiska frågor använder vanligt vis fler agg regerings funktioner och tabell kopplingar, vilket orsakar fler beräknings intensiva åtgärder, till exempel blandade och kopplingar i frågekörningen.  Det är därför som frågorna tar längre tid att slutföra, särskilt i stora tabeller.  

Användare kan skapa materialiserade vyer för data som returneras från vanliga beräkningar av frågor, så det finns ingen omberäkning som krävs när dessa data behövs av frågor, vilket ger lägre beräknings kostnader och snabbare svar på frågor.

**Behöver snabbare prestanda utan eller minsta antal frågor**

Schema-och fråge ändringar i SQL-pooler behålls vanligt vis till ett minimum för att stödja vanliga ETL-åtgärder och rapportering.  Personer kan använda materialiserade vyer för att ställa frågor till prestanda, om kostnaden som uppstår i vyerna kan förskjutas av ökningen i frågans prestanda.

Jämfört med andra justerings alternativ som skalning och statistik hantering, är det en mycket mindre inverkan på produktions förändringar för att skapa och underhålla en materialiserad vy och dess potentiella prestanda ökning är också högre.

- Att skapa eller underhålla materialiserade vyer påverkar inte de frågor som körs mot bas tabellerna.
- Query Optimering kan automatiskt använda distribuerade materialiserade vyer utan direkt visnings referens i en fråga. Den här funktionen minskar behovet av ändring av frågan vid prestanda justering.

**Du behöver en annan strategi för data distribution för snabbare frågans prestanda**

Azure Synapse Analytics är ett system för distribuerad Query-bearbetning.  Data i en SQL-tabell distribueras över 60-noder med en av tre [distributions strategier](sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) (hash, Round_Robin eller replikerad).   

Data distributionen anges i tabellens skapelse tid och förblir oförändrad tills tabellen släpps. Materialiserad vy är en virtuell tabell på disken som stöder hash-och round_robin data distributioner.  Användare kan välja en data distribution som skiljer sig från bas tabellerna men som är optimal för prestanda för frågor som använder de flesta vyer.  

## <a name="design-guidance"></a>Design vägledning

Här är den allmänna vägledningen om hur du använder materialiserade vyer för att förbättra prestanda för frågor:

**Design för din arbets belastning**

Innan du börjar skapa materialiserade vyer är det viktigt att du har en djup förståelse för din arbets belastning i termer av fråge mönster, prioritet, frekvens och storleken på resulterande data.  

Användarna kan köra förklaringar WITH_RECOMMENDATIONS <SQL_statement> för de materialiserade vyer som rekommenderas av frågans optimering.  Eftersom dessa rekommendationer är fråge bara för fråga är en materialiserad vy som fördelar en enskild fråga inte optimal för andra frågor i samma arbets belastning.  

Utvärdera de här rekommendationerna med dina arbets belastnings behov i åtanke.  De idealiska materialiserade vyerna är de som förmånen för arbets Belastningens prestanda.  

**Var medveten om kompromissen mellan snabbare frågor och kostnaden**

För varje materialiserad vy finns det en kostnad för data lagring och en kostnad för att underhålla vyn.  När data ändras i bas tabeller, ökar storleken på den materialiserade vyn och dess fysiska struktur ändras också.  För att undvika att fråga prestanda försämringen underhålls varje materialiserad vy separat av SQL Analytics-motorn.  

Underhålls arbets belastningen blir högre när antalet materialiserade vyer och bas tabell ändringar ökar.   Användarna bör kontrol lera om kostnaden som uppstår från alla materialiserade vyer kan förskjutas med prestanda ökningen för frågan.  

Du kan köra den här frågan för listan över materialiserad vy i en SQL-pool:

```sql
SELECT V.name as materialized_view, V.object_id
FROM sys.views V
JOIN sys.indexes I ON V.object_id= I.object_id AND I.index_id < 2;
```

Alternativ för att minska antalet materialiserade vyer:

- Identifiera vanliga data uppsättningar som ofta används av komplexa frågor i din arbets belastning.  Skapa materialiserade vyer för att lagra dessa data uppsättningar så att optimeringen kan använda dem som bygg stenar när du skapar körnings planer.  

- Ta bort de materialiserade vyerna som har låg användning eller som inte längre behövs.  En inaktive rad materialiserad vy underhålls inte, men den kostar fortfarande lagrings kostnaden.  

- Kombinera materialiserade vyer som skapats på samma eller liknande bas tabeller även om deras data inte överlappar varandra.  Att kombinera materialiserade vyer kan resultera i en större vy än summan av de separata vyerna, men vyn underhålls kostnader bör minska.  Till exempel:

```sql

-- Query 1 would benefit from having a materialized view created with this SELECT statement

SELECT A, SUM(B)
FROM T
GROUP BY A

-- Query 2 would benefit from having a materialized view created with this SELECT statement

SELECT C, SUM(D)
FROM T
GROUP BY C

-- You could create a single mateiralized view of this form

SELECT A, C, SUM(B), SUM(D)
FROM T
GROUP BY A, C

```

**Inte alla prestanda justeringar kräver ändring av fråga**

SQL Analytics-optimeringen kan automatiskt använda distribuerade materialiserade vyer för att förbättra prestanda för frågor.  Detta stöd används transparent för frågor som inte refererar till vyer och frågor som använder agg regeringar som inte stöds i skapande av materialiserade vyer.  Ingen ändring av fråga krävs. Du kan kontrol lera en frågas uppskattade körnings plan för att bekräfta om en materialiserad vy används.  

**Övervaka materialiserade vyer**

En materialiserad vy lagras i SQL-poolen precis som en tabell med grupperat columnstore-index (CCI).  När du läser data från en materialiserad vy genomsöks de CCI-index segmenten och alla eventuella stegvisa ändringar av bas tabellerna tillämpas. När antalet stegvisa ändringar är för högt kan det ta längre tid att lösa en fråga från en materialiserad vy än att direkt fråga bas tabellerna.  

För att undvika prestanda försämring av frågor, är det en bra idé att köra [DBCC PDW_SHOWMATERIALIZEDVIEWOVERHEAD](/sql/t-sql/database-console-commands/dbcc-pdw-showmaterializedviewoverhead-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) för att övervaka vyns overhead_ratio (total_rows/Max (1 base_view_row)).  Användarna bör återskapa den materialiserade vyn om dess overhead_ratio är för hög.

**Materialiserad vy och resultat uppsättnings-cachelagring**

De här två funktionerna introduceras i SQL-analyser runt samma tid för prestanda justering av frågor.  Cachelagring av resultat uppsättningar används för att få hög samtidighet och snabba svar från upprepade frågor mot statiska data.  

För att kunna använda det cachelagrade resultatet måste formen för den begär ande frågan i cachen matcha med den fråga som skapade cacheminnet.  Dessutom måste det cachelagrade resultatet gälla för hela frågan.  

Materialiserade vyer tillåter data ändringar i bas tabellerna.  Data i materialiserade vyer kan tillämpas på en del av en fråga.  Detta stöd tillåter att samma materialiserade vyer används av olika frågor som delar en del beräkning för snabbare prestanda.

## <a name="example"></a>Exempel

I det här exemplet används en TPCDS fråga som söker efter kunder som tillbringar mer pengar via katalog än i butikerna, identifierar önskade kunder och deras ursprungsland/region.   Frågan omfattar att välja de översta 100 posterna från UNION av tre under SELECT-uttryck som involverar SUM () och GROUP BY.

```sql
WITH year_total AS (
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
       ,'s' sale_type
FROM customer
     ,store_sales
     ,date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
       ,'c' sale_type
FROM customer
     ,catalog_sales
     ,date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
UNION ALL
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
       ,'w' sale_type
FROM customer
     ,web_sales
     ,date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
         )
  SELECT TOP 100
                  t_s_secyear.customer_id
                 ,t_s_secyear.customer_first_name
                 ,t_s_secyear.customer_last_name
                 ,t_s_secyear.customer_birth_country
FROM year_total t_s_firstyear
     ,year_total t_s_secyear
     ,year_total t_c_firstyear
     ,year_total t_c_secyear
     ,year_total t_w_firstyear
     ,year_total t_w_secyear
WHERE t_s_secyear.customer_id = t_s_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_c_secyear.customer_id
   AND t_s_firstyear.customer_id = t_c_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_firstyear.customer_id
   AND t_s_firstyear.customer_id = t_w_secyear.customer_id
   AND t_s_firstyear.sale_type = 's'
   AND t_c_firstyear.sale_type = 'c'
   AND t_w_firstyear.sale_type = 'w'
   AND t_s_secyear.sale_type = 's'
   AND t_c_secyear.sale_type = 'c'
   AND t_w_secyear.sale_type = 'w'
   AND t_s_firstyear.dyear+0 =  1999
   AND t_s_secyear.dyear+0 = 1999+1
   AND t_c_firstyear.dyear+0 =  1999
   AND t_c_secyear.dyear+0 =  1999+1
   AND t_w_firstyear.dyear+0 = 1999
   AND t_w_secyear.dyear+0 = 1999+1
   AND t_s_firstyear.year_total > 0
   AND t_c_firstyear.year_total > 0
   AND t_w_firstyear.year_total > 0
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_s_firstyear.year_total > 0 THEN t_s_secyear.year_total / t_s_firstyear.year_total ELSE NULL END
   AND CASE WHEN t_c_firstyear.year_total > 0 THEN t_c_secyear.year_total / t_c_firstyear.year_total ELSE NULL END
           > CASE WHEN t_w_firstyear.year_total > 0 THEN t_w_secyear.year_total / t_w_firstyear.year_total ELSE NULL END
ORDER BY t_s_secyear.customer_id
         ,t_s_secyear.customer_first_name
         ,t_s_secyear.customer_last_name
         ,t_s_secyear.customer_birth_country
OPTION ( LABEL = 'Query04-af359846-253-3');
```

Kontrol lera frågans uppskattade körnings plan.  Det finns 18 sammanfogningar och 17 sammanfognings åtgärder, vilket tar mer tid att köra. Nu ska vi skapa en materialiserad vy för var och en av de tre under SELECT-satserna.

```sql
CREATE materialized view nbViewSS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ss_ext_list_price-ss_ext_wholesale_cost-ss_ext_discount_amt+ss_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.store_sales
     ,dbo.date_dim
WHERE c_customer_sk = ss_customer_sk
   AND ss_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year
GO
CREATE materialized view nbViewCS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(cs_ext_list_price-cs_ext_wholesale_cost-cs_ext_discount_amt+cs_ext_sales_price, 0)/2) year_total
          , count_big(*) as cb
FROM dbo.customer
     ,dbo.catalog_sales
     ,dbo.date_dim
WHERE c_customer_sk = cs_bill_customer_sk
   AND cs_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

GO
CREATE materialized view nbViewWS WITH (DISTRIBUTION=HASH(customer_id)) AS
SELECT c_customer_id customer_id
       ,c_first_name customer_first_name
       ,c_last_name customer_last_name
       ,c_preferred_cust_flag customer_preferred_cust_flag
       ,c_birth_country customer_birth_country
       ,c_login customer_login
       ,c_email_address customer_email_address
       ,d_year dyear
       ,sum(isnull(ws_ext_list_price-ws_ext_wholesale_cost-ws_ext_discount_amt+ws_ext_sales_price, 0)/2) year_total
          , count_big(*) AS cb
FROM dbo.customer
     ,dbo.web_sales
     ,dbo.date_dim
WHERE c_customer_sk = ws_bill_customer_sk
   AND ws_sold_date_sk = d_date_sk
GROUP BY c_customer_id
         ,c_first_name
         ,c_last_name
         ,c_preferred_cust_flag
         ,c_birth_country
         ,c_login
         ,c_email_address
         ,d_year

```

Kontrol lera körnings planen för den ursprungliga frågan igen.  Nu har antalet kopplingar ändrats från 17 till 5 och det finns ingen blandning längre.  Klicka på ikonen filter åtgärd i planen så visas data från de materialiserade vyerna i stället för bas tabeller.  

 ![Plan_Output_List_with_Materialized_Views](./media/performance-tuning-materialized-views/output-list.png)

Med materialiserade vyer körs samma fråga mycket snabbare utan kod ändringar.  

## <a name="next-steps"></a>Nästa steg

Mer utvecklings tips finns i [Översikt över SYNAPSE SQL-pool](sql-data-warehouse-overview-develop.md).
