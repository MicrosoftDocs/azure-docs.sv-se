---
title: CLI-skript – skala Server-Azure Database for MySQL
description: Det här CLI-exempelskriptet skalar Azure Database for MySQL-servern till en annan prestandanivå när du har kört frågor mot måtten.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 31cf6d1f3bfcc44887ec939cb2f27df77f12cea3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94539268"
---
# <a name="monitor-and-scale-an-azure-database-for-mysql-server-using-azure-cli"></a>Övervaka och skala en Azure Database for MySQL-server med Azure CLI
Det här CLI-skriptet skalar beräkning och lagring för en enskild Azure Database for MySQL server efter att ha frågat måtten. Compute kan skala upp eller ned. Lagringen kan bara skalas upp.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript
Uppdatera skriptet med ditt prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/scale-mysql-server.sh "Create and scale Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/scale-mysql-server/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Kommentarer** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mysql server create](/cli/azure/mysql/server#az-mysql-server-create) | Skapar en MySQL-server som är värd för databaserna. |
| [AZ MySQL Server Update](/cli/azure/mysql/server#az-mysql-server-update) | Uppdaterar egenskaperna för MySQL-servern. |
| [az monitor metrics list](/cli/azure/monitor/metrics#az-monitor-metrics-list) | Lista mätvärdet för resurserna. |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om [Azure Database for MySQL beräkning och lagring](../concepts-pricing-tiers.md)
- Prova fler skript: [Azure CLI-exempel för Azure Database for MySQL](../sample-scripts-azure-cli.md)
- Läs mer om [Azure CLI](/cli/azure)
