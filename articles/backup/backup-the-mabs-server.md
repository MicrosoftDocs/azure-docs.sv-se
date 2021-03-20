---
title: Säkerhetskopiera MABS-servern
description: Lär dig hur du säkerhetskopierar Microsoft Azure Backup Server (MABS).
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 81a6ee005e15b1d7ab7b11a938b8ab14143818f4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92172120"
---
# <a name="back-up-the-mabs-server"></a>Säkerhetskopiera MABS-servern

För att säkerställa att data kan återställas om Microsoft Azure Backup Server (MABS) Miss lyckas, behöver du en strategi för att säkerhetskopiera MABS-servern. Om den inte har säkerhetskopierats måste du återskapa den manuellt efter ett haveri och det går inte att återställa diskbaserade återställnings punkter. Du kan säkerhetskopiera MABS-servrar genom att säkerhetskopiera MABS-databasen.

## <a name="back-up-the-mabs-database"></a>Säkerhetskopiera MABS-databasen

Som en del av din strategi för MABS-säkerhetskopiering måste du säkerhetskopiera MABS-databasen. MABS-databasen heter DPMDB. Den här databasen innehåller MABS-konfigurationen tillsammans med data om säkerhets kopiering av MABS. Om det finns en katastrof kan du återskapa de flesta funktionerna i en MABS-server med hjälp av en aktuell säkerhets kopia av databasen. Förutsatt att du kan återställa databasen är bandbaserade säkerhets kopior tillgängliga och de behåller alla skydds grupps inställningar och säkerhets kopierings scheman. Om MABS Storage pool disks inte påverkades av avbrottet kan diskbaserade säkerhets kopieringar också användas efter en återskapning. Du kan säkerhetskopiera databasen med hjälp av flera olika metoder.

