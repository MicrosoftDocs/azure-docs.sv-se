---
title: CLI-skript – Återställ server – Azure Database for MySQL
description: Det här Azure CLI-exempelskriptet visar hur du återställer en Azure Database for MySQL-server och dess databaser till en tidigare tidpunkt.
author: savjani
ms.author: pariks
ms.service: mysql
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 2a2409c94e28135ca5c1fb8fa7210cd9b8b42858
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791700"
---
# <a name="restore-an-azure-database-for-mysql-server-using-azure-cli"></a>Återställa en Azure Database for MySQL-server med Azure CLI
Det här CLI-exempelskriptet återställer en enda Azure Database for MySQL-server till en tidigare tidpunkt.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna. Ersätt prenumerations-ID som används i `az monitor`-kommandon med ditt eget prenumerations-ID.
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/backup-restore.sh?highlight=15-16 "Restore Azure Database for MySQL.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den. 
[!code-azurecli-interactive[main](../../../cli_scripts/mysql/backup-restore-pitr/delete-mysql.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommandot** | **Kommentarer** |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mysql server create](/cli/azure/mysql/server#az_mysql_server_create) | Skapar en MySQL-server som är värd för databaserna. |
| [az mysql server restore](/cli/azure/mysql/server#az_mysql_server_restore) | Återställ en server från en säkerhetskopia. |
| [az group delete](/cli/azure/group#az_group_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure).
- Prova fler skript: [Azure CLI-exempel för Azure Database for MySQL](../sample-scripts-azure-cli.md)
