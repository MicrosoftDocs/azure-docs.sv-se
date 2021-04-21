---
title: Skapa en hanterad avbildning av en virtuell Linux-dator med Hjälp av Azure CLI
description: Skapa en hanterad avbildning av en virtuell Azure-dator som ska användas för massdistributioner med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 10/08/2018
ms.author: cynthn
ms.custom: legacy, devx-track-azurecli
ms.collection: linux
ms.openlocfilehash: dddbad2403734bc749497a7acca16b2a5b6076f4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792263"
---
# <a name="how-to-create-a-managed-image-of-a-virtual-machine-or-vhd"></a>Så här skapar du en hanterad avbildning av en virtuell dator eller vhd

Om du vill skapa flera kopior av en virtuell dator (VM) för användning i Azure för utveckling och testning skapar du en hanterad avbildning av den virtuella datorn eller den virtuella OS-hårddisken. Information om hur du skapar, lagrar och delar avbildningar i stor skala finns [i Delade bildgallerier.](../shared-images-cli.md)

En hanterad avbildning stöder upp till 20 samtidiga distributioner. Försök att skapa fler än 20 virtuella datorer samtidigt, från samma hanterade avbildning, kan resultera i tidsgränser för etablering på grund av lagringsprestandabegränsningar för en enskild virtuell hårddisk. Om du vill skapa fler än 20 virtuella datorer samtidigt använder du en avbildning av delade avbildningsgallerier som konfigurerats med 1 replik för varje 20 samtidiga VM-distributioner. [](../shared-image-galleries.md)

Om du vill skapa en hanterad avbildning måste du ta bort personlig kontoinformation. I följande steg avetablera du en befintlig virtuell dator, avallokerar den och skapar en avbildning. Du kan använda den här avbildningen för att skapa virtuella datorer i alla resursgrupper i din prenumeration.

Om du vill skapa en kopia av din befintliga virtuella Linux-dator för säkerhetskopiering eller felsökning, eller ladda upp en specialiserad virtuell Linux-hårddisk från en lokal virtuell dator, se Ladda upp och skapa en virtuell Linux-dator från en anpassad [diskavbildning.](upload-vhd.md)  

Du kan använda **tjänsten Azure VM Image Builder (offentlig förhandsversion)** för att skapa din anpassade avbildning, du behöver inte lära dig några verktyg eller konfigurera bygg-pipelines, bara tillhandahålla en avbildningskonfiguration så skapar Image Builder avbildningen. Mer information finns i [Komma igång med Azure VM Image Builder](../image-builder-overview.md).

Du behöver följande innan du skapar en avbildning:

* En virtuell Azure-dator som Resource Manager i distributionsmodellen som använder hanterade diskar. Om du inte har skapat en virtuell Linux-dator än kan du använda [portalen,](quick-create-portal.md) [Azure CLI](quick-create-cli.md)eller [Resource Manager mallar](create-ssh-secured-vm-from-template.md). Konfigurera den virtuella datorn efter behov. Du kan till [exempel lägga till datadiskar,](add-disk.md)tillämpa uppdateringar och installera program. 