|Metod för säkerhetskopiering av databas|Fördelar|Nackdelar|
|--------------------------|--------------|-----------------|
|[Säkerhetskopiera till Azure](#back-up-to-azure)|<li>Enkelt konfigurerat och övervakat i MABS.<li>Flera platser för de säkerhetskopierade databasfilerna.<li>Molnlagring är en robust lösning för haveriberedskap.<li>Mycket säker lagring för databasen.<li>Har stöd för 120 onlineåterställningspunkter.|<li>Kräver Azure-konto och ytterligare MABS-konfiguration. Viss kostnad för lagring med Azure tillkommer.<li> Kräver en version av Windows Server-baserad dator som stöds med Azure-agenten för att få åtkomst till MABS säkerhets kopior som lagras i Azure Backup-valvet. Detta kan inte vara en annan MABS-Server.<li>Inte ett alternativ om databasen finns lokalt och du vill aktivera sekundärt skydd. <li>Lite extra tid för förberedelser och återställning uppkommer.|
|[Säkerhetskopiera databasen genom att säkerhetskopiera MABS-lagringspoolen](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>Enkla att konfigurera och övervaka.<li>Säkerhets kopian sparas på MABS för lagrings diskar och är lätt att komma åt lokalt.<li>MABS schemalagda säkerhets kopieringar stöder 512 snabba och fullständiga säkerhets kopieringar. Om du säkerhetskopierar varje timme har du ett fullständigt skydd på 21 dagar.|<li>Inte ett bra alternativ för haveriberedskap. Det är online och återställning kanske inte fungerar som förväntat om det inte går att MABS-servern eller Storage pool-disken.<li>Inte ett alternativ om databasen finns lokalt och du vill aktivera sekundärt skydd. <li>Vissa förberedelser och särskilda åtgärder krävs för att få åtkomst till återställnings punkterna om MABS-tjänsten eller konsolen inte körs eller fungerar.|
|[Säkerhetskopiera med intern SQL Server-säkerhetskopiering till en lokal disk](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>Inbyggt i SQL Server.<li>Säkerhets kopian sparas på en lokal disk som är lättillgänglig.<li>Kan schemaläggas att köras så ofta du vill.<li>Helt oberoende av MABS.<li>Du kan schemalägga en rensning av säkerhetskopieringsfilen.|<li>Inte ett bra alternativ för haveriberedskap, såvida inte säkerhetskopiorna kopieras till en fjärrplats.<li>Kräver lokal lagring för säkerhets kopior, vilket kan begränsa kvarhållning och frekvens.|
|[Säkerhetskopiera med inbyggd SQL-säkerhetskopiering och MABS-skydd till en resurs som skyddas av MABS](#back-up-to-a-share-protected-by-mabs)|<li>Övervakas enkelt i MABS.<li>Flera platser för de säkerhetskopierade databasfilerna.<li>Lättillgängligt från alla Windows-datorer i nätverket.<li>Potentiellt den snabbaste återställningsmetoden.|<li>Har endast stöd för 64 återställningspunkter.<li>Inte ett bra alternativ för haveriberedskap. MABS Server eller MABS kan förhindra återställnings åtgärder.<li>Inte ett alternativ om MABS DB är lokalt och du vill aktivera sekundärt skydd. <li>Vissa extra förberedelser krävs för konfiguration och tester.<li>En eventuell extra förberedelse och återställnings tid behövs om själva MABS-servern är nere, men det är en bra MABS disk lagring.|

- Om du säkerhetskopierar med hjälp av en MABS skydds grupp rekommenderar vi att du använder en unik skydds grupp för databasen.

    > [!NOTE]
    > I återställnings syfte måste den MABS-installation som du vill återställa med MABS-databasen matcha versionen av MABS-databasen.  Om den databas som du vill återställa till exempel är från en MABS v3 med Samlad uppdatering 1 måste MABS-servern köra samma version med Samlad uppdatering 1. Det innebär att du kan behöva avinstallera och installera om MABS med en kompatibel version innan du återställer databasen.  Om du vill kontrollera databasversionen kan du behöva montera den manuellt till ett temporärt databasnamn och sedan köra en SQL-fråga mot databasen för att kontrollera den senaste samlade uppdateringen som har installerats baserat på den högre och den lägre versionen.

- Följ dessa steg om du vill kontrol lera MABS-databasens version:

    1. Om du vill köra frågan öppnar du SQL Management Studio och ansluter sedan till SQL-instansen som kör MABS-databasen.

    2. Välj MABS-databasen och starta sedan en ny fråga.

    3. Klistra in följande SQL-fråga i frågefönstret och kör den:

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    Om inget returneras i frågeresultaten, eller om MABS-servern har uppgraderats från tidigare versioner, men ingen ny samlad uppdatering har installerats sedan dess, kommer det inte att finnas någon post för den större, mindre för en grundläggande installation av MABS. För att kontrol lera de MABS-versioner som är associerade med samlade uppdateringar, se [lista över build-nummer för Mabs](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx).

### <a name="back-up-to-azure"></a>Säkerhetskopiera till Azure

1. Innan du börjar måste du köra ett skript för att hämta sökvägen till monterings punkten för MABS-repliken så att du vet vilken återställnings punkt som innehåller säkerhets kopian av MABS. Gör detta efter den första replikeringen med Azure Backup. I skriptet ersätter du `dplsqlservername%` med namnet på SQL Server-instansen som är värd för Mabs-databasen.

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

    Kontrol lera att du har lösen ordet som angavs när Azure Recovery Services-agenten installerades och att MABS-servern registrerades i Azure Backup-valvet. Du behöver lösenordet för att återställa säkerhetskopian.

2. Skapa ett Azure Backup-valv, ladda ned installationsfilen för Azure Backup Agent och valvautentiseringsuppgifterna. Kör installations filen för att installera agenten på MABS-servern och Använd autentiseringsuppgifterna för valvet för att registrera MABS-servern i valvet. [Läs mer](backup-azure-microsoft-azure-backup.md).

3. När valvet har kon figurer ATS konfigurerar du en MABS skydds grupp som innehåller MABS-databasen. Välj för att säkerhetskopiera den till disk och till Azure.

#### <a name="recover-the-mabs-database-from-azure"></a>Återställa MABS-databasen från Azure

Du kan återställa databasen från Azure med hjälp av en MABS-server som är registrerad i Azure Backup-valvet, enligt följande:

1. I Mabs-konsolen väljer du **återställning**  >  **Lägg till extern Mabs**.

2. Ange autentiseringsuppgifter för valvet (Ladda ned från Azure Backup-valvet). Observera att autentiseringsuppgifterna bara är giltiga i två dagar.

3. I **Välj externa Mabs för återställning** väljer du den Mabs-server som du vill återställa databasen för, skriver in krypterings lösen frasen och väljer **OK.**

4. Välj den återställningspunkt som du vill använda från listan över tillgängliga punkter. Välj **Rensa externa Mabs** för att återgå till den lokala Mabs-vyn.

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>Säkerhetskopiera MABS-databasen till MABS-lagringspoolen

> [!NOTE]  
> Det här alternativet gäller för MABS med Modern Backup Storage.

1. I Mabs-konsolen väljer du **skydd**  >  **skapa skydds grupp**.
2. På sidan **Välj typ av skydds grupp** väljer du **servrar**.
3. På sidan **Välj grupp medlemmar** väljer du **DPM-databas**. Expandera MABS-servern och välj DPMDB.
4. På sidan **Välj data skydds metod** väljer **du jag vill ha kortvarigt skydd med disk**. Ange alternativ för den kortsiktiga skyddsprincipen.
5. Kör följande SQL-skript efter den inledande replikeringen av MABS-databasen:

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

### <a name="recover-mabs-database"></a>Återställ MABS-databas

För att rekonstruera MABS med samma databas måste du först återställa MABS-databasen och synkronisera den med den nyligen installerade MABS.

#### <a name="use-the-following-steps"></a>Använd följande steg

1. Öppna en administrativ kommando tolk och kör `psexec.exe -s powershell.exe` för att starta ett PowerShell-fönster i system kontexten.
2. Bestäm var du vill återställa databasen:

#### <a name="to-copy-the-database-from-the-last-backup"></a>Kopiera databasen från den senaste säkerhets kopian

1. Navigera till VHD-sökväg för replikering `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>`
2. Montera **disk0. vhdx** som finns i den med hjälp av `mount-vhd disk0.vhdx` kommandot.
3. När den virtuella replik hård disken är monterad använder `mountvol.exe` du för att tilldela replik volymen en enhets beteckning med hjälp av det fysiska replik-ID: t från SQL-skriptet. Exempelvis: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>Kopiera databasen från en tidigare återställnings punkt

1. Navigera till behållar katalogen DPMDB  `\<MABSServer FQDN\>\<PhysicalReplicaId\>` . Du ser flera kataloger med vissa unika GUID-identifierare under motsvarande återställnings punkter för MABS-databasen. Andra kataloger representerar en depå/återställnings punkt.
2. Navigera till valfri depå-VHD-sökväg, till exempel `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` och montera **disk0. vhdx** som finns i den med hjälp av `mount-vhd disk0.vhdx` kommandot.
3. När den virtuella repliken är monterad använder `mountvol.exe` du för att tilldela replik volymen en enhets beteckning med hjälp av det fysiska replik-ID: t från SQL-skriptet. Exempelvis: `mountvol X: \?\Volume{}\`

   Alla termer som visas med vinkel paren tes i stegen ovan är placera innehavarna. Ersätt dem med lämpliga värden på följande sätt:
   - **ReFSVolume** – åtkomst Sök väg från SQL-skriptets utdata
   - **MABSSERVER FQDN** – fullständigt kvalificerat namn för Mabs-servern
   - **PhysicalReplicaId** -fysisk replik-ID från SQL-skriptet ut
   - **PITId** -GUID annat än det fysiska replik-ID: t i behållar katalogen.
4. Öppna en annan administrativ kommando tolk och kör `psexec.exe -s cmd.exe` för att starta en kommando tolk i system kontexten.
5. Ändra katalogen till X:-enheten och navigera till platsen för MABS.
6. Kopiera dem till en plats som är lätt att återställa från. Avsluta PsExec cmd-fönstret när du har kopierat.
7. Gå till PsExec PowerShell-fönstret som du öppnade i steg 1, navigera till VHDX-sökvägen och demontera VHDX med hjälp av kommandot `dismount-vhd disk0.vhdx` .
8. När du har installerat om MABS-servern kan du använda den återställda DPMDB för att ansluta till MABS-servern genom att köra `DPMSYNC-RESTOREDB` kommandot.
9. Kör `DPMSYNC-SYNC` en gång `DPMSYNC-RESTOREDB` har slutförts.

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>Säkerhetskopiera databasen genom att säkerhetskopiera MABS-lagringspoolen

> [!NOTE]
> Det här alternativet gäller för MABS med äldre lagrings enheter.

Innan du börjar måste du köra ett skript för att hämta sökvägen till monterings punkten för MABS-repliken så att du vet vilken återställnings punkt som innehåller säkerhets kopian av MABS. Gör detta efter den första replikeringen med Azure Backup. I skriptet ersätter du `dplsqlservername%` med namnet på SQL Server-instansen som är värd för Mabs-databasen.

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

1. I Mabs-konsolen väljer du **skydd**  >  **skapa skydds grupp**.

2. Välj **Servrar** på sidan **Välj typ av skyddsgrupp**.

3. På sidan **Välj grupp medlemmar** väljer du Mabs-databasen. Expandera MABS-serverobjektet och välj **DPMDB**.

4. På sidan  **Välj data skydds metod** väljer **du jag vill ha kortvarigt skydd med disk**. Ange alternativ för den kortsiktiga skyddsprincipen. Vi rekommenderar ett kvarhållningsintervall på två veckor för MABS-databaser.

#### <a name="recover-the-database"></a>Återställa databasen

Om MABS-servern fortfarande fungerar och lagringspoolen är intakt (till exempel problem med MABS-tjänsten eller konsolen) kopierar du databasen från replik volymen eller en skugg kopia enligt följande:

1. Välj från vilken tidpunkt du vill återställa databasen.

    - Om du vill kopiera databasen från den senaste säkerhets kopian som tagits direkt från MABS replik volym använder du **mountvol.exe** för att tilldela replik volymen en enhets beteckning med hjälp av det GUID som SQL-skriptet returnerade. Exempelvis: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - Om du vill kopiera databasen från en tidigare återställnings punkt (skugg kopia) måste du lista alla skugg kopior för repliken med hjälp av volymens GUID från SQL-skriptet. Det här kommandot visar skugg kopior för volymen: `C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` . Observera skapande tiden och skugg kopians ID som du vill återställa från.

2. Använd **diskshadow.exe** för att montera skugg kopian till en oanvänd enhets beteckning X: med hjälp av skugg kopians ID så att du kan kopiera databasfilerna.

3. Öppna en administrativ kommando tolk och kör `psexec.exe -s cmd.exe` för att starta en kommando tolk i system kontexten, så att du har behörighet att navigera till replik volymen (X:) och kopiera filerna.

4. CD till X:-enheten och navigera till platsen för MABS. Kopiera dem till en plats som är lätt att återställa från. När kopieringen är klar finns PsExec cmd-fönstret och kör **diskshadow.exe** och visar inte X:-volymen.

5. Nu kan du återställa databasfilerna med hjälp av SQL Management Studio eller genom att köra **DpmSync- \- restoredb**.

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>Säkerhetskopiera med intern SQL Server-säkerhetskopiering till en lokal disk

Du kan säkerhetskopiera MABS-databasen till en lokal disk med inbyggd SQL Server säkerhets kopiering, oberoende av MABS.

- Hämta en [översikt](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) över SQL Server-säkerhetskopiering.

- [Lär dig mer](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) om att säkerhetskopiera SQL Server till molnet.

## <a name="back-up-to-a-share-protected-by-mabs"></a>Säkerhetskopiera till en resurs som skyddas av MABS

Det här säkerhets kopierings alternativet använder inbyggd SQL för att säkerhetskopiera MABS-databasen till en resurs, skyddar resursen med MABS och använder Windows VSS tidigare versioner för att under lätta återställningen.

### <a name="before-you-start"></a>Innan du börjar

1. På SQL Server, gör du en mapp på en enhet med tillräckligt mycket ledigt utrymme för att lagra en enda kopia av en säkerhets kopia. Exempel: `C:\MABSBACKUP`.

1. Dela mappen. Dela till exempel `C:\MABSBACKUP` mappen som *DPMBackup*.

1. Kopiera och klistra in OSQL-kommandot nedan i anteckningar och spara det i en fil med namnet `C:\MABSACKUP\bkupdb.cmd` . Se till att det inte finns något. txt-tillägg. Ändra SQL_Instance_name och DPMDB_NAME för att matcha instans-och DPMDB-namnet som används av MABS-servern.

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. Använd anteckningar och öppna filen **ScriptingConfig.xml** som finns under `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` mappen på Mabs-servern.

1. Ändra **ScriptingConfig.xml** och ändra **DataSourceName =** till enhets beteckningen som innehåller mappen/resursen DPMDBBACKUP. Ändra posten PreBackupScript till den fullständiga sökvägen och namnet på **bkupdb. cmd** som sparades i steg 3.

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

1. Skydda mappen C:\MABSBACKUP eller `\sqlservername\MABSBACKUP` resursen med Mabs och vänta tills den första repliken har skapats. Det bör finnas en **DPMDB. bak** i mappen C:\MABSBACKUP som ett resultat av skriptet för säkerhets kopiering som körs, som i sin tur kopierades till Mabs-repliken.

1. Om du inte aktiverar självbetjänings återställning behöver du några ytterligare steg för att dela mappen MABSBACKUP på repliken:

    1. I MABS-konsolen > **skydd** letar du upp data källan MABSBACKUP och markerar den. I avsnittet information väljer **du klicka för att visa information** om länken till replik Sök vägen och kopierar sökvägen till anteckningar. Ta bort källsökvägen och behåll målsökvägen. Sökvägen bör se ut ungefär så här: `C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP` .

    2. Gör en resurs till den sökvägen med hjälp av resurs namnet **MABSSERVERNAME-DPMDB**. Du kan använda kommandot Net Share nedan från en administrativ kommandotolk.

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>Konfigurera säkerhetskopieringen

Du kan säkerhetskopiera MABS-databasen på samma sätt som andra SQL Server-databaser med SQL Server inbyggd säkerhets kopiering.

- Hämta en [översikt](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases) över SQL Server-säkerhetskopiering.

- [Lär dig mer](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service) om att säkerhetskopiera SQL Server till molnet.

### <a name="recover-the-mabs-database"></a>Återställa MABS-databasen

1. Anslut till `\\MABSServer\MABSSERVERNAME-dpmdb` resursen med Utforskaren från valfri Windows-dator.

2. Högerklicka på filen **DPMDB. bak** för att visa egenskaper. På fliken **Tidigare versioner** finns alla säkerhetskopior som du kan välja och kopiera. Det finns också den allra senaste säkerhets kopian som fortfarande finns i mappen C:\MABSBACKUP som också är lättillgänglig.

3. Om du behöver flytta en SAN-ansluten MABS till en annan server för att kunna läsa från replik volymen, eller om du vill installera om Windows för att läsa lokalt anslutna diskar, måste du känna till MABS replik volymens monterings punkt eller volym-GUID innan du vet vilken volym som innehåller säkerhets kopian av databasen. Du kan använda SQL-skriptet nedan för att ta fram denna information när som helst efter det inledande skyddet har genomförts, men innan du behöver göra en återställning. Ersätt `%dpmsqlservername%` med namnet på SQL Server som är värd för databasen.

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

4. Om du behöver återställa efter att ha flyttat diskarna för MABS eller en MABS-Server återskapar du:

    1. Du har volymens GUID, så om volymen måste monteras på en annan Windows-Server eller efter att en MABS-Server återskapas använder du **mountvol.exe** för att tilldela den en enhets beteckning med hjälp av volymens GUID från SQL-skriptet: `C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\` .

    2. Dela mappen MABSBACKUP på replik volymen med enhets beteckningen och den del av replik Sök vägen som representerar mappstrukturen.

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. Anslut till `\\SERVERNAME\MABSSERVERNAME-dpmdb` resursen med Utforskaren från valfri Windows-dator.

    4. Högerklicka på filen **DPMDB. bak** för att visa egenskaperna. På fliken **Tidigare versioner** finns alla säkerhetskopior som du kan välja och kopiera.

## <a name="using-dpmsync"></a>Använda DPMSync

**DpmSync** är ett kommando rads verktyg som gör att du kan synkronisera Mabs-databasen med statusen för diskarna i lagringspoolen och med de installerade skydds agenterna. DpmSync återställer MABS-databasen, synkroniserar MABS-databasen med replikerna i lagringspoolen, återställer rapport databasen och allokerar om saknade repliker.

### <a name="parameters"></a>Parametrar

| Parameter      | Beskrivning    |
|----------------|-----------------------------|
| **– RestoreDb**                       | Återställer en MABS-databas från en angiven plats.|
| **-Synkronisera**                            | Synkroniserar återställda databaser. Du måste köra **DpmSync – sync** när du har återställt databaserna. När du har kört **DpmSync – sync** kan vissa repliker fortfarande markeras som saknade. |
| **– DbLoc-** *plats*                | Identifierar platsen för säkerhets kopieringen av MABS-databasen.|
| **-InstanceName** <br/>*server\instance*     | Instans som DPMDB måste återställas till.|
| **– ReallocateReplica**         | Allokerar om alla replik volymer som saknas utan synkronisering. |
| **– DataCopied**                      | Anger att du har slutfört inläsningen av data i de nyligen allokerade replik volymerna. Detta gäller endast för klient datorer. |

**Exempel 1:** Om du vill återställa MABS-databasen från det lokala säkerhets kopierings mediet på MABS-servern kör du följande kommando:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

När du har återställt MABS-databasen ska du köra följande kommando för att synkronisera databaserna:

```cmd
DpmSync -Sync
```

När du har återställt och synkroniserat MABS-databasen och innan du återställer repliken kör du följande kommando för att allokera om disk utrymme för repliken:

```cmd
DpmSync -ReallocateReplica
```

**Exempel 2:** Om du vill återställa MABS-databasen från en fjärrdatabas kör du följande kommando på fjärrdatorn:

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

När du har återställt MABS-databasen ska du köra följande kommando på MABS-servern för att synkronisera databaserna:

```cmd
DpmSync -Sync
```

När du har återställt och synkroniserat MABS-databasen och innan du återställer repliken kör du följande kommando på MABS-servern för att allokera om disk utrymme för repliken:

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>Nästa steg

- [MABS support mat ris](backup-support-matrix-mabs-dpm.md)
- [VANLIGA FRÅGOR OCH SVAR OM MABS](backup-azure-dpm-azure-server-faq.md)