---
title: ta med fil
description: ta med fil
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 289804a0006941b3fd5666888d3d8e16d3618fe7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102245032"
---
Skapa en [webbapp](../articles/app-service/overview.md#app-service-on-linux) i `myAppServicePlan` App Service plan. 

I Cloud Shell kan du använda [`az webapp create`](/cli/azure/webapp#az-webapp-create) kommandot. Ersätt `<app-name>` med ett globalt unikt appnamn (giltiga tecken är `a-z`, `0-9` och `-`) i följande exempel. 

```azurecli-interactive
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --deployment-local-git
```

När webbappen har skapats visar Azure CLI utdata liknande den i följande exempel:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

> [!NOTE]
> URL för fjärransluten Git visas i egenskapen `deploymentLocalGitUrl` med formatet `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Spara den här URL:en, eftersom du behöver den senare.
>
