---
title: Felsök problem med Windows Virtual Desktop agent – Azure
description: Lösa vanliga problem med agent och anslutningar.
author: Sefriend
ms.topic: troubleshooting
ms.date: 12/16/2020
ms.author: sefriend
manager: clarkn
ms.openlocfilehash: 2f321413a275676d0abb1a075ba958885ffcd821
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505033"
---
# <a name="troubleshoot-common-windows-virtual-desktop-agent-issues"></a>Felsök vanliga problem med Windows Virtual Desktop agent

Windows Virtual Desktop-agenten kan orsaka anslutnings problem på grund av flera faktorer:
   - Ett fel på den Broker som gör att agenten stoppar tjänsten.
   - Problem med uppdateringar.
   - Problem med att installera under Agent installationen, vilket stör anslutningen till sessionens värd.

Den här artikeln vägleder dig genom lösningar på dessa vanliga scenarier och hur du löser anslutnings problem.

>[!NOTE]
>För fel sökning av problem som rör anslutning av sessioner och Windows Virtual Desktop-agenten rekommenderar vi att du granskar händelse loggarna i **Loggboken**  >  **Windows-loggar**  >  . Leta efter händelser som har någon av följande källor för att identifiera problemet:
>
>- WVD-Agent
>- WVD-agent-Updater
>- RDAgentBootLoader
>- MsiInstaller

## <a name="error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running"></a>Fel: RDAgentBootLoader och/eller agent inläsaren för fjärr skrivbord har stoppats

Om du ser något av följande problem innebär det att start inläsaren, som läser in agenten, inte kunde installera agenten korrekt och att Agent tjänsten inte körs:
- **RDAgentBootLoader** är antingen stoppad eller inte igång.
- Det finns ingen status för **agent inläsaren för fjärr skrivbord**.

Lös problemet genom att starta RDAgent-Start programmet:

1. I fönstret tjänster högerklickar du på **agent inläsare för fjärr skrivbord**.
2. Välj **Starta**. Om det här alternativet är nedtonat kan du inte ha administratörs behörighet och måste hämta dem för att starta tjänsten.
3. Vänta 10 sekunder och högerklicka sedan på **agent inläsare för fjärr skrivbord**.
4. Välj **Uppdatera**.
5. Om tjänsten stoppas efter att du har startat och uppdaterat den, kan du ha ett registrerings fel. Mer information finns i [INVALID_REGISTRATION_TOKEN](#error-invalid_registration_token).

## <a name="error-invalid_registration_token"></a>Fel: INVALID_REGISTRATION_TOKEN

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om en händelse med ID 3277 visas **INVALID_REGISTRATION_TOKEN** i beskrivningen, den registrerings-token som du har inte har identifierats som giltig.

Lös problemet genom att skapa en giltig registrerings-token:

1. Om du vill skapa en ny registreringsbegäran följer du stegen i avsnittet [skapa en ny registrerings nyckel för den virtuella datorn](#step-3-generate-a-new-registration-key-for-the-vm) .
2. Öppna Registereditorn. 
3. Gå till **HKEY_LOCAL_MACHINE**  >  **Software**  >  **Microsoft**  >  **RDInfraAgent**.
4. Välj **IsRegistered**. 
5. I rutan **Värde data:** post skriver du **0** och väljer **OK**. 
6. Välj **RegistrationToken**. 
7. I rutan **Värde data:** post klistrar du in registrerings-token från steg 1. 

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av IsRegistered 0](media/isregistered-token.png)

8. Öppna en kommandotolk som administratör.
9. Ange **net stop RDAgentBootLoader**. 
10. Ange **net start RDAgentBootLoader**. 
11. Öppna Registereditorn.
12. Gå till **HKEY_LOCAL_MACHINE**  >  **Software**  >  **Microsoft**  >  **RDInfraAgent**.
13. Kontrol lera att **IsRegistered** är inställt på 1 och att det inte finns något i data kolumnen för **RegistrationToken**. 

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av IsRegistered 1](media/isregistered-registry.png)

