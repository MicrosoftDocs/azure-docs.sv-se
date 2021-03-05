---
title: Migrera databaser till SQL-hanterad instans med hjälp av logg återuppspelnings tjänsten
description: Lär dig hur du migrerar databaser från SQL Server till SQL-hanterad instans med hjälp av tjänsten logga in Replay
services: sql-database
ms.service: sql-managed-instance
ms.custom: seo-lt-2019, sqldbrb=1
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: sstein
ms.date: 03/01/2021
ms.openlocfilehash: 0bc00aea67fa2f71599ee62e657e1ca1b0627681
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199857"
---
# <a name="migrate-databases-from-sql-server-to-sql-managed-instance-by-using-log-replay-service-preview"></a>Migrera databaser från SQL Server till SQL-hanterad instans med hjälp av tjänsten logga in Replay (för hands version)
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

I den här artikeln förklaras hur du manuellt konfigurerar migrering av databasen från SQL Server 2008-2019 till Azure SQL-hanterad instans med hjälp av tjänsten logg repetition (LRS), för närvarande i offentlig för hands version. LRS är en moln tjänst som är aktive rad för SQL-hanterad instans och som baseras på SQL Server logg leverans teknik. 

[Azure Database migration service](/azure/dms/tutorial-sql-server-to-managed-instance) och LRS använder samma underliggande teknik för migrering och samma API: er. Genom att släppa LRS aktiverar vi ytterligare komplexa anpassade migreringar och hybrid arkitektur mellan lokala SQL Server och SQL-hanterad instans.

## <a name="when-to-use-log-replay-service"></a>När du ska använda logg tjänsten för repetition

Om du inte kan använda Azure Database Migration Service för migrering kan du använda LRS direkt med PowerShell, Azure CLI-cmdletar eller API: er för att manuellt bygga och dirigera databas migreringar till SQL-hanterad instans. 

Du kan överväga att använda LRS i följande fall:
- Du behöver mer kontroll för ditt Database migration-projekt.
- Det finns ingen tolerans för stillestånds tid vid migrering start punkt.
- Den körbara filen Database Migration Service kan inte installeras i din miljö.
- Den körbara filen Database Migration Service inte har fil åtkomst till databas säkerhets kopior.
- Ingen åtkomst till värd operativ systemet är tillgängligt eller så finns det inga administratörs privilegier.
- Du kan inte öppna nätverks portar från din miljö till Azure.
- Säkerhets kopior lagras direkt till Azure Blob Storage via `TO URL` alternativet.
- Du måste använda differentiella säkerhets kopior.

> [!NOTE]
> Vi rekommenderar att du automatiserar migreringen av databaser från SQL Server till SQL-hanterad instans med hjälp av Database Migration Service. Den här tjänsten använder samma moln tjänst för LRS i Server delen, med logg överföring i `NORECOVERY` läget. Överväg att manuellt använda LRS för att dirigera migreringar när Database Migration Service inte har fullt stöd för dina scenarier.

## <a name="how-it-works"></a>Så här fungerar det

Att skapa en anpassad lösning genom att använda LRS för att migrera databaser till molnet kräver flera Orchestration-steg, som du ser i diagrammet och tabellen senare i det här avsnittet.

Migreringen består av att göra fullständiga databas säkerhets kopieringar på SQL Server med `CHECKSUM` aktiverade och kopiera säkerhetskopieringsfiler till Azure Blob Storage. LRS används för att återställa säkerhetskopierade filer från Blob Storage till SQL-hanterad instans. Blob Storage mellanliggande lagring mellan SQL Server och SQL-hanterad instans.

LRS övervakar Blob Storage för eventuella nya differentiella eller logg säkerhets kopior som lagts till när den fullständiga säkerhets kopieringen har återställts. LRS återställer sedan dessa nya filer automatiskt. Du kan använda tjänsten för att övervaka förloppet för de säkerhets kopierings filer som återställs på SQL-hanterad instans och du kan stoppa processen vid behov.

