---
title: Detaljerad fel sökning av fjärr skrivbord i Azure | Microsoft Docs
description: Granska detaljerade fel söknings steg för fjärr skrivbords fel där du inte kan gå till virtuella Windows-datorer i Azure
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
keywords: Det går inte att ansluta till fjärr skrivbord, felsöka fjärr skrivbord, fjärr skrivbord kan inte ansluta, fjärr skrivbords fel, fel sökning av fjärr skrivbord, problem med fjärr skrivbord
ms.assetid: 9da36f3d-30dd-44af-824b-8ce5ef07e5e0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 5782765504eb1e0cb57558d3d4772d08de6b4d25
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97913115"
---
# <a name="detailed-troubleshooting-steps-for-remote-desktop-connection-issues-to-windows-vms-in-azure"></a>Detaljerade åtgärder för felsökning av problem med fjärrskrivbordsanslutning till virtuella Windows-datorer i Azure
Den här artikeln innehåller detaljerade fel söknings steg för att diagnostisera och åtgärda komplexa fjärr skrivbords fel för Windows-baserade virtuella Azure-datorer.

> [!IMPORTANT]
> För att undvika vanliga fjärr skrivbords fel måste du läsa [artikeln om grundläggande fel sökning för fjärr skrivbord](troubleshoot-rdp-connection.md) innan du fortsätter.

