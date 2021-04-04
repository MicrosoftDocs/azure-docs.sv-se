---
title: 'Azure CLI: skapa en enskild databas'
description: Använd det här Azure CLI-exempel skriptet för att skapa en enskild databas.
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
ms.openlocfilehash: dc17ab55cb473036d8c40bd1e533bcc20c4c6259
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92742251"
---
# <a name="use-the-azure-cli-to-create-a-single-database-and-configure-a-firewall-rule"></a>Använd Azure CLI för att skapa en enskild databas och konfigurera en brand Väggs regel

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

Det här skript exemplet för Azure CLI skapar en enda databas i Azure SQL Database och konfigurerar en brand Väggs regel på server nivå. När skriptet har körts kan databasen nås från alla Azure-tjänster och den konfigurerade IP-adressen.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

### <a name="run-the-script"></a>Kör skriptet

[!code-azurecli-interactive[main](../../../../cli_scripts/sql-database/create-and-configure-database/create-and-configure-database.sh "Create SQL Database")]

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Beskrivning |
|---|---|
| [az sql server](/cli/azure/sql/server#az-sql-server-create) | Server kommandon |
| [AZ SQL Server-brandvägg](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create) | Server brand Väggs kommandon. |
| [AZ SQL DB](/cli/azure/sql/db#az-sql-db-create) | Databas kommandon. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../az-cli-script-samples-content-guide.md).
