---
title: Felsök vanliga anslutningsproblem till Azure SQL Database
description: Innehåller steg för att felsöka Azure SQL Database anslutnings problem och lösa andra Azure SQL Database-eller Azure SQL-hanterade instans specifika problem
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.topic: troubleshooting
ms.custom: seo-lt-2019, OKR 11/2019, sqldbrb=1
author: ramakoni1
ms.author: ramakoni
ms.reviewer: sstein,vanto
ms.date: 01/14/2021
ms.openlocfilehash: 7c797c7e002f40a28e4be674c125c6ea5d60a13f
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98219070"
---
# <a name="troubleshooting-connectivity-issues-and-other-errors-with-azure-sql-database-and-azure-sql-managed-instance"></a>Fel sökning av anslutnings problem och andra fel med Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Du får fel meddelanden när det inte går att ansluta till Azure SQL Database eller Azure SQL-hanterad instans. Dessa anslutnings problem kan orsakas av omkonfiguration, brand Väggs inställningar, tids gräns för anslutning, felaktig inloggnings information eller fel vid användning av metod tips och design rikt linjer under [programmets design](develop-overview.md) process. Om max gränsen på vissa Azure SQL Database-eller SQL-hanterade instans resurser nås, kan du inte längre ansluta.

## <a name="transient-fault-error-messages-40197-40613-and-others"></a>Tillfälliga fel fel meddelanden (40197, 40613 och andra)

Azure-infrastrukturen har kapacitet att dynamiskt omkonfigurera servrar vid ökad arbetsbelastning i SQL Database-tjänsten.  Detta dynamiska beteende kan leda till att klient programmet förlorar anslutningen till databasen eller instansen. Den här typen av fel tillstånd kallas för ett *tillfälligt fel*. Händelser vid databas konfiguration inträffar på grund av en planerad händelse (till exempel en program uppgradering) eller en oplanerad händelse (till exempel en process krasch eller belastnings utjämning). De flesta omkonfigurations händelser är vanligt vis korta och bör slutföras på mindre än 60 sekunder. Dessa händelser kan dock ibland ta längre tid att slutföra, till exempel när en stor transaktion orsakar en tids krävande återställning. I följande tabell visas olika tillfälliga fel som program kan ta emot vid anslutning till SQL Database

### <a name="list-of-transient-fault-error-codes"></a>Lista över felkoder för tillfälliga fel

