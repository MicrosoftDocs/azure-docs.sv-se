---
title: Det går inte att ansluta med RDP till en virtuell Windows-dator i Azure | Microsoft Docs
description: Felsöka problem när du inte kan ansluta till din virtuella Windows-dator i Azure med hjälp av fjärr skrivbord
keywords: Fel i fjärr skrivbord, anslutning till fjärr skrivbord, det går inte att ansluta till den virtuella datorn, fel sökning av fjärr skrivbord
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: akjosh
ms.openlocfilehash: 66e4ea622f546981d1f0651b20368590e59056d3
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201628"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Felsök fjärr skrivbords anslutningar till en virtuell Azure-dator
Det kan finnas flera orsaker till att det uppstår problem med RDP-anslutningen till den Windows-baserade virtuella Azure-datorn så att du inte kan komma åt den virtuella datorn. Problemet kan röra fjärrskrivbordstjänsten på den virtuella datorn, nätverksanslutningen eller fjärrskrivbordsklienten på din värddator. Den här artikeln vägleder dig igenom några av de vanligaste sätten att lösa problem med RDP-anslutningen. 

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**.

 

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>Snabb fel söknings steg
Försök att ansluta till den virtuella datorn efter varje fel söknings steg:

1. Återställ fjärr skrivbords konfiguration.
2. Kontrol lera regler för nätverks säkerhets grupper/Cloud Services slut punkter.
3. Granska konsol loggar för virtuella datorer.
4. Återställ NÄTVERKSKORTet för den virtuella datorn.
5. Kontrol lera den virtuella datorns Resource Health.
6. Återställ ditt virtuella dator lösen ord.
7. Starta om den virtuella datorn.
8. Distribuera om den virtuella datorn.

Fortsätt att läsa om du behöver mer detaljerade steg och förklaringar. Kontrol lera att lokal nätverks utrustning som routrar och brand väggar inte blockerar utgående TCP-port 3389, enligt beskrivningen i [detaljerade scenarier för RDP-felsökning](detailed-troubleshoot-rdp.md).

> [!TIP]
> Om knappen **Anslut** för den virtuella datorn är nedtonad i portalen och du inte är ansluten till Azure via en [Express väg](../../expressroute/expressroute-introduction.md) eller [VPN-anslutning från plats till plats](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) måste du skapa och tilldela en offentlig IP-adress för din virtuella dator innan du kan använda RDP. Du kan läsa mer om [offentliga IP-adresser i Azure](../../virtual-network/public-ip-addresses.md).


## <a name="ways-to-troubleshoot-rdp-issues"></a>Sätt att felsöka RDP-problem
Du kan felsöka virtuella datorer som skapats med hjälp av distributions modellen för Resource Manager genom att använda någon av följande metoder:

* Azure Portal – bra om du snabbt behöver återställa RDP-konfigurationen eller användarautentiseringsuppgifter och du inte har installerat Azure-verktygen.
* Azure PowerShell – om du är van att använda en PowerShell-prompt kan du snabbt återställa RDP-konfigurationen eller användarautentiseringsuppgifter med hjälp av Azure PowerShell-cmdletar.

