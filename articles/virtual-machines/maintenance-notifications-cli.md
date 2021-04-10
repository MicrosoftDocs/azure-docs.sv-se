---
title: Få underhålls meddelanden med CLI
description: Visa underhålls meddelanden för virtuella datorer som körs i Azure och starta självbetjänings underhållet med hjälp av Azure CLI.
author: shants123
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: cd042ce09533cbefe37cb2e4d311a3857e3dfdec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102552413"
---
# <a name="handling-planned-maintenance-notifications-using-the-azure-cli"></a>Hantering av planerade underhålls aviseringar med hjälp av Azure CLI

**Den här artikeln gäller för virtuella datorer som kör både Linux och Windows.**

Du kan använda CLI för att se när virtuella datorer är schemalagda för [Underhåll](maintenance-notifications.md). Information om planerat underhåll är tillgänglig från [AZ VM get-instance-View](/cli/azure/vm#az-vm-get-instance-view).
 
Underhålls information returneras endast om underhållet är planerat. 

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

Följande anrop startar underhåll på en virtuell dator om `IsCustomerInitiatedMaintenanceAllowed` är inställt på sant.

```azurecli-interactive
az vm perform-maintenance -g myResourceGroup -n myVM 
```

## <a name="classic-deployments"></a>Klassiska distributioner

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Om du fortfarande har äldre virtuella datorer som har distribuerats med den klassiska distributions modellen kan du använda den klassiska Azure CLI-funktionen för att fråga efter virtuella datorer och starta underhåll.

Kontrol lera att du har rätt läge för att arbeta med den klassiska virtuella datorn genom att skriva:

```
azure config mode asm
```

Om du vill hämta underhålls statusen för en virtuell dator med namnet *myVM*, skriver du:

```
azure vm show myVM 
``` 

För att starta underhåll på den klassiska virtuella datorn med namnet *myVM* i tjänsten för *fjärrdistribution* och *distribution av* distribution skriver du:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```

## <a name="next-steps"></a>Nästa steg

Du kan också hantera planerat underhåll med hjälp av [Azure PowerShell](maintenance-notifications-powershell.md) eller [portalen](maintenance-notifications-portal.md).
