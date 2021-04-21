---
title: Skapa och använda ett SSH-nyckelpar för virtuella Linux-datorer i Azure
description: Hur du skapar och använder ett offentligt/privat SSH-nyckelpar för virtuella Linux-datorer i Azure för att förbättra säkerheten för autentiseringsprocessen.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c5e683e1f5af42a69fac45c20f52169834967649
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788141"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snabbsteg: Skapa och använda ett offentligt/privat SSH-nyckelpar för virtuella Linux-datorer i Azure

Med ett SSH-nyckelpar (Secure Shell) kan du skapa virtuella datorer (VM) i Azure som använder SSH-nycklar för autentisering. Den här artikeln visar hur du snabbt genererar och använder ett offentligt/privat SSH-nyckelfilpar för virtuella Linux-datorer. Du kan utföra de här stegen med Azure Cloud Shell, en macOS- eller Linux-värd. 

> [!NOTE]
> Virtuella datorer som skapas med SSH-nycklar konfigureras som standard med lösenord inaktiverade, vilket avsevärt ökar svårare att gissa nyckelsökningsattacker. 

Mer bakgrundsinformation och exempel finns i [Detaljerade steg för att skapa SSH-nyckelpar.](create-ssh-keys-detailed.md)

Fler sätt att generera och använda SSH-nycklar på en Windows-dator finns i Så här [använder du SSH-nycklar med Windows på Azure.](ssh-from-windows.md)

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Använd kommandot `ssh-keygen` för att generera offentliga och privata SSH-nyckelfiler. Som standard skapas dessa filer i katalogen ~/.ssh. Du kan ange en annan plats och ett valfritt lösenord *(lösenfras) för* att få åtkomst till den privata nyckelfilen. Om det finns ett SSH-nyckelpar med samma namn på den angivna platsen skrivs dessa filer över.

Följande kommando skapar ett SSH-nyckelpar med RSA-kryptering och en bitlängd på 4 096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Om du använder [Azure CLI för](/cli/azure) att skapa den virtuella datorn med kommandot az vm [create](/cli/azure/vm#az_vm_create) kan du även generera offentliga och privata SSH-nyckelfiler med hjälp av `--generate-ssh-keys` alternativet . Nyckelfilerna lagras i katalogen ~/.ssh om inget annat anges med `--ssh-dest-key-path` alternativet . Om det redan finns ett ssh-nyckelpar och alternativet används genereras inte ett nytt nyckelpar, utan i stället används  `--generate-ssh-keys` det befintliga nyckelparet. I följande kommando ersätter du *VMname* och *RGname* med dina egna värden:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Ange en offentlig SSH-nyckel när du distribuerar en virtuell dator

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering anger du din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure Portal, Azure CLI, Azure Resource Manager-mallar eller andra metoder:

* [Skapa en virtuell Linux-dator med Azure-portalen](quick-create-portal.md)
* [Skapa en virtuell Linux-dator med Azure CLI](quick-create-cli.md)
* [Skapa en virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md)

Om du inte är bekant med formatet för en offentlig SSH-nyckel kan du visa den offentliga nyckeln med följande kommando och ersätta med sökvägen och filnamnet för din egen offentliga nyckelfil om det `cat` `~/.ssh/id_rsa.pub` behövs:

```bash
cat ~/.ssh/id_rsa.pub
```

Ett typiskt värde för offentlig nyckel ser ut som i det här exemplet:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Om du kopierar och klistrar in innehållet i den offentliga nyckelfilen som ska användas i Azure Portal- eller Resource Manager-mallen ska du se till att du inte kopierar några avslutande blanksteg. Om du vill kopiera en offentlig nyckel i macOS kan du skicka filen med den offentliga nyckeln till `pbcopy` . På samma sätt kan du i Linux skicka filen med den offentliga nyckeln till program som `xclip` .

Den offentliga nyckeln som du placerar på den virtuella Linux-datorn i Azure lagras som standard i ~/.ssh/id_rsa.pub, såvida du inte angav en annan plats när du skapade nyckelparet. Om du vill använda [Azure CLI 2.0](/cli/azure) för att skapa den virtuella datorn med en befintlig offentlig nyckel anger du värdet och eventuellt platsen för den offentliga nyckeln med kommandot [az vm create](/cli/azure/vm#az_vm_create) med alternativet `--ssh-key-values` . I följande kommando ersätter *du myVM*, *myResourceGroup*, *UbuntuLTS,* *azureuser* och *mysshkey.pub* med dina egna värden:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Om du vill använda flera SSH-nycklar med den virtuella datorn kan du ange dem i en blankstegsavgränsad lista, så `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` här.


## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Med den offentliga nyckeln distribuerad på den virtuella Azure-datorn och den privata nyckeln i ditt lokala system, SSH till den virtuella datorn med hjälp av IP-adressen eller DNS-namnet för den virtuella datorn. I följande kommando ersätter *du azureuser* *och myvm.westus.cloudapp.azure.com* med administratörsanvändarnamnet och det fullständigt kvalificerade domännamnet (eller IP-adressen):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du angav en lösenfras när du skapade nyckelparet anger du lösenfrasen när du uppmanas att göra det under inloggningsprocessen. Den virtuella datorn läggs till i filen ~/.ssh/known_hosts och du uppmanas inte att ansluta igen förrän antingen den offentliga nyckeln på den virtuella Azure-datorn ändras eller om servernamnet tas bort från ~/.ssh/known_hosts.

Om den virtuella datorn använder just-in-time-åtkomstprincipen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med just-in-time-principen](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med SSH-nyckelpar finns [i Detaljerade steg för att skapa och hantera SSH-nyckelpar.](create-ssh-keys-detailed.md)

* Om du har problem med SSH-anslutningar till virtuella Azure-datorer kan du gå till [Felsöka SSH-anslutningar till en virtuell Azure Linux-dator.](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection)