---
title: Azure SQL Edge-användning och diagnostik-data konfiguration
description: Lär dig hur du konfigurerar användnings-och diagnostikdata i Azure SQL Edge.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 81b2aabbae148faec06c9ab420bdfecde475b4bb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97605015"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Azure SQL Edge-användning och diagnostik-data konfiguration

Som standard samlar Azure SQL Edge in information om hur deras kunder använder programmet. Mer specifikt samlar Azure SQL Edge in information om distributions upplevelse, användning och prestanda. Den här informationen hjälper Microsoft att förbättra produkten så att den bättre uppfyller kundernas behov. Till exempel samlar Microsoft in information om vilka typer av felkoder kunderna har stötande på, så att vi kan åtgärda relaterade buggar, förbättra vår dokumentation om hur du använder Azure SQL Edge och avgör om funktioner ska läggas till i produkten för att bättre betjäna kunder.

Särskilt kan Microsoft inte skicka någon av följande typer av information via den här mekanismen:

- Alla värden inifrån användar tabeller.
- Autentiseringsuppgifter för inloggning eller annan autentiseringsinformation.
- Personliga eller kunddata.

Följande exempel scenario innehåller information om funktions användning som hjälper dig att förbättra produkten.

En exempel fråga från frågorna som används för insamling och diagnostikdata finns nedan. Frågan identifierar antalet och typerna av olika data källor för strömning som används i Azure SQL Edge. Den här informationen hjälper Microsoft att identifiera vilka strömmande data källor som används ofta, så att Microsoft kan förbättra prestanda och användar upplevelsen som är kopplad till dessa data källor. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js
             on js.stream_id = es.object_id 
    ) ds
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>Inaktivera insamling av användnings data och diagnostikdata

Insamling av användning och diagnostikdata på Azure SQL Edge kan inaktive ras med någon av metoderna nedan.

> [!NOTE]
> Det går inte att inaktivera användnings-och diagnostikdata för Developer-versionen.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>Inaktivera användning och diagnostik med miljövariabler

Om du vill inaktivera data insamling för användning och diagnostik på Azure SQL Edge lägger du till följande miljö variabel och anger värdet till `*False*` . Mer information om hur du konfigurerar Azure SQL Edge med miljövariabler finns [i Konfigurera användning av miljövariabler](configure.md#configure-by-using-environment-variables).

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE – aktiverar insamling av användnings-och diagnostikdata. Det här är standardkonfigurationen.
- FALSe-inaktiverar insamling av användnings-och diagnostikdata.

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>Inaktivera användning och diagnostik med hjälp av MSSQL. conf-filen

Om du vill inaktivera data insamling för användning och diagnostik på Azure SQL Edge lägger du till följande rader i filen MSSQL. conf på den permanenta lagrings enheten som är mappad till mappen/var/opt/MSSQL/i SQL Edge-modulen. Mer information om hur du konfigurerar Azure SQL Edge med hjälp av MSSQL. conf-filen finns i [Konfigurera med hjälp av MSSQL. conf-filen](configure.md#configure-by-using-an-mssqlconf-file).

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>Lokal granskning av användning och insamling av diagnostikdata

Den lokala gransknings komponenten i Azure SQL Edge-användning och insamling av diagnostikdata kan skriva data som samlas in av tjänsten till en angiven mapp, som representerar de data (loggar) som ska skickas till Microsoft. Syftet med den lokala granskningen är att göra det möjligt för kunder att se alla data som Microsoft samlar in med den här funktionen, för efterlevnad, reglering eller sekretess verifierings skäl.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>Aktivera lokal granskning av användnings-och diagnostikdata

Aktivera lokal gransknings användning och diagnostikdata på Azure SQL Edge

1. Skapa en mål katalog för den nya lokala Gransknings logg lagringen. Mål katalogen kan antingen finnas på värden eller i behållaren. I exemplet nedan skapas mål katalogen på samma monterings volym som är mappad till/var/opt/MSSQL/-sökvägen på SQL Edge.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. Konfigurera granskning av användnings-och diagnostikdata med hjälp av antingen miljövariabler eller MSSQL. conf-fil.

   - Använda miljövariabler – Lägg till följande miljö variabel i SQL Edge-distributionen och ange mål katalogen för gransknings filerna.
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - Använda MSSQL. conf-filen – Lägg till följande rader i filen MSSQL. conf och ange mål katalogen för gransknings filerna.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>Nästa steg

- [Ansluta till Azure SQL Edge](connect.md)
- [Skapa en IoT-lösning från slutpunkt till slutpunkt med SQL Edge](tutorial-deploy-azure-resources.md)