Du kan också hitta steg för att felsöka virtuella datorer som skapats med den [klassiska distributions modellen](#troubleshoot-vms-created-using-the-classic-deployment-model).

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Felsöka med hjälp av Azure Portal
Efter varje fel söknings steg försöker du ansluta till den virtuella datorn igen. Om du fortfarande inte kan ansluta kan du prova nästa steg.

1. **Återställ RDP-anslutningen**. I det här fel söknings steget återställs RDP-konfigurationen när fjärr anslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, till exempel.
   
    Välj den virtuella datorn i Azure Portal. Bläddra nedåt i fönstret inställningar för avsnittet **support och fel sökning** längst ned i listan. Klicka på knappen **Återställ lösen ord** . Ange **läget** för att **bara återställa konfigurationen** och klicka sedan på knappen **Uppdatera** :
   
    ![Återställ R D P-konfigurationen i Azure Portal.](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **Kontrol lera regler för nätverks säkerhets grupper**. Använd [kontrol lera IP-flöde](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) för att bekräfta om en regel i en nätverks säkerhets grupp blockerar trafik till eller från en virtuell dator. Du kan också granska gällande säkerhets grupps regler för att säkerställa att NSG-regeln för inkommande "Tillåt" finns och prioriteras för RDP-porten (standard 3389). Mer information finns i [använda effektiva säkerhets regler för att felsöka trafik flöde för virtuella datorer](../../virtual-network/diagnose-network-traffic-filter-problem.md).

3. **Granska VM Boot Diagnostics**. Det här fel söknings steget granskar VM-konsolens loggar för att avgöra om den virtuella datorn rapporterar ett problem. Det är inte alla virtuella datorer som har startdiagnostik aktiverat, så det kan vara valfritt att göra detta.
   
    Vissa fel söknings steg ligger utanför den här artikelns omfattning, men kan tyda på ett bredare problem som påverkar RDP-anslutningen. Mer information om hur du läser konsol loggar och skärm bilder av virtuella datorer finns i [startdiagnostik för virtuella datorer](boot-diagnostics.md).

4. **Återställ nätverkskortet för den virtuella datorn**. Mer information finns i [så här återställer du NIC för Azure Windows VM](./reset-network-interface.md).
5. **Kontrol lera den virtuella datorns Resource Health**. Det här fel söknings steget verifierar att det inte finns några kända problem med Azure-plattformen som kan påverka anslutningen till den virtuella datorn.
   
    Välj den virtuella datorn i Azure Portal. Bläddra nedåt i fönstret inställningar för avsnittet **support och fel sökning** längst ned i listan. Klicka på knappen **resurs hälsa** . En felfria VM-rapporter som är **tillgängliga**:
   
    ![Kontrol lera V M-resurs hälsan i Azure Portal.](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **Återställ användarautentiseringsuppgifter**. I det här fel söknings steget återställs lösen ordet på ett lokalt administratörs konto när du är osäker eller har glömt autentiseringsuppgifterna.  När du har loggat in på den virtuella datorn bör du återställa lösen ordet för användaren.
   
    Välj den virtuella datorn i Azure Portal. Bläddra nedåt i fönstret inställningar för avsnittet **support och fel sökning** längst ned i listan. Klicka på knappen **Återställ lösen ord** . Kontrol lera att **läget** är inställt på **Återställ lösen ord** och ange ditt användar namn och ett nytt lösen ord. Klicka slutligen på knappen **Uppdatera** :
   
    ![Återställ användarautentiseringsuppgifter i Azure Portal.](./media/troubleshoot-rdp-connection/reset-password.png)
7. **Starta om den virtuella** datorn. Det här fel söknings steget kan åtgärda eventuella underliggande problem som den virtuella datorn har.
   
    Välj den virtuella datorn i Azure Portal och klicka på fliken **Översikt** . Klicka på knappen **starta om** :
   
    ![Starta om V M i Azure Portal.](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **Distribuera om den virtuella** datorn. Det här fel söknings steget distribuerar om den virtuella datorn till en annan värd i Azure för att åtgärda eventuella underliggande plattforms-eller nätverks problem.
   
    Välj den virtuella datorn i Azure Portal. Bläddra nedåt i fönstret inställningar för avsnittet **support och fel sökning** längst ned i listan. Klicka på knappen **distribuera** igen och klicka sedan på **distribuera** om:
   
    ![Distribuera om den virtuella datorn i Azure Portal](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    När den här åtgärden har slutförts försvinner tillfälliga disk data och dynamiska IP-adresser som är associerade med den virtuella datorn uppdateras.

9. **Verifiera routning**. Använd Network Watcher [nästa hopp](../../network-watcher/diagnose-vm-network-routing-problem.md) -funktion för att bekräfta att en väg inte hindrar trafik från att dirigeras till eller från en virtuell dator. Du kan också granska effektiva vägar för att se alla effektiva vägar för ett nätverks gränssnitt. Mer information finns i [använda effektiva vägar för att felsöka trafik flöde för virtuella datorer](../../virtual-network/diagnose-network-routing-problem.md).

10. Kontrol lera att den lokala brand väggen eller brand väggen på datorn tillåter utgående TCP 3389-trafik till Azure.

Om du fortfarande stöter på RDP-problem kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerade metoder och anvisningar för RDP-felsökning](detailed-troubleshoot-rdp.md).

## <a name="troubleshoot-using-azure-powershell"></a>Felsöka med Azure PowerShell
Om du inte redan har gjort det kan du [Installera och konfigurera de senaste Azure PowerShell](/powershell/azure/).

I följande exempel används variabler som `myResourceGroup` , `myVM` , och `myVMAccessExtension` . Ersätt dessa variabel namn och platser med dina egna värden.

> [!NOTE]
> Du återställer användarautentiseringsuppgifter och RDP-konfiguration med hjälp av cmdleten [set-AzVMAccessExtension](/powershell/module/az.compute/set-azvmaccessextension) PowerShell. I följande exempel `myVMAccessExtension` är ett namn som du anger som en del av processen. Om du tidigare har arbetat med VMAccessAgent kan du hämta namnet på det befintliga tillägget genom `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"` att använda för att kontrol lera egenskaperna för den virtuella datorn. Om du vill visa namnet tittar du under avsnittet tillägg i utdata.

Efter varje fel söknings steg försöker du ansluta till den virtuella datorn igen. Om du fortfarande inte kan ansluta kan du prova nästa steg.

1. **Återställ RDP-anslutningen**. I det här fel söknings steget återställs RDP-konfigurationen när fjärr anslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, till exempel.
   
    I följande exempel återställs RDP-anslutningen på en virtuell dator med namnet `myVM` på `WestUS` platsen och i resurs gruppen med namnet `myResourceGroup` :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **Kontrol lera regler för nätverks säkerhets grupper**. Det här fel söknings steget verifierar att du har en regel i nätverks säkerhets gruppen för att tillåta RDP-trafik. Standard porten för RDP är TCP-port 3389. En regel för att tillåta RDP-trafik kan inte skapas automatiskt när du skapar den virtuella datorn.
   
    Först tilldelar du alla konfigurations data för din nätverks säkerhets grupp till `$rules` variabeln. I följande exempel hämtas information om nätverks säkerhets gruppen `myNetworkSecurityGroup` som heter i resurs gruppen med namnet `myResourceGroup` :
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    Nu kan du se de regler som har kon figurer ATS för den här nätverks säkerhets gruppen. Kontrol lera att det finns en regel för att tillåta TCP-port 3389 för inkommande anslutningar på följande sätt:
   
    ```powershell
    $rules.SecurityRules
    ```
   
    I följande exempel visas en giltig säkerhets regel som tillåter RDP-trafik. Du kan se `Protocol` , `DestinationPortRange` ,, `Access` och `Direction` konfigureras på rätt sätt:
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    Om du inte har en regel som tillåter RDP-trafik [skapar du en regel för nätverks säkerhets grupper](../windows/nsg-quickstart-powershell.md). Tillåt TCP-port 3389.
3. **Återställ användarautentiseringsuppgifter**. I det här fel söknings steget återställs lösen ordet på det lokala administratörs kontot som du anger när du är osäker på, eller har glömt, autentiseringsuppgifterna.
   
    Börja med att ange användar namnet och ett nytt lösen ord genom att tilldela autentiseringsuppgifter till `$cred` variabeln på följande sätt:
   
    ```powershell
    $cred=Get-Credential
    ```
   
    Uppdatera nu autentiseringsuppgifterna på den virtuella datorn. I följande exempel uppdateras autentiseringsuppgifterna på en virtuell dator med namnet på `myVM` `WestUS` platsen och i resurs gruppen med namnet `myResourceGroup` :
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **Starta om den virtuella** datorn. Det här fel söknings steget kan åtgärda eventuella underliggande problem som den virtuella datorn har.
   
    I följande exempel startas den virtuella datorn om med namnet `myVM` i resurs gruppen med namnet `myResourceGroup` :
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **Distribuera om den virtuella** datorn. Det här fel söknings steget distribuerar om den virtuella datorn till en annan värd i Azure för att åtgärda eventuella underliggande plattforms-eller nätverks problem.
   
    I följande exempel omdistribueras den virtuella datorn med namnet `myVM` på `WestUS` platsen och i resurs gruppen med namnet `myResourceGroup` :
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **Verifiera routning**. Använd Network Watcher [nästa hopp](../../network-watcher/diagnose-vm-network-routing-problem.md) -funktion för att bekräfta att en väg inte hindrar trafik från att dirigeras till eller från en virtuell dator. Du kan också granska effektiva vägar för att se alla effektiva vägar för ett nätverks gränssnitt. Mer information finns i [använda effektiva vägar för att felsöka trafik flöde för virtuella datorer](../../virtual-network/diagnose-network-routing-problem.md).

7. Kontrol lera att den lokala brand väggen eller brand väggen på datorn tillåter utgående TCP 3389-trafik till Azure.

Om du fortfarande stöter på RDP-problem kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerade metoder och anvisningar för RDP-felsökning](detailed-troubleshoot-rdp.md).

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>Felsöka virtuella datorer som skapats med den klassiska distributions modellen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


Försök att ansluta till den virtuella datorn efter varje fel söknings steg.

1. **Återställ RDP-anslutningen**. I det här fel söknings steget återställs RDP-konfigurationen när fjärr anslutningar är inaktiverade eller regler för Windows-brandväggen blockerar RDP, till exempel.
   
    Välj den virtuella datorn i Azure Portal. Klicka på **... Mer** knapp och klicka sedan på **Återställ fjärråtkomst**:
   
    ![Återställ R D P-konfigurationen i Azure Portal för V M med hjälp av den klassiska distributions modellen.](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Verifiera Cloud Services slut punkter**. I det här fel söknings steget kontrol leras att du har slut punkter i Cloud Services att tillåta RDP-trafik. Standard porten för RDP är TCP-port 3389. En regel för att tillåta RDP-trafik kan inte skapas automatiskt när du skapar den virtuella datorn.
   
   Välj den virtuella datorn i Azure Portal. Klicka på knappen **slut punkter** om du vill visa de slut punkter som är konfigurerade för den virtuella datorn. Kontrol lera att det finns slut punkter som tillåter RDP-trafik på TCP-port 3389.
   
   I följande exempel visas giltiga slut punkter som tillåter RDP-trafik:
   
   ![Verifiera Cloud Services slut punkter i Azure Portal för V M med hjälp av den klassiska distributions modellen.](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   Om du inte har en slut punkt som tillåter RDP-trafik [skapar du en Cloud Services slut punkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints). Tillåt TCP till privat port 3389.
3. **Granska VM Boot Diagnostics**. Det här fel söknings steget granskar VM-konsolens loggar för att avgöra om den virtuella datorn rapporterar ett problem. Det är inte alla virtuella datorer som har startdiagnostik aktiverat, så det kan vara valfritt att göra detta.
   
    Vissa fel söknings steg ligger utanför den här artikelns omfattning, men kan tyda på ett bredare problem som påverkar RDP-anslutningen. Mer information om hur du läser konsol loggar och skärm bilder av virtuella datorer finns i [startdiagnostik för virtuella datorer](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).
4. **Kontrol lera den virtuella datorns Resource Health**. Det här fel söknings steget verifierar att det inte finns några kända problem med Azure-plattformen som kan påverka anslutningen till den virtuella datorn.
   
    Välj den virtuella datorn i Azure Portal. Bläddra nedåt i fönstret inställningar för avsnittet **support och fel sökning** längst ned i listan. Klicka på knappen **Resource Health** . En felfria VM-rapporter som är **tillgängliga**:
   
    ![Kontrol lera V M-resurs hälsan i Azure Portal för V M med hjälp av den klassiska distributions modellen.](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **Återställ användarautentiseringsuppgifter**. I det här fel söknings steget återställs lösen ordet på det lokala administratörs kontot som du anger när du är osäker eller har glömt autentiseringsuppgifterna.  När du har loggat in på den virtuella datorn bör du återställa lösen ordet för användaren.
   
    Välj den virtuella datorn i Azure Portal. Bläddra nedåt i fönstret inställningar för avsnittet **support och fel sökning** längst ned i listan. Klicka på knappen **Återställ lösen ord** . Ange ditt användar namn och ett nytt lösen ord. Klicka slutligen på knappen **Spara** :
   
    ![Återställ användarautentiseringsuppgifter i Azure Portal för V M med hjälp av den klassiska distributions modellen.](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **Starta om den virtuella** datorn. Det här fel söknings steget kan åtgärda eventuella underliggande problem som den virtuella datorn har.
   
    Välj den virtuella datorn i Azure Portal och klicka på fliken **Översikt** . Klicka på knappen **starta om** :
   
    ![Starta om V M i Azure Portal för V M med hjälp av den klassiska distributions modellen.](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. Kontrol lera att den lokala brand väggen eller brand väggen på datorn tillåter utgående TCP 3389-trafik till Azure.

Om du fortfarande stöter på RDP-problem kan du [öppna en supportbegäran](https://azure.microsoft.com/support/options/) eller läsa [mer detaljerade metoder och anvisningar för RDP-felsökning](detailed-troubleshoot-rdp.md).

## <a name="troubleshoot-specific-rdp-errors"></a>Felsöka specifika RDP-fel
Du kan stöta på ett fel meddelande när du försöker ansluta till den virtuella datorn via RDP. Följande är de vanligaste fel meddelandena:

* [Fjärrsessionen kopplades från eftersom det inte finns några fjärr skrivbords licens servrar tillgängliga för att tillhandahålla en licens](troubleshoot-specific-rdp-errors.md#rdplicense).
* [Fjärr skrivbord kan inte hitta datorn "name"](troubleshoot-specific-rdp-errors.md#rdpname).
* [Ett autentiseringsfel har inträffat. Det går inte att kontakta den lokala säkerhets kontrollen](troubleshoot-specific-rdp-errors.md#rdpauth).
* [Windows-säkerhets fel: autentiseringsuppgifterna fungerade inte](troubleshoot-specific-rdp-errors.md#wincred).
* [Datorn kan inte ansluta till fjärrdatorn](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>Ytterligare resurser
Om ingen av dessa fel har inträffat och du fortfarande inte kan ansluta till den virtuella datorn via fjärr skrivbord läser du den detaljerade [fel söknings guiden för fjärr skrivbord](detailed-troubleshoot-rdp.md).
* Fel söknings steg för att komma åt program som körs på en virtuell dator finns i [Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).
* Om du har problem med att använda SSH (Secure Shell) för att ansluta till en virtuell Linux-dator i Azure läser du [FELSÖKA SSH-anslutningar till en virtuell Linux-dator i Azure](./troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
