---
title: Hantera offentligt innehåll i privat container Registry
description: Metoder och arbets flöden i Azure Container Registry för att hantera beroenden i offentliga avbildningar från Docker Hub och annat offentligt innehåll
author: dlepow
ms.topic: article
ms.author: danlep
ms.date: 11/20/2020
ms.openlocfilehash: 0c92899528d417f9c91f8f8930ca4932dc74e850
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "95024746"
---
# <a name="manage-public-content-with-azure-container-registry"></a>Hantera offentligt innehåll med Azure Container Registry

Den här artikeln är en översikt över metoder och arbets flöden för att använda ett lokalt register, till exempel ett [Azure Container Registry](container-registry-intro.md) för att underhålla kopior av offentligt innehåll, till exempel behållar avbildningar i Docker Hub. 


## <a name="risks-with-public-content"></a>Risker med offentligt innehåll

Din miljö kan ha beroenden av offentligt innehåll, till exempel offentliga behållar avbildningar, [Helm-diagram](https://helm.sh/), OPA-principer ( [Open Policy Agent](https://www.openpolicyagent.org/) ) eller andra artefakter. Du kan till exempel köra [nginx](https://hub.docker.com/_/nginx) för tjänst routning eller genom att hämta `docker build FROM alpine` avbildningar direkt från Docker Hub eller ett annat offentligt register. 

Utan rätt kontroller kan du med beroenden av offentligt register innehåll lägga till risker för dina bild utvecklings-och distributions arbets flöden. För att minimera riskerna bör du behålla lokala kopior av offentligt innehåll när det är möjligt. Mer information finns i [bloggen öppna container Initiative](https://opencontainers.org/posts/blog/2020-10-30-consuming-public-content/). 

## <a name="authenticate-with-docker-hub"></a>Autentisera med Docker Hub

Som ett första steg, om du för närvarande hämtar offentliga avbildningar från Docker Hub som en del av ett arbets flöde för bygge eller distribution, rekommenderar vi att du [autentiserar med ett Docker Hub-konto](https://docs.docker.com/docker-hub/download-rate-limit/#how-do-i-authenticate-pull-requests) i stället för att göra en anonym pull-begäran.

När du gör vanliga anonyma pull-begäranden kan du se Docker-fel som liknar `ERROR: toomanyrequests: Too Many Requests.` eller `You have reached your pull rate limit.` autentiseras för Docker Hub för att förhindra dessa fel.

> [!NOTE]
> Från och med den 2 november 2020 gäller [nedladdnings hastighets gränserna](https://docs.docker.com/docker-hub/download-rate-limit) för anonyma och autentiserade begär anden till Docker Hub från Docker-fri Plans konton och tillämpas av IP-adress respektive Docker-ID. 
>
> När du uppskattar antalet pull-begäranden bör du ta hänsyn till att när du använder moln leverantörs tjänster eller arbetar bakom en företags-NAT, visas flera användare för Docker-hubben i mängd som en del av IP-adresserna. Om du lägger till Docker-inbetald konto autentisering till förfrågningar som görs i Docker Hub kan du undvika avbrott i tjänsten på grund av hastighets begränsning.
>
> Mer information finns i [Docker-priser och prenumerationer](https://www.docker.com/pricing) och [Docker-villkoren](https://www.docker.com/legal/docker-terms-service).

### <a name="docker-hub-access-token"></a>Åtkomst-token för Docker Hub

Docker Hub stöder [personliga](https://docs.docker.com/docker-hub/access-tokens/) åtkomsttoken som alternativ till Docker-lösenord vid autentisering till Docker-hubben. Tokens rekommenderas för automatiska tjänster som hämtar bilder från Docker Hub. Du kan generera flera token för olika användare eller tjänster och återkalla tokens när de inte längre behövs.

Om du vill autentisera med `docker login` hjälp av en token utelämnar du lösen ordet på kommando raden. När du uppmanas att ange ett lösen ord anger du token i stället. Om du har aktiverat tvåfaktorautentisering för ditt Docker Hub-konto måste du använda en personlig åtkomsttoken när du loggar in från Docker CLI.

### <a name="authenticate-from-azure-services"></a>Autentisera från Azure-tjänster

Flera Azure-tjänster, inklusive App Service och Azure Container Instances stöd för att hämta bilder från offentliga register som Docker Hub för behållar distributioner. Om du behöver distribuera en avbildning från Docker Hub rekommenderar vi att du konfigurerar inställningar för att autentisera med ett Docker Hub-konto. Exempel:

**App Service**

* **Avbildnings källa**: Docker Hub
* **Åtkomst till lagrings plats**: privat
* **Inloggning**: \<Docker Hub username>
* **Lösen ord**: \<Docker Hub token>

Mer information finns i [Docker Hub autentiserade hämtningar på App Service](https://azure.github.io/AppService/2020/10/15/Docker-Hub-authenticated-pulls-on-App-Service.html).

**Azure Container Instances**

* **Avbildnings källa**: Docker hubb eller annat register
* **Avbildnings typ**: privat
* **Inloggnings Server för avbildnings register**: Docker.io
* **Användar namn för avbildnings register**: \<Docker Hub username>
* **Lösen ord för avbildnings register**: \<Docker Hub token>
* **Bild**: Docker.io/ \<repo name\> :\<tag>

## <a name="import-images-to-an-azure-container-registry"></a>Importera avbildningar till ett Azure Container Registry
 
Om du vill börja hantera kopior av offentliga avbildningar kan du skapa ett Azure Container Registry om du inte redan har ett. Skapa ett register med hjälp av [Azure CLI](container-registry-get-started-azure-cli.md), [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md)eller andra verktyg. 

Du rekommenderas att [Importera](container-registry-import-images.md) bas avbildningar och annat offentligt innehåll till ditt Azure Container Registry som ett rekommenderat steg i taget. [Import kommandot AZ ACR](/cli/azure/acr#az_acr_import) i Azure CLI stöder avbildnings import från offentliga register som Docker Hub och Microsoft container Registry och från andra register med privat behållare. 

`az acr import` kräver inte en lokal Docker-installation. Du kan köra den med en lokal installation av Azure CLI eller direkt i Azure Cloud Shell. Det stöder avbildningar av alla OS-typer, avbildningar med flera arkitektur eller OCI-artefakter som Helm-diagram.

Exempel:

```azurecli-interactive
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest \
  --username <Docker Hub username> \
  --password <Docker Hub token>
```

Beroende på organisationens behov kan du importera till ett dedikerat register eller en lagrings plats i ett delat register.

## <a name="automate-application-image-updates"></a>Automatisera uppdateringar av program avbildning

Utvecklare av program avbildningar bör se till att deras kod refererar till lokalt innehåll under deras kontroll. En `Docker FROM` instruktion i en Dockerfile bör till exempel referera till en bild i ett privat bas avbildnings register i stället för ett offentligt register. 

Expandera vid bild import, konfigurera en [Azure Container Registry uppgift](container-registry-tasks-overview.md) för att automatisera program avbildningar när bas avbildningar uppdateras. En automatiserad versions uppgift kan spåra både [bas avbildnings uppdateringar](container-registry-tasks-base-images.md) och [käll kods uppdateringar](container-registry-tasks-overview.md#trigger-task-on-source-code-update).

Ett detaljerat exempel finns i [så här använder och underhåller du offentligt innehåll med Azure Container Registry uppgifter](tasks-consume-public-content.md). 

> [!NOTE]
> En förkonfigurerad uppgift kan automatiskt återskapa varje program avbildning som refererar till en beroende bas avbildning. 
 
## <a name="next-steps"></a>Nästa steg
 
* Lär dig mer om [ACR-uppgifter](container-registry-tasks-overview.md) för att skapa, köra, push-och korrigerings behållar avbildningar i Azure.
* Se [hur du använder och underhåller offentligt innehåll med Azure Container Registry uppgifter](tasks-consume-public-content.md) för ett automatiserat hantera-arbetsflöde för att uppdatera bas avbildningar till din miljö. 
* I [självstudierna om ACR tasks](container-registry-tutorial-quick-task.md) hittar du fler exempel på hur du automatiserar avbildnings versioner och uppdateringar.
