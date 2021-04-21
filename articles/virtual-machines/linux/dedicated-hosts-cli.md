---
title: Distribuera virtuella datorer och skalningsuppsättningsinstanser till dedikerade värdar med hjälp av CLI
description: Distribuera virtuella datorer och skalningsuppsättningsinstanser till dedikerade värdar med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: adc09bf2572be563ff52cf9fa3d0dea51263d032
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774421"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Distribuera till dedikerade värdar med hjälp av Azure CLI
 

Den här artikeln vägleder dig genom hur du skapar en dedikerad [Azure-värd](../dedicated-hosts.md) som värd för dina virtuella datorer (VM). 

Kontrollera att du har installerat Azure CLI version 2.16.0 eller senare och loggat in på ett Azure-konto med `az login` . 


## <a name="limitations"></a>Begränsningar

- Vilka storlekar och maskinvarutyper som är tillgängliga för dedikerade värdar varierar beroende på region. Mer information finns [på sidan med](https://aka.ms/ADHPricing) värdpriser.

## <a name="create-resource-group"></a>Skapa resursgrupp 
En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa resursgruppen med az group create. I följande exempel skapas en resursgrupp med namnet *myDHResourceGroup* på platsen *USA,* östra.

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Lista tillgängliga värd-SKU:er i en region

Alla värd-SKU:er är inte tillgängliga i alla regioner och tillgänglighetszoner. 

Lista värdtillgänglighet och eventuella erbjudandebegränsningar innan du börjar etablera dedikerade värdar. 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Skapa en värdgrupp 

En **värdgrupp är** en resurs som representerar en samling dedikerade värdar. Du skapar en värdgrupp i en region och en tillgänglighetszon och lägger till värdar i den. När du planerar för hög tillgänglighet finns det ytterligare alternativ. Du kan använda ett eller båda av följande alternativ med dina dedikerade värdar: 
- Sträcker sig över flera tillgänglighetszoner. I det här fallet måste du ha en värdgrupp i var och en av de zoner som du vill använda.
- Sträcker sig över flera feldomäner som är mappade till fysiska rack. 
 
I båda fallen måste du ange antalet feldomäner för din värdgrupp. Om du inte vill sträcka dig över feldomäner i gruppen använder du feldomänantalet 1. 

Du kan också välja att använda både tillgänglighetszoner och feldomäner. 


I det här exemplet använder vi [az vm host group create för att skapa](/cli/azure/vm/host/group#az_vm_host_group_create) en värdgrupp med hjälp av både tillgänglighetszoner och feldomäner. 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

Lägg till `--automatic-placement true` parametern så att dina virtuella datorer och skalningsuppsättningsinstanser placeras automatiskt på värdar i en värdgrupp. Mer information finns i [Manuell eller automatisk placering. ](../dedicated-hosts.md#manual-vs-automatic-placement)


### <a name="other-examples"></a>Övriga exempel

Du kan också använda [az vm host group create för](/cli/azure/vm/host/group#az_vm_host_group_create) att skapa en värdgrupp i tillgänglighetszon 1 (och inga feldomäner).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Följande använder [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) för att skapa en värdgrupp med hjälp av endast feldomäner (som ska användas i regioner där tillgänglighetszoner inte stöds). 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Skapa en värd 

Nu ska vi skapa en dedikerad värd i värdgruppen. Förutom ett namn på värden måste du ange SKU:n för värden. Värd-SKU samlar in de VM-serier som stöds samt maskinvarugenereringen för din dedikerade värd.  

Mer information om värd-SKU:er och priser finns [i Azure Dedicated Host priser.](https://aka.ms/ADHPricing)

Använd [az vm host create för](/cli/azure/vm/host#az_vm_host_create) att skapa en värd. Om du anger ett antal feldomäner för din värdgrupp uppmanas du att ange feldomänen för värden.  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator 
Skapa en virtuell dator i en dedikerad värd med [az vm create](/cli/azure/vm#az_vm_create). Om du angav en tillgänglighetszon när du skapade värdgruppen måste du använda samma zon när du skapar den virtuella datorn.

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Om du vill placera den virtuella datorn på en specifik värd `--host` använder du i stället för att ange värdgruppen med `--host-group` .
 
> [!WARNING]
> Om du skapar en virtuell dator på en värd som inte har tillräckligt med resurser skapas den virtuella datorn i tillståndet MISSLYCKAD. 

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning 

När du distribuerar en skalningsuppsättning anger du värdgruppen.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Om du manuellt vill välja vilken värd som skalningsuppsättningen ska distribueras till lägger `--host` du till och namnet på värden.


## <a name="check-the-status-of-the-host"></a>Kontrollera status för värden

Du kan kontrollera värdhälsostatusen och hur många virtuella datorer som du fortfarande kan distribuera till värden med [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view).

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Utdata ser ut ungefär så här:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exportera som en mall 
Du kan exportera en mall om du nu vill skapa ytterligare en utvecklingsmiljö med samma parametrar eller en produktionsmiljö som matchar den. Resource Manager använder JSON-mallar som definierar alla parametrar för din miljö. Du skapar hela miljöer genom att referera till den här JSON-mallen. Du kan skapa JSON-mallar manuellt eller exportera en befintlig miljö för att skapa JSON-mallen åt dig. Använd [az group export](/cli/azure/group#az_group_export) för att exportera resursgruppen.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Det här kommandot skapar `myDHResourceGroup.json` filen i din aktuella arbetskatalog. När du skapar en miljö från den här mallen uppmanas du att ange alla resursnamn. Du kan fylla i dessa namn i mallfilen genom att lägga `--include-parameter-default-value` till parametern i `az group export` kommandot . Redigera JSON-mallen för att ange resursnamnen eller skapa parameters.jspå fil som anger resursnamnen.
 
Om du vill skapa en miljö från mallen använder du [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create).

```azurecli-interactive
az deployment group create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Rensa 

Du debiteras för dina dedikerade värdar även när inga virtuella datorer distribueras. Du bör ta bort alla värdar som du för närvarande inte använder för att spara kostnader.  

Du kan bara ta bort en värd när det inte längre finns några virtuella datorer som använder den. Ta bort de virtuella datorerna med [az vm delete](/cli/azure/vm#az_vm_delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

När du har tagit bort de virtuella datorerna kan du ta bort värden med [az vm host delete](/cli/azure/vm/host#az_vm_host_delete).

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
När du har tagit bort alla dina värdar kan du ta bort värdgruppen med az [vm host group delete](/cli/azure/vm/host/group#az_vm_host_group_delete).  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Du kan också ta bort hela resursgruppen i ett enda kommando. Detta tar bort alla resurser som skapats i gruppen, inklusive alla virtuella datorer, värdar och värdgrupper.
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Nästa steg

- Mer information finns i Översikt över [dedikerade värdar.](../dedicated-hosts.md)

- Du kan också skapa dedikerade värdar med hjälp [av Azure Portal](../dedicated-hosts-portal.md).

- Det finns en exempelmall, som [finns](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)här , som använder både zoner och feldomäner för maximal återhämtning i en region.
