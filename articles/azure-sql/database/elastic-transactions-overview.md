---
title: Distribuerade transaktioner mellan olika molndatabaser (förhandsversion)
description: Översikt över Elastic Database transaktioner med Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 5504b9bc87f78682ff584006255d4e75e5e69fa7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793355"
---
# <a name="distributed-transactions-across-cloud-databases-preview"></a>Distribuerade transaktioner mellan olika molndatabaser (förhandsversion)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Elastic Database-transaktioner för Azure SQL Database och Azure SQL-hanterad instans gör att du kan köra transaktioner som sträcker sig över flera databaser. Elastic Database-transaktioner är tillgängliga för .NET-program med ADO.NET och integreras med den välbekanta programmerings upplevelsen med [system. Transaction](/dotnet/api/system.transactions) -klasser. Information om hur du hämtar biblioteket finns i [.NET Framework 4.6.1 (webb installations program)](https://www.microsoft.com/download/details.aspx?id=49981).
Dessutom finns distribuerade distribuerade instanser av hanterade instanser i [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql).

I ett sådant scenario kräver det vanligt vis att du kör Microsoft Distributed Transaction Coordinator (MSDTC). Eftersom MSDTC inte är tillgängligt för plattforms-som-tjänstprogram i Azure är möjligheten att koordinera distribuerade transaktioner nu direkt integrerad med SQL Database eller hanterad instans. Program kan ansluta till en databas för att starta distribuerade transaktioner och en av databaserna eller servrarna översätts transparent till den distribuerade transaktionen, som visas i följande bild.

I det här dokumentet betraktas "distribuerade transaktioner" och "Elastic Database Transactions" som synonymer och kommer att användas utbytbart.

  ![Distribuerade transaktioner med Azure SQL Database med Elastic Database-transaktioner ][1]

## <a name="common-scenarios"></a>Vanliga scenarier

Transaktioner i Elastic Database gör det möjligt för program att göra atomiska ändringar av data som lagras i flera olika databaser. Förhands granskningen fokuserar på utvecklings upplevelser på klient sidan i C# och .NET. En upplevelse på Server sidan (kod skriven i lagrade procedurer eller Server skript) med hjälp av [Transact-SQL](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) är endast tillgänglig för hanterade instanser.
> [!IMPORTANT]
> I för hands versionen stöds inte körning av Elastic Database-transaktioner mellan Azure SQL Database och Azure SQL Managed instance för tillfället. Transaktionen för Elastic Database kan bara omfatta över en uppsättning SQL-databaser eller uppsättningar med hanterade instanser.

Transaktioner i Elastic Database riktar sig mot följande scenarier:

* Program med flera databaser i Azure: med det här scenariot partitioneras data lodrätt över flera databaser i SQL Database eller en hanterad instans, så att olika typer av data finns på olika databaser. Vissa åtgärder kräver data ändringar, som lagras i två eller flera databaser. Programmet använder elastiska databas transaktioner för att samordna ändringarna mellan databaser och säkerställa Atomicitet.
* Shardade Database-program i Azure: med det här scenariot använder data nivån [Elastic Database klient bibliotek](elastic-database-client-library.md) eller självhorisontell partitionering för att vågrätt partitionera data över flera databaser i SQL Database eller en hanterad instans. Ett framträdande användnings fall är behovet av att utföra atomiska ändringar i ett shardade program för flera klient organisationer när du ändrar intervallet för klienter. Tänk på en överföring från en klient till en annan, både på olika databaser. Ett andra fall är en detaljerad horisontell partitionering för att tillgodose kapacitets behoven för en stor klient, vilket i sin tur innebär att vissa atomiska åtgärder måste sträckas över flera databaser som används för samma klient organisation. Ett tredje fall är atomiska uppdateringar av referens data som replikeras mellan databaser. Atomiska, överförda åtgärder längs dessa rader kan nu samordnas över flera databaser med hjälp av för hands versionen.
  Elastiska databas transaktioner använder två fas genomförande för att säkerställa transaktions atomicning mellan databaser. Det passar bra för transaktioner som inbegriper färre än 100 databaser i taget inom en enda transaktion. Dessa gränser tillämpas inte, men en bör förvänta sig prestanda och framgång för att Elastic Database-transaktioner ska bli lidande när gränserna överskrids.

## <a name="installation-and-migration"></a>Installation och migrering

