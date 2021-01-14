---
title: Felsök problem med SSH-anslutningen till en virtuell Azure-dator | Microsoft Docs
description: Fel sökning av problem som "SSH-anslutning misslyckades" eller "SSH-anslutning nekad" för en virtuell Azure-dator som kör Linux.
keywords: ssh-anslutningen nekades, SSH-fel, Azure SSH, SSH-anslutning misslyckades
services: virtual-machines-linux
documentationcenter: ''
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 05/30/2017
ms.author: genli
ms.openlocfilehash: 63c1e388ecd53d9b827e45a1fa78bdb6feeaab21
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201951"
---
# <a name="troubleshoot-ssh-connections-to-an-azure-linux-vm-that-fails-errors-out-or-is-refused"></a>Felsöka SSH-anslutningar till en virtuell Linux-dator som misslyckas, returnerar fel eller avvisas
Den här artikeln hjälper dig att hitta och åtgärda de problem som uppstår på grund av SSH-fel (Secure Shell), SSH-anslutningsfel eller SSH nekas när du försöker ansluta till en virtuell Linux-dator (VM). Du kan använda Azure Portal, Azure CLI eller VM Access-tillägget för Linux för att felsöka och lösa anslutnings problem.


Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj **få support**. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).

## <a name="quick-troubleshooting-steps"></a>Snabb fel söknings steg
Försök att ansluta till den virtuella datorn efter varje fel söknings steg.

