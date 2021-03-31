---
title: ta med fil
description: ta med fil
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 03/20/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 10fb9e8169b7f4159ccbf4a0ff36021f6033f811
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "75552433"
---
Du måste uppfylla följande krav för att kunna slutföra den här självstudiekursen:

**Azure CLI**: Du måste ha Azure CLI version 2.0.29 eller senare installerat på den lokala datorn. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI][azure-cli-install].

**Docker**: Den här självstudiekursen förutsätter grundläggande kunskaper om grundläggande Docker-begrepp som containrar, containeravbildningar och grundläggande `docker`-kommandon. En introduktion till grunderna för Docker och containrar finns i [Docker-översikt][docker-get-started].

**Docker**: du måste ha Docker installerat lokalt för att kunna slutföra den här självstudien. Docker innehåller paket som konfigurerar Docker-miljön på [macOS][docker-mac], [Windows][docker-windows] och [Linux][docker-linux].

> [!IMPORTANT]
> Eftersom Azure Cloud Shell inte innehåller Docker-daemon *måste* du installera både Azure CLI och Docker-motorn på din *lokala dator* för att kunna fullfölja den här självstudiekursen. Du kan använda Azure Cloud Shell för den här självstudiekursen.

<!-- LINKS - External -->
[docker-get-started]: https://docs.docker.com/engine/docker-overview/
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
