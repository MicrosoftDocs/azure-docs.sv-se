---
title: Migrera data från en PostgreSQL-databas till en Azure Arc-aktiverad PostgreSQL för storskalig Server grupp
titleSuffix: Azure Arc enabled database services
description: Migrera data från en PostgreSQL-databas till en Azure Arc-aktiverad PostgreSQL för storskalig Server grupp
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d9cbfc30b10373ad2a4f4304987dac426b5dcabe
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101643583"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Migrera PostgreSQL Database till Azure Arc Enabled PostgreSQL för storskalig Server grupp

Det här dokumentet beskriver stegen för att hämta din befintliga PostgreSQL-databas (en som inte finns i Azure Arc aktiverat Data Services) i den Azure Arc-aktiverade PostgreSQL för storskalig Server.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>Överväganden

Azure Arc Enabled PostgreSQL storskalig Server Group är community-versionen av PostgreSQL och körs med CitusData-tillägget aktiverat. Det innebär att verktyg som fungerar på PostgreSQL utanför Azure-bågen fungerar med Azure Arc-aktiverade PostgreSQL för storskalig Server.


Med den uppsättning verktyg du använder idag för postgres bör du därför kunna:
1. Säkerhetskopiera postgres-databasen från din instans som ligger utanför Azure-bågen
2. Återställ den i din Azure Arc-aktiverade PostgreSQL-Server grupp

Det du får göra är att lämna:
- återställa Server parametrarna
- Återställ säkerhets kontexterna: återskapa användare, roller och Återställ behörigheter...

Om du vill göra denna säkerhets kopiering/återställning kan du använda valfritt verktyg som kan säkerhetskopiera/återställa till postgres. Exempel:
- Azure Data Studio och dess postgres-tillägg
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

## <a name="example"></a>Exempel
Vi illustrerar de här stegen med `pgAdmin` verktyget.
Överväg följande installation:
- **Källicensservern**  
    En postgres-server som körs lokalt på en Bare Metal-Server och kallas JEANYDSRV. Det är av version 12 och är värd för en databas med namnet MyOnPremPostgresDB som har en tabell T1 som har 1 rad :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="migrerings källa":::

- **Mål**  
    En postgres-server som körs i en Azure-båg miljö och med namnet postgres01. Det är av version 12. Den har ingen databas förutom standard postgres-databasen.  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrera-mål":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>Gör en säkerhets kopia av käll databasen lokalt

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrera-källa-säkerhets kopiering":::

Konfigurera den:
1. Ge den ett fil namn: **MySourceBackup**
2. Ange formatet till **anpassad** 
 :::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="migrering-källa-säkerhetskopiera – konfigurera":::

Säkerhets kopieringen har slutförts:  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrera-källa-säkerhets kopiering slutförd":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Skapa en tom databas på mål systemet i din Azure Arc-aktiverad PostgreSQL-Server grupp

> [!NOTE]
> Om du vill registrera en postgres-instans i `pgAdmin` verktyget måste du använda den offentliga IP-adressen för din instans i ditt Kubernetes-kluster och ange port och säkerhets kontext på lämpligt sätt. Du hittar den här informationen på `psql` slut punkts raden när du har kört följande kommando:

```console
azdata arc postgres endpoint list -n postgres01
```
Returnerar utdata som:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Nu ska vi namnge mål databasen **RESTORED_MyOnPremPostgresDB**.

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrera-mål-DB-skapa" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>Återställa databasen i din ARC-konfiguration

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-DB-Återställ":::

Konfigurera återställningen:
1. Peka på filen som innehåller säkerhets kopian som ska återställas: **MySourceBackup**
2. Behåll formatet inställt på **anpassad eller tar** med 
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="migrera-DB-Restore-Configure":::

3. Klicka på **Återställ**.  

   Återställningen har slutförts.  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrera-DB-Återställning-slutförd":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Kontrol lera att databasen har återställts i Azure Arc-aktiverade PostgreSQL för storskalig Server grupp

Använd någon av följande metoder:

**Från `pgAdmin` :**  

Expandera postgres-instansen som finns i konfigurationen av Azure-bågen. Tabellen i databasen som du har återställt visas och när du väljer data som visas på samma rad som den har i den lokala instansen:

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrera-DB-Restore-Verification":::

**Inifrån `psql` Azure Arc-installationen:**  

I din ARC-installation kan du använda `psql` för att ansluta till din postgres-instans, ange databasens kontext till `RESTORED_MyOnPremPostgresDB` och fråga data:

1. Visa en lista över slut punkter som hjälper dig från `psql` anslutnings strängen:

   ```console
   azdata arc postgres endpoint list -n postgres01
   [
     {
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. `psql`Använd `-d` parametern för att ange databas namnet i anslutnings strängen. Med kommandot nedan uppmanas du att ange lösen ordet:

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` anslutningen.

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. Välj tabellen så ser du de data som du har återställt från den lokala postgres-instansen:

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - Du kommer inte att se så mycket prestanda för delar som körs på Azure Arc-aktiverade PostgreSQL-skalning tills du skalar ut och du Shard/distribuerar data över arbetsnoderna i din PostgreSQL-Server grupp för hög skalning. Se [Nästa steg](#next-steps).
>
> - Det går inte att "registrera sig i Azure Arc" som en befintlig postgres-instans som körs lokalt eller i andra moln. Med andra ord går det inte att installera en viss typ av "Azure Arc-agent" på din befintliga postgres-instans för att göra den till en postgres-installation som aktive ras av Azure-bågen. I stället måste du skapa en ny postgres-instans och överföra data till den. Du kan använda den teknik som visas ovan för att göra detta, eller så kan du använda valfritt ETL-verktyg som du själv väljer.

## <a name="next-steps"></a>Nästa steg

- Läs begrepp och instruktions guider för Azure Database for PostgreSQL storskalig för att distribuera dina data över flera PostgreSQL storskaliga noder och för att dra nytta av all kraften i Azure Database for PostgreSQL storskalig:
    * [Noder och tabeller](../../postgresql/concepts-hyperscale-nodes.md)
    * [Fastställa programtyp](../../postgresql/concepts-hyperscale-app-type.md)
    * [Välja en distributionskolumn](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Tabellsamlokalisering](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuera och ändra tabeller](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Utforma en databas för flera innehavare](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Utforma en instrument panel för real tids analys](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> * I de här dokumenten hoppar du över avsnitten logga in på **Azure Portal** och **skapar en Azure Database för postgres-storskalig (citus)**. Implementera de återstående stegen i din Azure Arc-distribution. Dessa avsnitt är speciella för Azure Database for PostgreSQL disscale (citus) som erbjuds som en PaaS-tjänst i Azure-molnet, men andra delar av dokumenten är direkt tillämpliga på den Azure Arc-aktiverade PostgreSQL-skalan.

- [Skala ut din Azure Database for PostgreSQL Hyperscale-servergrupp](scale-out-postgresql-hyperscale-server-group.md)
