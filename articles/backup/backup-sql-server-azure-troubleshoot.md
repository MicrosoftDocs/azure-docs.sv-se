---
title: Felsöka SQL Server säkerhets kopiering av databasen
description: Felsöknings information för att säkerhetskopiera SQL Server databaser som körs på virtuella Azure-datorer med Azure Backup.
ms.topic: troubleshooting
ms.date: 06/18/2019
ms.openlocfilehash: d702959be70716f0c2bc85920bdb7aa3e061aff1
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97733951"
---
# <a name="troubleshoot-sql-server-database-backup-by-using-azure-backup"></a>Felsöka SQL Server säkerhets kopiering av databasen med Azure Backup

Den här artikeln innehåller felsöknings information för SQL Server databaser som körs på virtuella Azure-datorer.

Mer information om säkerhets kopierings processen och begränsningar finns i [om SQL Server säkerhets kopiering på virtuella Azure-datorer](sql-support-matrix.md#feature-considerations-and-limitations).

## <a name="sql-server-permissions"></a>SQL Server behörigheter

Om du vill konfigurera skydd för en SQL Server-databas på en virtuell dator måste du installera **AzureBackupWindowsWorkload** -tillägget på den virtuella datorn. Om du får felet **UserErrorSQLNoSysadminMembership** innebär det att din SQL Server-instans inte har de säkerhets kopierings behörigheter som krävs. Följ stegen i [Ange VM-behörigheter](backup-azure-sql-database.md#set-vm-permissions)för att åtgärda felet.

## <a name="troubleshoot-discover-and-configure-issues"></a>Felsöka problem med identifiering och konfigurering

När du har skapat och konfigurerat ett Recovery Services-valv, är det en två stegs process att identifiera databaser och konfigurera säkerhets kopiering.<br>

![Säkerhets kopierings mål – SQL Server i Azure VM](./media/backup-azure-sql-database/sql.png)

Om den virtuella SQL-datorn och dess instanser inte visas i **identifierings-databaser i virtuella datorer** och **Konfigurera säkerhets kopiering** (se avbildningen ovan) i säkerhets kopierings konfigurationen kontrollerar du att:

### <a name="step-1-discovery-dbs-in-vms"></a>Steg 1: identifiera databaser i virtuella datorer

- Om den virtuella datorn inte visas i listan över identifierade virtuella datorer och inte har registrerats för SQL backup i ett annat valv följer du stegen för [identifierings SQL Server säkerhets kopiering](./backup-sql-server-database-azure-vms.md#discover-sql-server-databases) .

### <a name="step-2-configure-backup"></a>Steg 2: Konfigurera säkerhets kopiering

- Om valvet där den virtuella SQL-datorn är registrerad i samma valv som används för att skydda databaserna följer du anvisningarna för att [Konfigurera säkerhets kopiering](./backup-sql-server-database-azure-vms.md#configure-backup) .

Om den virtuella SQL-datorn måste registreras i det nya valvet måste den avregistreras från det gamla valvet.  Avregistreringen av en virtuell SQL-dator från valvet kräver att alla skyddade data källor stoppas och att du kan ta bort säkerhetskopierade data. Att ta bort säkerhetskopierade data är en destruktiv åtgärd.  När du har granskat och vidtagit alla försiktighets åtgärder för att avregistrera den virtuella SQL-datorn registrerar du sedan samma virtuella dator med ett nytt valv och försöker säkerhetskopiera igen.

## <a name="troubleshoot-backup-and-recovery-issues"></a>Felsök problem med säkerhets kopiering och återställning  

Ibland kan slumpmässiga problem inträffa i säkerhets kopierings-och återställnings åtgärder, eller så kan dessa åtgärder fastna. Detta kan bero på antivirus program på den virtuella datorn. Som bästa praxis rekommenderar vi följande steg:

1. Undanta följande mappar från Antivirus genomsökning:

    `C:\Program Files\Azure Workload Backup` `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.WorkloadBackup.AzureBackupWindowsWorkload`

    Ersätt `C:\` med bokstaven för din *systemen het*.

1. Undanta följande tre processer som körs i en virtuell dator från Antivirus genomsökning:

    - IaasWLPluginSvc.exe
    - IaaSWorkloadCoordinatorService.exe
    - TriggerExtensionJob.exe

1. SQL innehåller också några rikt linjer för att arbeta med antivirus program. Mer information finns i [den här artikeln](https://support.microsoft.com/help/309422/choosing-antivirus-software-for-computers-that-run-sql-server) .

## <a name="faulty-instance-in-a-vm-with-multiple-sql-server-instances"></a>Felaktig instans i en virtuell dator med flera SQL Server instanser

Du kan bara återställa till en virtuell SQL-dator om alla SQL-instanser som körs på den virtuella datorn rapporteras som felfria. Om en eller flera instanser är "felaktiga" visas inte den virtuella datorn som ett återställnings mål. Det kan därför vara en möjlig orsak till att en virtuell dator med flera instanser inte visas i list rutan "Server" under återställnings åtgärden.

Du kan verifiera "säkerhets kopierings beredskap" för alla SQL-instanser på den virtuella datorn under **Konfigurera säkerhets kopiering**:

![Verifiera beredskap för säkerhets kopiering](./media/backup-sql-server-azure-troubleshoot/backup-readiness.png)

Gör så här om du vill utlösa en återställning av de felfria SQL-instanserna:

1. Logga in på den virtuella SQL-datorn och gå till `C:\Program Files\Azure Workload Backup\bin` .
1. Skapa en JSON-fil med namnet `ExtensionSettingsOverrides.json` (om den inte redan finns). Om filen redan finns på den virtuella datorn fortsätter du att använda den.
1. Lägg till följande innehåll i JSON-filen och spara filen:

    ```json
    {
                  "<ExistingKey1>":"<ExistingValue1>",
                    …………………………………………………… ,
              "whitelistedInstancesForInquiry": "FaultyInstance_1,FaultyInstance_2"
            }
            
            Sample content:        
            { 
              "whitelistedInstancesForInquiry": "CRPPA,CRPPB "
            }

    ```

1. Utlös databaser-åtgärden för att **identifiera** på den påverkade servern från den Azure Portal (samma plats där backup-beredskap kan ses). Den virtuella datorn kommer att visas som mål för återställnings åtgärder.

    ![Identifiera databaser](./media/backup-sql-server-azure-troubleshoot/rediscover-dbs.png)

1. Ta bort *whitelistedInstancesForInquiry* -posten från ExtensionSettingsOverrides.jspå filen när återställnings åtgärden har slutförts.

## <a name="error-messages"></a>Felmeddelanden

### <a name="backup-type-unsupported"></a>Säkerhets kopierings typen stöds inte

| Allvarlighetsgrad | Beskrivning | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|---|
| Varning | De aktuella inställningarna för den här databasen stöder inte vissa säkerhets kopierings typer som finns i den tillhör ande principen. | <li>Endast en fullständig databas säkerhets kopierings åtgärd kan utföras på huvud databasen. Det går inte att säkerhetskopiera differentiell säkerhets kopiering och transaktions logg. </li> <li>Alla databaser i den enkla återställnings modellen tillåter inte säkerhets kopiering av transaktions loggar.</li> | Ändra databas inställningarna så att alla säkerhets kopierings typer i principen stöds. Eller ändra den aktuella principen så att endast de säkerhets kopierings typer som stöds tas med. Annars kommer de säkerhets kopierings typer som inte stöds att hoppas över under schemalagd säkerhets kopiering, eller så kommer säkerhets kopieringen inte att kunna utföra säkerhets kopiering på begäran.

### <a name="usererrorsqlpodoesnotsupportbackuptype"></a>UserErrorSQLPODoesNotSupportBackupType

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den här SQL-databasen stöder inte den begärda säkerhetskopieringstypen. | Inträffar när databas återställnings modellen inte tillåter den begärda säkerhets kopierings typen. Felet kan inträffa i följande situationer: <br/><ul><li>En databas som använder en enkel återställnings modell tillåter inte logg säkerhets kopiering.</li><li>Differentiella och loggade säkerhets kopior är inte tillåtna för en huvud databas.</li></ul>Mer information finns i dokumentationen för [SQL Server återställnings modeller](/sql/relational-databases/backup-restore/recovery-models-sql-server) . | Om logg säkerhets kopieringen Miss lyckas för databasen i den enkla återställnings modellen kan du prova något av följande alternativ:<ul><li>Om databasen är i enkelt återställnings läge inaktiverar du logg säkerhets kopior.</li><li>Använd [SQL Server-dokumentationen](/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server) om du vill ändra databas återställnings modellen till fullständig eller Mass utloggning. </li><li> Om du inte vill ändra återställnings modellen och du har en standard princip för att säkerhetskopiera flera databaser som inte kan ändras ignorerar du felet. Dina fullständiga och differentiella säkerhets kopieringar fungerar per schema. Logg säkerhets kopiorna hoppas över, vilket förväntas i det här fallet.</li></ul>Om det är en huvud databas och du har konfigurerat differentiell eller logg säkerhets kopiering, använder du något av följande steg:<ul><li>Använd portalen för att ändra schemat för säkerhets kopierings policyn för Master-databasen till full.</li><li>Om du har en standard princip för att säkerhetskopiera flera databaser som inte kan ändras ignorerar du felet. Den fullständiga säkerhets kopieringen fungerar per schema. Differentiella eller logga säkerhets kopieringar sker inte, vilket förväntas i det här fallet.</li></ul> |
| Åtgärden avbröts eftersom en motstridig åtgärd redan körs på samma databas. | Se [blogg inlägget om begränsningar för säkerhets kopiering och återställning](https://deep.data.blog/2008/12/30/concurrency-of-full-differential-and-log-backups-on-the-same-database/) som körs samtidigt.| [Använd SQL Server Management Studio (SSMS) för att övervaka säkerhets kopierings jobben](manage-monitor-sql-database-backup.md). När den motstridiga åtgärden Miss lyckas startar du om åtgärden.|

### <a name="usererrorsqlpodoesnotexist"></a>UserErrorSQLPODoesNotExist

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| SQL-databasen finns inte. | Databasen har antingen tagits bort eller bytt namn. | Kontrol lera om databasen har tagits bort av misstag eller fått ett nytt namn.<br/><br/> Om databasen har tagits bort av misstag, om du vill fortsätta med säkerhets kopieringarna återställer du databasen till den ursprungliga platsen.<br/><br/> Om du har tagit bort databasen och inte behöver framtida säkerhets kopieringar väljer du **stoppa säkerhets kopiering** med **Behåll säkerhets kopierings data** eller **ta bort säkerhetskopierade data** i Recovery Services valvet. Mer information finns i [Hantera och övervaka säkerhetskopierade SQL Server-databaser](manage-monitor-sql-database-backup.md).

### <a name="usererrorsqllsnvalidationfailure"></a>UserErrorSQLLSNValidationFailure

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggkedjan är bruten. | Databasen eller den virtuella datorn säkerhets kopie ras via en annan säkerhets kopierings lösning som trunkerar logg kedjan.|<ul><li>Kontrol lera om någon annan lösning för säkerhets kopiering eller skript används. Om så är fallet, stoppa den andra säkerhets kopierings lösningen. </li><li>Om säkerhets kopieringen var en säkerhets kopiering på begäran, Utlös en fullständig säkerhets kopiering för att starta en ny logg kedja. För schemalagda säkerhets kopieringar behövs ingen åtgärd eftersom Azure Backups tjänsten automatiskt aktiverar en fullständig säkerhets kopiering för att åtgärda problemet.</li>|

### <a name="usererroropeningsqlconnection"></a>UserErrorOpeningSQLConnection

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup kan inte ansluta till SQL-instansen. | Azure Backup kan inte ansluta till SQL Server-instansen. | Använd den ytterligare informationen på menyn Azure Portal fel för att begränsa rotor saken. Åtgärda problemet genom att läsa fel [sökning av SQL-säkerhetskopiering](/sql/database-engine/configure-windows/troubleshoot-connecting-to-the-sql-server-database-engine) .<br/><ul><li>Om standard-SQL-inställningarna inte tillåter fjärr anslutningar ändrar du inställningarna. I följande artiklar finns information om hur du ändrar inställningarna:<ul><li>[MSSQLSERVER_-1](/sql/relational-databases/errors-events/mssqlserver-1-database-engine-error)</li><li>[MSSQLSERVER_2](/sql/relational-databases/errors-events/mssqlserver-2-database-engine-error)</li><li>[MSSQLSERVER_53](/sql/relational-databases/errors-events/mssqlserver-53-database-engine-error)</li></ul></li></ul><ul><li>Om det finns inloggnings problem kan du använda dessa länkar för att åtgärda dem:<ul><li>[MSSQLSERVER_18456](/sql/relational-databases/errors-events/mssqlserver-18456-database-engine-error)</li><li>[MSSQLSERVER_18452](/sql/relational-databases/errors-events/mssqlserver-18452-database-engine-error)</li></ul></li></ul> |

### <a name="usererrorparentfullbackupmissing"></a>UserErrorParentFullBackupMissing

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den första fullständiga säkerhets kopieringen saknas för den här data källan. | Fullständig säkerhets kopiering saknas för databasen. Logg-och differentiella säkerhets kopieringar är överordnade till en fullständig säkerhets kopia, så se till att ta fullständiga säkerhets kopior innan du utlöser differentiella eller loggar säkerhets kopior. | Utlös en fullständig säkerhets kopiering på begäran.   |

### <a name="usererrorbackupfailedastransactionlogisfull"></a>UserErrorBackupFailedAsTransactionLogIsFull

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att säkerhetskopiera eftersom transaktions loggen för data källan är full. | Databasens transaktions logg utrymme är full. | Information om hur du löser det här problemet finns i [SQL Server-dokumentationen](/sql/relational-databases/errors-events/mssqlserver-9002-database-engine-error). |

### <a name="usererrorcannotrestoreexistingdbwithoutforceoverwrite"></a>UserErrorCannotRestoreExistingDBWithoutForceOverwrite

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det finns redan en databas med samma namn på mål platsen | Mål återställnings målet har redan en databas med samma namn.  | <ul><li>Ändra namnet på mål databasen.</li><li>Eller Använd alternativet framtvinga överskrivning på sidan Återställ.</li> |

### <a name="usererrorrestorefaileddatabasecannotbeofflined"></a>UserErrorRestoreFailedDatabaseCannotBeOfflined

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det gick inte att återställa eftersom databasen inte kunde försättas offline. | När du utför en återställning måste mål databasen försättas i offlineläge. Azure Backup kan inte ta dessa data offline. | Använd den ytterligare informationen på menyn Azure Portal fel för att begränsa rotor saken. Mer information finns i [SQL Server-dokumentationen](/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms). |

### <a name="wlextgenericiofaultusererror"></a>WlExtGenericIOFaultUserError

|Felmeddelande |Möjliga orsaker  |Rekommenderad åtgärd  |
|---------|---------|---------|
|Ett indata/utdata-fel uppstod under åtgärden. Kontrol lera om det finns vanliga IO-fel på den virtuella datorn.   |   Åtkomst behörighet eller utrymmes begränsningar på målet.       |  Sök efter vanliga IO-fel på den virtuella datorn. Kontrol lera att mål enheten/nätverks resursen på datorn: <li> har Läs-/Skriv behörighet för kontot NT instans\system på datorn. <li> har tillräckligt med utrymme för att åtgärden ska kunna slutföras.<br> Mer information finns i [återställa som filer](restore-sql-database-azure-vm.md#restore-as-files).
       |

### <a name="usererrorcannotfindservercertificatewiththumbprint"></a>UserErrorCannotFindServerCertificateWithThumbprint

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Det går inte att hitta Server certifikatet med tumavtryck på målet. | Huvud databasen på mål instansen har inte något giltigt krypterings-tumavtryck. | Importera det giltiga tumavtrycket för certifikatet som används på käll instansen till mål instansen. |

### <a name="usererrorrestorenotpossiblebecauselogbackupcontainsbulkloggedchanges"></a>UserErrorRestoreNotPossibleBecauseLogBackupContainsBulkLoggedChanges

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Loggsäkerhetskopian för återställning innehåller massloggade ändringar. Det kan inte användas för att stoppa vid en godtycklig tidpunkt enligt SQL-rikt linjerna. | När en databas är i bulk-loggat återställnings läge går det inte att återställa data mellan en bulk-loggad transaktion och nästa logg transaktion. | Välj en annan tidpunkt för återställning. [Läs mer](/sql/relational-databases/backup-restore/recovery-models-sql-server).

### <a name="fabricsvcbackuppreferencecheckfailedusererror"></a>FabricSvcBackupPreferenceCheckFailedUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Säkerhetskopieringsinställningen för SQL AlwaysOn-tillgänglighetsgruppen uppfylls inte eftersom vissa noder i tillgänglighetsgruppen inte är registrerade. | Noder som krävs för att utföra säkerhets kopieringar är inte registrerade eller går inte att komma åt. | <ul><li>Se till att alla noder som krävs för att utföra säkerhets kopiering av databasen är registrerade och felfria och försök sedan igen.</li><li>Ändra inställningarna för säkerhets kopiering för SQL Server Always on-tillgänglighetsgrupper.</li></ul> |

### <a name="vmnotinrunningstateusererror"></a>VMNotInRunningStateUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Den virtuella SQL Server-datorn stängs av och är inte tillgänglig för Azure Backup-tjänsten. | Den virtuella datorn stängs av. | Se till att SQL Server-instansen körs. |

### <a name="guestagentstatusunavailableusererror"></a>GuestAgentStatusUnavailableUserError

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Azure Backup tjänsten använder Azures gästa Gent för virtuella datorer för säkerhets kopiering men gäst agenten är inte tillgänglig på mål servern. | Gäst agenten är inte aktive rad eller är skadad. | [Installera gäst agenten för den virtuella datorn](../virtual-machines/extensions/agent-windows.md) manuellt. |

### <a name="autoprotectioncancelledornotvalid"></a>AutoProtectionCancelledOrNotValid

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
| Automatiskt skydds avsikt har antingen tagits bort eller är inte mer giltigt. | När du aktiverar automatiskt skydd på en SQL Server instans måste du **Konfigurera säkerhets kopierings** jobb för alla databaser i den instansen. Om du inaktiverar automatiskt skydd medan jobben körs, avbryts **pågående** jobb med den här felkoden. | Aktivera automatiskt skydd en gång om du vill skydda alla återstående databaser. |

### <a name="clouddosabsolutelimitreached"></a>CloudDosAbsoluteLimitReached

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
Åtgärden blockeras eftersom du har nått gränsen för antalet tillåtna åtgärder inom 24 timmar. | När du har nått den högsta tillåtna gränsen för en åtgärd i ett intervall på 24 timmar visas det här felet. <br> Exempel: om du har nått gränsen för antalet konfigurations säkerhets kopierings jobb som kan utlösas per dag och du försöker konfigurera säkerhets kopiering för ett nytt objekt, visas det här felet. | Vanligt vis åtgärdar du problemet igen efter 24 timmar. Men om problemet kvarstår kan du kontakta Microsoft-supporten om du behöver hjälp.

### <a name="clouddosabsolutelimitreachedwithretry"></a>CloudDosAbsoluteLimitReachedWithRetry

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
Åtgärden blockeras eftersom valvet har uppnått Max gränsen för sådana åtgärder som tillåts inom ett intervall på 24 timmar. | När du har nått den högsta tillåtna gränsen för en åtgärd i ett intervall på 24 timmar visas det här felet. Det här felet visas vanligt vis när det finns åtgärder i skala, till exempel ändra princip eller automatiskt skydd. Till skillnad från CloudDosAbsoluteLimitReached är det inte mycket du kan göra för att lösa det här läget. Azure Backups tjänsten försöker faktiskt utföra åtgärderna internt för alla objekt i fråga.<br> Exempel: om du har ett stort antal data källor som skyddas med en princip och du försöker ändra den principen, utlöses konfigurationen av skydds jobb för vart och ett av de skyddade objekten och ibland kan det överskrida den maximala gräns som tillåts för sådana åtgärder per dag.| Azure Backup tjänsten gör om åtgärden automatiskt efter 24 timmar.

### <a name="usererrorvminternetconnectivityissue"></a>UserErrorVMInternetConnectivityIssue

| Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
|---|---|---|
Den virtuella datorn kan inte kontakta Azure Backup tjänsten på grund av problem med Internet anslutningen. | Den virtuella datorn behöver utgående anslutning till Azure Backup tjänst, Azure Storage eller Azure Active Directory tjänster.| – Om du använder NSG för att begränsa anslutningen bör du använda *AzureBackup* -tjänst tag gen för att tillåta utgående åtkomst till Azure Backup-tjänsten och på samma sätt som Azure AD-tjänsten (*AzureActiveDirectory*) och Azure Storage-tjänster (*lagring*). Följ dessa [steg](./backup-sql-server-database-azure-vms.md#nsg-tags) om du vill bevilja åtkomst.<br>– Kontrol lera att DNS löser Azure-slutpunkter.<br>-Kontrol lera om den virtuella datorn ligger bakom en belastningsutjämnare som blockerar Internet åtkomst. Genom att tilldela den offentliga IP-adressen till de virtuella datorerna fungerar identifieringen.<br>– Kontrol lera att det inte finns någon brand vägg/Antivirus/proxy som blockerar anrop till de tre mål tjänsterna ovan.

## <a name="re-registration-failures"></a>Försök att registrera igen

Kontrol lera om det finns ett eller flera av följande symptom innan du utlöser omregistrerings åtgärden:

- Alla åtgärder (till exempel säkerhets kopiering, återställning och konfigurations säkerhets kopiering) kan inte utföras på den virtuella datorn med någon av följande felkoder: **WorkloadExtensionNotReachable**, **UserErrorWorkloadExtensionNotInstalled**, **WorkloadExtensionNotPresent**, **WorkloadExtensionDidntDequeueMsg**.
- Om **säkerhets kopierings status** fältet för det säkerhetskopierade objektet **visas kan du** utesluta alla andra orsaker som kan resultera i samma status:

  - Saknar behörighet att utföra säkerhetskopierade åtgärder på den virtuella datorn.
  - Stäng av den virtuella datorn så att säkerhets kopieringen inte kan genomföras.
  - Nätverks problem.

   ![registrerar om virtuell dator](./media/backup-azure-sql-database/re-register-vm.png)

- Om det finns en Always on-tillgänglighets grupp startade säkerhets kopieringen inte när du ändrade inställningarna för säkerhets kopieringen eller efter en redundansväxling.

Dessa symtom kan uppstå på grund av en eller flera av följande orsaker:

- Ett tillägg har tagits bort eller avinstallerats från portalen.
- Ett tillägg avinstallerades från **kontroll panelen** på den virtuella datorn under **Avinstallera eller ändra ett program**.
- Den virtuella datorn återställdes i tid genom återställning av disk på plats.
- Den virtuella datorn stängdes under en längre period, så tilläggs konfigurationen på den har upphört att gälla.
- Den virtuella datorn har tagits bort och en annan virtuell dator har skapats med samma namn och i samma resurs grupp som den borttagna virtuella datorn.
- En av noderna i tillgänglighets gruppen fick inte den fullständiga konfigurationen för säkerhets kopiering. Detta kan inträffa när tillgänglighets gruppen är registrerad på valvet eller när en ny nod läggs till.

I föregående scenarier rekommenderar vi att du utlöser en ny registrering på den virtuella datorn. [Här](./backup-azure-sql-automation.md#enable-backup) finns instruktioner för hur du utför den här uppgiften i PowerShell.

## <a name="size-limit-for-files"></a>Storleks gräns för filer

Den totala sträng storleken för filer beror inte bara på antalet filer, utan även på deras namn och sökvägar. Hämta det logiska fil namnet och den fysiska sökvägen för varje databas fil. Du kan använda SQL-fr åga:

```sql
SELECT mf.name AS LogicalName, Physical_Name AS Location FROM sys.master_files mf
               INNER JOIN sys.databases db ON db.database_id = mf.database_id
               WHERE db.name = N'<Database Name>'"
```

Ändra nu i följande format:

```json
[{"path":"<Location>","logicalName":"<LogicalName>","isDir":false},{"path":"<Location>","logicalName":"<LogicalName>","isDir":false}]}
```

Här är ett exempel:

```json
[{"path":"F:\\Data\\TestDB12.mdf","logicalName":"TestDB12","isDir":false},{"path":"F:\\Log\\TestDB12_log.ldf","logicalName":"TestDB12_log","isDir":false}]}
```

Om innehålls sträng storleken överskrider 20 000 byte, lagras databasfilerna på olika sätt. Under återställningen kan du inte ange sökvägen till mål filen för återställning. Filerna kommer att återställas till standard-SQL-sökvägen som tillhandahålls av SQL Server.

### <a name="override-the-default-target-restore-file-path"></a>Åsidosätt fil Sök vägen för standard målets återställning

Du kan åsidosätta sökvägen till mål återställnings filen under återställnings åtgärden genom att placera en JSON-fil som innehåller mappningen av databas filen till mål återställnings Sök vägen. Skapa en `database_name.json` fil och placera den på platsen `C:\Program Files\Azure Workload Backup\bin\plugins\SQL*` .

Filens innehåll ska ha följande format:

```json
[
  {
    "Path": "<Restore_Path>",
    "LogicalName": "<LogicalName>",
    "IsDir": "false"
  },
  {
    "Path": "<Restore_Path>",
    "LogicalName": "LogicalName",
    "IsDir": "false"
  },  
]
```

Här är ett exempel:

```json
[
  {
   "Path": "F:\\Data\\testdb2_1546408741449456.mdf",
   "LogicalName": "testdb7",
   "IsDir": "false"
  },
  {
    "Path": "F:\\Log\\testdb2_log_1546408741449456.ldf",
    "LogicalName": "testdb7_log",
    "IsDir": "false"
  },  
]
```

I föregående innehåll kan du hämta databas filens logiska namn genom att använda följande SQL-fr åga:

```sql
SELECT mf.name AS LogicalName FROM sys.master_files mf
                INNER JOIN sys.databases db ON db.database_id = mf.database_id
                WHERE db.name = N'<Database Name>'"
```

Den här filen bör placeras innan du utlöser återställnings åtgärden.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Backup för SQL Server virtuella datorer (offentlig för hands version) finns i [Azure Backup för virtuella SQL-datorer](../azure-sql/virtual-machines/windows/backup-restore.md#azbackup).
