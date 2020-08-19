---
title: Återställa system tillstånd till en Windows-Server
description: Steg för steg-förklaringar för återställning av Windows Server-systemtillstånd från en säkerhets kopia i Azure.
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 727268b2dc9473a6bda79f023e8a75202d43afe4
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263443"
---
# <a name="restore-system-state-to-windows-server"></a>Återställa system tillstånd till Windows Server

Den här artikeln förklarar hur du återställer säkerhets kopior av Windows Server-systemtillstånd från ett Azure Recovery Services-valv. Om du vill återställa system tillstånd måste du ha en säkerhets kopia av system tillstånd (skapas med instruktionerna i [säkerhetskopiera system tillstånd](backup-azure-system-state.md#back-up-windows-server-system-state)och kontrol lera att du har installerat den [senaste versionen av Microsoft Azure Recovery Services (mars)-agenten](https://aka.ms/azurebackup_agent). Att hämta Windows Server System tillstånds data från ett Azure Recovery Services-valv är en process i två steg:

1. Återställ system tillstånd som filer från Azure Backup. När du återställer system tillstånd som filer från Azure Backup kan du antingen:
   * Återställa system tillstånd till samma server där säkerhets kopiorna gjordes, eller
   * Återställ system tillstånds filen till en annan server.

2. Tillämpa de återställda system tillstånds filerna på en Windows-Server med hjälp av Windows Server Backup-verktyget.

## <a name="recover-system-state-files-to-the-same-server"></a>Återställa systemtillståndsfiler till samma server

Följande steg beskriver hur du återställer din Windows Server-konfiguration till ett tidigare tillstånd. Att återställa Server konfigurationen till ett känt, stabilt tillstånd, kan vara mycket värdefull. Följande steg återställer serverns system tillstånd från ett Recovery Services-valv.

1. Öppna snapin-modulen **Microsoft Azure Backup**. Om du inte vet var snapin-modulen har installerats söker du efter **Microsoft Azure Backup**i datorn eller servern.

    Skriv bords appen bör visas i Sök resultaten.

2. Välj **Återställ data** för att starta guiden.

    ![Återställa data](./media/backup-azure-restore-windows-server/recover.png)

3. I rutan **komma igång** , för att återställa data till samma server eller dator, väljer du **den här servern ( `<server name>` )** och väljer **Nästa**.

    ![Välj detta Server alternativ för att återställa data till samma dator](./media/backup-azure-restore-system-state/samemachine.png)

4. I fönstret **Välj återställnings läge** väljer du **system tillstånd** och väljer sedan **Nästa**.

    ![Bläddra bland filer](./media/backup-azure-restore-system-state/recover-type-selection.png)

5. Välj en återställnings punkt i kalendern i fönstret **Välj volym och datum** .

    Du kan återställa från vilken återställnings punkt som helst. Datum i **fetstil** anger tillgänglighet för minst en återställnings punkt. När du har valt ett datum, om det finns flera återställnings punkter, väljer du den aktuella återställnings punkten från den nedrullningsbara menyn **tid** .

    ![Volym och datum](./media/backup-azure-restore-system-state/select-date.png)

6. När du har valt den återställnings punkt som ska återställas väljer du **Nästa**.

    Azure Backup monterar den lokala återställnings punkten och använder den som återställnings volym.

7. I nästa fönster anger du målet för de återställda system tillstånds filerna. Välj sedan **Bläddra** för att öppna Utforskaren och hitta de filer och mappar som du vill använda. Alternativet **skapa kopior så att du har båda versionerna**, skapar kopior av enskilda filer i en befintlig fil Arkiv i system tillstånd i stället för att skapa en kopia av hela system tillstånds arkivet.

    ![Återställnings alternativ](./media/backup-azure-restore-system-state/recover-as-files.png)

8. Kontrol lera informationen om återställningen i **bekräftelse** fönstret och välj **Återställ**.

   ![Klicka på Återställ för att bekräfta åtgärden för att återställa](./media/backup-azure-restore-system-state/confirm-recovery.png)

9. Kopiera *WindowsImageBackup* -katalogen på återställnings målet till en icke-kritisk volym på servern. Normalt är Windows OS-volymen den kritiska volymen.

10. När återställningen har slutförts följer du stegen i avsnittet [tillämpa återställt system tillstånd på en Windows-Server](#apply-restored-system-state-on-a-windows-server)för att slutföra återställnings processen för system tillstånd.

## <a name="recover-system-state-files-to-an-alternate-server"></a>Återställa system tillstånds filer till en annan server

Om Windows-servern är skadad eller otillgänglig, och du vill återställa den till ett stabilt tillstånd genom att återställa Windows Server System tillstånd, kan du återställa den skadade serverns system tillstånd från en annan server. Använd följande steg för att återställa system tillstånd på en separat server.  

Den terminologi som används i dessa steg omfattar:

* *Käll dator* – den ursprungliga dator som säkerhets kopian skapades från och som för tillfället inte är tillgänglig.
* *Måldator* – den dator som data återställs till.
* *Exempel valv* – det Recovery Services valv som *käll datorn* och *mål datorn* är registrerade på.

> [!NOTE]
> Säkerhets kopieringar som tas från en dator kan inte återställas till en dator som kör en tidigare version av operativ systemet. Säkerhets kopieringar som tagits från en Windows Server 2016-dator kan till exempel inte återställas till Windows Server 2012 R2. Inversen är dock möjlig. Du kan använda säkerhets kopior från Windows Server 2012 R2 för att återställa Windows Server 2016.
>

1. Öppna snapin-modulen **Microsoft Azure Backup** på *mål datorn*.
2. Se till att *mål datorn* och *käll datorn* är registrerade på samma Recovery Services-valv.
3. Välj **Återställ data** för att initiera arbets flödet.
4. Välj **en annan server**

    ![En annan server](./media/backup-azure-restore-system-state/anotherserver.png)

5. Ange den loggfil för valvet som motsvarar *exempel valvet*. Om valvet för valvet är ogiltigt (eller har gått ut) laddar du ned en ny fil för valvet från *exempel valvet* i Azure Portal. När du har angett valv filen för valvet visas det Recovery Services valv som är associerat med filen med valvets autentiseringsuppgifter.

6. I fönstret Välj säkerhets kopierings server väljer du *käll datorn* i listan med datorer som visas.
7. I fönstret Välj återställnings läge väljer du **system tillstånd** och **sedan nästa**.

    ![Search](./media/backup-azure-restore-system-state/recover-type-selection.png)

8. I kalendern i fönstret **Välj volym och datum** väljer du en återställnings punkt. Du kan återställa från vilken återställnings punkt som helst. Datum i **fetstil** anger tillgänglighet för minst en återställnings punkt. När du har valt ett datum, om det finns flera återställnings punkter, väljer du den aktuella återställnings punkten från den nedrullningsbara menyn **tid** .

    ![Sök efter objekt](./media/backup-azure-restore-system-state/select-date.png)

9. När du har valt den återställnings punkt som ska återställas väljer du **Nästa**.

10. I fönstret **Välj återställnings läge för system tillstånd** anger du den plats där du vill att system tillstånds filerna ska återställas och väljer sedan **Nästa**.

    ![Kryptering](./media/backup-azure-restore-system-state/recover-as-files.png)

    Alternativet **skapa kopior så att du har båda versionerna**, skapar kopior av enskilda filer i en befintlig fil Arkiv i system tillstånd i stället för att skapa en kopia av hela system tillstånds arkivet.

11. Kontrol lera informationen om återställningen i bekräftelse fönstret och välj **Återställ**.

    ![Klicka på knappen Återställ för att bekräfta återställnings processen](./media/backup-azure-restore-system-state/confirm-recovery.png)

12. Kopiera katalogen *WindowsImageBackup* till en icke-kritisk volym på servern (till exempel D: \) . Vanligt vis är Windows OS-volymen den kritiska volymen.

13. Slutför återställnings processen genom att använda följande avsnitt för att [tillämpa de återställda filerna för system tillstånd på en Windows-Server](#apply-restored-system-state-on-a-windows-server).

## <a name="apply-restored-system-state-on-a-windows-server"></a>Tillämpa återställt systemtillstånd på en Windows Server

När du har återställt system tillstånd som filer med Azure Recovery Services Agent använder du verktyget Windows Server Backup för att tillämpa det återställda system läget på Windows Server. Verktyget Windows Server Backup finns redan på servern. Följande steg beskriver hur du tillämpar det återställda system tillstånd.

1. Öppna snapin-modulen Windows Server Backup. Om du inte vet var snapin-modulen har installerats söker du efter **Windows Server Backup**i datorn eller servern.

    Skriv bords appen visas i Sök resultaten. Om den inte visas, eller om du stöter på fel när du öppnar programmet, måste du installera **Windows Server Backup funktioner**, och beroende komponenter under den, som är tillgängliga i **guiden Lägg till funktioner** i **Serverhanteraren**.

1. I snapin-modulen väljer du **lokal säkerhets kopiering**.

    ![Välj lokal säkerhets kopia att återställa därifrån](./media/backup-azure-restore-system-state/win-server-backup-local-backup.png)

1. I den lokala säkerhets kopierings konsolen går du till **fönstret åtgärder**och väljer **Återställ** för att öppna återställnings guiden.

1. Välj alternativet, **en säkerhets kopia som lagras på en annan plats**och välj **Nästa**.

   ![Välj att återställa till en annan server](./media/backup-azure-restore-system-state/backup-stored-in-diff-location.png)

1. När du anger plats typen väljer du **delad fjärrmapp** om säkerhets kopian av system tillstånd återställdes till en annan server. Om system tillstånd har återställts lokalt väljer du **lokala enheter**.

    ![Välj om du vill återställa från en lokal server eller en annan](./media/backup-azure-restore-system-state/ss-recovery-remote-shared-folder.png)

1. Ange sökvägen till katalogen *WindowsImageBackup* eller Välj den lokala enhet som innehåller katalogen (till exempel D:\WindowsImageBackup), återställt som en del av återställningen av system tillstånds filer med Azure Recovery Services agent och välj **Nästa**.

    ![sökväg till den delade filen](./media/backup-azure-restore-system-state/ss-recovery-remote-folder.png)

1. Välj den version av system tillstånd som du vill återställa och välj **Nästa**.

1. I fönstret Välj återställnings typ väljer du **system tillstånd** och **sedan nästa**.

1. För system tillstånds återställningens plats väljer du **ursprunglig plats**och **sedan nästa**.

    Om du återställer en domänkontrollant visas följande ytterligare alternativ:

    ![Plats för återställning av system tillstånd](./media/backup-azure-restore-system-state/location-for-system-state-recovery.png)

    >[!NOTE]
    >Välj endast "utför en auktoritativ återställning av Active Directory-filer" om du uttryckligen planerar att göra en auktoritativ återställning av alla Active Directory data.

1. Läs igenom bekräftelse informationen, verifiera inställningarna för omstart och välj **Återställ** för att tillämpa de återställda system tillstånds filerna.

    ![starta återställningen av filer för system tillstånd](./media/backup-azure-restore-system-state/launch-ss-recovery.png)

    >[!NOTE]
    >Välj inte alternativet **starta om servern automatiskt** om du utför återställningen i DSRM-läge.

1. När du har slutfört en återställning måste du starta om servern i normalt läge. Öppna en kommando tolk och skriv följande: `bcdedit /deletevalue safeboot`
1. Starta om servern.

## <a name="special-considerations-for-system-state-recovery-on-a-domain-controller"></a>Särskilda överväganden för återställning av system tillstånd på en domänkontrollant

Säkerhets kopiering av system tillstånd omfattar Active Directory data. Använd följande steg för att återställa Active Directory-domän tjänsten (AD DS) från det aktuella läget till ett tidigare tillstånd. Den här typen av återställning kan göras i två scenarier:

* Återställa alla Active Directory data när det inte finns några fungerande domänkontrollanter kvar i skogen
* Återställa en del av Active Directory data när objekten har tagits bort eller skadats

Den här artikeln diskuterar bara det första scenariot, som anropar en nonauthorative återställning av AD DS och en auktoritativ återställning av SYSVOL-mappen.  Om du behöver utföra det andra scenariot (där domän kontrol Lanterna fortfarande fungerar men du behöver återställa vissa AD-objekt) kan du läsa [följande instruktioner](https://support.microsoft.com/help/840001/how-to-restore-deleted-user-accounts-and-their-group-memberships-in-ac).

1. Följ stegen här för att [återställa system tillstånds filer till en annan server](#recover-system-state-files-to-an-alternate-server).
1. Använd följande kommandon för att starta om servern i *reparations läge för katalog tjänster*. I en upphöjd kommando tolk:

    ```cmd
    Bcdedit /set safeboot dsrepair
    Shutdown /r /t 0
    ```

1. Om du vill återställa Active Directory som en del av system tillstånds återställningen kan du välja någon av följande två metoder:

    * Följ anvisningarna ovan för att [tillämpa återställt system tillstånd på en Windows-Server](#apply-restored-system-state-on-a-windows-server) med Windows Server Backup-verktyget.

        >[!NOTE]
        >Om du återställer alla Active Directory data (och det inte finns några fungerande domänkontrollanter kvar i skogen) i steg 9 ovan, se till att välja **utför en auktoritativ återställning av Active Directory filer**.

    * Använd verktyget [Wbadmin](/windows-server/administration/windows-commands/wbadmin-start-systemstaterecovery) för att utföra återställningen från kommando raden.

        Du behöver versions identifieraren för den säkerhets kopia som du vill använda. Du kan hämta en lista över versions identifierare genom att köra det här kommandot:

        ```cmd
        wbadmin get versions -backuptarget <servername\sharename>
        ```

        Sedan använder du den versions identifieraren för att köra återställningen.

        Om du till exempel vill utföra en [nonauthorative återställning av AD DS och en auktoritativ återställning av SYSVOL-mappen](/windows-server/identity/ad-ds/manage/ad-forest-recovery-nonauthoritative-restore) med säkerhets kopian från 04/30/2020 kl. 9:00, som lagras på den delade resursen `\\servername\share` för `server01` , skriver du:

        ```cmd
        wbadmin start systemstaterecovery -version:04/30/2020-09:00 -backupTarget:\\servername\share -machine:server01 -authsysvol
        ```

1. När du har slutfört en återställning bör du starta om servern i normalt läge. Öppna en kommando tolk och skriv följande: `bcdedit /deletevalue safeboot`
1. Starta om servern.

## <a name="troubleshoot-failed-system-state-restore"></a>Felsökning av misslyckad återställning av systemtillstånd

Om den tidigare processen att tillämpa system tillstånd inte slutförs korrekt kan du använda Windows återställnings miljö (Win) för att återställa Windows Server. Följande steg beskriver hur du återställer med hjälp av Win RE. Använd bara det här alternativet om Windows Server inte startar normalt efter en återställning av system tillstånd. Följande process raderar icke-systemdata och använder försiktighet.

1. Starta Windows Server i Windows återställnings miljö (Win RE).

2. Välj Felsök från de tre tillgängliga alternativen.

    ![Välj Felsök](./media/backup-azure-restore-system-state/winre-1.png)

3. Välj **kommando tolk** på skärmen **Avancerade alternativ** och ange användar namn och lösen ord för Server administratör.

   ![Välj kommando tolk](./media/backup-azure-restore-system-state/winre-2.png)

4. Ange användar namn och lösen ord för Server administratören.

    ![Ange lösenord](./media/backup-azure-restore-system-state/winre-3.png)

5. När du öppnar kommando tolken i administratörs läge kör du följande kommando för att hämta system tillståndets säkerhets kopierings versioner.

    ```cmd
    Wbadmin get versions -backuptarget:<Volume where WindowsImageBackup folder is copied>:
    ```

    ![Hämta säkerhets kopie versioner för system tillstånd](./media/backup-azure-restore-system-state/winre-4.png)

6. Kör följande kommando för att hämta alla volymer som är tillgängliga i säkerhets kopian.

    ```cmd
    Wbadmin get items -version:<copy version from above step> -backuptarget:<Backup volume>
    ```

    ![Hämta alla tillgängliga volymer](./media/backup-azure-restore-system-state/winre-5.png)

7. Följande kommando återställer alla volymer som ingår i säkerhets kopieringen av system tillstånd. Observera att det här steget bara återställer de kritiska volymer som ingår i systemets tillstånd. Alla data som inte är system raderas.

    ```cmd
    Wbadmin start recovery -items:C: -itemtype:Volume -version:<Backupversion> -backuptarget:<backup target volume>
    ```

     ![Återställ alla volymer](./media/backup-azure-restore-system-state/winre-6.png)

## <a name="next-steps"></a>Nästa steg

* Nu när du har återställt dina filer och mappar kan du [Hantera dina säkerhets kopior](backup-azure-manage-windows-server.md).
