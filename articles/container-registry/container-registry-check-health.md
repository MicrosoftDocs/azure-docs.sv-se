---
title: Kontrollera registrets hälsa
description: Lär dig hur du kör ett snabbt diagnostikkommando för att identifiera vanliga problem när du använder ett Azure-containerregister, inklusive lokal Docker-konfiguration och anslutning till registret
ms.topic: article
ms.date: 07/02/2019
ms.openlocfilehash: fec05efe67f5c502f36ee90eec57ba283b15a4a0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761753"
---
# <a name="check-the-health-of-an-azure-container-registry"></a>Kontrollera hälsotillståndet för ett Azure-containerregister

När du använder ett Azure-containerregister kan det ibland uppstå problem. Du kanske till exempel inte kan hämta en containeravbildning på grund av ett problem med Docker i din lokala miljö. Eller så kan ett nätverksproblem hindra dig från att ansluta till registret. 

Som ett första diagnostiskt steg kör du [kommandot az acr check-health][az-acr-check-health] för att få information om miljöns hälsotillstånd och eventuellt åtkomst till ett målregister. Det här kommandot är tillgängligt i Azure CLI version 2.0.67 eller senare. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

Ytterligare felsökningsvägledning för registret finns i:
* [Felsöka registerinloggning](container-registry-troubleshoot-login.md)
* [Felsöka nätverksproblem med registret](container-registry-troubleshoot-access.md)
* [Felsöka registerprestanda](container-registry-troubleshoot-performance.md)

## <a name="run-az-acr-check-health"></a>Kör az acr check-health

I följande exempel visas olika sätt att köra `az acr check-health` kommandot.

> [!NOTE]
> Om du kör kommandot i Azure Cloud Shell kontrolleras inte den lokala miljön. Du kan dock kontrollera åtkomsten till ett målregister.

### <a name="check-the-environment-only"></a>Kontrollera endast miljön

Kontrollera den lokala Docker-daemonen, CLI-versionen och Helm-klientkonfigurationen genom att köra kommandot utan ytterligare parametrar:

```azurecli
az acr check-health
```

### <a name="check-the-environment-and-a-target-registry"></a>Kontrollera miljön och ett målregister

Om du vill kontrollera åtkomsten till ett register och utföra lokala miljökontroller skickar du namnet på ett målregister. Exempel:

```azurecli
az acr check-health --name myregistry
```

## <a name="error-reporting"></a>Felrapportering

Kommandot loggar information till standardutdata. Om ett problem upptäcks visas en felkod och en beskrivning. Mer information om koder och möjliga lösningar finns i [felreferensen](container-registry-health-error-reference.md).

Som standard stoppas kommandot när ett fel hittas. Du kan också köra kommandot så att det ger utdata för alla hälsokontroller, även om fel hittas. Lägg till `--ignore-errors` parametern enligt följande exempel:

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

Mer information om felkoder som returneras av [kommandot az acr check-health][az-acr-check-health] finns i [felreferensen för hälsokontroll.](container-registry-health-error-reference.md)

Se Vanliga [frågor](container-registry-faq.md) och svar för vanliga frågor och andra kända problem om Azure Container Registry.





<!-- LINKS - internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-check-health]: /cli/azure/acr#az_acr_check_health
