---
title: Skapa och Använd ett SSH-nyckelpar för virtuella Linux-datorer i Azure
description: Hur du skapar och använder ett offentligt privat privat nyckel par för virtuella Linux-datorer i Azure för att förbättra säkerheten för autentiseringsprocessen.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 12/06/2019
ms.author: cynthn
ms.openlocfilehash: c77375782ba23114be1953d9f8ad7de31ab06f1f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582195"
---
# <a name="quick-steps-create-and-use-an-ssh-public-private-key-pair-for-linux-vms-in-azure"></a>Snabb steg: skapa och använda ett offentligt privat privat nyckel par för virtuella Linux-datorer i Azure

Med ett SSH-nyckelpar (Secure Shell) kan du skapa virtuella datorer i Azure som använder SSH-nycklar för autentisering. Den här artikeln visar hur du snabbt skapar och använder ett fil par för offentliga SSH-privata nycklar för virtuella Linux-datorer. Du kan utföra de här stegen med Azure Cloud Shell, en macOS-eller Linux-värd. 

> [!NOTE]
> Virtuella datorer som skapats med SSH-nycklar konfigureras som standard med inaktiverade lösen ord, vilket avsevärt ökar svårigheten att knäcka gissnings attacker. 

Mer bakgrund och exempel finns i [detaljerade steg för att skapa SSH-nyckelpar](create-ssh-keys-detailed.md).

Ytterligare sätt att skapa och använda SSH-nycklar på en Windows-dator finns i [så här använder du SSH-nycklar med Windows på Azure](ssh-from-windows.md).

[!INCLUDE [virtual-machines-common-ssh-support](../../../includes/virtual-machines-common-ssh-support.md)]

## <a name="create-an-ssh-key-pair"></a>Skapa ett SSH-nyckelpar

Använd `ssh-keygen` kommandot för att generera offentliga och privata SSH-nyckelfiler. Som standard skapas de här filerna i katalogen ~/.ssh. Du kan ange en annan plats och ett valfritt lösen ord (lösen *fras*) för att få åtkomst till den privata nyckel filen. Om det finns ett SSH-nyckelpar med samma namn på den aktuella platsen, skrivs filerna över.

Följande kommando skapar ett SSH-nyckelpar med RSA-kryptering och en bit-längd på 4096:

```bash
ssh-keygen -m PEM -t rsa -b 4096
```

