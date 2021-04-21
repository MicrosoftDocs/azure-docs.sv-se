---
title: 'Azure CLI: Skapa en enkel databas'
description: Använd det här Azure CLI-exempelskriptet för att skapa en enkel databas.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: 00346accbccd67f542a8496ac097b0236c56a08c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773827"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Använda Azure CLI för att skapa en enkel databas och konfigurera en brandväggsregel

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Det här Azure CLI-skriptexempel skapar en enkel databas Azure SQL Database och konfigurerar en brandväggsregel på servernivå. När skriptet har körts kan databasen nås från alla Azure-tjänster och den konfigurerade IP-adressen.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempelreferens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Beskrivning |
|---|---|
| [az sql server](/cli/azure/sql/server#az_sql_server_create) | Serverkommandon |
| [az sql server firewall](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create) | Serverbrandväggskommandon. |
| [az sql db](/cli/azure/sql/db#az_sql_db_create) | Databaskommandon. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../az-cli-script-samples-content-guide.md).
