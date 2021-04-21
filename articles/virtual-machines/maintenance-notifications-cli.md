---
title: Få underhållsaviseringar med cli
description: Visa underhållsmeddelanden för virtuella datorer som körs i Azure och starta självbetjäning med hjälp av Azure CLI.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: d8a9b7ec6425a3cd32b597c3f14f8227fde67064
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777877"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Hantera meddelanden om planerat underhåll med hjälp av Azure CLI

**Den här artikeln gäller virtuella datorer som kör både Linux och Windows.**

Du kan använda CLI för att se när virtuella datorer är schemalagda för [underhåll.](maintenance-notifications.md) Information om planerat underhåll finns i [az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view).
 
Underhållsinformation returneras endast om det finns planerat underhåll. 

```azurecli-interactive
az vm get-instance-view -n myVM -g myResourceGroup --query instanceView.maintenanceRedeployStatus
```

Utdata
```
      "maintenanceRedeployStatus": {
      "additionalProperties": {},
      "isCustomerInitiatedMaintenanceAllowed": true,
      "lastOperationMessage": null,
      "lastOperationResultCode": "None",
      "maintenanceWindowEndTime": "2018-06-04T16:30:00+00:00",
      "maintenanceWindowStartTime": "2018-05-21T16:30:00+00:00",
      "preMaintenanceWindowEndTime": "2018-05-19T12:30:00+00:00",
      "preMaintenanceWindowStartTime": "2018-05-14T12:30:00+00:00"
```

## <a name="start-maintenance"></a>Starta underhåll

Följande anrop startar underhåll på en virtuell dator om `IsCustomerInitiatedMaintenanceAllowed` har angetts till true.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klassiska distributioner

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Om du fortfarande har äldre virtuella datorer som har distribuerats med den klassiska distributionsmodellen kan du använda klassiska Azure CLI för att fråga efter virtuella datorer och starta underhåll.

Kontrollera att du är i rätt läge för att arbeta med klassisk virtuell dator genom att skriva:

```
azure config mode asm
```

Om du vill hämta underhållsstatus för en virtuell dator med *namnet myVM* skriver du:

```
azure vm show myVM 
``` 

Om du vill starta underhåll på den klassiska virtuella datorn med namnet *myVM* i *tjänsten myService* och *myDeployment-distributionen* skriver du:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Nästa steg

Du kan också hantera planerat underhåll med hjälp [Azure PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md).