1. [Återställ SSH-konfigurationen](#reset-the-ssh-configuration).
2. [Återställ](#reset-ssh-credentials-for-a-user) användarens autentiseringsuppgifter.
3. Kontrol lera att reglerna för [nätverks säkerhets grupper](../../virtual-network/network-security-groups-overview.md) tillåter SSH-trafik.
   * Se till att det finns en [regel för nätverks säkerhets grupp](#check-security-rules) som tillåter SSH-trafik (som standard TCP-port 22).
   * Du kan inte använda omdirigering av portar/mappning utan att använda en Azure Load Balancer.
4. Kontrol lera den [virtuella datorns resurs hälsa](../../service-health/resource-health-overview.md).
   * Se till att den virtuella datorn rapporterar som felfri.
   * Om du har [aktiverat startdiagnostik](boot-diagnostics.md)kontrollerar du att den virtuella datorn inte rapporterar start fel i loggarna.
5. [Starta om den virtuella datorn](#restart-a-vm).
6. [Distribuera om den virtuella datorn](#redeploy-a-vm).

Fortsätt att läsa för mer detaljerade fel söknings steg och förklaringar.

## <a name="available-methods-to-troubleshoot-ssh-connection-issues"></a>Tillgängliga metoder för att felsöka problem med SSH-anslutningen
Du kan återställa autentiseringsuppgifter eller SSH-konfiguration med någon av följande metoder:

* [Azure Portal](#use-the-azure-portal) – bra om du snabbt behöver återställa SSH-konfigurationen eller SSH-nyckeln och du inte har installerat Azure-verktygen.
* [Serie konsol för virtuell Azure-dator](./serial-console-linux.md) – den virtuella datorns serie konsol fungerar oavsett SSH-konfigurationen, och du får en interaktiv konsol till den virtuella datorn. I själva verket är "inte SSH"-situationer särskilt vad serie konsolen har utformats för att hjälpa dig att lösa problemet. Mer information nedan.
* [Azure CLI](#use-the-azure-cli) – om du redan är på kommando raden kan du snabbt återställa SSH-konfigurationen eller autentiseringsuppgifterna. Om du arbetar med en klassisk virtuell dator kan du använda den [klassiska Azure CLI](#use-the-azure-classic-cli).
* [Azure tillägget vmaccessforlinux-tillägg](#use-the-vmaccess-extension) – skapa och återanvänd JSON-definitionsfiler för att återställa SSH-konfigurationen eller användarautentiseringsuppgifter.

Efter varje fel söknings steg försöker du ansluta till den virtuella datorn igen. Om du fortfarande inte kan ansluta kan du prova nästa steg.

## <a name="use-the-azure-portal"></a>Använda Azure-portalen
Azure Portal är ett snabbt sätt att återställa SSH-konfigurationen eller användarautentiseringsuppgifter utan att installera några verktyg på den lokala datorn.

Börja genom att välja den virtuella datorn i Azure Portal. Rulla ned till avsnittet **support och fel sökning** och välj **Återställ lösen ord** som i följande exempel:

![Återställ SSH-konfiguration eller autentiseringsuppgifter i Azure Portal](./media/troubleshoot-ssh-connection/reset-credentials-using-portal.png)

### <a name="reset-the-ssh-configuration"></a>Återställ SSH-konfigurationen
Om du vill återställa SSH-konfigurationen väljer du `Reset configuration only` i avsnittet **läge** som i föregående skärm bild och väljer sedan **Uppdatera**. Försök att komma åt den virtuella datorn igen när åtgärden har slutförts.

### <a name="reset-ssh-credentials-for-a-user"></a>Återställa SSH-autentiseringsuppgifter för en användare
Om du vill återställa autentiseringsuppgifterna för en befintlig användare väljer du antingen `Reset SSH public key` eller `Reset password` i **läges** avsnittet som i föregående skärm bild. Ange användar namnet och en SSH-nyckel eller nytt lösen ord och välj sedan  **Uppdatera**.

Du kan också skapa en användare med behörigheten sudo på den virtuella datorn från den här menyn. Ange ett nytt användar namn och associerat lösen ord eller SSH-nyckel och välj sedan **Uppdatera**.

### <a name="check-security-rules"></a>Kontrol lera säkerhets regler

Använd [kontrol lera IP-flöde](../../network-watcher/diagnose-vm-network-traffic-filtering-problem.md) för att bekräfta om en regel i en nätverks säkerhets grupp blockerar trafik till eller från en virtuell dator. Du kan också granska gällande säkerhets grupps regler för att säkerställa att NSG-regeln för inkommande "Tillåt" finns och prioriteras för SSH-porten (standard 22). Mer information finns i [använda effektiva säkerhets regler för att felsöka trafik flöde för virtuella datorer](../../virtual-network/diagnose-network-traffic-filter-problem.md).

### <a name="check-routing"></a>Kontrol lera routning

Använd Network Watcher [nästa hopp](../../network-watcher/diagnose-vm-network-routing-problem.md) -funktion för att bekräfta att en väg inte hindrar trafik från att dirigeras till eller från en virtuell dator. Du kan också granska effektiva vägar för att se alla effektiva vägar för ett nätverks gränssnitt. Mer information finns i [använda effektiva vägar för att felsöka trafik flöde för virtuella datorer](../../virtual-network/diagnose-network-routing-problem.md).

## <a name="use-the-azure-vm-serial-console"></a>Använd Azures serie konsol för virtuell dator
Den [seriella Azure VM-konsolen](./serial-console-linux.md) ger till gång till en text baserad konsol för virtuella Linux-datorer. Du kan använda-konsolen för att felsöka SSH-anslutningen i ett interaktivt gränssnitt. Se till att du har uppfyllt [kraven](./serial-console-linux.md#prerequisites) för att använda en serie konsol och försök med kommandona nedan för att ytterligare felsöka ssh-anslutningen.

### <a name="check-that-ssh-is-running"></a>Kontrol lera att SSH körs
Du kan använda följande kommando för att kontrol lera om SSH körs på den virtuella datorn:

```console
ps -aux | grep ssh
```

Om det finns några utdata är SSH igång.

### <a name="check-which-port-ssh-is-running-on"></a>Kontrol lera vilken port SSH körs på

Du kan använda följande kommando för att kontrol lera vilken port SSH som körs på:

```console
sudo grep Port /etc/ssh/sshd_config
```

Dina utdata kommer att se ut ungefär så här:

```output
Port 22
```

## <a name="use-the-azure-cli"></a>Använda Azure CLI
Om du inte redan har gjort det installerar du den senaste [Azure CLI](/cli/azure/install-az-cli2) och loggar in på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

Om du har skapat och överfört en anpassad Linux-diskavbildning kontrollerar du att [Microsoft Azure Linux-agentens](../extensions/agent-linux.md) version 2.0.5 eller senare är installerad. För virtuella datorer som skapats med hjälp av Galleri avbildningar har det här åtkomst tillägget redan installerats och kon figurer ATS för dig.

### <a name="reset-ssh-configuration"></a>Återställ SSH-konfiguration
Du kan börja med att återställa SSH-konfigurationen till standardvärdena och starta om SSH-servern på den virtuella datorn. Detta ändrar inte användar kontots namn, lösen ord eller SSH-nycklar.
I följande exempel används [AZ VM User reset-SSH](/cli/azure/vm/user) för att återställa SSH-konfigurationen på den virtuella datorn med namnet `myVM` i `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
az vm user reset-ssh --resource-group myResourceGroup --name myVM
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställa SSH-autentiseringsuppgifter för en användare
I följande exempel används [AZ VM User Update](/cli/azure/vm/user) för att återställa autentiseringsuppgifterna för `myUsername` värdet som anges i `myPassword` , på den virtuella datorn med namnet `myVM` i `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
     --username myUsername --password myPassword
```

Om du använder SSH-autentiseringsnyckel kan du återställa SSH-nyckeln för en specifik användare. I följande exempel används **AZ VM Access set-Linux-User** för att uppdatera SSH-nyckeln som lagras i `~/.ssh/id_rsa.pub` för användaren som heter `myUsername` , på den virtuella datorn med namnet `myVM` i `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
az vm user update --resource-group myResourceGroup --name myVM \
    --username myUsername --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="use-the-vmaccess-extension"></a>Använd VMAccess-tillägget
Tillägget för VM-åtkomst för Linux läser i en JSON-fil som definierar åtgärder som ska utföras. Dessa åtgärder omfattar att återställa SSHD, återställa en SSH-nyckel eller lägga till en användare. Du använder fortfarande Azure CLI för att anropa VMAccess-tillägget, men du kan återanvända JSON-filerna över flera virtuella datorer om du vill. Med den här metoden kan du skapa en databas med JSON-filer som sedan kan anropas för vissa scenarier.

### <a name="reset-sshd"></a>Återställ SSHD
Skapa en fil med namnet `settings.json` med följande innehåll:

```json
{
    "reset_ssh":True
}
```

Med hjälp av Azure CLI anropar du `VMAccessForLinux` tillägget för att återställa sshd-anslutningen genom att ange din JSON-fil. I följande exempel används [AZ VM Extension](/cli/azure/vm/extension) för att återställa sshd på den virtuella datorn med namnet `myVM` i `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställa SSH-autentiseringsuppgifter för en användare
Om SSHD verkar fungera korrekt kan du återställa autentiseringsuppgifterna för en giver-användare. Om du vill återställa lösen ordet för en användare skapar du en fil med namnet `settings.json` . I följande exempel återställs autentiseringsuppgifterna för `myUsername` värdet som anges i `myPassword` . Ange följande rader i `settings.json` filen med dina egna värden:

```json
{
    "username":"myUsername", "password":"myPassword"
}
```

Eller för att återställa SSH-nyckeln för en användare måste du först skapa en fil med namnet `settings.json` . I följande exempel återställs autentiseringsuppgifterna för `myUsername` värdet som anges i `myPassword` på den virtuella datorn med namnet `myVM` i `myResourceGroup` . Ange följande rader i `settings.json` filen med dina egna värden:

```json
{
    "username":"myUsername", "ssh_key":"mySSHKey"
}
```

När du har skapat JSON-filen använder du Azure CLI för att anropa `VMAccessForLinux` tillägget för att återställa dina SSH-användarautentiseringsuppgifter genom att ange din JSON-fil. I följande exempel återställs autentiseringsuppgifterna på den virtuella datorn med namnet `myVM` i `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
az vm extension set --resource-group philmea --vm-name Ubuntu \
    --name VMAccessForLinux --publisher Microsoft.OSTCExtensions --version 1.2 --settings settings.json
```

## <a name="use-the-azure-classic-cli"></a>Använd den klassiska Azure-CLI
Om du inte redan har gjort [det installerar du Azures klassiska CLI och ansluter till din Azure-prenumeration](/cli/azure/install-classic-cli). Kontrol lera att du använder Resource Manager-läge enligt följande:

```azurecli
azure config mode arm
```

Om du har skapat och överfört en anpassad Linux-diskavbildning kontrollerar du att [Microsoft Azure Linux-agentens](../extensions/agent-linux.md) version 2.0.5 eller senare är installerad. För virtuella datorer som skapats med hjälp av Galleri avbildningar har det här åtkomst tillägget redan installerats och kon figurer ATS för dig.

### <a name="reset-ssh-configuration"></a>Återställ SSH-konfiguration
Själva SSHD-konfigurationen kan vara felkonfigurerad eller ett fel uppstod i tjänsten. Du kan återställa SSHD för att se till att SSH-konfigurationen är giltig. Att återställa SSHD bör vara det första fel söknings steget du tar.

I följande exempel återställs SSHD på en virtuell dator `myVM` som heter i resurs gruppen med namnet `myResourceGroup` . Använd dina egna VM-och resurs grupp namn enligt följande:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --reset-ssh
```

### <a name="reset-ssh-credentials-for-a-user"></a>Återställa SSH-autentiseringsuppgifter för en användare
Om SSHD verkar fungera korrekt kan du återställa lösen ordet för en giver-användare. I följande exempel återställs autentiseringsuppgifterna för `myUsername` värdet som anges i `myPassword` på den virtuella datorn med namnet `myVM` i `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
     --user-name myUsername --password myPassword
```

Om du använder SSH-autentiseringsnyckel kan du återställa SSH-nyckeln för en specifik användare. I följande exempel uppdateras SSH-nyckeln som lagras i `~/.ssh/id_rsa.pub` för den användare som heter `myUsername` , på den virtuella datorn med namnet `myVM` i `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
azure vm reset-access --resource-group myResourceGroup --name myVM \
    --user-name myUsername --ssh-key-file ~/.ssh/id_rsa.pub
```

## <a name="restart-a-vm"></a>Starta om en virtuell dator
Om du har återställt SSH-konfigurationen och användarautentiseringsuppgifter, eller påträffat ett fel i detta, kan du prova att starta om den virtuella datorn för att åtgärda underliggande beräknings problem.

### <a name="azure-portal"></a>Azure Portal
Om du vill starta om en virtuell dator med Azure Portal väljer du den virtuella datorn och väljer sedan **starta om** som i följande exempel:

![Starta om en virtuell dator i Azure Portal](./media/troubleshoot-ssh-connection/restart-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
I följande exempel används [AZ VM restart](/cli/azure/vm) för att starta om den virtuella datorn som heter `myVM` i resurs gruppen med namnet `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
az vm restart --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

I följande exempel startas den virtuella datorn om med namnet `myVM` i resurs gruppen med namnet `myResourceGroup` . Använd dina egna värden på följande sätt:

```console
azure vm restart --resource-group myResourceGroup --name myVM
```

## <a name="redeploy-a-vm"></a>Distribuera om en VM
Du kan distribuera om en virtuell dator till en annan nod i Azure, vilket kan åtgärda eventuella underliggande nätverks problem. Information om hur du distribuerar om en virtuell dator finns i [distribuera om virtuell dator till en ny Azure-nod](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

> [!NOTE]
> När den här åtgärden har slutförts försvinner tillfälliga disk data och dynamiska IP-adresser som är associerade med den virtuella datorn uppdateras.
>
>

### <a name="azure-portal"></a>Azure Portal
Om du vill distribuera om en virtuell dator med hjälp av Azure Portal väljer du den virtuella datorn och bläddrar ned till avsnittet **support och fel sökning** . Välj **distribuera** igen som i följande exempel:

![Distribuera om en virtuell dator i Azure Portal](./media/troubleshoot-ssh-connection/redeploy-vm-using-portal.png)

### <a name="azure-cli"></a>Azure CLI
I följande exempel används [AZ VM redeploy](/cli/azure/vm) för att distribuera om den virtuella datorn som heter `myVM` i resurs gruppen med namnet `myResourceGroup` . Använd dina egna värden på följande sätt:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM
```

### <a name="azure-classic-cli"></a>Klassisk Azure CLI

Följande exempel distribuerar om den virtuella datorn som heter `myVM` i resurs gruppen med namnet `myResourceGroup` . Använd dina egna värden på följande sätt:

```console
azure vm redeploy --resource-group myResourceGroup --name myVM
```

## <a name="vms-created-by-using-the-classic-deployment-model"></a>Virtuella datorer som skapats med hjälp av den klassiska distributions modellen

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Testa de här stegen för att lösa de vanligaste SSH-anslutningsfel för virtuella datorer som har skapats med hjälp av den klassiska distributions modellen. Försök att ansluta till den virtuella datorn efter varje steg.

* Återställ fjärråtkomst från [Azure Portal](https://portal.azure.com). Välj den virtuella datorn på Azure Portal och välj sedan **Återställ fjärran sluten...**.
* Starta om den virtuella datorn. Välj den virtuella datorn på [Azure Portal](https://portal.azure.com)och välj **starta om**.

* Distribuera om den virtuella datorn till en ny Azure-nod. Information om hur du distribuerar om en virtuell dator finns i [distribuera om virtuell dator till en ny Azure-nod](./redeploy-to-new-node-windows.md?toc=/azure/virtual-machines/windows/toc.json).

    När den här åtgärden har slutförts kommer tillfälliga disk data att gå förlorade och dynamiska IP-adresser som är associerade med den virtuella datorn uppdateras.
* Följ anvisningarna i [så här återställer du ett lösen ord eller SSH för Linux-baserade virtuella datorer](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic) till:

  * Återställ lösen ordet eller SSH-nyckeln.
  * Skapa ett användar konto för *sudo* .
  * Återställ SSH-konfigurationen.
* Kontrol lera den virtuella datorns resurs hälsa för eventuella plattforms problem.<br>
     Välj din virtuella dator och rulla ned **Inställningar**  >  **kontrol lera hälsa**.

## <a name="additional-resources"></a>Ytterligare resurser
* Om du fortfarande inte kan använda SSH till den virtuella datorn efter att ha granskat stegen efter, kan du läsa [mer i detaljerade fel söknings steg](detailed-troubleshoot-ssh-connection.md) för att granska ytterligare steg för att lösa problemet.
* Mer information om hur du felsöker program åtkomst finns i [Felsöka åtkomst till ett program som körs på en virtuell Azure-dator](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json)
* Mer information om fel sökning av virtuella datorer som har skapats med hjälp av den klassiska distributions modellen finns i [så här återställer du ett lösen ord eller SSH för Linux-baserade virtuella datorer](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic).