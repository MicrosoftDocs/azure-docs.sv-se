---
title: Felsök Windows Virtual Desktop-Session värd – Azure
description: Så här löser du problem när du konfigurerar virtuella Windows-datorer för fjärrskrivbordssessioner-värd.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 84a49ea2f6ce4a5119af024ab3de67fa2b89c02e
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2021
ms.locfileid: "98539746"
---
# <a name="session-host-virtual-machine-configuration"></a>Session för konfiguration av virtuell värddator

>[!IMPORTANT]
>Det här innehållet gäller för virtuella Windows-datorer med Azure Resource Manager virtuella Windows Desktop-objekt. Om du använder Windows Virtual Desktop (klassisk) utan Azure Resource Manager objekt, se [den här artikeln](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md).

Använd den här artikeln för att felsöka problem som du har med när du konfigurerar virtuella Windows-sessioner för fjärrskrivbordssessioner värd för virtuella datorer (VM).

## <a name="provide-feedback"></a>Ge feedback

Besök [Windows-Tech-communityn för Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) för att diskutera Windows Virtual Desktop-tjänsten med produkt teamet och aktiva community-medlemmar.

## <a name="vms-are-not-joined-to-the-domain"></a>Virtuella datorer är inte anslutna till domänen

Följ dessa instruktioner om du har problem med att ansluta till virtuella datorer (VM) till domänen.

