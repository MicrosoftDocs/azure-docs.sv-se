---
title: Optimera autovakuum – Azure Database for PostgreSQL-enskild server
description: Den här artikeln beskriver hur du kan optimera autovakuum på en Azure Database for PostgreSQL-enskild server
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 07/09/2020
ms.openlocfilehash: a94afc1ab970c2cd3f509c86efba4e455d46fd13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012569"
---
# <a name="optimize-autovacuum-on-an-azure-database-for-postgresql---single-server"></a>Optimera autovakuum på en Azure Database for PostgreSQL-enskild server

Den här artikeln beskriver hur du effektivt optimerar autovakuum på en Azure Database for PostgreSQL-Server.

## <a name="overview-of-autovacuum"></a>Översikt över autovakuum

PostgreSQL använder sig av samtidighets kontroll mellan versioner (MVCC) för att tillåta större databas samtidighet. Varje uppdatering resulterar i en INSERT-och Delete-åtgärd, och varje borttagnings resultat i rader blir mjuka – markerade för borttagning. Mjuk markering identifierar döda tupler som kommer att rensas senare. PostgreSQL kör ett vakuum-jobb för att utföra dessa uppgifter.

Ett vakuum-jobb kan utlösas manuellt eller automatiskt. Det finns fler döda tupleer när databasen upplever frekventa uppdaterings-eller borttagnings åtgärder. Det finns färre döda tupleer när databasen är inaktiv. Du behöver vakuum oftare när databas belastningen är hög, vilket gör att vakuum-jobben kan vara olämpliga *manuellt* .

Autovakuum kan konfigureras och dra nytta av justeringen. Standardvärden som PostgreSQL levereras med försök att se till att produkten fungerar på alla typer av enheter. Dessa enheter innehåller Raspberry-Pis. De idealiska konfigurations värdena är beroende av:

- Totalt antal tillgängliga resurser, till exempel SKU och lagrings storlek.
- Resursanvändning.
- Enskilda objekt egenskaper.

## <a name="autovacuum-benefits"></a>Autovakuum-förmåner

Om du inte behöver vakuum från tid till tid kan de döda tupler som ackumuleras leda till:

- Data överdriven storlek, till exempel större databaser och tabeller.
- Större, optimala index.
- Ökat I/O.

## <a name="monitor-bloat-with-autovacuum-queries"></a>Övervaka överdriven storlek med autovakuum-frågor

Följande exempel fråga är utformad för att identifiera antalet döda och aktiva tupler i en tabell med namnet XYZ:

```sql
SELECT relname, n_dead_tup, n_live_tup, (n_dead_tup/ n_live_tup) AS DeadTuplesRatio, last_vacuum, last_autovacuum FROM pg_catalog.pg_stat_all_tables WHERE relname = 'XYZ' order by n_dead_tup DESC;
```

## <a name="autovacuum-configurations"></a>Autovakuum-konfigurationer

De konfigurations parametrar som styr autovakuumet baseras på svar på två viktiga frågor:

- När ska det starta?
- Hur mycket bör den rensas när den har startats?

Här följer några konfigurations parametrar för autovakuum som du kan uppdatera baserat på föregående frågor, tillsammans med viss vägledning.

Parameter|Beskrivning|Standardvärde
---|---|---
autovacuum_vacuum_threshold|Anger det minsta antalet uppdaterade eller borttagna tupler som behövs för att utlösa en vakuum-åtgärd i en tabell. Standardvärdet är 50 tuples. Ange bara den här parametern i filen postgresql. conf eller på Server kommando raden. Om du vill åsidosätta inställningen för enskilda tabeller ändrar du tabellens lagrings parametrar.|50
autovacuum_vacuum_scale_factor|Anger en bråkdel av den tabell storlek som ska läggas till autovacuum_vacuum_threshold när du bestämmer om en vakuum-åtgärd ska utlösas. Standardvärdet är 0,2, vilket är 20 procent av tabell storleken. Ange bara den här parametern i filen postgresql. conf eller på Server kommando raden. Om du vill åsidosätta inställningen för enskilda tabeller ändrar du tabellens lagrings parametrar.|0,2
autovacuum_vacuum_cost_limit|Anger det kostnads gräns värde som används för automatisk vakuum-åtgärd. Om-1 har angetts, vilket är standardvärdet används det vanliga vacuum_cost_limit svärdet. Om det finns fler än en arbets tagare fördelas värdet proportionellt mellan de arbeten som används för autovakuum. Summan av gränserna för varje anställd överskrider inte värdet för den här variabeln. Ange bara den här parametern i filen postgresql. conf eller på Server kommando raden. Om du vill åsidosätta inställningen för enskilda tabeller ändrar du tabellens lagrings parametrar.|-1
autovacuum_vacuum_cost_delay|Anger det kostnads fördröjnings värde som används vid automatisk vakuum-åtgärd. Om-1 anges används det vanliga vacuum_cost_delay svärdet. Standardvärdet är 20 millisekunder. Ange bara den här parametern i filen postgresql. conf eller på Server kommando raden. Om du vill åsidosätta inställningen för enskilda tabeller ändrar du tabellens lagrings parametrar.|20 MS
autovacuum_naptime | Anger den minsta fördröjningen mellan autovakuum som körs på en viss databas. I varje tur undersöker daemon databasen och utfärdar vakuum och ANALYSerar kommandon efter behov för tabeller i databasen. Fördröjningen mäts i sekunder. Ange bara den här parametern i filen postgresql. conf eller på Server kommando raden.| 15 s
autovacuum_max_workers | Anger det maximala antalet autovakuum-processer, förutom autovakuum-startaren, som kan köras vid ett och samma tillfälle. Standardvärdet är tre. Ange bara den här parametern på Server start.|3

