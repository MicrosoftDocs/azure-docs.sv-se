---
title: CLI-skript – återställa Server-Azure Database for MariaDB
description: Det här Azure CLI-exempelskriptet visar hur du återställer en Azure Database for MariaDB-server och dess databaser till en tidigare tidpunkt.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: a4fc645f43858e80eeffc6c1672f6bd4369e7290
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98664590"
---
# <a name="restore-an-azure-database-for-mariadb-server-using-azure-cli"></a>Återställa en Azure Database for MariaDB-server med hjälp av Azure CLI
Det här CLI-exempelskriptet återställer en enskild Azure Database for MariaDB-server till en tidigare tidpunkt.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna. Ersätt prenumerations-ID som används i `az monitor`-kommandon med ditt eget prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/backup-restore-pitr/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Kommentarer** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Skapar en MariaDB-server som är värd för databaserna. |
| [az mariadb server restore](/cli/azure/mariadb/server#az-mariadb-server-restore) | Återställ en server från en säkerhetskopia. |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure).
- Prova fler skript: [Azure CLI-exempel för Azure Database for MariaDB](../sample-scripts-azure-cli.md)
