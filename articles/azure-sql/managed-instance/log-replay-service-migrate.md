---
title: Migrera databaser till SQL Managed Instance log replay-tjänsten
description: Lär dig hur du migrerar databaser från SQL Server till SQL Managed Instance med log replay-tjänsten
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1, devx-track-azurecli
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/31/2021
ms.openlocfilehash: 522f4ec2f28f9d8975a8a7a7b10e435f602af855
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484038"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrera databaser från SQL Server till SQL Managed Instance med Log Replay Service (förhandsversion)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln förklarar hur du manuellt konfigurerar databasmigrering från SQL Server 2008–2019 till Azure SQL Managed Instance med hjälp av Log Replay Service (LRS), som för närvarande är en offentlig förhandsversion. LRS är en molntjänst som är aktiverad för SQL Managed Instance och som bygger SQL Server loggöverföringsteknik. 

[Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md) och LRS använder samma underliggande migreringsteknik och samma API:er. Genom att lansera LRS aktiverar vi ytterligare komplexa anpassade migreringar och hybridarkitektur mellan lokala SQL Server och SQL Managed Instance.

## <a name="when-to-use-log-replay-service"></a>När du ska använda log replay-tjänsten

När du inte kan använda Azure Database Migration Service för migrering kan du använda LRS direkt med PowerShell, Azure CLI-cmdlets eller API:er för att manuellt skapa och samordna databasmigrering till SQL Managed Instance. 

Du kan överväga att använda LRS i följande fall:
- Du behöver mer kontroll för databasmigreringsprojektet.
- Det finns lite tolerans för stilleståndstid vid migreringsavbrott.
- Den Database Migration Service körbara filen kan inte installeras i din miljö.
- Den Database Migration Service körbara filen har inte filåtkomst till databassäkerhetskopior.
- Ingen åtkomst till värdoperativsystemet är tillgänglig eller det finns inga administratörsbehörigheter.
- Du kan inte öppna nätverksportar från din miljö till Azure.
- Nätverksbegränsning eller proxyblockeringsproblem i din miljö.
- Säkerhetskopior lagras direkt till Azure Blob Storage via `TO URL` alternativet .
- Du måste använda differentiella säkerhetskopior.

> [!NOTE]
> Vi rekommenderar att du automatiserar migreringen av databaser från SQL Server till SQL Managed Instance med hjälp av Database Migration Service. Den här tjänsten använder samma LRS-molntjänst på backend-delen, med loggleverans i `NORECOVERY` läge. Överväg att manuellt använda LRS för att samordna migreringar Database Migration Service inte helt stöder dina scenarier.

## <a name="how-it-works"></a>Så här fungerar det

Att skapa en anpassad lösning med hjälp av LRS för att migrera databaser till molnet kräver flera orkestreringssteg, som du ser i diagrammet och tabellen senare i det här avsnittet.

Migreringen består av att göra fullständiga databassäkerhetskopior på SQL Server aktiverat och kopiera `CHECKSUM` säkerhetskopierade filer till Azure Blob Storage. LRS används för att återställa säkerhetskopierade filer från Blob Storage till SQL Managed Instance. Blob Storage mellan lagringen mellan SQL Server och SQL Managed Instance.

LRS övervakar Blob Storage eventuella nya differentiella säkerhetskopior eller loggsäkerhetskopior som lagts till efter att den fullständiga säkerhetskopian har återställts. LRS återställer sedan automatiskt dessa nya filer. Du kan använda tjänsten för att övervaka förloppet för säkerhetskopierade filer som återställs på SQL Managed Instance och du kan stoppa processen om det behövs.

LRS kräver ingen specifik namngivningskonvention för säkerhetskopieringsfiler. Den söker igenom alla filer som placeras Blob Storage och konstruerar säkerhetskopieringskedjan från att endast läsa filhuvudena. Databaser är i ett "återställningstillstånd" under migreringsprocessen. Databaser återställs i [NORECOVERY-läge,](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) så de kan inte användas för läsning eller skrivning förrän migreringsprocessen har slutförts. 

Om du migrerar flera databaser måste du:
 
- Placera säkerhetskopior för varje databas i en separat mapp på Blob Storage.
- Starta LRS separat för varje databas.
- Ange olika sökvägar för att Blob Storage mappar. 