## <a name="error-agent-cannot-connect-to-broker-with-invalid_form"></a>Fel: agenten kan inte ansluta till Broker med INVALID_FORM

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3277 som säger "INVALID_FORM" i beskrivningen gick något fel med kommunikationen mellan agenten och Broker. Agenten kan inte ansluta till Service Broker eller komma åt en viss URL på grund av vissa brand väggar eller DNS-inställningar.

För att lösa det här problemet kontrollerar du att du kan komma åt BrokerURI och BrokerURIGlobal:
1. Öppna Registereditorn. 
2. Gå till **HKEY_LOCAL_MACHINE**  >  **Software**  >  **Microsoft**  >  **RDInfraAgent**. 
3. Anteckna värdena för **BrokerURI** och **BrokerURIGlobal**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av global Broker URI och Broker URI global](media/broker-uri.png)

 
4. Öppna en webbläsare och gå till *\<BrokerURI\> API/hälsa*. 
   - Se till att du använder värdet från steg 3 i **BrokerURI**. I det här avsnittet är ett exempel <https://rdbroker-g-us-r0.wvd.microsoft.com/api/health> .
5. Öppna en annan flik i webbläsaren och gå till *\<BrokerURIGlobal\> API/hälsa*. 
   - Se till att du använder värdet från steg 3 i **BrokerURIGlobal** -länken. I det här avsnittet är ett exempel <https://rdbroker.wvd.microsoft.com/api/health> .
6. Om nätverket inte blockerar Broker-anslutningen kommer båda sidorna att läsas in och ett meddelande visas om att **"RD Broker är felfritt"** , vilket visas i följande skärm bilder. 

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av URL-åtkomst för Broker har lästs in](media/broker-uri-web.png)

   > [!div class="mx-imgBorder"]
   > ![Skärm bild där global URI-åtkomst för Broker har lästs in](media/broker-global.png)
 

7. Om nätverket blockerar Broker-anslutningen, läses inte sidorna in, som du ser i följande skärm bild. 

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av misslyckad inläsning av Broker-åtkomst](media/unsuccessful-broker-uri.png)

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av misslyckad global åtkomst för Broker](media/unsuccessful-broker-global.png)

