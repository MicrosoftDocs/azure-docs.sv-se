---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/04/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 404fd10c3e3610671d2b6e5dbc6aba8bcaa70046
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100019483"
---
## <a name="push-image-to-registry"></a>Push-överför avbildningen till registret

Innan du kan push-överföra en avbildning till Azure Container Registry måste du ha en avbildning. Om du inte har några lokala behållar avbildningar kör du följande [Docker pull][docker-pull] -kommando för att hämta en befintlig offentlig avbildning. I det här exemplet hämtar du `hello-world` avbildningen från Microsoft container Registry.

```
docker pull mcr.microsoft.com/hello-world
```

Innan du kan push-överföra en avbildning till registret måste du tagga den med det fullständigt kvalificerade namnet på inloggnings servern för registret. Inloggnings serverns namn har formatet *\<registry-name\> . azurecr.io* (måste vara alla gemener), till exempel *mycontainerregistry.azurecr.io*.

Tagga avbildningen med hjälp av kommandot [docker tag][docker-tag]. Ersätt `<login-server>` med namnet på inloggningsservern för ACR-instansen.

```
docker tag mcr.microsoft.com/hello-world <login-server>/hello-world:v1
```

Exempel:

```
docker tag mcr.microsoft.com/hello-world mycontainerregistry.azurecr.io/hello-world:v1
```


Slutligen använder du [Docker push][docker-push] för att push-överföra avbildningen till register instansen. Ersätt `<login-server>` med inloggnings Server namnet för din register instans. Det här exemplet skapar lagringsplatsen **hello-world**, som innehåller `hello-world:v1`-avbildningen.

```
docker push <login-server>/hello-world:v1
```

När du har push-överfört avbildningen till containerregistret tar du bort `hello-world:v1`-avbildningen från den lokala Docker-miljön. (Observera att detta [docker rmi][docker-rmi]-kommando inte tar bort avbildningen från **hello-world**-lagringsplatsen i Azure-containerregistret.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->