- Anslut den virtuella datorn manuellt med processen i [koppla en virtuell Windows Server-dator till en hanterad domän](../active-directory-domain-services/join-windows-vm.md) eller Använd [domän kopplings mal len](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Försök att pinga domän namnet från en kommando rad på den virtuella datorn.
- Granska listan över domän anslutnings fel meddelanden i fel [sökning av fel meddelanden för domän anslutning](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Fel: Felaktiga autentiseringsuppgifter

**Orsak:** Ett skrivnings fel inträffade när autentiseringsuppgifterna angavs i gränssnitts korrigeringarna för Azure Resource Manager-mall.

**KORRIGERA:** Utför en av följande åtgärder för att lösa problemet.

- Lägg till de virtuella datorerna i en domän manuellt.
- Distribuera om mallen när autentiseringsuppgifterna har bekräftats. Se [skapa en adresspool med PowerShell](create-host-pools-powershell.md).
- Använd en mall för att ansluta virtuella datorer till en domän med en [befintlig virtuell Windows-dator till AD-domän](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Fel: tids gräns väntar på användarindata

**Orsak:** Det konto som används för att slutföra domän anslutningen kan ha Multi-Factor Authentication (MFA).

**KORRIGERA:** Utför en av följande åtgärder för att lösa problemet.

- Ta tillfälligt bort MFA för kontot.
- Använd ett tjänst konto.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Fel: kontot som används under etableringen har inte behörighet att slutföra åtgärden

**Orsak:** Kontot som används har inte behörighet att ansluta virtuella datorer till domänen på grund av efterlevnad och föreskrifter.

**KORRIGERA:** Utför en av följande åtgärder för att lösa problemet.

- Använd ett konto som är medlem i gruppen Administratörer.
- Bevilja de behörigheter som krävs för det konto som används.

### <a name="error-domain-name-doesnt-resolve"></a>Fel: domän namnet matchar inte

**Orsak 1:** Virtuella datorer finns i ett virtuellt nätverk som inte är kopplat till det virtuella nätverk (VNET) där domänen finns.

**Korrigering 1:** Skapa VNET-peering mellan VNET där virtuella datorer etablerades och det virtuella nätverk där domänkontrollanten (DC) körs. Se [skapa ett virtuellt nätverk peering – Resource Manager, olika prenumerationer](../virtual-network/create-peering-different-subscriptions.md).

**Orsak 2:** När du använder Azure Active Directory Domain Services (Azure AD DS) har det virtuella nätverket inte uppdaterat sina DNS-serverinställningar så att de pekar på de hanterade domän kontrol Lanterna.

**Korrigera 2:** Information om hur du uppdaterar DNS-inställningarna för det virtuella nätverket som innehåller Azure AD DS finns i [Uppdatera DNS-inställningar för det virtuella Azure-nätverket](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Orsak 3:** Nätverks gränssnittets DNS-serverinställningar pekar inte mot lämplig DNS-server i det virtuella nätverket.

**Korrigera 3:** Vidta någon av följande åtgärder för att lösa problemet genom att följa stegen i [ändra DNS-servrar].
- Ändra nätverks gränssnittets DNS-serverinställningar till **anpassad** med stegen från [ändra DNS-servrar](../virtual-network/virtual-network-network-interface.md#change-dns-servers) och ange de privata IP-adresserna för DNS-servrarna i det virtuella nätverket.
- Ändra nätverks gränssnittets DNS-serverinställningar så att de **ärver från Virtual Network** med stegen från [ändra DNS-servrar](../virtual-network/virtual-network-network-interface.md#change-dns-servers)och ändra sedan det virtuella nätverkets DNS-serverinställningar med stegen från [ändra DNS-servrar](../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows Virtual Desktop-agenten och start inläsaren för virtuella Windows-datorer har inte installerats

Det rekommenderade sättet att etablera virtuella datorer är att använda mallen för att skapa Azure Portal. Mallen installerar automatiskt Windows Virtual Desktop-agenten och start inläsaren för Windows Virtual Desktop agent.

Följ de här anvisningarna för att bekräfta att komponenterna är installerade och för att söka efter fel meddelanden.

1. Bekräfta att de två komponenterna är installerade genom att kontrol lera i program  >    >  **och funktioner** på kontroll panelen. Om **Windows Virtual Desktop-agenten** och **Start inläsaren för virtuella Skriv bords agenter** för Windows inte visas, är de inte installerade på den virtuella datorn.
2. Öppna **Utforskaren** och gå till **C:\Windows\Temp\ScriptLog.log**. Om filen saknas, anger det att PowerShell DSC som installerade de två komponenterna inte kunde köras i den angivna säkerhets kontexten.
3. Om filen **C:\Windows\Temp\ScriptLog.log** finns öppnar du den och kontrollerar om det finns fel meddelanden.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Fel: Windows Virtual Desktop-agenten och start inläsaren för Windows Virtual Desktop agent saknas. C:\Windows\Temp\ScriptLog.log saknas också

**Orsak 1:** Autentiseringsuppgifterna som angavs vid indata för Azure Resource Manager mal len var felaktiga eller var otillräckliga.

**Korrigering 1:** Lägg till de saknade komponenterna manuellt i de virtuella datorerna med hjälp av [skapa en adresspool med PowerShell](create-host-pools-powershell.md).

**Orsak 2:** PowerShell DSC kunde starta och köra, men det gick inte att slutföra eftersom den inte kan logga in på Windows Virtual Desktop och hämta nödvändig information.

**Korrigera 2:** Bekräfta objekten i följande lista.

- Kontrol lera att kontot inte har MFA.
- Bekräfta att värddatorns namn är korrekt och att poolen finns i Windows Virtual Desktop.
- Bekräfta att kontot har minst deltagar behörighet för Azure-prenumerationen eller resurs gruppen.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Fel: Autentiseringen misslyckades, fel i C:\Windows\Temp\ScriptLog.log

**Orsak:** PowerShell DSC kunde köra men kunde inte ansluta till det virtuella Windows-skrivbordet.

**KORRIGERA:** Bekräfta objekten i följande lista.

- Registrera de virtuella datorerna manuellt med Windows Virtual Desktop-tjänsten.
- Bekräfta att kontot som används för att ansluta till Windows Virtual Desktop har behörighet för Azure-prenumerationen eller resurs gruppen för att skapa värdar för pooler.
- Bekräfta att kontot inte har MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows Virtual Desktop-agenten registreras inte med Windows Virtual Desktop-tjänsten

När Windows-agenten för virtuella skriv bord installeras på en virtuell dator i Session Host (antingen manuellt eller via Azure Resource Manager-mallen och PowerShell DSC), innehåller den en registreringsbegäran. I följande avsnitt beskrivs fel söknings problem som gäller för Windows Virtual Desktop-agenten och token.

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>Fel: status som har arkiverats i Get-AzWvdSessionHost cmdlet visar status som ej tillgänglig

> [!div class="mx-imgBorder"]
> ![Get-AzWvdSessionHost-cmdlet: en visar status som ej tillgänglig.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Orsak:** Agenten kan inte uppdatera sig själv till en ny version.

**KORRIGERA:** Följ dessa instruktioner om du vill uppdatera agenten manuellt.

1. Hämta en ny version av agenten på den virtuella datorn för sessionens värd.
2. Starta aktivitets hanteraren och stoppa RDAgentBootLoader-tjänsten på fliken tjänst.
3. Kör installations programmet för den nya versionen av Windows-agenten för virtuella skriv bord.
4. När du uppmanas att registrera token tar du bort posten INVALID_TOKEN och trycker på nästa (en ny token krävs inte).
5. Slutför installations guiden.
6. Öppna aktivitets hanteraren och starta RDAgentBootLoader-tjänsten.

## <a name="error-windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Fel: register posten Windows Virtual Desktop agent IsRegistered visar värdet 0

**Orsak:** Registrerings-token har upphört att gälla.

**KORRIGERA:** Följ de här anvisningarna för att åtgärda agentens register fel.

1. Om det redan finns en Registration-token tar du bort den med Remove-AzWvdRegistrationInfo.
2. Kör cmdleten **New-AzWvdRegistrationInfo** för att generera en ny token.
3. Bekräfta att parametern *-ExpriationTime* har angetts till 3 dagar.

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>Fel: Windows Virtual Desktop-agenten rapporterar inget pulsslag vid körning Get-AzWvdSessionHost

**Orsak 1:** RDAgentBootLoader-tjänsten har stoppats.

**Korrigering 1:** Starta aktivitets hanteraren och starta tjänsten om tjänsten tjänst rapporterar en stoppad status för RDAgentBootLoader-tjänsten.

**Orsak 2:** Port 443 kan vara stängd.

**Korrigera 2:** Följ de här anvisningarna för att öppna port 443.

1. Bekräfta att port 443 är öppen genom att ladda ned verktyget PSPing från [Sysinternal-verktyg](/sysinternals/downloads/psping/).
2. Installera PSPing på den virtuella värddatorn för sessionen där agenten körs.
3. Öppna kommando tolken som administratör och utfärda kommandot nedan:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Bekräfta att PSPing tog emot information tillbaka från RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Fel sökning av problem med Windows Virtual Desktop sida vid sida

Windows Virtual Desktop sida-vid-sida-stacken installeras automatiskt med Windows Server 2019. Använd Microsoft Installer (MSI) för att installera stacken sida vid sida på Microsoft Windows Server 2016 eller Windows Server 2012 R2. För Microsoft Windows 10 är Windows Virtual Desktop sida vid sida aktive rad med **enablesxstackrs.ps1**.

Det finns tre sätt att placera stacken sida vid sida som är installerad eller aktive rad på virtuella datorer för fjärrskrivbordssessioner:

- Med mallen för att skapa Azure Portal
- Genom att inkluderas och aktive ras på huvud avbildningen
- Installerad eller aktive rad manuellt på varje virtuell dator (eller med tillägg/PowerShell)

Om du har problem med den virtuella Windows-skrivbordet sida vid sida, skriver du kommandot **Qwinsta** från kommando tolken för att bekräfta att stacken sida vid sida är installerad eller aktive rad.

Utdata från **Qwinsta** visar **RDP-SXS** i utdata om stacken sida vid sida är installerad och aktive rad.

> [!div class="mx-imgBorder"]
> ![Staplad sida vid sida installeras eller aktive ras med Qwinsta listad som RDP-SXS i utdata.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Granska register posterna i listan nedan och bekräfta att värdena stämmer överens. Om register nycklar saknas eller om värdena inte överensstämmer, följer du anvisningarna i [skapa en adresspool med PowerShell](create-host-pools-powershell.md) om hur du installerar om stacken sida vid sida.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Fel: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![O_REVERSE_CONNECT_STACK_FAILURE fel kod.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Orsak:** Stacken sida vid sida är inte installerad på den virtuella datorn för sessionen.

**KORRIGERA:** Följ de här anvisningarna för att installera stacken sida vid sida på den virtuella datorns Session Host.

1. Använd Remote Desktop Protocol (RDP) för att komma direkt till den virtuella datorns värd för sessionen som lokal administratör.
2. Installera stacken sida vid sida med hjälp av [skapa en värdbaserad pool med PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Så här åtgärdar du en virtuell Windows-sida vid sida-stack som inte fungerar

Det finns kända omständigheter som kan orsaka att stacken sida vid sida inte fungerar:

- Följer inte rätt ordning på stegen för att aktivera stack sida vid sida
- Automatisk uppdatering till Windows 10 Enhanced mångsidig disk (EVD)
- RDSH-rollen värd för fjärrskrivbordssession saknas
- Köra enablesxsstackrc.ps1 flera gånger
- Köra enablesxsstackrc.ps1 i ett konto som inte har lokal administratörs behörighet

Anvisningarna i det här avsnittet kan hjälpa dig att avinstallera Windows Virtual Desktop sida vid sida-stack. När du har avinstallerat stacken sida vid sida går du till "registrera den virtuella datorn med Windows-adresspoolen för virtuella skriv bord" i [skapa en adresspool med PowerShell](create-host-pools-powershell.md) för att installera om stacken sida vid sida.

Den virtuella datorn som används för att köra reparation måste finnas i samma undernät och domän som den virtuella datorn med den felaktiga stacken sida vid sida.

Följ de här anvisningarna för att köra reparationen från samma undernät och domän:

1. Anslut med standard Remote Desktop Protocol (RDP) till den virtuella datorn från den plats där korrigeringen ska tillämpas.
2. Hämta PsExec från https://docs.microsoft.com/sysinternals/downloads/psexec .
3. Zippa upp den hämtade filen.
4. Starta kommando tolken som lokal administratör.
5. Navigera till mappen där PsExec var zippad.
6. Använd följande kommando från kommando tolken:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname är dator namnet på den virtuella datorn med den felaktiga stacken sida vid sida.

7. Godkänn licens avtalet PsExec genom att klicka på Godkänn.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild för program varu licens avtal.](media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >I den här dialog rutan visas endast den första gången PsExec körs.

8. När kommando tolken har öppnats på den virtuella datorn med fel sida-vid-sida-stack kör du Qwinsta och bekräftar att en post med namnet RDP-SXS är tillgänglig. Om inte, finns inte en sida-vid-sida-stack på den virtuella datorn, så problemet är inte kopplat till stacken sida vid sida.

    > [!div class="mx-imgBorder"]
    > ![Administratörs kommando tolk](media/AdministratorCommandPrompt.png)

9. Kör följande kommando, som visar en lista över Microsoft-komponenter som är installerade på den virtuella datorn med den felaktiga stacken sida vid sida.

    ```cmd
        wmic product get name
    ```

10. Kör kommandot nedan med produkt namn från steg ovan.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Avinstallera alla produkter som börjar med "fjärr skrivbord".

12. När alla komponenter för virtuella Windows-datorer har avinstallerats följer du anvisningarna för ditt operativ system:

13. Om operativ systemet är Windows Server startar du om den virtuella datorn med den felaktiga stacken sida vid sida (antingen med Azure Portal eller från PsExec-verktyget).

Om operativ systemet är Microsoft Windows 10 fortsätter du med instruktionerna nedan:

14. Från den virtuella datorn som kör PsExec öppnar du Utforskaren och kopierar disablesxsstackrc.ps1 till den virtuella datorns systemen het med fel staplad sida vid sida.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname är dator namnet på den virtuella datorn med den felaktiga stacken sida vid sida.

15. Den rekommenderade processen: starta PowerShell i PsExec-verktyget och navigera till mappen från föregående steg och kör disablesxsstackrc.ps1. Du kan också köra följande cmdlets:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. När cmdletarna har körts startar du om den virtuella datorn med den felaktiga stacken sida vid sida.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Licensierings läget för fjärr skrivbord är inte konfigurerat

Om du loggar in på Windows 10 Enterprise multi-session med ett administratörs konto kan du få ett meddelande om att licens läget för fjärr skrivbord inte har kon figurer ATS, Fjärrskrivbordstjänster slutar att fungera om X dagar. Använd Serverhanteraren för att ange licensierings läget för fjärr skrivbord på anslutnings Utjämnings servern. "

Om tids gränsen löper ut visas ett fel meddelande om att "Fjärrsessionen kopplades från eftersom det inte finns några åtkomst licenser för fjärr skrivbords klienten tillgängliga för den här datorn."

Om något av dessa meddelanden visas innebär det att avbildningen inte har de senaste Windows-uppdateringarna installerade eller att du ställer in fjärr skrivbords licensierings läget via en grup princip. Följ stegen i nästa avsnitt för att kontrol lera grup princip inställningen, identifiera versionen av Windows 10 Enterprise multi-session och installera motsvarande uppdatering.

>[!NOTE]
>Windows Virtual Desktop kräver bara en klient åtkomst licens för klient organisation (CAL) när din värddator innehåller Windows Server-sessionsbaserade värdar. Information om hur du konfigurerar en klient åtkomst licens för fjärr skrivbords tjänster finns i [licens för klient åtkomst licenser](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Inaktivera grup princip inställningen för fjärr skrivbords licensierings läge

Kontrol lera grup princip inställningen genom att öppna grupprincip redigeraren på den virtuella datorn och navigera till **administrativa mallar**  >  **Windows-komponenter**  >  **Fjärrskrivbordstjänster**  >  **värd**  >  **licensiering** för fjärrskrivbordssession  >  **Ange fjärr skrivbords licensierings läge**. Om grup princip inställningen är **aktive rad** ändrar du den till **inaktive rad**. Om den redan är inaktive rad lämnar du den som den är.

>[!NOTE]
>Om du anger grup princip via din domän inaktiverar du den här inställningen för principer som riktar sig mot de här Windows 10 Enterprise-datorer med flera sessioner.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Identifiera vilken version av Windows 10 Enterprise multi-session som du använder

För att kontrol lera vilken version av Windows 10 Enterprise multi-session du har:

1. Logga in med ditt administratörs konto.
2. Ange "About" i Sök fältet bredvid Start-menyn.
3. Välj **om din dator**.
4. Kontrol lera numret bredvid "version". Talet ska vara antingen "1809" eller "1903", som du ser i följande bild.

    > [!div class="mx-imgBorder"]
    > ![En skärm bild av fönstret Windows-specifikationer. Versions numret är markerat i blått.](media/windows-specifications.png)

Nu när du vet versions numret kan du gå vidare till relevant avsnitt.

### <a name="version-1809"></a>Version 1809

Om versions numret säger "1809" installerar [du KB4516077-uppdateringen](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Version 1903

Distribuera om värd operativ systemet med den senaste versionen av Windows 10, version 1903-avbildningen från Azure-galleriet.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Det gick inte att ansluta till fjärrdatorn på grund av ett säkerhets fel

Om dina användare ser ett fel som säger att "vi inte kunde ansluta till fjärrdatorn på grund av ett säkerhets fel. Om detta händer kan du be din administratör eller teknisk support om hjälp, validera eventuella befintliga principer som ändrar RDP-standardbehörigheterna. En princip som kan orsaka att det här felet visas är "Tillåt inloggning genom Fjärrskrivbordstjänster säkerhets princip".

Mer information om den här principen finns i [Tillåt inloggning via Fjärrskrivbordstjänster](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services).

## <a name="next-steps"></a>Nästa steg

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en adresspool i en Windows Virtual Desktop-miljö finns i avsnittet om att [skapa en miljö och en värddator](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem som rör agenten för virtuella Windows-datorer eller sessioner finns i [Felsöka vanliga problem med Windows Virtual Desktop agent](troubleshoot-agent.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md).
- Information om hur du felsöker problem med fjärr skrivbords klienter finns i [Felsöka fjärr skrivbords klienten](troubleshoot-client.md)
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](../azure-resource-manager/templates/deployment-history.md).
