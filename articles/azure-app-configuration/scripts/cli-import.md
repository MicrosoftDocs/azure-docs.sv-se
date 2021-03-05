---
title: Skript exempel för Azure CLI – importera till ett konfigurations lager för appar
titleSuffix: Azure App Configuration
description: Använd Azure CLI-skript – importera konfiguration till Azure App konfiguration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: f7d68024dd86a1eb2a0311cecf607d41a7067deb
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173576"
---
# <a name="import-to-an-azure-app-configuration-store"></a>Importera till ett Azure App Configuration-arkiv

Det här exempel skriptet importerar nyckel värdes inställningar till en Azure App konfigurations lagring.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#!/bin/bash

# Import key-values from a file
az appconfig kv import --name myTestAppConfigStore --source file --format json --path ~/Import.json
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon för att importera till ett konfigurations lager för appar. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [AZ appconfig kv import](/cli/azure/appconfig/kv#az-appconfig-kv-import) | Importerar till en resurs för konfigurations Arkiv för appar. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare skript exempel för programkonfigurations-CLI finns i [Azure App konfigurations-CLI-exempel](../cli-samples.md).
