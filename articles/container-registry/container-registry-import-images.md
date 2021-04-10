---
title: Importera containeravbildningar
description: 'Importera behållar avbildningar till ett Azure Container Registry med hjälp av Azure API: er, utan att behöva köra Docker-kommandon.'
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: b481a2b4f0a08f0ccbfd33f7059e5d8e0c04f5f0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106221508"
---
# <a name="import-container-images-to-a-container-registry"></a>Importera behållar avbildningar till ett behållar register

Du kan enkelt importera (kopiera) behållar avbildningar till ett Azure Container Registry utan att använda Docker-kommandon. Du kan till exempel importera bilder från ett utvecklings register till ett produktions register eller kopiera bas avbildningar från ett offentligt register.

Azure Container Registry hanterar ett antal vanliga scenarier för att kopiera avbildningar och andra artefakter från ett befintligt register:

* Importera avbildningar från ett offentligt register

* Importera bilder eller OCI-artefakter, inklusive Helm 3-diagram från ett annat Azure Container Registry, i samma eller en annan Azure-prenumeration eller klient organisation

* Importera från ett icke-Azure privat behållar register

Avbildnings import till ett Azure Container Registry har följande fördelar jämfört med Docker CLI-kommandon:

* Eftersom klient miljön inte behöver en lokal Docker-installation importerar du en behållar avbildning, oavsett vilken typ av operativ system som stöds.

* När du importerar avbildningar med flera arkitekturer (till exempel officiella Docker-avbildningar) kopieras avbildningar för alla arkitekturer och plattformar som anges i manifest listan.

* Åtkomst till mål registret behöver inte använda registrets offentliga slut punkt.

Den här artikeln kräver att du kör Azure CLI i Azure Cloud Shell eller lokalt (version 2.0.55 eller senare rekommenderas) för att importera behållar avbildningar. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!NOTE]
> Om du behöver distribuera identiska behållar avbildningar i flera Azure-regioner stöder Azure Container Registry också [geo-replikering](container-registry-geo-replication.md). Genom geo-replikering av ett register (Premium service nivå krävs) kan du hantera flera regioner med identiska bild-och taggnamn från ett enda register.
>

> [!IMPORTANT]
> Ändringar av avbildnings import mellan två Azure-behållar register har införts från och med januari 2021:
> * Att importera till eller från en nätverks begränsad Azure Container Registry kräver att det begränsade registret [**tillåter åtkomst av betrodda tjänster**](allow-access-trusted-services.md) för att kringgå nätverket. Inställningen är aktive rad som standard, vilket tillåter import. Om inställningen inte är aktive rad i ett nyligen skapat register med en privat slut punkt eller med brand Väggs regler för registret kommer importen att Miss Förslut ATS. 
> * I ett befintligt nätverks begränsat Azure Container Registry som används som import källa eller mål, är det valfritt att aktivera den här funktionen för nätverks säkerhet, men rekommenderas.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har ett Azure Container Registry skapar du ett register. Anvisningar finns i [snabb start: skapa ett privat behållar register med hjälp av Azure CLI](container-registry-get-started-azure-cli.md).

