---
title: Använda SSH-nycklar för att ansluta till virtuella Linux-datorer
description: Lär dig hur du skapar och använder SSH-nycklar från en Windows-dator för att ansluta till en virtuell Linux-dator på Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 167ce63931155f5142ed34b41f857505699bc0a6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102552787"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Använda SSH-nycklar med Windows på Azure

Den här artikeln är för Windows-användare som vill [skapa](#create-an-ssh-key-pair) och använda SSH-nycklar ( *Secure Shell* ) för att [ansluta](#connect-to-your-vm) till virtuella Linux-datorer (VM: ar) i Azure. Du kan också [skapa och lagra SSH-nycklar i Azure Portal](../ssh-keys-portal.md) som ska användas när du skapar virtuella datorer i portalen.


Information om hur du använder SSH-nycklar från en Linux-eller macOS-klient finns i [snabb stegen](mac-create-ssh-keys.md). En mer detaljerad översikt över SSH finns i [detaljerade steg: skapa och hantera SSH-nycklar för autentisering till en virtuell Linux-dator i Azure](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar

[SSH](https://www.ssh.com/ssh/) är ett krypterat anslutnings protokoll som tillåter säkra inloggningar via oskyddade anslutningar. SSH är standard anslutnings protokollet för virtuella Linux-datorer som finns i Azure. Även om SSH tillhandahåller en krypterad anslutning, lämnar lösen ord med SSH fortfarande den virtuella datorn som är sårbar för angrepp med brute force. Vi rekommenderar att du ansluter till en virtuell dator via SSH med ett offentligt privat nyckel par, även kallat *SSH-nycklar*. 

Det offentliga privata nyckel paret liknar låset på din front dörr. Låset exponeras för **allmänheten**, vem som helst med rätt nyckel kan öppna dörren. Nyckeln är **privat** och endast för personer som du litar på eftersom den kan användas för att låsa upp dörren. 

- Den *offentliga nyckeln* placeras på din virtuella Linux-dator när du skapar den virtuella datorn. 

- Den *privata nyckeln* finns kvar i det lokala systemet. Skydda den privata nyckeln. Dela den inte med andra.

När du ansluter till din virtuella Linux-dator testar den virtuella datorn SSH-klienten för att kontrol lera att den har rätt privat nyckel. Om klienten har den privata nyckeln beviljas den åtkomst till den virtuella datorn. 

Beroende på din organisations säkerhets principer kan du återanvända ett enda nyckel par för att få åtkomst till flera virtuella Azure-datorer och-tjänster. Du behöver inte ett separat nyckel par för varje virtuell dator. 

Din offentliga nyckel kan delas med vem som helst, men bara du (eller din lokala säkerhets infrastruktur) bör ha åtkomst till din privata nyckel.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH-klienter

De senaste versionerna av Windows 10 innehåller [openssh-klient kommandon](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) för att skapa och använda SSH-nycklar och göra SSH-anslutningar från PowerShell eller en kommando tolk. Detta är det enklaste sättet att skapa en SSH-anslutning till din virtuella Linux-dator från en Windows-dator. 

Du kan också använda bash i [Azure Cloud Shell](../../cloud-shell/overview.md) för att ansluta till din virtuella dator. Du kan använda Cloud Shell i en [webbläsare](https://shell.azure.com/bash), från [Azure Portal](https://portal.azure.com)eller som en Terminal i Visual Studio Code med hjälp av [tillägget Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

Du kan också installera [Windows-undersystemet för Linux](/windows/wsl/about) för att ansluta till din virtuella dator via SSH och använda andra inbyggda Linux-verktyg i ett bash-gränssnitt.

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Skapa ett SSH-nyckelpar med `ssh-keygen` kommandot. Ange ett fil namn eller Använd standardvärdet som visas i parentes (till exempel `C:\Users\username/.ssh/id_rsa` ).  Ange en lösen fras för filen eller lämna lösen frasen tom om du inte vill använda en lösen fras. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Skapa en virtuell dator med din nyckel

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering, anger du din offentliga SSH-nyckel när du skapar den virtuella datorn.

Med hjälp av Azure CLI anger du sökvägen och fil namnet för den offentliga nyckeln med hjälp av `az vm create` och `--ssh-key-value` parametern.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Med PowerShell använder `New-AzVM` du och lägger till SSH-nyckeln i VM-konfigurationen med hjälp av. Ett exempel finns i [snabb start: skapa en virtuell Linux-dator i Azure med PowerShell](quick-create-powershell.md).

Om du gör många distributioner med hjälp av portalen kanske du vill överföra den offentliga nyckeln till Azure, där den enkelt kan väljas när du skapar en virtuell dator från portalen. Mer information finns i [Ladda upp en SSH-nyckel](../ssh-keys-portal.md#upload-an-ssh-key).


## <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Med den offentliga nyckeln distribuerad på din virtuella Azure-dator och den privata nyckeln i det lokala systemet, med hjälp av den virtuella datorns IP-adress eller DNS-namn. Ersätt *azureuser* och *10.111.12.123* i följande kommando med administratörs användar namnet, IP-adressen (eller det fullständigt kvalificerade domän namnet) och sökvägen till din privata nyckel:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Om du har konfigurerat en lösen fras när du skapade nyckel paret anger du lösen frasen när du uppmanas till det.

Om den virtuella datorn använder just-in-Time-åtkomst-principen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med just-in-time-principen](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Nästa steg

- Information om SSH-nycklar i Azure Portal finns i [skapa och lagra SSH-nycklar i Azure Portal](../ssh-keys-portal.md) som ska användas när du skapar virtuella datorer i portalen.

- Detaljerade steg, alternativ och avancerade exempel på hur du arbetar med SSH-nycklar finns i [detaljerade steg för att skapa SSH-nyckelpar](create-ssh-keys-detailed.md).

- Du kan också använda PowerShell i Azure Cloud Shell för att generera SSH-nycklar och göra SSH-anslutningar till virtuella Linux-datorer. Se [snabb start för PowerShell](../../cloud-shell/quickstart-powershell.md#ssh).

- Om du har problem med att använda SSH för att ansluta till dina virtuella Linux-datorer kan du läsa [FELSÖK SSH-anslutningar till en virtuell Azure Linux-dator](../troubleshooting/troubleshoot-ssh-connection.md?toc=/azure/virtual-machines/linux/toc.json).