| Felkod | Allvarlighetsgrad | Beskrivning |
| ---:| ---:|:--- |
| 4060 |16 |Det går inte att öppna databasen%. &#x2a;LS som begärdes av inloggningen. Inloggningen misslyckades. Mer information finns i [fel 4000 till 4999](/sql/relational-databases/errors-events/database-engine-events-and-errors#errors-4000-to-4999)|
| 40197 |17 |Tjänsten har påträffat ett fel när din begäran bearbetades. Försök igen. Felkod% d.<br/><br/>Du får det här felet när tjänsten är avstängd på grund av program varu-eller maskin varu uppgraderingar, maskin varu fel eller andra problem med redundansväxling. Felkoden (% d) i meddelandet om fel 40197 ger ytterligare information om vilken typ av fel eller redundans som har inträffat. Några exempel på fel koderna är inbäddade i meddelandet om fel 40197 är 40020, 40143, 40166 och 40540.<br/><br/>Om du återansluter automatiskt ansluts du till en felfri kopia av databasen. Ditt program måste fånga fel 40197, logga den inbäddade felkoden (% d) i meddelandet för fel sökning och försöka ansluta till SQL Database tills resurserna är tillgängliga och anslutningen upprättas igen. Mer information finns i [tillfälliga fel](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 40501 |20 |Tjänsten är upptagen för närvarande. Gör om begäran efter 10 sekunder. Incident-ID:% ls. Kod:% d. Mer information finns i: <br/>&bull;&nbsp; [Logiska begränsningar för SQL Server-resurser](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md).|
| 40613 |17 |Databasen%. &#x2a;ls på servern%. &#x2a;LS är inte tillgänglig för tillfället. Försök att ansluta igen senare. Om problemet kvarstår kan du kontakta kund support och tillhandahålla sessions-ID: t%. &#x2a;ls.<br/><br/> Det här felet kan inträffa om det redan finns en befintlig DAC-anslutning (Dedicated Administrator Connection) som är etablerad i databasen. Mer information finns i [tillfälliga fel](troubleshoot-common-connectivity-issues.md#transient-errors-transient-faults).|
| 49918 |16 |Det går inte att behandla begäran. Det finns inte tillräckligt med resurser för att bearbeta begäran.<br/><br/>Tjänsten är upptagen för närvarande. Försök att utföra begäran senare. Mer information finns i: <br/>&bull;&nbsp; [Logiska begränsningar för SQL Server-resurser](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md). |
| 49919 |16 |Det går inte att bearbeta Create eller Update-begäran. För många skapande-eller uppdaterings åtgärder pågår för prenumerationen% LD.<br/><br/>Tjänsten är upptagen med att bearbeta flera skapande-eller uppdaterings begär Anden för din prenumeration eller server. Förfrågningar är för närvarande blockerade för resurs optimering. Fråga [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) för väntande åtgärder. Vänta tills väntande skapande-eller uppdaterings begär Anden är slutförda eller ta bort en väntande begäran och försök sedan igen senare. Mer information finns i: <br/>&bull;&nbsp; [Logiska begränsningar för SQL Server-resurser](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md). |
| 49920 |16 |Det går inte att behandla begäran. För många åtgärder pågår för prenumerationen% LD.<br/><br/>Tjänsten är upptagen med att bearbeta flera begär Anden för den här prenumerationen. Förfrågningar är för närvarande blockerade för resurs optimering. Fråga [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) för åtgärds status. Vänta tills väntande begär Anden är slutförda eller ta bort en väntande begäran och försök igen senare. Mer information finns i: <br/>&bull;&nbsp; [Logiska begränsningar för SQL Server-resurser](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md). |
| 4221 |16 |Inloggningen till Read-Secondary misslyckades på grund av en lång väntan på HADR_DATABASE_WAIT_FOR_TRANSITION_TO_VERSIONING. Repliken är inte tillgänglig för inloggning eftersom rad versioner saknas för transaktioner som var i flygning när replikeringen återvanns. Problemet kan lösas genom att återställa eller bekräfta de aktiva transaktionerna på den primära repliken. Förekomster av det här tillståndet kan minimeras genom att undvika långa Skriv transaktioner på den primära. |

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Steg för att lösa problem med tillfälliga anslutningar

1. Kontrol lera [instrument panelen för Microsoft Azures tjänsten](https://azure.microsoft.com/status) för eventuella kända avbrott som inträffat under den tid då felen rapporterades av programmet.
2. Program som ansluter till en moln tjänst som Azure SQL Database bör förvänta sig regelbunden omkonfiguration av händelser och implementera omprövnings logik för att hantera dessa fel i stället för att visa dem som program fel till användare.
3. När en databas närmar sig resurs gränserna kan det verka som ett tillfälligt anslutnings problem. Se [resurs begränsningar](resource-limits-logical-server.md#what-happens-when-database-resource-limits-are-reached).
4. Om problem med anslutningen fortsätter eller om varaktigheten för programmet stöter på felet överskrider 60 sekunder eller om du ser flera förekomster av felet under en dag, kan du skicka en support förfrågan till Azure genom att välja **få support** på support webbplatsen för [Azure](https://azure.microsoft.com/support/options) .

#### <a name="implementing-retry-logic"></a>Implementera logik för omprövning

Vi rekommenderar starkt att ditt klient program har omprövnings logik så att det kan återupprätta en anslutning när du har angett den tillfälliga fel tiden för att korrigera sig själv.  Vi rekommenderar att du väntar i 5 sekunder innan ditt första försök. Försöker igen efter en fördröjning som är kortare än 5 sekunders risker med moln tjänsten. För varje efterföljande försök bör fördröjningen växa exponentiellt, upp till högst 60 sekunder.

Kod exempel på logik för omprövning finns i:

- [Ansluta elastiskt till SQL med ADO.NET](/sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net)
- [Ansluta elastiskt till SQL med PHP](/sql/connect/php/step-4-connect-resiliently-to-sql-with-php)

För ytterligare information om hur du hanterar tillfälliga fel i program granskning [Felsöka tillfälliga anslutnings fel till SQL Database](troubleshoot-common-connectivity-issues.md)

En diskussion om *spärrnings perioden* för klienter som använder ADO.NET finns i [ADO.net (connection pooling)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

## <a name="a-network-related-or-instance-specific-error-occurred-while-establishing-a-connection-to-your-server"></a>Ett nätverksrelaterade eller instans fel inträffade när en anslutning till servern upprättades

Problemet uppstår om programmet inte kan ansluta till servern.

Lös problemet genom att prova stegen (i den ordning som anges) i avsnittet [steg för att åtgärda vanliga anslutnings problem](#steps-to-fix-common-connection-issues) .

## <a name="the-serverinstance-was-not-found-or-was-not-accessible-errors-26-40-10053"></a>Det gick inte att hitta servern/instansen eller så var den inte tillgänglig (fel 26, 40, 10053)

### <a name="error-26-error-locating-server-specified"></a>Fel 26: det gick inte att hitta den angivna servern

``System.Data.SqlClient.SqlException: A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections.(provider: SQL Network Interfaces, error: 26 – Error Locating Server/Instance Specified)``

#### <a name="error-40-could-not-open-a-connection-to-the-server"></a>Fel 40: det gick inte att öppna en anslutning till servern

``A network-related or instance-specific error occurred while establishing a connection to SQL Server. The server was not found or was not accessible. Verify that the instance name is correct and that SQL Server is configured to allow remote connections. (provider: Named Pipes Provider, error: 40 - Could not open a connection to SQL Server)``

#### <a name="error-10053-a-transport-level-error-has-occurred-when-receiving-results-from-the-server"></a>Fel 10053: det uppstod ett fel på transport nivå när resultat togs emot från servern

``10053: A transport-level error has occurred when receiving results from the server. (Provider: TCP Provider, error: 0 - An established connection was aborted by the software in your host machine)``

De här problemen uppstår om programmet inte kan ansluta till servern.

Lös problemen genom att prova stegen (i den ordning som anges) i avsnittet [steg för att åtgärda vanliga anslutnings problem](#steps-to-fix-common-connection-issues) .

## <a name="cannot-connect-to-server-due-to-firewall-issues"></a>Det går inte att ansluta till servern på grund av brand Väggs problem

### <a name="error-40615-cannot-connect-to--servername-"></a>Fel 40615: det går inte att ansluta till < servername >

Lös problemet genom att [Konfigurera brand Väggs inställningar på SQL Database via Azure Portal](firewall-configure.md).

### <a name="error-5-cannot-connect-to--servername-"></a>Fel 5: det går inte att ansluta till < servername >

Lös problemet genom att kontrol lera att port 1433 är öppen för utgående anslutningar på alla brand väggar mellan klienten och Internet.

## <a name="unable-to-log-in-to-the-server-errors-18456-40531"></a>Det går inte att logga in på servern (fel 18456, 40531)

### <a name="login-failed-for-user--user-name-"></a>Inloggningen misslyckades för användaren < användar namn >

``Login failed for user '<User name>'.This session has been assigned a tracing ID of '<Tracing ID>'. Provide this tracing ID to customer support when you need assistance. (Microsoft SQL Server, Error: 18456)``

Kontakta tjänst administratören för att få ett giltigt användar namn och lösen ord för att lösa problemet.

Tjänst administratören kan normalt använda följande steg för att lägga till inloggnings uppgifterna:

1. Logga in på servern med SQL Server Management Studio (SSMS).
2. Kör följande SQL-fråga för att kontrol lera om inloggnings namnet är inaktiverat:

   ```sql
   SELECT name, is_disabled FROM sys.sql_logins
   ```

3. Om motsvarande namn har inaktiverats kan du aktivera det med hjälp av följande beskrivning:

   ```sql
   Alter login <User name> enable
   ```

4. Om SQL-inloggningens användar namn inte finns skapar du det genom att följa dessa steg:

   1. I SSMS dubbelklickar du på **säkerhet** för att expandera den.
   2. Högerklicka på **inloggningar** och välj sedan **ny inloggning**.
   3. I det genererade skriptet med plats hållare redigerar du och kör följande SQL-fr åga:

   ```sql
   CREATE LOGIN <SQL_login_name, sysname, login_name>
   WITH PASSWORD = '<password, sysname, Change_Password>'
   GO
   ```

5. Dubbelklicka på **databas**.
6. Välj den databas som du vill ge användaren behörighet till.
7. Dubbelklicka på **säkerhet**.
8. Högerklicka på **användare** och välj sedan **ny användare**.
9. I det genererade skriptet med plats hållare redigerar du och kör följande SQL-fr åga:

   ```sql
   CREATE USER <user_name, sysname, user_name>
   FOR LOGIN <login_name, sysname, login_name>
   WITH DEFAULT_SCHEMA = <default_schema, sysname, dbo>
   GO
   -- Add user to the database owner role

   EXEC sp_addrolemember N'db_owner', N'<user_name, sysname, user_name>'
   GO
   ```

   > [!NOTE]
   > Du kan också använda `sp_addrolemember` för att mappa vissa användare till vissa databas roller.

Mer information finns i [Hantera databaser och inloggningar i Azure SQL Database](./logins-create-manage.md).

## <a name="connection-timeout-expired-errors"></a>Fel vid tids gräns för anslutning

### <a name="systemdatasqlclientsqlexception-0x80131904-connection-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): anslutningens tids gräns har överskridits

``System.Data.SqlClient.SqlException (0x80131904): Connection Timeout Expired. The timeout period elapsed while attempting to consume the pre-login handshake acknowledgement. This could be because the pre-login handshake failed or the server was unable to respond back in time. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=3; handshake=29995;``

### <a name="systemdatasqlclientsqlexception-0x80131904-timeout-expired"></a>System. data. SqlClient. SqlException (0x80131904): tids gränsen har gått ut

``System.Data.SqlClient.SqlException (0x80131904): Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding.``

### <a name="systemdataentitycoreentityexception-the-underlying-provider-failed-on-open"></a>System. data. Entity. Core. EntityException: den underliggande providern misslyckades vid öppning

``System.Data.Entity.Core.EntityException: The underlying provider failed on Open. -> System.Data.SqlClient.SqlException: Timeout expired. The timeout period elapsed prior to completion of the operation or the server is not responding. -> System.ComponentModel.Win32Exception: The wait operation timed out``

### <a name="cannot-connect-to--server-name-"></a>Det går inte att ansluta till < Server namn >

``Cannot connect to <server name>.ADDITIONAL INFORMATION:Connection Timeout Expired. The timeout period elapsed during the post-login phase. The connection could have timed out while waiting for server to complete the login process and respond; Or it could have timed out while attempting to create multiple active connections. The duration spent while attempting to connect to this server was - [Pre-Login] initialization=231; handshake=983; [Login] initialization=0; authentication=0; [Post-Login] complete=13000; (Microsoft SQL Server, Error: -2) For help, click: http://go.microsoft.com/fwlink?ProdName=Microsoft%20SQL%20Server&EvtSrc=MSSQLServer&EvtID=-2&LinkId=20476 The wait operation timed out``

Dessa undantag kan inträffa antingen på grund av problem med anslutningen eller frågan. För att bekräfta att det här felet orsakas av anslutnings problem, se [bekräfta om ett fel orsakas av ett anslutnings problem](#confirm-whether-an-error-is-caused-by-a-connectivity-issue).

Timeout för anslutningar inträffar eftersom programmet inte kan ansluta till servern. Lös problemet genom att prova stegen (i den ordning som anges) i avsnittet [steg för att åtgärda vanliga anslutnings problem](#steps-to-fix-common-connection-issues) .

## <a name="resource-governance-errors"></a>Resurs styrnings fel

### <a name="error-10928-resource-id-d"></a>Fel 10928: resurs-ID:% d

``10928: Resource ID: %d. The %s limit for the database is %d and has been reached. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. The Resource ID value in error message indicates the resource for which limit has been reached. For sessions, Resource ID = 2.``

Undvik det här problemet genom att prova någon av följande metoder:

- Kontrol lera om det finns tids krävande frågor.

  > [!NOTE]
  > Det här är en minimalist metod som kanske inte löser problemet. Detaljerad information om hur du felsöker blockerade frågor finns i [förstå och lösa problem med att blockera Azure SQL](understand-resolve-blocking.md).

1. Kör följande SQL-fråga för att kontrol lera [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) vyn för att se eventuella spärrnings begär Anden:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Ta reda på **indatabufferten** för huvud blocket.
3. Finjustera frågan om huvud Blocker.

   En djupgående fel söknings procedur finns i finns [min fråga som körs i molnet?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud). 

Om databasen ständigt når gränsen trots att blockera och långvariga frågor, bör du överväga att uppgradera till en utgåva med fler resurs [versioner](https://azure.microsoft.com/pricing/details/sql-database/).

Mer information om databas gränser finns i  [SQL Database resurs gränser för servrar](./resource-limits-logical-server.md).

### <a name="error-10929-resource-id-1"></a>Fel 10929: resurs-ID: 1

``10929: Resource ID: 1. The %s minimum guarantee is %d, maximum limit is %d and the current usage for the database is %d. However, the server is currently too busy to support requests greater than %d for this database. See http://go.microsoft.com/fwlink/?LinkId=267637 for assistance. Otherwise, please try again later.``

### <a name="error-40501-the-service-is-currently-busy"></a>Fel 40501: tjänsten är upptagen för tillfället

``40501: The service is currently busy. Retry the request after 10 seconds. Incident ID: %ls. Code: %d.``

Det här är ett fel i motorns begränsning, en indikation på att resurs gränserna överskrids.

Mer information om resurs begränsningar finns i [begränsningar för logiska SQL Server-resurser](./resource-limits-logical-server.md).

### <a name="error-40544-the-database-has-reached-its-size-quota"></a>Fel 40544: databasen har nått sin storleks kvot

``40544: The database has reached its size quota. Partition or delete data, drop indexes, or consult the documentation for possible resolutions. Incident ID: <ID>. Code: <code>.``

Det här felet uppstår när databasen har nått sin storleks kvot.

Följande steg kan antingen hjälpa dig att komma runt problemet eller ge dig ytterligare alternativ:

1. Kontrol lera databasens aktuella storlek med hjälp av instrument panelen i Azure Portal.

   > [!NOTE]
   > Kör följande SQL-fråga för att identifiera vilka tabeller som utnyttjar det mesta av utrymmet och är därför potentiella kandidater för rensning:

   ```sql
   SELECT o.name,
    SUM(p.row_count) AS 'Row Count',
    SUM(p.reserved_page_count) * 8.0 / 1024 AS 'Table Size (MB)'
   FROM sys.objects o
   JOIN sys.dm_db_partition_stats p on p.object_id = o.object_id
   GROUP BY o.name
   ORDER BY [Table Size (MB)] DESC;
   ```

2. Om den aktuella storleken inte överskrider den maximala storlek som stöds för din utgåva kan du använda ALTER DATABASE för att öka inställningen MAXSIZE.
3. Om databasen redan har passerat den maximala storleken som stöds för din utgåva kan du prova ett eller flera av följande steg:

   - Utför normala databas rensnings aktiviteter. Du kan till exempel rensa oönskade data genom att använda trunkera/ta bort eller flytta data med hjälp av SQL Server Integration Services (SSIS) eller verktyget för Mass kopierings program (BCP).
   - Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen.
   - För databas skalning, se [skala resurser för enkel databas](./single-database-scale.md) och [skala elastiska pooler](./elastic-pool-scale.md).

### <a name="error-40549-session-is-terminated-because-you-have-a-long-running-transaction"></a>Fel 40549: sessionen avslutas eftersom du har en tids krävande transaktion

``40549: Session is terminated because you have a long-running transaction. Try shortening your transaction.``

Om du upprepade gånger stöter på det här felet kan du försöka lösa problemet genom att följa dessa steg:

1. Kontrol lera vyn sys.dm_exec_requests för att se alla öppna sessioner som har ett högt värde för kolumnen total_elapsed_time. Utför den här kontrollen genom att köra följande SQL-skript:

   ```sql
   SELECT * FROM sys.dm_exec_requests;
   ```

2. Fastställ indatabufferten för den tids krävande frågan.
3. Finjustera frågan.

Överväg också att gruppera dina frågor. Information om batching finns i [så här använder du batching för att förbättra SQL Database program prestanda](../performance-improve-use-batching.md).

En djupgående fel söknings procedur finns i finns [min fråga som körs i molnet?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="error-40551-the-session-has-been-terminated-because-of-excessive-tempdb-usage"></a>Fel 40551: sessionen har avslut ATS på grund av överdriven TEMPDB-användning

``40551: The session has been terminated because of excessive TEMPDB usage. Try modifying your query to reduce the temporary table space usage.``

Undvik det här problemet genom att följa dessa steg:

1. Ändra frågorna för att minska användningen av temporärt tabell utrymme.
2. Släpp tillfälliga objekt när de inte längre behövs.
3. Trunkera tabeller eller ta bort oanvända tabeller.

### <a name="error-40552-the-session-has-been-terminated-because-of-excessive-transaction-log-space-usage"></a>Fel 40552: sessionen har avslut ATS på grund av överdriven användning av transaktions logg utrymme

``40552: The session has been terminated because of excessive transaction log space usage. Try modifying fewer rows in a single transaction.``

Prova att lösa problemet med hjälp av följande metoder:

- Problemet kan uppstå på grund av åtgärder för att infoga, uppdatera eller ta bort.
Försök att minska antalet rader som körs omedelbart genom att implementera batchbearbetning eller dela upp i flera mindre transaktioner.
- Problemet kan uppstå på grund av åtgärder för att återskapa index. Undvik det här problemet genom att se till att antalet rader som påverkas i tabellen * (genomsnittlig storlek på fält som uppdateras i byte + 80) < 2 gigabyte (GB).

  > [!NOTE]
  > För att återskapa indexet ska den genomsnittliga storleken för det fält som uppdateras ersättas av den genomsnittliga index storleken.

### <a name="error-40553-the-session-has-been-terminated-because-of-excessive-memory-usage"></a>Fel 40553: sessionen har avslut ATS på grund av överdriven minnes användning

``40553 : The session has been terminated because of excessive memory usage. Try modifying your query to process fewer rows.``

Undvik det här problemet genom att försöka optimera frågan.

En djupgående fel söknings procedur finns i finns [min fråga som körs i molnet?](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

### <a name="table-of-additional-resource-governance-error-messages"></a>Tabell över ytterligare fel meddelanden för resurs styrning

| Felkod | Allvarlighetsgrad | Beskrivning |
| ---:| ---:|:--- |
| 10928 |20 |Resurs-ID:% d. % S-gränsen för databasen är% d och har nåtts. Mer information finns i [SQL Database resurs gränser för databaser med enkel och pool](resource-limits-logical-server.md).<br/><br/>Resurs-ID: t anger den resurs som har nått gränsen. Resurs-ID = 1 för arbets trådar. För sessioner är resurs-ID = 2.<br/><br/>Mer information om det här felet och hur du löser det finns i: <br/>&bull;&nbsp; [Logiska begränsningar för SQL Server-resurser](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md). |
| 10929 |20 |Resurs-ID:% d. % S minsta garanti är% d, max gränsen är% d och den aktuella användningen för databasen är% d. Servern är dock för närvarande upptagen för att stödja begär Anden som är större än% d för den här databasen. Resurs-ID: t anger den resurs som har nått gränsen. Resurs-ID = 1 för arbets trådar. För sessioner är resurs-ID = 2. Mer information finns i: <br/>&bull;&nbsp; [Logiska begränsningar för SQL Server-resurser](resource-limits-logical-server.md)<br/>&bull;&nbsp; [DTU-baserade gränser för enskilda databaser](service-tiers-dtu.md)<br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md). <br/>Annars kan du försöka igen senare. |
| 40544 |20 |Databasen har nått sin storleks kvot. Partitionera eller ta bort data, släpp index eller Läs om möjliga lösningar i dokumentationen. För databas skalning, se [skala resurser för enkel databas](single-database-scale.md) och [skala elastiska pooler](elastic-pool-scale.md).|
| 40549 |16 |Sessionen avslutas eftersom du har en tids krävande transaktion. Försök att förkorta din transaktion. Information om batching finns i [så här använder du batching för att förbättra SQL Database program prestanda](../performance-improve-use-batching.md).|
| 40550 |16 |Sessionen har avslut ATS eftersom den har fått för många lås. Försök att läsa eller ändra färre rader i en enskild transaktion. Information om batching finns i [så här använder du batching för att förbättra SQL Database program prestanda](../performance-improve-use-batching.md).|
| 40551 |16 |Sessionen har avslut ATS på grund av för hög `TEMPDB` användning. Försök att ändra frågan för att minska den temporära tabell utrymmes användningen.<br/><br/>Om du använder tillfälliga objekt sparar du utrymme i databasen genom att ta bort `TEMPDB` tillfälliga objekt efter att de inte längre behövs i sessionen. Mer information om tempdb-användning i SQL Database finns i [tempdb-databasen i SQL Database](/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
| 40552 |16 |Sessionen har avslut ATS på grund av överdriven användning av transaktions logg utrymme. Försök att ändra färre rader i en enskild transaktion. Information om batching finns i [så här använder du batching för att förbättra SQL Database program prestanda](../performance-improve-use-batching.md).<br/><br/>Om du utför Mass infogningar med `bcp.exe` verktyget eller `System.Data.SqlClient.SqlBulkCopy` -klassen kan du prova `-b batchsize` att använda `BatchSize` alternativen eller för att begränsa antalet rader som kopieras till servern i varje transaktion. Försök att använda alternativet om du återskapar ett index med `ALTER INDEX` instruktionen `REBUILD WITH ONLINE = ON` . Information om transaktions logg storlekar för vCore-inköps modellen finns i: <br/>&bull;&nbsp; [vCore-baserade gränser för enskilda databaser](resource-limits-vcore-single-databases.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md)<br/>&bull;&nbsp; [Resurs gränser för Azure SQL-hanterad instans](../managed-instance/resource-limits.md).|
| 40553 |16 |Sessionen har avslut ATS på grund av överdriven minnes användning. Försök att ändra frågan så att den bearbetar färre rader.<br/><br/>Om du minskar antalet `ORDER BY` och `GROUP BY` -åtgärder i din Transact-SQL-kod minskar frågans minnes krav. För databas skalning, se [skala resurser för enkel databas](single-database-scale.md) och [skala elastiska pooler](elastic-pool-scale.md).|

## <a name="elastic-pool-errors"></a>Elastiska pool-fel

Följande fel är relaterade till att skapa och använda elastiska pooler:

| Felkod | Allvarlighetsgrad | Beskrivning | Korrigerande åtgärd |
|:--- |:--- |:--- |:--- |
| 1132 | 17 |Den elastiska poolen har nått sin lagrings gräns. Lagrings användningen för den elastiska poolen får inte överskrida (% d) MB. Försök att skriva data till en databas när lagrings gränsen för den elastiska poolen har nåtts. Information om resurs gränser finns i: <br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md). <br/> |Överväg att öka DTU: er och/eller lägga till lagring till den elastiska poolen om det är möjligt för att öka lagrings gränsen, minska lagrings utrymmet som används av enskilda databaser i den elastiska poolen eller ta bort databaser från den elastiska poolen. För skalning av elastiska pooler, se [skala elastiska pool resurser](elastic-pool-scale.md).|
| 10929 | 16 |% S minsta garanti är% d, max gränsen är% d och den aktuella användningen för databasen är% d. Servern är dock för närvarande upptagen för att stödja begär Anden som är större än% d för den här databasen. Information om resurs gränser finns i: <br/>&bull;&nbsp; [DTU-baserade gränser för elastiska pooler](resource-limits-dtu-elastic-pools.md)<br/>&bull;&nbsp; [vCore-baserade gränser för elastiska pooler](resource-limits-vcore-elastic-pools.md). <br/> Annars kan du försöka igen senare. DTU/vCore min per databas; Max per databas för DTU/vCore. Det totala antalet samtidiga arbetare (begär Anden) över alla databaser i den elastiska poolen försökte överskrida poolens gräns. |Överväg att öka DTU: er-eller virtuella kärnor för den elastiska poolen om det är möjligt för att öka arbets gränsen, eller ta bort databaser från den elastiska poolen. |
| 40844 | 16 |Databasen% ls på servern% LS är en% LS Edition-databas i en elastisk pool och kan inte ha en kontinuerlig kopierings relation.  |Saknas |
| 40857 | 16 |Det gick inte att hitta någon elastisk pool för servern:% ls, namn på elastisk pool:% ls. Den angivna elastiska poolen finns inte på den angivna servern. | Ange ett giltigt namn på elastisk pool. |
| 40858 | 16 |Den elastiska poolen% LS finns redan på servern:% ls. Den angivna elastiska poolen finns redan på den angivna servern. | Ange ett nytt namn på elastisk pool. |
| 40859 | 16 |Den elastiska poolen stöder inte tjänst nivån% ls. Den angivna tjänst nivån stöds inte för etablering av elastisk pool. |Ange rätt utgåva eller lämna tjänst nivån tom för att använda standard tjänst nivån. |
| 40860 | 16 |Kombinationen av elastisk pool% LS och tjänst målet% LS är ogiltig. Elastisk pool och tjänst nivå kan bara anges om resurs typ har angetts som ' ElasticPool '. |Ange rätt kombination av elastisk pool och tjänst nivå. |
| 40861 | 16 |Databas versionen%. *ls får inte vara samma som tjänst nivån för elastisk pool, som är%.* ls. Databas versionen skiljer sig från tjänst nivån för elastisk pool. |Ange inte någon annan databas version än tjänst nivån för elastisk pool.  Observera att databas versionen inte behöver anges. |
| 40862 | 16 |Namn på elastisk pool måste anges om det angivna målet för tjänsten elastisk pool har angetts. Ett tjänst mål för elastisk pool kan inte unikt identifiera en elastisk pool. |Ange namnet på den elastiska poolen om du använder det elastiska pool tjänst målet. |
| 40864 | 16 |DTU: er för den elastiska poolen måste vara minst (% d) DTU: er för tjänst nivån%. * ls. Försöker ställa in DTU: er för den elastiska poolen under minimigränsen. |Försök att ange DTU: er för den elastiska poolen till minst den minsta gränsen. |
| 40865 | 16 |DTU: er för den elastiska poolen får inte överskrida (% d) DTU: er för tjänst nivån%. * ls. Försöker ställa in DTU: er för den elastiska poolen ovanför Max gränsen. |Försök att ange DTU: er för den elastiska poolen så att den inte överskrider max gränsen. |
| 40867 | 16 |Max DTU per databas måste vara minst (% d) för tjänst nivån%. * ls. Försöker ställa in Max DTU per databas under den gräns som stöds. | Överväg att använda den elastiska pool tjänst nivån som stöder önskad inställning. |
| 40868 | 16 |Max DTU per databas får inte överskrida (% d) för tjänst nivån%. * ls. Försöker ställa in Max DTU per databas utöver den gräns som stöds. | Överväg att använda den elastiska pool tjänst nivån som stöder önskad inställning. |
| 40870 | 16 |Minsta DTU per databas får inte överskrida (% d) för tjänst nivån%. * ls. Försöker ställa in min DTU-min per databas utöver den gräns som stöds. | Överväg att använda den elastiska pool tjänst nivån som stöder önskad inställning. |
| 40873 | 16 |Antalet databaser (% d) och minsta DTU per databas (% d) får inte överskrida DTU: er för den elastiska poolen (% d). Försöker ange min DTU för databaser i den elastiska poolen som överskrider DTU: er för den elastiska poolen. | Överväg att öka DTU: er för den elastiska poolen, eller minska det minsta DTU-minimivärdet per databas, eller minska antalet databaser i den elastiska poolen. |
| 40877 | 16 |Det går inte att ta bort en elastisk pool om den inte innehåller några databaser. Den elastiska poolen innehåller en eller flera databaser och kan därför inte tas bort. |Ta bort databaser från den elastiska poolen för att ta bort den. |
| 40881 | 16 |Den elastiska poolen%. * ls har nått sin gräns för antal databaser.  Gränsen för antalet databaser för den elastiska poolen får inte överskrida (% d) för en elastisk pool med (% d) DTU: er. Försöker skapa eller lägga till en databas i en elastisk pool när gränsen för antal databaser för den elastiska poolen har nåtts. | Överväg att öka DTU: er för den elastiska poolen om det är möjligt för att öka databas gränsen, eller ta bort databaser från den elastiska poolen. |
| 40889 | 16 |DTU: er eller lagrings gränsen för den elastiska poolen%. * ls kan inte minskas eftersom det inte finns tillräckligt med lagrings utrymme för databaserna. Försöker minska lagrings gränsen för den elastiska poolen under lagrings användningen. | Överväg att minska lagrings användningen för enskilda databaser i den elastiska poolen eller ta bort databaser från poolen, för att minska dess DTU: er eller lagrings gräns. |
| 40891 | 16 |Minimivärdet för DTU per databas (% d) får inte överskrida Max värdet för DTU per databas (% d). Försöker ställa in min DTU-minimivärde per databas som är högre än värdet för DTU per databas. |Se till att det lägsta DTU-antalet per databas inte överskrider max värdet per databas. |
| TBD | 16 |Lagrings storleken för en enskild databas i en elastisk pool får inte överskrida den maximala storlek som tillåts av den elastiska poolen%. * ls. Databasens Max storlek överskrider max storleken som tillåts av tjänst nivån för elastisk pool. |Ange Max storleken för databasen inom gränserna för den maximala storlek som tillåts av tjänst nivån elastisk pool. |

## <a name="cannot-open-database-master-requested-by-the-login-the-login-failed"></a>Det går inte att öppna databasen "Master" som begärdes av inloggningen. Inloggningen misslyckades

Det här problemet uppstår eftersom kontot inte har behörighet att komma åt Master-databasen. Men SQL Server Management Studio (SSMS) försöker som standard att ansluta till huvud databasen.

Följ dessa anvisningar för att lösa problemet:

1. Välj **alternativ** på inloggnings skärmen på SSMS och välj sedan **anslutnings egenskaper**.
2. I fältet **Anslut till databas** anger du användarens standard databas namn som standard inloggnings databas och väljer sedan **Anslut**.

   ![Anslutningsegenskaper](./media/troubleshoot-common-errors-issues/cannot-open-database-master.png)

## <a name="confirm-whether-an-error-is-caused-by-a-connectivity-issue"></a>Bekräfta om ett fel orsakas av ett anslutnings problem

Du kan kontrol lera om ett fel orsakas av ett anslutnings problem genom att granska stack spårningen för ramar som visar anrop för att öppna en anslutning som följande (Observera referensen till **SQLConnection** -klassen):

```
System.Data.SqlClient.SqlConnection.TryOpen(TaskCompletionSource`1 retry)
 at System.Data.SqlClient.SqlConnection.Open()
 at AzureConnectionTest.Program.Main(String[] args)
ClientConnectionId:<Client connection ID>
```

När undantaget utlöses av frågor om frågor kommer du att märka en anrops stack som liknar följande (Observera referensen till klassen **SqlCommand** ). I den här situationen kan [du finjustera dina frågor](/archive/blogs/sqlblog/is-my-query-running-fine-in-the-cloud).

```
  at System.Data.SqlClient.SqlCommand.ExecuteReader()
  at AzureConnectionTest.Program.Main(String[] args)
  ClientConnectionId:<Client ID>
```

Ytterligare anvisningar om fin justering av prestanda finns i följande resurser:

- [Så här hanterar du Azure SQL-index och-statistik](https://techcommunity.microsoft.com/t5/Azure-Database-Support-Blog/How-to-maintain-Azure-SQL-Indexes-and-Statistics/ba-p/368787)
- [Justera prestanda för manuell prestanda i Azure SQL Database](./performance-guidance.md)
- [Övervaka prestanda Azure SQL Database med hjälp av vyer för dynamisk hantering](./monitoring-with-dmvs.md)
- [Kör Frågearkivet i Azure SQL Database](/sql/relational-databases/performance/best-practice-with-the-query-store#Insight)

## <a name="steps-to-fix-common-connection-issues"></a>Steg för att åtgärda vanliga anslutningsproblem

1. Kontrol lera att TCP/IP är aktiverat som ett klient protokoll på program servern. Mer information finns i [Konfigurera klient protokoll](/sql/database-engine/configure-windows/configure-client-protocols). På program servrar där du inte har installerat SQL-verktyg kontrollerar du att TCP/IP är aktiverat genom att köra **cliconfg.exe** (SQL Server klient nätverks verktyg).
2. Kontrol lera programmets anslutnings sträng för att kontrol lera att den är korrekt konfigurerad. Kontrol lera till exempel att anslutnings strängen anger rätt port (1433) och ett fullständigt kvalificerat Server namn.
Se [Hämta anslutnings information](./connect-query-ssms.md#get-server-connection-information).
3. Försök att öka timeout-värdet för anslutning. Vi rekommenderar att du använder en tids gräns för anslutning på minst 30 sekunder.
4. Testa anslutningen mellan program servern och Azure SQL Database med hjälp av [SQL Server Management Studio (SSMS)](./connect-query-ssms.md), en UDL-fil, ping eller Telnet. Mer information finns i [fel sökning av anslutnings problem](https://support.microsoft.com/help/4009936/solving-connectivity-errors-to-sql-server) och [diagnostik för anslutnings problem](./troubleshoot-common-connectivity-issues.md#diagnostics).

   > [!NOTE]
   > Som ett fel söknings steg kan du också testa anslutningen på en annan klient dator.

5. Vi rekommenderar att du kontrollerar att logiken för omprövning är på plats. Mer information om logik för omprövning finns i [Felsöka tillfälliga fel och anslutnings fel till SQL Database](./troubleshoot-common-connectivity-issues.md).

Om de här stegen inte löser problemet kan du försöka samla in mer data och sedan kontakta supporten. Aktivera loggning om ditt program är en moln tjänst. Det här steget returnerar en tidsstämpel för UTC-tid för felet. Dessutom returnerar SQL Database spårnings-ID. [Microsofts kund support tjänster](https://azure.microsoft.com/support/options/) kan använda den här informationen.

Mer information om hur du aktiverar loggning finns i [Aktivera diagnostikloggning för appar i Azure App Service](../../app-service/troubleshoot-diagnostic-logs.md).

## <a name="next-steps"></a>Nästa steg

- [Azure SQL Database-anslutningsarkitektur](./connectivity-architecture.md)
- [Azure SQL Database och Azure Synapse Analytics Network Access Controls](./network-access-controls-overview.md)