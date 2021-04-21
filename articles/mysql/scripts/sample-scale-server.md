---
title: CLI-skript – Skalningsserver – Azure Database for MySQL
description: Det här CLI-exempelskriptet skalar Azure Database for MySQL-servern till en annan prestandanivå när du har kört frågor mot måtten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: e7dea1cb99ea8ce941b16739bdbd258843877e89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791669"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Övervaka och skala en Azure Database for MySQL-server med Azure CLI
Det här CLI-exempelskriptet skalar beräkning och lagring för en Azure Database for MySQL server när du har frågat måtten. Beräkning kan skalas upp eller ned. Lagring kan bara skalas upp.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript
Uppdatera skriptet med ditt prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommandot** | **Kommentarer** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Skapar en MySQL-server som är värd för databaserna. |
| [az mysql server update](/cli/azure/mysql/server#az_mysql_server_update) | Uppdaterar egenskaperna för MySQL-servern. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) | Lista mätvärdet för resurserna. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Database for MySQL beräkning och lagring](../concepts-pricing-tiers.md)
- Prova fler skript: [Azure CLI-exempel för Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Läs mer om [Azure CLI](/cli/azure)
