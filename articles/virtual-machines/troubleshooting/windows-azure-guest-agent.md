---
title: Felsöka Windows Azures gästa Gent
description: Fel sökning av gäst agenten i Windows Azure fungerar inte
services: virtual-machines-windows
ms.service: virtual-machines-windows
author: dkdiksha
manager: dcscontentpm
editor: ''
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/07/2020
ms.author: genli
ms.openlocfilehash: c3295365859ad3291a95b616cccc6fa265237a01
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88264047"
---
# <a name="troubleshooting-windows-azure-guest-agent"></a>Felsöka Windows Azures gästa Gent

Windows Azures gästa Gent är en virtuell dator agent (VM). Den gör det möjligt för den virtuella datorn att kommunicera med infrastruktur styrenheten (den underliggande fysiska server som den virtuella datorn finns på) på IP-168.63.129.16. Detta är en virtuell offentlig IP-adress som underlättar kommunikationen. Mer information finns i [Vad är IP-168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

 Den virtuella datorn som migreras till Azure lokalt eller som har skapats med hjälp av en anpassad avbildning har inte Windows Azures gästa Gent installerad. I dessa fall måste du installera VM-agenten manuellt. Mer information om hur du installerar VM-agenten finns i [Översikt över Azure Virtual Machine agent](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows).

När Windows Azures gästa Gent har installerats kan du se följande tjänster som listas i Services. msc på den virtuella datorn:
 
- Windows Azure gästa Gent tjänst
- Telemetri-tjänst
- RD agent-tjänst

**Tjänsten Windows Azure gästa Gent**: den här tjänsten är den tjänst som ansvarar för all loggning i WAppAgent. log. Den här tjänsten ansvarar för konfigurering av olika tillägg och kommunikation från gäst till värd. 

**Telemetri-tjänst**: den här tjänsten ansvarar för att skicka telemetri-data för den virtuella datorn till backend-servern.

**RD agent-tjänst**: den här tjänsten ansvarar för installationen av gäst agenten. Transparent installations program är också en komponent i RD-agenten som hjälper till att uppgradera andra komponenter och tjänster i gäst agenten. RDAgent ansvarar också för att skicka pulsslag från den virtuella gäst datorn till värd agent på den fysiska servern.

> [!NOTE]
> Från och med version 2.7.41491.971 av den virtuella datorns gästa Gent ingår komponenten telemetri i RDAgent-tjänsten, vilket innebär att du kanske inte ser den här telemetri-tjänsten som visas i nyligen skapade virtuella datorer.

## <a name="checking-agent-status-and-version"></a>Kontrollerar agent status och version

Gå till sidan Egenskaper för virtuell dator i Azure Portal och kontrol lera **agent statusen**. Om Windows Azures gästa Gent fungerar som den ska visas statusen **klar**. Om den virtuella dator agenten **inte är klar** fungerar inte kommandot Extensions och **Kör** på Azure Portal.

## <a name="troubleshooting-vm-agent-that-is-in-not-ready-status"></a>Felsöka VM-agenten som är i status klar

### <a name="step-1-check-whether-the-windows-azure-guest-agent-service-is-installed"></a>Steg 1 kontrol lera om tjänsten Windows Azure gästa Gent är installerad

- Sök efter paketet

    Leta upp mappen C:\WindowsAzure Om du ser GuestAgent-mappen som visar versions numret innebär det att Windows Azures gästa Gent har installerats på den virtuella datorn. Du kan också leta efter det installerade paketet.  Om Windows Azures gästa Gent är installerad på den virtuella datorn kommer paketet att sparas på följande plats: `C:\windows\OEM\GuestAgent\VMAgentPackage.zip` .
    
    Du kan köra följande PowerShell-kommando för att kontrol lera om VM-agenten har distribuerats till den virtuella datorn:
    
    `Get-Az VM -ResourceGroup “RGNAME” – Name “VMNAME” -displayhint expand`
    
    Leta upp egenskapen **ProvisionVMAgent** i utdata och kontrol lera om värdet är inställt på **Sant**. Om det är det innebär det att agenten är installerad på den virtuella datorn.
    
- Kontrol lera tjänsterna och processerna

   Gå till tjänst konsolen (Services. msc) och kontrol lera status för följande tjänster: Windows Azure gästa Gent tjänst, RDAgent-tjänst, tjänsten Windows Azure telemetri och tjänsten Windows Azure Network agent.
 
    Du kan också kontrol lera om dessa tjänster körs genom att undersöka aktivitets hanteraren för följande processer:
       
    - WindowsAzureGuestAgent.exe: tjänsten Windows Azure gästa Gent
    - WaAppAgent.exe: RDAgent-tjänsten
    - WindowsAzureNetAgent.exe: tjänsten Windows Azure Network agent
    - WindowsAzureTelemetryService.exe: tjänsten Windows Azure telemetri

   Om du inte kan hitta de här processerna och tjänsterna betyder det att du inte har Windows Azures gästa Gent installerad.

- Kontrol lera programmet och funktionen

    I kontroll panelen går du till **program och funktioner** för att avgöra om tjänsten Windows Azure gästa Gent är installerad.

Om du inte hittar några paket, tjänster och processer som kör och inte ens ser Windows Azures gästa Gent installerade under program och funktioner, kan du försöka [Installera Windows Azure gästa Gent tjänst](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows). Om gäst agenten inte installeras korrekt kan du [installera VM-agenten offline](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).

Om du kan se tjänsterna och de körs startar du om tjänsten som ser om problemet är löst. Om tjänsterna stoppas startar du dem och väntar några minuter. Kontrol lera sedan om **agent statusen** rapporteras som **klar**. Om du upptäcker att dessa tjänster kraschar kan vissa utomstående processer orsaka att dessa tjänster kraschar. Kontakta [Microsoft Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om du vill ha ytterligare fel sökning av dessa problem.

### <a name="step-2-check-whether-auto-update-is-working"></a>Steg 2 kontrol lera om automatisk uppdatering fungerar

Windows Azures gästa Gent har en funktion för automatisk uppdatering. Den söker automatiskt efter nya uppdateringar och installerar dem. Om funktionen för automatisk uppdatering inte fungerar som den ska kan du prova att avinstallera och installera Windows Azures gästa Gent med hjälp av följande steg:

1. Om Windows Azures gästa Gent visas i **program och funktioner**avinstallerar du Windows Azures gästa Gent.

2. Öppna ett kommando tolks fönster med administratörs behörighet.

3. Stoppa gäst Agent tjänsterna. Om tjänsterna inte stoppas måste du ställa in tjänsterna på **manuell start** och sedan starta om den virtuella datorn.
    ```
    net stop rdagent
    net stop WindowsAzureGuestAgent
    net stop WindowsAzureTelemetryService
    ```
1. Ta bort gäst agents tjänsterna:
    ```
    sc delete rdagent
    sc delete WindowsAzureGuestAgent
    sc delete WindowsAzureTelemetryService
    ```
1. Under `C:\WindowsAzure` skapa en mapp med namnet Old.

1. Flytta alla mappar med namnet paket eller GuestAgent till den gamla mappen.

1. Hämta och installera den senaste versionen av agent installations paketet [härifrån.](https://go.microsoft.comfwlink/?linkid=394789&clcid=0x409) Du måste ha administratörs behörighet för att slutföra installationen.

1. Installera gäst agenten med hjälp av följande kommando:

    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log
    ```
    Kontrol lera sedan om gäst Agent tjänsterna har startats på rätt sätt.
 
    I sällsynta fall där gäst agenten inte installeras korrekt kan du [installera VM-agenten offline](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline).
    

### <a name="step-3-check-whether-the-vm-can-connect-to-the-fabric-controller"></a>Steg 3 kontrol lera om den virtuella datorn kan ansluta till infrastruktur styrenheten

Använd ett verktyg som PsPing för att testa om den virtuella datorn kan ansluta till 168.63.129.16 på portarna 80, 32526 och 443. Om den virtuella datorn inte ansluter som förväntat kontrollerar du om utgående kommunikation över portarna 80, 443 och 32526 är öppen i din lokala brand vägg på den virtuella datorn. Om den här IP-adressen är blockerad kan en virtuell dator agent Visa oväntade beteenden i flera olika scenarier.

## <a name="advanced-troubleshooting"></a>Avancerad felsökning

Händelser för fel sökning av Windows Azure gästa Gent registreras i följande loggfiler:

- C:\WindowsAzure\Logs\WaAppAgent.log
- C:\WindowsAzure\Logs\TransparentInstaller.log

Här följer några vanliga scenarier där Windows Azures gästa Gent kan ange statusen **inte klar** eller sluta fungera som förväntat.

### <a name="agent-stuck-on-starting"></a>Agent fastnar på "startar"

I WaAppAgent-loggen kan du se att agenten fastnar vid start processen och inte kan starta.

**Logg information**
 
[00000007] [05/28/2019 12:58:50.90] [INFO] WindowsAzureGuestAgent startar. Version 2.7.41491.901

**Analys**
 
Den virtuella datorn kör fortfarande den äldre versionen av gäst agenten för Windows Azure. I mappen C:\WindowsAzure kan du märka att flera Windows Azure-gästa Gent instanser är installerade, inklusive flera av samma version. Eftersom flera agent instanser är installerade, startar inte den virtuella datorn den senaste versionen av Windows Azures gästa Gent.

**Lösning**

Avinstallera gäst agenten i Windows Azure manuellt och installera om den genom att följa dessa steg:

1. Öppna kontroll panelen > program och funktioner och avinstallera Windows Azures gästa Gent.
1. Öppna aktivitets hanteraren och stoppa följande tjänster: Windows Azure gästa Gent tjänst, RDAgent-tjänst, tjänsten Windows Azure telemetri och tjänsten Windows Azure Network agent.
1. Under C:\WindowsAzure skapar du en mapp med namnet OLD.
1. Flytta alla mappar med namnet paket eller GuestAgent till den gamla mappen. Flytta också någon av GuestAgent-mapparna i C:\WindowsAzure\logs som startar som GuestAgent_x. x. xxxxx till den gamla mappen.
1. Hämta och installera den senaste versionen av MSI-agenten. Du måste ha administratörs behörighet för att slutföra installationen.
1. Installera gästa Gent med följande MSI-kommando:
    ```
    msiexec.exe /i c:\VMAgentMSI\WindowsAzureVmAgent.2.7.<version>.fre.msi /quiet /L*v c:\VMAgentMSI\msiexec.log`
    ```
1. Kontrol lera att RDAgent, Windows Azures gästa Gent och Windows Azure telemetri-tjänsterna körs nu.
 
1. Kontrol lera WaAppAgent. log för att se till att den senaste versionen av Windows Azures gästa Gent körs.
 
1. Ta bort den gamla mappen under C:\WindowsAzure.
  
### <a name="unable-to-connect-to-wireserver-ip-host-ip"></a>Det går inte att ansluta till WireServer IP (värd-IP) 

Du ser följande fel poster i WaAppAgent. log och telemetri. log:

**Logg information**

```Log sample
[ERROR] GetVersions() failed with exception: Microsoft.ServiceModel.Web.WebProtocolException: Server Error: Service Unavailable (ServiceUnavailable) ---> 
System.Net.WebException: The remote server returned an error: (503) Server Unavailable.
```
```Log sample
[00000011] [12/11/2018 06:27:55.66] [WARN]  (Ignoring) Exception while fetching supported versions from HostGAPlugin: System.Net.WebException: Unable to connect to the remote server 
---> System.Net.Sockets.SocketException: An attempt was made to access a socket in a way forbidden by its access permissions 168.63.129.16:32526
at System.Net.Sockets.Socket.DoConnect(EndPoint endPointSnapshot, SocketAddress socketAddress)
at System.Net.ServicePoint.ConnectSocketInternal(Boolean connectFailure, Socket s4, Socket s6, Socket& socket, IPAddress& address, ConnectSocketState status, IAsyncResult asyncResult, Exception& exception)
--- End of inner exception stack trace ---
at System.Net.WebClient.DownloadDataInternal(Uri address, WebRequest& request)
at System.Net.WebClient.DownloadString(Uri address)
at Microsoft.GuestAgentHostPlugin.Client.GuestInformationServiceClient.GetVersions()
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.HostGAPluginUtility.UpdateHostGAPluginAvailability()`
```
**Analys**

Den virtuella datorn kan inte komma åt wireserver-värd servern.

**Lösning**

1. Eftersom wireserver inte kan nås ansluter du till den virtuella datorn med hjälp av fjärr skrivbord och försöker sedan komma åt följande URL från en webbläsare: http://168.63.129.16/?comp=versions 
1. Om du inte kan komma åt URL: en från steg 1 kontrollerar du nätverks gränssnittet för att avgöra om det har angetts som DHCP-aktiverat och har DNS. Om du vill kontrol lera DHCP-statusen för nätverks gränssnittet kör du följande kommando:  `netsh interface ip show config` .
1. Om DHCP är inaktiverat kör du följande för att se till att du ändrar värdet i gult till namnet på ditt gränssnitt: `netsh interface ip set address name="Name of the interface" source=dhcp` .
1. Sök efter eventuella problem som kan ha orsakats av en brand vägg, en proxy eller annan källa som kan blockera åtkomst till IP-168.63.129.16.
1. Kontrol lera om Windows-brandväggen eller en brand vägg från tredje part blockerar åtkomsten till portarna 80, 443 och 32526. Mer information om varför den här adressen inte ska blockeras finns i [Vad är IP-168.63.129.16](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16).

### <a name="guest-agent-is-stuck-stopping"></a>Gäst agenten har fastnat i att stoppa  

Du ser följande fel poster i WaAppAgent. log:

**Logg information** 

```Log sample
[00000007] [07/18/2019 14:46:28.87] [WARN] WindowsAzureGuestAgent stopping.
[00000007] [07/18/2019 14:46:28.89] [INFO] Uninitializing StateExecutor with WaitForTerminalStateReachedOnEnd : True
[00000004] [07/18/2019 14:46:28.89] [WARN] WindowsAzureGuestAgent could not be stopped. Exception: System.NullReferenceException: Object reference not set to an instance of an object.
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.WaitForExtensionWorkflowComplete(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorBase.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.ContainerStateMachine.GoalStateExecutorForCloud.Uninitialize(Boolean WaitForTerminalStateReachedOnEnd)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentCore.Stop(Boolean waitForTerminalState)
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.DoStopService()
at Microsoft.WindowsAzure.GuestAgent.AgentCore.AgentService.<>c__DisplayClass2.<OnStopProcessing>b__1()
```
**Analys**

Windows Azures gästa Gent fastnar i stopp processen.

**Lösning**

1. Kontrol lera att WaAppAgent.exe körs på den virtuella datorn. Om den inte körs startar du om rdgagent-tjänsten och väntar fem minuter. Avsluta WindowsAzureGuest.exe processen när WaAppAgent.exe körs.
2. Om steg 1 inte löser problemet tar du bort den nuvarande installerade versionen och installerar den senaste versionen av agenten manuellt.

### <a name="npcap-loopback-adapter"></a>Npcap loopback adapter 

Du ser följande fel poster i WaAppAgent. log:
 
```Log sample
[00000006] [06/20/2019 07:44:28.93] [INFO]  Attempting to discover fabric address on interface Npcap Loopback Adapter.
[00000024] [06/20/2019 07:44:28.93] [WARN]  Empty DHCP option data returned
[00000006] [06/20/2019 07:44:28.93] [ERROR] Did not discover fabric address on interface Npcap Loopback Adapter
```
**Analys**

Npcap Loopback Adapter installeras på den virtuella datorn av wireshark. Wireshark är ett verktyg med öppen källkod för profilering av nätverks trafik och analys av paket. Ett sådant verktyg kallas ofta för nätverks analys, Network Protocol Analyzer eller Sniffer.

**Lösning**

Npcap Loopback Adapter installeras förmodligen av WireShark. Försök inaktivera det och kontrol lera om problemet är löst.

## <a name="next-steps"></a>Nästa steg

[Kontakta Microsoft-supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)för att ytterligare felsöka problemet "Windows Azures gästa Gent fungerar inte".