---
title: Återställ åtkomst till en virtuell Azure Linux-dator
description: Hantera administrativa användare och återställning av åtkomst på virtuella Linux-datorer med VMAccess-tillägget och Azure CLI
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 05/10/2018
ms.openlocfilehash: e0e63762fc151d93cb34885c04dd4d03285c29f1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557054"
---
# <a name="manage-administrative-users-ssh-and-check-or-repair-disks-on-linux-vms-using-the-vmaccess-extension-with-the-azure-cli"></a>Hantera administrativa användare, SSH och kontrol lera eller reparera diskar på virtuella Linux-datorer med VMAccess-tillägget med Azure CLI
## <a name="overview"></a>Översikt
Disken på den virtuella Linux-datorn visar fel. Du återställer inte rot lösen ordet för din virtuella Linux-dator eller oavsiktligt tagit bort den privata SSH-nyckeln. Om detta hände i dagar för data centret måste du köra det och sedan öppna KVM för att hämta i Server konsolen. Tänk på Azure VMAccess-tillägget som en KVM-växel som gör att du kan komma åt konsolen för att återställa åtkomsten till Linux eller utföra underhåll på disk nivå.

Den här artikeln visar hur du använder Azure VMAccess-tillägget för att kontrol lera eller reparera en disk, återställa användar åtkomst, hantera administrativa användar konton eller uppdatera SSH-konfigurationen på Linux när den körs som Azure Resource Manager virtuella datorer. Om du behöver hantera klassiska virtuella datorer kan du följa anvisningarna i den [klassiska VM-dokumentationen](/previous-versions/azure/virtual-machines/linux/classic/reset-access-classic). 
 
> [!NOTE]
> Om du använder VMAccess-tillägget för att återställa lösen ordet för den virtuella datorn efter att du har installerat inloggnings tillägget AAD måste du köra inloggnings tillägget AAD igen för att återaktivera AAD-inloggningen för datorn.

## <a name="prerequisites"></a>Förutsättningar
### <a name="operating-system"></a>Operativsystem

Tillägget för VM-åtkomst kan köras mot dessa Linux-distributioner:

| Distribution | Version |
|---|---|
| Ubuntu | 16,04 LTS, 14,04 LTS och 12,04 LTS |
| Debian | Debian 7.9 +, 8.2 + |
| Red Hat | RHEL 6,7 +, 7.1 + |
| Oracle Linux | 6.4 +, 7.0 + |
| SUSE | 11 och 12 |
| OpenSuse | openSUSE skottår 42.2 + |
| CentOS | CentOS 6.3 +, 7.0 + |
| CoreOS | 494.4.0 + |

## <a name="ways-to-use-the-vmaccess-extension"></a>Sätt att använda VMAccess-tillägget
Du kan använda VMAccess-tillägget på virtuella Linux-datorer på två sätt:

