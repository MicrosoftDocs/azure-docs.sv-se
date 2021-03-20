---
title: Transaktionsreplikering
titleSuffix: Azure SQL Managed Instance
description: Lär dig mer om att använda SQL Server Transaktionsreplikering med Azure SQL-hanterad instans (för hands version).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein
ms.date: 04/20/2020
ms.openlocfilehash: 3e4b4fc3d4a6c9529c7c0ac0daef8a28173e0bf3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99225351"
---
# <a name="transactional-replication-with-azure-sql-managed-instance-preview"></a>Transaktionell replikering med Azure SQL-hanterad instans (för hands version)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Transaktionsreplikering är en funktion i Azure SQL-hanterad instans och SQL Server som gör att du kan replikera data från en tabell i en Azure SQL-hanterad instans eller en SQL Server instans till tabeller som placeras på fjärrdatabaser. Med den här funktionen kan du synkronisera flera tabeller i olika databaser. 

Transaktionell replikering är för närvarande en offentlig för hands version för SQL-hanterad instans. 

## <a name="overview"></a>Översikt

Du kan använda Transaktionsreplikering för att skicka ändringar som görs i en hanterad Azure SQL-instans till:

- En SQL Server databas – lokalt eller på en virtuell Azure-dator
- En databas i Azure SQL Database
- En instans databas i Azure SQL-hanterad instans

  > [!NOTE]
  > Om du vill använda alla funktioner i den hanterade Azure SQL-instansen måste du använda de senaste versionerna av [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) och [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt).

### <a name="components"></a>Komponenter

Nyckel komponenterna i Transaktionsreplikering är **utgivare**, **distributör** och **prenumerant**, som du ser i följande bild:  

![replikering med SQL Database](./media/replication-transactional-overview/replication-to-sql-database.png)

| Roll | Azure SQL Database | Hanterad Azure SQL-instans |
| :----| :------------- | :--------------- |
| **Utgivare** | Inga | Ja |
| **Möjligheter** | Inga | Ja|
| **Pull-prenumerant** | Inga | Ja|
| **Push-prenumerant**| Ja | Ja|
| &nbsp; | &nbsp; | &nbsp; |

**Utgivaren** publicerar ändringar som gjorts i vissa tabeller (artiklar) genom att skicka uppdateringarna till distributören. Utgivaren kan vara en hanterad Azure SQL-instans eller en SQL Server instans.

**Distributören** samlar in ändringar i artiklarna från en utgivare och distribuerar dem till prenumeranterna. Distributören kan antingen vara en Azure SQL-hanterad instans eller en SQL Server instans (vilken version som helst som är lika med eller högre än utgivar versionen).

**Prenumeranten** tar emot ändringar som gjorts på utgivaren. En SQL Server instans och en Azure SQL-hanterad instans kan både vara push-och pull-prenumeranter, även om en pull-prenumeration inte stöds när distributören är en Azure SQL-hanterad instans och prenumeranten inte är det. En databas i Azure SQL Database kan bara vara en push-prenumerant.

En Azure SQL-hanterad instans kan stödja en prenumerant från följande versioner av SQL Server:

- SQL Server 2016 och senare
- SQL Server 2014 [RTM-CU10 (12.0.4427.24)](https://support.microsoft.com/help/3094220/cumulative-update-10-for-sql-server-2014) eller [SP1-CU3 (12.0.2556.4)](https://support.microsoft.com/help/3094221/cumulative-update-3-for-sql-server-2014-service-pack-1)
- SQL Server 2012 [SP2 CU8 (11.0.5634.1)](https://support.microsoft.com/help/3082561/cumulative-update-8-for-sql-server-2012-sp2) eller [SP3 (11.0.6020.0)](https://www.microsoft.com/download/details.aspx?id=49996)

   > [!NOTE]
   >
   > - För andra versioner av SQL Server som inte stöder publicering till objekt i Azure är det möjligt att använda metoden [republishing](/sql/relational-databases/replication/republish-data) för att flytta data till nyare versioner av SQL Server.
   > - Försök att konfigurera replikering med en äldre version kan resultera i fel nummer MSSQL_REPL20084 (processen kunde inte ansluta till prenumeranten.) och MSSQ_REPL40532 (det går inte att öppna servern \<name> som begärdes av inloggningen. Inloggningen misslyckades.)

### <a name="types-of-replication"></a>Typer av replikering

Det finns olika [typer av replikering](/sql/relational-databases/replication/types-of-replication):

| Replikering | Azure SQL Database | Hanterad Azure SQL-instans |
| :----| :------------- | :--------------- |
| [**Standard transaktion**](/sql/relational-databases/replication/transactional/transactional-replication) | Ja (endast som prenumerant) | Ja |
| [**Ögonblicks bild**](/sql/relational-databases/replication/snapshot-replication) | Ja (endast som prenumerant) | Ja|
| [**Sammanfoga replikering**](/sql/relational-databases/replication/merge/merge-replication) | Inga | Inga|
| [**Peer-to-peer**](/sql/relational-databases/replication/transactional/peer-to-peer-transactional-replication) | Inga | Inga|
| [**Dubbelriktad**](/sql/relational-databases/replication/transactional/bidirectional-transactional-replication) | Inga | Ja|
| [**Uppdaterings bara prenumerationer**](/sql/relational-databases/replication/transactional/updatable-subscriptions-for-transactional-replication) | Inga | Inga|
| &nbsp; | &nbsp; | &nbsp; |

### <a name="supportability-matrix"></a>Support mat ris

  Matrisen för stöd för transaktionella replikering för Azure SQL Managed instance är samma som den för SQL Server.
  
| **Utgivare**   | **Möjligheter** | **Prenumerant** |
| :------------   | :-------------- | :------------- |
| SQL Server 2019 | SQL Server 2019 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/>  |
| SQL Server 2017 | SQL Server 2019 <br/>SQL Server 2017 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 |
| SQL Server 2016 | SQL Server 2019 <br/>SQL Server 2017 <br/> SQL Server 2016 | SQL Server 2019 <br/> SQL Server 2017 <br/>SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 |
| SQL Server 2014 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>| SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2012 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> | SQL Server 2016 <br/> SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |
| SQL Server 2008 R2 <br/> SQL Server 2008 | SQL Server 2019 <br/> SQL Server 2017 <br/> SQL Server 2016 <br/> SQL Server 2014 <br/>SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 |  SQL Server 2014 <br/> SQL Server 2012 <br/> SQL Server 2008 R2 <br/> SQL Server 2008 <br/>  |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="when-to-use"></a>När du ska använda detta

Transaktionell replikering är användbart i följande scenarier:

- Publicera ändringar som gjorts i en eller flera tabeller i en databas och distribuera dem till en eller flera databaser i en SQL Server instans eller Azure SQL Database som prenumererar på ändringarna.
- Behåll flera distribuerade databaser i synkroniserat tillstånd.
- Migrera databaser från en SQL Server instans eller en hanterad Azure SQL-instans till en annan databas genom att kontinuerligt publicera ändringarna.

### <a name="compare-data-sync-with-transactional-replication"></a>Jämför datasynkronisering med transaktionell replikering

| Kategori | Datasynkronisering | Transaktionsreplikering |
|---|---|---|
| Fördelar | – Stöd för aktiv-aktiv<br/>– Dubbelriktad mellan lokala och Azure SQL Database | -Nedre latens<br/>– Transaktionell konsekvens<br/>-Återanvänd befintlig topologi efter migrering |
| Nackdelar | – Ingen transaktionell konsekvens<br/>-Högre prestanda påverkan | -Det går inte att publicera från Azure SQL Database <br/>– Kostnad för hög underhåll |

## <a name="common-configurations"></a>Vanliga konfigurationer

I allmänhet måste utgivaren och distributören vara antingen i molnet eller lokalt. Följande konfigurationer stöds:

### <a name="publisher-with-local-distributor-on-sql-managed-instance"></a>Utgivare med lokal distributör på SQL-hanterad instans

![Enskild instans som utgivare och distributör](./media/replication-transactional-overview/01-single-instance-asdbmi-pubdist.png)

Utgivare och distributör konfigureras inom en enskild SQL-hanterad instans och distribuerar ändringar till en annan SQL-hanterad instans, SQL Database eller SQL Server instans.

### <a name="publisher-with-remote-distributor-on-sql-managed-instance"></a>Utgivare med fjärrdistributör på SQL-hanterad instans

I den här konfigurationen publicerar en hanterad instans ändringar i en åter försäljare som placerats på en annan SQL-hanterad instans som kan hantera flera SQL-hanterade instanser och distribuera ändringar till ett eller flera mål på Azure SQL Database, Azure SQL-hanterad instans eller SQL Server.

![Separata instanser för utgivare och distributör](./media/replication-transactional-overview/02-separate-instances-asdbmi-pubdist.png)

Utgivare och distributör konfigureras på två hanterade instanser. Det finns vissa begränsningar med den här konfigurationen:

- Båda hanterade instanser finns på samma vNet.
- Båda hanterade instanser finns på samma plats.

### <a name="on-premises-publisherdistributor-with-remote-subscriber"></a>Lokal utgivare/distributör med fjärran sluten prenumerant

![Azure SQL Database som prenumerant](./media/replication-transactional-overview/03-azure-sql-db-subscriber.png)

I den här konfigurationen är en databas i Azure SQL Database eller Azure SQL-hanterad instans en prenumerant. Den här konfigurationen stöder migrering från lokal plats till Azure. Om en prenumerant är en databas i Azure SQL Database måste den vara i push-läge.  

## <a name="requirements"></a>Krav

- Använd SQL-autentisering för anslutning mellan replikeringspartner.
- Använd en Azure Storage konto resurs för arbets katalogen som används av replikeringen.
- Öppna TCP utgående port 445 i säkerhets reglerna för undernät för att få åtkomst till Azure-filresursen.
- Öppna TCP utgående port 1433 när den SQL-hanterade instansen är utgivare/distributör och prenumeranten inte är det. Du kan också behöva ändra den SQL-hanterade instans NSG utgående säkerhets regel för `allow_linkedserver_outbound` för port 1433 **mål tjänst tag gen** från `virtualnetwork` till `internet` .
- Placera både utgivaren och distributören i molnet eller både lokalt.
- Konfigurera VPN-peering mellan virtuella nätverk för replikeringspartner om de virtuella nätverken skiljer sig åt.

> [!NOTE]
> Du kan stöta på fel 53 när du ansluter till en Azure Storage-fil om den utgående nätverks säkerhets gruppen (NSG) port 445 är blockerad när distributören är en Azure SQL-hanterad instans databas och prenumeranten är lokalt. [Uppdatera vNet-NSG](../../storage/files/storage-troubleshoot-windows-file-connection-problems.md) för att lösa problemet.

## <a name="with-failover-groups"></a>Med failover-grupper

[Aktiv geo-replikering](../database/active-geo-replication-overview.md) stöds inte med en SQL-hanterad instans med transaktionell replikering. I stället för aktiv geo-replikering använder du [grupper för automatisk redundans](../database/auto-failover-group-overview.md), men Observera att publikationen måste [tas bort manuellt](transact-sql-tsql-differences-sql-server.md#replication) från den primära hanterade instansen och sedan återskapas på den sekundära SQL-hanterade instansen efter redundansväxlingen.

Om geo-replikering har Aktiver ATS för en **utgivare** eller **distributör** av SQL-hanterad instans i en [grupp för växling vid fel](../database/auto-failover-group-overview.md), måste SQL-hanterad instans administratör rensa alla publikationer på den gamla primären och konfigurera om dem på den nya primära efter en redundansväxling. Följande aktiviteter behövs i det här scenariot:

1. Stoppa alla migreringsjobb som körs på databasen, om det finns några.
1. Släpp metadata för prenumerationer från utgivare genom att köra följande skript på utgivar databasen:

   ```sql
   EXEC sp_dropsubscription @publication='<name of publication>', @article='all',@subscriber='<name of subscriber>'
   ```

1. Ta bort metadata för prenumerationen från prenumeranten. Kör följande skript i prenumerations databasen på prenumerantens SQL-hanterade instans:

   ```sql
   EXEC sp_subscription_cleanup
      @publisher = N'<full DNS of publisher, e.g. example.ac2d23028af5.database.windows.net>',
      @publisher_db = N'<publisher database>',
      @publication = N'<name of publication>';
   ```

1. Tvinga bort alla replikeringsalternativ från utgivaren genom att köra följande skript i den publicerade databasen:

   ```sql
   EXEC sp_removedbreplication
   ```

1. Framtvinga släppa gamla distributörer från den ursprungliga primära SQL-hanterade instansen (om det växlar tillbaka till en gammal primär som används för en distributör). Kör följande skript på huvud databasen i den gamla distributören av SQL-hanterad instans:

   ```sql
   EXEC sp_dropdistributor 1,1
   ```

Om geo-replikering har Aktiver ATS på en **prenumerants** instans i en failover-grupp ska publikationen konfigureras för att ansluta till slut punkten för den lyssnare som hanterar den hanterade instansen för en prenumerant. I händelse av en redundansväxling beror efterföljande åtgärder av den hanterade instans administratören på vilken typ av redundansväxling som har inträffat:

- För en redundansväxling utan data förlust fortsätter replikeringen att fungera efter redundansväxlingen.
- Replikeringen fungerar även för redundans med data förlust. Den kommer att replikera de förlorade ändringarna igen.
- För en redundansväxling med data förlust, men data förlusten ligger utanför lagrings perioden för distributions databasen, måste SQL-hanterade instans administratören initiera om prenumerations databasen.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du konfigurerar Transaktionsreplikering finns i följande Självstudier:

- [Konfigurera replikering mellan en SQL-hanterad instans utgivare och prenumerant](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Konfigurera replikering mellan en SQL-hanterad instans utgivare, SQL-hanterad instans distributör och SQL Server prenumerant](../managed-instance/replication-two-instances-and-sql-server-configure-tutorial.md)
- [Skapa en publikation](/sql/relational-databases/replication/publish/create-a-publication).
- [Skapa en push-prenumeration](/sql/relational-databases/replication/create-a-push-subscription) med hjälp av Server namnet som prenumeranten (till exempel `N'azuresqldbdns.database.windows.net` och databasen i Azure SQL Database namn som mål databas (till exempel **AdventureWorks**. )

## <a name="see-also"></a>Se även  

- [Replikering med en SQL-hanterad instans och en failover-grupp](transact-sql-tsql-differences-sql-server.md#replication)
- [Replikering till SQL-databas](../database/replication-to-sql-database.md)
- [Replikering till hanterad instans](../managed-instance/replication-between-two-instances-configure-tutorial.md)
- [Skapa en publikation](/sql/relational-databases/replication/publish/create-a-publication)
- [Skapa en push-prenumeration](/sql/relational-databases/replication/create-a-push-subscription/)
- [Typer av replikering](/sql/relational-databases/replication/types-of-replication)
- [Övervakning (replikering)](/sql/relational-databases/replication/monitor/monitoring-replication)
- [Initiera en prenumeration](/sql/relational-databases/replication/initialize-a-subscription)
