---
title: Avbilda en hanterad avbildning av en virtuell Linux-dator med Azure CLI
description: Avbilda en hanterad avbildning av en virtuell Azure-dator som ska användas för Mass distributioner med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.openlocfilehash: 53fb11216e65ebead43c02a7153d937c37b841a0
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98681068"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Så här skapar du en hanterad avbildning av en virtuell dator eller virtuell hård disk

Om du vill skapa flera kopior av en virtuell dator (VM) för användning i Azure för utveckling och testning kan du avbilda en hanterad avbildning av den virtuella datorn eller OS-VHD: n. Information om hur du skapar, lagrar och delar bilder i skala finns i [delade avbildnings gallerier](../shared-images-cli.md).

En hanterad avbildning har stöd för upp till 20 samtidiga distributioner. Om du försöker skapa fler än 20 virtuella datorer samtidigt, från samma hanterade avbildning, kan timeout uppstå för etablerings tids gränsen på grund av lagrings prestanda begränsningarna för en enda virtuell hård disk. Om du vill skapa fler än 20 virtuella datorer samtidigt använder du en avbildning av [delade avbildnings gallerier](../shared-image-galleries.md) som kon figurer ATS med 1 replik för varje 20 samtidiga VM-distributioner.

Om du vill skapa en hanterad avbildning måste du ta bort personlig konto information. I följande steg avetablerar du en befintlig virtuell dator, frigör den och skapar en avbildning. Du kan använda den här avbildningen för att skapa virtuella datorer i alla resurs grupper i din prenumeration.

Om du vill skapa en kopia av din befintliga virtuella Linux-dator för säkerhets kopiering eller fel sökning eller ladda upp en specialiserad Linux-VHD från en lokal virtuell dator, se [Ladda upp och skapa en virtuell Linux-dator från anpassad disk avbildning](upload-vhd.md).  

Du kan använda tjänsten **Azure VM Image Builder (offentlig för hands version)** för att skapa en anpassad avbildning, inget behov av att lära sig några verktyg eller skapa pipelines för att skapa en avbildning av Image Builder. Mer information finns i [komma igång med Azure VM Image Builder](../image-builder-overview.md).

Du behöver följande objekt innan du skapar en avbildning:

* En virtuell Azure-dator som skapats i Resource Manager-distributions modellen som använder hanterade diskar. Om du ännu inte har skapat en virtuell Linux-dator kan du använda [portalen](quick-create-portal.md), [Azure CLI](quick-create-cli.md)-eller [Resource Manager-mallarna](create-ssh-secured-vm-from-template.md). Konfigurera den virtuella datorn efter behov. Du kan till exempel [lägga till data diskar](add-disk.md), tillämpa uppdateringar och installera program. 

* Den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) är installerad och är inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index#az-login).

## <a name="prefer-a-tutorial-instead"></a>Föredrar du en själv studie kurs i stället?

För en förenklad version av den här artikeln och för testning, utvärdering eller inlärning av virtuella datorer i Azure, se [skapa en anpassad avbildning av en virtuell Azure-dator med hjälp av CLI](tutorial-custom-images.md).  Annars fortsätter du att läsa hit för att hämta hela bilden.


## <a name="step-1-deprovision-the-vm"></a>Steg 1: avetablera den virtuella datorn
Först avetablerar du den virtuella datorn med hjälp av Azure VM-agenten för att ta bort datorspecifika filer och data. Använd `waagent` kommandot med `-deprovision+user` parametern på den virtuella käll Linux-datorn. Mer information finns i [Användarguide för Azure Linux Agent](../extensions/agent-linux.md).

1. Anslut till din virtuella Linux-dator med en SSH-klient.
2. I SSH-fönstret anger du följande kommando:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Kör bara det här kommandot på en virtuell dator som du ska avbilda som en avbildning. Det här kommandot garanterar inte att avbildningen är klar med all känslig information eller är lämplig för omdistribution. `+user`Parametern tar också bort det senast etablerade användar kontot. Använd endast om du vill behålla autentiseringsuppgifterna för användar kontot på den virtuella datorn `-deprovision` .
 
3. Fortsätt genom att ange **y** . Du kan lägga till `-force` parametern för att undvika det här bekräftelse steget.
4. När kommandot har slutförts anger du **Avsluta** för att stänga SSH-klienten.  Den virtuella datorn kommer fortfarande att köras nu.

## <a name="step-2-create-vm-image"></a>Steg 2: skapa en VM-avbildning
Använd Azure CLI för att markera den virtuella datorn som generaliserad och avbilda avbildningen. Ersätt exempel parameter namn med dina egna värden i följande exempel. Exempel på parameter namn är *myResourceGroup*, *myVnet* och *myVM*.

1. Frigör den virtuella dator som du avetablerat med [AZ VM-frigörning](/cli/azure/vm). I följande exempel avallokeras den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Vänta tills den virtuella datorn är helt avallokerad innan du går vidare. Det kan ta några minuter att slutföra.  Den virtuella datorn stängs av under avallokeringen.

2. Markera den virtuella datorn som generaliserad med [AZ VM generalize](/cli/azure/vm). I följande exempel märks den virtuella datorn med namnet *myVM* i resurs gruppen med namnet *myResourceGroup* som generaliserad.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    En virtuell dator som har generaliserats kan inte längre startas om.

3. Skapa en avbildning av den virtuella dator resursen med [AZ image Create](/cli/azure/image#az-image-create). I följande exempel skapas en avbildning med namnet *image* i resurs gruppen med namnet *myResourceGroup* med hjälp av den virtuella dator resursen med namnet *myVM*.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Avbildningen skapas i samma resurs grupp som den virtuella käll datorn. Du kan skapa virtuella datorer i alla resurs grupper i din prenumeration från den här avbildningen. Från ett hanterings perspektiv kanske du vill skapa en resurs grupp för dina VM-resurser och avbildningar.
   >
   > Om du vill lagra avbildningen i zoner – elastisk lagring måste du skapa den i en region som har stöd för [tillgänglighets zoner](../../availability-zones/az-overview.md) och inkludera `--zone-resilient true` parametern.
   
Det här kommandot returnerar JSON som beskriver avbildningen av den virtuella datorn. Spara utdata för senare referens.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Steg 3: skapa en virtuell dator från avbildningen
Skapa en virtuell dator med hjälp av avbildningen som du skapade med [AZ VM Create](/cli/azure/vm). I följande exempel skapas en virtuell dator med namnet *myVMDeployed* från avbildningen med namnet *image*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Skapa den virtuella datorn i en annan resurs grupp 

Du kan skapa virtuella datorer från en avbildning i alla resurs grupper i din prenumeration. Om du vill skapa en virtuell dator i en annan resurs grupp än avbildningen anger du det fullständiga resurs-ID: t för avbildningen. Använd [AZ avbildnings lista](/cli/azure/image#az-image-list) om du vill visa en lista över avbildningar. De utdata som genereras påminner om de i följande exempel.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

I följande exempel används [AZ VM Create](/cli/azure/vm#az-vm-create) för att skapa en virtuell dator i en annan resurs grupp än käll avbildningen genom att ange resurs-ID för avbildningen.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Steg 4: verifiera distributionen

SSH till den virtuella datorn som du skapade för att verifiera distributionen och börja använda den nya virtuella datorn. För att ansluta via SSH hittar du IP-adressen eller FQDN för den virtuella datorn med [AZ VM show](/cli/azure/vm#az-vm-show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar, lagrar och delar bilder i skala finns i [delade avbildnings gallerier](../shared-images-cli.md).