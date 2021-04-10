---
title: Skapa och ladda upp en OpenBSD-avbildning
description: Lär dig hur du skapar och laddar upp en virtuell hård disk (VHD) som innehåller operativ systemet OpenBSD för att skapa en virtuell Azure-dator via Azure CLI
author: gbowerman
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 3437fcd2d1d521aad237ecf6d3f7937b4835d376
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102554606"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Skapa och ladda upp en OpenBSD disk avbildning till Azure
Den här artikeln visar hur du skapar och laddar upp en virtuell hård disk (VHD) som innehåller operativ systemet OpenBSD. När du har laddat upp det kan du använda det som en egen avbildning för att skapa en virtuell dator (VM) i Azure via Azure CLI.


## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har följande objekt:

* **En Azure-prenumeration** – om du inte har något konto kan du skapa ett på bara några minuter. Om du har en MSDN-prenumeration kan du se [månatlig Azure-kredit för Visual Studio-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Annars kan du lära dig hur du [skapar ett kostnads fritt utvärderings konto](https://azure.microsoft.com/pricing/free-trial/).  
* **Azure CLI** – kontrol lera att du har det senaste [Azure CLI](/cli/azure/install-azure-cli) installerat och inloggat på ditt Azure-konto med [AZ-inloggning](/cli/azure/reference-index).
* **OpenBSD operativ system installerat i en VHD-fil** – ett OpenBSD-operativsystem som stöds ([6,6 version amd64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) måste installeras på en virtuell hård disk. Det finns flera verktyg för att skapa. VHD-filer. Du kan till exempel använda en virtualiseringslösning som Hyper-V för att skapa VHD-filen och installera operativ systemet. Anvisningar om hur du installerar och använder Hyper-V finns i [Installera Hyper-v och skapa en virtuell dator](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).


## <a name="prepare-openbsd-image-for-azure"></a>Förbereda OpenBSD-avbildning för Azure
På den virtuella datorn där du installerade operativ systemet OpenBSD 6,1, som har lagt till Hyper-V-stöd, slutför du följande procedurer:

1. Om DHCP inte är aktiverat under installationen aktiverar du tjänsten på följande sätt:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Konfigurera en serie konsol enligt följande:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Konfigurera paket installationen enligt följande:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Som standard `root` är användaren inaktive rad på virtuella datorer i Azure. Användare kan köra kommandon med utökade privilegier genom att använda `doas` kommandot på OPENBSD VM. DOAS är aktiverat som standard. Mer information finns i [DOAS. conf](https://man.openbsd.org/doas.conf.5). 

5. Installera och konfigurera krav för Azure-agenten på följande sätt:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. Den senaste versionen av Azure-agenten kan alltid hittas på [GitHub](https://github.com/Azure/WALinuxAgent/releases). Så här installerar du agenten:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > När du har installerat Azure agent är det en bra idé att kontrol lera att den körs enligt följande:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Avetablera systemet för att rensa det och göra det lämpligt för att etablera. Följande kommando tar också bort det senast etablerade användar kontot och tillhör ande data:

    ```sh
    waagent -deprovision+user -force
    ```

Nu kan du stänga av den virtuella datorn.


## <a name="prepare-the-vhd"></a>Förbered den virtuella hård disken
VHDX-formatet stöds inte i Azure, endast **fast virtuell hård disk**. Du kan konvertera disken till fast VHD-format med Hyper-V Manager eller PowerShell-cmdleten [Convert-VHD](/powershell/module/hyper-v/convert-vhd) . Ett exempel är följande.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Skapa lagrings resurser och ladda upp
Skapa först en resursgrupp med [az group create](/cli/azure/group). I följande exempel skapas en resurs grupp med namnet *myResourceGroup* på platsen för *öster* :

```azurecli
az group create --name myResourceGroup --location eastus
```

Om du vill ladda upp en virtuell hård disk skapar du ett lagrings konto med [AZ lagrings konto Create](/cli/azure/storage/account). Lagrings konto namn måste vara unika, så ange ditt eget namn. I följande exempel skapas ett lagrings konto med namnet *mystorageaccount*:

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Du styr åtkomsten till lagrings kontot genom att hämta lagrings nyckeln med [AZ lagrings konto nycklar](/cli/azure/storage/account/keys) på följande sätt:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

För att logiskt separera de virtuella hård diskar som du överför, skapar du en behållare i lagrings kontot med [AZ Storage container Create](/cli/azure/storage/container):

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Ladda slutligen upp den virtuella hård disken med [AZ Storage BLOB upload](/cli/azure/storage/blob) enligt följande:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>Skapa en virtuell dator från din virtuella hård disk
Du kan skapa en virtuell dator med ett [exempel skript](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-vhd) eller direkt med [AZ VM Create](/cli/azure/vm). Om du vill ange OpenBSD-VHD: n som du överför använder du `--image` parametern på följande sätt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Hämta IP-adressen för din virtuella OpenBSD-dator med [AZ VM List-IP-adresser](/cli/azure/vm) enligt följande:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Nu kan du använda SSH till din virtuella OpenBSD-dator som normalt:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Nästa steg
Om du vill veta mer om Hyper-V-stöd på OpenBSD 6.1 läser du [OpenBSD 6,1](https://www.openbsd.org/61.html) och [HyperV. 4](https://man.openbsd.org/hyperv.4).

Om du vill skapa en virtuell dator från den hanterade disken läser du [AZ disk](/cli/azure/disk).