Om du vill åsidosätta inställningarna för enskilda tabeller ändrar du tabellens lagrings parametrar.

## <a name="autovacuum-cost"></a>Autovakuum-kostnad

Här är "kostnader" för att köra en vakuum-åtgärd:

- De data sidor som vakuumet körs på är låsta.
- Beräkning och minne används när ett vakuum jobb körs.

Därför kan du inte köra vakuum-jobb antingen för ofta eller för sällan. Ett vakuum-jobb måste anpassas till arbets belastningen. Testa alla ändringar av autovakuum-parametern på grund av kompromisserna för var och en.

## <a name="autovacuum-start-trigger"></a>Autovakuum-start-utlösare

Autovakuum utlöses när antalet döda tupleer överskrider autovacuum_vacuum_threshold + autovacuum_vacuum_scale_factor * reltuples. Här är reltuples en konstant.

Rensning från autovakuum måste hålla databasen inläst. Annars kan du få slut på lagring och få en allmän minskning av frågorna. Periodiseras över tid, den hastighet med vilken en vakuum-åtgärd rensar döda tupler ska vara lika med den hastighet med vilken död tupler skapas.

Databaser med många uppdateringar och borttagningar har fler döda tupler och behöver mer utrymme. I allmänhet har databaser med många uppdateringar och ta bort fördelarna med låga värden för autovacuum_vacuum_scale_factor och autovacuum_vacuum_threshold. De låga värdena förhindrar långvarig ackumulering av döda tupleer. Du kan använda högre värden för båda parametrarna med mindre databaser eftersom behovet av vakuum är mindre brådskande. Ett frekvent vakuum är till kostnaden för data bearbetning och minne.

Standard skalnings faktorn på 20 procent fungerar bra i tabeller med en låg procent andel döda tupler. Det fungerar inte bra med tabeller med en hög procent andel döda tupler. I en 20 GB-tabell översätts exempelvis den här skalnings faktorn till 4 GB döda tupler. I en tabell med 1 TB är det 200 GB döda tupler.

Med PostgreSQL kan du ange dessa parametrar på tabell nivå eller instans nivå. I dag kan du ange dessa parametrar på tabell nivå endast i Azure Database for PostgreSQL.

## <a name="estimate-the-cost-of-autovacuum"></a>Uppskatta kostnaden för autovakuum

Att köra autovakuum är "kostsam" och det finns parametrar för att kontrol lera körningen av vakuum-åtgärder. Följande parametrar hjälper till att beräkna kostnaden för att köra vakuum:

- vacuum_cost_page_hit = 1
- vacuum_cost_page_miss = 10
- vacuum_cost_page_dirty = 20

Vakuum-processen läser fysiska sidor och söker efter döda tupler. Varje sida i shared_buffers anses ha en kostnad på 1 (vacuum_cost_page_hit). Alla andra sidor anses ha en kostnad på 20 (vacuum_cost_page_dirty), om det finns död tupler eller 10 (vacuum_cost_page_miss), om det inte finns några döda tupleer. Vakuum-åtgärden stoppas när processen överskrider autovacuum_vacuum_cost_limit.

När gränsen har nåtts väntar processen i vilo läge för den varaktighet som anges av parametern autovacuum_vacuum_cost_delay innan den startas igen. Om gränsen inte uppnås startar autovakuumet efter värdet som anges av parametern autovacuum_nap_time.

I Sammanfattning kontrollerar parametrarna autovacuum_vacuum_cost_delay och autovacuum_vacuum_cost_limit hur mycket data rensning som tillåts per tidsenhet. Observera att standardvärdena är för låga för de flesta pris nivåer. De optimala värdena för dessa parametrar är pris nivå beroende och bör konfigureras enligt detta.

Parametern autovacuum_max_workers fastställer det maximala antalet autovakuum-processer som kan köras samtidigt.

Med PostgreSQL kan du ange dessa parametrar på tabell nivå eller instans nivå. I dag kan du ange dessa parametrar på tabell nivå endast i Azure Database for PostgreSQL.

## <a name="optimize-autovacuum-per-table"></a>Optimera autovakuum per tabell

Du kan konfigurera alla tidigare konfigurations parametrar per tabell. Här är ett exempel:

```sql
ALTER TABLE t SET (autovacuum_vacuum_threshold = 1000);
ALTER TABLE t SET (autovacuum_vacuum_scale_factor = 0.1);
ALTER TABLE t SET (autovacuum_vacuum_cost_limit = 1000);
ALTER TABLE t SET (autovacuum_vacuum_cost_delay = 10);
```

Autovakuum är en synkron process per tabell. Den större procent andelen döda tupler som en tabell har, desto högre är "kostnad" för autovakuum. Du kan dela tabeller med en hög grad av uppdateringar och borttagningar i flera tabeller. Genom att dela tabeller kan du parallellisera autovakuumet och minska kostnaden för att slutföra autovakuumet i en tabell. Du kan också öka antalet parallella autovakuum-arbetskrafter för att säkerställa att arbets tagarna har schemalagts.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder och finjusterar autovakuum finns i följande PostgreSQL-dokumentation:

- [Kapitel 18, Server konfiguration](https://www.postgresql.org/docs/9.5/static/runtime-config-autovacuum.html)
- [Kapitel 24, rutinmässiga databas underhålls uppgifter](https://www.postgresql.org/docs/9.6/static/routine-vacuuming.html)
