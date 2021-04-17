---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 04/22/2020
ms.author: cephalin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 727e49e9086e5800fbbcb4e42a37a8b1a0c1c4b7
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107513357"
---
Skapa en [webbapp](../articles/app-service/overview.md#app-service-on-linux) i `myAppServicePlan` App Service plan. 

I Cloud Shell kan du använda [`az webapp create`](/cli/azure/webapp) kommandot . Ersätt `<app-name>` med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel. Körningen har angetts till `DOTNETCORE|3.1`. Om du vill se alla körningar som stöds kör du [`az webapp list-runtimes --linux`](/cli/azure/webapp) . 

```azurecli-interactive
# Bash
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git
# PowerShell
az --% webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

<pre>
Local git is configured with url of 'https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app-name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
</pre>

Du har ™ en tom webbapp i en Linux-container med git-distribution aktiverad.

> [!NOTE]
> URL för fjärransluten Git visas i egenskapen `deploymentLocalGitUrl` med formatet `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Spara den här URL:en, eftersom du behöver den senare.
>
