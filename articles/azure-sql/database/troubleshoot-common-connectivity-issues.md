---
title: Arbeta med tillfälliga fel
description: Lär dig att felsöka, diagnostisera och förhindra ett fel i SQL-anslutning eller tillfälligt fel vid anslutning till Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics.
keywords: SQL-anslutning, anslutnings sträng, anslutnings problem, tillfälligt fel, anslutnings fel
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: troubleshooting
author: dalechen
ms.author: ninarn
ms.reviewer: sstein, vanto
ms.date: 01/14/2020
ms.openlocfilehash: 9f2e755047910aefa89c2f187cda956aca608b98
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093765"
---
# <a name="troubleshoot-transient-connection-errors-in-sql-database-and-sql-managed-instance"></a>Felsök tillfälliga anslutnings fel i SQL Database och SQL-hanterad instans

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Den här artikeln beskriver hur du kan förhindra, felsöka, diagnostisera och minimera anslutnings fel och tillfälliga fel som klient programmet stöter på när det interagerar med Azure SQL Database, Azure SQL-hanterad instans och Azure Synapse Analytics. Lär dig hur du konfigurerar omprövnings logik, skapar anslutnings strängen och justerar andra anslutnings inställningar.

<a id="i-transient-faults" name="i-transient-faults"></a>

## <a name="transient-errors-transient-faults"></a>Tillfälliga fel (tillfälliga fel)

Ett tillfälligt fel, som även kallas ett tillfälligt fel, har en underliggande orsak som snart löser sig själv. En tillfällig orsak till tillfälliga fel är när Azure-systemet snabbt byter maskin varu resurser för att få bättre belastnings utjämning för olika arbets belastningar. De flesta av dessa omkonfigurations händelser slutförs på mindre än 60 sekunder. Under den här tids perioden för omkonfiguration kan du ha problem med att ansluta till databasen i SQL Database. Program som ansluter till din databas bör byggas för att förvänta sig dessa tillfälliga fel. Hantera dem genom att implementera logik för omprövning i koden i stället för att visa dem till användare som program fel.

Om ditt klient program använder ADO.NET, meddelas ditt program om det tillfälliga felet genom Throw of **SqlException**.

<a id="connection-versus-command" name="connection-versus-command"></a>

### <a name="connection-vs-command"></a>Anslutning vs.-kommando

Försök att ansluta till SQL Database-och SQL-hanterad instans eller upprätta den igen, beroende på följande:

- **Ett tillfälligt fel inträffar under en anslutning försök**

Försök ansluta igen efter en fördröjning på flera sekunder.

- **Ett tillfälligt fel inträffar under kommandot SQL Database och SQL-hanterad instans fråga**

Försök inte omedelbart att köra kommandot igen. I stället kan du efter en fördröjning upprätta anslutningen. Försök sedan utföra kommandot på nytt.

<a id="j-retry-logic-transient-faults" name="j-retry-logic-transient-faults"></a>

## <a name="retry-logic-for-transient-errors"></a>Logik för omprövning av tillfälliga fel

Klient program som ibland stöter på ett tillfälligt fel är mer robusta när de innehåller logik för återförsök. När ditt program kommunicerar med databasen i SQL Database via mellanprodukter från tredje part, frågar du leverantören om mellanprodukter innehåller omprövnings logik för tillfälliga fel.

<a id="principles-for-retry" name="principles-for-retry"></a>

### <a name="principles-for-retry"></a>Principer för återförsök

- Om felet är tillfälligt kan du försöka öppna en anslutning igen.
- Försök inte direkt med en SQL Database-eller SQL-hanterad instans- `SELECT` instruktion som misslyckades med ett tillfälligt fel. Upprätta i stället en ny anslutning och försök sedan igen `SELECT` .
- När en SQL Database-eller SQL-hanterad instans `UPDATE` -instruktionen Miss lyckas med ett tillfälligt fel, upprätta en ny anslutning innan du försöker uppdatera igen. Logiken för omprövning måste se till att antingen hela databas transaktionen har avslut ATS eller att hela transaktionen återställs.

