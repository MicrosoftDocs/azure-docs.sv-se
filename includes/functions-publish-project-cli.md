---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: c99cac6626cb40b8fd368e4fc1d8454bb2195521
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93425168"
---
## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktions projektet till Azure

När du har skapat din Function-app i Azure är du nu redo att distribuera ditt lokala Functions-projekt med hjälp av kommandot [FUNC Azure-functionapp Publish](../articles/azure-functions/functions-run-local.md#project-file-deployment) .  

I följande exempel ersätter du `<APP_NAME>` med namnet på din app.

```console
func azure functionapp publish <APP_NAME>
```

Kommandot Publicera visar resultat som liknar följande utdata (trunkerade för enkelhetens skull):

<pre>
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>
