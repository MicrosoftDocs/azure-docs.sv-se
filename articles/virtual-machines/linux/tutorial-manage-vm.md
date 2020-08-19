---
title: Självstudie – Skapa och hantera virtuella Linux-datorer med Azure CLI
description: I den här självstudien lär du dig hur du använder Azure CLI för att skapa och hantera virtuella Linux-datorer i Azure
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/23/2018
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 601e51c7a587cd55e9b0b235bbdfb2e559be09e2
ms.sourcegitcommit: 64ad2c8effa70506591b88abaa8836d64621e166
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2020
ms.locfileid: "88263103"
---
# <a name="tutorial-create-and-manage-linux-vms-with-the-azure-cli"></a>Självstudie: Skapa och hantera virtuella Linux-datorer med Azure CLI

Med virtuella Azure-datorer får du en fullständigt konfigurerbar och flexibel datormiljö. I den här självstudien beskrivs den grundläggande distributionen av virtuella Azure-datorer, till exempel att välja en VM-storlek, välja en VM-avbildning och distribuera en virtuell dator. Lär dig att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välja och använda VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuella datorer

I den här självstudien används CLI i [Azure Cloud Shell](../../cloud-shell/overview.md), som uppdateras kontinuerligt till den senaste versionen. Om du vill öppna Cloud Shell väljer du **testa den** överst i ett kodblock.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [az group create](/cli/azure/group). 

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. En resursgrupp måste skapas före den virtuella datorn. I det här exemplet skapas en resursgrupp med namnet *myResourceGroupVM* i regionen *eastus*. 

```azurecli-interactive
az group create --name myResourceGroupVM --location eastus
```

Resursgruppen som anges när du skapar eller ändrar en virtuell dator visas i hela den här självstudien.

## <a name="create-virtual-machine"></a>Skapa en virtuell dator

Skapa en virtuell dator med kommandot [az vm create](/cli/azure/vm). 

När du skapar en virtuell dator finns flera tillgängliga alternativ, som t.ex. avbildning av operativsystemet, bestämning av diskstorlek och administrativa autentiseringsuppgifter. I följande exempel skapas en virtuell dator med namnet *myVM* som kör en Ubuntu Server. Ett användarkonto med namnet *azureuser* skapas på den virtuella datorn och om inte SSH-nycklar finns genereras de på standardplatsen för nyckeln (*~/.ssh*):

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Det kan ta några minuter att skapa den virtuella datorn. När den virtuella datorn skapats visar Azure CLI information om den virtuella datorn. Skriv upp `publicIpAddress` eftersom adressen kan användas för att komma åt den virtuella datorn. 

```output
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroupVM/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroupVM"
}
```

## <a name="connect-to-vm"></a>Ansluta till virtuell dator

Du kan nu ansluta till den virtuella datorn via SSH i Azure Cloud Shell eller från den lokala datorn. Byt ut IP-adressen i exemplet mot den `publicIpAddress` du skrev upp i föregående steg.

```bash
ssh azureuser@52.174.34.95
```

När du loggat in i den virtuella datorn kan du installera och konfigurera program. När du är klar stänger du SSH-sessionen på vanligt sätt:

```bash
exit
```

## <a name="understand-vm-images"></a>Förstå VM-avbildningar

På Azures marknadsplats finns många avbildningar som kan användas för att skapa virtuella datorer. I de föregående stegen skapades en virtuell dator med en Ubuntu-avbildning. I det här steget används Azure CLI för att söka i marknadsplatsen efter en CentOS-avbildning som sedan används för att distribuera en andra virtuella dator. 

Du kan lista de vanligaste avbildningarna med kommandot [az vm image list](/cli/azure/vm/image).

```azurecli-interactive 
az vm image list --output table
```

Kommandot returnerar de populäraste avbildningarna av virtuella datorer på Azure.

