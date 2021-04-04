---
title: 'Azure CLI: skapa en hanterad instans'
description: Exempel skript för Azure CLI för att skapa en hanterad instans i Azure SQL-hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/25/2019
ms.openlocfilehash: 54e1aa993f177a4d3bc255287ae8c2fb14cf65af
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497281"
---
# <a name="use-cli-to-create-an-azure-sql-managed-instance"></a>Använd CLI för att skapa en hanterad Azure SQL-instans

Det här skript exemplet för Azure CLI skapar en hanterad Azure SQL-instans i ett dedikerat undernät i ett nytt virtuellt nätverk. Den konfigurerar också en routningstabell och en nätverks säkerhets grupp för det virtuella nätverket. När skriptet har körts kan den hanterade instansen nås från det virtuella nätverket eller från en lokal miljö. Se [Konfigurera virtuell Azure-dator för att ansluta till en Azure SQL-hanterad instans]. /.. /Azure-SQL/Managed-instance/Connect-VM-instance-configure.MD) och [Konfigurera en punkt-till-plats-anslutning till en Azure SQL-hanterad instans från den lokala platsen](../../azure-sql/managed-instance/point-to-site-p2s-configure.md).

> [!IMPORTANT]
> För begränsningar, se [regioner som stöds](../../azure-sql/managed-instance/resource-limits.md#supported-regions) och [prenumerations typer som stöds](../../azure-sql/managed-instance/resource-limits.md#supported-subscription-types).

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../cli_scripts/sql-database/managed-instance/create-managed-instance.sh "Create managed instance")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Beskrivning |
|---|---|
| [AZ nätverkets VNet](/cli/azure/network/vnet) | Virtuella nätverks kommandon. |
| [AZ Network VNet Subnet](/cli/azure/network/vnet/subnet) | Under näts kommandon för virtuellt nätverk. |
| [AZ nätverks väg-tabell](/cli/azure/network/route-table) | Kommandon för nätverks väg tabell. |
| [AZ SQL mi](/cli/azure/sql/mi) | SQL-hanterade instans kommandon. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../../azure-sql/database/az-cli-script-samples-content-guide.md).
