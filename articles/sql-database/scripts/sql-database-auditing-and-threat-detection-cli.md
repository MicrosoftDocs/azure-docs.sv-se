---
title: CLI-exempel på granskning och Advanced Threat Protection – Azure SQL Database
description: Azure CLI-exempelskript för att konfigurera granskning och Advanced Threat Protection i en Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: security, devx-track-azurecli
ms.devlang: azurecli
ms.topic: sample
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 02/09/2021
ms.openlocfilehash: 1af5c3ede32ab85652b7f35d53694a77a36947b8
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478258"
---
# <a name="use-cli-to-configure-sql-database-auditing-and-advanced-threat-protection"></a>Använd CLI för att konfigurera SQL Database granskning och Advanced Threat Protection

Det här Azure CLI-skriptexempel konfigurerar SQL Database granskning och Advanced Threat Protection.

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

Använd följande kommando för att ta bort resursgruppen och alla resurser som är associerade med den.

```azurecli-interactive
az group delete --name $resource
```

## <a name="sample-reference"></a>Exempelreferens

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Beskrivning |
|---|---|
| [az sql db audit-policy](/cli/azure/sql/db/audit-policy) | Anger granskningsprincipen för en databas. |
| [az sql db threat-policy](/cli/azure/sql/db/threat-policy) | Anger en Advanced Threat Protection-princip för en databas. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för SQL Database finns i [Azure SQL Database-dokumentationen](../../azure-sql/database/az-cli-script-samples-content-guide.md).