LRS kräver inte någon speciell namngivnings konvention för säkerhetskopierade filer. Den söker igenom alla filer som placerats på Blob Storage och skapar säkerhets kopierings kedjan från att bara läsa fil huvudena. Databaserna har statusen "återställning" under migreringsprocessen. Databaser återställs i [NORECOVERY](/sql/t-sql/statements/restore-statements-transact-sql#comparison-of-recovery-and-norecovery) -läge så att de inte kan användas för läsning eller skrivning förrän migreringen har slutförts. 

Om du migrerar flera databaser måste du:
 
- Placera säkerhets kopior för varje databas i en separat mapp på Blob Storage.
- Starta LRS separat för varje databas.
- Ange olika sökvägar för att separera Blob Storage mappar. 

Du kan starta LRS i antingen *Autoavsluta* eller *kontinuerligt* läge. När du startar den i läget komplettera automatiskt, kommer migreringen att avslutas automatiskt när den sista av de angivna säkerhetskopieringsfilerna har återställts. När du startar LRS i kontinuerligt läge återställer tjänsten kontinuerligt alla nya säkerhetskopierade filer som läggs till, och migreringen avslutas bara på den manuella start punkt. 

Vi rekommenderar att du klipper ut manuellt efter att den slutgiltiga säkerhets kopieringen log-pilslut har gjorts och visas som återställd på SQL-hanterad instans. Det sista steget i Start punkt gör att databasen är online och tillgänglig för Läs-och skriv användning på SQL-hanterad instans.

När LRS har stoppats, antingen automatiskt genom automatisk komplettering eller manuellt via Start punkt, kan du inte återuppta återställnings processen för en databas som har kopplats online på SQL-hanterad instans. Om du vill återställa ytterligare säkerhetskopierade filer när migreringen har slutförts via Autoavsluta eller start punkt, måste du ta bort databasen. Du måste också återställa hela säkerhets kopierings kedjan från grunden genom att starta om LRS.

:::image type="content" source="./media/log-replay-service-migrate/log-replay-service-conceptual.png" alt-text="Diagram som förklarar stegen för att logga repetitions tjänstens dirigering för SQL-hanterad instans." border="false":::
    
| Åtgärd | Information |
| :----------------------------- | :------------------------- |
| **1. Kopiera säkerhets kopior av databasen från SQL Server till Blob Storage**. | Kopiera fullständiga, differentiella och logga säkerhets kopior från SQL Server till en Blob Storage-behållare med hjälp av [AzCopy](/azure/storage/common/storage-use-azcopy-v10) eller [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/). <br /><br />Använd alla fil namn. LRS kräver inte en speciell fil namngivnings konvention.<br /><br />När du migrerar flera databaser behöver du en separat mapp för varje databas. |
| **2. Starta LRS i molnet**. | Du kan starta om tjänsten med ett val av cmdlet: PowerShell ([Start-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/start-azsqlinstancedatabaselogreplay)) eller Azure CLI ([az_sql_midb_log_replay_start cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_start)). <br /><br /> Starta LRS separat för varje databas som pekar på en mapp för säkerhets kopiering på Blob Storage. <br /><br /> När du har startat tjänsten kommer den att ta säkerhets kopior från Blob Storage behållare och börja återställa dem på SQL-hanterad instans.<br /><br /> Om du har startat LRS i kontinuerligt läge kommer tjänsten att se om det finns nya filer som har överförts till mappen efter att alla inhämtade säkerhets kopior har återställts. Tjänsten tillämpar kontinuerligt loggar som baseras på LSN-kedjan tills den stoppas. |
| **2,1. övervaka åtgärdens förlopp**. | Du kan övervaka förloppet för återställnings åtgärden med ett val av cmdlet: PowerShell ([Get-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/get-azsqlinstancedatabaselogreplay)) eller Azure CLI ([az_sql_midb_log_replay_show cmdlets](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_show)). |
| **2,2. stoppa åtgärden om det behövs**. | Om du behöver stoppa migreringsprocessen kan du välja cmdlet: PowerShell ([Stop-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/stop-azsqlinstancedatabaselogreplay)) eller Azure CLI ([az_sql_midb_log_replay_stop](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_stop)). <br /><br /> Om du stoppar åtgärden tas den databas som du återställer på SQL-hanterad instans bort. När du har stoppat en åtgärd kan du inte återuppta LRS för en databas. Du måste starta om migreringsprocessen från början. |
| **3. Klipp ut till molnet när du är redo**. | Stoppa programmet och arbets belastningen. Ta den senaste säkerhets kopieringen av logg boken och ladda upp den till Azure Blob Storage.<br /><br /> Slutför start punkt genom att initiera en LRS `complete` -åtgärd med ett val av cmdlet: PowerShell ([Complete-azsqlinstancedatabaselogreplay](/powershell/module/az.sql/complete-azsqlinstancedatabaselogreplay)) eller Azure CLI [az_sql_midb_log_replay_complete](/cli/azure/sql/midb/log-replay#az_sql_midb_log_replay_complete). Den här åtgärden stoppar LRS och orsakar att databasen är online för Läs-och skriv användning på SQL-hanterad instans.<br /><br /> Peka kopplings strängen för programmet från SQL Server till SQL-hanterad instans. |

## <a name="requirements-for-getting-started"></a>Krav för att komma igång

### <a name="sql-server-side"></a>SQL Server sida
- SQL Server 2008-2019
- Fullständig säkerhets kopiering av databaser (en eller flera filer)
- Differentiell säkerhets kopiering (en eller flera filer)
- Logg säkerhets kopia (delas inte upp för en transaktions logg fil)
- `CHECKSUM` aktive rad för säkerhets kopiering (obligatorisk)

### <a name="azure-side"></a>Azure-sida
- PowerShell-AZ. SQL-modul version 2.16.0 eller senare ([installeras](https://www.powershellgallery.com/packages/Az.Sql/) eller nås via [Azure Cloud Shell](/azure/cloud-shell/))
- Azure CLI version 2.19.0 eller senare ([installerad](/cli/azure/install-azure-cli))
- Azure Blob Storage-behållare etablerades
- Säkerhets-token för signatur för delad åtkomst (SAS) med Läs-och list behörigheter som har genererats för Blob Storage container

### <a name="migration-of-multiple-databases"></a>Migrering av flera databaser
Du måste placera säkerhetskopierade filer för olika databaser i separata mappar på Blob Storage.

Starta LRS separat för varje databas genom att peka på en lämplig mapp på Blob Storage. LRS har stöd för upp till 100 samtidiga återställnings processer per enskild hanterad instans.

### <a name="azure-rbac-permissions"></a>Azure RBAC-behörigheter
Att köra LRS via de tillhandahållna klienterna kräver någon av följande Azure-roller:
- Rollen prenumerations ägare
- Rollen [hanterad instans deltagare](../../role-based-access-control/built-in-roles.md#sql-managed-instance-contributor)
- Anpassad roll med följande behörighet: `Microsoft.Sql/managedInstances/databases/*`

## <a name="best-practices"></a>Bästa praxis

Vi rekommenderar följande metod tips:
- Kör [Data Migration Assistant](/sql/dma/dma-overview) för att kontrol lera att databaserna är redo att migreras till SQL-hanterad instans. 
- Dela fullständiga och differentiella säkerhets kopieringar i flera filer i stället för att använda en enda fil.
- Aktivera komprimering av säkerhets kopiering.
- Använd Cloud Shell för att köra skript, eftersom det alltid kommer att uppdateras till de senaste cmdletarna som släpps.
- Planera för att slutföra migreringen inom 47 timmar efter att du har startat LRS. Detta är en Respitperiod som förhindrar installation av systemhanterade program varu korrigeringar.

> [!IMPORTANT]
> - Du kan inte använda databasen som återställs via LRS tills migreringen är klar. 
> - LRS stöder inte skrivskyddad åtkomst till databaser under migreringen.
> - När migreringen är klar slutförs migreringsprocessen eftersom LRS inte har stöd för att återuppta återställnings processen.

## <a name="steps-to-execute"></a>Steg att köra

### <a name="make-backups-of-sql-server"></a>Säkerhetskopiera SQL Server

Du kan säkerhetskopiera SQL Server med något av följande alternativ:

- Säkerhetskopiera till den lokala disk lagringen och ladda upp filer till Azure-Blob Storage, om miljön begränsar direkta säkerhets kopieringar till Blob Storage.
- Säkerhetskopiera direkt till Blob Storage med `TO URL` alternativet i T-SQL, om din miljö och dina säkerhets procedurer tillåter det. 

Ange de databaser som du vill migrera till det fullständiga återställnings läget för att tillåta logg säkerhets kopieringar.

```SQL
-- To permit log backups, before the full database backup, modify the database to use the full recovery
USE master
ALTER DATABASE SampleDB
SET RECOVERY FULL
GO
```

Om du vill göra fullständiga, differentiella och logga säkerhets kopior av din databas på lokal lagring, använder du följande exempel-SQL-skript. Se till att `CHECKSUM` alternativet är aktiverat eftersom det är obligatoriskt för LRS.

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

Azure Blob Storage används som mellanlagring för säkerhets kopiering av filer mellan SQL Server-och SQL-hanterade instanser. Följ dessa steg om du vill skapa ett nytt lagrings konto och en BLOB-behållare i lagrings kontot:

1. [Skapa ett lagrings konto](../../storage/common/storage-account-create.md?tabs=azure-portal).
2. [Crete en BLOB-behållare](../../storage/blobs/storage-quickstart-blobs-portal.md) i lagrings kontot.

### <a name="copy-backups-from-sql-server-to-blob-storage"></a>Kopiera säkerhets kopior från SQL Server till Blob Storage

När du migrerar databaser till en hanterad instans med hjälp av LRS kan du använda följande metoder för att överföra säkerhets kopior till Blob Storage:
- Använda SQL Server inbyggd funktion [för säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url)
- Använda [AzCopy](/azure/storage/common/storage-use-azcopy-v10) eller [Azure Storage Explorer](https://azure.microsoft.com/en-us/features/storage-explorer) för att överföra säkerhets kopior till en BLOB-behållare
- Använda Storage Explorer i Azure Portal

### <a name="make-backups-from-sql-server-directly-to-blob-storage"></a>Gör säkerhets kopieringar från SQL Server direkt till Blob Storage
Om företagets och nätverkets principer tillåter det, är ett alternativ att göra säkerhets kopieringar från SQL Server direkt till Blob Storage med alternativet SQL Server inbyggd [säkerhets kopiering till URL](/sql/relational-databases/backup-restore/sql-server-backup-to-url) . Om du kan använda det här alternativet behöver du inte göra säkerhets kopior på den lokala lagringen och överföra dem till Blob Storage.

Som det första steget kräver den här åtgärden att du genererar en SAS-autentiseringstoken för Blob Storage och sedan importerar token till SQL Server. Det andra steget är att göra säkerhets kopior med `TO URL` alternativet i T-SQL. Se till att alla säkerhets kopior görs med `CHEKSUM` alternativet aktiverat.

Som referens gör följande exempel kod säkerhets kopieringar till Blob Storage. Det här exemplet innehåller inte instruktioner om hur du importerar SAS-token. Du hittar detaljerade instruktioner, inklusive hur du skapar och importerar SAS-token till SQL Server, i självstudien [använder du Azure Blob Storage med SQL Server](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage). 

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

Azure Blob Storage används som mellanlagring för säkerhets kopiering av filer mellan SQL Server-och SQL-hanterade instanser. Du måste skapa en SAS-autentiseringstoken, med endast list-och Läs behörighet, för LRS. Token gör att LRS får åtkomst till Blob Storage och använder de säkerhetskopierade filerna för att återställa dem på SQL-hanterad instans. 

Följ de här stegen för att generera token:

1. Öppna Storage Explorer från Azure Portal.
2. Expandera **BLOB-behållare**.
3. Högerklicka på BLOB-behållaren och välj **Hämta signatur för delad åtkomst**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-01.png" alt-text="Skärm bild som visar val för att skapa en S-autentiseringstoken.":::

4. Välj tidsram för förfallo datum för token. Se till att token är giltig under migreringens varaktighet.
5. Välj tidszon för token: UTC eller lokal tid.
    
   > [!IMPORTANT]
   > Tids zonen för token och din hanterade instans kanske inte matchar. Se till att SAS-token har lämplig giltighets tid, och ta tids zoner i beaktande. Ange om möjligt tids zonen till en tidigare och senare tid för det planerade migrerings fönstret.
6. Välj **Läs** -och **list** behörigheter endast.

   > [!IMPORTANT]
   > Välj inte andra behörigheter. Om du gör det startar inte LRS. Detta säkerhets krav är avsiktligt.
7. Välj **Skapa**.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-sas-token-02.png" alt-text="Skärm bild som visar markeringar för en S token, tidszon och behörigheter, tillsammans med knappen Skapa.":::

SAS-autentiseringen genereras med den giltighets tid som du har angett. Du behöver URI-versionen av token, som visas i följande skärm bild.

:::image type="content" source="./media/log-replay-service-migrate/lrs-generated-uri-token.png" alt-text="Skärm bild som visar ett exempel på U R I-versionen av en s-token.":::

### <a name="copy-parameters-from-the-sas-token"></a>Kopiera parametrar från SAS-token

Innan du använder SAS-token för att starta LRS måste du förstå dess struktur. URI: n för den genererade SAS-token består av två delar åtskiljda med ett frågetecken ( `?` ), som visas i det här exemplet:

:::image type="content" source="./media/log-replay-service-migrate/lrs-token-structure.png" alt-text="Exempel U R I för en genererad S-token för logg uppspelnings tjänsten." border="false":::

Den första delen, som börjar med tills frågetecknet `https://` ( `?` ), används för den `StorageContainerURI` parameter som matas in som intill LRS. Den ger LRS information om mappen där säkerhetskopierade databasfiler lagras.

Den andra delen, som börjar efter frågetecknet ( `?` ) och går hela vägen till slutet av strängen, är `StorageContainerSasToken` parametern. Detta är den faktiska signerade autentiseringstoken som är giltig under den angivna tids perioden. Den här delen behöver inte nödvändigt vis börja med `sp=` som visas i exemplet. Ditt ärende kan skilja sig.

Kopiera parametrarna enligt följande:

1. Kopiera den första delen av token och börja från `https://` hela vägen tills frågetecknet ( `?` ). Använd den som `StorageContainerUri` parameter i PowerShell eller Azure CLI för att starta LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-01.png" alt-text="Skärm bild som visar kopiering av den första delen av token.":::

2. Kopiera den andra delen av token, med början från frågetecknet ( `?` ) till slutet av strängen. Använd den som `StorageContainerSasToken` parameter i PowerShell eller Azure CLI för att starta LRS.

   :::image type="content" source="./media/log-replay-service-migrate/lrs-token-uri-copy-part-02.png" alt-text="Skärm bild som visar kopiering av den andra delen av token.":::
   
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

Du startar migreringen genom att starta LRS. Du kan starta tjänsten i antingen Autoavsluta eller kontinuerligt läge. 

När du använder Autoavsluta-läget slutförs migreringen automatiskt när den sista av de angivna säkerhetskopieringsfilerna har återställts. Det här alternativet kräver att Start kommandot anger fil namnet för den senaste säkerhets kopierings filen. 

När du använder kontinuerligt läge återställer tjänsten kontinuerligt alla nya säkerhetskopierade filer som har lagts till. Migreringen kommer endast att slutföras på den manuella start punkt. 

### <a name="start-lrs-in-autocomplete-mode"></a>Starta LRS i Autoavsluta-läge

Om du vill starta LRS i Autoavsluta-läge, använder du följande PowerShell-eller Azure CLI-kommandon. Ange det senaste fil namnet för säkerhets kopian med hjälp av `-LastBackupName` parametern. Vid återställning av de senaste säkerhetskopierade filerna initierar tjänsten automatiskt en start punkt.

Här är ett exempel på hur du startar LRS i Autoavsluta-läge med hjälp av PowerShell:

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

Här är ett exempel på hur du startar LRS i Autoavsluta-läge med hjälp av Azure CLI:

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

PowerShell-och CLI-klienter för att starta LRS i kontinuerligt läge är synkrona. Det innebär att klienterna väntar på API-svar för att rapportera om det lyckas eller om det inte går att starta jobbet. 

Under den här tiden returnerar kommandot inte kontrollen till kommando tolken. Om du har skript för migreringsprocessen och behöver kommandot LRS start för att ge kontrollen direkt för att fortsätta med resten av skriptet kan du köra PowerShell som ett bakgrunds jobb med `-AsJob` växeln. Exempel:

```PowerShell
$lrsjob = Start-AzSqlInstanceDatabaseLogReplay <required parameters> -AsJob
```

När du startar ett bakgrunds jobb returnerar ett Job-objekt omedelbart, även om jobbet tar lång tid att slutföra. Du kan fortsätta att arbeta i sessionen utan avbrott medan jobbet körs. Mer information om hur du kör PowerShell som ett bakgrunds jobb finns i [PowerShell start-Job-](/powershell/module/microsoft.powershell.core/start-job#description) dokumentationen.

På samma sätt kan du starta ett Azure CLI-kommando på Linux som bakgrunds process med hjälp av et-tecknet ( `&` ) i slutet av LRS start kommando:

```CLI
az sql midb log-replay start <required parameters> &
```

> [!IMPORTANT]
> När du har startat LRS stoppas alla systemhanterade program varu korrigeringar i 47 timmar. Efter det här fönstret kommer nästa automatiska program varu korrigering automatiskt att stoppa LRS. Om det händer kan du inte återuppta migreringen och behöva starta om den från grunden. 

## <a name="monitor-the-migration-progress"></a>Övervaka förloppet för migreringen

Använd följande kommando för att övervaka förloppet för migreringen via PowerShell:

```PowerShell
Get-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
    -InstanceName "ManagedInstance01" `
    -Name "ManagedDatabaseName"
```

Använd följande kommando för att övervaka förloppet för migreringen via Azure CLI:

```CLI
az sql midb log-replay show -g mygroup --mi myinstance -n mymanageddb
```

## <a name="stop-the-migration"></a>Stoppa migreringen

Om du behöver stoppa migreringen använder du följande cmdletar. Om du stoppar migreringen tas återställnings databasen på SQL-hanterad instans bort, så det går inte att återuppta migreringen.

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

## <a name="complete-the-migration-continuous-mode"></a>Slutför migreringen (kontinuerligt läge)

Om du startade LRS i kontinuerligt läge, efter att du har kontrollerat att alla säkerhets kopior har återställts, kommer initieringen av start punkt att slutföra migreringen. Efter start punkt migreras databasen och är redo för Läs-och Skriv behörighet.

Använd följande kommando för att slutföra migreringsprocessen i LRS kontinuerligt läge via PowerShell:

```PowerShell
Complete-AzSqlInstanceDatabaseLogReplay -ResourceGroupName "ResourceGroup01" `
-InstanceName "ManagedInstance01" `
-Name "ManagedDatabaseName" `
-LastBackupName "last_backup.bak"
```

Om du vill slutföra migreringsprocessen i LRS kontinuerligt läge via Azure CLI använder du följande kommando:

```CLI
az sql midb log-replay complete -g mygroup --mi myinstance -n mymanageddb --last-backup-name "backup.bak"
```

## <a name="functional-limitations"></a>Funktionella begränsningar

Funktionella begränsningar för LRS är:
- Databasen som du återställer kan inte användas för skrivskyddad åtkomst under migreringsprocessen.
- Systemhanterade program varu korrigeringar blockeras i 47 timmar efter att du har startat LRS. När den här tids perioden har löpt ut kommer nästa program varu uppdatering att stoppa LRS. Sedan måste du starta om LRS från början.
- LRS kräver att databaser på SQL Server säkerhets kopie ras med `CHECKSUM` alternativet aktiverat.
- SAS-token som LRS kommer att använda måste genereras för hela Azure Blob Storage-behållaren och får bara ha läs-och list behörigheter.
- Säkerhetskopierade filer för olika databaser måste placeras i separata mappar på Blob Storage.
- LRS måste startas separat för varje databas som pekar på separata mappar med säkerhetskopierade filer på Blob Storage.
- LRS har stöd för upp till 100 samtidiga återställnings processer per enskild hanterad instans.

## <a name="troubleshooting"></a>Felsökning

När du har startat LRS använder du övervaknings-cmdleten ( `get-azsqlinstancedatabaselogreplay` eller `az_sql_midb_log_replay_show` ) för att se status för åtgärden. Om LRS inte startar efter en stund och du får ett fel meddelande, kontrollerar du de vanligaste problemen:

- Har en befintlig databas på SQL-hanterad instans samma namn som du försöker migrera från SQL Server? Lös konflikten genom att byta namn på en av databaserna.
- Har säkerhets kopian av databasen SQL Server gjorts via `CHECKSUM` alternativet?
- Är behörigheterna bara för SAS-token Read och list for LRS?
- Kopierade SAS-token för LRS efter frågetecknet ( `?` ), med innehåll som startar så här: `sv=2020-02-10...` ? 
- Är den giltighets tid för SAS-token som kan användas för tids perioden för att starta och slutföra migreringen? Det kan finnas Felaktiga matchningar på grund av olika tids zoner som används för SQL-hanterad instans och SAS-token. Försök att återskapa SAS-token och utöka giltighets tiden för tids perioden före och efter det aktuella datumet.
- Är databas namnet, resurs gruppens namn och namnet på den hanterade instansen rätt stavat?
- Var det ett giltigt fil namn för den senaste säkerhets kopian som angavs om du startade LRS i Autoavsluta-läge?

## <a name="next-steps"></a>Nästa steg
- Läs mer om hur [du migrerar SQL Server till SQL-hanterad instans](../migration-guides/managed-instance/sql-server-to-managed-instance-guide.md).
- Läs mer om [skillnader mellan SQL Server och SQL-hanterad instans](transact-sql-tsql-differences-sql-server.md).
- Lär dig mer om [metod tips för kostnads-och storleks arbets belastningar som migrerats till Azure](/azure/cloud-adoption-framework/migrate/azure-best-practices/migrate-best-practices-costs).
