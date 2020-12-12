---
title: Distribuera virtuella datorer och skalnings uppsättnings instanser till dedikerade värdar med CLI
description: Distribuera virtuella datorer och skalnings uppsättnings instanser till dedikerade värdar med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: ef0c8d53d885f11acdcf578db155de3d7848887e
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/12/2020
ms.locfileid: "97360075"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Distribuera till dedikerade värdar med hjälp av Azure CLI
 

Den här artikeln vägleder dig genom hur du skapar en dedikerad Azure- [värd](../dedicated-hosts.md) som värd för dina virtuella datorer. 

Kontrol lera att du har installerat Azure CLI version 2.16.0 eller senare och loggat in på ett Azure-konto med hjälp av `az login` . 


## <a name="limitations"></a>Begränsningar

- De storlekar och maskin varu typer som är tillgängliga för dedikerade värdar varierar beroende på region. Mer information hittar du på [prissättnings sidan](https://aka.ms/ADHPricing) för värden.

## <a name="create-resource-group"></a>Skapa resursgrupp 
En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Skapa resurs gruppen med AZ Group Create. I följande exempel skapas en resurs grupp med namnet *myDHResourceGroup* på platsen *USA, östra* .

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Lista tillgängliga värd-SKU: er i en region

Alla värd-SKU: er är inte tillgängliga i alla regioner och tillgänglighets zoner. 

Visa lista över värd tillgänglighet och eventuella begränsningar innan du börjar etablering av dedikerade värdar. 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Skapa en värd grupp 

En **värd grupp** är en resurs som representerar en samling dedikerade värdar. Du skapar en värd grupp i en region och en tillgänglighets zon och lägger till värdar i den. När du planerar för hög tillgänglighet finns det ytterligare alternativ. Du kan använda ett eller båda av följande alternativ med dina dedikerade värdar: 
- Sträck över flera tillgänglighets zoner. I så fall måste du ha en värd grupp i var och en av de zoner som du vill använda.
- Sträck över flera fel domäner som är mappade till fysiska rack. 
 
I båda fallen måste du ange antalet fel domäner för värd gruppen. Om du inte vill spänna över fel domäner i gruppen använder du antalet fel domäner 1. 

Du kan också välja att använda både tillgänglighets zoner och fel domäner. 


I det här exemplet ska vi använda [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) för att skapa en värd grupp med hjälp av både tillgänglighets zoner och fel domäner. 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

Lägg till `--automatic-placement true` parametern om du vill att de virtuella datorerna och skalnings uppsättnings instanserna automatiskt ska placeras på värdar i en värd grupp. För ytterligare information, se [manuell respektive automatisk placering ](../dedicated-hosts.md#manual-vs-automatic-placement).


### <a name="other-examples"></a>Övriga exempel

Du kan också använda [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) för att skapa en värd grupp i tillgänglighets zon 1 (och inga fel domäner).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
Följande använder [AZ VM Host Group Create](/cli/azure/vm/host/group#az-vm-host-group-create) för att skapa en värd grupp genom att endast använda fel domäner (som ska användas i regioner där tillgänglighets zoner inte stöds). 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Skapa en värd 

Nu ska vi skapa en dedikerad värd i värd gruppen. Förutom ett namn för värden måste du ange SKU för värden. Värd-SKU: n samlar in de VM-serier som stöds samt maskin varu generering för den dedikerade värden.  

Mer information om värd-SKU: er och priser finns i [prissättning för Azure-dedikerad värd](https://aka.ms/ADHPricing).

Använd [AZ VM Host Create](/cli/azure/vm/host#az-vm-host-create) för att skapa en värd. Om du anger ett fel domän antal för värd gruppen uppmanas du att ange fel domänen för värden.  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator 
Skapa en virtuell dator på en dedikerad värd med [AZ VM Create](/cli/azure/vm#az-vm-create). Om du har angett en tillgänglighets zon när du skapar värd gruppen måste du använda samma zon när du skapar den virtuella datorn.

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

Om du vill placera den virtuella datorn på en särskild värd använder du `--host` i stället för att ange värd gruppen med `--host-group` .
 
> [!WARNING]
> Om du skapar en virtuell dator på en värd som inte har tillräckligt med resurser, kommer den virtuella datorn att skapas i ett felaktigt tillstånd. 

## <a name="create-a-scale-set"></a>Skapa en skalningsuppsättning 

När du distribuerar en skalnings uppsättning anger du värd gruppen.

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

Om du manuellt vill välja vilken värd som ska användas för att distribuera skalnings uppsättningen till lägger du till `--host` och namnet på värden.


## <a name="check-the-status-of-the-host"></a>Kontrol lera status för värden

Du kan kontrol lera värdets hälso status och hur många virtuella datorer du kan distribuera till värden med [AZ VM Host get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view).

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 Resultatet kommer att se ut ungefär så här:
 
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
Du kan exportera en mall om du nu vill skapa en ytterligare utvecklings miljö med samma parametrar, eller en produktions miljö som matchar den. Resource Manager använder JSON-mallar som definierar alla parametrar för din miljö. Du skapar hela miljöer genom att referera till den här JSON-mallen. Du kan skapa JSON-mallar manuellt eller exportera en befintlig miljö för att skapa en JSON-mall åt dig. Använd [AZ Group export](/cli/azure/group#az-group-export) för att exportera din resurs grupp.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Det här kommandot skapar `myDHResourceGroup.json` filen i din aktuella arbets katalog. När du skapar en miljö från den här mallen uppmanas du att ange alla resurs namn. Du kan fylla i dessa namn i mallfilen genom att lägga till `--include-parameter-default-value` parametern i `az group export` kommandot. Redigera din JSON-mall för att ange resurs namnen eller skapa en parameters.jspå en fil som anger resurs namnen.
 
Om du vill skapa en miljö från mallen använder du [AZ Group Deployment Create](/cli/azure/group/deployment#az-group-deployment-create).

```azurecli-interactive
az group deployment create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>Rensa 

Du debiteras för dina dedikerade värdar även om inga virtuella datorer distribueras. Du bör ta bort alla värdar som du för närvarande inte använder för att spara kostnader.  

Du kan bara ta bort en värd när det inte finns några längre virtuella datorer som använder den. Ta bort de virtuella [datorerna med AZ VM Delete](/cli/azure/vm#az-vm-delete).

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

När du har tagit bort de virtuella datorerna kan du ta bort värden med [AZ VM Host Delete](/cli/azure/vm/host#az-vm-host-delete).

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
När du har tagit bort alla värdar kan du ta bort värd gruppen med [AZ VM Host Group Delete](/cli/azure/vm/host/group#az-vm-host-group-delete).  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
Du kan också ta bort hela resurs gruppen i ett enda kommando. Detta tar bort alla resurser som skapats i gruppen, inklusive alla virtuella datorer, värdar och värd grupper.
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Nästa steg

- Mer information finns i Översikt över [dedikerade värdar](../dedicated-hosts.md) .

- Du kan också skapa dedikerade värdar med hjälp av [Azure Portal](../dedicated-hosts-portal.md).

- Det finns en exempel mall som du hittar [här](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), som använder både zoner och fel domäner för maximal återhämtning i en region.
