---
title: ta med fil
description: inkludera fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/27/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: e6c4b07d01a4992e22107cb7d524646f439c37c6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997964"
---
Om du vill komma åt konsolloggarna som genereras i din programkod i App Service aktiverar du diagnostisk loggning genom att köra följande kommando i [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp log config --resource-group <resource-group-name> --name <app-name> --application-logging true --level Verbose
```

Möjliga värden för `--level` är: `Error`, `Warning`, `Info` och `Verbose`. Varje efterföljande nivå omfattar den föregående nivån. Exempel: `Error` omfattar endast felmeddelanden och `Verbose` omfattar alla meddelanden.

När diagnostisk loggning har aktiverats kör du följande kommando för att visa loggströmmen:

```azurecli-interactive
az webapp log tail --resource-group <resource-group-name> --name <app-name>
```

Om du inte ser konsolloggarna omedelbart kan du titta efter igen efter 30 sekunder.

> [!NOTE]
> Du kan även granska loggfilerna från din webbläsare via `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Skriv `Ctrl`+`C` när som helst för att stoppa loggströmningen.
