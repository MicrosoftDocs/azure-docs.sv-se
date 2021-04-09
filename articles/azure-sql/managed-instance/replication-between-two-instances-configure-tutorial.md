---
title: Konfigurera replikering mellan hanterade instanser
titleSuffix: Azure SQL Managed Instance
description: I den här självstudien får du lära dig att konfigurera Transaktionsreplikering mellan en Azure SQL-hanterad instans utgivare/distributör och en SQL-hanterad instans prenumerant.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: MashaMSFT
ms.author: ferno
ms.reviewer: mathoma
ms.date: 04/28/2020
ms.openlocfilehash: 47ea5ea73c581313f90791ca6d7892ebad3f666b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101690693"
---
# <a name="tutorial-configure-replication-between-two-managed-instances"></a>Självstudie: Konfigurera replikering mellan två hanterade instanser

[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Med Transaktionsreplikering kan du replikera data från en databas till en annan som finns på antingen SQL Server eller [Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md). SQL-hanterad instans kan vara utgivare, distributör eller prenumerant i replikeringstopologin. Se [konfigurationer för transaktionell replikering](replication-transactional-overview.md#common-configurations) för tillgängliga konfigurationer. 

Transaktionell replikering är för närvarande en offentlig för hands version för SQL-hanterad instans. 

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> - Konfigurera en hanterad instans som en replik utgivare och distributör.
> - Konfigurera en hanterad instans som prenumerant på replikering.

![Replikera mellan två hanterade instanser](./media/replication-between-two-instances-configure-tutorial/sqlmi-sqlmi-repl.png)

Den här självstudien är avsedd för en erfaren mål grupp och förutsätter att användaren är van vid distribution och anslutning till både hanterade instanser och SQL Server virtuella datorer i Azure. 


> [!NOTE]
> - I den här artikeln beskrivs användningen av [transaktionell replikering](/sql/relational-databases/replication/transactional/transactional-replication) i Azure SQL-hanterad instans. Den är inte relaterad till [redundansväxlingen](../database/auto-failover-group-overview.md), en funktion för Azure SQL-hanterad instans som gör att du kan skapa kompletta läsbara repliker av enskilda instanser. Det finns ytterligare överväganden när du konfigurerar [transaktionell replikering med grupper för växling vid fel](replication-transactional-overview.md#with-failover-groups).



## <a name="requirements"></a>Krav

Att konfigurera SQL-hanterad instans så att den fungerar som en utgivare och/eller en distributör kräver:

- Att utgivarens hanterade instans finns i samma virtuella nätverk som distributören och prenumeranten, eller [VPN-gatewayer](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) har kon figurer ATS mellan de virtuella nätverken i alla tre entiteter. 
- Anslutningen använder SQL-autentisering mellan replikeringsdeltagare.
- En Azure Storage-konto resurs för replikeringens arbets katalog.
- Port 445 (TCP utgående) är öppen i säkerhets reglerna för NSG för de hanterade instanserna för åtkomst till Azure-filresursen.  Om felet uppstår `failed to connect to azure storage <storage account name> with os error 53` måste du lägga till en utgående regel i NSG för rätt undernät för SQL-hanterad instans.

## <a name="1---create-a-resource-group"></a>1 – Skapa en resurs grupp

Använd [Azure Portal](https://portal.azure.com) för att skapa en resurs grupp med namnet `SQLMI-Repl` .  

## <a name="2---create-managed-instances"></a>2 – skapa hanterade instanser

Använd [Azure Portal](https://portal.azure.com) för att skapa två [SQL-hanterade instanser](instance-create-quickstart.md) i samma virtuella nätverk och undernät. Namnge till exempel de två hanterade instanserna:

- `sql-mi-pub` (tillsammans med några tecken för slumpmässig het)
- `sql-mi-sub` (tillsammans med några tecken för slumpmässig het)

Du måste också [Konfigurera en virtuell Azure-dator för att ansluta](connect-vm-instance-configure.md) till dina hanterade instanser. 

## <a name="3---create-an-azure-storage-account"></a>3 – skapa ett Azure Storage-konto

[Skapa ett Azure Storage-konto](../../storage/common/storage-account-create.md#create-a-storage-account) för arbets katalogen och skapa sedan en [fil resurs](../../storage/files/storage-how-to-create-file-share.md) i lagrings kontot. 

Kopiera sökvägen till fil resursen i formatet: `\\storage-account-name.file.core.windows.net\file-share-name`

Exempel: `\\replstorage.file.core.windows.net\replshare`

Kopiera lagrings åtkomst nycklarna i formatet: `DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net`

Exempel: `DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net`

Mer information finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md). 

## <a name="4---create-a-publisher-database"></a>4 – skapa en utgivar databas

Anslut till din `sql-mi-pub` hanterade instans med hjälp av SQL Server Management Studio och kör följande Transact-SQL-kod (T-SQL) för att skapa en utgivar databas:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_PUB]
GO

USE [ReplTran_PUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO


USE [ReplTran_PUB]
GO

INSERT INTO ReplTest (ID, c1) VALUES (6, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (2, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (3, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (4, 'pub')
INSERT INTO ReplTest (ID, c1) VALUES (5, 'pub')
GO
SELECT * FROM ReplTest
GO
```

## <a name="5---create-a-subscriber-database"></a>5 – skapa en prenumerations databas

Anslut till din `sql-mi-sub` hanterade instans med hjälp av SQL Server Management Studio och kör följande T-SQL-kod för att skapa din tomma prenumerations databas:

```sql
USE [master]
GO

CREATE DATABASE [ReplTran_SUB]
GO

USE [ReplTran_SUB]
GO
CREATE TABLE ReplTest (
  ID INT NOT NULL PRIMARY KEY,
  c1 VARCHAR(100) NOT NULL,
  dt1 DATETIME NOT NULL DEFAULT getdate()
)
GO
```

## <a name="6---configure-distribution"></a>6 – Konfigurera distribution

Anslut till din `sql-mi-pub` hanterade instans med hjälp av SQL Server Management Studio och kör följande T-SQL-kod för att konfigurera distributions databasen.

```sql
USE [master]
GO

EXEC sp_adddistributor @distributor = @@ServerName;
EXEC sp_adddistributiondb @database = N'distribution';
GO
```

## <a name="7---configure-publisher-to-use-distributor"></a>7 – Konfigurera utgivare att använda distributör

På din Publisher SQL-hanterade instans `sql-mi-pub` ändrar du frågekörningen till [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) -läge och kör följande kod för att registrera den nya distributören hos utgivaren.

```sql
:setvar username loginUsedToAccessSourceManagedInstance
:setvar password passwordUsedToAccessSourceManagedInstance
:setvar file_storage "\\storage-account-name.file.core.windows.net\file-share-name"
-- example: file_storage "\\replstorage.file.core.windows.net\replshare"
:setvar file_storage_key "DefaultEndpointsProtocol=https;AccountName=<Storage-Account-Name>;AccountKey=****;EndpointSuffix=core.windows.net"
-- example: file_storage_key "DefaultEndpointsProtocol=https;AccountName=replstorage;AccountKey=dYT5hHZVu9aTgIteGfpYE64cfis0mpKTmmc8+EP53GxuRg6TCwe5eTYWrQM4AmQSG5lb3OBskhg==;EndpointSuffix=core.windows.net"

USE [master]
EXEC sp_adddistpublisher
  @publisher = @@ServerName,
  @distribution_db = N'distribution',
  @security_mode = 0,
  @login = N'$(username)',
  @password = N'$(password)',
  @working_directory = N'$(file_storage)',
  @storage_connection_string = N'$(file_storage_key)'; -- Remove this parameter for on-premises publishers
```

   > [!NOTE]
   > Se till att endast använda omvända snedstreck ( `\` ) för parametern file_storage. Om du använder ett snedstreck ( `/` ) kan det orsaka ett fel när du ansluter till fil resursen.

Det här skriptet konfigurerar en lokal utgivare på den hanterade instansen, lägger till en länkad server och skapar en uppsättning jobb för SQL Server agenten.

## <a name="8---create-publication-and-subscriber"></a>8 – Skapa publikation och prenumerant

Använd [SQLCMD](/sql/ssms/scripting/edit-sqlcmd-scripts-with-query-editor) läge och kör följande T-SQL-skript för att aktivera replikering för din databas och konfigurera replikering mellan utgivaren, distributören och prenumeranten.

```sql
-- Set variables
:setvar username sourceLogin
:setvar password sourcePassword
:setvar source_db ReplTran_PUB
:setvar publication_name PublishData
:setvar object ReplTest
:setvar schema dbo
:setvar target_server "sql-mi-sub.wdec33262scj9dr27.database.windows.net"
:setvar target_username targetLogin
:setvar target_password targetPassword
:setvar target_db ReplTran_SUB

-- Enable replication for your source database
USE [$(source_db)]
EXEC sp_replicationdboption
  @dbname = N'$(source_db)',
  @optname = N'publish',
  @value = N'true';

-- Create your publication
EXEC sp_addpublication
  @publication = N'$(publication_name)',
  @status = N'active';


-- Configure your log reader agent
EXEC sp_changelogreader_agent
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the publication snapshot
EXEC sp_addpublication_snapshot
  @publication = N'$(publication_name)',
  @frequency_type = 1,
  @publisher_security_mode = 0,
  @publisher_login = N'$(username)',
  @publisher_password = N'$(password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Add the ReplTest table to the publication
EXEC sp_addarticle
  @publication = N'$(publication_name)',
  @type = N'logbased',
  @article = N'$(object)',
  @source_object = N'$(object)',
  @source_owner = N'$(schema)';

-- Add the subscriber
EXEC sp_addsubscription
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @destination_db = N'$(target_db)',
  @subscription_type = N'Push';

-- Create the push subscription agent
EXEC sp_addpushsubscription_agent
  @publication = N'$(publication_name)',
  @subscriber = N'$(target_server)',
  @subscriber_db = N'$(target_db)',
  @subscriber_security_mode = 0,
  @subscriber_login = N'$(target_username)',
  @subscriber_password = N'$(target_password)',
  @job_login = N'$(username)',
  @job_password = N'$(password)';

-- Initialize the snapshot
EXEC sp_startpublication_snapshot
  @publication = N'$(publication_name)';
```

## <a name="9---modify-agent-parameters"></a>9 – ändra agent parametrar

En Azure SQL-hanterad instans har för närvarande en del Server dels problem med replikerings agenter. Även om det här problemet åtgärdas är lösningen att öka värdet för inloggnings tids gränsen för-replik agenterna.

Kör följande T-SQL-kommando på utgivaren för att öka inloggnings tids gränsen:

```sql
-- Increase login timeout to 150s
update msdb..sysjobsteps set command = command + N' -LoginTimeout 150'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Kör följande T-SQL-kommando igen för att ange timeout-värdet för inloggning till standardvärdet, om du behöver göra det:

```sql
-- Increase login timeout to 30
update msdb..sysjobsteps set command = command + N' -LoginTimeout 30'
where subsystem in ('Distribution','LogReader','Snapshot') and command not like '%-LoginTimeout %'
```

Starta om alla tre agenterna för att tillämpa ändringarna.

## <a name="10---test-replication"></a>10-test-replikering

När replikeringen har kon figurer ATS kan du testa den genom att infoga nya objekt i utgivaren och titta på ändringarna Sprid till prenumeranten.

Kör följande T-SQL-kodfragment för att visa raderna på prenumeranten:

```sql
select * from dbo.ReplTest
```

Kör följande T-SQL-kodfragment för att infoga ytterligare rader i utgivaren och kontrol lera sedan raderna igen på prenumeranten.

```sql
INSERT INTO ReplTest (ID, c1) VALUES (15, 'pub')
```

## <a name="clean-up-resources"></a>Rensa resurser

Du tar bort publikationen genom att köra följande T-SQL-kommando:

```sql
-- Drops the publication
USE [ReplTran_PUB]
EXEC sp_droppublication @publication = N'PublishData'
GO
```

Om du vill ta bort alternativet replikering från databasen kör du följande T-SQL-kommando:

```sql
-- Disables publishing of the database
USE [ReplTran_PUB]
EXEC sp_removedbreplication
GO
```

Om du vill inaktivera publicering och distribution kör du följande T-SQL-kommando:

```sql
-- Drops the distributor
USE [master]
EXEC sp_dropdistributor @no_checks = 1
GO
```

Du kan rensa dina Azure-resurser genom [att ta bort SQL-hanterade instans resurser från resurs gruppen](../../azure-resource-manager/management/manage-resources-portal.md#delete-resources) och sedan ta bort resurs gruppen `SQLMI-Repl` . 

## <a name="next-steps"></a>Nästa steg

Du kan också lära dig mer om Transaktionsreplikering [med Azure SQL Managed instance](replication-transactional-overview.md) eller Lär dig att konfigurera replikering mellan en [SQL-hanterad instans utgivare/distributör och en SQL på Azure VM-prenumerant](replication-two-instances-and-sql-server-configure-tutorial.md).