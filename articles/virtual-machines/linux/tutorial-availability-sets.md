---
title: Distribuera virtuella datorer i en tillgänglighetsuppsättning med Hjälp av Azure CLI
description: I den här självstudien får du lära dig hur du använder Azure CLI för att distribuera virtuella datorer med hög tillgänglighet i tillgänglighetsuppsättningar
documentationcenter: ''
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.topic: how-to
ms.date: 3/8/2021
ms.author: mimckitt
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 7c45f08a339ca8878bb9e2840faa8a412f3e60e0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765979"
---
# <a name="create-and-deploy-virtual-machines-in-an-availability-set-using-azure-cli"></a>Skapa och distribuera virtuella datorer i en tillgänglighetsuppsättning med Azure CLI

I den här självstudien får du lära dig hur du ökar tillgängligheten och tillförlitligheten för dina VM-lösningar i Azure med en funktion som heter ”Tillgänglighetsuppsättningar”. Tillgänglighetsuppsättningarna ser till att de virtuella datorer som du distribuerar i Azure distribueras över flera isolerade maskinvarukluster. Detta innebär att endast en del av de virtuella datorerna påverkas om det skulle uppstå ett maskinvaru- eller programvarufel i Azure, och att din lösning fortfarande är tillgänglig och fungerar.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar

Den här självstudien använder CLI [i Azure Cloud Shell](../../cloud-shell/overview.md), som ständigt uppdateras till den senaste versionen. Om du vill Cloud Shell väljer **du Testa** längst upp i ett kodblock.

Om du väljer att installera och använda CLI lokalt krävs Azure CLI version 2.0.30 eller senare för att du ska kunna genomföra den här självstudiekursen. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="create-an-availability-set"></a>Skapa en tillgänglighetsuppsättning

Du kan skapa en tillgänglighetsuppsättning med [az vm availability-set create](/cli/azure/vm/availability-set). I det här exemplet är antalet uppdaterings- och feldomäner satt till *2* för tillgänglighetsuppsättningen med namnet *myAvailabilitySet* i resursgruppen *myResourceGroupAvailability*.

Börja med att skapa en resursgrupp med [az group create](/cli/azure/group#az_group_create) och skapa sedan tillgänglighetsuppsättningen:

```azurecli-interactive
az group create --name myResourceGroupAvailability --location eastus

az vm availability-set create \
    --resource-group myResourceGroupAvailability \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

Med tillgänglighetsuppsättningar kan du isolera resurser över feldomäner och uppdateringsdomäner. En **feldomän** representerar en isolerad resurssamling med server + nätverk + lagring. I föregående exempel fördelas tillgänglighetsuppsättningen över minst två feldomäner när de virtuella datorerna har distribuerats. Tillgänglighetsuppsättningen distribueras också mellan två **uppdateringsdomäner**. Med två uppdateringsdomäner kommer Azures programuppdateringar till VM-resurserna isoleras, vilket förhindrar att alla program som körs på den virtuella datorn uppdateras samtidigt.


## <a name="create-vms-inside-an-availability-set"></a>Skapa virtuella datorer i en tillgänglighetsuppsättning

De virtuella datorerna måste skapas i tillgänglighetsuppsättningen för att säkerställa att de distribueras i maskinvaran. En befintlig virtuell dator kan inte läggas till i en tillgänglighetsuppsättning när den har skapats.

När en virtuell dator skapas med [az vm create](/cli/azure/vm) använder du parametern `--availability-set` för att ange namnet på tillgänglighetsuppsättningen.

```azurecli-interactive
for i in `seq 1 2`; do
   az vm create \
     --resource-group myResourceGroupAvailability \
     --name myVM$i \
     --availability-set myAvailabilitySet \
     --size Standard_DS1_v2  \
     --vnet-name myVnet \
     --subnet mySubnet \
     --image UbuntuLTS \
     --admin-username azureuser \
     --generate-ssh-keys
done
```

Det finns nu två virtuella datorer i tillgänglighetsuppsättningen. Eftersom de finns i samma tillgänglighetsuppsättning säkerställer Azure att de virtuella datorerna och deras resurser (inklusive datadiskar) distribueras över isolerad fysisk maskinvara. Den här distributionen garanterar mycket högre tillgänglighet för den övergripande VM-lösningen.

Distributionen av tillgänglighetsuppsättningen kan visas i portalen genom att gå till Resursgrupper > myResourceGroupAvailability > myAvailabilitySet. Virtuella datorer distribueras via två fel- och uppdateringsdomäner, enligt följande exempel:

![Tillgänglighetsuppsättning i portalen](./media/tutorial-availability-sets/fd-ud.png)

## <a name="check-for-available-vm-sizes"></a>Kontrollera tillgängliga VM-storlekar

Ytterligare virtuella datorer kan läggas till i tillgänglighetsuppsättningen senare, där VM-storlekar är tillgängliga i maskinvaran. Använd [az vm availability-set list-sizes](/cli/azure/vm/availability-set#az_vm_availability_set_list_sizes) för att visa en lista över alla tillgängliga storlekar i maskinvaruklustret för tillgänglighetsuppsättningen:

```azurecli-interactive
az vm availability-set list-sizes \
     --resource-group myResourceGroupAvailability \
     --name myAvailabilitySet \
     --output table
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en tillgänglighetsuppsättning
> * Skapa en virtuell dator i en tillgänglighetsuppsättning
> * Kontrollera tillgängliga VM-storlekar

Gå vidare till nästa kurs vill veta mer om VM-skalningsuppsättningar.

> [!div class="nextstepaction"]
> [Skapa en VM-skalningsuppsättning](tutorial-create-vmss.md)

* Mer information om tillgänglighetszoner finns i [dokumentationen Tillgänglighetszoner .](../../availability-zones/az-overview.md)
* Mer dokumentation om både tillgänglighetsuppsättningar och tillgänglighetszoner finns [också här.](../availability.md)
* Om du vill prova tillgänglighetszoner går [du till Skapa en virtuell Linux-dator i en tillgänglighetszon med Azure CLI](./create-cli-availability-zone.md)