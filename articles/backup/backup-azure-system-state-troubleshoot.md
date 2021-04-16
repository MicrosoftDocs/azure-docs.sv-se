---
title: Felsöka säkerhetskopiering av systemtillstånd
description: I den här artikeln får du lära dig hur du felsöker problem med säkerhetskopiering av systemtillstånd för lokala Windows-servrar.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/22/2019
ms.openlocfilehash: 07e101fe87fb3f5db0bb716f0bc9ea6f8773aa3e
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518566"
---
# <a name="troubleshoot-system-state-backup"></a>Felsöka säkerhetskopiering av systemtillstånd

I den här artikeln beskrivs lösningar på problem som du kan stöta på när du använder Säkerhetskopiering av systemtillstånd.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du utför följande verifieringssteg innan du börjar felsöka säkerhetskopiering av systemtillstånd:

- [Kontrollera Microsoft Azure Recovery Services-agenten (MARS) är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Kontrollera att det finns en nätverksanslutning mellan MARS-agenten och Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Kontrollera att Microsoft Azure Recovery Services körs (i tjänstkonsolen). Starta om och försök igen om det behövs
- [Kontrollera att det finns 5–10 % ledigt utrymme i den tillfälliga mappen](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- [Kontrollera att inte andra processer eller antivirusprogram stör Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)
- [Schemalagd säkerhetskopiering misslyckas, men manuell säkerhetskopiering fungerar](./backup-azure-mars-troubleshoot.md#backups-dont-run-according-to-schedule)
- Kontrollera att ditt operativsystem har de senaste uppdateringarna
- [Se till att enheter och filer med attribut som inte stöds undantas från säkerhetskopiering](backup-support-matrix-mars-agent.md#supported-drives-or-volumes-for-backup)
- Kontrollera att **systemklockan** i det skyddade systemet är konfigurerad till rätt tidszon <br>
- [Kontrollera att servern har minst .NET Framework version 4.5.2 eller senare](https://www.microsoft.com/download/details.aspx?id=30653)<br>
- Om du försöker registrera om **servern till ett** valv gör du följande: <br>
  - Se till att agenten avinstalleras på servern och tas bort från portalen <br>
  - Använd samma lösenfras som användes vid den första registreringen av servern <br>
- Om det här är en offlinesäkerhetskopiering måste du Azure PowerShell version 3.7.0 är installerad på både käll- och kopieringsdatorn innan du påbörjar offlinesäkerhetskopiering
- [Att ta hänsyn till när Backup-agenten körs på en virtuell Azure-dator](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-agent-running-on-an-azure-virtual-machine)

### <a name="limitation"></a>Begränsning

- Återställning till annan maskinvara med återställning av systemtillståndet rekommenderas inte av Microsoft
- Säkerhetskopiering av systemtillstånd stöder för närvarande "lokala" Windows-servrar. Den här funktionen är inte tillgänglig för virtuella Azure-datorer.

## <a name="prerequisites"></a>Förutsättningar

Innan vi felsöker Säkerhetskopiering av systemtillstånd Azure Backup bör du utföra följande kravkontroll.  

### <a name="verify-windows-server-backup-is-installed"></a>Kontrollera Windows Server Backup har installerats

Kontrollera Windows Server Backup är installerat och aktiverat på servern. Kontrollera installationsstatusen genom att köra följande PowerShell-kommando:

 ```powershell
Get-WindowsFeature Windows-Server-Backup
 ```

Om utdata visar **installationstillståndet** som **tillgängligt** betyder det att funktionen Windows Server Backup är tillgänglig för installationen men inte installerad på servern. Men om Windows Server Backup inte är installerat använder du någon av metoderna nedan för att installera den.

#### <a name="method-1-install-windows-server-backup-using-powershell"></a>Metod 1: Installera Windows Server Backup powershell

Om du Windows Server Backup med PowerShell kör du följande kommando:

  ```powershell
  Install-WindowsFeature -Name Windows-Server-Backup
  ```

#### <a name="method-2-install-windows-server-backup-using-server-manager"></a>Metod 2: Installera Windows Server Backup med Serverhanteraren

Utför följande Windows Server Backup installera Serverhanteraren med hjälp av Serverhanteraren:

1. I **Serverhanteraren väljer** du Lägg **till roller och funktioner**. Guiden **Lägg till roller och funktioner** visas.

    ![Instrumentpanel](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

2. Välj **Installationstyp** och sedan **Nästa.**

    ![Installationstyp](./media/backup-azure-system-state-troubleshoot/install_type.jpg)

3. Välj en server från serverpoolen och välj **Nästa.** Lämna standardvalet i Serverroll och välj **Nästa.**
4. Välj **Windows Server Backup** på **fliken** Funktioner och välj **Nästa.**

    ![Fönstret Välj funktioner](./media/backup-azure-system-state-troubleshoot/features.png)

5. På fliken **Bekräftelse** väljer du **Installera** för att starta installationsprocessen.
6. På **fliken Resultat** visas funktionen Windows Server Backup har installerats på Windows Server.

    ![Installationsresultat](./media/backup-azure-system-state-troubleshoot/results.jpg)

### <a name="system-volume-information-permission"></a>Behörighet för systemvolyminformation

Kontrollera att det lokala systemet har fullständig kontroll över mappen **System Volume Information** som finns på den volym där Windows är installerat. Detta är vanligtvis **C:\System Volume Information**. Windows Server-säkerhetskopiering kan misslyckas om behörigheterna ovan inte har angetts korrekt.

### <a name="dependent-services"></a>Beroende tjänster

Se till att tjänsterna nedan är i körningstillstånd:

**Tjänstnamn** | **Startmetod**
--- | ---
RPC (Remote Procedure Call) | Automatiskt
COM+-händelsesystem (EventSystem) | Automatiskt
System Event Notification Service (SENS) | Automatiskt
Volume Shadow Copy(VSS) | Manuell
Microsoft Software Shadow Copy Provider (SWPRV) | Manuell

### <a name="validate-windows-server-backup-status"></a>Verifiera Windows Server Backup status

Verifiera statusen Windows Server Backup genom att utföra följande steg:

- Kontrollera att WSB PowerShell körs

  - Kör `Get-WBJob` från ett upphöjt PowerShell och kontrollera att det inte returnerar följande fel:

    > [!WARNING]
    > Get-WBJob: Termen "Get-WBJob" identifieras inte som namnet på en cmdlet, funktion, skriptfil eller ett hanterbart program. Kontrollera stavningen i namnet eller om en sökväg inkluderades, kontrollera att sökvägen är korrekt och försök igen.

    - Om det här felet misslyckas installerar du om Windows Server Backup på serverdatorn enligt steg 1 i förutsättningarna.

  - Kontrollera att WSB-säkerhetskopieringen fungerar korrekt genom att köra följande kommando från en upphöjd kommandotolk:

      `wbadmin start systemstatebackup -backuptarget:X: -quiet`

      > [!NOTE]
      >Ersätt X med enhetsbeteckningen för den volym där du vill lagra systemtillståndet för avbildningen.

    - Kontrollera jobbets status regelbundet genom att köra `Get-WBJob` kommandot från upphöjd PowerShell
    - När säkerhetskopieringsjobbet har slutförts kontrollerar du jobbets slutgiltiga status genom att köra `Get-WBJob -Previous 1` kommandot

Om jobbet misslyckas indikerar det ett WSB-problem som skulle leda till fel vid säkerhetskopiering av MARS-agentens systemtillstånd.

## <a name="common-errors"></a>Vanliga fel

### <a name="vss-writer-timeout-error"></a>Timeout-fel för VSS-skrivare

| Symptom | Orsak | Lösning
| -- | -- | --
| – MARS-agenten misslyckas med felmeddelandet: "WSB-jobbet misslyckades med VSS-fel. Kontrollera VSS-händelseloggarna för att lösa felet"<br/><br/> – Följande fellogg finns i VSS-programmets händelseloggar: "En VSS-skrivare har avvisat en händelse med felet 0x800423f2, skrivarens tidsgräns gick ut mellan låsningshändelserna och Thaw-händelserna."| VSS-skrivaren kan inte slutföras i tid på grund av brist på processor- och minnesresurser på datorn <br/><br/> En annan säkerhetskopieringsprogramvara använder redan VSS-skrivaren, vilket gör att det inte gick att slutföra åtgärden med ögonblicksbilden för den här säkerhetskopieringen | Vänta tills CPU/minne frigörs i systemet eller avbryt processerna som tar för mycket minne/CPU och försök igen. <br/><br/>  Vänta tills den pågående säkerhetskopieringen har slutförts och försök utföra åtgärden vid ett senare tillfälle när inga säkerhetskopieringar körs på datorn.

### <a name="insufficient-disk-space-to-grow-shadow-copies"></a>Otillräckligt diskutrymme för att utöka skuggkopior

| Symptom | Lösning
| -- | --
| – MARS-agenten misslyckas med felmeddelandet: Säkerhetskopieringen misslyckades eftersom skuggkopievolymen inte kunde växa på grund av otillräckligt diskutrymme på volymer som innehåller systemfiler <br/><br/> - Följande fel-/varningslogg finns i volsnap-systemhändelseloggarna: "Det fanns inte tillräckligt med diskutrymme på volym C: för att utöka skuggkopielagringen för skuggkopior av C: på grund av det här felet riskerar alla skuggkopior av volym C: att tas bort" | – Frigör utrymme i den markerade volymen i händelseloggen så att det finns tillräckligt med utrymme för skuggkopior att växa medan säkerhetskopieringen pågår <br/><br/> - När du konfigurerar skuggkopieutrymme kan vi begränsa mängden utrymme som används för skuggkopia. Mer information finns i den här [artikeln](/windows-server/administration/windows-commands/vssadmin-resize-shadowstorage)

### <a name="efi-partition-locked"></a>Låst EFI-partition

| Symptom | Lösning
| -- | --
| MARS-agenten misslyckas med felmeddelandet: "Säkerhetskopiering av systemtillstånd misslyckades eftersom EFI-systempartitionen är låst. Detta kan bero på åtkomst till systempartitioner av en säkerhet från tredje part eller programvara som backas upp" | – Om problemet beror på en säkerhetsprogramvara från tredje part måste du kontakta leverantören av antivirusprogrammet så att de kan tillåta MARS-agenten <br/><br/> – Om en programvara för säkerhetskopiering från tredje part körs väntar du tills den har avslutats och försöker sedan säkerhetskopiera igen

## <a name="next-steps"></a>Nästa steg

- Mer information om Windows-systemtillstånd i Resource Manager distribution finns i [Operativsystemet för Windows Server](backup-azure-system-state.md)
