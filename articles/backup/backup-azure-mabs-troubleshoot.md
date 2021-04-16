---
title: Felsöka Azure Backup Server
description: Felsöka installation, registrering av Azure Backup Server samt säkerhetskopiering och återställning av programarbetsbelastningar.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 644946ca90c2893ba3d87f9d2ff8bfd8325f4715
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514758"
---
# <a name="troubleshoot-azure-backup-server"></a>Felsöka Azure Backup Server

Använd informationen i följande tabeller för att felsöka fel som uppstår när du använder Azure Backup Server.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du utför följande verifiering innan du börjar felsöka Microsoft Azure Backup Server (MABS):

- [Kontrollera Microsoft Azure Recovery Services-agenten (MARS) är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Kontrollera att det finns en nätverksanslutning mellan MARS-agenten och Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Kontrollera att Microsoft Azure Recovery Services körs (i tjänstkonsolen). Starta om och försök igen om det behövs
- [Kontrollera att det finns 5–10 % ledigt utrymme i den tillfälliga mappen](./backup-azure-file-folder-backup-faq.yml#what-s-the-minimum-size-requirement-for-the-cache-folder-)
- Om registreringen misslyckas ser du till att den server som du försöker installera Azure Backup Server inte redan har registrerats med ett annat valv
- Kontrollera om DPM-agenten redan finns om push-överföringen misslyckas. Om den gör det avinstallerar du agenten och provar att installera igen
- [Se till att inga andra processer eller antivirusprogram stör Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Kontrollera att SQL Agent-tjänsten körs och är inställd på automatisk på MABS-servern<br>

## <a name="configure-antivirus-for-mabs-server"></a>Konfigurera antivirus för MABS-server

MABS är kompatibelt med de flesta populära antivirusprogram. Vi rekommenderar följande steg för att undvika konflikter:

1. **Inaktivera övervakning i realtid** – inaktivera övervakning i realtid av antivirusprogram för följande:
    - `C:\Program Files<MABS Installation path>\XSD` Mappen
    - `C:\Program Files<MABS Installation path>\Temp` Mappen
    - Enhetsbeteckning för Modern Backup Storage volym
    - Replik- och överföringsloggar: Inaktivera realtidsövervakning **avdpmra.exe**, som finns i mappen `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . Realtidsövervakning försämrar prestandan eftersom antivirusprogram genomsöker replikerna varje gång MABS synkroniserar med den skyddade servern och genomsöker alla berörda filer varje gång MABS tillämpar ändringar på replikerna.
    - Administratörskonsol: Om du vill undvika prestandapåverkan inaktiverar du realtidsövervakning av **csc.exe** processen. Processen **csc.exe** C-kompilatorn och realtidsövervakning kan försämra prestandan eftersom antivirusprogrammet genomsöker filer somcsc.exegenererar när \# XML-meddelanden  genereras. **CSC.exe** finns i följande sökvägar:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - För MARS-agenten som är installerad på MABS-servern rekommenderar vi att du undantar följande filer och platser:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` som en process
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Scratch-plats (om du inte använder standardplatsen)
2. **Inaktivera realtidsövervakning på** den skyddade servern: Inaktivera realtidsövervakning **avdpmra.exe**, som finns i mappen , på den `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` skyddade servern.
3. Konfigurera antivirusprogram för att ta bort de infekterade filerna på skyddade servrar och **MABS-servern:** Om du vill förhindra skadade data för repliker och återställningspunkter konfigurerar du antivirusprogrammet så att det tar bort infekterade filer i stället för att rensa eller ta bort infekterade filer automatiskt. Automatisk rensning och karantän kan göra att antivirusprogrammet ändrar filer och gör ändringar som MABS inte kan identifiera.

Du bör köra en manuell synkronisering med en konsekvens. Kontrollera jobbet varje gång som antivirusprogrammet tar bort en fil från repliken, även om repliken är markerad som inkonsekvent.

### <a name="mabs-installation-folders"></a>MABS-installationsmappar

Standardinstallationsmapparna för DPM är följande:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

Du kan också köra följande kommando för att hitta sökvägen till installationsmappen:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter har angetts

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Registrera till ett valv | Ogiltiga valvautentiseringsuppgifter har angetts. Filen är skadad eller har inte de senaste autentiseringsuppgifterna som är associerade med återställningstjänsten. | Rekommenderad åtgärd: <br> <ul><li> Ladda ned den senaste autentiseringsfilen från valvet och försök igen. <br>(OR)</li> <li> Om den föregående åtgärden inte fungerade kan du försöka ladda ned autentiseringsuppgifterna till en annan lokal katalog eller skapa ett nytt valv. <br>(OR)</li> <li> Prova att uppdatera datum- och tidsinställningarna enligt beskrivningen i [den här artikeln.](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided) <br>(OR)</li> <li> Kontrollera om c:\windows\temp har fler än 65 000 filer. Flytta inaktuella filer till en annan plats eller ta bort objekten i Temp-mappen. <br>(OR)</li> <li> Kontrollera certifikatens status. <br> a. Öppna **Hantera datorcertifikat** (i Kontrollpanelen). <br> b. Expandera den **personliga noden** och dess underordnade nodCertifikat. <br> c.  Ta bort certifikatet **Windows Azure Tools**. <br> d. Försök att registrera på nytt i Azure Backup klienten. <br> (OR) </li> <li> Kontrollera om det finns någon grupprincip. </li></ul> |

## <a name="replica-is-inconsistent"></a>Repliken är inkonsekvent

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Repliken är inkonsekvent | Kontrollera att alternativet för automatisk konsekvenskontroll i guiden Skyddsgrupp är aktiverat. Mer information om replikeringsalternativ och konsekvenskontroller finns i den [här artikeln.](/system-center/dpm/create-dpm-protection-groups)<br> <ol><li> När det gäller säkerhetskopiering av systemtillstånd/BMR kontrollerar du Windows Server Backup har installerats på den skyddade servern.</li><li> Kontrollera om det finns utrymmesrelaterade problem i DPM-lagringspoolen på DPM/Microsoft Azure Backup Server och allokera lagringsutrymme efter behov.</li><li> Kontrollera tillståndet för den tjänsten Volume Shadow Copy på den skyddade servern. Om den är i ett inaktiverat tillstånd anger du att den ska starta manuellt. Starta tjänsten på servern. Gå sedan tillbaka till DPM/Microsoft Azure Backup Server-konsolen och starta synkroniseringen med konsekvenskontrolljobbet.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Det gick inte att skapa återställningspunkten online

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Det gick inte att skapa återställningspunkten online | **Felmeddelande:** Windows Azure Backup Agent kunde inte skapa en ögonblicksbild av den valda volymen. <br> **Lösning:** Prova att öka utrymmet på replik- och återställningspunktvolymen.<br> <br> **Felmeddelande:** Windows Azure Backup-agenten kan inte ansluta till OBEngine-tjänsten <br> **Lösning:** Kontrollera att OBEngine finns i listan över tjänster som körs på datorn. Om OBEngine-tjänsten inte körs använder du kommandot "net start OBEngine" för att starta OBEngine-tjänsten. <br> <br> **Felmeddelande:** Krypteringsfrasen för den här servern har inte angetts. Konfigurera en krypteringsfras. <br> **Lösning:** Prova att konfigurera en lösenfras för kryptering. Om det misslyckas gör du följande: <br> <ol><li>Kontrollera att den scratch-platsen finns. Det här är den plats som anges i **HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config**, med namnet **ScratchLocation** ska finnas.</li><li> Om den scratch-platsen finns kan du försöka registrera igen med hjälp av den gamla lösenfrasen. *När du konfigurerar en krypterings lösenfras sparar du den på en säker plats.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>De ursprungliga och externa DPM-servrarna måste vara registrerade i samma valv

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Återställ | **Felkod:** CBPServerRegisteredVaultDontMatchWithCurrent/Vault Credentials Error: 100110 <br/> <br/>**Felmeddelande:** De ursprungliga och externa DPM-servrarna måste vara registrerade i samma valv | **Orsak:** Det här problemet uppstår när du försöker återställa filer till den alternativa servern från den ursprungliga servern med hjälp av alternativet Extern DPM-återställning, och om den server som återställs och den ursprungliga servern där data säkerhetskopieras inte är associerade med samma Recovery Services-valv.<br/> <br/>**Lösning** Lös problemet genom att se till att både den ursprungliga och alternativa servern är registrerade i samma valv.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Det går inte att skapa återställningspunkt online för virtuella VMware-datorer

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Det går inte att skapa återställningspunkt online för virtuella VMware-datorer. DPM påträffade ett fel från VMware vid försök att hämta ChangeTracking-information. ErrorCode – FileFaultFault (ID 33621) |  <ol><li> Återställa CTK på VMware för de berörda virtuella datorerna.</li> <li>Kontrollera att oberoende diskar inte finns på VMware.</li> <li>Stoppa skyddet för de berörda virtuella datorerna och återaktivera skyddet med **knappen** Uppdatera. </li><li>Kör en kopia för de berörda virtuella datorerna.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM-agentkoordinatortjänsten på servern

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Push-pusha agenter till skyddade servrar | Agentåtgärden misslyckades på grund av ett kommunikationsfel med DPM-agentkoordinator tjänsten på \<ServerName> . | **Om den rekommenderade åtgärden som visas i produkten inte fungerar utför du följande steg:** <ul><li> Om du kopplar en dator från en obetrott domän följer du [dessa steg.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br> (OR) </li><li> Om du kopplar en dator från en betrodd domän felsöker du med hjälp av stegen som beskrivs i den [här bloggen.](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726) <br>(OR)</li><li> Prova att inaktivera antivirus som ett felsökningssteg. Om det löser problemet ändrar du antivirusinställningarna enligt förslaget i den [här artikeln.](/system-center/dpm/run-antivirus-server)</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Det gick inte att uppdatera metadata för registret

| Åtgärd | Felinformation | Lösning |
|-----------|---------------|------------|
|Installation | Det gick inte att uppdatera registermetadata. Det här uppdateringsfelet kan leda till överanvändning av lagringsförbrukning. Undvik detta genom att uppdatera registerposten ReFS Trimming. | Justera registernyckeln **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim.** Ange värdet Dword till 1. |
|Installation | Det gick inte att uppdatera metadata för registret. Det här uppdateringsfelet kan leda till överanvändning av lagringsförbrukningen. Undvik detta genom att uppdatera registerposten Volume SnapOptimization. | Skapa registernyckeln **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** med ett tomt strängvärde. |

## <a name="registration-and-agent-related-issues"></a>Problem med registrering och agent

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Push-pusha agenter till skyddade servrar | Autentiseringsuppgifterna som anges för servern är ogiltiga. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar gör du följande:** <br> Försök att installera skyddsagenten manuellt på produktionsservern enligt vad som anges i [den här artikeln.](/system-center/dpm/deploy-dpm-protection-agent)|
| Azure Backup Agent kunde inte ansluta till Azure Backup (ID: 100050) | Agenten Azure Backup kunde inte ansluta till Azure Backup tjänsten. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar gör du följande:** <br>1. Kör följande kommando från en upphöjd kommandotolk: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. Då öppnas Internet Explorer fönster. <br/> 2. Gå till **Verktyg**  >  **InternetalternativAnslutningar**  >    >  **LAN-inställningar**. <br/> 3. Ändra inställningarna för att använda en proxyserver. Ange sedan information om proxyservern.<br/> 4. Om datorn har begränsad Internetåtkomst ska du kontrollera att brandväggsinställningarna på datorn eller proxyservern tillåter dessa [URL:er](install-mars-agent.md#verify-internet-access) och [IP-adresser.](install-mars-agent.md#verify-internet-access)|
| Azure Backup Agentinstallationen misslyckades | Installationen Microsoft Azure Recovery Services misslyckades. Alla ändringar som har gjorts i systemet av Microsoft Azure Recovery Services-installationen återställdes. (ID: 4024) | Installera Azure Agent manuellt.

## <a name="configuring-protection-group"></a>Konfigurera skyddsgrupp

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera skyddsgrupper | DPM kunde inte räkna upp programkomponenten på den skyddade datorn (namnet på den skyddade datorn). | Välj **Uppdatera** på skärmen konfigurera skyddsgruppens användargränssnitt på relevant datakälla/komponentnivå. |
| Konfigurera skyddsgrupper | Kan inte konfigurera skydd | Om den skyddade servern är en SQL-server kontrollerar du att rollbehörigheterna för sysadmin har angetts till systemkontot (NTAuthority\System) på den skyddade datorn enligt beskrivningen i den här [artikeln](/system-center/dpm/back-up-sql-server).
| Konfigurera skyddsgrupper | Det finns inte tillräckligt med ledigt utrymme i lagringspoolen för den här skyddsgruppen. | Diskarna som läggs till i lagringspoolen [får inte innehålla en partition](/system-center/dpm/create-dpm-protection-groups). Ta bort alla befintliga volymer på diskarna. Lägg sedan till dem i lagringspoolen.|
| Principändring |Det gick inte att ändra säkerhetskopieringsprincipen. Fel: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x29834]. Försök igen efter en stund. Kontakta Microsofts supportavdelning om problemet kvarstår. | **Orsak:**<br/>Det här felet inträffar under tre villkor: när säkerhetsinställningar är aktiverade, när du försöker minska kvarhållningsintervallet under de minimivärden som angavs tidigare och när du har en version som inte stöds. (Versioner som inte stöds är de som är lägre än Microsoft Azure Backup Server version 2.0.9052 och Azure Backup Server uppdatering 1.) <br/>**Rekommenderad åtgärd:**<br/> Om du vill fortsätta med principrelaterade uppdateringar anger du kvarhållningsperioden över den minsta kvarhållningsperiod som angetts. (Den minsta kvarhållningsperioden är sju dagar per dag, fyra veckor för varje vecka, tre veckor per månad eller ett år för varje år.) <br><br>En annan bra metod är att uppdatera säkerhetskopieringsagenten och Azure Backup Server att utnyttja alla säkerhetsuppdateringar. |

## <a name="backup"></a>Backup

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Ett oväntat fel uppstod när jobbet kördes. Enheten är inte klar. | **Om den rekommenderade åtgärden som visas i produkten inte fungerar gör du följande:** <br> <ul><li>Ange skuggkopielagringsutrymmet till obegränsat utrymme för objekten i skyddsgruppen och kör sedan konsekvenskontrollen.<br></li> (OR) <li>Prova att ta bort den befintliga skyddsgruppen och skapa flera nya grupper. Varje ny skyddsgrupp bör ha ett enskilt objekt i den.</li></ul> |
| Backup | Om du endast säkerhetskopierar systemtillståndet kontrollerar du att det finns tillräckligt med ledigt utrymme på den skyddade datorn för att lagra säkerhetskopian av systemtillståndet. | <ol><li>Kontrollera att Windows Server Backup är installerad på den skyddade datorn.</li><li>Kontrollera att det finns tillräckligt med utrymme på den skyddade datorn för systemtillståndet. Det enklaste sättet att kontrollera detta är att gå till den skyddade datorn, öppna Windows Server Backup, klicka dig igenom valen och sedan välja BMR. Användargränssnittet visar sedan hur mycket utrymme som krävs. Öppna schema för lokal säkerhetskopiering av **WSB**  >    >    >  **Välj säkerhetskopieringskonfiguration**  >  **Fullständig server** (storlek visas). Använd den här storleken för verifiering.</li></ol>
| Backup | Fel vid återställning av BMR | Om BMR-storleken är stor flyttar du vissa programfiler till OS-enheten och försöker igen. |
| Backup | Alternativet att återaktivera skyddet av en virtuell VMware-dator på en Microsoft Azure Backup Server visas inte som tillgängligt att lägga till. | VMware-egenskaper pekar på en gammal, tillbakadragen instans av Microsoft Azure Backup Server. Lös problemet så här:<br><ol><li>I VCenter (SC-VMM-motsvarighet) går du till **fliken** Sammanfattning och sedan **till Anpassade attribut.**</li>  <li>Ta bort det Microsoft Azure Backup servernamnet från **DPMServer-värdet.**</li>  <li>Gå tillbaka till den nya Microsoft Azure Backup server och ändra PG.  När du har valt **knappen** Uppdatera visas den virtuella datorn med en kryssruta som tillgänglig för att lägga till i skyddet.</li></ol> |
| Backup | Fel vid åtkomst till filer/delade mappar | Prova att ändra antivirusinställningarna enligt vad som föreslås i den här [artikeln Kör antivirusprogram på DPM-servern.](/system-center/dpm/run-antivirus-server)|

## <a name="change-passphrase"></a>Ändra lösenfras

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Ändra lösenfras |Säkerhets-PIN-koden som angavs är felaktig. Ange rätt PIN-kod för säkerhet för att slutföra den här åtgärden. |**Orsak:**<br/> Det här felet uppstår när du anger en ogiltig eller utgången säkerhets-PIN-kod när du utför en kritisk åtgärd (till exempel att ändra en lösenfras). <br/>**Rekommenderad åtgärd:**<br/> För att slutföra åtgärden måste du ange en giltig PIN-kod för säkerhet. Hämta PIN-koden genom att logga in på Azure Portal och gå till Recovery Services-valvet. Gå sedan till Inställningar **Egenskaper**  >  **Generera**  >  **säkerhets-PIN-kod.** Använd den här PIN-koden för att ändra lösenfrasen. |
| Ändra lösenfras |Åtgärden misslyckades. ID: 120002 |**Orsak:**<br/>Det här felet uppstår när säkerhetsinställningar är aktiverade eller när du försöker ändra lösenfrasen när du använder en version som inte stöds.<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösenfrasen måste du först uppdatera säkerhetskopieringsagenten till den lägsta versionen, som är 2.0.9052. Du måste också uppdatera Azure Backup Server till minst uppdatering 1 och sedan ange en giltig PIN-kod för säkerhet. För att hämta PIN-koden loggar du in Azure Portal och går till Recovery Services-valvet. Gå sedan till Inställningar **Egenskaper**  >  **Generera**  >  **säkerhets-PIN-kod.** Använd den här PIN-koden för att ändra lösenfrasen. |

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera e-postaviseringar med ett arbets- eller skolkonto |Fel-ID: 2013| **Orsak:**<br> Försöker använda ett arbets- eller skolkonto <br>**Rekommenderad åtgärd:**<ol><li> Det första du ska se till är att "Tillåt Anonym relä på en mottagningsanslutning" för DPM-servern har ställts in på Exchange. Mer information om hur du konfigurerar detta finns i [Allow Anonymous Relay on a Receive Connector](/exchange/mail-flow/connectors/allow-anonymous-relay).</li> <li> Om du inte kan använda ett internt SMTP-relä och behöver konfigurera det med hjälp av din Office 365-server kan du konfigurera IIS som ett relä. Konfigurera DPM-servern att vidarebefordra [SMTP till Office 365 med hjälp av IIS](/exchange/mail-flow/test-smtp-with-telnet).<br><br>  Se till att använda \@ användarformatet  domain.com inte domän\användare.<br><br><li>Peka DPM för att använda det lokala servernamnet som SMTP-server, port 587. Peka den sedan till användarens e-postadress som e-postmeddelandena ska komma från.<li> Användarnamnet och lösenordet på DPM SMTP-konfigurationssidan ska vara för ett domänkonto i domänen där DPM finns. </li><br> När du ändrar SMTP-serveradressen gör du ändringen i de nya inställningarna, stänger inställningsrutan och öppnar den igen för att vara säker på att den återspeglar det nya värdet.  Att bara ändra och testa kanske inte alltid får de nya inställningarna att gälla, så det är bästa praxis att testa det på det här sättet.<br><br>Du kan när som helst under den här processen rensa inställningarna genom att stänga DPM-konsolen och redigera följande registernycklar: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Ta bort SMTPPassword- och SMTPUserName-nycklar.** Du kan lägga till dem i användargränssnittet igen när du startar det igen.

## <a name="common-issues"></a>Vanliga problem

Det här avsnittet beskriver vanliga fel som kan uppstå när du använder Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Felmeddelande | Rekommenderad åtgärd |
-- | --
Säkerhetskopieringen misslyckades eftersom disksäkerhetskopieringsrepliken är ogiltig eller saknas. | Lös problemet genom att kontrollera stegen nedan och försök igen: <br/> 1. Skapa en diskåterställningspunkt<br/> 2. Kör konsekvenskontroll på datakällan <br/> 3. Stoppa skyddet av datakällan och konfigurera om skyddet för den här datakällan

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Felmeddelande | Rekommenderad åtgärd |
-- | --
Ögonblicksbilden av källvolymen misslyckades eftersom metadata på repliken är ogiltiga. | Skapa en diskåterställningspunkt för den här datakällan och försök att säkerhetskopiera online igen

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Felmeddelande | Rekommenderad åtgärd |
-- | --
Ögonblicksbilden av källvolymen misslyckades på grund av inkonsekvent datakällreplik. | Kör en konsekvenskontroll på den här datakällan och försök igen

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Felmeddelande | Rekommenderad åtgärd |
-- | --
Det gick inte att säkerhetskopiera eftersom disksäkerhetskopieringsrepliken inte kunde klonas.| Kontrollera att alla tidigare replikfiler för disksäkerhetskopiering (.vhdx) är demonterade och att ingen säkerhetskopiering från disk till disk pågår under onlinesäkerhetskopior
