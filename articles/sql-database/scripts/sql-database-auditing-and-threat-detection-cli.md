---
title: CLI-exempel på gransknings-och Avancerat skydd – Azure SQL Database
description: Exempel skript för Azure CLI för att konfigurera gransknings-och Avancerat skydd i en Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security
ms.devlang: azurecli
ms.topic: sample
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/09/2021
ms.openlocfilehash: 7450911dabe04ea04ba16ec5e648f09ecf4a616f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100095995"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Använd CLI för att konfigurera SQL Database granskning och Avancerat skydd

Detta exempel på Azure CLI-skript konfigurerar SQL Database granskning och Avancerat skydd.

Om du väljer att installera och använda CLI lokalt måste du köra Azure CLI version 2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

### <a name="sign-in-to-azure"></a>Logga in på Azure

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

```azurecli-interactive
$subscription = "<subscriptionId>" # add subscription here

az account set -s $subscription # ...or use 'az login'
```

### <a name="run-the-script"></a>Kör skriptet

```azurecli-interactive
#!/bin/bash
location="East US"
randomIdentifier=random123

resource="resource-$randomIdentifier"
server="server-$randomIdentifier"
database="database-$randomIdentifier"
storage="storage$randomIdentifier"

notification="changeto@your.email;changeto@your.email"

login="sampleLogin"
password="samplePassword123!"

echo "Using resource group $resource with login: $login, password: $password..."

echo "Creating $resource..."
az group create --name $resource --location "$location"

echo "Creating $server in $location..."
az sql server create --name $server --resource-group $resource --location "$location" --admin-user $login --admin-password $password

echo "Creating $database on $server..."
az sql db create --name $database --resource-group $resource --server $server --service-objective S0

echo "Creating $storage..."
az storage account create --name $storage --resource-group $resource --location "$location" --sku Standard_LRS

echo "Setting access policy on $storage..."
az sql db audit-policy update --resource-group $resource --server $server --name $database --state Enabled --blob-storage-target-state Enabled --storage-account $storage

echo "Setting threat detection policy on $storage..."
az sql db threat-policy update --email-account-admins Disabled --email-addresses $notification --name $database --resource-group $resource --server $server --state Enabled --storage-account $storage
```

### <a name="clean-up-deployment"></a>Rensa distribution

Använd följande kommando för att ta bort resurs gruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempel referens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Beskrivning |
|---|---|
| [AZ SQL DB audit-policy](/cli/azure/sql/db/audit-policy) | Anger granskningsprincipen för en databas. |
| [AZ SQL DB Threat-policy](/cli/azure/sql/db/threat-policy) | Ställer in en princip för avancerat skydd på en databas. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../../azure-sql/database/az-cli-script-samples-content-guide.md).
