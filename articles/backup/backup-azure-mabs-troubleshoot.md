---
title: Felsöka Azure Backup Server
description: Felsöka installation, registrering av Azure Backup Server och säkerhets kopiering och återställning av program arbets belastningar.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 54b7295eaed5f04a118cf5097ebc7b25b18f67d2
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88522852"
---
# <a name="troubleshoot-azure-backup-server"></a>Felsöka Azure Backup Server

Använd informationen i följande tabeller för att felsöka fel som uppstår när du använder Azure Backup Server.

## <a name="basic-troubleshooting"></a>Grundläggande felsökning

Vi rekommenderar att du utför verifieringen nedan innan du börjar felsöka Microsoft Azure Backup Server (MABS):

- [Se till att Microsoft Azure Recovery Services (MARS) Agent är uppdaterad](https://go.microsoft.com/fwlink/?linkid=229525&clcid=0x409)
- [Se till att det finns en nätverks anslutning mellan MARS-agenten och Azure](./backup-azure-mars-troubleshoot.md#the-microsoft-azure-recovery-service-agent-was-unable-to-connect-to-microsoft-azure-backup)
- Kontrollera att Microsoft Azure Recovery Services körs (i tjänstkonsolen). Om det behövs startar du om och försöker igen
- [Kontrollera att det finns 5–10 % ledigt utrymme i den tillfälliga mappen](./backup-azure-file-folder-backup-faq.md#whats-the-minimum-size-requirement-for-the-cache-folder)
- Om registreringen inte fungerar kontrollerar du att den server som du försöker installera Azure Backup Server inte redan har registrerats med ett annat valv
- Kontrollera om DPM-agenten redan finns om push-överföringen misslyckas. Om den gör det avinstallerar du agenten och provar att installera igen
- [Se till att inga andra processer eller antivirusprogram stör Azure Backup](./backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-another-process-or-antivirus-software-interfering-with-azure-backup)<br>
- Se till att SQL Agent-tjänsten körs och är inställd på automatisk i MABS-servern<br>

## <a name="configure-antivirus-for-mabs-server"></a>Konfigurera antivirus program för MABS-Server

MABS är kompatibelt med de flesta populära antivirus program produkter. Vi rekommenderar följande steg för att undvika konflikter:

1. **Inaktivera real tids övervakning** – inaktivera real tids övervakning av antivirus programmet för följande:
    - `C:\Program Files<MABS Installation path>\XSD` projektbevakningsmappen
    - `C:\Program Files<MABS Installation path>\Temp` projektbevakningsmappen
    - Enhets bokstav för Modern Backup Storage volym
    - Replik-och överförings loggar: du kan göra detta genom att inaktivera real tids övervakning av **dpmra.exe**, som finns i mappen `Program Files\Microsoft Azure Backup Server\DPM\DPM\bin` . Prestanda i real tid försämras, eftersom antivirus programmet söker igenom replikerna varje gången MABS synkroniseras med den skyddade servern och söker igenom alla berörda filer varje gången MABS tillämpar ändringar på replikerna.
    - Administratörs konsol: om du vill undvika påverkan på prestanda inaktiverar du real tids övervakning av **csc.exe** processen. **csc.exe** processen är C \# -kompilatorn och övervakning i real tid kan försämra prestandan eftersom antivirus programmet söker igenom filer som **csc.exe** processen avger när de genererar XML-meddelanden. **CSC.exe** finns i följande sökvägar:
        - `\Windows\Microsoft.net\Framework\v2.0.50727\csc.exe`
        - `\Windows\Microsoft.NET\Framework\v4.0.30319\csc.exe`
    - För att MARS-agenten ska installeras på MABS-servern rekommenderar vi att du undantar följande filer och platser:
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\bin\cbengine.exe` som en process
        - `C:\Program Files\Microsoft Azure Backup Server\DPM\MARS\Microsoft Azure Recovery Services Agent\folder`
        - Arbets plats (om du inte använder standard platsen)
2. **Inaktivera real tids övervakning på den skyddade servern**: inaktivera real tids övervakning av **dpmra.exe**, som finns i mappen `C:\Program Files\Microsoft Data Protection Manager\DPM\bin` , på den skyddade servern.
3. **Konfigurera antivirus program vara för att ta bort de infekterade filerna på skyddade servrar och Mabs-servern**: för att förhindra skadade data från repliker och återställnings punkter konfigurerar du antivirus programmet att ta bort infekterade filer i stället för att automatiskt rengöra eller sätta dem. Automatisk rensning och sätta kan orsaka att antivirus programmet ändrar filer, och gör ändringar som MABS inte kan identifiera.

Du bör köra en manuell synkronisering med konsekvens. Kontrol lera jobbet varje gången antivirus programmet tar bort en fil från repliken, även om repliken markeras som inkonsekvent.

### <a name="mabs-installation-folders"></a>MABS-installationsfiler

Standardmappen för installation av DPM är följande:

- `C:\Program Files\Microsoft Azure Backup Server\DPM\DPM`

Du kan också köra följande kommando för att hitta sökvägen till installationsmappen:

```cmd
Reg query "HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Setup"
```

## <a name="invalid-vault-credentials-provided"></a>Ogiltiga valvautentiseringsuppgifter har angetts

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Registrera till ett valv | Ogiltiga valvautentiseringsuppgifter har angetts. Filen är skadad eller har inte de senaste autentiseringsuppgifterna som är associerade med återställnings tjänsten. | Rekommenderad åtgärd: <br> <ul><li> Hämta den senaste inloggnings filen från valvet och försök igen. <br>ELLER</li> <li> Om föregående åtgärd inte fungerade kan du försöka att hämta autentiseringsuppgifterna till en annan lokal katalog eller skapa ett nytt valv. <br>ELLER</li> <li> Försök att uppdatera datum-och tids inställningarna enligt beskrivningen i [den här artikeln](./backup-azure-mars-troubleshoot.md#invalid-vault-credentials-provided). <br>ELLER</li> <li> Kontrol lera om c:\Windows\Temp har fler än 65000 filer. Flytta inaktuella filer till en annan plats eller ta bort objekten i Temp-mappen. <br>ELLER</li> <li> Kontrol lera status för certifikat. <br> a. Öppna **Hantera dator certifikat** (på kontroll panelen). <br> b. Expandera den **personliga** noden och dess underordnade Node- **certifikat**.<br> c.  Ta bort certifikatet **Windows Azure-verktyg**. <br> d. Gör om registreringen i Azure Backup-klienten. <br> ELLER </li> <li> Kontrol lera om någon grup princip finns på plats. </li></ul> |

## <a name="replica-is-inconsistent"></a>Repliken är inkonsekvent

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Repliken är inkonsekvent | Kontrol lera att alternativet automatisk konsekvens kontroll i guiden skydds grupp är aktiverat. Mer information om replikeringsalternativ och konsekvens kontroller finns i [den här artikeln](/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019) .<br> <ol><li> Kontrol lera att Windows Server Backup är installerat på den skyddade servern i händelse av system tillstånd/BMR säkerhets kopiering.</li><li> Kontrol lera om det finns problem med problem i DPM-lagringspoolen på DPM/Microsoft Azure Backup-servern och allokera lagrings utrymme efter behov.</li><li> Kontrol lera status för tjänsten Volume Shadow Copy på den skyddade servern. Om den är i ett inaktiverat tillstånd ställer du in den så att den startar manuellt. Starta tjänsten på-servern. Gå sedan tillbaka till DPM/Microsoft Azure Backup Server-konsolen och starta synkroniseringen med konsekvens kontroll jobbet.</li></ol>|

## <a name="online-recovery-point-creation-failed"></a>Det gick inte att skapa återställningspunkten online

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Det gick inte att skapa återställningspunkten online | **Fel meddelande**: Windows Azure Backup-agenten kunde inte skapa en ögonblicks bild av den valda volymen. <br> **Lösning**: försök att öka utrymmet på replik-och återställnings punkt volymen.<br> <br> **Fel meddelande**: Windows Azure Backup-agenten kan inte ansluta till OBEngine-tjänsten <br> **Lösning**: kontrol lera att OBEngine finns i listan över aktiva tjänster på datorn. Om OBEngine-tjänsten inte körs använder du kommandot "net start OBEngine" för att starta tjänsten OBEngine. <br> <br> **Fel meddelande**: krypterings lösen frasen för den här servern har inte angetts. Konfigurera en krypterings lösen fras. <br> **Lösning**: försök att konfigurera en krypterings lösen fras. Gör så här om det Miss lyckas: <br> <ol><li>Kontrol lera att arbets platsen finns. Detta är den plats som nämns i registret **HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config**, med namnet **ScratchLocation** ska finnas.</li><li> Försök att registrera igen med hjälp av den gamla lösen frasen om det finns en sådan. *När du konfigurerar en krypterings lösen fras sparar du den på en säker plats.*</li><ol>|

## <a name="the-original-and-external-dpm-servers-must-be-registered-to-the-same-vault"></a>De ursprungliga och externa DPM-servrarna måste vara registrerade på samma valv

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Återställ | **Felkod**: CBPServerRegisteredVaultDontMatchWithCurrent/valv autentiseringsuppgifter fel: 100110 <br/> <br/>**Fel meddelande**: de ursprungliga och externa DPM-servrarna måste vara registrerade på samma valv | **Orsak**: det här problemet uppstår när du försöker återställa filer till den alternativa servern från den ursprungliga servern med hjälp av alternativet extern DPM-återställning och om servern som återställs och den ursprungliga servern från vilken data säkerhets kopie ras inte är kopplad till samma Recovery Service-valv.<br/> <br/>**Lösning** För att lösa det här problemet ser du till att både den ursprungliga och den alternativa servern är registrerad i samma valv.|

## <a name="online-recovery-point-creation-jobs-for-vmware-vm-fail"></a>Det gick inte att skapa jobb för onlineåterställningspunkt för VMware VM

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Det gick inte att skapa jobb för onlineåterställningspunkt för VMware VM. DPM påträffade ett fel från VMware vid försök att hämta ChangeTracking-information. ErrorCode-FileFaultFault (ID 33621) |  <ol><li> Återställ CTK på VMware för de virtuella datorerna.</li> <li>Kontrol lera att den oberoende disken inte finns på plats i VMware.</li> <li>Stoppa skyddet för de virtuella datorerna och återaktivera skyddet med knappen **Uppdatera** . </li><li>Kör en kopia av de virtuella datorerna.</li></ol>|

## <a name="the-agent-operation-failed-because-of-a-communication-error-with-the-dpm-agent-coordinator-service-on-the-server"></a>Agent åtgärden misslyckades på grund av ett kommunikations fel med tjänsten DPM agent Coordinator på servern

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Push-överför agent (er) till skyddade servrar | Agent åtgärden misslyckades på grund av ett kommunikations fel med den DPM-agentkoordinator tjänsten på \<ServerName> . | **Om den rekommenderade åtgärden som visas i produkten inte fungerar utför du följande steg**: <ul><li> Följ [dessa steg](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains?view=sc-dpm-2019)om du kopplar en dator från en obetrodd domän. <br> ELLER </li><li> Om du ansluter en dator från en betrodd domän kan du felsöka med hjälp av stegen som beskrivs i [den här bloggen](https://techcommunity.microsoft.com/t5/system-center-blog/data-protection-manager-agent-network-troubleshooting/ba-p/344726). <br>ELLER</li><li> Försök att inaktivera antivirus som fel söknings steg. Om det löser problemet ändrar du inställningarna för antivirus programmet enligt rekommendationerna i [den här artikeln](/system-center/dpm/run-antivirus-server?view=sc-dpm-2019).</li></ul> |

## <a name="setup-could-not-update-registry-metadata"></a>Det gick inte att uppdatera metadata för registret

| Åtgärd | Felinformation | Lösning |
|-----------|---------------|------------|
|Installation | Det gick inte att uppdatera metadata för registret. Det här uppdaterings felet kan leda till överförbrukning av lagrings förbrukning. För att undvika detta uppdaterar du register posten ReFS-trimning. | Justera register nyckeln **SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim**. Ange värdet DWORD till 1. |
|Installation | Det gick inte att uppdatera metadata för registret. Det här uppdaterings felet kan leda till överförbrukning av lagrings förbrukning. Undvik detta genom att uppdatera register posten Volume SnapOptimization. | Skapa register nyckeln **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** med ett tomt sträng värde. |

## <a name="registration-and-agent-related-issues"></a>Registrerings-och Agent-relaterade problem

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Push-överför agent (er) till skyddade servrar | De angivna autentiseringsuppgifterna för servern är ogiltiga. | **Utför följande steg om den rekommenderade åtgärden som visas i produkten inte fungerar**: <br> Försök att installera skydds agenten manuellt på produktions servern enligt vad som anges i [den här artikeln](/system-center/dpm/deploy-dpm-protection-agent?view=sc-dpm-2019).|
| Azure Backup-agenten kunde inte ansluta till Azure Backups tjänsten (ID: 100050) | Azure Backup-agenten kunde inte ansluta till Azure Backups tjänsten. | **Utför följande steg om den rekommenderade åtgärden som visas i produkten inte fungerar**: <br>1. Kör följande kommando från en upphöjd prompt: **PsExec-i-s "c:\program\internet Explorer\iexplore.exe**. Fönstret Internet Explorer öppnas. <br/> 2. gå till **verktyg**  >  **Internet alternativ**  >  **anslutningar**  >  **LAN-inställningar**. <br/> 3. ändra inställningarna om du vill använda en proxyserver. Ange sedan information om proxyservern.<br/> 4. om datorn har begränsad Internet åtkomst kontrollerar du att brand Väggs inställningarna på datorn eller proxyservern tillåter dessa [URL: er](install-mars-agent.md#verify-internet-access) och [IP-adress](install-mars-agent.md#verify-internet-access).|
| Det gick inte att installera Azure Backup Agent | Det gick inte att installera Microsoft Azure Recovery Services. Alla ändringar som har gjorts i systemet av den Microsoft Azure Recovery Services installationen återställdes. (ID: 4024) | Installera Azure-agenten manuellt.

## <a name="configuring-protection-group"></a>Konfigurerar skydds grupp

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera skydds grupper | DPM kunde inte räkna upp program komponenten på den skyddade datorn (skyddat dator namn). | Välj **Uppdatera** på skärmen konfigurera skydds gruppens användar gränssnitt på den relevanta data källan/komponent nivån. |
| Konfigurera skydds grupper | Kan inte konfigurera skydd | Om den skyddade servern är en SQL-Server kontrollerar du att sysadmin-rollens behörigheter har angetts till system kontot (NTAuthority\System) på den skyddade datorn enligt beskrivningen i [den här artikeln](/system-center/dpm/back-up-sql-server?view=sc-dpm-2019).
| Konfigurera skydds grupper | Det finns inte tillräckligt med ledigt utrymme i lagringspoolen för den här skydds gruppen. | Diskarna som läggs till i lagringspoolen [får inte innehålla en partition](/system-center/dpm/create-dpm-protection-groups?view=sc-dpm-2019). Ta bort alla befintliga volymer på diskarna. Lägg sedan till dem i lagringspoolen.|
| Princip ändring |Det gick inte att ändra säkerhets kopierings principen. Fel: den aktuella åtgärden kunde inte utföras på grund av ett internt tjänst fel [0x29834]. Försök igen om en stund. Kontakta Microsofts supportavdelning om problemet kvarstår. | **Orsak**<br/>Det här felet uppstår under tre förhållanden: när säkerhets inställningarna är aktiverade kan du, när du försöker minska kvarhållningsintervallet under de lägsta värdena som angetts tidigare, och när du använder en version som inte stöds. (Versioner som inte stöds är de nedan Microsoft Azure Backup Server version 2.0.9052 och Azure Backup Server uppdatering 1.) <br/>**Rekommenderad åtgärd:**<br/> Om du vill fortsätta med principbaserad uppdateringar anger du kvarhållningsperioden ovanför den minsta kvarhållningsperioden som angetts. (Den minsta Retentions perioden är sju dagar i fyra veckor för varje vecka, tre veckor för varje månad eller ett år för varje år.) <br><br>Alternativt är det en annan prioriterad metod att uppdatera säkerhets kopierings agenten och Azure Backup Server för att utnyttja alla säkerhets uppdateringar. |

## <a name="backup"></a>Backup

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Backup | Ett oväntat fel uppstod när jobbet kördes. Enheten är inte klar. | **Utför följande steg om den rekommenderade åtgärden som visas i produkten inte fungerar:** <br> <ul><li>Ange lagrings utrymmet för skugg kopian till obegränsade för objekten i skydds gruppen och kör sedan konsekvens kontrollen.<br></li> ELLER <li>Försök att ta bort den befintliga skydds gruppen och skapa flera nya grupper. Varje ny skydds grupp måste ha ett enskilt objekt.</li></ul> |
| Backup | Om du bara säkerhetskopierar system tillstånd kontrollerar du att det finns tillräckligt med ledigt utrymme på den skyddade datorn för att lagra säkerhets kopian av system tillstånd. | <ol><li>Kontrol lera att Windows Server Backup är installerat på den skyddade datorn.</li><li>Kontrol lera att det finns tillräckligt med utrymme på den skyddade datorn för system tillstånd. Det enklaste sättet att kontrol lera detta är att gå till den skyddade datorn, öppna Windows Server Backup, klicka igenom valen och sedan välja BMR. Användar gränssnittet visar sedan hur mycket utrymme som krävs. Öppna **WSB**, lokal säkerhets kopiering schema för säkerhets kopiering  >  **Local backup**  >  **Backup schedule**  >  **Välj säkerhets kopierings konfiguration**  >  **fullständig Server** (storleken visas). Använd den här storleken för verifiering.</li></ol>
| Backup | Säkerhets kopierings problem för BMR | Om storleken på BMR är stor flyttar du några programfiler till OS-enheten och försöker igen. |
| Backup | Alternativet för att skydda en virtuell VMware-dator på en ny Microsoft Azure Backup Server visas inte som tillgänglig för att lägga till. | VMware-egenskaper pekas på en gammal, tillbakadragen instans av Microsoft Azure Backup Server. Lös problemet så här:<br><ol><li>I VCenter (SC-VMM-motsvarighet) går du till fliken **Sammanfattning** och sedan till **anpassade attribut**.</li>  <li>Ta bort det gamla Microsoft Azure Backup Server namnet från **Dpmserver** -värdet.</li>  <li>Gå tillbaka till den nya Microsoft Azure Backup servern och ändra PG.  När du har valt knappen **Uppdatera** visas den virtuella datorn med en kryss ruta som tillgänglig för att lägga till i skyddet.</li></ol> |
| Backup | Fel vid åtkomst till filer/delade mappar | Försök att ändra Antivirus inställningarna enligt rekommendationerna i den här artikeln [köra antivirus program på DPM-servern](/system-center/dpm/run-antivirus-server?view=sc-dpm-2019).|

## <a name="change-passphrase"></a>Ändra lösen fras

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Ändra lösen fras |Den angivna säkerhets koden är felaktig. Ange rätt säkerhets kod för att slutföra åtgärden. |**Orsak**<br/> Det här felet uppstår när du anger en ogiltig eller utgången säkerhets-PIN-kod när du utför en kritisk åtgärd (till exempel ändra en lösen fras). <br/>**Rekommenderad åtgärd:**<br/> Du måste ange en giltig PIN-kod för att slutföra åtgärden. Logga in på Azure Portal och gå till Recovery Services-valvet för att få PIN-koden. Gå sedan till **Inställningar**  >  **Egenskaper**  >  **skapa säkerhets kod**. Använd den här PIN-koden för att ändra lösen frasen. |
| Ändra lösen fras |Åtgärden misslyckades. ID: 120002 |**Orsak**<br/>Felet uppstår när säkerhets inställningar är aktiverade, eller när du försöker ändra lösen frasen när du använder en version som inte stöds.<br/>**Rekommenderad åtgärd:**<br/> Om du vill ändra lösen frasen måste du först uppdatera säkerhets kopierings agenten till den lägsta versionen, som är 2.0.9052. Du måste också uppdatera Azure Backup Server till minst uppdatering 1 och sedan ange en giltig PIN-kod för säkerhet. Logga in på Azure Portal och gå till Recovery Services-valvet för att få PIN-koden. Gå sedan till **Inställningar**  >  **Egenskaper**  >  **skapa säkerhets kod**. Använd den här PIN-koden för att ändra lösen frasen. |

## <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

| Åtgärd | Felinformation | Lösning |
| --- | --- | --- |
| Konfigurera e-postaviseringar med ett Office 365-konto |Fel-ID: 2013| **Orsak**<br> Försöker använda Office 365-konto <br>**Rekommenderad åtgärd:**<ol><li> Det första du ser är att "Tillåt anonym vidarebefordran på en mottagnings anslutning" för DPM-servern har kon figurer ATS på Exchange. Mer information om hur du konfigurerar detta finns i [Tillåt anonymt relä på en Receive-anslutning](/exchange/mail-flow/connectors/allow-anonymous-relay?view=exchserver-2019).</li> <li> Om du inte kan använda en intern SMTP-relä och behöver konfigurera med hjälp av din Office 365-Server kan du konfigurera IIS att vara ett relä. Konfigurera DPM-servern så att [den vidarebefordrar SMTP till O365 med IIS](/exchange/mail-flow/test-smtp-with-telnet?view=exchserver-2019)).<br><br>  Se till att använda formatet User \@ Domain.com och *inte* domain\user.<br><br><li>Punkt DPM använder det lokala server namnet som SMTP-server, port 587. Peka sedan på det e-postmeddelande som e-postmeddelandena ska komma från.<li> Användar namnet och lösen ordet på sidan DPM SMTP-installation ska vara ett domän konto i den domän där DPM finns. </li><br> När du ändrar SMTP-serveradressen gör du ändringen av de nya inställningarna, stänger rutan inställningar och öppnar den igen för att vara säker på att den motsvarar det nya värdet.  Att bara ändra och testa kanske inte alltid gör att de nya inställningarna börjar gälla, så testa det på det sättet som är bäst.<br><br>Du kan när som helst under den här processen rensa de här inställningarna genom att stänga DPM-konsolen och redigera följande register nycklar: **HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> ta bort SMTPPassword och SMTPUserName-nycklar**. Du kan lägga tillbaka dem i användar gränssnittet när du startar det igen.

## <a name="common-issues"></a>Vanliga problem

I det här avsnittet beskrivs vanliga fel som kan uppstå när du använder Azure Backup Server.

### <a name="cbpsourcesnapshotfailedreplicamissingorinvalid"></a>CBPSourceSnapshotFailedReplicaMissingOrInvalid

Felmeddelande | Rekommenderad åtgärd |
-- | --
Säkerhetskopieringen misslyckades eftersom disksäkerhetskopieringsrepliken är ogiltig eller saknas. | Lös problemet genom att kontrol lera stegen nedan och försök igen: <br/> 1. skapa en disk återställnings punkt<br/> 2. kör konsekvens kontroll på data källan <br/> 3. stoppa skyddet av data källan och konfigurera sedan om skyddet för den här data källan

### <a name="cbpsourcesnapshotfailedreplicametadatainvalid"></a>CBPSourceSnapshotFailedReplicaMetadataInvalid

Felmeddelande | Rekommenderad åtgärd |
-- | --
Ögonblicks bilden av käll volymen misslyckades eftersom metadata på repliken är ogiltiga. | Skapa en disk återställnings punkt för data källan och försök att säkerhetskopiera online igen

### <a name="cbpsourcesnapshotfailedreplicainconsistent"></a>CBPSourceSnapshotFailedReplicaInconsistent

Felmeddelande | Rekommenderad åtgärd |
-- | --
Ögonblicks bilden av käll volymen misslyckades på grund av inkonsekvent DataSource-replik. | Kör en konsekvens kontroll på den här data källan och försök igen

### <a name="cbpsourcesnapshotfailedreplicacloningissue"></a>CBPSourceSnapshotFailedReplicaCloningIssue

Felmeddelande | Rekommenderad åtgärd |
-- | --
Det gick inte att säkerhetskopiera eftersom disksäkerhetskopieringsrepliken inte kunde klonas.| Se till att alla tidigare disk säkerhets kopierings repliker (. vhdx) har demonterats och att ingen disk till disk säkerhets kopiering pågår under säkerhets kopiering online
