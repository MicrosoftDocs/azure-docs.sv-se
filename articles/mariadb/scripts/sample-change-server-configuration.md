---
title: CLI-skript – ändra server parametrar – Azure Database for MariaDB
description: Det här exempel på CLI-skriptet visar en lista över alla tillgängliga serverkonfigurationer och uppdateringar av en Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurecli
ms.topic: sample
ms.custom: mvc, devx-track-azurecli
ms.date: 12/02/2019
ms.openlocfilehash: 41b298b60377b8f6b3ad667157f8915876a0baa8
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664655"
---
# <a name="list-and-update-configurations-of-an-azure-database-for-mariadb-server-using-azure-cli"></a>Visa och uppdatera konfigurationer av en Azure Database for MariaDB-server med hjälp av Azure CLI
Det här CLI-exempelskriptet visar alla tillgängliga konfigurationsparametrar samt tillåtna värden för Azure Database for MariaDB-servern och anger *innodb_lock_wait_timeout* till ett annat värde än standardvärdet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript
I det här exempelskriptet ändrar du de markerade raderna om du vill uppdatera administratörens användarnamn och lösenord till dina egna.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/change-server-configurations.sh?highlight=15-16 "List and update configurations of Azure Database for MariaDB.")]

## <a name="clean-up-deployment"></a>Rensa distribution
När skriptet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.
[!code-azurecli-interactive[main](../../../cli_scripts/mariadb/change-server-configurations/delete-mariadb.sh  "Delete the resource group.")]

## <a name="script-explanation"></a>Förklaring av skript
Det här skriptet använder de kommandon som beskrivs i följande tabell:

| **Kommando** | **Kommentarer** |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az mariadb server create](/cli/azure/mariadb/server#az-mariadb-server-create) | Skapar en MariaDB-server som är värd för databaserna. |
| [az mariadb server configuration list](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-list) | Visar en lista över konfigurationerna för en Azure Database for MariaDB-server. |
| [az mariadb server configuration set](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-set) | Uppdaterar konfigurationen för en Azure Database for MariaDB-server. |
| [az mariadb server configuration show](/cli/azure/mariadb/server/configuration#az-mariadb-server-configuration-show) | Visar konfigurationen för en Azure Database for MariaDB-server. |
| [az group delete](/cli/azure/group#az-group-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg
- Läs mer om Azure CLI: [Azure CLI-dokumentation](/cli/azure).
- Prova fler skript: [Azure CLI-exempel för Azure Database for MariaDB](../sample-scripts-azure-cli.md)
- Mer information om serverparametrar finns i [Konfigurera serverparametrar i Azure Database for MariaDB](../howto-server-parameters.md).
