---
title: Säkerhetskopiera MABS-servern
description: Lär dig hur du serverar Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: fbd3d1f2d7cb24c21962dbe5c88acebf73652a04
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107519127"
---
# <a name="back-up-the-mabs-server"></a>Säkerhetskopiera MABS-servern

För att säkerställa att data kan återställas Microsoft Azure Backup Server (MABS) misslyckas, behöver du en strategi för att servera MABS-servern. Om den inte säkerhetskopieras måste du återskapa den manuellt efter ett fel, och diskbaserade återställningspunkter kan inte återställas. Du kan servera MABS-servrar genom att servera MABS-databasen.

## <a name="back-up-the-mabs-database"></a>Back up the MABS database ( Back up the MABS database)

Som en del av din MABS-säkerhetskopieringsstrategi måste du säkerhetskopiera MABS-databasen. MABS-databasen heter DPMDB. Den här databasen innehåller MABS-konfigurationen tillsammans med data om säkerhetskopior av MABS. Vid ett haveri kan du återskapa de flesta funktionerna i en MABS-server med hjälp av en ny säkerhetskopia av databasen. Förutsatt att du kan återställa databasen är bandbaserade säkerhetskopior tillgängliga och de underhåller alla skyddsgruppsinställningar och säkerhetskopieringsscheman. Om MABS-lagringspooldiskarna inte påverkades av avbrottet kan även diskbaserade säkerhetskopior användas efter en återskapning. Du kan säkerhetskopiera databasen med hjälp av flera olika metoder.

|Metod för säkerhetskopiering av databas|Fördelar|Nackdelar|
|--------------------------|--------------|-----------------|
|[Säkerhetskopiera till Azure](#back-up-to-azure)|<li>Enkelt konfigurerad och övervakad i MABS.<li>Flera platser för de säkerhetskopierade databasfilerna.<li>Molnlagring är en robust lösning för haveriberedskap.<li>Mycket säker lagring för databasen.<li>Har stöd för 120 onlineåterställningspunkter.|<li>Kräver Azure-konto och ytterligare MABS-konfiguration. Viss kostnad för lagring med Azure tillkommer.<li> Kräver en version av Windows Server-baserat system som stöds med Azure-agenten för att få åtkomst till MABS-säkerhetskopior som lagras i Azure-säkerhetskopieringsvalvet. Detta kan inte vara en annan MABS-server.<li>Inte ett alternativ om databasen finns lokalt och du vill aktivera sekundärt skydd. <li>Lite extra tid för förberedelser och återställning uppkommer.|
|[Back up the database by backing up the MABS storage pool](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Enkla att konfigurera och övervaka.<li>Säkerhetskopian sparas på MABS-lagringspooldiskarna och är enkel att komma åt lokalt.<li>Schemalagda MABS-säkerhetskopieringar stöder 512 fullständiga snabbsäkerhetskopior. Om du backar upp varje timme har du 21 dagars fullständigt skydd.|<li>Inte ett bra alternativ för haveriberedskap. Det är online och återställning kanske inte fungerar som förväntat om MABS-servern eller lagringspooldisken slutar fungera.<li>Inte ett alternativ om databasen finns lokalt och du vill aktivera sekundärt skydd. <li>Vissa förberedelser och särskilda steg krävs för att få åtkomst till återställningspunkterna om MABS-tjänsten eller -konsolen inte körs eller fungerar.|
|[Säkerhetskopiera med intern SQL Server-säkerhetskopiering till en lokal disk](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Inbyggt i SQL Server.<li>Säkerhetskopian sparas på en lokal disk som är lättillgänglig.<li>Kan schemaläggas att köras så ofta du vill.<li>Helt oberoende av MABS.<li>Du kan schemalägga en rensning av säkerhetskopieringsfilen.|<li>Inte ett bra alternativ för haveriberedskap, såvida inte säkerhetskopiorna kopieras till en fjärrplats.<li>Kräver lokal lagring för säkerhetskopior, vilket kan begränsa kvarhållning och frekvens.|
|[Säkerhetskopiera med inbyggd SQL-säkerhetskopiering och MABS-skydd till en resurs som skyddas av MABS](#back-up-to-a-share-protected-by-mabs)|<li>Övervakas enkelt i MABS.<li>Flera platser för de säkerhetskopierade databasfilerna.<li>Lättillgängligt från alla Windows-datorer i nätverket.<li>Potentiellt den snabbaste återställningsmetoden.|<li>Har endast stöd för 64 återställningspunkter.<li>Inte ett bra alternativ för haveriberedskap. MABS-servern eller MABS-lagringspooldiskfel kan hindra återställningen.<li>Inte ett alternativ om MABS DB finns lokalt och du vill aktivera sekundärt skydd. <li>Vissa extra förberedelser krävs för konfiguration och tester.<li>Viss extra tid för förberedelse och återställning krävs om själva MABS-servern är nere men MABS-lagringspooldiskar fungerar bra.|

- Om du backar upp med hjälp av en MABS-skyddsgrupp rekommenderar vi att du använder en unik skyddsgrupp för databasen.

    > [!NOTE]
    > I återställningssyfte måste MABS-installationen som du vill återställa med MABS-databasen matcha versionen av själva MABS-databasen.  Om den databas som du vill återställa till exempel kommer från en MABS V3 med installation av Samlad uppdatering 1, måste MABS-servern köra samma version med Samlad uppdatering 1. Det innebär att du kan behöva avinstallera och installera om MABS med en kompatibel version innan du återställer databasen.  Om du vill kontrollera databasversionen kan du behöva montera den manuellt till ett temporärt databasnamn och sedan köra en SQL-fråga mot databasen för att kontrollera den senaste samlade uppdateringen som har installerats baserat på den högre och den lägre versionen.

- Följ dessa steg om du vill kontrollera MABS-databasversionen:

    1. Kör frågan genom att öppna SQL Management Studio och sedan ansluta till den SQL-instans som kör MABS-databasen.

    2. Välj MABS-databasen och starta sedan en ny fråga.

    3. Klistra in följande SQL-fråga i frågefönstret och kör den:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Om inget returneras i frågeresultaten, eller om MABS-servern har uppgraderats från tidigare versioner men ingen ny samlad uppdatering har installerats sedan dess, kommer det inte att finnas någon post för större, mindre för en grundläggande installation av MABS. Information om vilka MABS-versioner som är associerade med samlade uppdateringar [finns i Lista över build-nummer för MABS.](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)

### <a name="back-up-to-azure"></a>Säkerhetskopiera till Azure

1. Innan du börjar måste du köra ett skript för att hämta sökvägen till mabs-replikvolymens monteringspunkt så att du vet vilken återställningspunkt som innehåller MABS-säkerhetskopian. Gör detta efter den första replikeringen med Azure Backup. I skriptet ersätter du `dplsqlservername%` med namnet på den SQL Server som är värd för MABS-databasen.

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Kontrollera att du har lösenordet som angavs när Azure Recovery Services-agenten installerades och MABS-servern registrerades i Azure Backup valvet. Du behöver lösenordet för att återställa säkerhetskopian.

2. Skapa ett Azure Backup-valv, ladda ned installationsfilen för Azure Backup Agent och valvautentiseringsuppgifterna. Kör installationsfilen för att installera agenten på MABS-servern och använd valvautentiseringsuppgifterna för att registrera MABS-servern i valvet. [Läs mer](backup-azure-microsoft-azure-backup.md).

3. När valvet har konfigurerats konfigurerar du en MABS-skyddsgrupp som innehåller MABS-databasen. Välj att backa upp den till disk och till Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Återställa MABS-databasen från Azure

Du kan återställa databasen från Azure med valfri MABS-server som är registrerad i Azure Backup valvet enligt följande:

1. I MABS-konsolen väljer du **Återställning Lägg till** extern  >  **MABS**.

2. Ange autentiseringsuppgifterna för valvet (ladda ned från Azure Backup valvet). Observera att autentiseringsuppgifterna bara är giltiga i två dagar.

3. I **Välj extern MABS** för återställning väljer du den MABS-server som du vill återställa databasen för, skriver in krypteringsfrasen och väljer **OK.**

4. Välj den återställningspunkt som du vill använda från listan över tillgängliga punkter. Välj **Rensa extern MABS för** att återgå till den lokala MABS-vyn.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Back up the MABS database to MABS storage pool

> [!NOTE]  
> Det här alternativet gäller för MABS med Modern Backup Storage.

1. I MABS-konsolen väljer du **Skydd**  >  **Skapa skyddsgrupp.**
2. På sidan **Välj typ av skyddsgrupp** väljer du **Servrar.**
3. På sidan **Välj gruppmedlemmar** väljer du **DPM-databas**. Expandera MABS-servern och välj DPMDB.
4. På sidan **Välj dataskyddsmetod väljer** du **Jag vill ha kortvarigt skydd med disk**. Ange alternativ för den kortsiktiga skyddsprincipen.
5. Efter den inledande replikeringen av MABS-databasen kör du följande SQL-skript:

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>Återställa MABS-databas

För att rekonstruera din MABS med samma databas måste du först återställa MABS-databasen och synkronisera den med den nyligen installerade MABS.

#### <a name="use-the-following-steps"></a>Använd följande steg

1. Öppna en administrativ kommandotolk och kör `psexec.exe -s powershell.exe` för att starta ett PowerShell-fönster i systemkontexten.
2. Bestäm platsen där du vill återställa databasen:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Kopiera databasen från den senaste säkerhetskopian

1. Navigera till vhd-repliksökvägen `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Montera **disk0.vhdx som** finns i den med hjälp av `mount-vhd disk0.vhdx` kommandot .
3. När replikens virtuella hårddisk har monterats använder du för att tilldela replikvolymen en enhetsbeteckning med hjälp av det fysiska `mountvol.exe` replik-ID:t från SQL-skriptets utdata. Exempelvis: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Kopiera databasen från en tidigare återställningspunkt

1. Gå till DPMDB-containerkatalogen  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . Du ser flera kataloger med några unika GUID-identifierare under motsvarande återställningspunkter som tagits för MABS DB. Andra kataloger representerar en PIT/återställningspunkt.
2. Gå till valfri VHD-sökväg för PIT, till exempel `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` och montera **disk0.vhdx** som finns i den med hjälp av `mount-vhd disk0.vhdx` kommandot .
3. När replikens virtuella hårddisk har monterats använder du för att tilldela replikvolymen en enhetsbeteckning med hjälp av det fysiska `mountvol.exe` replik-ID:t från SQL-skriptets utdata. Exempelvis: `mountvol X: \?\Volume{}\`

   Alla termer som visas med angularparenteser i ovanstående steg är platshållare. Ersätt dem med lämpliga värden enligt följande:
   - **ReFSVolume –** Åtkomstsökväg från SQL-skriptutdata
   - **MABSServer FQDN** – fullständigt kvalificerat namn på MABS-servern
   - **PhysicalReplicaId** – fysiskt replik-ID från SQL-skriptet ut
   - **PITId –** GUID-identifierare förutom det fysiska replik-ID:t i containerkatalogen.
4. Öppna en annan administrativ kommandotolk och kör `psexec.exe -s cmd.exe` för att starta en kommandotolk i systemkontexten.
5. Ändra katalogen till enheten X: och navigera till platsen för MABS-databasfilerna.
6. Kopiera dem till en plats som är lätt att återställa från. Avsluta fönstret psexec cmd när du har kopierat.
7. Gå till det psexec PowerShell-fönster som öppnades i steg 1, navigera till VHDX-sökvägen och demontera VHDX med hjälp av kommandot `dismount-vhd disk0.vhdx` .
8. När du har installerat om MABS-servern kan du använda den återställda DPMDB:en för att ansluta till MABS-servern genom att köra `DPMSYNC-RESTOREDB` kommandot .
9. Kör `DPMSYNC-SYNC` en gång är `DPMSYNC-RESTOREDB` klart.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Back up the database by backing up the MABS storage pool

> [!NOTE]
> Det här alternativet gäller för MABS med äldre lagring.

Innan du börjar måste du köra ett skript för att hämta sökvägen till mabs-replikvolymens monteringspunkt så att du vet vilken återställningspunkt som innehåller MABS-säkerhetskopian. Gör detta efter den första replikeringen med Azure Backup. I skriptet ersätter du `dplsqlservername%` med namnet på den SQL Server som är värd för MABS-databasen.

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. I MABS-konsolen väljer du **Skydd**  >  **Skapa skyddsgrupp.**

2. Välj **Servrar** på sidan **Välj typ av skyddsgrupp**.

3. På sidan **Välj gruppmedlemmar** väljer du MABS-databasen. Expandera MABS-serverobjektet och välj **DPMDB.**

4. På sidan  **Välj dataskyddsmetod väljer** du **Jag vill ha kortvarigt skydd med disk**. Ange alternativ för den kortsiktiga skyddsprincipen. Vi rekommenderar ett kvarhållningsintervall på två veckor för MABS-databaser.

#### <a name="recover-the-database"></a>Återställa databasen

Om MABS-servern fortfarande fungerar och lagringspoolen är intakt (till exempel problem med MABS-tjänsten eller -konsolen) kopierar du databasen från replikvolymen eller en skuggkopia på följande sätt:

1. Välj från vilken tidpunkt du vill återställa databasen.

    - Om du vill kopiera databasen från den senaste säkerhetskopian som tagits direkt från MABS-replikvolymen använder du **mountvol.exe** för att tilldela replikvolymen en enhetsbeteckning med hjälp av GUID:t från SQL-skriptets utdata. Exempelvis: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Om du vill kopiera databasen från en tidigare återställningspunkt (skuggkopia) måste du lista alla skuggkopior för repliken med hjälp av volymens GUID från SQL-skriptets utdata. Det här kommandot visar skuggkopior för volymen: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Observera skapandetiden och skuggkopie-ID:t som du vill återställa från.

2. Använd sedan **diskshadow.exemontera** skuggkopian till enhetsbeteckningen X som inte används: med hjälp av skuggkopie-ID:t, så att du kan kopiera databasfilerna.

3. Öppna en administrativ kommandotolk och kör för att starta en kommandotolk i systemkontexten, så att du har behörighet att navigera till `psexec.exe -s cmd.exe` replikvolymen (X:) och kopiera filerna.

4. CD till X:-enheten och navigera till platsen för MABS-databasfilerna. Kopiera dem till en plats som är lätt att återställa från. När kopieringen är klar finns fönstret psexec cmd och **kördiskshadow.exe** och ta bort X:-volymen.

5. Nu kan du återställa databasfilerna med hjälp av SQL Management Studio eller genom att **köra DPMSYNC \- RESTOREDB**.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Säkerhetskopiera med intern SQL Server-säkerhetskopiering till en lokal disk

Du kan säkerhetskopiera MABS-databasen till en lokal disk med inbyggd SQL Server säkerhetskopiering, oberoende av MABS.

- Hämta en [översikt](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) över SQL Server-säkerhetskopiering.

- [Lär dig mer](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) om att säkerhetskopiera SQL Server till molnet.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Tillbaka till en resurs som skyddas av MABS

Det här säkerhetskopieringsalternativet använder intern SQL för att säkerhetskopiera MABS-databasen till en resurs, skydda resursen med MABS och använder Windows VSS tidigare versioner för att underlätta återställningen.

### <a name="before-you-start"></a>Innan du börjar

1. På SQL Server en mapp på en enhet med tillräckligt med ledigt utrymme för att rymma en kopia av en säkerhetskopia. Exempel: `C:\MABSBACKUP`.

1. Dela mappen. Du kan till exempel `C:\MABSBACKUP` dela mappen som *DPMBACKUP*.

1. Kopiera och klistra in OSQL-kommandot nedan i Anteckningar och spara det i en fil med namnet `C:\MABSACKUP\bkupdb.cmd` . Kontrollera att det inte finns något .txt-tillägg. Ändra SQL_Instance_name och DPMDB_NAME så att de matchar instansen och DPMDB-namnet som används av MABS-servern.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Använd Anteckningar och öppna **ScriptingConfig.xml** filen som finns under mappen på `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` MABS-servern.

1. Ändra **ScriptingConfig.xml** **DataSourceName= till** enhetsbeteckningen som innehåller mappen/resursen DPMDBBACKUP. Ändra posten PreBackupScript till den fullständiga sökvägen och namnet på **bkupdb.cmd som** sparades i steg 3.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. Spara ändringarna i **ScriptingConfig.xml**.

1. Skydda mappen C:\MABSBACKUP eller resursen med MABS och `\sqlservername\MABSBACKUP` vänta tills den första repliken har skapats. Det bör finnas en **dpmdb.bak** i mappen C:\MABSBACKUP som ett resultat av att försäkerhetskopieringsskriptet körs, som i sin tur kopierades till MABS-repliken.

1. Om du inte aktiverar självbetjäningsåterställning behöver du några ytterligare steg för att dela ut MABSBACKUP-mappen på repliken:

    1. I MABS-konsolen > **Protection** letar du upp MABSBACKUP-datakällan och väljer den. I informationsavsnittet väljer **du Klicka för att visa** information om länken till repliksökvägen och kopierar sökvägen till Anteckningar. Ta bort källsökvägen och behåll målsökvägen. Sökvägen bör se ut ungefär så här: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Skapa en resurs till den sökvägen med resursnamnet **MABSSERVERNAME-DPMDB.** Du kan använda kommandot Net Share nedan från en administrativ kommandotolk.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Konfigurera säkerhetskopieringen

Du kan säkerhetskopiera MABS-databasen på samma sätt som andra SQL Server med hjälp SQL Server intern säkerhetskopiering.

- Hämta en [översikt](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) över SQL Server-säkerhetskopiering.

- [Lär dig mer](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) om att säkerhetskopiera SQL Server till molnet.

### <a name="recover-the-mabs-database"></a>Återställa MABS-databasen

1. Anslut till resursen `\\MABSServer\MABSSERVERNAME-dpmdb` med Utforskaren från valfri Windows-dator.

2. Högerklicka på filen **dpmdb.bak för** att visa egenskaper. På fliken **Tidigare versioner** finns alla säkerhetskopior som du kan välja och kopiera. Det finns också den allra sista säkerhetskopian som fortfarande finns i mappen C:\MABSBACKUP som också är lättillgänglig.

3. Om du behöver flytta en SAN-ansluten MABS-lagringspooldisk till en annan server för att kunna läsa från replikvolymen, eller installera om Windows för att läsa lokalt anslutna diskar, måste du känna till sökvägen till monteringspunkten för MABS-replikvolymen eller volym-GUID i förväg så att du vet vilken volym som innehåller databassäkerhetskopian. Du kan använda SQL-skriptet nedan för att ta fram denna information när som helst efter det inledande skyddet har genomförts, men innan du behöver göra en återställning. Ersätt `%dpmsqlservername%` med namnet på den SQL Server är värd för databasen.

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. Om du behöver återställa efter att ha flyttat MABS-lagringspooldiskarna eller återskapa en MABS-server:

    1. Du har volymens GUID, så om volymen måste monteras på en annan Windows-server eller efter att en MABS-server har återskapats använder du **mountvol.exe** för att tilldela den en enhetsbeteckning med hjälp av volymens GUID från SQL-skriptets utdata: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Dela om MABSBACKUP-mappen på replikvolymen med enhetsbeteckningen och delen av repliksökvägen som representerar mappstrukturen.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Anslut till resursen `\\SERVERNAME\MABSSERVERNAME-dpmdb` med Utforskaren från valfri Windows-dator.

    4. Högerklicka på filen **dpmdb.bak** för att visa egenskaperna. På fliken **Tidigare versioner** finns alla säkerhetskopior som du kan välja och kopiera.

## <a name="using-dpmsync"></a>Använda DPMSync

**DpmSync** är ett kommandoradsverktyg som gör att du kan synkronisera MABS-databasen med tillståndet för diskarna i lagringspoolen och med de installerade skyddsagenterna. DpmSync återställer MABS-databasen, synkroniserar MABS-databasen med replikerna i lagringspoolen, återställer rapportdatabasen och omallokerar saknade repliker.

### <a name="parameters"></a>Parametrar

| Parameter      | Beskrivning    |
|----------------|-----------------------------|
| **-RestoreDb**                       | Återställer en MABS-databas från en angiven plats.|
| **-Sync**                            | Synkroniserar återställda databaser. Du måste köra **DpmSync – Sync när** du har återställt databaserna. När du har **kört DpmSync –Sync** kan vissa repliker fortfarande markeras som saknade. |
| **-DbLoc-plats**                 | Identifierar platsen för säkerhetskopian av MABS-databasen.|
| **-InstanceName** <br/>*server\instance*     | Instans som DPMDB måste återställas till.|
| **-ReallocateReplica**         | Omallokerar alla saknade replikvolymer utan synkronisering. |
| **-DataCopied**                      | Anger att du har slutfört inläsningen av data till de nyligen allokerade replikvolymerna. Detta gäller endast för klientdatorer. |

**Exempel 1:** Kör följande kommando för att återställa MABS-databasen från lokala säkerhetskopieringsmedia på MABS-servern:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

När du har återställt MABS-databasen kör du följande kommando för att synkronisera databaserna:

```cmd
DpmSync -Sync
```

När du har återställt och synkroniserat MABS-databasen och innan du återställer repliken kör du följande kommando för att allokera om diskutrymmet för repliken:

```cmd
DpmSync -ReallocateReplica
```

**Exempel 2:** Kör följande kommando på fjärrdatorn för att återställa MABS-databasen från en fjärrdatabas:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

När du har återställt MABS-databasen kör du följande kommando på MABS-servern för att synkronisera databaserna:

```cmd
DpmSync -Sync
```

När du har återställt och synkroniserat MABS-databasen och innan du återställer repliken kör du följande kommando på MABS-servern för att omallokera diskutrymmet för repliken:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Nästa steg

- [MABS-stödmatris](backup-support-matrix-mabs-dpm.md)
- [Vanliga frågor och svar om MABS](backup-azure-dpm-azure-server-faq.yml)