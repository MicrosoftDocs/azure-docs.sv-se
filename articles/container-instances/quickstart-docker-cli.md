---
title: Snabb start – distribuera Docker-behållare till container instance – Docker CLI
description: I den här snabb starten använder du Docker CLI för att snabbt distribuera en container-webbapp som körs i en isolerad Azure Container instance
ms.topic: quickstart
ms.date: 09/14/2020
ms.custom: devx-track-js
ms.openlocfilehash: 9904fb8c2142816196a1939a16445318bdb245d0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91262322"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>Snabb start: Distribuera en behållar instans i Azure med hjälp av Docker CLI

Använd Azure Container Instances för att köra Server lös Docker-behållare i Azure med enkelhet och hastighet. Distribuera till en behållar instans på begäran när du utvecklar Cloud-inhemska appar och du vill växla sömlöst från lokal utveckling till moln distribution.

I den här snabb starten använder du inbyggda Docker CLI-kommandon för att distribuera en Docker-behållare och göra dess program tillgängligt i Azure Container Instances. Den här funktionen är aktive rad genom [integrering mellan Docker och Azure](https://docs.docker.com/engine/context/aci-integration/). Några sekunder efter att du kört ett `docker run` kommando kan du bläddra till programmet som körs i behållaren:

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="App som distribuerats via Azure Container Instances visas i webbläsare":::

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto][azure-account] innan du börjar.

I den här snabb starten behöver du Docker Desktop version 2.3.0.5 eller senare, tillgänglig för [Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) eller [MacOS](https://desktop.docker.com/mac/edge/Docker.dmg). Eller installera [Docker ACI integration CLI för Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux). 

> [!IMPORTANT]
> Det finns inte stöd för alla funktioner i Azure Container Instances. Ge feedback om Docker-Azure-integrering genom att skapa ett problem i [ACI-integration – beta GitHub-](https://github.com/docker/aci-integration-beta) lagringsplatsen.

[!INCLUDE [container-instances-create-docker-context](../../includes/container-instances-create-docker-context.md)]

## <a name="create-a-container"></a>Skapa en container

När du har skapat en Docker-kontext kan du skapa en behållare i Azure. I den här snabb starten använder du den offentliga `mcr.microsoft.com/azuredocs/aci-helloworld` avbildningen. Den här avbildningen paketerar en liten webbapp som skrivits i Node.js och som hanterar en statisk HTML-sida.

Ändra först till ACI-kontexten. Alla efterföljande Docker-kommandon körs i den här kontexten.

```
docker context use myacicontext
```

Kör följande `docker run` kommando för att skapa Azure Container instance med port 80 exponerad för Internet:

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

Exempel på utdata för lyckad distribution:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

Kör `docker ps` för att få information om den behållare som körs, inklusive den offentliga IP-adressen:

```
docker ps
```


Exempel på utdata visar en offentlig IP-adress, i det här fallet *52.230.225.232*:

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 Gå nu till IP-adressen i webbläsaren. Om du ser en webbsida som liknar följande – grattis! Du har distribuerat ett program som körs i en dockercontainer till Azure.

:::image type="content" source="media/quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="App som distribuerats via Azure Container Instances visas i webbläsare":::

## <a name="pull-the-container-logs"></a>Hämta containerloggarna

Om du behöver felsöka en container eller det program som körs av containern (eller bara se dess utdata), börjar du med att granska loggarna för containerinstansen.

Kör till exempel `docker logs` kommandot för att se loggarna för *Kirch* container i Aci-kontexten:

```azurecli-interactive
docker logs hungry-kirch
```

Utdata visar loggarna för containern och bör även visa de HTTP GET-förfrågningar som genererades när du granskade programmet i webbläsaren.

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med behållaren kan du köra `docker rm` för att ta bort den. Det här kommandot stoppar och tar bort Azure Container instance.

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Azure Container instance från en offentlig avbildning genom att använda integration mellan Docker och Azure. Lär dig mer om integrations scenarier i [Docker-dokumentationen](https://docs.docker.com/engine/context/aci-integration/). 

Du kan också använda [Docker-tillägget för Visual Studio Code](https://aka.ms/VSCodeDocker) för en integrerad upplevelse för att utveckla, köra och hantera behållare, bilder och kontexter.

Om du vill använda Azure-verktyg för att skapa och hantera behållar instanser, se andra snabb starter med [Azure CLI](container-instances-quickstart.md)-, [Azure PowerShell](container-instances-quickstart-powershell.md)-, [Azure Portal](container-instances-quickstart-portal.md)-och [Azure Resource Manager-mall](container-instances-quickstart-template.md).

Fortsätt till självstudien om du vill använda Docker Compose för att definiera och köra ett program med flera behållare lokalt och sedan växla till Azure Container Instances.

> [!div class="nextstepaction"]
> [Själv studie kurs om Docker](./tutorial-docker-compose.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

