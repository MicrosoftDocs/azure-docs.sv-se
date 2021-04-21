---
title: Ladda upp eller kopiera en anpassad virtuell Linux-dator med Azure CLI
description: Ladda upp eller kopiera en anpassad virtuell dator med hjälp Resource Manager distributionsmodellen och Azure CLI
services: virtual-machines
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: how-to
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: ecad48592ecfb6723548a27997cfe1b81545b24a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759607"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Skapa en virtuell Linux-dator från en anpassad disk med Azure CLI

<!-- rename to create-vm-specialized -->

Den här artikeln visar hur du laddar upp en anpassad virtuell hårddisk (VHD) och hur du kopierar en befintlig virtuell hårddisk i Azure. Den nyligen skapade virtuella hårddisken används sedan för att skapa nya virtuella Linux-datorer (VM). Du kan installera och konfigurera en Linux-distribution enligt dina behov och sedan använda den virtuella hårddisken för att skapa en ny virtuell Azure-dator.

Om du vill skapa flera virtuella datorer från din anpassade disk skapar du först en avbildning från den virtuella datorn eller den virtuella hårddisken. Mer information finns i Skapa [en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI.](tutorial-custom-images.md)

Du har två alternativ för att skapa en anpassad disk:
* Ladda upp en virtuell hårddisk
* Kopiera en befintlig virtuell Azure-dator


## <a name="requirements"></a>Krav
För att slutföra följande steg behöver du:

- En virtuell Linux-dator som har förberetts för användning i Azure. Avsnittet [Förbered den virtuella](#prepare-the-vm) datorn i den här artikeln beskriver hur du hittar distro-specifik information om hur du installerar Azure Linux-agenten (waagent), som behövs för att du ska kunna ansluta till en virtuell dator med SSH.
- VHD-filen från en [befintlig Azure-godkänd Linux-distribution](endorsed-distros.md) (eller se information om [icke-godkända distributioner](create-upload-generic.md)) till en virtuell disk i VHD-format. Det finns flera verktyg för att skapa en virtuell dator och virtuell hårddisk:
  - Installera och konfigurera [QEMU eller](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) [KVM](https://www.linux-kvm.org/page/RunningKVM)och se till att använda VHD som avbildningsformat. Om det behövs kan du [konvertera en avbildning](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med `qemu-img convert` .
  - Du kan också använda Hyper-V [på Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) eller på Windows [Server 2012/2012 R2.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11))

> [!NOTE]
> Det nyare VHDX-formatet stöds inte i Azure. När du skapar en virtuell dator anger du VHD som format. Om det behövs kan du konvertera VHDX-diskar till VHD med [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller [Convert-VHD](/powershell/module/hyper-v/convert-vhd) PowerShell-cmdleten. Azure stöder inte uppladdning av dynamiska virtuella hårddiskar, så du måste konvertera sådana diskar till statiska virtuella hårddiskar innan du laddar upp. Du kan använda verktyg som [Azure VHD Utilities for GO](https://github.com/Microsoft/azure-vhd-utils-for-go) för att konvertera dynamiska diskar under uppladdningen till Azure.
> 
> 


- Kontrollera att du har den senaste [versionen av Azure CLI](/cli/azure/install-az-cli2) installerad och att du är inloggad på ett Azure-konto med az [login](/cli/azure/reference-index#az_login).

I följande exempel ersätter du exempelparameternamn med dina egna värden, till `myResourceGroup` exempel `mystorageaccount` , och `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn

Azure stöder olika Linux-distributioner (se [Godkända distributioner).](endorsed-distros.md) I följande artiklar beskrivs hur du förbereder de olika Linux-distributioner som stöds i Azure:

* [CentOS-baserade distributioner](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES och openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Andra: Icke-godkända distributioner](create-upload-generic.md)

Se även [installationsanteckningarna för Linux för](create-upload-generic.md#general-linux-installation-notes) mer allmänna tips om hur du förbereder Linux-avbildningar för Azure.

> [!NOTE]
> [Serviceavtalet för Azure-plattformen](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller endast för virtuella datorer som kör Linux när en av de godkända distributionerna används med konfigurationsinformationen som anges under "Versioner som stöds" i [Linux på Azure-Endorsed Distributioner](endorsed-distros.md).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Alternativ 1: Ladda upp en virtuell hårddisk

Du kan nu ladda upp en virtuell hårddisk direkt till en hanterad disk. Instruktioner finns i Ladda [upp en virtuell hårddisk till Azure med Hjälp av Azure CLI.](disks-upload-vhd-to-managed-disk-cli.md)

## <a name="option-2-copy-an-existing-vm"></a>Alternativ 2: Kopiera en befintlig virtuell dator

Du kan också skapa en anpassad virtuell dator i Azure och sedan kopiera OS-disken och koppla den till en ny virtuell dator för att skapa en till kopia. Detta fungerar bra vid testning, men om du vill använda en befintlig virtuell Azure-dator som modell för flera nya virtuella datorer skapar du en *avbildning i* stället. Mer information om hur du skapar en avbildning från en befintlig virtuell Azure-dator finns i Skapa en anpassad avbildning av [en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).

Om du vill kopiera en befintlig virtuell dator till en annan region kanske du vill använda azcopy för att skapa en kopia av en [disk i en annan region.](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 

Annars bör du ta en ögonblicksbild av den virtuella datorn och sedan skapa en ny os-VHD från ögonblicksbilden.

### <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Det här exemplet skapar en ögonblicksbild av en virtuell dator med namnet *myVM* i resursgruppen *myResourceGroup* och skapar en ögonblicksbild med namnet *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Skapa den hanterade disken

Skapa en ny hanterad disk från ögonblicksbilden.

Hämta ID:t för ögonblicksbilden. I det här exemplet heter ögonblicksbilden *osDiskSnapshot* och finns i resursgruppen *myResourceGroup.*

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Skapa den hanterade disken. I det här exemplet skapar vi en hanterad disk med namnet *myManagedDisk* från ögonblicksbilden, där disken är i standardlagring och har en storlek på 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa den virtuella datorn [med az vm create](/cli/azure/vm#az_vm_create) och attach (--attach-os-disk) till den hanterade disken som OS-disk. I följande exempel skapas en virtuell dator med *namnet myNewVM med hjälp* av den hanterade disken som du skapade från den uppladdade virtuella hårddisken:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Du bör kunna använda SSH i den virtuella datorn med autentiseringsuppgifterna från den virtuella källdatorn. 

## <a name="next-steps"></a>Nästa steg
När du har förberett och laddat upp din anpassade virtuella disk kan du läsa mer om att [använda Resource Manager och mallar](../../azure-resource-manager/management/overview.md). Du kanske också vill lägga [till en datadisk](add-disk.md) till dina nya virtuella datorer. Om du har program som körs på dina virtuella datorer som du behöver åtkomst till måste du [öppna portar och slutpunkter.](nsg-quickstart.md)
