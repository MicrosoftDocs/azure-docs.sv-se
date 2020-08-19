---
title: Hämta nyckel/värde-par från en tidpunkt
titleSuffix: Azure App Configuration
description: Hämta gamla nyckel/värde-par med tidpunkts ögonblicks bilder i Azure App konfiguration, som innehåller en post med ändringar av nyckel värden.
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b1d559d82cb22d8a787785c6d8c6a5101d89793a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586638"
---
# <a name="point-in-time-snapshot"></a>Tidpunktsbaserad ögonblicksbild

Azure App-konfigurationen innehåller en post med ändringar som gjorts i nyckel värden. Den här posten innehåller en tids linje med nyckel värdes ändringar. Du kan återskapa historiken för alla nyckel värden och ange det tidigare värdet när som helst inom nyckel historik perioden (7 dagar för platser med ledig nivå eller 30 dagar för standard lager). Med hjälp av den här funktionen kan du "Time-Travel" bakåt och hämta ett gammalt nyckel värde. Du kan till exempel återställa de konfigurations inställningar som används före den senaste distributionen för att återställa programmet till den tidigare konfigurationen.

## <a name="key-value-retrieval"></a>Hämtning av nyckelvärde

Du kan använda Azure Portal eller CLI för att hämta tidigare nyckel värden. I Azure CLI använder du `az appconfig revision list` för att lägga till lämpliga parametrar för att hämta de värden som krävs.  Ange Azure App konfigurations instans genom att antingen ange Store-namnet ( `--name <app-config-store-name>` ) eller med hjälp av en anslutnings sträng ( `--connection-string <your-connection-string>` ). Begränsa utdata genom att ange en viss tidpunkt ( `--datetime` ) och genom att ange det maximala antalet objekt som ska returneras ( `--top` ).

Om du inte har installerat Azure CLI lokalt kan du välja att använda Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Hämta alla registrerade ändringar till dina nyckel värden.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name>.
```

Hämta alla registrerade ändringar för nyckeln `environment` och etiketterna `test` och `prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Hämta alla registrerade ändringar i det hierarkiska nyckel utrymmet `environment:prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Hämta alla registrerade ändringar för nyckeln `color` vid en viss tidpunkt.

```azurecli-interactive
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Hämta de senaste 10 registrerade ändringarna till dina nyckel värden och returnera bara värdena för `key` , och tidstämpelt `label` `last_modified` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-webbapp](./quickstart-aspnet-core-app.md)  
