---
title: Felsök säkerhets kopierings fel med virtuella Azure-datorer
description: I den här artikeln får du lära dig hur du felsöker fel som påträffas med säkerhets kopiering och återställning av virtuella Azure-datorer.
ms.reviewer: srinathv
ms.topic: troubleshooting
ms.date: 08/30/2019
ms.openlocfilehash: 2cda13ea089ac08dff7c1ba5ca93ba56ab3c23cf
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831558"
---
# <a name="troubleshooting-backup-failures-on-azure-virtual-machines"></a>Felsöka säkerhets kopierings fel på virtuella Azure-datorer

Du kan felsöka fel som uppstår när du använder Azure Backup med informationen i listan nedan:

## <a name="backup"></a>Backup

Det här avsnittet beskriver felet vid säkerhets kopiering av virtuella Azure-datorer.

### <a name="basic-troubleshooting"></a>Grundläggande felsökning

* Se till att VM-agenten (WA-agenten) är den [senaste versionen](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* Se till att Windows eller Linux VM OS-versionen stöds, se [IaaS VM backup support Matrix](./backup-support-matrix-iaas.md).
* Kontrol lera att ingen annan säkerhets kopierings tjänst körs.
  * För att se till att det inte finns några ögonblicks bilds tillägg måste [du avinstallera tillägg för att framtvinga inläsning och sedan försöka säkerhetskopiera igen](./backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md).
* Kontrol lera att den virtuella datorn är ansluten till Internet.
  * Kontrol lera att en annan säkerhets kopierings tjänst inte körs.
* Från `Services.msc` , se till att tjänsten **Windows Azure gästa Gent** **körs**. Om tjänsten **Windows Azure gästa Gent** saknas installerar du den från [säkerhetskopiera virtuella Azure-datorer i ett Recovery Services valv](./backup-azure-arm-vms-prepare.md#install-the-vm-agent).
* **Händelse loggen** kan visa säkerhets kopierings problem som kommer från andra säkerhets kopierings produkter, t. ex. Windows Server Backup, och inte på grund av Azure Backup. Använd följande steg för att fastställa om problemet är med Azure Backup:
  * Om det uppstår ett fel med **säkerhets kopian** av posten i händelse källan eller meddelandet kontrollerar du om säkerhets kopieringen av Azure IaaS VM-säkerhetskopiering lyckades och om en återställnings punkt skapades med den önskade ögonblicks bild typen.
  * Om Azure Backup fungerar är problemet troligt vis en annan lösning för säkerhets kopiering.
  * Här är ett exempel på ett Loggboken fel 517 där Azure Backup fungerade bra men "Windows Server Backup" misslyckades: ![ Windows Server Backup misslyckades](media/backup-azure-vms-troubleshoot/windows-server-backup-failing.png)
  * Om Azure Backup inte fungerar söker du efter motsvarande felkod i avsnittet Vanliga fel vid säkerhets kopiering av virtuella datorer i den här artikeln.

## <a name="common-issues"></a>Vanliga problem

Följande är vanliga problem med säkerhets kopierings fel på virtuella Azure-datorer.

### <a name="vmrestorepointinternalerror---antivirus-configured-in-the-vm-is-restricting-the-execution-of-backup-extension"></a>VMRestorePointInternalError-Antivirus konfigurationen på den virtuella datorn begränsar körningen av säkerhets kopierings tillägget

Felkod: VMRestorePointInternalError

Om du vid tidpunkten för säkerhets kopieringen visar **Loggboken-programloggarna** visas meddelandets **fel program namn: IaaSBcdrExtension.exe** sedan bekräftas att antivirus programmet som kon figurer ATS i den virtuella datorn begränsar körningen av säkerhets kopierings tillägget.
Lös problemet genom att undanta katalogerna nedan i Antivirus konfigurationen och försök att säkerhetskopiera igen.

* `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`
* `C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot`

### <a name="copyingvhdsfrombackupvaulttakinglongtime---copying-backed-up-data-from-vault-timed-out"></a>Tids gränsen nåddes under CopyingVHDsFromBackUpVaultTakingLongTime av säkerhetskopierade data från valvet

Felkod: CopyingVHDsFromBackUpVaultTakingLongTime <br/>
Fel meddelande: det gick inte att kopiera säkerhetskopierade data från valvet

Detta kan inträffa på grund av tillfälliga lagrings fel eller otillräckligt lagrings konto IOPS för säkerhets kopierings tjänsten för att överföra data till valvet inom tids gränsen. Konfigurera säkerhets kopiering av virtuella datorer med dessa [metod tips](backup-azure-vms-introduction.md#best-practices) och försök att säkerhetskopiera igen.

### <a name="usererrorvmnotindesirablestate---vm-is-not-in-a-state-that-allows-backups"></a>UserErrorVmNotInDesirableState – Den virtuella datorn är i ett tillstånd som inte tillåter säkerhetskopieringar

Felkod: UserErrorVmNotInDesirableState <br/>
Fel meddelande: den virtuella datorn är inte i ett tillstånd som tillåter säkerhets kopieringar.<br/>

Säkerhets kopierings åtgärden misslyckades eftersom den virtuella datorn är i ett felaktigt tillstånd. För att säkerhetskopieringen ska lyckas måste tillståndet för den virtuella datorn vara Körs, Stoppad eller Stoppad (frigjord).

* Om den virtuella datorn är i ett tillfälligt tillstånd mellan att **köra** och **stänga** av, väntar du tills status har ändrats. Utlös sedan säkerhets kopierings jobbet.
* Om den virtuella datorn är en virtuell Linux-dator och använder modulen Security-Enhanced Linux-kernel, utelämnar du sökvägen **/var/lib/waagent** för Azure Linux-agenten från säkerhets principen och kontrollerar att säkerhets kopierings tillägget är installerat.

### <a name="usererrorfsfreezefailed---failed-to-freeze-one-or-more-mount-points-of-the-vm-to-take-a-file-system-consistent-snapshot"></a>UserErrorFsFreezeFailed – det gick inte att låsa en eller flera monterings punkter för den virtuella datorn för att ta en konsekvent fil system ögonblicks bild

Felkod: UserErrorFsFreezeFailed <br/>
Fel meddelande: det gick inte att låsa en eller flera monterings punkter för den virtuella datorn för att ta en konsekvent fil system ögonblicks bild.

* Demontera enheterna för vilka fil systemets tillstånd inte har rensats, med kommandot **umount** .
* Kör en konsekvens kontroll av fil systemet på dessa enheter med kommandot **fsck** .
* Montera enheterna igen och försök att säkerhetskopiera igen.</ol>

Om du inte kan demontera enheterna kan du uppdatera konfigurationen för den virtuella datorns säkerhets kopiering så att vissa monterings punkter ignoreras. Om t. ex. "/mnt/Resource"-monterings punkten inte kan avmonteras och orsakar säkerhets kopieringen av virtuella datorer, kan du uppdatera konfigurationsfilerna för säkerhets kopiering av virtuella datorer med ```MountsToSkip``` egenskapen på följande sätt.

```bash
cat /var/lib/waagent/Microsoft.Azure.RecoveryServices.VMSnapshotLinux-1.0.9170.0/main/tempPlugin/vmbackup.conf[SnapshotThread]
fsfreeze: True
MountsToSkip = /mnt/resource
SafeFreezeWaitInSeconds=600
```


### <a name="extensionsnapshotfailedcom--extensioninstallationfailedcom--extensioninstallationfailedmdtc---extension-installationoperation-failed-due-to-a-com-error"></a>ExtensionSnapshotFailedCOM/ExtensionInstallationFailedCOM/ExtensionInstallationFailedMDTC-Extension-installationen/åtgärden misslyckades på grund av ett COM+-fel

Felkod: ExtensionSnapshotFailedCOM <br/>
Fel meddelande: ögonblicks bild åtgärden misslyckades på grund av ett COM+-fel

Felkod: ExtensionInstallationFailedCOM  <br/>
Fel meddelande: tilläggs installation/åtgärd misslyckades på grund av ett COM+-fel

Felkod: ExtensionInstallationFailedMDTC <br/>
Fel meddelande: tilläggs installationen misslyckades med felet "COM+ kunde inte kommunicera med Microsoft koordinator för distribuerad transaktion <br/>

Säkerhets kopieringen misslyckades på grund av ett problem med Windows-tjänstens **com+-system** program.  Följ dessa anvisningar för att lösa problemet:

* Försök starta/starta om Windows-tjänsten **com+-systemprogram** (från en upphöjd kommando tolk **-net start COMSysApp**).
* Se till att **koordinator för distribuerad transaktion** -tjänsten körs som **nätverks tjänst** konto. Om inte, ändrar du den så att den körs som **nätverks tjänst** konto och startar om **com+-system programmet**.
* Om du inte kan starta om tjänsten installerar du om **koordinator för distribuerad transaktion** tjänsten genom att följa stegen nedan:
  * Stoppa MSDTC-tjänsten
  * Öppna en kommandotolk (cmd)
  * Kör kommandot `msdtc -uninstall`
  * Kör kommandot `msdtc -install`
  * Starta MSDTC-tjänsten
* Starta Windows-tjänsten **com+-system tillämpning**. När **com+-system programmet** startar utlöser du ett säkerhets kopierings jobb från Azure Portal.</ol>

### <a name="extensionfailedvsswriterinbadstate---snapshot-operation-failed-because-vss-writers-were-in-a-bad-state"></a>ExtensionFailedVssWriterInBadState – Åtgärden för ögonblicksbild misslyckades eftersom VSS-skrivarna var i ett felaktigt tillstånd

Felkod: ExtensionFailedVssWriterInBadState <br/>
Fel meddelande: ögonblicks bild åtgärden misslyckades eftersom VSS-skrivare befann sig i ett felaktigt tillstånd.

Felet beror på att VSS-skribenterna är i ett felaktigt tillstånd. Azure Backup-tilläggen interagerar med VSS-skrivare för att ta ögonblicks bilder av diskarna. Följ dessa anvisningar för att lösa problemet:

Steg 1: Starta om VSS-skrivare som är i ett felaktigt tillstånd.

* Kör i en upphöjd kommando tolk ```vssadmin list writers``` .
* Utdata innehåller alla VSS-skrivare och deras tillstånd. Starta om VSS-skrivartjänsten för alla VSS-skrivare som inte har ett **[1] stabilt** tillstånd.
* Du kan starta om tjänsten genom att köra följande kommandon från en upphöjd kommandotolk: 

 ```net stop serviceName``` <br>
 ```net start serviceName```

> [!NOTE]
> Att starta om vissa tjänster kan påverka produktions miljön. Se till att godkännande processen följs och att tjänsten startas om vid schemalagd stillestånds tid.

Steg 2: om problemet inte löstes med att starta om VSS-skrivare kör du följande kommando från en upphöjd kommando tolk (som administratör) för att förhindra att trådarna skapas för BLOB-ögonblicksbilder.

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotWithoutThreads /t REG_SZ /d True /f
```

Steg 3: om det inte gick att lösa problemet med steg 1 och 2 kan felet bero på att tids gränsen för VSS-skrivare orsakade timeout på grund av begränsade IOPS.<br>

Kontrol lera genom att gå till ***system och Loggboken program loggar** _ och kontrol lera följande fel meddelande:<br>
Tids gränsen nåddes för _The Shadow Copy Provider vid lagring av skrivning till volymen som skugg kopie ras. Detta beror troligen på överdriven aktivitet på volymen av ett program eller en system tjänst. Försök igen senare när aktivitet på volymen minskar. *<br>

Lösning:

* Sök efter möjligheter att distribuera belastningen på de virtuella dator diskarna. Detta minskar belastningen på enskilda diskar. Du kan [kontrol lera IOPS-begränsningen genom att aktivera diagnostiska mått på lagrings nivå](../virtual-machines/troubleshooting/performance-diagnostics.md#install-and-run-performance-diagnostics-on-your-vm).
* Ändra säkerhets kopierings policyn för att utföra säkerhets kopieringar under låg belastnings tider, när belastningen på den virtuella datorn är den lägsta.
* Uppgradera Azure-diskarna för att stödja högre IOPs. [Läs mer här](../virtual-machines/disks-types.md)

### <a name="extensionfailedvssserviceinbadstate---snapshot-operation-failed-due-to-vss-volume-shadow-copy-service-in-bad-state"></a>ExtensionFailedVssServiceInBadState – Det gick inte att ta en ögonblicksbild eftersom VSS-tjänsten (Volume Shadow Copy) är i felaktigt tillstånd

Felkod: ExtensionFailedVssServiceInBadState <br/>
Fel meddelande: det gick inte att utföra ögonblicks bilder på grund av att tjänsten VSS (Volume Shadow Copy) är i felaktigt tillstånd.

Felet beror på att VSS-tjänsten har ett felaktigt tillstånd. Azure Backup tilläggen interagerar med VSS-tjänsten för att ta ögonblicks bilder av diskarna. Följ dessa anvisningar för att lösa problemet:

Starta om tjänsten VSS (Volume Shadow Copy).

* Gå till Services.msc och starta om tjänsten Volume Shadow Copy.<br>
(eller)<br>
* Kör följande kommando från en kommandorad med förhöjd behörighet:

 ```net stop VSS``` <br>
 ```net start VSS```

Om problemet kvarstår startar du om den virtuella datorn vid schemalagd stilleståndstid.

### <a name="usererrorskunotavailable---vm-creation-failed-as-vm-size-selected-is-not-available"></a>UserErrorSkuNotAvailable-det gick inte att skapa den virtuella datorn eftersom den valda virtuella dator storleken inte är tillgänglig

Felkod: UserErrorSkuNotAvailable-fel meddelande: det gick inte att skapa en virtuell dator eftersom den valda virtuella dator storleken inte är tillgänglig.

Felet beror på att storleken på den virtuella datorn som valts under återställnings åtgärden inte stöds. <br>

Lös problemet genom att använda alternativet för att [återställa diskar](./backup-azure-arm-restore-vms.md#restore-disks) under återställnings åtgärden. Använd diskarna för att skapa en virtuell dator i listan över [tillgängliga VM-storlekar som stöds](./backup-support-matrix-iaas.md#vm-compute-support) med hjälp av PowerShell- [cmdletar](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

### <a name="usererrormarketplacevmnotsupported---vm-creation-failed-due-to-market-place-purchase-request-being-not-present"></a>UserErrorMarketPlaceVMNotSupported-det gick inte att skapa den virtuella datorn på grund av att begäran om marknads plats köp saknas

Felkod: UserErrorMarketPlaceVMNotSupported-fel meddelande: det gick inte att skapa en virtuell dator på grund av att begäran om marknads plats köp saknas.

Azure Backup stöder säkerhets kopiering och återställning av virtuella datorer som är tillgängliga i Azure Marketplace. Det här felet uppstår när du försöker återställa en virtuell dator (med en angiven inställning för plan/utgivare) som inte längre är tillgänglig på Azure Marketplace. [Läs mer här](/legal/marketplace/participation-policy#offering-suspension-and-removal).

* Lös problemet genom att använda alternativet för att [återställa diskar](./backup-azure-arm-restore-vms.md#restore-disks) under återställningen och sedan använda [PowerShell](./backup-azure-vms-automation.md#create-a-vm-from-restored-disks) -eller [Azure CLI](./tutorial-restore-disk.md) -CMDLETAR för att skapa den virtuella datorn med den senaste Marketplace-informationen som motsvarar den virtuella datorn.
* Om utgivaren inte har någon Marketplace-information kan du använda data diskarna för att hämta dina data och du kan koppla dem till en befintlig virtuell dator.

### <a name="extensionconfigparsingfailure--failure-in-parsing-the-config-for-the-backup-extension"></a>ExtensionConfigParsingFailure – det gick inte att parsa konfigurationen för säkerhets kopierings tillägget

Felkod: ExtensionConfigParsingFailure<br/>
Fel meddelande: det gick inte att parsa konfigurationen för säkerhets kopierings tillägget.

Det här felet inträffar på grund av ändrade behörigheter i **MachineKeys** -katalogen: **%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys**.
Kör följande kommando och kontrol lera att behörigheterna för **MachineKeys** -katalogen är standardvärden: `icacls %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys` .

Standard behörigheterna är följande:

* Alla: (R, W)
* BUILTIN\Administrators: (F)

Om du ser behörigheter i **MachineKeys** -katalogen som skiljer sig från standardvärdena följer du dessa steg för att åtgärda behörighet, ta bort certifikatet och utlösa säkerhets kopieringen:

1. Åtgärda behörigheter för **MachineKeys** -katalogen. Genom att använda säkerhets egenskaper i Utforskaren och avancerade säkerhets inställningar i katalogen återställer du behörighet tillbaka till standardvärdena. Ta bort alla användar objekt utom standardvärdena från katalogen och se till att behörigheten **alla** har särskild åtkomst enligt följande:

   * Visa mapp/läsa data
   * Läsa attribut
   * Läsa utökade attribut
   * Skapa filer/skriva data
   * Skapa mappar/lägga till data
   * Skriva attribut
   * Skriva utökade attribut
   * Läs behörigheter
2. Ta bort alla certifikat som har **utfärdats till** är den klassiska distributions modellen eller **Windows Azure CRP Certificate Generator**:

   * [Öppna certifikat på en lokal dator konsol](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in).
   * Under **personliga**  >  **certifikat** kan du ta bort alla certifikat som har **utfärdats till** är den klassiska distributions modellen eller **Windows Azure CRP Certificate Generator**.
3. Utlös ett jobb för säkerhets kopiering av virtuella datorer.

### <a name="extensionstuckindeletionstate---extension-state-is-not-supportive-to-backup-operation"></a>ExtensionStuckInDeletionState-Extension-tillstånd stöds inte för säkerhets kopiering

Felkod: ExtensionStuckInDeletionState <br/>
Fel meddelande: det går inte att använda tilläggs tillstånd för säkerhets kopiering

Säkerhets kopierings åtgärden misslyckades på grund av ett inkonsekvent tillstånd för säkerhets kopierings tillägget. Följ dessa anvisningar för att lösa problemet:

* Kontrollera att gästagenten är installerad och svarar.
* Från Azure Portal går du till **virtuella datorer**  >  **alla inställningar**  >  **tillägg**
* Välj säkerhets kopierings tillägget VmSnapshot eller VmSnapshotLinux och välj **Avinstallera**.
* Försök att säkerhetskopiera igen efter att du har tagit bort säkerhets kopierings tillägget
* Vid nästföljande säkerhetskopiering installeras det nya tillägget med önskat tillstånd.

### <a name="extensionfailedsnapshotlimitreachederror---snapshot-operation-failed-as-snapshot-limit-is-exceeded-for-some-of-the-disks-attached"></a>ExtensionFailedSnapshotLimitReachedError-åtgärden misslyckades eftersom gränsen för ögonblicks bilder har överskridits för vissa av diskarna

Felkod: ExtensionFailedSnapshotLimitReachedError  <br/>
Fel meddelande: ögonblicks bild åtgärden misslyckades eftersom ögonblicks bilds gränsen har överskridits för vissa av diskarna som är anslutna

Det gick inte att utföra ögonblicks bild åtgärden eftersom gränsen för ögonblicks bilder har överskridits för vissa av de anslutna diskarna. Slutför följande fel söknings steg och försök sedan igen.

* Ta bort disk-BLOB-ögonblicksbilder som inte behövs. Var noga med att inte ta bort disk blobbar. Det är bara ögonblicks bilds blobbar som ska tas bort.
* Om mjuk borttagning är aktiverat på VM disk Storage-konton, konfigurerar du kvarhållning av kvarhållning så att befintliga ögonblicks bilder är mindre än det högsta tillåtna antalet vid någon tidpunkt.
* Om Azure Site Recovery har Aktiver ATS i den säkerhetskopierade virtuella datorn utför du stegen nedan:

  * Se till att värdet för **isanysnapshotfailed** är inställt på falskt i/etc/Azure/vmbackup.conf
  * Schemalägg Azure Site Recovery vid en annan tidpunkt, så att den inte hamnar i konflikt med säkerhets kopierings åtgärden.

### <a name="extensionfailedtimeoutvmnetworkunresponsive---snapshot-operation-failed-due-to-inadequate-vm-resources"></a>ExtensionFailedTimeoutVMNetworkUnresponsive-åtgärden misslyckades på grund av otillräckliga VM-resurser

Felkod: ExtensionFailedTimeoutVMNetworkUnresponsive<br/>
Fel meddelande: ögonblicks bild åtgärden misslyckades på grund av otillräckliga VM-resurser.

Det gick inte att säkerhetskopiera den virtuella datorn på grund av fördröjning i nätverks anrop medan ögonblicks bild åtgärden utfördes. Du löser problemet genom att utföra steg 1. Om problemet kvarstår kan du prova steg 2 och 3.

**Steg 1**: skapa en ögonblicks bild via värden

Kör följande kommando från en utökad (admin) kommando tolk:

```console
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v SnapshotMethod /t REG_SZ /d firstHostThenGuest /f
REG ADD "HKLM\SOFTWARE\Microsoft\BcdrAgentPersistentKeys" /v CalculateSnapshotTimeFromHost /t REG_SZ /d True /f
```

Detta säkerställer att ögonblicksbilderna tas via värden i stället för gästen. Försök att utföra säkerhetskopieringsåtgärden igen.

**Steg 2**: försök att ändra schemat för säkerhets kopiering till en tidpunkt då den virtuella datorn är under mindre belastning (t. ex. mindre processor eller IOPS)

**Steg 3**: försök att [öka storleken på den virtuella datorn](../virtual-machines/windows/resize-vm.md) och försök igen

### <a name="320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found"></a>320001, ResourceNotFound-det gick inte att utföra åtgärden eftersom den virtuella datorn inte längre finns/400094, BCMV2VMNotFound-den virtuella datorn finns inte/ingen virtuell Azure-dator hittades

Felkod: 320001, ResourceNotFound <br/> Fel meddelande: det gick inte att utföra åtgärden eftersom den virtuella datorn inte längre finns. <br/> <br/> Felkod: 400094, BCMV2VMNotFound <br/> Fel meddelande: den virtuella datorn finns inte <br/>
Det gick inte att hitta någon virtuell Azure-dator.

Felet uppstår när den primära virtuella datorn tas bort, men säkerhets kopierings principen söker fortfarande efter en virtuell dator som ska säkerhets kopie ras. Åtgärda felet genom att utföra följande steg:

* Återskapa den virtuella datorn med samma namn och samma resurs grupp namn, **moln tjänst namn**,<br>eller
* Sluta skydda den virtuella datorn med eller utan att ta bort säkerhetskopierade data. Mer information finns i [stoppa skyddet av virtuella datorer](backup-azure-manage-vms.md#stop-protecting-a-vm).</li></ol>

### <a name="usererrorbcmpremiumstoragequotaerror---could-not-copy-the-snapshot-of-the-virtual-machine-due-to-insufficient-free-space-in-the-storage-account"></a>UserErrorBCMPremiumStorageQuotaError – det gick inte att kopiera ögonblicks bilden av den virtuella datorn eftersom det inte finns tillräckligt med ledigt utrymme på lagrings kontot

Felkod: UserErrorBCMPremiumStorageQuotaError<br/> Fel meddelande: det gick inte att kopiera ögonblicks bilden av den virtuella datorn eftersom det inte finns tillräckligt med ledigt utrymme på lagrings kontot

 För Premium-VM: ar på VM-säkerhetskopiering stack v1 kopieras ögonblicks bilden till lagrings kontot. Det här steget ser till att säkerhets kopierings hanterings trafiken, som fungerar på ögonblicks bilden, inte begränsar antalet IOPS som är tillgängligt för programmet med hjälp av Premium diskar. <br><br>Vi rekommenderar att du endast tilldelar 50 procent, 17,5 TB, av det totala lagrings konto utrymmet. Sedan kan Azure Backups tjänsten kopiera ögonblicks bilden till lagrings kontot och överföra data från den här kopierade platsen i lagrings kontot till valvet.

### <a name="380008-azurevmoffline---failed-to-install-microsoft-recovery-services-extension-as-virtual-machine--is-not-running"></a>380008, AzureVmOffline-det gick inte att installera Microsoft Recovery Services-tillägget eftersom den virtuella datorn inte körs

Felkod: 380008, AzureVmOffline <br/> Fel meddelande: det gick inte att installera Microsoft Recovery Services-tillägget eftersom den virtuella datorn inte körs

VM-agenten är en förutsättning för Azure Recovery Services-tillägget. Installera agenten för den virtuella Azure-datorn och starta om registrerings åtgärden. <br> <ol> <li>Kontrol lera att den virtuella dator agenten är korrekt installerad. <li>Kontrol lera att flaggan på VM-konfigurationen är korrekt inställd.</ol> Läs mer om hur du installerar VM-agenten och hur du verifierar installationen av VM-agenten.

### <a name="extensionsnapshotbitlockererror---the-snapshot-operation-failed-with-the-volume-shadow-copy-service-vss-operation-error"></a>ExtensionSnapshotBitlockerError-åtgärden för ögonblicks bilden misslyckades med felet tjänsten Volume Shadow Copy (VSS)

Felkod: ExtensionSnapshotBitlockerError <br/> Fel meddelande: det gick inte att utföra ögonblicks bild åtgärden med den tjänsten Volume Shadow Copy (VSS) åtgärds fel **enheten är låst av BitLocker-diskkryptering. Du måste låsa upp den här enheten från kontroll panelen.**

Inaktivera BitLocker för alla enheter på den virtuella datorn och kontrol lera om problemet med VSS är löst.

### <a name="vmnotindesirablestate---the-vm-isnt-in-a-state-that-allows-backups"></a>VmNotInDesirableState – den virtuella datorn är inte i ett tillstånd som tillåter säkerhets kopieringar

Felkod: VmNotInDesirableState <br/> Fel meddelande: den virtuella datorn är inte i ett tillstånd som tillåter säkerhets kopieringar.

* Om den virtuella datorn är i ett tillfälligt tillstånd mellan att **köra** och **stänga** av, väntar du tills status har ändrats. Utlös sedan säkerhets kopierings jobbet.
* Om den virtuella datorn är en virtuell Linux-dator och använder modulen Security-Enhanced Linux-kernel, utelämnar du sökvägen **/var/lib/waagent** för Azure Linux-agenten från säkerhets principen och kontrollerar att säkerhets kopierings tillägget är installerat.

* VM-agenten finns inte på den virtuella datorn: <br>Installera eventuella nödvändiga komponenter och VM-agenten. Starta sedan om åtgärden. | Läs mer om [installation av VM-agenten och hur du verifierar installationen av VM-agenten](#vm-agent).

### <a name="extensionsnapshotfailednosecurenetwork---the-snapshot-operation-failed-because-of-failure-to-create-a-secure-network-communication-channel"></a>ExtensionSnapshotFailedNoSecureNetwork-ögonblicks bild åtgärden misslyckades på grund av att det inte gick att skapa en säker kanal för nätverks kommunikation

Felkod: ExtensionSnapshotFailedNoSecureNetwork <br/> Fel meddelande: ögonblicks bild åtgärden misslyckades på grund av att det inte gick att skapa en säker kanal för nätverkskommunikation.

* Öppna Registereditorn genom att köra **regedit.exe** i förhöjd läge.
* Identifiera alla versioner av .NET Framework som finns i systemet. De finns under hierarkin för register nyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft**.
* Lägg till följande nyckel för varje .NET Framework som finns i register nyckeln: <br> **SchUseStrongCrypto "= DWORD: 00000001**. </ol>

### <a name="extensionvcredistinstallationfailure---the-snapshot-operation-failed-because-of-failure-to-install-visual-c-redistributable-for-visual-studio-2012"></a>ExtensionVCRedistInstallationFailure-ögonblicks bild åtgärden misslyckades på grund av att det inte gick att installera Visual C++ Redistributable för Visual Studio 2012

Felkod: ExtensionVCRedistInstallationFailure <br/> Fel meddelande: ögonblicks bild åtgärden misslyckades på grund av att det inte gick att installera Visual C++ Redistributable för Visual Studio 2012.

* Navigera till `C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion` och installera vcredist2013_x64.<br/>Kontrol lera att värdet för register nyckeln som tillåter tjänst installationen har värdet korrekt. Det vill säga ange **startvärdet** i **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver** till **3** och inte **4**. <br><br>Om du fortfarande har problem med installationen startar du om installations tjänsten genom att köra **msiexec/unregister** följt av **msiexec/register** från en upphöjd kommando tolk.
* Kontrol lera händelse loggen för att kontrol lera om du har märker åtkomst till relaterade problem. Exempel: *Product: Microsoft Visual C++ 2013 x64 minimal körning-12.0.21005--Error 1401. Det gick inte att skapa nyckeln: Software\Classes.  Systemfel 5.  Kontrol lera att du har tillräcklig åtkomst till nyckeln eller kontakta en support tekniker.* <br><br> Se till att administratören eller användar kontot har tillräcklig behörighet för att uppdatera register nyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Classes**. Ange tillräckliga behörigheter och starta om Windows Azures gästa Gent.<br><br> <li> Om du har antivirus produkter på plats ser du till att de har rätt undantags regler för att tillåta installationen.

### <a name="usererrorrequestdisallowedbypolicy---an-invalid-policy-is-configured-on-the-vm-which-is-preventing-snapshot-operation"></a>UserErrorRequestDisallowedByPolicy – En ogiltig princip har konfigurerats på den virtuella datorn vilket förhindrar att åtgärden utförs för ögonblicksbilden

Felkod: UserErrorRequestDisallowedByPolicy <BR> Fel meddelande: en ogiltig princip har kon figurer ATS på den virtuella datorn som förhindrar ögonblicks bild åtgärden.

Om du har en Azure Policy som [styr Taggar i din miljö](../governance/policy/tutorials/govern-tags.md)kan du antingen överväga att ändra principen från en [neka](../governance/policy/concepts/effects.md#deny) -förändring till en [ändrings funktion](../governance/policy/concepts/effects.md#modify)eller skapa resurs gruppen manuellt enligt det [namngivnings schema som krävs av Azure Backup](./backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines).

## <a name="jobs"></a>Jobb

| Felinformation | Lösning |
| --- | --- |
| Annullering stöds inte för den här jobb typen: <br>Vänta tills jobbet har slutförts. |Ingen |
| Jobbet är inte i ett cancelable-tillstånd: <br>Vänta tills jobbet har slutförts. <br>**eller**<br> Det valda jobbet är inte i ett cancelable-tillstånd: <br>Vänta tills jobbet har slutförts. |Det är troligt att jobbet är nästan klart. Vänta tills jobbet är klart.|
| Säkerhets kopieringen kan inte avbryta jobbet eftersom det inte pågår: <br>Annullering stöds bara för pågående jobb. Försök att avbryta ett pågående jobb. |Felet beror på ett överförings tillstånd. Vänta en minut och försök att avbryta åtgärden igen. |
| Säkerhets kopieringen kunde inte avbryta jobbet: <br>Vänta tills jobbet har slutförts. |Ingen |

## <a name="restore"></a>Återställ

### <a name="disks-appear-offline-after-file-restore"></a>Diskar visas offline efter fil återställning

Om du efter att ha återställt ser du till att diskarna är offline.

* Kontrol lera att datorn där skriptet körs uppfyller operativ system kraven. [Läs mer](./backup-azure-restore-files-from-vm.md#step-3-os-requirements-to-successfully-run-the-script).  
* Se till att du inte återställer till samma källa, [Läs mer](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script).

### <a name="usererrorinstantrpnotfound---restore-failed-because-the-snapshot-of-the-vm-was-not-found"></a>UserErrorInstantRpNotFound-återställningen misslyckades eftersom ögonblicks bilden av den virtuella datorn inte hittades

Felkod: UserErrorInstantRpNotFound <br>
Fel meddelande: återställningen misslyckades eftersom det inte gick att hitta ögonblicks bilden av den virtuella datorn. Ögonblicks bilden kan ha tagits bort, kontrol lera.<br>

Det här felet uppstår när du försöker återställa från en återställnings punkt som inte överförts till valvet och som har tagits bort i ögonblicks bild fasen. 
<br>
Försök att återställa den virtuella datorn från en annan återställnings punkt för att lösa problemet.<br>

#### <a name="common-errors"></a>Vanliga fel 
| Felinformation | Lösning |
| --- | --- |
| Återställningen misslyckades med ett internt moln fel. |<ol><li>Den moln tjänst som du försöker återställa till har kon figurer ATS med DNS-inställningar. Du kan kontrol lera följande: <br>**$Deployment = Get-AzureDeployment-ServiceName "ServiceName"-plats "produktion" Get-AzureDns-DnsSettings $Deployment. DnsSettings**.<br>Om **adress** har kon figurer ATS konfigureras DNS-inställningarna.<br> <li>Den moln tjänst som du försöker återställa till har kon figurer ATS med **reservedip** och befintliga virtuella datorer i moln tjänsten är i stoppat läge. Du kan kontrol lera att en moln tjänst har reserverat en IP-adress med hjälp av följande PowerShell-cmdlets: **$Deployment = Get-AzureDeployment-ServiceName "ServiceName"-plats "produktion" $DEP. ReservedIPName**. <br><li>Du försöker återställa en virtuell dator med följande särskilda nätverkskonfigurationer i samma moln tjänst: <ul><li>Virtuella datorer under belastnings Utjämnings konfiguration, intern och extern.<li>Virtuella datorer med flera reserverade IP-adresser. <li>Virtuella datorer med flera nätverkskort. </ul><li>Välj en ny moln tjänst i användar gränssnittet eller se [återställnings överväganden](backup-azure-arm-restore-vms.md#restore-vms-with-special-configurations) för virtuella datorer med särskilda nätverkskonfigurationer.</ol> |
| Det valda DNS-namnet har redan tagits: <br>Ange ett annat DNS-namn och försök igen. |Det här DNS-namnet refererar till moln tjänstens namn, vanligt vis slutar med **. cloudapp.net**. Det här namnet måste vara unikt. Om du får det här felet måste du välja ett annat namn för virtuell dator under återställningen. <br><br> Det här felet visas endast för användare av Azure Portal. Återställnings åtgärden via PowerShell slutförs eftersom den återställer endast diskarna och inte skapar den virtuella datorn. Felet kommer att visas när den virtuella datorn skapas explicit av dig efter disk återställnings åtgärden. |
| Den angivna konfigurationen för virtuellt nätverk är felaktig: <br>Ange en annan konfiguration för virtuellt nätverk och försök igen. |Ingen |
| Den angivna moln tjänsten använder en reserverad IP-adress som inte matchar konfigurationen för den virtuella dator som återställs: <br>Ange en annan moln tjänst som inte använder en reserverad IP-adress. Eller Välj en annan återställnings punkt att återställa från. |Ingen |
| Moln tjänsten har nått gränsen för antalet ingångs slut punkter: <br>Försök igen genom att ange en annan moln tjänst eller genom att använda en befintlig slut punkt. |Ingen |
| Recovery Services valvet och mål lagrings kontot finns i två olika regioner: <br>Se till att det lagrings konto som anges i återställnings åtgärden finns i samma Azure-region som Recovery Services-valvet. |Ingen |
| Det lagrings konto som har angetts för återställnings åtgärden stöds inte: <br>Endast Basic-eller standard-lagrings konton med lokalt redundanta eller geo-redundanta replikeringsinställningar stöds. Välj ett lagrings konto som stöds. |Ingen |
| Den angivna lagrings konto typen för återställnings åtgärden är inte online: <br>Kontrol lera att lagrings kontot som angetts i återställnings åtgärden är online. |Det här felet kan inträffa på grund av ett tillfälligt fel i Azure Storage eller på grund av ett avbrott. Välj ett annat lagrings konto. |
| Resurs grupps kvoten har uppnåtts: <br>Ta bort några resurs grupper från Azure Portal eller kontakta Azure-supporten för att öka gränserna. |Ingen |
| Det valda under nätet finns inte: <br>Välj ett undernät som finns. |Ingen |
| Säkerhets kopierings tjänsten har inte behörighet att komma åt resurser i din prenumeration. |Lös problemet genom att först återställa diskarna genom att följa stegen i [återställa säkerhetskopierade diskar](backup-azure-arm-restore-vms.md#restore-disks). Använd sedan PowerShell-stegen i [skapa en virtuell dator från återställda diskar](backup-azure-vms-automation.md#restore-an-azure-vm). |

## <a name="backup-or-restore-takes-time"></a>Säkerhets kopiering eller återställning tar tid

Om säkerhets kopieringen tar mer än 12 timmar, eller om det tar längre tid än 6 timmar att återställa, kan du granska [metod tips](backup-azure-vms-introduction.md#best-practices)och [prestanda överväganden](backup-azure-vms-introduction.md#backup-performance)

## <a name="vm-agent"></a>VM-agent

### <a name="set-up-the-vm-agent"></a>Konfigurera VM-agenten

Normalt finns VM-agenten redan i virtuella datorer som skapas från Azure-galleriet. Men virtuella datorer som migreras från lokala data Center kommer inte att ha VM-agenten installerad. VM-agenten måste installeras explicit för de virtuella datorerna.

#### <a name="windows-vms---set-up-the-agent"></a>Virtuella Windows-datorer – Konfigurera agenten

* Ladda ned och installera [agentens MSI-fil](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Du måste ha administratörs behörighet för att slutföra installationen.
* För virtuella datorer som skapats med den klassiska distributions modellen [uppdaterar du egenskapen VM](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) för att ange att agenten är installerad. Det här steget krävs inte för Azure Resource Manager virtuella datorer.

#### <a name="linux-vms---set-up-the-agent"></a>Virtuella Linux-datorer – Konfigurera agenten

* Installera den senaste versionen av agenten från distributions platsen. Mer information om paket namnet finns i Linux- [agentens lagrings plats](https://github.com/Azure/WALinuxAgent).
* För virtuella datorer som skapats med den klassiska distributions modellen [uppdaterar du egenskapen VM](../virtual-machines/troubleshooting/install-vm-agent-offline.md#use-the-provisionguestagent-property-for-classic-vms) och kontrollerar att agenten är installerad. Det här steget krävs inte för virtuella Resource Manager-datorer.

### <a name="update-the-vm-agent"></a>Uppdatera VM-agenten

#### <a name="windows-vms---update-the-agent"></a>Virtuella Windows-datorer – uppdatera agenten

* Om du vill uppdatera VM-agenten installerar du om [binärfilerna för VM-agenten](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Innan du uppdaterar agenten ska du kontrol lera att inga säkerhets kopierings åtgärder sker under uppdateringen av VM-agenten.

#### <a name="linux-vms---update-the-agent"></a>Virtuella Linux-datorer – uppdatera agenten

* Uppdatera Linux VM-agenten genom att följa anvisningarna i artikeln [Uppdatera Linux VM-agenten](../virtual-machines/extensions/update-linux-agent.md?toc=/azure/virtual-machines/linux/toc.json).

    > [!NOTE]
    > Använd alltid distributions platsen för att uppdatera agenten.

    Hämta inte agent koden från GitHub. Om den senaste agenten inte är tillgänglig för din distribution kan du kontakta distributions supporten för instruktioner om hur du hämtar den senaste agenten. Du kan också kontrol lera den senaste informationen om [Windows Azure Linux-agenten](https://github.com/Azure/WALinuxAgent/releases) i GitHub-lagringsplatsen.

### <a name="validate-vm-agent-installation"></a>Verifiera installation av VM-agent

Verifiera VM-agentens version på virtuella Windows-datorer:

1. Logga in på den virtuella Azure-datorn och navigera till mappen **C:\WindowsAzure\Packages**. Du bör hitta **WaAppAgent.exe** -filen.
2. Högerklicka på filen och gå till **Egenskaper**. Välj fliken **information** . Fältet **produkt version** ska vara 2.6.1198.718 eller högre.

## <a name="troubleshoot-vm-snapshot-issues"></a>Felsöka problem med ögonblicksbilder av virtuella datorer

VM-säkerhetskopiering är beroende av utfärdande av ögonblicks bild kommandon för underliggande lagring. Om du inte har åtkomst till lagring eller fördröjningar i en ögonblicks bild aktivitet kan säkerhets kopierings jobbet inte köras. Följande villkor kan orsaka ögonblicks bild aktivitets fel:

* **Virtuella datorer med SQL Server säkerhets kopia konfigurerad kan orsaka aktivitets fördröjning i ögonblicks bilder**. Som standard skapar VM-säkerhetskopiering en fullständig VSS-säkerhetskopiering på virtuella Windows-datorer. Virtuella datorer som kör SQL Server, med SQL Server säkerhets kopiering konfigurerad, kan uppleva fördröjningar i ögonblicks bilder. Om ögonblicks bildernas fördröjning orsakar problem med säkerhets kopieringen anger du följande register nyckel:

   ```console
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```

* **VM-status rapporteras felaktigt eftersom den virtuella datorn stängs av i RDP**. Om du använde fjärr skrivbordet för att stänga av den virtuella datorn kontrollerar du att statusen för den virtuella datorn i portalen är korrekt. Om statusen inte är korrekt använder du alternativet **Stäng** av den virtuella portalens instrument panel för att stänga av den virtuella datorn.
* **Om fler än fyra virtuella datorer delar samma moln tjänst sprider du de virtuella datorerna över flera säkerhets kopierings principer**. Sprid säkerhets kopierings tiderna, så att inte fler än fyra VM-säkerhetskopieringar startar på samma gång. Försök att avgränsa start tiderna i principerna med minst en timme.
* **Den virtuella datorn körs med hög processor eller minne**. Om den virtuella datorn körs med hög minnes-eller CPU-användning, är mer än 90 procent av ögonblicks bilds aktiviteten i kö och fördröjd. Slutligen tids gränsen. Om det här problemet inträffar kan du prova en säkerhets kopiering på begäran.

## <a name="networking"></a>Nätverk

DHCP måste vara aktiverat i gästen för att IaaS VM-säkerhetskopiering ska fungera. Om du behöver en statisk privat IP-adress konfigurerar du den via Azure Portal eller PowerShell. Kontrol lera att DHCP-alternativet i den virtuella datorn är aktiverat.
Få mer information om hur du konfigurerar en statisk IP-adress med hjälp av PowerShell:

* [Så här lägger du till en statisk intern IP-adress till en befintlig virtuell dator](/powershell/module/az.network/set-aznetworkinterfaceipconfig#description)
* [Ändra tilldelnings metoden för en privat IP-adress som tilldelats till ett nätverks gränssnitt](../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface)