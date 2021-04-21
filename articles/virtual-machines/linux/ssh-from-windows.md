---
title: Använda SSH-nycklar för att ansluta till virtuella Linux-datorer
description: Lär dig hur du genererar och använder SSH-nycklar från en Windows-dator för att ansluta till en virtuell Linux-dator i Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.date: 10/31/2020
ms.topic: how-to
ms.author: cynthn
ms.openlocfilehash: 0199a47b2306d7d461ba61057c7ab1015015df08
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107835568"
---
# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Så här använder du SSH-nycklar med Windows på Azure

Den här artikeln är för [](#create-an-ssh-key-pair) Windows-användare som vill skapa [](#connect-to-your-vm) och använda SSH-nycklar *(Secure Shell)* för att ansluta till virtuella Linux-datorer (VM) i Azure. Du kan också [generera och lagra SSH-nycklar i Azure Portal](../ssh-keys-portal.md) som ska användas när du skapar virtuella datorer i portalen.


Om du vill använda SSH-nycklar från en Linux- eller macOS-klient kan du gå [till snabbstegen](mac-create-ssh-keys.md). En mer detaljerad översikt över SSH finns i Detaljerade steg: Skapa och hantera SSH-nycklar för autentisering till en [virtuell Linux-dator i Azure](create-ssh-keys-detailed.md).

## <a name="overview-of-ssh-and-keys"></a>Översikt över SSH och nycklar

[SSH](https://www.ssh.com/ssh/) är ett krypterat anslutningsprotokoll som tillåter säkra inloggningar över oskyddade anslutningar. SSH är standardanslutningsprotokollet för virtuella Linux-datorer i Azure. Även om SSH i sig tillhandahåller en krypterad anslutning, innebär användningen av lösenord med SSH fortfarande att den virtuella datorn är sårbar för råstyrkattacker. Vi rekommenderar att du ansluter till en virtuell dator via SSH med hjälp av ett offentligt/privat nyckelpar, även kallat *SSH-nycklar.* 

Det offentliga/privata nyckelparet är som låset på din ytterdörr. Låset exponeras för **allmänheten**. Vem som helst med rätt nyckel kan öppna dörren. Nyckeln är privat **och** ges endast till personer som du litar på eftersom den kan användas för att låsa upp dörren. 

- Den *offentliga nyckeln placeras* på den virtuella Linux-datorn när du skapar den virtuella datorn. 

- Den *privata nyckeln* finns kvar i det lokala systemet. Skydda den privata nyckeln. Dela den inte med andra.

När du ansluter till din virtuella Linux-dator testar den virtuella datorn SSH-klienten för att se till att den har rätt privat nyckel. Om klienten har den privata nyckeln beviljas den åtkomst till den virtuella datorn. 

Beroende på organisationens säkerhetsprinciper kan du återanvända ett enskilt nyckelpar för att få åtkomst till flera virtuella Azure-datorer och -tjänster. Du behöver inte ett separat nyckelpar för varje virtuell dator. 

Din offentliga nyckel kan delas med vem som helst, men bara du (eller din lokala säkerhetsinfrastruktur) ska ha åtkomst till din privata nyckel.

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="ssh-clients"></a>SSH-klienter

De senaste Windows 10 inkluderar [OpenSSH-klientkommandon](https://blogs.msdn.microsoft.com/commandline/2018/03/07/windows10v1803/) för att skapa och använda SSH-nycklar och göra SSH-anslutningar från PowerShell eller en kommandotolk. Det här är det enklaste sättet att skapa en SSH-anslutning till din virtuella Linux-dator från en Windows-dator. 

Du kan också använda Bash i Azure Cloud Shell [för](../../cloud-shell/overview.md) att ansluta till den virtuella datorn. Du kan använda Cloud Shell i en webbläsare [,](https://shell.azure.com/bash) [från Azure Portal](https://portal.azure.com)eller som en terminal i Visual Studio Code med hjälp av [Azure-kontotillägget](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

Du kan också installera Windows-undersystem för Linux [ansluta](/windows/wsl/about) till den virtuella datorn via SSH och använda andra inbyggda Linux-verktyg i ett Bash-gränssnitt.

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Skapa ett SSH-nyckelpar med `ssh-keygen` kommandot . Ange ett filnamn eller använd standardvärdet som visas inom parentes (till exempel `C:\Users\username/.ssh/id_rsa` ).  Ange en lösenfras för filen eller lämna lösenfrasen tom om du inte vill använda en lösenfras. 

```powershell
ssh-keygen -m PEM -t rsa -b 4096
```

## <a name="create-a-vm-using-your-key"></a>Skapa en virtuell dator med hjälp av din nyckel

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering anger du din offentliga SSH-nyckel när du skapar den virtuella datorn.

Med Hjälp av Azure CLI anger du sökvägen och filnamnet för den offentliga nyckeln med `az vm create` parametern och `--ssh-key-value` .

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVM \
   --image UbuntuLTS\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

Använd och lägg till `New-AzVM` SSH-nyckeln i VM-konfigurationen med Hjälp av med PowerShell. Ett exempel finns i [Snabbstart: Skapa en virtuell Linux-dator i Azure med PowerShell](quick-create-powershell.md).

Om du gör många distributioner med hjälp av portalen kanske du vill ladda upp din offentliga nyckel till Azure, där den enkelt kan väljas när du skapar en virtuell dator från portalen. Mer information finns i Ladda [upp en SSH-nyckel.](../ssh-keys-portal.md#upload-an-ssh-key)


## <a name="connect-to-your-vm"></a>Ansluta till din virtuella dator

Med den offentliga nyckeln distribuerad på den virtuella Azure-datorn och den privata nyckeln i ditt lokala system, SSH till den virtuella datorn med hjälp av IP-adressen eller DNS-namnet för den virtuella datorn. Ersätt *azureuser* och *10.111.12.123* i följande kommando med administratörens användarnamn, IP-adressen (eller det fullständiga domännamnet) och sökvägen till din privata nyckel:

```bash
ssh -i ~/.ssh/id_rsa.pub azureuser@10.111.12.123
```

Om du konfigurerade en lösenfras när du skapade nyckelparet anger du lösenfrasen när du tillfrågas.

Om den virtuella datorn använder just-in-time-åtkomstprincipen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med just-in-time-principen](../../security-center/security-center-just-in-time.md).


## <a name="next-steps"></a>Nästa steg

- Information om SSH-nycklar i Azure Portal finns i Generera och lagra [SSH-nycklar](../ssh-keys-portal.md) i Azure Portal som ska användas när du skapar virtuella datorer i portalen.

- Detaljerade steg, alternativ och avancerade exempel på hur du arbetar med SSH-nycklar finns i Detaljerade steg [för att skapa SSH-nyckelpar.](create-ssh-keys-detailed.md)

- Du kan också använda PowerShell i Azure Cloud Shell för att generera SSH-nycklar och göra SSH-anslutningar till virtuella Linux-datorer. Se [PowerShell-snabbstarten.](../../cloud-shell/quickstart-powershell.md#ssh)

- Om du har problem med att använda SSH för att ansluta till dina virtuella Linux-datorer kan du gå [till Felsöka SSH-anslutningar till en virtuell Azure Linux-dator.](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
