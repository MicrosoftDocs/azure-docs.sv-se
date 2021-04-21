---
title: Skriptexempel för Azure CLI – Importera till ett App Configuration store
titleSuffix: Azure App Configuration
description: Använda Azure CLI-skript – Importera konfiguration för att Azure App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5a80acd1261211fe3efaefa6a4b6025a321e2d65
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774187"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importera till ett Azure App Configuration-arkiv

Det här exempelskriptet importerar nyckel/värde-inställningar till Azure App Configuration arkivet.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --format json --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att importera till ett App Configuration store. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az appconfig kv import](/cli/azure/appconfig/kv#az_appconfig_kv_import) | Importerar till en App Configuration store-resurs. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen.](/cli/azure)

Ytterligare App Configuration CLI-skriptexempel finns i Azure App Configuration [CLI-exempel](../cli-samples.md).
