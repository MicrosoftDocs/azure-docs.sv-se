---
title: Metod tips för App-utveckling – Azure Database for PostgreSQL
description: Lär dig mer om metod tips för att skapa en app med hjälp av Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364621"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Metod tips för att skapa ett program med Azure Database for PostgreSQL

Här följer några metod tips som hjälper dig att bygga ett moln klart program med hjälp av Azure Database for PostgreSQL. Dessa metod tips kan minska utvecklings tiden för din app.

## <a name="configuration-of-application-and-database-resources"></a>Konfiguration av program-och databas resurser

### <a name="keep-the-application-and-database-in-the-same-region"></a>Behåll programmet och databasen i samma region
Se till att alla dina beroenden finns i samma region när du distribuerar ditt program i Azure. Genom att sprida instanser över regioner eller tillgänglighets zoner skapas nätverks fördröjning, vilket kan påverka programmets övergripande prestanda.

### <a name="keep-your-postgresql-server-secure"></a>Skydda din PostgreSQL-Server
Konfigurera PostgreSQL-servern så att den är [säker](./concepts-security.md) och inte tillgänglig offentligt. Använd något av dessa alternativ för att skydda servern:
- [Brandväggsregler](./concepts-firewall-rules.md)
- [Virtuella nätverk](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

För säkerhet måste du alltid ansluta till PostgreSQL-servern via SSL och konfigurera PostgreSQL-servern och ditt program för att använda TLS 1,2. Se [så här konfigurerar du SSL/TLS](./concepts-ssl-connection-security.md).

### <a name="tune-your-server-parameters"></a>Justera dina Server parametrar
För att finjustera Server parametrar med Läs-och hög `tmp_table_size` belastning `max_heap_table_size` kan du optimera för bättre prestanda. Om du vill beräkna de värden som krävs för dessa variabler tittar du på de totala minnes värdena per anslutning och bas minnet. Summan av minnes parametrarna per anslutning, exklusive `tmp_table_size` , tillsammans med de grundläggande minnes kontona för det totala minnet på servern.

### <a name="use-environment-variables-for-connection-information"></a>Använd miljövariabler för anslutnings information
Spara inte dina autentiseringsuppgifter för databasen i program koden. Beroende på klient programs applikationen följer du vägledningen för att konfigurera miljövariabler. Information om hur du använder app service finns i så här[konfigurerar du inställningar för appar](../app-service/configure-common.md#configure-app-settings) och Azure Kuberentes-tjänsten. mer information finns i [så här använder du Kubernetes hemligheter](https://kubernetes.io/docs/concepts/configuration/secret/).

## <a name="performance-and-resiliency"></a>Prestanda och återhämtning
Här följer några verktyg och metoder som du kan använda för att felsöka prestanda problem med programmet.

### <a name="use-connection-pooling"></a>Använd anslutningspoolen
Med anslutningspoolen upprättas en fast uppsättning anslutningar vid start tiden och underhålls. Detta hjälper också till att minska fragmenteringen av cacheminnet på servern som orsakas av de dynamiska nya anslutningar som upprättats på databas servern. Anslutningspoolen kan konfigureras på program sidan om app Framework-eller databas driv rutinen stöder det. Om detta inte stöds är det andra rekommenderade alternativet att använda en proxy-tjänst för anslutningspoolen som [PgBouncer](https://pgbouncer.github.io/) eller [pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) som körs utanför programmet och ansluter till databas servern. Både PgBouncer och pgpool är community-baserade verktyg som fungerar med Azure Database for PostgreSQL.

### <a name="retry-logic-to-handle-transient-errors"></a>Försök igen med logik för att hantera tillfälliga fel
Ditt program kan uppleva tillfälliga fel där anslutningar till databasen släpps eller försvinner tillfälligt. I sådana situationer är servern igång efter en och två nya försök i 5 till 10 sekunder. En bra idé är att vänta i 5 sekunder innan ditt första försök. Följ sedan varje nytt försök genom att öka vänte tiden gradvis, upp till 60 sekunder. Begränsa det maximala antalet återförsök som programmet anser att åtgärden misslyckades, så att du kan undersöka dem ytterligare. Mer information finns i [Felsöka anslutnings fel](./concepts-connectivity.md) .

### <a name="enable-read-replication-to-mitigate-failovers"></a>Aktivera Läs replikering för att minimera redundans
Du kan använda [datareplikering](./concepts-read-replicas.md) för scenarier med växling vid fel. När du använder Läs repliker sker ingen automatisk redundans mellan käll-och replik servrar. Du ser en fördröjning mellan källan och repliken eftersom replikeringen är asynkron. Nätverks fördröjning kan påverkas av många faktorer, t. ex. storleken på arbets belastningen som körs på käll servern och fördröjningen mellan data Center. I de flesta fall är replik fördröjningen från några sekunder till några minuter.


## <a name="database-deployment"></a>Databasdistribution

### <a name="configure-cicd-deployment-pipeline"></a>Konfigurera en pipeline för CI/CD-distribution
Ibland måste du distribuera ändringar i databasen. I sådana fall kan du använda kontinuerlig integrering (CI) genom [GitHub-åtgärder](https://github.com/Azure/postgresql/blob/master/README.md) för postgresql-servern för att uppdatera databasen genom att köra ett anpassat skript mot den.

### <a name="define-manual-database-deployment-process"></a>Definiera process för manuell databas distribution
Under en manuell databas distribution följer du dessa steg för att minimera stillestånds tiden eller minska risken för misslyckad distribution:

- Skapa en kopia av en produktions databas i en ny databas med hjälp av pg_dump.
- Uppdatera den nya databasen med dina nya schema ändringar eller uppdateringar som krävs för din databas.
- Flytta produktions databasen i skrivskyddat läge. Du bör inte ha Skriv åtgärder i produktions databasen förrän distributionen är klar.
- Testa ditt program med den nyligen uppdaterade databasen från steg 1.
- Distribuera dina program ändringar och se till att programmet nu använder den nya databasen som har de senaste uppdateringarna.
- Behåll den gamla produktions databasen så att du kan återställa ändringarna. Du kan sedan utvärdera för att antingen ta bort den gamla produktions databasen eller exportera den Azure Storage vid behov.

>  [!NOTE]
> Om programmet är som en app för e-handel och du inte kan försätta det i skrivskyddat läge, distribuerar du ändringarna direkt i produktions databasen när du har gjort en säkerhets kopia. De här-ändring bör ske under låg belastnings tider med låg trafik till appen för att minimera påverkan, eftersom vissa användare kan uppleva misslyckade förfrågningar. Kontrol lera att program koden även hanterar misslyckade begär Anden.

## <a name="database-schema-and-queries"></a>Databasschemat och-frågor
Här följer några tips att tänka på när du skapar ditt databas schema och dina frågor.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>Använda BIGINT eller UUID för primära nycklar
När du skapar anpassade program eller vissa ramverk som de kanske använder `INT` i stället för `BIGINT` för primära nycklar. När du använder kan ```INT``` du köra risken för att värdet i databasen får överskrida lagrings kapaciteten av ```INT``` data typen. Att göra den här ändringen i ett befintligt produktions program kan ta lång tid med kostnad mer utvecklings tid. Ett annat alternativ är att använda [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) för primära nycklar. Den här identifieraren använder en automatiskt genererad 128-bitars sträng, till exempel ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . Läs mer om [data typer i postgresql](https://www.postgresql.org/docs/8.1/datatype.html).

### <a name="use-indexes"></a>Använda index

Det finns många typer av [index](https://www.postgresql.org/docs/9.1/indexes.html) i postgres som kan användas på olika sätt. Genom att använda ett index kan servern hitta och hämta vissa rader mycket snabbare än det kan göra utan ett index. Men indexen lägger också till överbelastning till databas servern. Därför bör du undvika att ha för många index.

### <a name="use-autovacuum"></a>Använd autovakuum
Du kan optimera servern med autovakuum på en Azure Database for PostgreSQL-Server. PostgreSQL tillåter större databas samtidighet men med varje uppdaterings resultat i INSERT och Delete. För att ta bort, är posterna mjuka markerade och rensas senare. PostgreSQL kör ett vakuum-jobb för att utföra dessa uppgifter. Om du inte behöver vakuum från tid till tid kan de döda tupler som ackumuleras leda till:

- Data överdriven storlek, till exempel större databaser och tabeller.
- Större, optimala index.
- Ökat I/O.

Lär dig mer om [hur du optimerar med autovakuum](howto-optimize-autovacuum.md).

### <a name="use-pg_stats_statements"></a>Använd pg_stats_statements
Pg_stat_statements är ett PostgreSQL-tillägg som är aktiverat som standard i Azure Database for PostgreSQL. Tillägget är ett sätt att spåra körnings statistik för alla SQL-uttryck som körs av en server. Se [hur du använder pg_statement](howto-optimize-query-stats-collection.md).


### <a name="use-the-query-store"></a>Använda Frågearkivet
Funktionen [query Store](./concepts-query-store.md) i Azure Database for PostgreSQL ger en mer effektiv metod för att spåra statistik för frågor. Vi rekommenderar den här funktionen som ett alternativ till att använda pg_stats_statements.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>Optimera Mass infogningar och Använd tillfälliga data
Om du har arbets belastnings åtgärder som involverar tillfälliga data eller lägger till stora data mängder i bulk bör du överväga att använda ej loggade tabeller. Den ger atomiska och tålighet som standard. Atomicitet, konsekvens, isolering och hållbarhet utgör egenskaperna för syror. Se [optimera Mass infogningar](howto-optimize-bulk-inserts.md).

## <a name="next-steps"></a>Nästa steg
[Postgres-guide](http://postgresguide.com/)