```output
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2016-Datacenter     MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest     Win2016Datacenter    latest
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
CentOS         OpenLogic               7.3                 OpenLogic:CentOS:7.3:latest                                     CentOS               latest
openSUSE-Leap  SUSE                    42.2                SUSE:openSUSE-Leap:42.2:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7.3                 RedHat:RHEL:7.3:latest                                          RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

En fullständig lista visas om du anger argumentet `--all`. Listan med avbildningar kan även filtreras efter `--publisher` eller `–-offer`. I det här exemplet är listan filtrerad så den visar alla avbildningar som överensstämmer med *CentOS*. 

```azurecli-interactive 
az vm image list --offer CentOS --all --output table
```

Delvisa utdata:

```output
Offer             Publisher         Sku   Urn                                     Version
----------------  ----------------  ----  --------------------------------------  -----------
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201501         6.5.201501
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201503         6.5.201503
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.201506         6.5.201506
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20150904       6.5.20150904
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20160309       6.5.20160309
CentOS            OpenLogic         6.5   OpenLogic:CentOS:6.5:6.5.20170207       6.5.20170207
```

Om du vill distribuera en virtuell dator med en specifik avbildning, antecknar du värdet i *Urn*-kolumnen vilken består av utgivare, erbjudande, SKU och eventuellt ett versionsnummer för att [identifiera](cli-ps-findimage.md#terminology) avbildningen. När en avbildning anges kan avbildningens versionsnummer ersättas med ”latest” (senaste), vilket väljer den senaste versionen av distributionen. I det här exemplet används argumentet `--image` för att ange den senaste versionen av en CentOS 6.5-avbildning.  

```azurecli-interactive
az vm create --resource-group myResourceGroupVM --name myVM2 --image OpenLogic:CentOS:6.5:latest --generate-ssh-keys
```

## <a name="understand-vm-sizes"></a>Förstå VM-storlekar

Storleken på den virtuella datorn avgör hur mycket av beräkningsresurser som CPU, GPU och minne som är tillgängliga för den virtuella datorn. Virtuella datorer måste ha lämplig storlek för förväntad arbetsbelastning. Om arbetsbelastningen ökar kan man ändra storlek på den befintliga virtuella datorn.

### <a name="vm-sizes"></a>VM-storlekar

I följande tabell kategoriseras storlekarna i användningsfall.  

| Typ                     | Normala storlekar           |    Beskrivning       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Generell användning](../sizes-general.md)         |B, Dsv3, Dv3, DSv2, Dv2, Av2, DC| Balanserat förhållande mellan processor och minne. Perfekt för utveckling eller test samt små till medelstora lösningar för program och data.  |
| [Beräkningsoptimerad](../sizes-compute.md)   | Fsv2          | Högt förhållande mellan processor och minne. Bra för program med medelhög trafik, nätverkstillämpningar och batchprocesser.        |
| [Minnesoptimerad](../sizes-memory.md)    | Esv3, Ev3, M, DSv2, Dv2  | Högt förhållande mellan minne och kärna. Utmärkt för relationsdatabaser, mellanstora till stora cacheminnen och minnesinterna analyser.                 |
| [Lagringsoptimerad](../sizes-storage.md)      | Lsv2, LS              | Högt diskgenomflöde och I/O. Perfekt för stordata, SQL- och NoSQL-databaser.                                                         |
| [GPU](../sizes-gpu.md)          | NV, NVv2, NC, NCv2, NCv3, ND            | Virtuella specialdatorer som är avsedda för tung grafisk rendering och videoredigering.       |
| [Höga prestanda](../sizes-hpc.md) | H        | Virtuella datorer med de kraftfullaste processorerna och nätverksgränssnitt för stora dataflöden (RDMA). |


### <a name="find-available-vm-sizes"></a>Hitta tillgängliga VM-storlekar

Om du vill se en lista med VM-storlekar som är tillgängliga i en viss region kan du använda kommandot [az vm list-sizes](/cli/azure/vm). 

```azurecli-interactive 
az vm list-sizes --location eastus --output table
```

Delvisa utdata:

```output
  MaxDataDiskCount    MemoryInMb  Name                      NumberOfCores    OsDiskSizeInMb    ResourceDiskSizeInMb
------------------  ------------  ----------------------  ---------------  ----------------  ----------------------
                 2          3584  Standard_DS1                          1           1047552                    7168
                 4          7168  Standard_DS2                          2           1047552                   14336
                 8         14336  Standard_DS3                          4           1047552                   28672
                16         28672  Standard_DS4                          8           1047552                   57344
                 4         14336  Standard_DS11                         2           1047552                   28672
                 8         28672  Standard_DS12                         4           1047552                   57344
                16         57344  Standard_DS13                         8           1047552                  114688
                32        114688  Standard_DS14                        16           1047552                  229376
                 1           768  Standard_A0                           1           1047552                   20480
                 2          1792  Standard_A1                           1           1047552                   71680
                 4          3584  Standard_A2                           2           1047552                  138240
                 8          7168  Standard_A3                           4           1047552                  291840
                 4         14336  Standard_A5                           2           1047552                  138240
                16         14336  Standard_A4                           8           1047552                  619520
                 8         28672  Standard_A6                           4           1047552                  291840
                16         57344  Standard_A7                           8           1047552                  619520
```

### <a name="create-vm-with-specific-size"></a>Skapa en virtuell dator med en specifik storlek

I föregående exempel angavs inte en storlek vilket ger en standardstorlek på den virtuella datorn. En storlek för den virtuella datorn kan väljas när den skapas med kommandot [az vm create](/cli/azure/vm) och argumentet `--size`. 

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupVM \
    --name myVM3 \
    --image UbuntuLTS \
    --size Standard_F4s \
    --generate-ssh-keys
```

### <a name="resize-a-vm"></a>Ändra storlek på en virtuell dator

När en virtuell dator har distribuerats kan storleken ändras för att öka eller minska resurstilldelningen. Kommandot [az vm show](/cli/azure/vm) visar den virtuella datorns aktuella storlek:

```azurecli-interactive
az vm show --resource-group myResourceGroupVM --name myVM --query hardwareProfile.vmSize
```

