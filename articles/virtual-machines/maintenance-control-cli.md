---
title: Underhållskontroll för virtuella Azure-datorer med CLI
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med hjälp av underhållskontroll och CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: c57f66eca5d15024c6b10e8fad12ddb575b9f894
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107765907"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Kontrollera uppdateringar med underhållskontroll och Azure CLI

Med underhållskontroll kan du bestämma när du ska tillämpa plattformsuppdateringar på värdinfrastrukturen för dina isolerade virtuella datorer och dedikerade Azure-värdar. Det här avsnittet beskriver Azure CLI-alternativen för underhållskontroll. Mer information om fördelarna med att använda underhållskontroll, dess begränsningar och andra hanteringsalternativ finns i [Hantera plattformsuppdateringar med underhållskontroll.](maintenance-control.md)

## <a name="create-a-maintenance-configuration"></a>Skapa en underhållskonfiguration

Använd `az maintenance configuration create` för att skapa en underhållskonfiguration. I det här exemplet skapas en underhållskonfiguration med *namnet myConfig* som är begränsad till värden. 

```azurecli-interactive
az group create \
   --location eastus \
   --name myMaintenanceRG
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host\
   --location eastus
```

Kopiera konfigurations-ID:t från utdata för senare användning.

Med `--maintenance-scope host` ser du till att underhållskonfigurationen används för att kontrollera uppdateringar av värdinfrastrukturen.

Om du försöker skapa en konfiguration med samma namn, men på en annan plats, får du ett felmeddelande. Konfigurationsnamnen måste vara unika för resursgruppen.

Du kan fråga efter tillgängliga underhållskonfigurationer med hjälp av `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Skapa en underhållskonfiguration med schemalagd tid
Du kan också deklarera ett schemalagt fönster när Azure ska tillämpa uppdateringarna på dina resurser. Det här exemplet skapar en underhållskonfiguration med namnet myConfig med ett schemalagt fönster på 5 timmar den fjärde måndagen i varje månad. När du har skapat ett schemalagt fönster behöver du inte längre tillämpa uppdateringarna manuellt.

```azurecli-interactive
az maintenance configuration create \
   -g myMaintenanceRG \
   --resource-name myConfig \
   --maintenance-scope host \
   --location eastus \
   --maintenance-window-duration "05:00" \
   --maintenance-window-recur-every "Month Fourth Monday" \
   --maintenance-window-start-date-time "2020-12-30 08:00" \
   --maintenance-window-time-zone "Pacific Standard Time"
```

> [!IMPORTANT]
> **Underhållstiden** måste vara *2 timmar* eller längre. Upprepning **av** underhåll måste anges till minst en gång om 35 dagar.

Upprepning av underhåll kan uttryckas som varje dag, varje vecka eller varje månad. Några exempel är:
- **daily**– maintenance-window-recur-every: "Day" **eller** "3Days"
- **weekly**– maintenance-window-recur-every: "3Weeks" **eller** "Week Saturday,Sunday"
- **monthly**– maintenance-window-recur-every: "Month day23,day24" **eller** "Month Last Sunday" **eller** "Month Fourth Monday"


## <a name="assign-the-configuration"></a>Tilldela konfigurationen

Använd `az maintenance assignment create` för att tilldela konfigurationen till den isolerade virtuella datorn eller Azure Dedicated Host.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Tillämpa konfigurationen på en virtuell dator med hjälp av ID:t för konfigurationen. Ange och ange namnet på den virtuella datorn för , och resursgruppen för till den virtuella datorn i `--resource-type virtualMachines` och platsen för den virtuella datorn för `--resource-name` `--resource-group` `--location` . 

```azurecli-interactive
az maintenance assignment create \
   --resource-group myMaintenanceRG \
   --location eastus \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myMaintenanceRG/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig"
```

### <a name="dedicated-host"></a>Dedikerad värd

Om du vill tillämpa en konfiguration på en dedikerad värd måste du `--resource-type hosts` inkludera , med namnet på `--resource-parent-name` värdgruppen och `--resource-parent-type hostGroups` . 

Parametern `--resource-id` är värdens ID. Du kan använda [az vm host get-instance-view för](/cli/azure/vm/host#az_vm_host_get_instance_view) att hämta ID:t för din dedikerade värd.

```azurecli-interactive
az maintenance assignment create \
   -g myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --configuration-assignment-name myConfig \
   --maintenance-configuration-id "/subscriptions/1111abcd-1a11-1a2b-1a12-123456789abc/resourcegroups/myDhResourceGroup/providers/Microsoft.Maintenance/maintenanceConfigurations/myConfig" \
   -l eastus \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
```

## <a name="check-configuration"></a>Kontrollera konfigurationen

Du kan kontrollera att konfigurationen har tillämpats korrekt eller kontrollera vilken konfiguration som används för närvarande med hjälp av `az maintenance assignment list` .

### <a name="isolated-vm"></a>Isolerad virtuell dator

```azurecli-interactive
az maintenance assignment list \
   --provider-name Microsoft.Compute \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --query "[].{resource:resourceGroup, configName:name}" \
   --output table
```

### <a name="dedicated-host"></a>Dedikerad värd 

```azurecli-interactive
az maintenance assignment list \
   --resource-group myDHResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups 
   --query "[].{ResourceGroup:resourceGroup,configName:name}" \
   -o table
```


## <a name="check-for-pending-updates"></a>Sök efter väntande uppdateringar

Använd `az maintenance update list` för att se om det finns väntande uppdateringar. Uppdatera – prenumerationen ska vara ID:t för prenumerationen som innehåller den virtuella datorn.

Om det inte finns några uppdateringar returnerar kommandot ett felmeddelande som innehåller texten: `Resource not found...StatusCode: 404` .

Om det finns uppdateringar returneras bara en, även om det finns flera väntande uppdateringar. Data för den här uppdateringen returneras i ett -objekt:

```text
[
  {
    "impactDurationInSec": 9,
    "impactType": "Freeze",
    "maintenanceScope": "Host",
    "notBefore": "2020-03-03T07:23:04.905538+00:00",
    "resourceId": "/subscriptions/9120c5ff-e78e-4bd0-b29f-75c19cadd078/resourcegroups/DemoRG/providers/Microsoft.Compute/hostGroups/demoHostGroup/hosts/myHost",
    "status": "Pending"
  }
]
  ```

### <a name="isolated-vm"></a>Isolerad virtuell dator

Sök efter väntande uppdateringar för en isolerad virtuell dator. I det här exemplet formateras utdata som en tabell för läsbarhet.

```azurecli-interactive
az maintenance update list \
   -g myMaintenanceRg \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   -o table
```

### <a name="dedicated-host"></a>Dedikerad värd

För att söka efter väntande uppdateringar för en dedikerad värd. I det här exemplet formateras utdata som en tabell för läsbarhet. Ersätt värdena för resurserna med dina egna.

```azurecli-interactive
az maintenance update list \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parentname myHostGroup \
   --resource-parent-type hostGroups \
   -o table
```

## <a name="apply-updates"></a>Tillämpa uppdateringar

Använd `az maintenance apply update` för att tillämpa väntande uppdateringar. Om det lyckas returnerar det här kommandot JSON som innehåller information om uppdateringen.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Skapa en begäran om att tillämpa uppdateringar på en isolerad virtuell dator.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute
```


### <a name="dedicated-host"></a>Dedikerad värd

Tillämpa uppdateringar på en dedikerad värd.

```azurecli-interactive
az maintenance applyupdate create \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   --resource-group myHostResourceGroup \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \
   --resource-parent-type hostGroups
```

## <a name="check-the-status-of-applying-updates"></a>Kontrollera statusen för att tillämpa uppdateringar 

Du kan kontrollera förloppet för uppdateringarna med hjälp av `az maintenance applyupdate get` . 

Du kan använda som uppdateringsnamn för att visa resultat för den senaste uppdateringen eller ersätta med namnet på uppdateringen som returnerades `default` `myUpdateName` när du körde `az maintenance applyupdate create` .

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime är den tid då uppdateringen slutfördes, antingen initierad av dig eller av plattformen om självunderhållsfönstret inte användes. Om en uppdatering aldrig har tillämpats via underhållskontroll visas standardvärdet.

### <a name="isolated-vm"></a>Isolerad virtuell dator

```azurecli-interactive
az maintenance applyupdate get \
   --resource-group myMaintenanceRG \
   --resource-name myVM \
   --resource-type virtualMachines \
   --provider-name Microsoft.Compute \
   --apply-update-name default 
```

### <a name="dedicated-host"></a>Dedikerad värd

```azurecli-interactive
az maintenance applyupdate get \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \ 
   --resource-group myMaintenanceRG \
   --resource-name myHost \
   --resource-type hosts \
   --provider-name Microsoft.Compute \
   --resource-parent-name myHostGroup \ 
   --resource-parent-type hostGroups \
   --apply-update-name myUpdateName \
   --query "{LastUpdate:lastUpdateTime, Name:name, ResourceGroup:resourceGroup, Status:status}" \
   --output table
```


## <a name="delete-a-maintenance-configuration"></a>Ta bort en underhållskonfiguration

Använd `az maintenance configuration delete` för att ta bort en underhållskonfiguration. Om du tar bort konfigurationen tas underhållskontroll bort från de associerade resurserna.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Underhåll och uppdateringar.](maintenance-and-updates.md)
