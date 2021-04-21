---
title: 'Azure CLI: Övervaka och skala en enkel databas i Azure SQL Database'
description: Använd ett Azure CLI-exempelskript för att övervaka och skala en enkel databas i Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/25/2019
ms.openlocfilehash: bd982ab6dc66674e705f080511282bcfeb909872
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787187"
---
# <a name="use-the-azure-cli-to-monitor-and-scale-a-single-database-in-azure-sql-database"></a>Använd Azure CLI för att övervaka och skala en enkel databas i Azure SQL Database

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Det här Azure CLI-skriptexempel skalar en enkel databas i Azure SQL Database till en annan beräkningsstorlek efter att ha frågat storleksinformationen för databasen.

Om du väljer att installera och använda Azure CLI lokalt kräver den här artikeln att du kör Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.sh "Monitor and scale a database in Azure SQL Database")]

> [!TIP]
> Använd [az sql db op list för](/cli/azure/sql/db/op?#az_sql_db_op_list) att hämta en lista över åtgärder som utförs på databasen och använd az sql db op [cancel](/cli/azure/sql/db/op#az_sql_db_op_cancel) för att avbryta en uppdateringsåtgärd på databasen.

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempelreferens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Skript | Description |
|---|---|
| [az sql server](/cli/azure/sql/server) | Serverkommandon. |
| [az sql db show-usage](/cli/azure/sql#az_sql_show_usage) | Visar användningsinformation om storlek för en databas. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel finns i [Azure CLI-exempelskript.](../az-cli-script-samples-content-guide.md)