Om du vill importera en avbildning till ett Azure Container Registry måste identiteten ha Skriv behörighet till mål registret (minst deltagar rollen eller en anpassad roll som tillåter åtgärden importImage). Se [Azure Container Registry roller och behörigheter](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importera från ett offentligt register

### <a name="import-from-docker-hub"></a>Importera från Docker Hub

Använd exempelvis kommandot [AZ ACR import][az-acr-import] för att importera avbildningen av flera arkitekturer `hello-world:latest` från Docker-hubb till ett register med namnet *unregistry*. Eftersom `hello-world` är en officiell avbildning från Docker Hub finns den här avbildningen i standard `library` lagrings platsen. Inkludera databas namnet och eventuellt en tagg i värdet för `--source` parametern image. (Du kan också identifiera en avbildning med dess manifest sammandrag i stället för efter tagg, som garanterar en viss version av en avbildning.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Du kan kontrol lera att flera manifest är associerade med den här avbildningen genom att köra `az acr repository show-manifests` kommandot:

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Om du har ett [Docker Hub-konto](https://www.docker.com/pricing)rekommenderar vi att du använder autentiseringsuppgifterna när du importerar en avbildning från Docker Hub. Skicka Docker-hubbens användar namn och lösen ord eller en [personlig åtkomsttoken](https://docs.docker.com/docker-hub/access-tokens/) som parametrar till `az acr import` . I följande exempel importeras en offentlig avbildning från `tensorflow` lagrings platsen i Docker Hub med hjälp av Docker Hub-autentiseringsuppgifter:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importera från Microsoft Container Registry

Importera till exempel `ltsc2019` Windows Server Core-avbildningen från `windows` lagrings platsen i Microsoft container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importera från ett Azure Container Registry i samma AD-klient

Du kan importera en avbildning från ett Azure Container Registry i samma AD-klient med hjälp av integrerade Azure Active Directory behörigheter.

* Din identitet måste ha Azure Active Directory behörighet att läsa från käll registret (läsar roll) och för att kunna importera till mål registret (deltagar rollen eller en [anpassad roll](container-registry-roles.md#custom-roles) som tillåter åtgärden importImage).

* Registret kan finnas i samma eller en annan Azure-prenumeration i samma Active Directory klient.

* [Offentlig åtkomst](container-registry-access-selected-networks.md#disable-public-network-access) till käll registret kan inaktive ras. Om offentlig åtkomst är inaktive rad anger du käll registret per resurs-ID i stället för inloggnings Server namnet för registret.

* Om käll registret och/eller mål registret har en privat slut punkt eller brand Väggs regler för registret, kontrollerar du att det begränsade registret [tillåter betrodda tjänster](allow-access-trusted-services.md) att få åtkomst till nätverket.

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importera från ett register i samma prenumeration

Importera till exempel `aci-helloworld:latest` avbildningen från ett käll register *mysourceregistry* till *registret* i samma Azure-prenumeration.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

I följande exempel importeras `aci-helloworld:latest` avbildningen till *registret* från en *mysourceregistry* för käll registret där åtkomst till registrets offentliga slut punkt är inaktive rad. Ange resurs-ID för käll registret med `--registry` parametern. Observera att `--source` parametern endast anger käll databasen och taggen, inte namnet på inloggnings servern för registret.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

I följande exempel importeras en avbildning av manifest sammandrag (SHA-256-hash, som visas `sha256:...` ) i stället för taggen:

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importera från ett register i en annan prenumeration

I följande exempel finns *mysourceregistry* i en annan prenumeration än *registret* i samma Active Directory-klient. Ange resurs-ID för käll registret med `--registry` parametern. Observera att `--source` parametern endast anger käll databasen och taggen, inte namnet på inloggnings servern för registret.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importera från ett register med autentiseringsuppgifter för tjänstens huvud namn

Om du vill importera från ett register som du inte kan komma åt med hjälp av integrerade Active Directory behörigheter kan du använda autentiseringsuppgifterna för tjänstens huvud namn (om de är tillgängliga) i käll registret. Ange appID och lösen ordet för en Active Directory [tjänstens huvud namn](container-registry-auth-service-principal.md) som har ACRPull åtkomst till käll registret. Att använda ett huvud namn för tjänsten är användbart för att bygga system och andra obevakade system som behöver importera avbildningar till registret.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importera från ett Azure Container Registry i en annan AD-klient

Om du vill importera från ett Azure Container Registry i en annan Azure Active Directory klient, anger du käll registret efter inloggnings Server namn och anger autentiseringsuppgifter för användar namn och lösen ord som aktiverar åtkomst till registret. Du kan till exempel använda en [Central token](container-registry-repository-scoped-permissions.md) och ett lösen ord för databasen, eller AppID och lösen ordet för en Active Directory [tjänstens huvud namn](container-registry-auth-service-principal.md) som har ACRPull åtkomst till käll registret. 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importera från ett icke-Azure privat behållar register

Importera en avbildning från ett privat icke-Azure-register genom att ange autentiseringsuppgifter som aktiverar åtkomst till registret. Hämta till exempel en avbildning från ett privat Docker-register: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du importerar behållar avbildningar till ett Azure Container Registry från ett offentligt register eller ett annat privat register. Ytterligare alternativ för avbildnings import finns i kommando referens för [AZ ACR import][az-acr-import] . 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-import]: /cli/azure/acr#az-acr-import
[azure-cli]: /cli/azure/install-azure-cli