Du kan stöta på ett fel meddelande om fjärr skrivbord som inte liknar något av de fel meddelanden som beskrivs i [den grundläggande fel söknings guiden för fjärr skrivbord](troubleshoot-rdp-connection.md). Följ de här stegen för att ta reda på varför fjärr skrivbords klienten (RDP) inte kan ansluta till RDP-tjänsten på den virtuella Azure-datorn.


Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna på [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Alternativt kan du också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och klicka på **Hämta support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="components-of-a-remote-desktop-connection"></a>Komponenter i en fjärr skrivbords anslutning
Följande komponenter ingår i en RDP-anslutning:

![Ett diagram som visar de komponenter som ingår i en anslutning till fjärr skrivbord (RDP).](./media/detailed-troubleshoot-rdp/tshootrdp_0.png)

Innan du fortsätter kan det vara bra att granska vad som har ändrats sedan den senaste lyckade fjärr skrivbords anslutningen till den virtuella datorn. Till exempel:

* Den offentliga IP-adressen för den virtuella datorn eller moln tjänsten som innehåller den virtuella datorn (kallas även den virtuella IP-adressen [VIP](https://en.wikipedia.org/wiki/Virtual_IP_address)) har ändrats. RDP-felet kan bero på att DNS-klientcachen fortfarande har den *gamla IP-adressen* registrerad för DNS-namnet. Töm cacheminnet för DNS-klienten och försök att ansluta den virtuella datorn igen. Eller försök att ansluta direkt med den nya VIP: en.
* Du använder ett program från tredje part för att hantera dina fjärr skrivbords anslutningar i stället för att använda den anslutning som genererades av Azure Portal. Kontrol lera att program konfigurationen innehåller rätt TCP-port för fjärr skrivbords trafiken. Du kan kontrol lera den här porten för en klassisk virtuell dator i [Azure Portal](https://portal.azure.com)genom att klicka på den virtuella datorns inställningar > slut punkter.

## <a name="preliminary-steps"></a>Preliminära steg
Innan du fortsätter till detaljerad fel sökning,

* Kontrol lera statusen för den virtuella datorn i Azure Portal för eventuella uppenbara problem.
* Följ [snabb korrigerings stegen för vanliga RDP-fel i den grundläggande fel söknings guiden](troubleshoot-rdp-connection.md#quick-troubleshooting-steps).
* För anpassade avbildningar kontrollerar du att den virtuella hård disken är korrekt för beredd innan du laddar upp den. Mer information finns i [förbereda en virtuell Windows-disk eller VHDX att ladda upp till Azure](../windows/prepare-for-upload-vhd-image.md).


Försök att ansluta till den virtuella datorn via fjärr skrivbord igen efter de här stegen.

## <a name="detailed-troubleshooting-steps"></a>Detaljerade felsökningsanvisningar
Fjärr skrivbords klienten kanske inte kan komma åt fjärr skrivbords tjänsten på den virtuella Azure-datorn på grund av problem med följande källor:

* [Klient dator för fjärr skrivbord](#source-1-remote-desktop-client-computer)
* [Organisationens intranät gräns enhet](#source-2-organization-intranet-edge-device)
* [Moln tjänst slut punkt och åtkomst kontrol lista (ACL)](#source-3-cloud-service-endpoint-and-acl)
* [Nätverkssäkerhetsgrupper](#source-4-network-security-groups)
* [Windows-baserad virtuell Azure-dator](#source-5-windows-based-azure-vm)

## <a name="source-1-remote-desktop-client-computer"></a>Källa 1: klient dator för fjärr skrivbord
Kontrol lera att datorn kan göra fjärr skrivbords anslutningar till en annan lokal, Windows-baserad dator.

![Ett diagram över komponenterna i en fjärr skrivbords anslutning (RDP) med RDP-klienten markerad och en pil som pekar på en annan lokal dator som anger en anslutning.](./media/detailed-troubleshoot-rdp/tshootrdp_1.png)

Om du inte kan det kan du söka efter följande inställningar på datorn:

* En lokal brand Väggs inställning som blockerar fjärr skrivbords trafik.
* Lokalt installerat program för klient-proxy som förhindrar anslutning till fjärr skrivbord.
* Lokalt installerat program vara för nätverks övervakning som förhindrar fjärr skrivbords anslutningar.
* Andra typer av säkerhets program som antingen övervakar trafik eller tillåter/nekar vissa typer av trafik som förhindrar fjärr skrivbords anslutningar.

I alla dessa fall inaktiverar du tillfälligt program varan och försöker ansluta till en lokal dator via fjärr skrivbord. Om du kan ta reda på den faktiska orsaken på det här sättet kan du arbeta med nätverks administratören för att rätta till program varu inställningarna så att de tillåter fjärr skrivbords anslutningar.

## <a name="source-2-organization-intranet-edge-device"></a>Källa 2: organisationens intranät gräns enhet
Kontrol lera att en dator som är direktansluten till Internet kan göra fjärr skrivbords anslutningar till din virtuella Azure-dator.

![Ett diagram över komponenterna i en anslutning till fjärr skrivbord (RDP) med en RDP-klient som är ansluten till Internet markerad och en pil som pekar på en virtuell Azure-dator som anger en anslutning.](./media/detailed-troubleshoot-rdp/tshootrdp_2.png)

Om du inte har en dator som är direkt ansluten till Internet skapar du och testar med en ny virtuell Azure-dator i en resurs grupp eller moln tjänst. Mer information finns i [skapa en virtuell dator som kör Windows i Azure](../windows/quick-create-portal.md). Du kan ta bort den virtuella datorn och resurs gruppen eller moln tjänsten efter testet.

Om du kan skapa en anslutning till fjärr skrivbord med en dator som är direktansluten till Internet, kontrollerar du organisationens intranät gräns enhet för:

* En intern brand vägg som blockerar HTTPS-anslutningar till Internet.
* En proxyserver som förhindrar fjärr skrivbords anslutningar.
* Intrångs identifiering eller program vara för nätverks övervakning som körs på enheter i Edge-nätverket som förhindrar fjärr skrivbords anslutningar.

Arbeta med nätverks administratören för att korrigera inställningarna för organisationens intranät gräns enhet för att tillåta HTTPS-baserade fjärr skrivbords anslutningar till Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Källa 3: moln tjänstens slut punkt och ACL

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

För virtuella datorer som skapats med den klassiska distributions modellen kontrollerar du att en annan virtuell Azure-dator som finns i samma moln tjänst eller virtuella nätverk kan göra fjärr skrivbords anslutningar till din virtuella Azure-dator.

![Ett diagram över komponenterna i en anslutning till fjärr skrivbord (RDP) med en virtuell Azure-dator markerad och en pil som pekar på en annan virtuell Azure-dator i samma moln tjänst som indikerar en anslutning.](./media/detailed-troubleshoot-rdp/tshootrdp_3.png)

> [!NOTE]
> För virtuella datorer som skapats i Resource Manager, gå över till [källa 4: nätverks säkerhets grupper](#source-4-network-security-groups).

Skapa en annan virtuell dator i samma moln tjänst eller virtuella nätverk. Följ stegen i [skapa en virtuell dator som kör Windows i Azure](../windows/quick-create-portal.md). Ta bort den virtuella test datorn när testet har slutförts.

Om du kan ansluta via fjärr skrivbord till en virtuell dator i samma moln tjänst eller virtuellt nätverk kontrollerar du följande inställningar:

* Slut punkts konfigurationen för fjärr skrivbords trafik på den virtuella mål datorn: den privata TCP-porten för slut punkten måste matcha den TCP-port som den virtuella datorns fjärr skrivbords tjänst lyssnar på (Standardvärdet är 3389).
* Åtkomst kontrol listan för fjärr skrivbords trafikens slut punkt på den virtuella mål datorn: ACL: er låter dig ange tillåten eller nekad inkommande trafik från Internet baserat på dess käll-IP-adress. Felkonfigurerade ACL: er kan förhindra inkommande fjärr skrivbords trafik till slut punkten. Kontrol lera dina ACL: er för att säkerställa att inkommande trafik från dina offentliga IP-adresser för proxyservern eller andra gräns servrar tillåts. Mer information finns i [Vad är en lista över nätverks Access Control (ACL)?](/previous-versions/azure/virtual-network/virtual-networks-acl)

Om du vill kontrol lera om slut punkten är orsaken till problemet tar du bort den aktuella slut punkten och skapar en ny och väljer en slumpmässig port i intervallet 49152 – 65535 för det externa port numret. Mer information finns i [så här konfigurerar du slut punkter till en virtuell dator](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints?toc=/azure/virtual-machines/windows/classic/toc.json).

## <a name="source-4-network-security-groups"></a>Källa 4: nätverks säkerhets grupper
Nätverks säkerhets grupper ger mer detaljerad kontroll över tillåten inkommande och utgående trafik. Du kan skapa regler som spänner över undernät och moln tjänster i ett virtuellt Azure-nätverk.

Använd [kontrol lera IP-flöde](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) för att bekräfta om en regel i en nätverks säkerhets grupp blockerar trafik till eller från en virtuell dator. Du kan också granska gällande säkerhets grupps regler för att säkerställa att NSG-regeln för inkommande "Tillåt" finns och prioriteras för RDP-porten (standard 3389). Mer information finns i [använda effektiva säkerhets regler för att felsöka trafik flöde för virtuella datorer](../../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="source-5-windows-based-azure-vm"></a>Källa 5: Windows-baserad virtuell Azure-dator
![Ett diagram över komponenterna i en anslutning till fjärr skrivbord (RDP) med en virtuell Azure-dator markerad i en moln tjänst och ett meddelande om att det kan vara en "möjlig problem källa". En blå linje indikerar att regler för nätverks säkerhets grupper kan blockera trafik till eller från den virtuella Azure-datorn.](./media/detailed-troubleshoot-rdp/tshootrdp_5.png)

Följ anvisningarna i [den här artikeln](./reset-rdp.md). Den här artikeln återställer fjärr skrivbords tjänsten på den virtuella datorn:

* Aktivera standard regeln "fjärr skrivbord" i Windows-brandväggen (TCP-port 3389).
* Aktivera fjärr skrivbords anslutningar genom att ange registervärdet HKLM\System\CurrentControlSet\Control\Terminal Server\fDenyTSConnections till 0.

Försök att ansluta från datorn igen. Om du fortfarande inte kan ansluta via fjärr skrivbord kontrollerar du följande möjliga problem:

* Fjärr skrivbords tjänsten körs inte på den virtuella mål datorn.
* Fjärr skrivbords tjänsten lyssnar inte på TCP-port 3389.
* Windows-brandväggen eller en annan lokal brand vägg har en utgående regel som förhindrar fjärr skrivbords trafik.
* Intrångs identifiering eller program vara för nätverks övervakning som körs på den virtuella Azure-datorn förhindrar fjärr skrivbords anslutningar.

För virtuella datorer som skapats med den klassiska distributions modellen kan du använda en fjärran sluten Azure PowerShell-session till den virtuella Azure-datorn. Först måste du installera ett certifikat för den virtuella datorns värd moln tjänst. Gå till [Konfigurera säker fjärran sluten PowerShell-åtkomst till Azure Virtual Machines](https://gallery.technet.microsoft.com/scriptcenter/Configures-Secure-Remote-b137f2fe) och ladda ned **InstallWinRMCertAzureVM.ps1** -skript filen till den lokala datorn.

Installera sedan Azure PowerShell om du inte redan gjort det. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/).

Öppna sedan en Azure PowerShell kommando tolk och ändra den aktuella mappen till platsen för **InstallWinRMCertAzureVM.ps1** skript filen. Om du vill köra ett Azure PowerShell skript måste du ange rätt körnings princip. Kör kommandot **Get-ExecutionPolicy** för att fastställa den aktuella policy nivån. Information om hur du anger lämplig nivå finns i [set-ExecutionPolicy](/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-5.1&preserve-view=true).

Fyll sedan i namnet på din Azure-prenumeration, moln tjänst namnet och namnet på den virtuella datorn (ta bort < och > tecken) och kör sedan dessa kommandon.

```powershell
$subscr="<Name of your Azure subscription>"
$serviceName="<Name of the cloud service that contains the target virtual machine>"
$vmName="<Name of the target virtual machine>"
.\InstallWinRMCertAzureVM.ps1 -SubscriptionName $subscr -ServiceName $serviceName -Name $vmName
```

Du kan hämta rätt prenumerations namn från egenskapen *SubscriptionName* för visningen av kommandot **Get-AzureSubscription** . Du kan hämta namnet på moln tjänsten för den virtuella datorn från kolumnen *ServiceName* i visningen av kommandot **Get-AzureVM** .

Kontrol lera om du har det nya certifikatet. Öppna en snapin-modul för certifikat för den aktuella användaren och titta i mappen **Trusted Root Certificate Authorities\Certificates** . Du bör se ett certifikat med DNS-namnet för din moln tjänst i kolumnen utfärdat till (exempel: cloudservice4testing.cloudapp.net).

Starta sedan en fjärrAzure PowerShell-session med hjälp av dessa kommandon.

```powershell
$uri = Get-AzureWinRMUri -ServiceName $serviceName -Name $vmName
$creds = Get-Credential
Enter-PSSession -ConnectionUri $uri -Credential $creds
```

När du har angett giltiga autentiseringsuppgifter för administratör bör du se något som liknar följande Azure PowerShell prompt:

```powershell
[cloudservice4testing.cloudapp.net]: PS C:\Users\User1\Documents>
```

Den första delen av den här prompten är namnet på moln tjänsten som innehåller den virtuella mål datorn, vilket kan skilja sig från "cloudservice4testing.cloudapp.net". Nu kan du utfärda Azure PowerShell kommandon för den här moln tjänsten för att undersöka de problem som nämns och korrigera konfigurationen.

### <a name="to-manually-correct-the-remote-desktop-services-listening-tcp-port"></a>Korrigera Fjärrskrivbordstjänster lyssnings-TCP-port manuellt
Kör det här kommandot i den fjärranslutna Azure PowerShell-sessionen.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Egenskapen port nummer visar det aktuella port numret. Om det behövs ändrar du Port numret för fjärr skrivbord tillbaka till standardvärdet (3389) med hjälp av det här kommandot.

```powershell
Set-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber" -Value 3389
```

Kontrol lera att porten har ändrats till 3389 med hjälp av det här kommandot.

```powershell
Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp" -Name "PortNumber"
```

Avsluta fjärrAzure PowerShell-sessionen genom att använda det här kommandot.

```powershell
Exit-PSSession
```

Kontrol lera att fjärr skrivbords slut punkten för den virtuella Azure-datorn också använder TCP-port 3398 som intern port. Starta om den virtuella Azure-datorn och försök att ansluta till fjärr skrivbordet igen.

## <a name="additional-resources"></a>Ytterligare resurser
[Återställa ett lösen ord eller fjärr skrivbords tjänsten för virtuella Windows-datorer](./reset-rdp.md)

[Hur du installerar och konfigurerar Azure PowerShell](/powershell/azure/)

[Felsök SSH-anslutningar (Secure Shell) till en Linux-baserad virtuell Azure-dator](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json)

[Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
