---
title: 'Azure CLI: Lägg till hanterad instans i redundans gruppen'
description: Lär dig hur du skapar två hanterade instanser, lägger till dem i en grupp för växling vid fel och sedan testar redundansväxlingen.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: MashaMSFT
ms.author: mathoma
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: afefc556bcac096d67051f3014c31e449dbbca32
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91323574"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance-to-a-failover-group"></a>Använd CLI för att skapa en hanterad Azure SQL-instans till en failover-grupp

Det här Azure CLI-exemplet skapar två hanterade instanser, lägger till dem i en grupp för redundans och testar sedan redundans från den primära hanterade instansen till den sekundära hanterade instansen.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-scripts"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-cli.sh "Add managed instance to a failover group")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den. Du måste ta bort resurs gruppen två gånger. Om du tar bort resurs gruppen första gången tas den hanterade instansen och virtuella kluster bort, men fel meddelandet kommer att Miss sen `az group delete : Long running operation failed with status 'Conflict'.` . Kör kommandot AZ Group Delete en andra gång för att ta bort eventuella kvarvarande resurser samt resurs gruppen.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Beskrivning |
|---|---|
| [AZ nätverkets VNet](/cli/azure/network/vnet) | Virtuella nätverks kommandon.  |
| [AZ Network VNet Subnet](/cli/azure/network/vnet/subnet) | Under näts kommandon för virtuellt nätverk. |
| [AZ Network NSG](/cli/azure/network/nsg) | Kommandon för nätverks säkerhets grupp. |
| [AZ Network NSG Rule](/cli/azure/network/nsg/rule)| Kommandon för nätverks säkerhets regel. |
| [AZ nätverks väg-tabell](/cli/azure/network/route-table) | Route Table-kommandon. |
| [AZ SQL mi](/cli/azure/sql/mi) | SQL-hanterade instans kommandon. |
| [AZ Network Public-IP](/cli/azure/network/public-ip) | Nätverks kommandon för offentlig IP-adress. |
| [AZ Network VNet-Gateway](/cli/azure/network/vnet-gateway) | Virtual Network Gateway-kommandon |
| [AZ SQL-instans – redundans-grupp](/cli/azure/sql/instance-failover-group) | SQL-hanterad instans redundans grupp kommandon. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../../azure-sql/database/az-cli-script-samples-content-guide.md).
