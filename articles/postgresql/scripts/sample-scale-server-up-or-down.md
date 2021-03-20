---
title: Azure CLI-skript – skala och övervaka Azure Database for PostgreSQL
description: Azure CLI-skriptexempel – Skala Azure Database for PostgreSQL-servern till en annan prestandanivå när du har kört frågor mot måtten.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.devlang: azurecli
ms.custom: mvc, devx-track-azurecli
ms.topic: sample
ms.date: 08/07/2019
ms.openlocfilehash: 6bbf5f3a0a7d32425f80687de10444ee0819b9df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94660465"
---
# <a name="monitor-and-scale-a-single-postgresql-server-using-azure-cli"></a>Övervaka och skala en enskild PostgreSQL-server med Azure CLI
Det här CLI-skriptet skalar beräkning och lagring för en enskild Azure Database for PostgreSQL Server efter att ha frågat måtten. Compute kan skala upp eller ned. Lagringen kan bara skalas upp. 

> [!IMPORTANT] 
> Lagringen kan bara skalas upp, inte nedåt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript
Uppdatera skriptet med ditt prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/scale-postgresql-server.sh "Create and scale Azure Database for PostgreSQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/postgresql/scale-postgresql-server/delete-postgresql.sh "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Kommentarer** |
|---|---|
| [az group create](/cli/azure/group) | Skapar en resursgrupp där alla resurser lagras. |
| [az postgres server create](/cli/azure/postgres/server#az-postgres-server-create) | Skapar en PostgreSQL-server som är värd för databaserna. |
| [AZ postgres Server Update](/cli/azure/postgres/server#az-postgres-server-update) | Uppdaterar egenskaperna för PostgreSQL-servern. |
| [az monitor metrics list](/cli/azure/monitor/metrics) | Lista mätvärdet för resurserna. |
| [az group delete](/cli/azure/group) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Database for PostgreSQL beräkning och lagring](../concepts-pricing-tiers.md)
- Prova ytterligare skript: [Azure CLI-exempel för Azure Database for PostgreSQL](../sample-scripts-azure-cli.md)
- Läs mer om [Azure CLI](/cli/azure)