* Använd Azure CLI och de obligatoriska parametrarna.
* [Använd RAW JSON-filer som VMAccess-tillägget](#use-json-files-and-the-vmaccess-extension) och sedan agera på.

I följande exempel används [AZ VM User](/cli/azure/vm/user) -kommandon. För att utföra de här stegen måste du ha den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

## <a name="update-ssh-key"></a>Uppdatera SSH-nyckel
I följande exempel uppdateras SSH-nyckeln för användaren `azureuser` på den virtuella datorn med namnet `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

> **Obs:** `az vm user update` Kommandot lägger till den nya offentliga nyckel texten i `~/.ssh/authorized_keys` filen för administratörs användaren på den virtuella datorn. Detta ersätter eller tar inte bort några befintliga SSH-nycklar. Detta tar inte bort tidigare nycklar som ställts in vid distributions tillfället eller efterföljande uppdateringar via VMAccess-tillägget.

## <a name="reset-password"></a>Återställa lösenord
I följande exempel återställer lösen ordet för användaren `azureuser` på den virtuella datorn med namnet `myVM` :

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username azureuser \
  --password myNewPassword
```

## <a name="restart-ssh"></a>Starta om SSH
I följande exempel startar SSH-daemon om och återställer SSH-konfigurationen till standardvärdena på en virtuell dator med namnet `myVM` :

```azurecli-interactive
az vm user reset-ssh \
  --resource-group myResourceGroup \
  --name myVM
```

## <a name="create-an-administrativesudo-user"></a>Skapa en administrativ/sudo-användare
I följande exempel skapas en användare med `myNewUser` namnet **sudo** -behörigheter. Kontot använder en SSH-nyckel för autentisering på den virtuella datorn med namnet `myVM` . Den här metoden är utformad för att hjälpa dig att få åtkomst till en virtuell dator i händelse av att aktuella autentiseringsuppgifter går förlorade eller glöms bort. Som bästa praxis bör konton med **sudo** -behörigheter begränsas.

```azurecli-interactive
az vm user update \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser \
  --ssh-key-value ~/.ssh/id_rsa.pub
```

## <a name="delete-a-user"></a>Ta bort en användare
I följande exempel tar bort en användare `myNewUser` som heter på den virtuella datorn med namnet `myVM` :

```azurecli-interactive
az vm user delete \
  --resource-group myResourceGroup \
  --name myVM \
  --username myNewUser
```

## <a name="use-json-files-and-the-vmaccess-extension"></a>Använda JSON-filer och VMAccess-tillägget
I följande exempel används RAW JSON-filer. Använd [AZ VM Extension](/cli/azure/vm/extension) för att anropa dina JSON-filer. Dessa JSON-filer kan även anropas från Azure-mallar. 

### <a name="reset-user-access"></a>Återställ användar åtkomst
Om du har förlorat åtkomsten till roten på din virtuella Linux-dator kan du starta ett VMAccess-skript för att uppdatera en användares SSH-nyckel eller lösen ord.

Om du vill uppdatera den offentliga SSH-nyckeln för en användare skapar du en fil med namnet `update_ssh_key.json` och lägger till inställningar i följande format. Ersätt dina egna värden för `username` parametrarna och `ssh_key` :

```json
{
  "username":"azureuser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNGxxxxxx2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7xxxxxxwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5wxxtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== azureuser@myVM"
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings update_ssh_key.json
```

Om du vill återställa ett användar lösen ord skapar du en fil med namnet `reset_user_password.json` och lägger till inställningar i följande format. Ersätt dina egna värden för `username` parametrarna och `password` :

```json
{
  "username":"azureuser",
  "password":"myNewPassword" 
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_user_password.json
```

### <a name="restart-ssh"></a>Starta om SSH
Om du vill starta om SSH-daemonen och återställa SSH-konfigurationen till standardvärdena skapar du en fil med namnet `reset_sshd.json` . Lägg till följande innehåll:

```json
{
  "reset_ssh": true
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings reset_sshd.json
```

### <a name="manage-administrative-users"></a>Hantera administrativa användare

Skapa en användare med **sudo** -behörigheter som använder en SSH-nyckel för autentisering genom att skapa en fil med namnet `create_new_user.json` och lägga till inställningar i följande format. Ersätt dina egna värden för `username` parametrarna och `ssh_key` . Den här metoden är utformad för att hjälpa dig att få åtkomst till en virtuell dator i händelse av att aktuella autentiseringsuppgifter går förlorade eller glöms bort. Som bästa praxis bör konton med **sudo** -behörigheter begränsas.

```json
{
  "username":"myNewUser",
  "ssh_key":"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCZ3S7gGp3rcbKmG2Y4vGZFMuMZCwoUzZNG1vHY7P2XV2x9FfAhy8iGD+lF8UdjFX3t5ebMm6BnnMh8fHwkTRdOt3LDQq8o8ElTBrZaKPxZN2thMZnODs5Hlemb2UX0oRIGRcvWqsd4oJmxsXa/Si98Wa6RHWbc9QZhw80KAcOVhmndZAZAGR+Wq6yslNo5TMOr1/ZyQAook5C4FtcSGn3Y+WczaoGWIxG4ZaWk128g79VIeJcIQqOjPodHvQAhll7qDlItVvBfMOben3GyhYTm7k4YwlEdkONm4yV/UIW0la1rmyztSBQIm9sZmSq44XXgjVmDHNF8UfCZ1ToE4r2SdwTmZv00T2i5faeYnHzxiLPA3Enub7iUo5IdwFArnqad7MO1SY1kLemhX9eFjLWN4mJe56Fu4NiWJkR9APSZQrYeKaqru4KUC68QpVasNJHbuxPSf/PcjF3cjO1+X+4x6L1H5HTPuqUkyZGgDO4ynUHbko4dhlanALcriF7tIfQR9i2r2xOyv5gxJEW/zztGqWma/d4rBoPjnf6tO7rLFHXMt/DVTkAfn5woYtLDwkn5FMyvThRmex3BDf0gujoI1y6cOWLe9Y5geNX0oj+MXg/W0cXAtzSFocstV1PoVqy883hNoeQZ3mIGB3Q0rIUm5d9MA2bMMt31m1g3Sin6EQ== myNewUser@myVM",
  "password":"myNewUserPassword"
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings create_new_user.json
```

Om du vill ta bort en användare skapar du en fil med namnet `delete_user.json` och lägger till följande innehåll. Ersätt ditt eget värde för `remove_user` parametern:

```json
{
  "remove_user":"myNewUser"
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings delete_user.json
```

### <a name="check-or-repair-the-disk"></a>Kontrol lera eller reparera disken
Med VMAccess kan du också kontrol lera och reparera en disk som du har lagt till i den virtuella Linux-datorn.

Om du vill kontrol lera och reparera disken skapar du en fil med namnet `disk_check_repair.json` och lägger till inställningar i följande format. Ersätt ditt eget värde med namnet `repair_disk` :

```json
{
  "check_disk": "true",
  "repair_disk": "true, mydiskname"
}
```

Kör VMAccess-skriptet med:

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name VMAccessForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 1.4 \
  --protected-settings disk_check_repair.json
```
## <a name="troubleshoot-and-support"></a>Felsöka och support

### <a name="troubleshoot"></a>Felsöka

Data om tillstånd för tilläggs distributioner kan hämtas från Azure Portal och med hjälp av Azure CLI. Om du vill se distributions statusen för tillägg för en virtuell dator kör du följande kommando med hjälp av Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

### <a name="support"></a>Support

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta Azure-experterna i [MSDN Azure och Stack Overflow forum](https://azure.microsoft.com/support/forums/). Du kan också skriva en support incident för Azure. Gå till [Support webbplatsen för Azure](https://azure.microsoft.com/support/options/) och välj få support. Information om hur du använder Azure-support finns i [vanliga frågor och svar om Microsoft Azure support](https://azure.microsoft.com/support/faq/).
