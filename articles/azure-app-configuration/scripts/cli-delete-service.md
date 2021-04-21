---
title: Skriptexempel för Azure CLI – Ta bort ett Azure App Configuration Store
titleSuffix: Azure App Configuration
description: Ta bort ett Azure App Configuration store med hjälp av ett Azure CLI-exempelskript. Se referensartikellänkar till kommandon som används i skriptet.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 07c4ac3234dc67978f16f6c0c7e0702924bf2bb5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768956"
---
# <a name="delete-an-azure-app-configuration-store"></a>Ta bort ett Azure App Configuration-arkiv

Det här exempelskriptet tar bort en instans av Azure App Configuration.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här självstudien kräver version 2.0 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

```azurecli-interactive
#/bin/bash

# Delete an App Configuration store named myTestAppConfigStore from the Resource Group myResourceGroup
az appconfig delete --name myTestAppConfigStore --resource-group myResourceGroup
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att ta bort en App Configuration store. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az appconfig delete](/cli/azure/appconfig#az_appconfig_delete) | Tar bort en App Configuration store-resurs. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare App Configuration CLI-skriptexempel finns i Azure App Configuration [CLI-exempel](../cli-samples.md).