Funktionerna för Elastic Database-transaktioner tillhandahålls via uppdateringar till .NET-biblioteken System.Data.dll och System.Transactions.dll. DLL: erna säkerställer att genomförande i två faser används vid behov för att säkerställa atomication. Om du vill börja utveckla program med Elastic Database-transaktioner installerar du [.NET Framework 4.6.1](https://www.microsoft.com/download/details.aspx?id=49981) eller en senare version. När den körs på en tidigare version av .NET Framework kan transaktionerna inte befordras till en distribuerad transaktion och ett undantag aktive ras.

Efter installationen kan du använda API: erna för distribuerade transaktioner i system. Transactions med anslutningar till SQL Database och hanterad instans. Om du har befintliga MSDTC-program som använder dessa API: er kan du återskapa dina befintliga program för .NET 4,6 efter att ha installerat 4.6.1-ramverket. Om dina projekt är riktade till .NET 4,6, kommer de automatiskt att använda de uppdaterade DLL-filerna från den nya Ramverks versionen och API-anrop för distribuerade transaktioner i kombination med anslutningar till SQL Database eller en hanterad instans kommer nu att lyckas.

Kom ihåg att elastiska databas transaktioner inte kräver installation av MSDTC. I stället hanteras elastiska databas transaktioner direkt av och inom tjänsten. Detta fören klar moln scenarier avsevärt eftersom en distribution av MSDTC inte är nödvändig för att använda distribuerade transaktioner med SQL Database eller hanterad instans. Avsnitt 4 innehåller mer information om hur du distribuerar elastiska databas transaktioner och det nödvändiga .NET Framework tillsammans med dina moln program till Azure.

## <a name="net-installation-for-azure-cloud-services"></a>.NET-installation för Azure Cloud Services

Azure tillhandahåller flera erbjudanden som värd för .NET-program. Jämförelse av olika erbjudanden finns i [Azure App Service, Cloud Services och Virtual Machines jämförelse](/azure/architecture/guide/technology-choices/compute-decision-tree). Om gäst operativ systemet för erbjudandet är mindre än .NET 4.6.1 krävs för elastiska transaktioner måste du uppgradera gäst operativ systemet till 4.6.1.

Uppgraderingar till gäst operativ systemet stöds inte för närvarande av Azure App Service. För Azure Virtual Machines loggar du bara in på den virtuella datorn och kör installations programmet för det senaste .NET Framework. För Azure Cloud Services måste du inkludera installationen av en nyare .NET-version i Start åtgärderna för distributionen. Begreppen och stegen beskrivs i [Installera .net på en moln tjänst roll](../../cloud-services/cloud-services-dotnet-install-dotnet.md).  

Observera att installations programmet för .NET 4.6.1 kan kräva mer temporär lagring under start processen på Azure Cloud Services än installations programmet för .NET 4,6. För att säkerställa en lyckad installation måste du öka den tillfälliga lagringen för Azures moln tjänst i din service definition. csdef-fil i avsnittet LocalResources och miljö inställningarna för start uppgiften, som du ser i följande exempel:

```xml
    <LocalResources>
    ...
        <LocalStorage name="TEMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
        <LocalStorage name="TMP" sizeInMB="5000" cleanOnRoleRecycle="false" />
    </LocalResources>
    <Startup>
        <Task commandLine="install.cmd" executionContext="elevated" taskType="simple">
            <Environment>
        ...
                <Variable name="TEMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TEMP']/@path" />
                </Variable>
                <Variable name="TMP">
                    <RoleInstanceValue xpath="/RoleEnvironment/CurrentInstance/LocalResources/LocalResource[@name='TMP']/@path" />
                </Variable>
            </Environment>
        </Task>
    </Startup>
```

## <a name="net-development-experience"></a>.NET-utvecklings miljö

### <a name="multi-database-applications"></a>Program med flera databaser

Följande exempel kod använder den välbekanta programmerings upplevelsen med .NET system. Transactions. Klassen TransactionScope upprättar en omgivande transaktion i .NET. (En "omgivande transaktion" är en som finns i den aktuella tråden.) Alla anslutningar som öppnas i TransactionScope deltar i transaktionen. Om olika databaser deltar, höjs transaktionen automatiskt till en distribuerad transaktion. Resultatet av transaktionen styrs genom att ange omfånget till Slutför för att indikera en incheckning.

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = new SqlConnection(connStrDb1))
        {
            conn1.Open();
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = new SqlConnection(connStrDb2))
        {
            conn2.Open();
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T2 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

### <a name="sharded-database-applications"></a>Shardade databas program

Elastic Database-transaktioner för SQL Database och hanterade instanser stöder också koordinera distribuerade transaktioner där du använder OpenConnectionForKey-metoden i klient biblioteket för Elastic Database för att öppna anslutningar för en utskalad data nivå. Överväg fall där du behöver garantera transaktions konsekvens för ändringar i flera olika horisontell partitionering-nyckel värden. Anslutningar till Shards som är värdar för de olika horisontell partitionering-nyckelvärdena sammanställs med hjälp av OpenConnectionForKey. I det allmänna fallet kan anslutningarna vara av olika Shards som garanterar att transaktions garantier kräver en distribuerad transaktion.
I följande kod exempel visas den här metoden. Det förutsätter att en variabel med namnet shardmap används för att representera en Shard-karta från klient biblioteket för Elastic Database:

```csharp
    using (var scope = new TransactionScope())
    {
        using (var conn1 = shardmap.OpenConnectionForKey(tenantId1, credentialsStr))
        {
            SqlCommand cmd1 = conn1.CreateCommand();
            cmd1.CommandText = string.Format("insert into T1 values(1)");
            cmd1.ExecuteNonQuery();
        }

        using (var conn2 = shardmap.OpenConnectionForKey(tenantId2, credentialsStr))
        {
            var cmd2 = conn2.CreateCommand();
            cmd2.CommandText = string.Format("insert into T1 values(2)");
            cmd2.ExecuteNonQuery();
        }

        scope.Complete();
    }
```

## <a name="transact-sql-development-experience"></a>Utvecklings miljö för Transact-SQL

Distribuerade transaktioner på Server sidan med hjälp av Transact-SQL är bara tillgängliga för Azure SQL-hanterade instanser. Distribuerad transaktion kan bara köras mellan hanterade instanser som tillhör samma [Server förtroende grupp](../managed-instance/server-trust-group-overview.md). I det här scenariot måste hanterade instanser använda den [länkade servern](/sql/relational-databases/linked-servers/create-linked-servers-sql-server-database-engine#TsqlProcedure) för att referera till varandra.

I följande exempel Transact-SQL-kod används starta distribuerad [transaktion](/sql/t-sql/language-elements/begin-distributed-transaction-transact-sql) för att starta distribuerad transaktion.

```Transact-SQL

    -- Configure the Linked Server
    -- Add one Azure SQL Managed Instance as Linked Server
    EXEC sp_addlinkedserver
        @server='RemoteServer', -- Linked server name
        @srvproduct='',
        @provider='sqlncli', -- SQL Server Native Client
        @datasrc='managed-instance-server.46e7afd5bc81.database.windows.net' -- Managed Instance endpoint

    -- Add credentials and options to this Linked Server
    EXEC sp_addlinkedsrvlogin
        @rmtsrvname = 'RemoteServer', -- Linked server name
        @useself = 'false',
        @rmtuser = '<login_name>',         -- login
        @rmtpassword = '<secure_password>' -- password

    USE AdventureWorks2012;
    GO
    SET XACT_ABORT ON;
    GO
    BEGIN DISTRIBUTED TRANSACTION;
    -- Delete candidate from local instance.
    DELETE AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    -- Delete candidate from remote instance.
    DELETE RemoteServer.AdventureWorks2012.HumanResources.JobCandidate
        WHERE JobCandidateID = 13;
    COMMIT TRANSACTION;
    GO
```

## <a name="combining-net-and-transact-sql-development-experience"></a>Kombinera .NET och Transact-SQL-utvecklings miljö

.NET-program som använder system. Transaction-klasser kan kombinera TransactionScope-klassen med Transact-SQL-instruktionen starta DISTRIBUERAd transaktion. I TransactionScope befordras den inre transaktion som kör BEGIN DITRIBUTED-transaktionen till distribuerad transaktion. När andra SqlConnecton öppnas i TransactionScope kommer det också att befordras implicit till distribuerad transaktion. När den distribuerade transaktionen har startats kommer alla efterföljande transaktioner som begärs, oavsett om de kommer från .NET eller Transact-SQL, att ansluta till den överordnade distribuerade transaktionen. Eftersom alla kapslade transaktions omfattningar som initieras av BEGIN-instruktionen kommer att upphöra i samma transaktion och genomförande/återställnings uttryck kommer att ha följande påverkan på det övergripande resultatet:
 * GENOMFÖRANDE instruktionen har ingen påverkan på transaktions omfånget som initieras av BEGIN-instruktionen, det vill säga att inga resultat genomförs innan metoden Complete () anropas för TransactionScope-objektet. Om TransactionScope-objektet förstörs innan det slutförs, återställs alla ändringar som gjorts inom omfånget.
 * Återställnings instruktionen leder till att hela TransactionScope återställs. Försök att registrera nya transaktioner i TransactionScope Miss lyckas efteråt, och försöker anropa Complete () på TransactionScope-objektet.

Här är ett exempel där transaktionen explicit befordras till distribuerad transaktion med Transact-SQL.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
        
            // Transaction is here promoted to distributed by BEGIN statement
            //
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            // ...
        }
     
        using (SqlConnection conn2 = new SqlConnection(DB1_ConnectionString)
        {
            conn2.Open();
            // ...
        }
        
        s.Complete();
    }
```

I följande exempel visas en transaktion som implicit befordras till distribuerad transaktion när den andra SqlConnecton startades i TransactionScope.

```csharp
    using (TransactionScope s = new TransactionScope())
    {
        using (SqlConnection conn = new SqlConnection(DB0_ConnectionString)
        {
            conn.Open();
            // ...
        }
        
        using (SqlConnection conn = new SqlConnection(DB1_ConnectionString)
        {
            // Because this is second SqlConnection within TransactionScope transaction is here implicitly promoted distributed.
            //
            conn.Open(); 
            Helper.ExecuteNonQueryOnOpenConnection(conn, "BEGIN DISTRIBUTED TRAN");
            Helper.ExecuteNonQueryOnOpenConnection(conn, lsQuery);
            // ...
        }
        
        s.Complete();
    }
```

## <a name="transactions-across-multiple-servers-for-azure-sql-database"></a>Transaktioner över flera servrar för Azure SQL Database

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Elastiska databas transaktioner stöds på olika servrar i Azure SQL Database. När transaktioner korsar Server gränser måste de deltagande servrarna först anges i en ömsesidig kommunikations relation. När kommunikations relationen har upprättats kan alla databaser i någon av de två servrarna delta i elastiska transaktioner med databaser från den andra servern. Med transaktioner som sträcker sig över fler än två servrar måste en kommunikations relation finnas på plats för alla servrar.

Använd följande PowerShell-cmdletar för att hantera kommunikation mellan servrar för elastiska databas transaktioner:

* **New-AzSqlServerCommunicationLink**: Använd denna cmdlet för att skapa en ny kommunikations relation mellan två servrar i Azure SQL Database. Relationen är symmetrisk, vilket innebär att båda servrarna kan initiera transaktioner med den andra servern.
* **Get-AzSqlServerCommunicationLink**: Använd denna cmdlet för att hämta befintliga kommunikations relationer och deras egenskaper.
* **Remove-AzSqlServerCommunicationLink**: Använd denna cmdlet för att ta bort en befintlig kommunikations relation.

## <a name="transactions-across-multiple-servers-for-azure-sql-managed-instance"></a>Transaktioner över flera servrar för Azure SQL-hanterad instans

Distribuerade transaktioner stöds på olika servrar i Azure SQL-hanterad instans. När transaktionerna korsar hanterade instans gränser måste de deltagande instanserna anges först i en ömsesidig säkerhets-och kommunikations relation. Detta görs genom att skapa en [Server förtroende grupp](../managed-instance/server-trust-group-overview.md)som kan göras på Azure Portal. Om hanterade instanser inte finns i samma virtuella nätverk måste [peering av virtuella nätverk](../../virtual-network/virtual-network-peering-overview.md) konfigureras och nätverks säkerhets grupp regler för inkommande och utgående trafik måste tillåta portarna 5024 och 11000-12000 för alla deltagande virtuella nätverk.

  ![Server förtroende grupper på Azure Portal][3]

Följande diagram visar Server förtroende grupp med hanterade instanser som kan köra distribuerade transaktioner med .NET eller Transact-SQL.

  ![Distribuerade transaktioner med Azure SQL-hanterad instans med elastiska transaktioner][2]

## <a name="monitoring-transaction-status"></a>Övervakar transaktions status

Använd DMV: er (Dynamic Management views) för att övervaka status och förlopp för dina pågående elastiska databas transaktioner. Alla DMV: er som är relaterade till transaktioner är relevanta för distribuerade transaktioner i SQL Database och hanterad instans. Du hittar motsvarande lista med DMV: er här: [transaktions relaterade vyer och funktioner för dynamisk hantering (Transact-SQL)](/sql/relational-databases/system-dynamic-management-views/transaction-related-dynamic-management-views-and-functions-transact-sql).

Dessa DMV: er är särskilt användbara:

* **sys.DM för att \_ omlasta \_ aktiva \_ transaktioner**: Visar aktiva transaktioner och deras status. I kolumnen UOW (enhets arbete) kan du identifiera de olika underordnade transaktioner som tillhör samma distribuerade transaktion. Alla transaktioner inom samma distribuerade transaktion bär samma UOW-värde. Mer information finns i dokumentationen för [DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-active-transactions-transact-sql).
* **sys.DM \_ : \_ \_** ger ytterligare information om transaktioner, till exempel placering av transaktionen i loggen. Mer information finns i dokumentationen för [DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-database-transactions-transact-sql).
* **sys.DM \_ - \_ startlås**: innehåller information om de lås som för närvarande innehas av pågående transaktioner. Mer information finns i dokumentationen för [DMV](/sql/relational-databases/system-dynamic-management-views/sys-dm-tran-locks-transact-sql).

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller för närvarande för Elastic Database-transaktioner i SQL Database:

* Endast transaktioner mellan databaser i SQL Database stöds. Andra [X/Open XA-](https://en.wikipedia.org/wiki/X/Open_XA) resursposter och databaser utanför SQL Database kan inte delta i Elastic Database-transaktioner. Det innebär att elastiska databas transaktioner inte kan sträckas ut över lokalt SQL Server och Azure SQL Database. Fortsätt att använda MSDTC för distribuerade transaktioner lokalt.
* Endast klient samordnade transaktioner från ett .NET-program stöds. Stöd på Server sidan för T-SQL, till exempel starta DISTRIBUERAd transaktion, är planerat, men ännu inte tillgängligt.
* Transaktioner över WCF-tjänster stöds inte. Du kan till exempel ha en WCF-tjänst metod som kör en transaktion. Omslutning av anrop inom ett transaktions omfång Miss fungerar som [system. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).

Följande begränsningar gäller för närvarande för distribuerade transaktioner i en hanterad instans:

* Endast transaktioner mellan databaser i en hanterad instans stöds. Andra [X/Open XA-](https://en.wikipedia.org/wiki/X/Open_XA) resursposter och databaser utanför Azure SQL Managed instance kan inte delta i distribuerade transaktioner. Det innebär att distribuerade transaktioner inte kan sträckas ut över lokalt SQL Server och Azure SQL-hanterad instans. Fortsätt att använda MSDTC för distribuerade transaktioner lokalt.
* Transaktioner över WCF-tjänster stöds inte. Du kan till exempel ha en WCF-tjänst metod som kör en transaktion. Omslutning av anrop inom ett transaktions omfång Miss fungerar som [system. ServiceModel. ProtocolException](/dotnet/api/system.servicemodel.protocolexception).
* Azure SQL Managed instance måste vara en del av en [Server förtroende grupp](../managed-instance/server-trust-group-overview.md) för att kunna delta i en distribuerad transaktion.
* Begränsningar i [Server förtroende grupper](../managed-instance/server-trust-group-overview.md) påverkar distribuerade transaktioner.
* Hanterade instanser som deltar i distribuerade transaktioner måste ha anslutning över privata slut punkter (med hjälp av privat IP-adress från det virtuella nätverket där de distribueras) och måste kunna refereras samtidigt med privata FQDN. Klient program kan använda distribuerade transaktioner på privata slut punkter. Om Transact-SQL utnyttjar länkade servrar som refererar till privata slut punkter kan även klient program använda distribuerade transaktioner på offentliga slut punkter. Den här begränsningen beskrivs i följande diagram.
  ![Begränsning för privat slut punkts anslutning][4]
## <a name="next-steps"></a>Nästa steg

* För frågor, kontakta oss på [sidan Microsoft Q&en fråga för SQL Database](/answers/topics/azure-sql-database.html).
* Om du vill ha funktions förfrågningar lägger du till dem i forumen [SQL Database feedback](https://feedback.azure.com/forums/217321-sql-database/) eller [hanterad instans](https://feedback.azure.com/forums/915676-sql-managed-instance).



<!--Image references-->
[1]: ./media/elastic-transactions-overview/distributed-transactions.png
[2]: ./media/elastic-transactions-overview/sql-mi-distributed-transactions.png
[3]: ./media/elastic-transactions-overview/server-trust-groups-azure-portal.png
[4]: ./media/elastic-transactions-overview/managed-instance-distributed-transactions-private-endpoint-limitations.png