Kontrollera om önskad storlek är tillgänglig i det aktuella Azure-klustret innan du ändrar storleken på en virtuell dator. Kommandot [az vm list-vm-resize-options](/cli/azure/vm) returnerar en lista med storlekar. 

```azurecli-interactive 
az vm list-vm-resize-options --resource-group myResourceGroupVM --name myVM --query [].name
```

Om önskad storlek är tillgänglig kan storleken på den virtuella datorn ändras medan den är igång, men den startas om under åtgärden. Använd kommandot [az vm resize]( /cli/azure/vm) för att ändra storleken.

```azurecli-interactive 
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_DS4_v2
```

Om önskad storlek inte finns i det aktuella klustret måste den virtuella datorn frigöras innan åtgärden för att ändra storlek kan utföras. Använd kommandot [az vm deallocate]( /cli/azure/vm) för att stoppa och frigöra den virtuella datorn. När den virtuella datorn startas igen kan alla data på den temporära disken tas bort. Den offentliga IP-adressen ändras också om inte en statisk IP-adress används. 

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupVM --name myVM
```

När datorn är frigjord kan storleksändringen utföras. 

```azurecli-interactive
az vm resize --resource-group myResourceGroupVM --name myVM --size Standard_GS1
```

När storleksändringen utförts kan den virtuella datorn startas.

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

## <a name="vm-power-states"></a>Energinivåer för VM

En virtuell Azure-dator kan ha en av många energinivåer. Det här tillståndet motsvarar aktuellt tillstånd för den virtuella datorn i hypervisor-programmet. 

### <a name="power-states"></a>Energinivåer

| Energinivå | Beskrivning
|----|----|
| Startar | Anger att den virtuella datorn startas. |
| Körs | Anger att den virtuella datorn körs. |
| Stoppas | Anger att den virtuella datorn stoppas. | 
| Stoppad | Anger att den virtuella datorn har stoppats. Virtuella datorer i ett stoppat tillstånd kan fortfarande medföra debitering.  |
| Frigör | Anger att den virtuella datorn frigörs. |
| Frigjord | Anger att den virtuella datorn är frånkopplad från hypervisor-programmet, men att den fortfarande är tillgänglig i kontrollplanet. Virtuella datorer med tillståndet Frigjord medför inte några debiteringar. |
| - | Anger att energinivån för den virtuella datorn är okänd. |

### <a name="find-the-power-state"></a>Hitta energinivån

Om du vill hämta tillståndet för en viss virtuell dator, använder du kommandot [az vm get instance-view](/cli/azure/vm). Du måste ange ett giltigt namn för en virtuell dator och resursgrupp. 

```azurecli-interactive
az vm get-instance-view \
    --name myVM \
    --resource-group myResourceGroupVM \
    --query instanceView.statuses[1] --output table
```

Resultat:

```output
ode                DisplayStatus    Level
------------------  ---------------  -------
PowerState/running  VM running       Info
```

Om du vill hämta energi läget för alla virtuella datorer i din prenumeration använder du [Virtual Machines-List all API](https://docs.microsoft.com/rest/api/compute/virtualmachines/listall) med parametern **statusOnly** inställd på *True*.

## <a name="management-tasks"></a>Hanteringsuppgifter

Under livscykeln för en virtuell dator kan du vilja utföra administrativa uppgifter som att starta, stoppa eller ta bort en virtuell dator. Dessutom kanske du vill skapa skript för att automatisera repetitiva och komplicerade uppgifter. Med Azure CLI kan många vanliga administrativa uppgifter köras från kommandoraden eller i skript. 

### <a name="get-ip-address"></a>Hämta IP-adress

Det här kommandot returnerar de privata och offentliga IP-adresserna för en virtuell dator.  

```azurecli-interactive
az vm list-ip-addresses --resource-group myResourceGroupVM --name myVM --output table
```

### <a name="stop-virtual-machine"></a>Stoppa en virtuell dator

```azurecli-interactive
az vm stop --resource-group myResourceGroupVM --name myVM
```

### <a name="start-virtual-machine"></a>Starta en virtuell dator

```azurecli-interactive
az vm start --resource-group myResourceGroupVM --name myVM
```

### <a name="delete-resource-group"></a>Ta bort resursgrupp

Om en resursgrupp tas bort, tas även alla resurser som ingår i gruppen bort, som den virtuella datorn, virtuellt nätverk och disken. Parametern `--no-wait` återför kontrollen till kommandotolken utan att vänta på att uppgiften slutförs. Parametern `--yes` bekräftar att du vill ta bort resurserna utan att tillfrågas ytterligare en gång.

```azurecli-interactive
az group delete --name myResourceGroupVM --no-wait --yes
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig om grundläggande VM-skapande och hantering, till exempel att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator
> * Välja och använda VM-avbildningar
> * Visa och använda specifika VM-storlekar
> * Ändra storlek på en virtuell dator
> * Visa och förstå tillstånd för virtuella datorer

Gå vidare till nästa självstudie om du vill lära dig mer om diskar i virtuella dator.  

> [!div class="nextstepaction"]
> [Skapa och hantera virtuella dator diskar](./tutorial-manage-disks.md)
