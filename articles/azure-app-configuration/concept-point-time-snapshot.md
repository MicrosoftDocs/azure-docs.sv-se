---
title: Hämta nyckel/värde-par från en tidpunkt
titleSuffix: Azure App Configuration
description: Hämta gamla nyckel/värde-par med tidpunkts ögonblicks bilder i Azure App konfiguration, som innehåller en post med ändringar av nyckel värden.
services: azure-app-configuration
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 08/05/2020
ms.openlocfilehash: fa2dbb11b3b8b9afd90c7f6fe3ffe77e2e57c4e6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929905"
---
# <a name="point-in-time-snapshot"></a>Tidpunktsbaserad ögonblicksbild

Azure App-konfigurationen innehåller en post med ändringar som gjorts i nyckel värden. Den här posten innehåller en tids linje med nyckel värdes ändringar. Du kan återskapa historiken för alla nyckel värden och ange det tidigare värdet när som helst inom nyckel historik perioden (7 dagar för platser med ledig nivå eller 30 dagar för standard lager). Med hjälp av den här funktionen kan du "Time-Travel" bakåt och hämta ett gammalt nyckel värde. Du kan till exempel återställa de konfigurations inställningar som används före den senaste distributionen för att återställa programmet till den tidigare konfigurationen.

## <a name="key-value-retrieval"></a>Hämtning av nyckelvärde

Du kan använda Azure Portal eller CLI för att hämta tidigare nyckel värden. I Azure CLI använder du `az appconfig revision list` för att lägga till lämpliga parametrar för att hämta de värden som krävs.  Ange Azure App konfigurations instans genom att antingen ange Store-namnet ( `--name <app-config-store-name>` ) eller med hjälp av en anslutnings sträng ( `--connection-string <your-connection-string>` ). Begränsa utdata genom att ange en viss tidpunkt ( `--datetime` ) och genom att ange det maximala antalet objekt som ska returneras ( `--top` ).

Om du inte har installerat Azure CLI lokalt kan du välja att använda [Azure Cloud Shell](../cloud-shell/overview.md).

Hämta alla registrerade ändringar till dina nyckel värden.

```azurecli
az appconfig revision list --name <your-app-config-store-name>.
```

Hämta alla registrerade ändringar för nyckeln `environment` och etiketterna `test` och `prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Hämta alla registrerade ändringar i det hierarkiska nyckel utrymmet `environment:prod` .

```azurecli
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Hämta alla registrerade ändringar för nyckeln `color` vid en viss tidpunkt.

```azurecli
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Hämta de senaste 10 registrerade ändringarna till dina nyckel värden och returnera bara värdena för `key` , och tidstämpelt `label` `last_modified` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-webbapp](./quickstart-aspnet-core-app.md)