Du kan starta LRS i läget *automatisk komplettering* *eller kontinuerlig.* När du startar den i läget för automatisk komplettering slutförs migreringen automatiskt när den sista av de angivna säkerhetskopieringsfilerna har återställts. När du startar LRS i kontinuerligt läge återställer tjänsten kontinuerligt alla nya säkerhetskopieringsfiler som lagts till, och migreringen slutförs endast vid den manuella snabbstarten. 

Vi rekommenderar att du manuellt klipper över när den slutliga loggslutssäkerhetskopian har tagits och visas som återställd SQL Managed Instance. Det sista direktsteget gör databasen online och tillgänglig för läs- och skrivanvändning på SQL Managed Instance.

När LRS har stoppats, antingen automatiskt via automatisk komplettering eller manuellt via snabb, kan du inte återuppta återställningsprocessen för en databas som har förts online på SQL Managed Instance. Om du vill återställa ytterligare säkerhetskopierade filer när migreringen är klar via automatisk komplettering eller snabb återställning måste du ta bort databasen. Du måste också återställa hela säkerhetskopieringskedjan från grunden genom att starta om LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagram som förklarar dirigeringsstegen för Log Replay-tjänsten för SQL Managed Instance." border="false":::
    
| Åtgärd | Information |
| :----------------------------- | :------------------------- |
| **1. Kopiera databassäkerhetskopior från SQL Server till Blob Storage**. | Kopiera fullständiga, differentiella och loggsäkerhetskopior från SQL Server till en Blob Storage container med [hjälp av Azcopy](../../storage/common/storage-use-azcopy-v10.md) [eller Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Använd alla filnamn. LRS kräver inte någon specifik namngivningskonvention för filer.<br /><br />När du migrerar flera databaser behöver du en separat mapp för varje databas. |
| **2. Starta LRS i molnet**. | Du kan starta om tjänsten med ett val av cmdlets: PowerShell[(start-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)eller Azure CLI ([az_sql_midb_log_replay_start cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Starta LRS separat för varje databas som pekar på en säkerhetskopia på Blob Storage. <br /><br /> När du startar tjänsten tar den säkerhetskopior från containern Blob Storage och börjar återställa dem på SQL Managed Instance.<br /><br /> Om du startade LRS i kontinuerligt läge kommer tjänsten, efter att alla ursprungligen uppladdade säkerhetskopior har återställts, att hålla utkik efter nya filer som laddats upp till mappen. Tjänsten tillämpar kontinuerligt loggar baserat på loggsekvensnummerkedjan (LSN) tills den stoppas. |
| **2.1. Övervaka åtgärdens förlopp**. | Du kan övervaka förloppet för återställningsåtgärden med ett antal cmdlets: PowerShell ([get-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)eller Azure CLI ([az_sql_midb_log_replay_show cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2.2. Stoppa åtgärden om det behövs**. | Om du behöver stoppa migreringsprocessen kan du välja mellan följande cmdlets: PowerShell ([stop-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)eller Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> Om du stoppar åtgärden raderas databasen som du återställer SQL Managed Instance. När du har stoppat en åtgärd kan du inte återuppta LRS för en databas. Du måste starta om migreringsprocessen från grunden. |
| **3. Klipp ut till molnet när du är klar.** | Stoppa programmet och arbetsbelastningen. Ta den senaste säkerhetskopieringen av loggen och ladda upp den till Azure Blob Storage.<br /><br /> Slutför övergången genom att initiera en LRS-åtgärd med ett antal `complete` cmdlets: PowerShell ([complete-azsqlinstancedatabaselogreplay)](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)eller Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Den här åtgärden stoppar LRS och gör att databasen blir online för användning med läsning och skrivning SQL Managed Instance.<br /><br /> Repoint the application connection string from SQL Server to SQL Managed Instance. Du måste orkestrera det här steget själv, antingen via en manuell ändring av anslutningssträngen i ditt program eller automatiskt (t.ex. om ditt program till exempel kan läsa anslutningssträngen från en egenskap eller en databas). |

## <a name="requirements-for-getting-started"></a>Krav för att komma igång

### <a name="sql-server-side"></a>SQL Server sidan
- SQL Server 2008-2019
- Fullständig säkerhetskopiering av databaser (en eller flera filer)
- Differentiell säkerhetskopia (en eller flera filer)
- Loggsäkerhetskopia (inte delad för en transaktionsloggfil)
- `CHECKSUM` aktiverat för säkerhetskopieringar (obligatoriskt)

### <a name="azure-side"></a>Azure-sida
- PowerShell Az.SQL-modul version 2.16.0 eller senare ([installerad](https://www.powershellgallery.com/packages/Az.Sql/) eller åtkoms [via Azure Cloud Shell](/azure/cloud-shell/))
- Azure CLI version 2.19.0 eller senare[(installerat)](/cli/azure/install-azure-cli)
- Azure Blob Storage containern har etablerats
- Säkerhetstoken för signatur för delad åtkomst (SAS) med läs- och listbehörigheter som genererats för Blob Storage containern

### <a name="migration-of-multiple-databases"></a>Migrering av flera databaser
Du måste placera säkerhetskopierade filer för olika databaser i separata mappar på Blob Storage.

Starta LRS separat för varje databas genom att peka på en lämplig mapp Blob Storage. LRS har stöd för upp till 100 samtidiga återställningsprocesser per enskild hanterad instans.

### <a name="azure-rbac-permissions"></a>Azure RBAC-behörigheter
Att köra LRS via de tillhandahållna klienterna kräver någon av följande Azure-roller:
- Rollen Prenumerationsägare
- [Rollen Hanterad instansdeltagare](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Anpassad roll med följande behörighet: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Bästa praxis

Vi rekommenderar följande metodtips:
- Kör [Data Migration Assistant](/sql/dma/dma-overview) för att verifiera att dina databaser är redo att migreras till SQL Managed Instance. 
- Dela upp fullständiga och differentiella säkerhetskopior i flera filer i stället för att använda en enda fil.
- Aktivera komprimering av säkerhetskopiering.
- Använd Cloud Shell för att köra skript, eftersom den alltid kommer att uppdateras till de senaste cmdletarna som släppts.
- Planera att slutföra migreringen inom 47 timmar efter att du har börjat använda LRS. Det här är en respitperiod som förhindrar installationen av system-hanterade programkorrigeringar.

> [!IMPORTANT]
> - Du kan inte använda databasen som återställs via LRS förrän migreringen är klar. 
> - LRS stöder inte skrivskyddade åtkomst till databaser under migreringen.
> - När migreringen är klar slutförs migreringsprocessen eftersom LRS inte stöder att återställningsprocessen återupptas.

## <a name="steps-to-execute"></a>Steg att köra

### <a name="make-backups-of-sql-server"></a>Göra säkerhetskopior av SQL Server

Du kan göra säkerhetskopior av SQL Server med något av följande alternativ:

- Säkerhetskopiera till lokal disklagring och ladda sedan upp filer till Azure Blob Storage, om din miljö begränsar direkta säkerhetskopieringar till Blob Storage.
- Back up directly to Blob Storage with the option in T-SQL (Säkerhetsprocedurer och miljö- och säkerhetsprocedurer tillåter det) med alternativet i `TO URL` T-SQL. 

Ange databaser som du vill migrera till det fullständiga återställningsläget för att tillåta loggsäkerhetskopior.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Om du vill göra fullständiga, differentiella och loggsäkerhetskopior av databasen manuellt på lokal lagring använder du följande T-SQL-exempelskript. Kontrollera att `CHECKSUM` alternativet är aktiverat eftersom det är obligatoriskt för LRS.

```SQL
-- Example of how to make a full database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO

-- Example of how to make a differential database backup to the local disk
BACKUP DATABASE [SampleDB]
TO DISK='C:\BACKUP\SampleDB_diff.bak'
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to the local disk
BACKUP LOG [SampleDB]
TO DISK='C:\BACKUP\SampleDB_log.trn'
WITH COMPRESSION, CHECKSUM
GO
```

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Azure Blob Storage används som mellanlagring för säkerhetskopierade filer mellan SQL Server och SQL Managed Instance. Följ dessa steg om du vill skapa ett nytt lagringskonto och en blobcontainer i lagringskontot:

1. [Skapa ett lagringskonto](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Det finns en blobcontainer](../../storage/blobs/storage-quickstart-blobs-portal.md) i lagringskontot.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Kopiera säkerhetskopior från SQL Server till Blob Storage

När du migrerar databaser till en hanterad instans med hjälp av LRS kan du använda följande metoder för att ladda upp säkerhetskopior till Blob Storage:
- Använda SQL Server inbyggda [funktioner för SÄKERHETSKOPIERING TILL URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Använda [Azcopy eller](../../storage/common/storage-use-azcopy-v10.md) [Azure Storage Explorer för att](https://azure.microsoft.com/en-us/features/storage-explorer) ladda upp säkerhetskopior till en blobcontainer
- Använda Storage Explorer i Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Göra säkerhetskopior från SQL Server direkt till Blob Storage
Om företagets och nätverkets principer tillåter det är ett alternativ att göra säkerhetskopior från SQL Server direkt till Blob Storage med hjälp av SQL Server [inbyggda alternativet SÄKERHETSKOPIERA TILL URL.](/sql/relational-databases/backup-restore/sql-server-backup-to-url) Om du kan använda det här alternativet behöver du inte göra säkerhetskopior på den lokala lagringen och ladda upp dem till Blob Storage.

Som första steg kräver den här åtgärden att du genererar en SAS-autentiseringstoken för Blob Storage och sedan importerar token till SQL Server. Det andra steget är att göra säkerhetskopior med `TO URL` alternativet i T-SQL. Se till att alla säkerhetskopior görs med `CHEKSUM` alternativet aktiverat.

Som referens gör följande exempelkod säkerhetskopior för att Blob Storage. Det här exemplet innehåller inte instruktioner om hur du importerar SAS-token. Du hittar detaljerade anvisningar, inklusive hur du genererar och importerar SAS-token till SQL Server, i självstudien [Använda Azure Blob Storage med SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

```SQL
-- Example of how to make a full database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_full.bak'
WITH INIT, COMPRESSION, CHECKSUM
GO
-- Example of how to make a differential database backup to a URL
BACKUP DATABASE [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_diff.bak'  
WITH DIFFERENTIAL, COMPRESSION, CHECKSUM
GO

-- Example of how to make a transactional log backup to a URL
BACKUP LOG [SampleDB]
TO URL = 'https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>/SampleDB_log.trn'  
WITH COMPRESSION, CHECKSUM
```

### <a name="generate-a-blob-storage-sas-authentication-token-for-lrs"></a>Generera en Blob Storage SAS-autentiseringstoken för LRS

Azure Blob Storage används som mellanlagring för säkerhetskopierade filer mellan SQL Server och SQL Managed Instance. Du måste generera en SAS-autentiseringstoken, med endast list- och läsbehörigheter, för LRS. Token gör att LRS kan komma åt Blob Storage och använda säkerhetskopierade filer för att återställa dem på SQL Managed Instance. 

Följ dessa steg för att generera token:

1. Öppna Storage Explorer från Azure Portal.
2. Expandera **Blobcontainrar**.
3. Högerklicka på blobcontainern och välj **Hämta signatur för delad åtkomst.**

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Skärmbild som visar val för att generera en S A S-autentiseringstoken.":::

4. Välj tidsram för tokenförfallotid. Kontrollera att token är giltig under migreringen.
5. Välj tidszon för token: UTC eller din lokala tid.
    
   > [!IMPORTANT]
   > Tidszonen för token och din hanterade instans kan matchas felaktigt. Se till att SAS-token har rätt tids giltighet, med hänsyn till tidszoner. Om möjligt anger du tidszonen till en tidigare och senare tidpunkt för det planerade migreringsfönstret.
6. Välj **Endast läs-** **och** listbehörigheter.

   > [!IMPORTANT]
   > Välj inte några andra behörigheter. Om du gör det startar inte LRS. Det här säkerhetskravet är designspecifikt.
7. Välj **Skapa**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Skärmbild som visar val för S A S-tokenförfallotid, tidszon och behörigheter, tillsammans med knappen Skapa.":::

SAS-autentiseringen genereras med den tids giltighet som du har angett. Du behöver URI-versionen av token, som du ser i följande skärmbild.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Skärmbild som visar ett exempel på U R I-versionen av en S A S-token.":::

### <a name="copy-parameters-from-the-sas-token"></a>Kopiera parametrar från SAS-token

Innan du använder SAS-token för att starta LRS måste du förstå dess struktur. URI:n för den genererade SAS-token består av två delar avgränsade med ett frågetecken ( `?` ), som du ser i det här exemplet:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Exempel på U R I för en genererad S A S-token för Log Replay Service." border="false":::

Den första delen, som börjar med till frågetecknet ( ), används för parametern som matas `https://` `?` in som `StorageContainerURI` indata till LRS. Den ger LRS-information om mappen där säkerhetskopierade databasfiler lagras.

Den andra delen, som börjar efter frågetecknet ( ) och går hela vägen till slutet av `?` strängen, är `StorageContainerSasToken` parametern . Det här är den faktiska signerade autentiseringstoken som är giltig under den angivna tiden. Den här delen behöver inte nödvändigtvis börja med `sp=` som i exemplet. Ditt fall kan skilja sig åt.

Kopiera parametrarna på följande sätt:

1. Kopiera den första delen av token, från `https://` hela vägen till frågetecknet ( `?` ). Använd den som `StorageContainerUri` parameter i PowerShell eller Azure CLI för att starta LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Skärmbild som visar kopiering av den första delen av token.":::

2. Kopiera den andra delen av token, med början från frågetecknet ( `?` ) ända till slutet av strängen. Använd den som `StorageContainerSasToken` parameter i PowerShell eller Azure CLI för att starta LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Skärmbild som visar kopiering av den andra delen av token.":::
   
> [!NOTE]
> Ta inte med frågetecknet när du kopierar någon del av token.

### <a name="log-in-to-azure-and-select-a-subscription"></a>Logga in på Azure och välj en prenumeration

Använd följande PowerShell-cmdlet för att logga in på Azure:

```powershell
Login-AzAccount
```

Välj lämplig prenumeration där din hanterade instans finns med hjälp av följande PowerShell-cmdlet:

```powershell
Select-AzSubscription -SubscriptionId <subscription ID>
```

## <a name="start-the-migration"></a>Starta migreringen

Du startar migreringen genom att starta LRS. Du kan starta tjänsten i läget komplettera automatiskt eller kontinuerligt. 

När du använder läget för automatisk komplettering slutförs migreringen automatiskt när den sista av de angivna säkerhetskopieringsfilerna har återställts. Det här alternativet kräver startkommandot för att ange filnamnet för den senaste säkerhetskopieringsfilen. 

När du använder kontinuerligt läge återställer tjänsten kontinuerligt alla nya säkerhetskopieringsfiler som har lagts till. Migreringen slutförs endast vid den manuella övergången. 

### <a name="start-lrs-in-autocomplete-mode"></a>Starta LRS i läget för automatisk komplettering

Om du vill starta LRS i läget för automatisk komplettering använder du följande PowerShell- eller Azure CLI-kommandon. Ange namnet på den senaste säkerhetskopian med hjälp av `-LastBackupName` parametern . När du återställer den sista av de angivna säkerhetskopieringsfilerna initierar tjänsten automatiskt en återställning.

Här är ett exempel på hur du startar LRS i läget för automatisk komplettering med hjälp av PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" `
    -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D" `
    -AutoCompleteRestore `
    -LastBackupName "last_backup.bak"
```

Här är ett exempel på hur du startar LRS i läget för automatisk komplettering med hjälp av Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb -a --last-bn "backup.bak"
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

### <a name="start-lrs-in-continuous-mode"></a>Starta LRS i kontinuerligt läge

Här är ett exempel på hur du startar LRS i kontinuerligt läge med hjälp av PowerShell:

```PowerShell
Start-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName" `
    -Collation "SQL_Latin1_General_CP1_CI_AS" -StorageContainerUri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>" `
    -StorageContainerSasToken "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

Här är ett exempel på hur du startar LRS i kontinuerligt läge med hjälp av Azure CLI:

```CLI
az sql midb log-replay start -g mygroup --mi myinstance -n mymanageddb
    --storage-uri "https://<mystorageaccountname>.blob.core.windows.net/<mycontainername>"
    --storage-sas "sv=2019-02-02&ss=b&srt=sco&sp=rl&se=2023-12-02T00:09:14Z&st=2019-11-25T16:09:14Z&spr=https&sig=92kAe4QYmXaht%2Fgjocqwerqwer41s%3D"
```

PowerShell- och CLI-klienter för att starta LRS i kontinuerligt läge är synkrona. Det innebär att klienterna väntar på API-svaret för att rapportera om jobbet har lyckats eller misslyckats. 

Under den här väntetiden returnerar kommandot inte kontrollen till kommandotolken. Om du kör skript för migreringen och du behöver LRS-startkommandot för att omedelbart ge tillbaka kontrollen för att fortsätta med resten av skriptet kan du köra PowerShell som ett bakgrundsjobb med `-AsJob` växeln. Exempel:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

När du startar ett bakgrundsjobb returneras ett jobbobjekt omedelbart, även om jobbet tar en längre tid att slutföra. Du kan fortsätta att arbeta i sessionen utan avbrott medan jobbet körs. Mer information om hur du kör PowerShell som bakgrundsjobb finns i [powershell-dokumentationen för startjobb.](/powershell/module/microsoft.powershell.core/start-job#description)

Om du vill starta ett Azure CLI-kommando i Linux som en bakgrundsprocess använder du et-et-kommandot ( ) i slutet av `&` LRS-startkommandot:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> När du startar LRS stoppas alla systembaserade programkorrigeringar i 47 timmar. Efter det här fönstret stoppas automatiskt LRS av nästa automatiserade programkorrigering. Om det händer kan du inte återuppta migreringen och behöva starta om den från grunden. 

## <a name="monitor-the-migration-progress"></a>Övervaka migreringsförloppet

Om du vill övervaka migreringsförloppet via PowerShell använder du följande kommando:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Om du vill övervaka migreringsförloppet via Azure CLI använder du följande kommando:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Stoppa migreringen

Om du behöver stoppa migreringen använder du följande cmdlets. Om du stoppar migreringen raderas återställningsdatabasen SQL Managed Instance, så det går inte att återuppta migreringen.

Om du vill stoppa migreringsprocessen via PowerShell använder du följande kommando:

```PowerShell
Stop-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Om du vill stoppa migreringsprocessen via Azure CLI använder du följande kommando:

```CLI
az sql midb log-replay stop -g mygroup --mi myinstance -n mymanageddb
```

## <a name="complete-the-migration-continuous-mode"></a>Slutföra migreringen (kontinuerligt läge)

Om du startade LRS i kontinuerligt läge kommer migreringen att slutföras när du har säkerställt att alla säkerhetskopior har återställts. Efter övergången migreras databasen och är redo för läs- och skrivåtkomst.

Använd följande kommando för att slutföra migreringsprocessen i LRS-kontinuerligt läge via PowerShell:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Använd följande kommando för att slutföra migreringsprocessen i LRS-kontinuerligt läge via Azure CLI:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Funktionsbegränsningar

Funktionsbegränsningar för LRS är:
- Databasen som du återställer kan inte användas för skrivskyddade åtkomst under migreringsprocessen.
- System-hanterade programkorrigeringar blockeras i 47 timmar efter att du startar LRS. När tidsperioden har gått ut stoppas LRS vid nästa programuppdatering. Du måste sedan starta om LRS från grunden.
- LRS kräver att databaser på SQL Server säkerhetskopieras med `CHECKSUM` alternativet aktiverat.
- SAS-token som LRS ska använda måste genereras för hela containern Azure Blob Storage och den måste bara ha läs- och listbehörigheter.
- Säkerhetskopierade filer för olika databaser måste placeras i separata mappar på Blob Storage.
- LRS måste startas separat för varje databas som pekar på separata mappar med säkerhetskopierade filer på Blob Storage.
- LRS har stöd för upp till 100 samtidiga återställningsprocesser per enskild hanterad instans.

## <a name="troubleshooting"></a>Felsökning

När du har börjat med LRS använder du cmdleten för övervakning ( `get-azsqlinstancedatabaselogreplay` eller ) för att se status för `az_sql_midb_log_replay_show` åtgärden. Om LRS inte startar efter en stund och du får ett felmeddelande kontrollerar du om det finns de vanligaste problemen:

- Har en befintlig databas SQL Managed Instance samma namn som du försöker migrera från SQL Server? Lös konflikten genom att byta namn på en av databaserna.
- Gjordes säkerhetskopian av databasen SQL Server via `CHECKSUM` alternativet ?
- Är behörigheterna för SAS-tokenA endast läs- och listbehörigheter för LRS?
- Kopierade du SAS-token för LRS efter frågetecknet ( `?` ), med innehåll som börjar så här: `sv=2020-02-10...` ? Â 
- Gäller giltighetstiden för SAS-token för tidsperioden då migreringen startades och slutförds? Det kan finnas matchningsfel på grund av de olika tidszoner som används för SQL Managed Instance och SAS-token. Försök att återskapa SAS-token och utöka tokens giltighet för tidsfönstret före och efter det aktuella datumet.
- Är databasnamnet, resursgruppens namn och namnet på den hanterade instansen rättstavat?
- Om du startade LRS i läget för automatisk komplettering, var ett giltigt filnamn för den senaste säkerhetskopierade filen angiven?

## <a name="next-steps"></a>Nästa steg
- Läs mer om [hur du migrerar SQL Server till SQL Managed Instance](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Läs mer om [skillnaderna mellan SQL Server och SQL Managed Instance](transact-sql-tsql-differences-sql-server.md).
- Läs mer om [metodtips för kostnad och storlek på arbetsbelastningar som migreras till Azure.](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs)
