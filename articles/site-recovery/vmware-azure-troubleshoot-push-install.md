---
title: Felsöka mobilitets tjänsten push-installation med Azure Site Recovery
description: Felsök installations fel för mobilitets tjänster när du aktiverar replikering för haveri beredskap med Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.author: ramamill
ms.date: 04/03/2020
ms.openlocfilehash: 62c8240a4d2e50aa3b584f322baf7d2ee217c6d3
ms.sourcegitcommit: 02b1179dff399c1aa3210b5b73bf805791d45ca2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98127880"
---
# <a name="troubleshoot-mobility-service-push-installation"></a>Felsöka mobilitets tjänstens push-installation

Mobilitets tjänst installationen är ett viktigt steg för att aktivera replikering. Framgången av det här steget beror på Mötes krav och arbetar med konfigurationer som stöds. De vanligaste felen som du kanske möter under installationen av mobilitets tjänsten beror på följande:

* [Autentiseringsuppgifter/behörighets fel](#credentials-check-errorid-95107--95108)
* [Inloggnings försök](#login-failures-errorid-95519-95520-95521-95522)
* [Anslutningsfel](#connectivity-failure-errorid-95117--97118)
* [Fil-och skrivar delnings fel](#file-and-printer-sharing-services-check-errorid-95105--95106)
* [Windows Management Instrumentation (WMI) Miss lyckas](#windows-management-instrumentation-wmi-configuration-check-error-code-95103)
* [Operativsystem som inte stöds](#unsupported-operating-systems)
* [Startkonfigurationer som inte stöds](#unsupported-boot-disk-configurations-errorid-95309-95310-95311)
* [Installations problem för tjänsten Volume Shadow Copy (VSS)](#vss-installation-failures)
* [Enhets namn i GRUB-konfiguration i stället för enhets-UUID](#enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320)
* [LVM-volym (Logical Volume Manager)](#lvm-support-from-920-version)
* [Varningar om omstart](#install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266)

När du aktiverar replikering försöker Azure Site Recovery installera mobilitets tjänst agenten på den virtuella datorn (VM). Som en del av den här processen försöker konfigurations servern ansluta till den virtuella datorn och kopiera agenten. Följ steg-för-steg-fel söknings vägledningen för att aktivera installationen.

## <a name="credentials-check-errorid-95107--95108"></a>Kontroll av autentiseringsuppgifter (ErrorID: 95107 & 95108)

Kontrol lera att det användar konto som valdes under aktivera replikering är giltigt och korrekt. Azure Site Recovery kräver ett **rot** konto eller ett användar konto med **administratörs behörighet** för att utföra en push-installation. Annars kommer push-installationen att blockeras på käll datorn.

För Windows (**fel 95107**) kontrollerar du att användar kontot har administrativ åtkomst på käll datorn, antingen med ett lokalt konto eller domän konto. Om du inte använder ett domän konto måste du inaktivera åtkomst kontroll för fjärran vändare på den lokala datorn.

* För att manuellt lägga till en register nyckel som inaktiverar fjärran vändare åtkomst kontroll:

  * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
  * Lägg till en ny `DWORD` : `LocalAccountTokenFilterPolicy`
  * Ange värdet till `1`

* Om du vill lägga till register nyckeln kör du följande kommando från en kommando tolk:

  `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

För Linux (**fel 95108**) måste du välja **rot** kontot för lyckad installation av mobilitets tjänst agenten. Dessutom bör SSH-File Transfer Protocol (SFTP)-tjänster köras. Så här aktiverar du under systemet SFTP och lösenordsautentisering i _sshd_config_ -filen:

1. Logga in som **rot**.
1. Gå till _/etc/ssh/sshd_config-filen_ och leta upp raden som börjar med `PasswordAuthentication` .
1. Ta bort kommentaren till raden och ändra värdet till `yes` .
1. Hitta raden som börjar med och ta bort `Subsystem` kommentaren mellan raden.
1. Starta om `sshd` tjänsten.

Följ [dessa instruktioner](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)om du vill ändra autentiseringsuppgifterna för det valda användar kontot.

## <a name="insufficient-privileges-failure-errorid-95517"></a>Otillräckligt med behörighets problem (ErrorID: 95517)

När den användare som valts för att installera mobilitets agenten inte har administratörs behörighet får inte konfigurations servern/skalbar processervern att kopiera mobilitets agentens program vara till käll datorn. Det här felet är ett resultat av ett nekat åtkomst fel. Kontrol lera att användar kontot har administratörs behörighet.

Följ [dessa instruktioner](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)om du vill ändra autentiseringsuppgifterna för det valda användar kontot.

## <a name="insufficient-privileges-failure-errorid-95518"></a>Otillräckligt med behörighets problem (ErrorID: 95518)

När en domän förtroende Relations etablering mellan den primära domänen och arbets stationen Miss lyckas när du försöker logga in på käll datorn, Miss lyckas installationen av mobilitets agenten med fel-ID 95518. Se till att det användar konto som används för att installera mobilitets agenten har administratörs behörighet för att logga in via den primära domänen på käll datorn.

Följ [dessa instruktioner](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation)om du vill ändra autentiseringsuppgifterna för det valda användar kontot.

## <a name="login-failures-errorid-95519-95520-95521-95522"></a>Inloggnings försök (ErrorID: 95519, 95520, 95521, 95522)

I det här avsnittet beskrivs fel meddelanden om autentiseringsuppgifter och inloggning.

### <a name="credentials-of-the-user-account-have-been-disabled-errorid-95519"></a>Autentiseringsuppgifterna för användar kontot har inaktiverats (ErrorID: 95519)

Det användar konto som valdes under aktiveringen av replikering har inaktiverats. Om du vill aktivera användar kontot läser du [den här artikeln](/powershell/module/Microsoft.PowerShell.LocalAccounts/Enable-LocalUser) eller kör följande kommando genom att ersätta text _användar_ namn med det faktiska användar namnet.

`net user 'username' /active:yes`

### <a name="credentials-locked-out-because-of-multiple-failed-login-attempts-errorid-95520"></a>Autentiseringsuppgifterna har låsts på grund av flera misslyckade inloggnings försök (ErrorID: 95520)

Flera misslyckade försök att komma åt en dator kommer att låsa användar kontot. Felen kan bero på att:

* De autentiseringsuppgifter som angavs under konfigurations installationen är felaktiga.
* Det användar konto som valdes under aktiveringen av replikering är felaktigt.

Ändra de autentiseringsuppgifter som valts genom att följa [dessa anvisningar](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation) och försök igen.

### <a name="logon-servers-arent-available-on-the-source-machine-errorid-95521"></a>Inloggnings servrar är inte tillgängliga på käll datorn (ErrorID: 95521)

Felet uppstår när inloggnings servrarna inte är tillgängliga på käll datorn. Om inloggnings servrar inte är tillgängliga Miss känner inloggnings begär Anden och mobilitets agenten inte kan installeras. För en lyckad inloggning kontrollerar du att inloggnings servrar är tillgängliga på käll datorn och startar tjänsten Netlogon. Mer information finns i [scenarier med Windows-inloggning](/windows-server/security/windows-authentication/windows-logon-scenarios).

### <a name="logon-service-isnt-running-on-the-source-machine-errorid-95522"></a>Inloggnings tjänsten körs inte på käll datorn (ErrorID: 95522)

Inloggnings tjänsten körs inte på käll datorn och det uppstod ett problem med inloggnings förfrågan. Det går inte att installera mobilitets agenten. Lös problemet genom att använda någon av följande metoder för att starta `Netlogon` tjänsten på käll datorn:

* Starta `Netlogon` -tjänsten från en kommando tolk genom att köra kommandot `net start Netlogon` .
* Starta tjänsten från aktivitets hanteraren `Netlogon` .

## <a name="connectivity-failure-errorid-95117--97118"></a>Anslutnings problem (ErrorID: 95117 & 97118)

Konfigurations servern/Scale-Out-processervern försöker ansluta till den virtuella käll datorn för att installera mobilitets agenten. Felet uppstår när det inte går att hitta käll datorn på grund av problem med nätverks anslutningen.

Så här löser du felet:

* Kontrol lera att användar kontot har administrativ åtkomst på käll datorn, med antingen ett lokalt konto eller ett domän konto. Om du inte använder ett domän konto måste du inaktivera åtkomst kontroll för fjärran vändare på den lokala datorn.
  * För att manuellt lägga till en register nyckel som inaktiverar fjärran vändare åtkomst kontroll:
    * `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
    * Lägg till en ny `DWORD` : `LocalAccountTokenFilterPolicy`
    * Ange värdet till `1`
  * Om du vill lägga till register nyckeln kör du följande kommando från en kommando tolk:

    `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`

* Se till att du kan pinga käll datorn från konfigurations servern. Om du har valt den skalbara processervern under aktivera replikering, se till att du kan pinga käll datorn från processervern.

* Se till att fil-och skrivar delnings tjänsten är aktive rad på den virtuella datorn. Kontrol lera stegen [här](vmware-azure-troubleshoot-push-install.md#file-and-printer-sharing-services-check-errorid-95105--95106).

* Se till att WMI-tjänsten är aktive rad på den virtuella datorn. Kontrol lera stegen [här](vmware-azure-troubleshoot-push-install.md#windows-management-instrumentation-wmi-configuration-check-error-code-95103).

* Se till att delade nätverksmappar på den virtuella datorn är tillgängliga från processervern. Kontrol lera stegen [här](vmware-azure-troubleshoot-push-install.md#check-access-for-network-shared-folders-on-source-machine-errorid-9510595523).

* Från kommando raden för käll serverns dator använder `Telnet` du för att pinga konfigurations servern eller den skalbara processervern på https-port 135, som du ser i följande kommando. Det här kommandot kontrollerar om det finns problem med nätverks anslutningen eller blockerar brand Väggs porten.

  `telnet <CS/ scale-out PS IP address> <135>`

* För en virtuell Linux-dator:
  * Kontrol lera om de senaste OpenSSH-, OpenSSH-Server-och OpenSSL-paketen har installerats.
  * Kontrol lera och se till att SSH (Secure Shell) är aktiverat och körs på port 22.
  * SFTP-tjänster ska köras. Aktivera SFTP-undersystem och lösenordsautentisering i _sshd_config_ -filen:

    1. Logga in som **rot**.
    1. Gå till _/etc/ssh/sshd_config_ -filen och leta upp raden som börjar med `PasswordAuthentication` .
    1. Ta bort kommentaren till raden och ändra värdet till `yes` .
    1. Hitta raden som börjar med och ta bort `Subsystem` kommentar på raden
    1. Starta om `sshd` tjänsten.

* Ett anslutnings försök kan ha misslyckats om det inte finns några korrekta svar efter en viss tids period eller om en upprättad anslutning misslyckades eftersom en ansluten värd inte svarade.
* Det kan vara en anslutning/ett nätverks-/domän relaterat problem. Det kan också bero på att DNS-namn löser problemet eller att TCP-portens överbelastnings problem. Kontrol lera om det finns några kända problem i din domän.

## <a name="connectivity-failure-errorid-95523"></a>Anslutnings problem (ErrorID: 95523)

Felet uppstår när nätverket som käll datorn finns på inte hittas, kan ha tagits bort eller inte längre är tillgängligt. Det enda sättet att lösa felet är att se till att nätverket finns.

## <a name="check-access-for-network-shared-folders-on-source-machine-errorid-9510595523"></a>Kontrol lera åtkomsten för delade nätverks-mappar på käll datorn (ErrorID: 95105, 95523)

Kontrol lera att nätverks delade mappar på den virtuella datorn kan nås från processervern (PS) via fjärr anslutning med angivna autentiseringsuppgifter. För att bekräfta åtkomst: 

1. Logga in på datorn med processervern.
2. Öppna Utforskaren. I adress fältet skriver du `\\<SOURCE-MACHINE-IP>\C$` och klickar på RETUR.

    ![Öppna mappen i PS](./media/vmware-azure-troubleshoot-push-install/open-folder-process-server.PNG)

3. Utforskaren kommer att fråga efter autentiseringsuppgifter. Ange användar namn och lösen ord och klicka på OK. <br><br/>

    ![Ange autentiseringsuppgifter](./media/vmware-azure-troubleshoot-push-install/provide-credentials.PNG)

    >[!NOTE]
    > Om käll datorn är ansluten till en domän anger du domän namnet tillsammans med användar namnet som `<domainName>\<username>` . Om käll datorn finns i en arbets grupp anger du bara användar namnet.

4. Om anslutningen lyckas visas mapparna på käll datorn via fjärr anslutning från processervern.

    ![Synliga mappar från käll datorn](./media/vmware-azure-troubleshoot-push-install/visible-folders-from-source.png)

Om anslutningen Miss lyckas kontrollerar du om alla krav är uppfyllda.

## <a name="file-and-printer-sharing-services-check-errorid-95105--95106"></a>Kontroll av fil-och skrivar delnings tjänster (ErrorID: 95105 & 95106)

Efter en anslutnings kontroll kontrollerar du om fil-och skrivar delnings tjänsten är aktive rad på den virtuella datorn. De här inställningarna krävs för att kopiera mobilitets agenten till käll datorn.

För **Windows 2008 R2 och tidigare versioner**:

* Om du vill aktivera fil-och skrivar delning via Windows-brandväggen,
  1. Öppna **kontroll panelen**  >  **system och säkerhet**  >  **Windows-brandväggen**. I det vänstra fönstret väljer du **Avancerade inställningar**  >  **regler för inkommande trafik** i konsol trädet.
  1. Hitta regel fil och skrivar delning (NB-session-in) och fil-och skrivar delning (SMB-in).
  1. För varje regel högerklickar du på regeln och klickar sedan på **Aktivera regel**.

* Så här aktiverar du fildelning med grupprincip:
  1. Gå till **Start**, Skriv `gpmc.msc` och Sök.
  1. Öppna följande mappar i navigerings fönstret: användar konfiguration för **lokal dator princip**  >    >  **administrativa mallar**  >  **Windows-komponenter**  >  **nätverks delning**.
  1. I informations fönstret dubbelklickar **du på förhindra användare från att dela filer i profilen**.

     Om du vill inaktivera inställningen för grupprincip och aktivera användarens möjlighet att dela filer väljer du **inaktive rad**.

  1. Klicka på **OK** för att spara ändringarna.

  Mer information finns i [Aktivera eller inaktivera fildelning med Grupprincip](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc754359(v=ws.10)).

Om du vill aktivera fil-och skrivar delning för senare versioner av Windows eller Linux följer du anvisningarna i [Installera mobilitets tjänsten för haveri beredskap för virtuella VMware-datorer och fysiska servrar](vmware-azure-install-mobility-service.md) .

## <a name="windows-management-instrumentation-wmi-configuration-check-error-code-95103"></a>Windows Management Instrumentation (WMI) konfigurations kontroll (felkod: 95103)

När fil-och skrivar tjänster är markerat aktiverar du WMI-tjänsten för privata, offentliga och domän profiler genom brand väggen. De här inställningarna krävs för att slutföra fjärrkörning på käll datorn.

Så här aktiverar du WMI:

1. Gå till **kontroll panelen**  >  **säkerhet** och välj **Windows-brandvägg**.
1. Välj **ändra inställningar** och välj sedan fliken **undantag** .
1. I fönstret **undantag** markerar du kryss rutan för Windows Management INSTRUMENTATION (WMI) om du vill aktivera WMI-trafik genom brand väggen.

Du kan också aktivera WMI-trafik genom brand väggen från kommando tolken med följande kommando:

`netsh advfirewall firewall set rule group="windows management instrumentation (wmi)" new enable=yes`

Andra artiklar om WMI-felsökning finns i följande artiklar.

* [Grundläggande WMI-testning](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)
* [WMI-felsökning](/windows/win32/wmisdk/wmi-troubleshooting)
* [Fel sökning av problem med WMI-skript och WMI-tjänster](/previous-versions/tn-archive/ff406382(v=msdn.10))

## <a name="unsupported-operating-systems"></a>Operativsystem som inte stöds

En annan vanlig orsak till haveri kan bero på ett operativ system som inte stöds. Använd ett operativ system och en kernel-version som stöds för att lyckas med installationen av mobilitets tjänsten. Undvik att använda privata uppdateringar.

Om du vill visa en lista över operativ system och kernel-versioner som stöds av Azure Site Recovery, se [dokumentet med support mat ris](vmware-physical-azure-support-matrix.md#replicated-machines).

## <a name="unsupported-boot-disk-configurations-errorid-95309-95310-95311"></a>Konfigurationer för start disk som inte stöds (ErrorID: 95309, 95310, 95311)

### <a name="boot-and-system-partitions--volumes-arent-the-same-disk-errorid-95309"></a>Start-och systempartitionerna/volymerna är inte samma disk (ErrorID: 95309)

Innan 9,20-versionen har start-och systempartitionerna/volymerna på olika diskar en konfiguration som inte stöds. Från och med [9,20-versionen](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)stöds den här konfigurationen.

### <a name="the-boot-disk-isnt-available-errorid-95310"></a>Start disken är inte tillgänglig (ErrorID: 95310)

Det går inte att skydda en virtuell dator utan en start disk. En start disk garanterar en smidig återställning av en virtuell dator under en redundansväxling. Frånvaro av en start disk innebär att det inte går att starta datorn efter redundansväxlingen. Se till att den virtuella datorn innehåller en start disk och försök sedan igen. Dessutom stöds inte flera start diskar på samma dator.

### <a name="multiple-boot-disks-present-on-the-source-machine-errorid-95311"></a>Det finns flera start diskar på käll datorn (ErrorID: 95311)

En virtuell dator med flera start diskar är inte en [konfiguration som stöds](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage).

## <a name="system-partition-on-multiple-disks-errorid-95313"></a>Systempartition på flera diskar (ErrorID: 95313)

Innan 9,20-versionen hade en rot partition eller volym konfiguration på flera diskar en konfiguration som inte stöds. Från och med [9,20-versionen](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)stöds den här konfigurationen.

## <a name="enable-protection-failed-as-device-name-mentioned-in-the-grub-configuration-instead-of-uuid-errorid-95320"></a>Det gick inte att aktivera skydd eftersom enhets namnet som anges i GRUB-konfigurationen i stället för UUID (ErrorID: 95320)

### <a name="possible-cause"></a>Möjlig orsak

Konfigurationsfilerna för Grand Unified startGRUB (_/boot/grub/menu.lst_, _/boot/grub/grub.cfg_, _/Boot/grub2/grub.cfg_ eller _/etc/default/grub_) kan innehålla värdet för parameter **roten** och **återupptas** som faktiska enhets namn i stället för en universell unik identifierare (UUID). Site Recovery bestämmer UUID-metoden som enhets namn kan ändras för den virtuella datorn. Till exempel kanske den virtuella datorn inte är online med samma namn vid redundansväxling och som resulterar i problem.

Exempel:

- Följande rad är från GRUB-filen _/Boot/grub2/grub.cfg_:

  `linux /boot/vmlinuz-3.12.49-11-default root=/dev/sda2  ${extra_cmdline} resume=/dev/sda1 splash=silent quiet showopts`

- Följande rad är från GRUB-filen _/boot/grub/menu.lst_:

  `kernel /boot/vmlinuz-3.0.101-63-default root=/dev/sda2 resume=/dev/sda1 splash=silent crashkernel=256M-:128M showopts vga=0x314`

> [!NOTE]
> GRUB-raderna innehåller faktiska enhets namn för parameter **roten** och **återupptas** istället för uuid.

### <a name="how-to-fix"></a>Så här åtgärdar du

Enhetsnamnen bör ersättas med motsvarande UUID.

1. Hitta enhetens UUID genom att köra kommandot `blkid \<device name>` .

   Exempel:

   ```shell
   blkid /dev/sda1
   /dev/sda1: UUID="6f614b44-433b-431b-9ca1-4dd2f6f74f6b" TYPE="swap"
   blkid /dev/sda2
   /dev/sda2: UUID="62927e85-f7ba-40bc-9993-cc1feeb191e4" TYPE="ext3"
   ```

1. Ersätt nu enhets namnet med dess UUID i formatet som `root=UUID=\<UUID>` . Om vi till exempel ersätter enhets namnen med UUID för rot-och återställnings parametern som anges i filerna _/Boot/grub2/grub.cfg_, _/Boot/grub2/grub.cfg_ eller _/etc/default/grub_ , ser raderna i filerna ut som följande rad:

   `kernel /boot/vmlinuz-3.0.101-63-default root=UUID=62927e85-f7ba-40bc-9993-cc1feeb191e4 resume=UUID=6f614b44-433b-431b-9ca1-4dd2f6f74f6b splash=silent crashkernel=256M-:128M showopts vga=0x314`

1. Starta om skyddet.

## <a name="install-mobility-service-completed-with-warning-to-reboot-errorid-95265--95266"></a>Installations mobilitets tjänsten har slutförts med varning för omstart (ErrorID: 95265 & 95266)

Site Recovery mobilitets tjänsten har många komponenter, en av dessa kallas filter driv rutin. Filter driv rutinen laddas bara in i system minnet under en omstart av systemet. Filter driv rutins korrigeringar kan bara realiseras när en ny filter driv rutin läses in vid en omstart av systemet.

> [!IMPORTANT]
> Detta är en varning och den befintliga replikeringen fungerar även efter den nya agent uppdateringen. Du kan välja att starta om varje gång som du vill få fördelarna med den nya filter driv rutinen, men om du inte startar om datorn fortsätter den gamla filter driv rutinen att fungera. Så efter en uppdatering utan omstart, förutom filter driv rutinen, **kommer fördelarna med andra förbättringar och korrigeringar i mobilitets tjänsten att bli realiserade**. Även om det är rekommenderat, är det inte obligatoriskt att starta om efter varje uppgradering. Om du vill ha information om när en omstart är obligatorisk anger du avsnittet [starta om efter uppgraderingen av mobilitets tjänsten](service-updates-how-to.md#reboot-after-mobility-service-upgrade) i service updates Azure Site Recovery.

> [!TIP]
>Bästa praxis om hur du schemalägger uppgraderingar i underhålls fönstret finns i [stöd för senaste operativ system/kernel](service-updates-how-to.md#support-for-latest-operating-systemskernels) i tjänst uppdateringar i Azure Site Recovery.

## <a name="lvm-support-from-920-version"></a>LVM-stöd från 9,20-versionen

Innan 9,20-versionen stöddes Logical Volume Manager (LVM) för data diskar. `/boot`Partitionen bör finnas på en diskpartition och inte en LVM-volym.

Från och med [9,20-versionen](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery)stöds [OS-disken på LVM](vmware-physical-azure-support-matrix.md#linux-file-systemsguest-storage) .

## <a name="insufficient-space-errorid-95524"></a>Otillräckligt med utrymme (ErrorID: 95524)

När mobilitets agenten kopieras till käll datorn krävs minst 100 MB ledigt utrymme. Se till att käll datorn har den nödvändiga mängden ledigt utrymme och försök igen.

## <a name="low-system-resources"></a>Låga system resurser

De möjliga fel-ID: na som visas för det här problemet är 95572 och 95573. Det här problemet uppstår när systemet har ont om ledigt minne och inte kan allokera minne för mobilitets tjänst installationen. Se till att tillräckligt med minne har frigjorts för att installationen ska kunna fortsätta och slutföras.

## <a name="vss-installation-failures"></a>Installations problem med VSS

VSS-installationen (Volume Shadow Copy Service) är en del av installationen av mobilitets agenten. Den här tjänsten används i processen för att generera programkonsekventa återställnings punkter. Fel under installationen av VSS kan ske på grund av flera orsaker. För att identifiera de exakta felen, se _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_. Några av de vanliga felen och lösnings stegen är markerade i följande avsnitt.

### <a name="vss-error--2147023170-0x800706be---exit-code-511"></a>VSS-fel – 2147023170 [0x800706BE] – slutkod 511

Det här problemet visas oftast när antivirus program vara blockerar Azure Site Recovery tjänsters åtgärder.

Lös problemet så här:

1. Granska listan över mappar som ska [undantas från antivirus programmet](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).
1. Följ de rikt linjer som publicerats av antivirus leverantören för att avblockera registreringen av DLL i Windows.

### <a name="vss-error-7-0x7---exit-code-511"></a>VSS-fel 7 [0x7]-slutkod 511

Det här felet är ett körnings fel som beror på att det inte finns tillräckligt med minne för att installera VSS. Öka disk utrymmet för slutförd åtgärd.

### <a name="vss-error--2147023824-0x80070430---exit-code-517"></a>VSS-fel – 2147023824 [0x80070430] – slutkod 517

Felet uppstår när Azure Site Recovery tjänsten VSS Provider har [marker ATS för borttagning](/previous-versions/ms838153(v=msdn.10)). Försök att installera VSS manuellt på käll datorn genom att köra följande kommando:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

### <a name="vss-error--2147023841-0x8007041f---exit-code-512"></a>VSS-fel – 2147023841 [0x8007041F] – slutkod 512

Felet uppstår när Azure Site Recovery VSS Provider Service-databasen är [låst](/previous-versions/ms833798(v=msdn.10)). Försök att installera VSS manuellt på käll datorn genom att köra följande kommando från en kommando tolk:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

När det uppstår ett problem kan du kontrol lera om något antivirus program eller andra tjänster har fastnat i **Start** läge. En process i **Start** läge kan behålla låset på databas tjänsterna. Det leder till problem vid installation av VSS-providern. Se till att ingen tjänst är i ett **Start** läge och försök sedan igen.

### <a name="vss-exit-code-806"></a>Avslutnings kod för VSS 806

Felet uppstår när det användar konto som används för installation inte har behörighet att köra `CSScript` kommandot. Ange de behörigheter som krävs för användar kontot för att köra skriptet och försök sedan igen.

### <a name="other-vss-errors"></a>Andra VSS-fel

Försök att installera tjänsten VSS Provider manuellt på käll datorn genom att köra följande kommando från en kommando tolk:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

## <a name="vss-error---0x8004e00f"></a>VSS-fel – 0x8004E00F

Det här felet uppstår vanligt vis under installationen av mobilitets agenten på grund av problem i `DCOM` och `DCOM` är i ett kritiskt tillstånd.

Använd följande procedur för att ta reda på orsaken till felet.

### <a name="examine-the-installation-logs"></a>Granska installations loggarna

1. Öppna installations loggen som finns på _C:\ProgramData\ASRSetupLogs\ASRUnifiedAgentInstaller.log_.
2. Förekomsten av följande fel indikerar det här problemet:

    ```Output
    Unregistering the existing application...
    Create the catalogue object
    Get the collection of Applications

    ERROR:

    - Error code: -2147164145 [0x8004E00F]
    - Exit code: 802
    ```

Så här löser du problemet:

Kontakta [Microsoft Windows Platform-teamet](https://aka.ms/Windows_Support) för att få hjälp med att lösa DCOM-problemet.

När DCOM-problemet är löst måste du installera om Azure Site Recovery VSS-providern manuellt med hjälp av följande kommando från en kommando tolk:

`"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

Om program konsekvens inte är kritiskt för katastrof återställnings kraven kan du kringgå installationen av VSS-providern.

För att kringgå Azure Site Recovery installation av VSS-providern och installera Azure Site Recovery VSS-Provider efter installationen manuellt:

1. Installera mobilitets tjänsten. Installationen kan inte utföras i steget: **efter installationen**.
1. Så här kringgår du VSS-installationen:
   1. Öppna installations katalogen för Azure Site Recovery Mobility Service på:

      _C:\Program Files (x86) \Microsoft Azure Site Recovery\agent._

   1. Ändra installations skripten Azure Site Recovery VSS-Provider _InMageVSSProvider_Install_ och _InMageVSSProvider_Uninstall. cmd_ så att de alltid lyckas genom att lägga till följande rader:

      ```plaintext
      rem @echo off
      setlocal
      exit /B 0
      ```

1. Gör en manuell installation av mobilitets agenten.
1. När installationen har slutförts och fortsätter till nästa steg, **Konfigurera**, ta bort de rader som du har lagt till.
1. Om du vill installera VSS-providern öppnar du en kommando tolk som administratör och kör följande kommando:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

1. Kontrol lera att VSS-providern för Azure Site Recovery är installerad som en tjänst i Windows-tjänster. Öppna Component service-MMC för att bekräfta att VSS-providern visas.
1. Om installationen av VSS-providern fortsätter att fungera, arbetar du med teknisk support för att lösa behörighets felen i CAPI2 (Cryptographic Application Programming Interface).

## <a name="vss-provider-installation-fails-because-the-cluster-service-being-enabled-on-non-cluster-machine"></a>Installationen av VSS-providern Miss lyckas eftersom kluster tjänsten är aktive rad på en annan dator än en kluster dator

Det här problemet medför att installationen av Azure Site Recovery Mobility-agenten Miss fungerar under installationen av Azure Site Recovery VSS-providern. Felet beror på att det finns ett problem med `COM+` som förhindrar installationen av VSS-providern.

### <a name="to-identify-the-issue"></a>Identifiera problemet

I den logg som finns på konfigurations servern på _C:\ProgramData\ASRSetupLogs\UploadedLogs \<date-time> UA_InstallLogFile. log_ hittar du följande undantag:

```plaintext
COM+ was unable to talk to the Microsoft Distributed Transaction Coordinator (Exception from HRESULT: 0x8004E00F)
```

Så här löser du problemet:

1. Kontrol lera att datorn är en icke-klustrad dator och att kluster komponenterna inte används.
1. Om komponenterna inte används tar du bort kluster komponenterna från datorn.

## <a name="drivers-are-missing-on-the-source-server"></a>Driv rutiner saknas på käll servern

Om installationen av mobilitets agenten Miss lyckas granskar du loggarna under _C:\ProgramData\ASRSetupLogs_ för att avgöra om några av de nödvändiga driv rutinerna saknas i vissa kontroll uppsättningar.

Så här löser du problemet:

1. Öppna registret med hjälp av en registereditor som `regedit.msc` .
1. Öppna `HKEY_LOCAL_MACHINE\SYSTEM` noden.
1. `SYSTEM`Leta upp kontroll uppsättningarna i noden.
1. Öppna varje kontroll uppsättning och kontrol lera att följande Windows-drivrutiner finns:

   * ATAPI
   * VMBus
   * Storflt
   * Storvsc
   * Intelide

1. Installera om eventuella driv rutiner som saknas.

## <a name="next-steps"></a>Nästa steg

[Läs mer](vmware-azure-tutorial.md) om hur du konfigurerar haveri beredskap för virtuella VMware-datorer.
