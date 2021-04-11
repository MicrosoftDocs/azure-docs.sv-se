---
title: Underhålls kontroll för virtuella Azure-datorer med CLI
description: Lär dig hur du styr när underhåll tillämpas på dina virtuella Azure-datorer med underhålls kontroll och CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/20/2020
ms.author: cynthn
ms.openlocfilehash: 9425759de1e08bc83cac80cd1b56c602edb59fb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562970"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-cli"></a>Styra uppdateringar med underhålls kontroll och Azure CLI

Med underhålls kontrollen kan du bestämma när du ska tillämpa plattforms uppdateringar på värd infrastrukturen för de isolerade virtuella datorerna och Azure-dedikerade värdar. Det här avsnittet beskriver Azure CLI-alternativen för underhålls kontroll. Mer information om fördelarna med att använda underhålls kontroll, dess begränsningar och andra hanterings alternativ finns i [Hantera plattforms uppdateringar med underhålls kontroll](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Skapa en underhållskonfiguration

Använd `az maintenance configuration create` för att skapa en underhålls konfiguration. I det här exemplet skapas en underhålls konfiguration med namnet *unconfig* som är begränsad till värden. 

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

Kopiera konfigurations-ID: t från de utdata som ska användas senare.

Med `--maintenance-scope host` ser du till att underhålls konfigurationen används för att styra uppdateringar av värd infrastrukturen.

Om du försöker skapa en konfiguration med samma namn, men på en annan plats, får du ett fel meddelande. Konfigurations namn måste vara unika för din resurs grupp.

Du kan fråga efter tillgängliga underhålls konfigurationer med `az maintenance configuration list` .

```azurecli-interactive
az maintenance configuration list --query "[].{Name:name, ID:id}" -o table 
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>Skapa en underhålls konfiguration med schemalagt fönster
Du kan också deklarera ett schemalagt fönster när Azure ska tillämpa uppdateringarna på dina resurser. I det här exemplet skapas en underhålls konfiguration med namnet config med ett schemalagt fönster på 5 timmar den fjärde måndagen i varje månad. När du har skapat en schemalagd period behöver du inte längre använda uppdateringarna manuellt.

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
> Underhålls **tiden** måste vara *2 timmar* eller längre. **Upprepning** av underhåll måste anges till minst en gång i 35 dagar.

Upprepning av underhåll kan uttryckas som varje dag, varje vecka eller varje månad. Några exempel är:
- **dagligt** underhåll-Window-upprepa-var: "dag" **eller** "3Days"
- **veckovis**-underhåll-Window-upprepa – var: "3Weeks" **eller** "Week lördag, söndag"
- **månatligt** underhåll-Window-upprepa – var: "månad day23, day24" **eller** "månadens sista söndag" **eller** "månad fjärde måndagen"


## <a name="assign-the-configuration"></a>Tilldela konfigurationen

Använd `az maintenance assignment create` för att tilldela konfigurationen till den isolerade virtuella datorn eller den dedikerade Azure-värden.

### <a name="isolated-vm"></a>Isolerad virtuell dator

Tillämpa konfigurationen på en virtuell dator med hjälp av konfigurationens ID. Ange `--resource-type virtualMachines` och ange namnet på den virtuella datorn för `--resource-name` , och resurs gruppen för den virtuella datorn i `--resource-group` , och platsen för den virtuella datorn för `--location` . 

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

Om du vill tillämpa en konfiguration på en dedikerad värd måste du inkludera `--resource-type hosts` , `--resource-parent-name` med namnet på värd gruppen och `--resource-parent-type hostGroups` . 

-Parametern `--resource-id` är värdens ID. Du kan använda [AZ VM Host get-instance-View](/cli/azure/vm/host#az-vm-host-get-instance-view) för att hämta ID för den dedikerade värden.

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

## <a name="check-configuration"></a>Kontrol lera konfigurationen

Du kan kontrol lera att konfigurationen har tillämpats korrekt eller kontrol lera vilken konfiguration som tillämpas med hjälp av `az maintenance assignment list` .

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

Använd `az maintenance update list` för att se om det finns väntande uppdateringar. Uppdatera--prenumerationen är ID för den prenumeration som innehåller den virtuella datorn.

Om det inte finns några uppdateringar returnerar kommandot ett fel meddelande som kommer att innehålla texten: `Resource not found...StatusCode: 404` .

Om det finns uppdateringar returneras bara en av dem, även om det finns flera väntande uppdateringar. Data för den här uppdateringen kommer att returneras i ett objekt:

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

Används `az maintenance apply update` för att tillämpa väntande uppdateringar. Vid lyckad kommer det här kommandot returnera JSON som innehåller information om uppdateringen.

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

## <a name="check-the-status-of-applying-updates"></a>Kontrol lera status för att tillämpa uppdateringar 

Du kan kontrol lera förloppet för uppdateringarna med hjälp av `az maintenance applyupdate get` . 

Du kan använda `default` som uppdaterings namn för att se resultat för den senaste uppdateringen eller ersätta `myUpdateName` med namnet på den uppdatering som returnerades när du körde `az maintenance applyupdate create` .

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
LastUpdateTime kommer att vara den tid då uppdateringen slutfördes, antingen initierad av dig eller av plattformen i fallet själv underhålls period inte användes. Om en uppdatering aldrig har tillämpats via underhålls kontrollen visas standardvärdet.

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


## <a name="delete-a-maintenance-configuration"></a>Ta bort en underhålls konfiguration

Används `az maintenance configuration delete` för att ta bort en underhålls konfiguration. Om du tar bort konfigurationen tas underhålls kontrollen bort från de tillhör ande resurserna.

```azurecli-interactive
az maintenance configuration delete \
   --subscription 1111abcd-1a11-1a2b-1a12-123456789abc \
   -g myResourceGroup \
   --resource-name myConfig
```

## <a name="next-steps"></a>Nästa steg
Mer information finns i [underhåll och uppdateringar](maintenance-and-updates.md).
