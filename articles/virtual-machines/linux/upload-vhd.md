---
title: Ladda upp eller kopiera en anpassad virtuell Linux-dator med Azure CLI
description: Ladda upp eller kopiera en anpassad virtuell dator med hjälp av distributions modellen för Resource Manager och Azure CLI
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
ms.openlocfilehash: 9d549d77b4a60f7543f69a9fd89e8b538c95d010
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102564619"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Skapa en virtuell Linux-dator från en anpassad disk med Azure CLI

<!-- rename to create-vm-specialized -->

Den här artikeln visar hur du laddar upp en anpassad virtuell hård disk (VHD) och hur du kopierar en befintlig virtuell hård disk i Azure. Den nyligen skapade virtuella hård disken används sedan för att skapa nya virtuella Linux-datorer. Du kan installera och konfigurera en Linux-distribution på dina krav och sedan använda den virtuella hård disken för att skapa en ny virtuell Azure-dator.

Skapa flera virtuella datorer från din anpassade disk genom att först skapa en avbildning från din virtuella dator eller VHD. Mer information finns i [skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).

Det finns två alternativ för att skapa en anpassad disk:
* Ladda upp en virtuell hårddisk
* Kopiera en befintlig virtuell Azure-dator


## <a name="requirements"></a>Krav
För att utföra följande steg behöver du:

- En virtuell Linux-dator som har förberetts för användning i Azure. Avsnittet [förbereda den virtuella datorn](#prepare-the-vm) i den här artikeln beskriver hur du hittar distribution information om hur du installerar Azure Linux-agenten (waagent), vilket krävs för att du ska kunna ansluta till en virtuell dator med SSH.
- VHD-filen från en befintlig [Azure-godkänd Linux-distribution](endorsed-distros.md) (eller Visa [information om icke-godkända distributioner](create-upload-generic.md)) till en virtuell disk i VHD-format. Det finns flera verktyg för att skapa en virtuell dator och en virtuell hård disk:
  - Installera och konfigurera [qemu](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) eller [kvm](https://www.linux-kvm.org/page/RunningKVM)och ta hand om att använda VHD som avbildnings format. Om det behövs kan du [konvertera en avbildning](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) med `qemu-img convert` .
  - Du kan också använda Hyper-V [på Windows 10](/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) eller [Windows Server 2012/2012 R2](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh846766(v=ws.11)).

> [!NOTE]
> Det nya VHDX-formatet stöds inte i Azure. När du skapar en virtuell dator anger du VHD som format. Vid behov kan du konvertera VHDX-diskar till en virtuell hård disk med [qemu-img-konvertering](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) eller PowerShell-cmdleten [Convert-VHD](/powershell/module/hyper-v/convert-vhd) . Azure stöder inte uppladdning av dynamiska virtuella hård diskar, så du måste konvertera sådana diskar till statiska virtuella hård diskar innan du laddar upp. Du kan använda verktyg som [Azure VHD-verktyg för att gå](https://github.com/Microsoft/azure-vhd-utils-for-go) till konvertera dynamiska diskar under processen med att ladda upp dem till Azure.
> 
> 


- Kontrol lera att du har den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) och att du är inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index#az-login).

I följande exempel ersätter du parameter namn med dina egna värden, till exempel `myResourceGroup` , `mystorageaccount` och `mydisks` .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Förbereda den virtuella datorn

Azure har stöd för olika Linux-distributioner (se godkända [distributioner](endorsed-distros.md)). I följande artiklar beskrivs hur du förbereder de olika Linux-distributioner som stöds i Azure:

* [CentOS-baserade distributioner](create-upload-centos.md)
* [Debian Linux](debian-create-upload-vhd.md)
* [Oracle Linux](oracle-create-upload-vhd.md)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
* [SLES och openSUSE](suse-create-upload-vhd.md)
* [Ubuntu](create-upload-ubuntu.md)
* [Andra: icke-godkända distributioner](create-upload-generic.md)

Mer information om hur du förbereder Linux-avbildningar för Azure finns också i [Linux-installationsprogrammet](create-upload-generic.md#general-linux-installation-notes) .

> [!NOTE]
> [Service avtalet för Azure-plattformen](https://azure.microsoft.com/support/legal/sla/virtual-machines/) gäller endast virtuella datorer som kör Linux när en av de godkända distributionerna används med konfigurations informationen som anges under "versioner som stöds" i [Linux på Azure-Endorsed distributioner](endorsed-distros.md).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Alternativ 1: Ladda upp en virtuell hård disk

Du kan nu ladda upp VHD direkt till en hanterad disk. Instruktioner finns i [Ladda upp en virtuell hård disk till Azure med Azure CLI](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Alternativ 2: kopiera en befintlig virtuell dator

Du kan också skapa en anpassad virtuell dator i Azure och sedan kopiera OS-disken och koppla den till en ny virtuell dator för att skapa en ny kopia. Detta är bra för testning, men om du vill använda en befintlig virtuell Azure-dator som modell för flera nya virtuella datorer skapar du en *avbildning* i stället. Mer information om hur du skapar en avbildning från en befintlig virtuell Azure-dator finns i [skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).

Om du vill kopiera en befintlig virtuell dator till en annan region kanske du vill använda AzCopy för att [skap en kopia av en disk i en annan region](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

Annars bör du ta en ögonblicks bild av den virtuella datorn och sedan skapa en ny OS-VHD från ögonblicks bilden.

### <a name="create-a-snapshot"></a>Skapa en ögonblicksbild

Det här exemplet skapar en ögonblicks bild av en virtuell dator med namnet *myVM* i resurs gruppen *myResourceGroup* och skapar en ögonblicks bild med namnet *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Skapa den hanterade disken

Skapa en ny hanterad disk från ögonblicks bilden.

Hämta ögonblicks bildens ID. I det här exemplet heter ögonblicks bilden *osDiskSnapshot* och finns i resurs gruppen *myResourceGroup* .

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Skapa den hanterade disken. I det här exemplet ska vi skapa en hanterad disk med namnet *myManagedDisk* från vår ögonblicks bild, där disken är i standard lagring och storlek 128 GB.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa din virtuella dator med [AZ VM Create](/cli/azure/vm#az-vm-create) and attach (--Attach-OS-disk) den hanterade disken som operativ system disk. I följande exempel skapas en virtuell dator med namnet *myNewVM* med den hanterade disk som du skapade från den uppladdade virtuella hård disken:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Du bör kunna SSH till den virtuella datorn med autentiseringsuppgifterna från den virtuella käll datorn. 

## <a name="next-steps"></a>Nästa steg
När du har för berett och överfört din anpassade virtuella disk kan du läsa mer om hur du [använder Resource Manager och mallar](../../azure-resource-manager/management/overview.md). Du kanske också vill [lägga till en datadisk](add-disk.md) till de nya virtuella datorerna. Om du har program som körs på dina virtuella datorer som du behöver åtkomst till, måste du [öppna portar och slut punkter](nsg-quickstart.md).