### <a name="other-considerations-for-retry"></a>Andra överväganden för återförsök

- En kommando fil som startar automatiskt efter arbets tid och som har slutförts innan morgon får bli mycket patient med långa tidsintervall mellan försöken.
- Ett användar gränssnitts program bör ta hänsyn till den mänskliga tendensen för att få upp efter för lång väntan. Lösningen får inte försöka igen med några sekunder, eftersom principen kan belasta systemet med begär Anden.

### <a name="interval-increase-between-retries"></a>Intervall ökning mellan återförsök

Vi rekommenderar att du väntar 5 sekunder innan ditt första försök. Försöker igen efter en fördröjning som är kortare än 5 sekunders risker med moln tjänsten. För varje efterföljande försök bör fördröjningen växa exponentiellt, upp till högst 60 sekunder.

En beskrivning av spärrnings perioden för klienter som använder ADO.NET finns i anslutningspoolen [(ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling).

Du kanske också vill ange ett maximalt antal försök innan programmet självavslutas.

### <a name="code-samples-with-retry-logic"></a>Kod exempel med logik för omprövning

Kod exempel med logik för omprövning finns på:

- [Anslut elastiskt till Azure SQL med ADO.NET][step-4-connect-resiliently-to-sql-with-ado-net-a78n]
- [Ansluta elastiskt till Azure SQL med PHP][step-4-connect-resiliently-to-sql-with-php-p42h]

<a id="k-test-retry-logic" name="k-test-retry-logic"></a>

### <a name="test-your-retry-logic"></a>Testa logiken för omprövning

Om du vill testa logiken för omprövning måste du simulera eller orsaka ett fel som kan åtgärdas medan programmet fortfarande körs.

#### <a name="test-by-disconnecting-from-the-network"></a>Testa genom att koppla från nätverket

Ett sätt som du kan testa din logik för omprövning är att koppla bort klient datorn från nätverket medan programmet körs. Felet är:

- **SqlException. Number** = 11001
- Meddelande: "ingen sådan värd är känd"

Som en del av det första försöket att ansluta igen kan du återansluta klient datorn till nätverket och sedan försöka ansluta.

För att göra det här testet praktiskt kan du koppla bort datorn från nätverket innan du startar programmet. Sedan identifierar programmet en körnings parameter som gör att programmet:

- Lägg tillfälligt till 11001 i listan över fel som ska beaktas som tillfälligt.
- Försök att ansluta till den första anslutningen som vanligt.
- Ta bort 11001 från listan när felet har påträffats.
- Visa ett meddelande som uppmanar användaren att ansluta datorn till nätverket.
- Pausa ytterligare körningen genom att använda antingen- **konsolen. readline** -metoden eller en dialog ruta med en OK-knapp. Användaren trycker på RETUR när datorn är ansluten till nätverket.
- Försök igen för att ansluta, förväntat resultat.

#### <a name="test-by-misspelling-the-user-name-when-connecting"></a>Testa genom att stava fel i användar namnet vid anslutning

Ditt program kan stava fel på användar namnet före det första anslutnings försöket. Felet är:

- **SqlException. Number** = 18456
- Meddelande: "inloggningen misslyckades för användaren" WRONG_MyUserName "."

Som en del av det första försöket med nya försök kan programmet korrigera fel stavningen och sedan försöka ansluta.

För att göra det här testet användbart identifierar programmet en körnings parameter som gör att programmet:

- Lägg tillfälligt till 18456 i listan över fel som ska beaktas som tillfälligt.
- Lägg till WRONG_ till användar namnet i syfte.
- Ta bort 18456 från listan när felet har påträffats.
- Ta bort WRONG_ från användar namnet.
- Försök igen för att ansluta, förväntat resultat.

<a id="net-sqlconnection-parameters-for-connection-retry" name="net-sqlconnection-parameters-for-connection-retry"></a>

## <a name="net-sqlconnection-parameters-for-connection-retry"></a>.NET SqlConnection-parametrar för anslutnings försök

Om klient programmet ansluter till databasen i SQL Database med hjälp av .NET Framework Class **system. data. SqlClient. SQLConnection**, använder du .NET 4.6.1 eller senare (eller .net Core) så att du kan använda funktionen för att ansluta igen. Mer information om funktionen finns i [egenskapen SQLConnection. ConnectionString](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring?view=netframework-4.8&preserve-view=true).

<!--
2015-11-30, FwLink 393996 points to dn632678.aspx, which links to a downloadable .docx related to SqlClient and SQL Server 2014.
-->

När du skapar [anslutnings strängen](/dotnet/api/system.data.sqlclient.sqlconnection.connectionstring) för **SQLConnection** -objektet koordinerar du värdena bland följande parametrar:

- **ConnectRetryCount**: &nbsp; &nbsp; standardvärdet är 1. Intervallet är 0 till 255.
- **ConnectRetryInterval**: &nbsp; &nbsp; standardvärdet är 10 sekunder. Intervallet är 1 till 60.
- **Tids gräns för anslutning**: &nbsp; &nbsp; standardvärdet är 15 sekunder. Intervallet är 0 till 2147483647.

Mer specifikt bör dina valda värden göra följande likhets värde: anslutningstimeout = ConnectRetryCount * ConnectionRetryInterval

Om antalet till exempel är lika med 3 och intervallet är lika med 10 sekunder, ger en tids gräns på bara 29 sekunder inte systemet tillräckligt med tid för det tredje och sista försöket att ansluta: 29 < 3 * 10.

<a id="connection-versus-command" name="connection-versus-command"></a>

## <a name="connection-vs-command"></a>Anslutning vs.-kommando

**ConnectRetryCount** -och **ConnectRetryInterval** -parametrarna gör att **SQLConnection** -objektet kan försöka ansluta igen utan att tala eller bry dig om programmet, till exempel att returnera kontroll till programmet. Återförsök kan ske i följande situationer:

- SqlConnection. Open-metod anrop
- Anrop för SqlConnection.Exesöta-metoden

Det finns en subtlety. Om ett tillfälligt fel inträffar när din *fråga* körs, kommer **SQLConnection** -objektet inte att försöka ansluta igen. Det gör inget nytt försök att köra frågan igen. **SQLConnection** måste dock snabbt kontrol lera anslutningen innan du skickar din fråga för körning. Om snabb kontrollen identifierar ett anslutnings problem, försöker **SQLConnection** att försöka ansluta igen. Om försöket lyckas skickas frågan för körning.

### <a name="should-connectretrycount-be-combined-with-application-retry-logic"></a>Ska ConnectRetryCount kombineras med logik för omförsök för program

Anta att ditt program har robust anpassad omprövnings logik. Det kan försöka ansluta igen fyra gånger. Om du lägger till **ConnectRetryInterval** och **ConnectRetryCount** = 3 i anslutnings strängen ökar antalet försök till 4 * 3 = 12 återförsök. Du kanske inte vill ha ett stort antal återförsök.

<a id="a-connection-connection-string" name="a-connection-connection-string"></a>

## <a name="connections-to-your-database-in-sql-database"></a>Anslutningar till databasen i SQL Database

<a id="c-connection-string" name="c-connection-string"></a>

### <a name="connection-connection-string"></a>Anslutning: anslutnings sträng

Anslutnings strängen som krävs för att ansluta till databasen skiljer sig något från den sträng som används för att ansluta till SQL Server. Du kan kopiera anslutnings strängen för databasen från [Azure Portal](https://portal.azure.com/).

[!INCLUDE [sql-database-include-connection-string-20-portalshots](../../../includes/sql-database-include-connection-string-20-portalshots.md)]

<a id="b-connection-ip-address" name="b-connection-ip-address"></a>

### <a name="connection-ip-address"></a>Anslutning: IP-adress

Du måste konfigurera SQL Database för att acceptera kommunikation från IP-adressen för den dator som är värd för ditt klient program. Konfigurera den här konfigurationen genom att redigera brand Väggs inställningarna via [Azure Portal](https://portal.azure.com/).

Om du glömmer att konfigurera IP-adressen, Miss lyckas programmet med ett användbart fel meddelande som anger den nödvändiga IP-adressen.

[!INCLUDE [sql-database-include-ip-address-22-portal](../../../includes/sql-database-include-ip-address-22-v12portal.md)]

Mer information finns i [Konfigurera brand Väggs inställningar i SQL Database](firewall-configure.md).
<a id="c-connection-ports" name="c-connection-ports"></a>

### <a name="connection-ports"></a>Anslutning: portar

Normalt måste du se till att endast port 1433 är öppen för utgående kommunikation på den dator som är värd för ditt klient program.

Om ditt klient program till exempel finns på en Windows-dator kan du använda Windows-brandväggen på värden för att öppna port 1433.

1. Öppna Kontrollpanelen.
2. Markera **alla objekt på kontroll panelen**  >  **Windows-brandväggen**  >  **Avancerade inställningar**  >  **utgående regler**  >  **åtgärder**  >  **ny regel**.

Om ditt klient program finns på en virtuell Azure-dator (VM) läser du [portarna utöver 1433 för ADO.NET 4,5 och SQL Database](adonet-v12-develop-direct-route-ports.md).

Bakgrunds information om konfiguration av portar och IP-adresser i databasen finns [Azure SQL Database brand vägg](firewall-configure.md).

<a id="d-connection-ado-net-4-5" name="d-connection-ado-net-4-5"></a>

### <a name="connection-adonet-462-or-later"></a>Anslutning: ADO.NET 4.6.2 eller senare

Om programmet använder ADO.NET-klasser som **system. data. SqlClient. SQLConnection** för att ansluta till SQL Database, rekommenderar vi att du använder .NET Framework version 4.6.2 eller senare.

#### <a name="starting-with-adonet-462"></a>Från och med ADO.NET 4.6.2

- Det går inte att öppna anslutningen direkt för Azure SQL, vilket förbättrar prestandan för molnbaserade appar.

#### <a name="starting-with-adonet-461"></a>Från och med ADO.NET 4.6.1

- För SQL Database förbättras tillförlitligheten när du öppnar en anslutning med metoden **SQLConnection. Open** . Den **Öppna** metoden införlivar nu mekanismer med bästa möjliga återförsök som svar på tillfälliga fel för vissa fel inom tids gränsen för anslutningen.
- Anslutningspoolen stöds, vilket innebär en effektiv verifiering av att anslutningsobjektet som det ger ditt program fungerar.

När du använder ett anslutnings objekt från en anslutningspool, rekommenderar vi att programmet tillfälligt stänger anslutningen när den inte används direkt. Det är inte dyrt att öppna en anslutning igen, men det är att skapa en ny anslutning.

Om du använder ADO.NET 4,0 eller tidigare rekommenderar vi att du uppgraderar till den senaste ADO.NET. Från och med augusti 2018 kan du [Ladda ned ADO.NET 4.6.2](https://blogs.msdn.microsoft.com/dotnet/20../../announcing-the-net-framework-4-7-2/).

<a id="e-diagnostics-test-utilities-connect" name="e-diagnostics-test-utilities-connect"></a>

## <a name="diagnostics"></a>Diagnostik

<a id="d-test-whether-utilities-can-connect" name="d-test-whether-utilities-can-connect"></a>

### <a name="diagnostics-test-whether-utilities-can-connect"></a>Diagnostik: testa om verktyg kan ansluta

Om programmet inte kan ansluta till databasen i SQL Database, är ett diagnostiskt alternativ att försöka ansluta med ett verktygs program. Vi rekommenderar att verktyget ansluter med hjälp av samma bibliotek som programmet använder.

På en Windows-dator kan du prova följande verktyg:

- SQL Server Management Studio (ssms.exe), som ansluter med hjälp av ADO.NET
- `sqlcmd.exe`, som ansluter med hjälp av [ODBC](/sql/connect/odbc/microsoft-odbc-driver-for-sql-server)

När programmet har anslutits testar du om en kort SQL SELECT-fråga fungerar.

<a id="f-diagnostics-check-open-ports" name="f-diagnostics-check-open-ports"></a>

### <a name="diagnostics-check-the-open-ports"></a>Diagnostik: kontrol lera de öppna portarna

Om du misstänker att anslutnings försöken Miss lyckas på grund av port problem kan du köra ett verktyg på datorn som rapporterar port konfiguration.

I Linux kan följande verktyg vara användbara:

- `netstat -nap`
- `nmap -sS -O 127.0.0.1`: Ändra värdet i exemplet så att det blir din IP-adress.

I Windows kan [PortQry.exe](https://www.microsoft.com/download/details.aspx?id=17148) -verktyget vara till hjälp. Här är en exempel körning som efterfrågade port situationen på en databas i SQL Database och som kördes på en bärbar dator:

```cmd
[C:\Users\johndoe\]
>> portqry.exe -n johndoesvr9.database.windows.net -p tcp -e 1433

Querying target system called: johndoesvr9.database.windows.net

Attempting to resolve name to IP address...
Name resolved to 23.100.117.95

querying...
TCP port 1433 (ms-sql-s service): LISTENING

[C:\Users\johndoe\]
>>
```

<a id="g-diagnostics-log-your-errors" name="g-diagnostics-log-your-errors"></a>

### <a name="diagnostics-log-your-errors"></a>Diagnostik: Logga dina fel

Ett tillfälligt problem diagnostiseras ibland bäst genom identifiering av ett allmänt mönster över dagar eller veckor.

Klienten kan hjälpa till med en diagnos genom att logga alla fel som den påträffar. Du kanske kan korrelera logg posterna med Feldata som SQL Database loggar internt internt.

Enterprise Library 6 (EntLib60) erbjuder .NET-hanterade klasser som hjälp vid loggning. Mer information finns i [5-lika enkelt som att falla från en logg: Använd loggnings program blocket](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="h-diagnostics-examine-logs-errors" name="h-diagnostics-examine-logs-errors"></a>

### <a name="diagnostics-examine-system-logs-for-errors"></a>Diagnostik: kontrol lera fel i system loggarna

Här följer några Transact-SQL SELECT-uttryck som frågar efter fel loggar och annan information.

| Fråga efter logg | Description |
|:--- |:--- |
| `SELECT e.*`<br/>`FROM sys.event_log AS e`<br/>`WHERE e.database_name = 'myDbName'`<br/>`AND e.event_category = 'connectivity'`<br/>`AND 2 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, e.end_time, GetUtcDate())`<br/>`ORDER BY e.event_category,`<br/>&nbsp;&nbsp;`e.event_type, e.end_time;` |I [sys.event_logs](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database) vyn finns information om enskilda händelser som innehåller vissa som kan orsaka tillfälliga fel eller anslutnings fel.<br/><br/>Vi rekommenderar att du korrelerar **start_time** -eller **end_times** värden med information om när ditt klient program fick problem.<br/><br/>Du måste ansluta till *huvud* databasen för att köra den här frågan. |
| `SELECT c.*`<br/>`FROM sys.database_connection_stats AS c`<br/>`WHERE c.database_name = 'myDbName'`<br/>`AND 24 >= DateDiff`<br/>&nbsp;&nbsp;`(hour, c.end_time, GetUtcDate())`<br/>`ORDER BY c.end_time;` |I [sys.database_connection_statss](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database) vyn finns sammansatta antal händelse typer för ytterligare diagnostik.<br/><br/>Du måste ansluta till *huvud* databasen för att köra den här frågan. |

<a id="d-search-for-problem-events-in-the-sql-database-log" name="d-search-for-problem-events-in-the-sql-database-log"></a>

### <a name="diagnostics-search-for-problem-events-in-the-sql-database-log"></a>Diagnostik: Sök efter problem händelser i SQL Database loggen

Du kan söka efter poster om problem händelser i SQL Database loggen. Prova följande Transact-SQL SELECT-instruktion i *huvud* databasen:

```sql
SELECT
   object_name
  ,CAST(f.event_data as XML).value
      ('(/event/@timestamp)[1]', 'datetime2')                      AS [timestamp]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="error"]/value)[1]', 'int')             AS [error]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="state"]/value)[1]', 'int')             AS [state]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="is_success"]/value)[1]', 'bit')        AS [is_success]
  ,CAST(f.event_data as XML).value
      ('(/event/data[@name="database_name"]/value)[1]', 'sysname') AS [database_name]
FROM
  sys.fn_xe_telemetry_blob_target_read_file('el', null, null, null) AS f
WHERE
  object_name != 'login_event'  -- Login events are numerous.
  and
  '2015-06-21' < CAST(f.event_data as XML).value
        ('(/event/@timestamp)[1]', 'datetime2')
ORDER BY
  [timestamp] DESC
;
```

#### <a name="a-few-returned-rows-from-sysfn_xe_telemetry_blob_target_read_file"></a>Några returnerade rader från sys.fn_xe_telemetry_blob_target_read_file

I följande exempel visas hur en returnerad rad kan se ut. De null-värden som visas är ofta inte null i andra rader.

```
object_name                   timestamp                    error  state  is_success  database_name

database_xml_deadlock_report  2015-10-16 20:28:01.0090000  NULL   NULL   NULL        AdventureWorks
```

<a id="l-enterprise-library-6" name="l-enterprise-library-6"></a>

## <a name="enterprise-library-6"></a>Enterprise Library 6

Enterprise Library 6 (EntLib60) är ett ramverk med .NET-klasser som hjälper dig att implementera robusta klienter i moln tjänster, varav en är SQL Database. Information om hur du hittar ämnen som är reserverade för varje utrymme där EntLib60 kan hjälpa finns i [företags bibliotek 6 – April 2013](/previous-versions/msp-n-p/dn169621(v=pandp.10)).

Omprövnings logik för hantering av tillfälliga fel är ett utrymme där EntLib60 kan hjälpa dig. Mer information finns i [4-perseverance, hemlighet för alla Triumphs: Använd program blocket för den tillfälliga fel hanteringen](/previous-versions/msp-n-p/dn440719(v=pandp.60)).

> [!NOTE]
> Käll koden för EntLib60 är tillgänglig för offentlig nedladdning från [Download Center](https://github.com/MicrosoftArchive/enterprise-library). Microsoft har inga planer på att göra ytterligare funktions uppdateringar eller underhålls uppdateringar av EntLib.

<a id="entlib60-classes-for-transient-errors-and-retry" name="entlib60-classes-for-transient-errors-and-retry"></a>

### <a name="entlib60-classes-for-transient-errors-and-retry"></a>EntLib60-klasser för tillfälliga fel och försök igen

Följande EntLib60-klasser är särskilt användbara för omprövnings logik. Alla dessa klasser finns i eller under namn området **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling**.

I namn området **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling**:

- **RetryPolicy** -klass
  - **ExecuteAction** -Metod
- **ExponentialBackoff** -klass
- **SqlDatabaseTransientErrorDetectionStrategy** -klass
- **ReliableSqlConnection** -klass
  - **ExecuteCommand** -Metod

I namn området **Microsoft. Practices. EnterpriseLibrary. TransientFaultHandling. TestSupport**:

- **AlwaysTransientErrorDetectionStrategy** -klass
- **NeverTransientErrorDetectionStrategy** -klass

Här följer några länkar till information om EntLib60:

- Hämtning av den kostnads fria pärmen: [Developer ' s Guide to Microsoft Enterprise Library, andra utgåvan](https://www.microsoft.com/download/details.aspx?id=41145).
- Bästa praxis: den [allmänna vägledningen](/azure/architecture/best-practices/transient-faults) har en utmärkt detaljerad beskrivning av omprövnings logiken.
- NuGet nedladdning: [Enterprise Library-tillfälligt fel hantering Application Block 6,0](https://www.nuget.org/packages/EnterpriseLibrary.TransientFaultHandling/).

<a id="entlib60-the-logging-block" name="entlib60-the-logging-block"></a>

### <a name="entlib60-the-logging-block"></a>EntLib60: loggnings block

- Loggnings blocket är en mycket flexibel och konfigurerbar lösning som du kan använda för att:
  - Skapa och lagra logg meddelanden på en rad olika platser.
  - Kategorisera och filtrera meddelanden.
  - Samla in kontextuell information som är användbar för fel sökning och spårning, samt för gransknings-och allmänna loggnings krav.
- Loggnings blocket sammanfattar loggnings funktionen från logg destinationen så att program koden är konsekvent, oavsett plats och typ för mål loggnings lagret.

Mer information finns i [5-lika enkelt som att falla från en logg: Använd loggnings program blocket](/previous-versions/msp-n-p/dn440731(v=pandp.60)).

<a id="entlib60-istransient-method-source-code" name="entlib60-istransient-method-source-code"></a>

### <a name="entlib60-istransient-method-source-code"></a>Käll kod för EntLib60 IsTransient-metoden

Sedan, från **SqlDatabaseTransientErrorDetectionStrategy** -klassen, är C#-käll koden för **IsTransient** -metoden. Käll koden klargör vilka fel som ansågs tillfälligt och betrodd för återförsök, från och med april 2013.

```csharp
public bool IsTransient(Exception ex)
{
  if (ex != null)
  {
    SqlException sqlException;
    if ((sqlException = ex as SqlException) != null)
    {
      // Enumerate through all errors found in the exception.
      foreach (SqlError err in sqlException.Errors)
      {
        switch (err.Number)
        {
            // SQL Error Code: 40501
            // The service is currently busy. Retry the request after 10 seconds.
            // Code: (reason code to be decoded).
          case ThrottlingCondition.ThrottlingErrorNumber:
            // Decode the reason code from the error message to
            // determine the grounds for throttling.
            var condition = ThrottlingCondition.FromError(err);

            // Attach the decoded values as additional attributes to
            // the original SQL exception.
            sqlException.Data[condition.ThrottlingMode.GetType().Name] =
              condition.ThrottlingMode.ToString();
            sqlException.Data[condition.GetType().Name] = condition;

            return true;

          case 10928:
          case 10929:
          case 10053:
          case 10054:
          case 10060:
          case 40197:
          case 40540:
          case 40613:
          case 40143:
          case 233:
          case 64:
            // DBNETLIB Error Code: 20
            // The instance of SQL Server you attempted to connect to
            // does not support encryption.
          case (int)ProcessNetLibErrorCode.EncryptionNotSupported:
            return true;
        }
      }
    }
    else if (ex is TimeoutException)
    {
      return true;
    }
    else
    {
      EntityException entityException;
      if ((entityException = ex as EntityException) != null)
      {
        return this.IsTransient(entityException.InnerException);
      }
    }
  }

  return false;
}
```

## <a name="next-steps"></a>Nästa steg

- [Anslutnings bibliotek för SQL Database och SQL Server](connect-query-content-reference-guide.md#libraries)
- [Anslutningspoolen (ADO.NET)](/dotnet/framework/data/adonet/sql-server-connection-pooling)
- Att [ *försöka* igen är ett Apache 2,0-licensierat bibliotek för generell användning, som är skrivet i python,](https://pypi.python.org/pypi/retrying) för att förenkla uppgiften att lägga till omprövnings beteende till nästan vad som helst.

<!-- Link references. -->

[step-4-connect-resiliently-to-sql-with-ado-net-a78n]: /sql/connect/ado-net/step-4-connect-resiliently-sql-ado-net

[step-4-connect-resiliently-to-sql-with-php-p42h]: /sql/connect/php/step-4-connect-resiliently-to-sql-with-php