* Den senaste [versionen av Azure CLI](/cli/azure/install-az-cli2) har installerats och loggas in på ett Azure-konto med az [login](/cli/azure/reference-index#az_login).

## <a name="prefer-a-tutorial-instead"></a>Föredrar du en självstudie i stället?

En förenklad version av den här artikeln och för testning, utvärdering eller inlärning av virtuella datorer i Azure finns i Skapa en anpassad avbildning av en virtuell [Azure-dator med hjälp av CLI.](tutorial-custom-images.md)  Annars kan du fortsätta att läsa här för att få en fullständig bild.


## <a name="step-1-deprovision-the-vm"></a>Steg 1: Avetablera den virtuella datorn
Först avetablera du den virtuella datorn med hjälp av Azure VM-agenten för att ta bort datorspecifika filer och data. Använd kommandot `waagent` med parametern `-deprovision+user` på den virtuella Linux-källdatorn. Mer information finns i [Användarguide för Azure Linux Agent](../extensions/agent-linux.md). Den här processen kan inte ångras.

1. Anslut till din virtuella Linux-dator med en SSH-klient.
2. Ange följande kommando i SSH-fönstret:
   
    ```bash
    sudo waagent -deprovision+user
    ```
   > [!NOTE]
   > Kör bara det här kommandot på en virtuell dator som du ska avbilda som en avbildning. Det här kommandot garanterar inte att avbildningen rensas från all känslig information eller är lämplig för omdistribution. Parametern `+user` tar även bort det senast etablerade användarkontot. Om du vill behålla autentiseringsuppgifterna för användarkontot på den virtuella datorn använder du bara `-deprovision` .
 
3. Ange **y för** att fortsätta. Du kan lägga till `-force` parametern för att undvika det här bekräftelsesteget.
4. När kommandot har slutförts anger du **avsluta för** att stänga SSH-klienten.  Den virtuella datorn kommer fortfarande att köras nu.

## <a name="step-2-create-vm-image"></a>Steg 2: Skapa en VM-avbildning
Använd Azure CLI för att markera den virtuella datorn som generaliserad och avbilda avbildningen. I följande exempel ersätter du exempelparameternamn med dina egna värden. Exempelparameternamnen *är myResourceGroup,* *myVnet* och *myVM.*

1. Frisöka den virtuella dator som du avetablererade [med az vm deallocate](/cli/azure/vm). I följande exempel frisänds den virtuella datorn *med namnet myVM* i resursgruppen med namnet *myResourceGroup*.  
   
    ```azurecli
    az vm deallocate \
        --resource-group myResourceGroup \
        --name myVM
    ```
    
    Vänta tills den virtuella datorn har friserats helt innan du fortsätter. Det kan ta några minuter att slutföra.  Den virtuella datorn stängs av under avallokeringen.

2. Markera den virtuella datorn som generaliserad med [az vm generalize](/cli/azure/vm). I följande exempel markerar den virtuella datorn *med namnet myVM* i resursgruppen med namnet *myResourceGroup* som generaliserad.
   
    ```azurecli
    az vm generalize \
        --resource-group myResourceGroup \
        --name myVM
    ```

    En virtuell dator som har generaliserats kan inte längre startas om.

3. Skapa en avbildning av den virtuella datorresursen [med az image create](/cli/azure/image#az_image_create). I följande exempel skapas en avbildning med namnet *myImage* i resursgruppen med namnet *myResourceGroup* med hjälp av VM-resursen med namnet *myVM*.
   
    ```azurecli
    az image create \
        --resource-group myResourceGroup \
        --name myImage --source myVM
    ```
   
   > [!NOTE]
   > Avbildningen skapas i samma resursgrupp som den virtuella källdatorn. Du kan skapa virtuella datorer i valfri resursgrupp i din prenumeration från den här avbildningen. Ur ett hanteringsperspektiv kanske du vill skapa en specifik resursgrupp för dina VM-resurser och avbildningar.
   >
   > Om du vill lagra avbildningen i zonreparameterlagring måste du skapa den i en region som har stöd för [tillgänglighetszoner](../../availability-zones/az-overview.md) och inkluderar `--zone-resilient true` parametern .
   
Det här kommandot returnerar JSON som beskriver VM-avbildningen. Spara dessa utdata för senare referens.

## <a name="step-3-create-a-vm-from-the-captured-image"></a>Steg 3: Skapa en virtuell dator från den avbildade avbildningen
Skapa en virtuell dator med hjälp av avbildningen som du skapade [med az vm create](/cli/azure/vm). I följande exempel skapas en virtuell dator med *namnet myVMDeployed från* avbildningen med namnet *myImage*.

```azurecli
az vm create \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --image myImage\
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```

### <a name="creating-the-vm-in-another-resource-group"></a>Skapa den virtuella datorn i en annan resursgrupp 

Du kan skapa virtuella datorer från en avbildning i valfri resursgrupp i din prenumeration. Om du vill skapa en virtuell dator i en annan resursgrupp än avbildningen anger du det fullständiga resurs-ID:t för avbildningen. Använd [az image list för](/cli/azure/image#az_image_list) att visa en lista med bilder. De utdata som genereras påminner om de i följande exempel.

```json
"id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage",
   "location": "westus",
   "name": "myImage",
```

I följande exempel används [az vm create för att](/cli/azure/vm#az_vm_create) skapa en virtuell dator i en annan resursgrupp än källavbildningen genom att ange resurs-ID:t för avbildningen.

```azurecli
az vm create \
   --resource-group myOtherResourceGroup \
   --name myOtherVMDeployed \
   --image "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/images/myImage" \
   --admin-username azureuser \
   --ssh-key-value ~/.ssh/id_rsa.pub
```


## <a name="step-4-verify-the-deployment"></a>Steg 4: Verifiera distributionen

SSH till den virtuella dator som du skapade för att verifiera distributionen och börja använda den nya virtuella datorn. Om du vill ansluta via SSH hittar du IP-adressen eller FQDN för den virtuella datorn med [az vm show](/cli/azure/vm#az_vm_show).

```azurecli
az vm show \
   --resource-group myResourceGroup \
   --name myVMDeployed \
   --show-details
```

## <a name="next-steps"></a>Nästa steg
Information om hur du skapar, lagrar och delar avbildningar i stor skala finns [i Delade bildgallerier.](../shared-images-cli.md)
