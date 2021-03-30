---
title: Kontrol lera register hälsa
description: Lär dig hur du kör ett Quick Diagnostic-kommando för att identifiera vanliga problem när du använder ett Azure Container Registry, inklusive lokal Docker-konfiguration och anslutning till registret
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: f27a99818260553cbd7ba26158db0064c145a21f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88245391"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Kontrol lera hälso tillståndet för ett Azure Container Registry

När du använder ett Azure Container Registry kan du ibland stöta på problem. Till exempel kanske du inte kan hämta en behållar avbildning på grund av ett problem med Docker i din lokala miljö. Eller också kan ett nätverks problem förhindra att du ansluter till registret. 

Som ett första diagnostiskt steg kör du kommandot [AZ ACR check-Health][az-acr-check-health] för att få information om hälso tillståndet för miljön och eventuellt till gång till ett mål register. Det här kommandot är tillgängligt i Azure CLI version 2.0.67 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Ytterligare fel söknings vägledning för registret finns i:
* [Felsöka inloggning av registret](container-registry-troubleshoot-login.md)
* [Felsöka nätverks problem med registret](container-registry-troubleshoot-access.md)
* [Felsöka register prestanda](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Kör AZ ACR-kontroll – hälsa

I exemplen nedan visas olika sätt att köra `az acr check-health` kommandot.

> [!NOTE]
> Om du kör kommandot i Azure Cloud Shell, kontrol leras inte den lokala miljön. Du kan dock kontrol lera åtkomsten till ett mål register.

### <a name="check-the-environment-only"></a>Kontrol lera endast miljön

Om du vill kontrol lera den lokala Docker-daemonen, CLI-versionen och Helm-klient konfigurationen kör du kommandot utan ytterligare parametrar:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Kontrol lera miljön och ett mål register

Om du vill kontrol lera åtkomsten till ett register och utföra lokala miljö kontroller, måste du skicka namnet på ett mål register. Exempel:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Fel rapportering

Kommandot loggar information till standardutdata. Om ett problem upptäcks visas en felkod och en beskrivning. Mer information om koder och möjliga lösningar finns i [fel referensen](container-registry-health-error-reference.md).

Som standard stannar kommandot när ett fel påträffas. Du kan också köra kommandot så att det ger utdata för alla hälso kontroller, även om fel har påträffats. Lägg till `--ignore-errors` parametern, som du ser i följande exempel:

```azurecli
# Check environment only
az acr check-health --ignore-errors

# Check environment and target registry
az acr check-health --name myregistry --ignore-errors
```      

Exempel på utdata:

```console
$ az acr check-health --name myregistry --ignore-errors --yes

Docker daemon status: available
Docker version: Docker version 18.09.2, build 6247962
Docker pull of 'mcr.microsoft.com/mcr/hello-world:latest' : OK
ACR CLI version: 2.2.9
Helm version:
Client: &version.Version{SemVer:"v2.14.1", GitCommit:"5270352a09c7e8b6e8c9593002a73535276507c0", GitTreeState:"clean"}
DNS lookup to myregistry.azurecr.io at IP 40.xxx.xxx.162 : OK
Challenge endpoint https://myregistry.azurecr.io/v2/ : OK
Fetch refresh token for registry 'myregistry.azurecr.io' : OK
Fetch access token for registry 'myregistry.azurecr.io' : OK
```  



## <a name="next-steps"></a>Nästa steg

Mer information om felkoder som returneras av kommandot [AZ ACR check-Health][az-acr-check-health] finns i [fel referens för hälso kontroll](container-registry-health-error-reference.md).

Se vanliga frågor och [svar](container-registry-faq.md) om vanliga frågor och andra kända problem med Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az-acr-check-health