Om du använder [Azure CLI](/cli/azure) för att skapa en virtuell dator med kommandot [AZ VM Create](/cli/azure/vm#az-vm-create) kan du välja att generera offentliga och privata nyckel filer för SSH med `--generate-ssh-keys` alternativet. De viktigaste filerna lagras i katalogen ~/.ssh, om inget annat anges med `--ssh-dest-key-path` alternativet. Om det redan finns ett SSH-nyckelpar och  `--generate-ssh-keys` alternativet används, genereras inte ett nytt nyckel par, utan i stället används det befintliga nyckel paret. I följande kommando ersätter du *VMName* och *RGname* med dina egna värden:

```azurecli
az vm create --name VMname --resource-group RGname --image UbuntuLTS --generate-ssh-keys 
```

## <a name="provide-an-ssh-public-key-when-deploying-a-vm"></a>Ange en offentlig SSH-nyckel när du distribuerar en virtuell dator

Om du vill skapa en virtuell Linux-dator som använder SSH-nycklar för autentisering anger du din offentliga SSH-nyckel när du skapar den virtuella datorn med hjälp av Azure Portal, Azure CLI, Azure Resource Manager mallar eller andra metoder:

* [Skapa en virtuell Linux-dator med Azure-portalen](quick-create-portal.md)
* [Skapa en virtuell Linux-dator med Azure CLI](quick-create-cli.md)
* [Skapa en virtuell Linux-dator med hjälp av en Azure-mall](create-ssh-secured-vm-from-template.md)

Om du inte är bekant med formatet för en offentlig SSH-nyckel kan du Visa din offentliga nyckel med följande `cat` kommando och ersätta `~/.ssh/id_rsa.pub` med sökvägen och fil namnet för din egen offentliga nyckel fil om det behövs:

```bash
cat ~/.ssh/id_rsa.pub
```

Ett typiskt offentligt nyckel värde ser ut som i det här exemplet:

```
ssh-rsa AAAAB3NzaC1yc2EAABADAQABAAACAQC1/KanayNr+Q7ogR5mKnGpKWRBQU7F3Jjhn7utdf7Z2iUFykaYx+MInSnT3XdnBRS8KhC0IP8ptbngIaNOWd6zM8hB6UrcRTlTpwk/SuGMw1Vb40xlEFphBkVEUgBolOoANIEXriAMvlDMZsgvnMFiQ12tD/u14cxy1WNEMAftey/vX3Fgp2vEq4zHXEliY/sFZLJUJzcRUI0MOfHXAuCjg/qyqqbIuTDFyfg8k0JTtyGFEMQhbXKcuP2yGx1uw0ice62LRzr8w0mszftXyMik1PnshRXbmE2xgINYg5xo/ra3mq2imwtOKJpfdtFoMiKhJmSNHBSkK7vFTeYgg0v2cQ2+vL38lcIFX4Oh+QCzvNF/AXoDVlQtVtSqfQxRVG79Zqio5p12gHFktlfV7reCBvVIhyxc2LlYUkrq4DHzkxNY5c9OGSHXSle9YsO3F1J5ip18f6gPq4xFmo6dVoJodZm9N0YMKCkZ4k1qJDESsJBk2ujDPmQQeMjJX3FnDXYYB182ZCGQzXfzlPDC29cWVgDZEXNHuYrOLmJTmYtLZ4WkdUhLLlt5XsdoKWqlWpbegyYtGZgeZNRtOOdN6ybOPJqmYFd2qRtb4sYPniGJDOGhx4VodXAjT09omhQJpE6wlZbRWDvKC55R2d/CSPHJscEiuudb+1SG2uA/oik/WQ== username@domainname
```

Om du kopierar och klistrar in innehållet i den offentliga nyckel filen som ska användas i Azure Portal eller en Resource Manager-mall ser du till att du inte kopierar något avslutande blank steg. Om du vill kopiera en offentlig nyckel i macOS kan du skicka vidare den offentliga nyckel filen till `pbcopy` . På samma sätt i Linux kan du skicka vidare den offentliga nyckel filen till program som `xclip` .

Den offentliga nyckeln som du placerar på din virtuella Linux-dator i Azure lagras som standard i ~/.ssh/id_rsa. pub, om du inte har angett en annan plats när du skapade nyckel paret. Om du vill använda [Azure CLI 2,0](/cli/azure) för att skapa en virtuell dator med en befintlig offentlig nyckel anger du värdet och eventuellt platsen för den offentliga nyckeln med hjälp av kommandot [AZ VM Create](/cli/azure/vm#az-vm-create) med `--ssh-key-values` alternativet. I följande kommando ersätter du *myVM*, *myResourceGroup*, *UbuntuLTS*, *azureuser* och *mysshkey. pub* med dina egna värden:


```azurecli
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --ssh-key-values mysshkey.pub
```

Om du vill använda flera SSH-nycklar med den virtuella datorn kan du ange dem i en blankstegsavgränsad lista, som det här `--ssh-key-values sshkey-desktop.pub sshkey-laptop.pub` .


## <a name="ssh-into-your-vm"></a>SSH till den virtuella datorn

Med den offentliga nyckeln distribuerad på din virtuella Azure-dator och den privata nyckeln på det lokala systemet, använder du SSH i din virtuella dator med IP-adressen eller DNS-namnet för den virtuella datorn. I följande kommando ersätter du *azureuser* och *myvm.westus.cloudapp.Azure.com* med administratörs användar namnet och det fullständigt kvalificerade domän namnet (eller IP-adressen):

```bash
ssh azureuser@myvm.westus.cloudapp.azure.com
```

Om du har angett en lösen fras när du skapade nyckel paret anger du den lösen frasen när du uppmanas att göra det under inloggnings processen. Den virtuella datorn har lagts till i din ~/.ssh/-known_hosts-fil och du kommer inte att uppmanas att ansluta igen förrän antingen den offentliga nyckeln på din virtuella Azure-dator ändras eller om Server namnet har tagits bort från ~/.ssh/known_hosts.

Om den virtuella datorn använder just-in-Time-åtkomst-principen måste du begära åtkomst innan du kan ansluta till den virtuella datorn. Mer information om just-in-time-principen finns i [Hantera åtkomst till virtuella datorer med just-in-time-principen](../../security-center/security-center-just-in-time.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du arbetar med SSH-nyckelpar finns i [detaljerade steg för att skapa och hantera SSH-nyckel par](create-ssh-keys-detailed.md).

* Om du har problem med SSH-anslutningar till virtuella Azure-datorer kan du läsa [FELSÖK SSH-anslutningar till en virtuell Azure Linux-dator](/troubleshoot/azure/virtual-machines/troubleshoot-ssh-connection).