---
title: Skriptexempel för Azure CLI – Exportera från en Azure App Configuration Store
titleSuffix: Azure App Configuration
description: Använda Azure CLI-skript för att exportera konfiguration från Azure App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1c4eb6e2aa150751dfbadc2307d64ab206b92b6d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782233"
---
# <a name="export-from-an-azure-app-configuration-store"></a>Exportera från ett Azure App Configuration-arkiv

Det här exempelskriptet exporterar nyckelvärden från ett Azure App Configuration-arkiv.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

# Export all key-values
az appconfig kv export --name myTestAppConfigStore --file ~/Export.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att exportera från ett App Configuration store. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az appconfig kv export](/cli/azure/appconfig/kv#az_appconfig_kv_export) | Exporterar från en App Configuration store-resurs. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare App Configuration CLI-skriptexempel finns i Azure App Configuration [CLI-exempel](../cli-samples.md).
