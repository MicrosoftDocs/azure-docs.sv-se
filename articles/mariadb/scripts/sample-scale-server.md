---
title: CLI-skript – Skalningsserver – Azure Database for MariaDB
description: Det här CLI-exempelskriptet skalar Azure Database for MariaDB-servern till en annan prestandanivå efter frågekörning mot måtten.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 3a099f1a3ab16c33baa8037e727d26f107a593a2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785556"
---
# <a name="monitor-and-scale-an-azure-database-for-mariadb-server-using-azure-cli"></a>Övervaka och skala en Azure Database for MariaDB-server med Azure CLI
Det här CLI-exempelskriptet skalar beräkning och lagring för en Azure Database for MariaDB server när du har frågat måtten. Beräkning kan skalas upp eller ned. Lagring kan bara skalas upp.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript
Uppdatera skriptet med ditt prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/scale-mariadb-server.sh "Create and scale Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/scale-mariadb-server/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommandot** | **Kommentarer** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mariadb server create](/cli/azure/mariadb/server#az_mariadb_server_create) | Skapar en MariaDB-server som är värd för databaserna. |
| [az mariadb server update](/cli/azure/mariadb/server#az_mariadb_server_update) | Uppdaterar egenskaperna för MariaDB-servern. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Lista mätvärdet för resurserna. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Database for MariaDB beräkning och lagring](../concepts-pricing-tiers.md)
- Prova fler skript: [Azure CLI-exempel för Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Läs mer om [Azure CLI](/cli/azure)