8. Om nätverket blockerar dessa URL: er måste du avblockera de nödvändiga URL: erna. Mer information finns i [obligatorisk URL-lista](safe-url-list.md).
9. Om detta inte löser problemet kontrollerar du att du inte har några grup principer med chiffer som blockerar agenten till Broker-anslutning. Windows Virtual Desktop använder samma TLS 1,2-chiffer som i [Azures front dörr](../frontdoor/front-door-faq.yml#what-are-the-current-cipher-suites-supported-by-azure-front-door-). Mer information finns i [anslutnings säkerhet](network-connectivity.md#connection-security).

## <a name="error-3703"></a>Fel: 3703

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3703 som säger "RD Gateway-URL: en inte är tillgänglig" i beskrivningen kan agenten inte nå Gateway-URL: erna. För att kunna ansluta till din värd för sessionen och tillåta nätverks trafik till dessa slut punkter för att kringgå begränsningar, måste du avblockera URL: erna från [listan över obligatoriska URL](safe-url-list.md): er. Kontrol lera också att brand väggen eller proxyinställningarna inte blockerar dessa URL: er. Att avblockera dessa URL: er krävs för att använda Windows Virtual Desktop.

Lös problemet genom att kontrol lera att inställningarna för brand väggen och/eller DNS inte blockerar dessa URL: er:
1. [Använd Azure-brandväggen för att skydda Windows-distributioner av virtuella datorer.](../firewall/protect-windows-virtual-desktop.md).
2. Konfigurera dina [DNS-inställningar för Azure-brandväggen](../firewall/dns-settings.md).

## <a name="error-3019"></a>Fel: 3019

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3019 innebär det att agenten inte kan komma åt URL: erna för WebSocket-transport. För att kunna ansluta till din värd för sessionen och tillåta nätverks trafik att kringgå dessa begränsningar måste du avblockera de URL: er som anges i [listan över obligatoriska URL](safe-url-list.md): er. Arbeta med Azures nätverks team för att se till att dina brand Väggs-, proxy-och DNS-inställningar inte blockerar dessa URL: er. Du kan också kontrol lera dina nätverks spårnings loggar för att identifiera var Windows Virtual Desktop-tjänsten blockeras. Om du öppnar en supportbegäran för det här problemet, se till att koppla dina nätverks spårnings loggar till begäran.

## <a name="error-installationhealthcheckfailedexception"></a>Fel: InstallationHealthCheckFailedException

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3277 som säger "InstallationHealthCheckFailedException" i beskrivningen innebär det att stack lyssnaren inte fungerar eftersom Terminal-servern har växlat register nyckeln för stack lyssnaren.

Lös problemet så här:
1. Kontrol lera om [stack lyssnaren fungerar](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
2. Om stack lyssnaren inte fungerar [avinstallerar du och installerar om stack-komponenten manuellt](#error-vms-are-stuck-in-unavailable-or-upgrading-state).

## <a name="error-endpoint_not_found"></a>Fel: ENDPOINT_NOT_FOUND

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3277 som säger "ENDPOINT_NOT_FOUND" i beskrivningen som innebär att det inte gick att hitta någon slut punkt för Broker för att upprätta en anslutning till. Det här anslutnings problemet kan bero på någon av följande orsaker:

- Det finns inga virtuella datorer i din värd pool
- De virtuella datorerna i din värd pool är inte aktiva
- Alla virtuella datorer i din värd pool har överskridit gränsen för högsta antal sessioner
- Ingen av de virtuella datorerna i din adresspool har Agent tjänsten igång

Lös problemet så här:

1. Kontrol lera att den virtuella datorn är påslagen och inte har tagits bort från värddatorn.
2. Kontrol lera att den virtuella datorn inte har överskridit gränsen för högsta antal sessioner.
3. Kontrol lera att [Agent tjänsten körs](#error-the-rdagentbootloader-andor-remote-desktop-agent-loader-has-stopped-running) och [stack lyssnaren fungerar](#error-stack-listener-isnt-working-on-windows-10-2004-vm).
4. Kontrol lera att [agenten kan ansluta till Broker](#error-agent-cannot-connect-to-broker-with-invalid_form).
5. Se till att [den virtuella datorn har en giltig registrerings-token](#error-invalid_registration_token).
6. Se till att [den virtuella datorns registrerings-token inte har gått ut](faq.md#how-often-should-i-turn-my-vms-on-to-prevent-registration-issues). 

## <a name="error-installmsiexception"></a>Fel: InstallMsiException

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3277, som står i **InstallMsiException** i beskrivningen, körs installations programmet redan för ett annat program medan du försöker installera agenten, eller så blockerar en princip msiexec.exe programmet från att köras.

Lös problemet genom att inaktivera följande princip:
   - Inaktivera Windows Installer  
      - Kategori Sök väg: dator konfiguration \ administrativa Mallar\windows-komponenter\Windows Installer
   
>[!NOTE]
>Detta är inte en omfattande lista över principer, bara de som vi är medvetna om.

Så här inaktiverar du en princip:
1. Öppna en kommandotolk som administratör.
2. Ange och kör **RSoP. msc**.
3. I fönstret **gällande princip uppsättning** som öppnas går du till kategori Sök vägen.
4. Välj principen.
5. Välj **Inaktiverad**.
6. Välj **Använd**.   

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av Windows Installer princip i gällande princip uppsättning](media/gpo-policy.png)

## <a name="error-win32exception"></a>Fel: Win32Exception

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3277, som står **InstallMsiException** i beskrivningen, blockerar en princip cmd.exe från att starta. Om du blockerar det här programmet kan du inte köra konsol fönstret, som du måste använda för att starta om tjänsten när agenten uppdateras.

Lös problemet genom att inaktivera följande princip:
   - Neka åtkomst till kommandoprompten   
      - Kategori Sök väg: användar konfiguration \ användare Mallar\system
    
>[!NOTE]
>Detta är inte en omfattande lista över principer, bara de som vi är medvetna om.

Så här inaktiverar du en princip:
1. Öppna en kommandotolk som administratör.
2. Ange och kör **RSoP. msc**.
3. I fönstret **gällande princip uppsättning** som öppnas går du till kategori Sök vägen.
4. Välj principen.
5. Välj **Inaktiverad**.
6. Välj **Använd**.   

## <a name="error-stack-listener-isnt-working-on-windows-10-2004-vm"></a>Fel: stack lyssnaren fungerar inte på Windows 10 2004 VM

Kör **Qwinsta** i kommando tolken och anteckna det versions nummer som visas bredvid **RDP-SXS**. Om du inte ser **RDP-TCP-** och **RDP-SXS-** komponenterna kan du **Lyssna** bredvid dem eller de visas inte alls när du har kört **Qwinsta**, vilket innebär att det finns ett stack problem. Stack uppdateringar installeras tillsammans med agent uppdateringar, och när den här installationen går awry fungerar inte Windows Virtual Desktop-lyssnaren.

Lös problemet så här:
1. Öppna Registereditorn.
2. Gå till **HKEY_LOCAL_MACHINE**  >  **system**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
3. Under **WinStations** kan du se flera mappar för olika stack-versioner, välja den mapp som matchar den versions information som du såg när du körde **Qwinsta** i kommando tolken.
4. Hitta **fReverseConnectMode** och se till att dess data värde är **1**. Kontrol lera också att **fEnableWinStation** är inställt på **1**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av fReverseConnectMode](media/fenable-2.png)

5. Om **fReverseConnectMode** inte är inställt på **1** väljer du **fReverseConnectMode** och anger **1** i fältet värde. 
6. Om **fEnableWinStation** inte är inställt på **1** väljer du **fEnableWinStation** och anger **1** i fältet värde.
7. Starta om den virtuella datorn. 

>[!NOTE]
>Om du vill ändra **fReverseConnectMode** -eller **fEnableWinStation** -läget för flera virtuella datorer i taget kan du göra något av följande två saker:
>
>- Exportera register nyckeln från den dator som du redan har arbetat med och importera den till alla andra datorer som behöver den här ändringen.
>- Skapa ett grup princip objekt (GPO) som anger register nyckel svärdet för de datorer som behöver ändras.

7. Gå till **HKEY_LOCAL_MACHINE**  >  **system**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **ClusterSettings**.
8. Under **ClusterSettings** söker du efter **SessionDirectoryListener** och kontrollerar att dess data värde är **RDP-Sxs.**...
9. Om **SessionDirectoryListener** inte är inställt på **RDP-Sxs...**, måste du följa stegen i avsnittet [avinstallera agenten och start inläsaren](#step-1-uninstall-all-agent-boot-loader-and-stack-component-programs) för att först avinstallera agent, start inläsare och stack-komponenter och sedan [installera om agenten och start inläsaren](#step-4-reinstall-the-agent-and-boot-loader). Detta kommer att installera om stacken sida vid sida.

## <a name="error-heartbeat-issue-where-users-keep-getting-disconnected-from-session-hosts"></a>Fel: pulsslags problem där användare håller på att bli frånkopplade från sessionens värdar

Om servern inte har ett pulsslag från tjänsten Windows Virtual Desktop måste du ändra tröskelvärdet för pulsslag. Detta kan tillfälligt minimera problemets symptom, men korrigera inte det underliggande nätverks problemet. Följ anvisningarna i det här avsnittet om ett eller flera av följande scenarier gäller dig:

- Du får ett **CheckSessionHostDomainIsReachableAsync** -fel
- Du får ett **ConnectionBrokenMissedHeartbeatThresholdExceeded** -fel
- Du får ett **ConnectionEstablished: UnexpectedNetworkDisconnect** -fel
- Användar klienterna kommer att bli frånkopplade
- Användare kommer att bli bortkopplade från sina värdbaserade sessioner

Så här ändrar du tröskelvärdet för pulsslag:
1. Öppna kommando tolken som administratör.
2. Ange kommandot **Qwinsta** och kör det.
3. Två stack-komponenter ska visas: **RDP-TCP** och **RDP-SXS**. 
   - Beroende på vilken version av operativ systemet du använder kan **RDP-SXS** följas av versions numret. Om det är det, måste du skriva ned det här numret för senare.
4. Öppna Registereditorn.
5. Gå till **HKEY_LOCAL_MACHINE**  >  **system**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations**.
6. Under **WinStations** kan du se flera mappar för olika stack-versioner. Välj den mapp som matchar versions numret från steg 3.
7. Skapa ett nytt register-DWORD genom att högerklicka på Registereditorn och sedan välja **nytt**  >  **DWORD-värde (32-bitars)**. När du skapar DWORD anger du följande värden:
   - HeartbeatInterval: 10000
   - HeartbeatWarnCount: 30 
   - HeartbeatDropCount: 60 
8. Starta om den virtuella datorn.

>[!NOTE]
>Om du ändrar tröskelvärdet för pulsslag inte löser problemet kan du ha ett underliggande nätverks problem som du måste kontakta Azures nätverks team om.

## <a name="error-downloadmsiexception"></a>Fel: DownloadMsiException

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3277, som står i **DownloadMsiException** i beskrivningen, finns det inte tillräckligt med utrymme på disken för RDAgent.

Lös problemet genom att göra utrymme på disken genom att:
   - Ta bort filer som inte längre finns i användaren
   - Öka lagrings kapaciteten för den virtuella datorn

## <a name="error-agent-fails-to-update-with-missingmethodexception"></a>Fel: agenten kan inte uppdateras med MissingMethodException

Gå till **Loggboken**  >  **Windows logs**-  >  **programmet**. Om du ser en händelse med ID 3389 som säger "MissingMethodException: metoden hittades inte" i beskrivningen innebär det att Windows Virtual Desktop-agenten inte har uppdaterats och återställts till en tidigare version. Detta kan bero på att versions numret för .NET Framework som är installerat på de virtuella datorerna är lägre än 4.7.2. För att lösa det här problemet måste du uppgradera .NET till version 4.7.2 eller senare genom att följa installations anvisningarna i .NET Framework- [dokumentationen](https://support.microsoft.com/topic/microsoft-net-framework-4-7-2-offline-installer-for-windows-05a72734-2127-a15d-50cf-daf56d5faec2).


## <a name="error-vms-are-stuck-in-unavailable-or-upgrading-state"></a>Fel: virtuella datorer är låsta i läget otillgänglig eller uppgradering

Öppna ett PowerShell-fönster som administratör och kör följande cmdlet:

```powershell
Get-AzWvdSessionHost -ResourceGroupName <resourcegroupname> -HostPoolName <hostpoolname> | Select-Object *
```

Om statusen som anges för sessionens värd eller värdar i din värddator alltid står "ej tillgänglig" eller "uppgradering", har agenten eller stacken inte installerats korrekt.

Lös problemet genom att installera om stacken sida vid sida:
1. Öppna en kommandotolk som administratör.
2. Ange **net stop RDAgentBootLoader**. 
3. Gå till **kontroll panelen**  >  **program**  >  **program och funktioner**.
4. Avinstallera den senaste versionen av **Fjärrskrivbordstjänster SXS-protokollstacken** eller versionen som anges i **HKEY_LOCAL_MACHINE**  >  **system**  >  **CurrentControlSet**  >  **Control**  >  **Terminal Server**  >  **WinStations** under **ReverseConnectListener**.
5. Öppna ett konsol fönster som administratör och gå till **Program Files**  >  **Microsoft RDInfra**.
6. Välj komponenten **SxSStack** eller kör kommandot **msiexec/i SxSStack- <version> . MSI** för att installera MSI.
8. Starta om den virtuella datorn.
9. Gå tillbaka till kommando tolken och kör kommandot **Qwinsta** .
10. Kontrol lera att stack-komponenten som installeras i steg 6 står i **Lyssna** bredvid den.
   - I så fall, anger du **net start RDAgentBootLoader** i kommando tolken och startar om den virtuella datorn.
   - I så fall måste du registrera om den [virtuella datorn och installera om agent](#your-issue-isnt-listed-here-or-wasnt-resolved) komponenten.

## <a name="error-connection-not-found-rdagent-does-not-have-an-active-connection-to-the-broker"></a>Fel: anslutningen hittades inte: RDAgent har ingen aktiv anslutning till Service Broker

De virtuella datorerna kan finnas i anslutnings gränsen, så den virtuella datorn kan inte acceptera nya anslutningar.

Lös problemet så här:
   - Minska Max gränsen för session. Detta säkerställer att resurserna är jämnt fördelade över värd tjänster och förhindrar resurs utdelning.
   - Öka resurs kapaciteten för de virtuella datorerna.

## <a name="error-operating-a-pro-vm-or-other-unsupported-os"></a>Fel: driften av en virtuell dator med Pro eller annat operativ system som inte stöds

Staplad sida vid sida stöds bara av Windows Enterprise-eller Windows Server-SKU: er, vilket innebär att operativ system som Pro VM inte är. Om du inte har någon Enterprise-eller Server-SKU kommer stacken att installeras på den virtuella datorn men aktive ras inte, så du kan inte se den när du kör **Qwinsta** på kommando raden.

Lös problemet genom att skapa en virtuell dator som är Windows Enterprise eller Windows Server.
1. Gå till [information om virtuell dator](create-host-pools-azure-marketplace.md#virtual-machine-details) och följ steg 1-12 för att ställa in en av följande rekommenderade avbildningar:
   - Windows 10 Enterprise multi-session, version 1909
   - Windows 10 Enterprise multi-session, version 1909 + Microsoft 365-appar
   - Windows Server 2019 Datacenter
   - Windows 10 Enterprise multi-session, version 2004
   - Windows 10 Enterprise multi-session, version 2004 + Microsoft 365-appar
2. Välj **Granska och skapa**.

## <a name="error-name_already_registered"></a>Fel: NAME_ALREADY_REGISTERED

Namnet på den virtuella datorn har redan registrerats och är förmodligen en dubblett.

Lös problemet så här:
1. Följ stegen i avsnittet [ta bort Session Host från Host pool](#step-2-remove-the-session-host-from-the-host-pool) .
2. [Skapa en annan virtuell dator](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal). Se till att välja ett unikt namn för den här virtuella datorn.
3. Gå till [Azure Portal](https://portal.azure.com) och öppna sidan **Översikt** för den värddator som den virtuella datorn var i. 
4. Öppna fliken **värdar** och kontrol lera att alla värdar i sessionen finns i den aktuella poolen.
5. Vänta i 5-10 minuter så att sessionens värd status **är tillgänglig**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av tillgänglig värd för fjärrskrivbordssession](media/hostpool-portal.png)

## <a name="your-issue-isnt-listed-here-or-wasnt-resolved"></a>Problemet visas inte här eller har inte lösts

Om du inte kan hitta ditt problem i den här artikeln eller om instruktionerna inte hjälper dig, rekommenderar vi att du avinstallerar, installerar om och registrerar Windows Virtual Desktop Agent igen. Anvisningarna i det här avsnittet visar hur du omregistrerar din virtuella dator i Windows Virtual Desktop-tjänsten genom att avinstallera alla agent-, start inläsnings-och stack-komponenter, ta bort sessionsnyckeln från poolen, generera en ny registrerings nyckel för den virtuella datorn och installera om agenten och start inläsaren. Om ett eller flera av följande scenarier gäller för dig, följer du dessa anvisningar:
- Den virtuella datorn har fastnat i **uppgradering** eller är **inte tillgänglig**
- Stack lyssnaren fungerar inte och du kör på Windows 10 1809, 1903 eller 1909
- Du får ett **EXPIRED_REGISTRATION_TOKEN** fel
- Du ser inte att dina virtuella datorer visas i listan över sessions värdar
- Du ser inte **Remote Desktop agent-inläsaren** i fönstret tjänster
- Du ser inte **RdAgentBootLoader** -komponenten i aktivitets hanteraren
- Du får en **anslutnings utjämning som inte kunde verifiera inställnings** fel på virtuella datorer med anpassade avbildningar
- Det gick inte att lösa problemet med instruktionerna i den här artikeln

### <a name="step-1-uninstall-all-agent-boot-loader-and-stack-component-programs"></a>Steg 1: Avinstallera alla program för agenter, start program och stack-komponenter

Innan du installerar om agenten, start inläsaren och stacken måste du avinstallera alla befintliga komponent program från den virtuella datorn. Så här avinstallerar du alla agent-, start inläsnings-och stack komponent program:
1. Logga in på den virtuella datorn som administratör. 
2. Gå till **kontroll panelen**  >  **program**  >  **program och funktioner**.
3. Ta bort följande program:
   - Start inläsare för fjärr skrivbords agent
   - Fjärrskrivbordstjänster infrastruktur agent
   - Fjärrskrivbordstjänster Infrastructure Genève-agent
   - Fjärrskrivbordstjänster SxS-nätverks stack
   
>[!NOTE]
>Du kan se flera instanser av dessa program. Se till att ta bort alla.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av avinstallation av program](media/uninstall-program.png)

### <a name="step-2-remove-the-session-host-from-the-host-pool"></a>Steg 2: ta bort värd för sessionen från poolen

När du tar bort en sessions värd från poolen är den inte längre registrerad för den aktuella poolen. Detta fungerar som en återställning för registreringen av sessionens värd. Så här tar du bort en sessions värd från poolen:
1. Gå till sidan **Översikt** för den värddator som den virtuella datorn finns i [Azure Portal](https://portal.azure.com). 
2. Gå till fliken **sessioner värdar** om du vill se en lista över alla sessionsbaserade värdar i den aktuella poolen.
3. Titta på listan över sessionsbaserade värdar och välj den virtuella dator som du vill ta bort.
4. Välj **Ta bort**.  

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av borttagning av virtuell dator från poolen värd](media/remove-sh.png)

### <a name="step-3-generate-a-new-registration-key-for-the-vm"></a>Steg 3: generera en ny registrerings nyckel för den virtuella datorn

Du måste generera en ny registrerings nyckel som används för att registrera om den virtuella datorn till poolen och tjänsten. Så här skapar du en ny registrerings nyckel för den virtuella datorn:
1. Öppna [Azure Portal](https://portal.azure.com) och gå till sidan **Översikt** för poolen för den virtuella dator som du vill redigera.
2. Välj **registrerings nyckel**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av registrerings nyckeln i portalen](media/reg-key.png)

3. Öppna fliken **registrerings nyckel** och välj **generera ny nyckel**.
4. Ange förfallo datum och välj sedan **OK**.  

>[!NOTE]
>Utgångs datumet får inte vara mindre än en timme och inte längre än 27 dagar från dess generations tid och datum. Vi rekommenderar starkt att du ställer in utgångs datumet på den 27: a max.

5. Kopiera den nya skapade nyckeln till Urklipp. Du behöver den här nyckeln senare.

### <a name="step-4-reinstall-the-agent-and-boot-loader"></a>Steg 4: installera om agenten och start inläsaren

Genom att installera om den uppdaterade versionen av agenten och start inläsaren installeras sidan-vid-sida-och Genèvekonventionen-övervaknings agenten automatiskt också. Så här installerar du om agenten och start inläsaren:
1. Logga in på den virtuella datorn som administratör och Använd rätt version av agent installations programmet för din distribution beroende på vilken version av Windows som den virtuella datorn körs på. Om du har en virtuell Windows 10-dator följer du instruktionerna i [Registrera virtuella datorer](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) för att ladda ned **Windows-agenten för virtuella skriv bord** och **Start programmet för Windows-agenten för virtuella skriv bord**. Om du har en virtuell Windows 7-dator följer du steg 13-14 i [Registrera virtuella datorer](deploy-windows-7-virtual-machine.md#configure-a-windows-7-virtual-machine) för att ladda ned **Windows-agenten för virtuella skriv bord** och **Windows Virtual Desktop Agent Manager**.

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av hämtnings sidan för agent och Start programmet](media/download-agent.png)

2. Högerklicka på den agent och den Start programs installation som du har laddat ned.
3. Välj **Egenskaper**.
4. Välj **Avblockera**.
5. Välj **OK**.
6. Kör installationsprogrammet för agenten.
7. När installations programmet ber dig om registrerings-token klistrar du in registrerings nyckeln från Urklipp. 

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av inklistrad registrerings-token](media/pasted-agent-token.png)

8. Kör installations programmet för Start programmet.
9. Starta om den virtuella datorn. 
10. Gå till [Azure Portal](https://portal.azure.com) och öppna sidan **Översikt** för den värddator som den virtuella datorn tillhör.
11. Gå till fliken **sessioner värdar** om du vill se en lista över alla sessionsbaserade värdar i den aktuella poolen.
12. Du bör nu se den värd för sessionen som registrerats i värd-poolen med statusen **tillgänglig**. 

   > [!div class="mx-imgBorder"]
   > ![Skärm bild av tillgänglig värd för fjärrskrivbordssession](media/hostpool-portal.png)

## <a name="next-steps"></a>Nästa steg

Om problemet kvarstår kan du skapa ett support ärende och ta med detaljerad information om det problem du har och de åtgärder som du har vidtagit för att försöka lösa det. Följande lista innehåller andra resurser som du kan använda för att felsöka problem i distributionen av virtuella Windows-datorer.

- En översikt över fel sökning av virtuella Windows-datorer och eskalerade spår finns i [fel söknings översikt, feedback och support](troubleshoot-set-up-overview.md).
- Information om hur du felsöker problem när du skapar en adresspool i en Windows Virtual Desktop-miljö finns i avsnittet om att [skapa en miljö och en värddator](troubleshoot-set-up-issues.md).
- Information om hur du felsöker problem när du konfigurerar en virtuell dator (VM) i Windows Virtual Desktop finns i [konfiguration av Session Host-dator](troubleshoot-vm-configuration.md).
- Information om hur du felsöker problem med klient anslutningar för virtuella Windows-datorer finns i [Windows Virtual Desktop Service Connections](troubleshoot-service-connection.md).
- Information om hur du felsöker problem med fjärr skrivbords klienter finns i [Felsöka fjärr skrivbords klienten](troubleshoot-client.md).
- Information om hur du felsöker problem när du använder PowerShell med Windows Virtual Desktop finns i [Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Mer information om tjänsten finns i [Windows Virtual Desktop-miljö](environment-setup.md).
- Information om hur du går igenom en fel söknings kurs finns i [Självstudier: Felsöka distributioner av Resource Manager-mallar](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Mer information om gransknings åtgärder finns i [gransknings åtgärder med Resource Manager](../azure-resource-manager/management/view-activity-logs.md).
- Information om åtgärder för att fastställa felen under distributionen finns i [Visa distributions åtgärder](../azure-resource-manager/templates/deployment-history.md).
