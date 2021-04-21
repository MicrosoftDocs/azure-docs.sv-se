---
title: Importera containeravbildningar
description: Importera containeravbildningar till ett Azure-containerregister med hjälp av Azure-API:er, utan att behöva köra Docker-kommandon.
ms.topic: article
ms.date: 01/15/2021
ms.openlocfilehash: e7becadab7f23acd7b85d6d82fd8abbfa7608add
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781531"
---
# <a name="import-container-images-to-a-container-registry"></a>Importera containeravbildningar till ett containerregister

Du kan enkelt importera (kopiera) containeravbildningar till ett Azure-containerregister utan att använda Docker-kommandon. Du kan till exempel importera avbildningar från ett utvecklingsregister till ett produktionsregister eller kopiera basavbildningar från ett offentligt register.

Azure Container Registry hanterar ett antal vanliga scenarier för att kopiera avbildningar och andra artefakter från ett befintligt register:

* Importera avbildningar från ett offentligt register

* Importera avbildningar eller OCI-artefakter, inklusive Helm 3-diagram från ett annat Azure-containerregister, i samma eller en annan Azure-prenumeration eller -klientorganisation

* Importera från ett privat containerregister som inte kommer från Azure

Avbildningsimport till ett Azure-containerregister har följande fördelar över att använda Docker CLI-kommandon:

* Eftersom klientmiljön inte behöver en lokal Docker-installation importerar du alla containeravbildningar, oavsett vilken OS-typ som stöds.

* När du importerar avbildningar med flera arkitekturer (till exempel officiella Docker-avbildningar) kopieras avbildningar för alla arkitekturer och plattformar som anges i manifestlistan.

* Åtkomst till målregistret behöver inte använda registrets offentliga slutpunkt.

För att importera containeravbildningar kräver den här artikeln att du kör Azure CLI i Azure Cloud Shell eller lokalt (version 2.0.55 eller senare rekommenderas). Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli].

> [!NOTE]
> Om du behöver distribuera identiska containeravbildningar över flera Azure-regioner Azure Container Registry även [stöd för geo-replikering.](container-registry-geo-replication.md) Genom att geo-replikera ett register (Premium-tjänstnivå krävs) kan du hantera flera regioner med identiska avbildnings- och taggnamn från ett enda register.
>

> [!IMPORTANT]
> Ändringar av avbildningsimporten mellan två Azure-containerregister har introducerats från och med januari 2021:
> * Import till eller från ett nätverksbegränsat Azure-containerregister kräver att det begränsade registret tillåter [**åtkomst från betrodda tjänster**](allow-access-trusted-services.md) för att kringgå nätverket. Som standard är inställningen aktiverad, vilket tillåter import. Om inställningen inte är aktiverad i ett nyligen skapat register med en privat slutpunkt eller med brandväggsregler för registret misslyckas importen. 
> * I ett befintligt nätverksbegränsat Azure-containerregister som används som importkälla eller mål är det valfritt att aktivera den här nätverkssäkerhetsfunktionen, men det rekommenderas.

## <a name="prerequisites"></a>Förutsättningar

Om du inte redan har ett Azure-containerregister skapar du ett register. Anvisningar finns i [Snabbstart: Skapa ett privat containerregister med hjälp av Azure CLI.](container-registry-get-started-azure-cli.md)

Om du vill importera en avbildning till ett Azure-containerregister måste din identitet ha skrivbehörighet till målregistret (minst deltagarrollen eller en anpassad roll som tillåter importImage-åtgärden). Se [Azure Container Registry roller och behörigheter](container-registry-roles.md#custom-roles). 

## <a name="import-from-a-public-registry"></a>Importera från ett offentligt register

### <a name="import-from-docker-hub"></a>Importera från Docker Hub

Använd till exempel kommandot [az acr import][az-acr-import] för att importera avbildningen med flera arkitekturer från Docker Hub till ett `hello-world:latest` register med namnet *myregistry*. Eftersom `hello-world` är en officiell avbildning Docker Hub är den här avbildningen på standarddatabasen. `library` Inkludera lagringsplatsens namn och eventuellt en tagg i värdet för `--source` avbildningsparametern. (Du kan även identifiera en avbildning efter dess manifestavbildning i stället för tagg, vilket garanterar en viss version av en avbildning.)
 
```azurecli
az acr import \
  --name myregistry \
  --source docker.io/library/hello-world:latest \
  --image hello-world:latest
```

Du kan kontrollera att flera manifest är associerade med den här avbildningen genom att köra `az acr repository show-manifests` kommandot :

```azurecli
az acr repository show-manifests \
  --name myregistry \
  --repository hello-world
```

Om du har ett [Docker Hub-konto](https://www.docker.com/pricing)rekommenderar vi att du använder autentiseringsuppgifterna när du importerar en avbildning Docker Hub. Skicka Docker Hub användarnamnet och lösenordet eller en personlig [åtkomsttoken](https://docs.docker.com/docker-hub/access-tokens/) som parametrar till `az acr import` . I följande exempel importeras en offentlig avbildning från `tensorflow` lagringsplatsen i Docker Hub med Docker Hub autentiseringsuppgifter:

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/tensorflow/tensorflow:latest-gpu \
  --image tensorflow:latest-gpu
  --username <Docker Hub user name>
  --password <Docker Hub token>
```

### <a name="import-from-microsoft-container-registry"></a>Importera från Microsoft Container Registry

Importera till exempel `ltsc2019` Windows Server Core-avbildningen från `windows` lagringsplatsen i Microsoft Container Registry.

```azurecli
az acr import \
--name myregistry \
--source mcr.microsoft.com/windows/servercore:ltsc2019 \
--image servercore:ltsc2019
```

## <a name="import-from-an-azure-container-registry-in-the-same-ad-tenant"></a>Importera från ett Azure-containerregister i samma AD-klientorganisation

Du kan importera en avbildning från ett Azure-containerregister i samma AD-klientorganisation med hjälp av Azure Active Directory behörigheter.

* Din identitet måste Azure Active Directory behörighet att läsa från källregistret (läsarrollen) och importera till [](container-registry-roles.md#custom-roles) målregistret (deltagarrollen eller en anpassad roll som tillåter importImage-åtgärden).

* Registret kan finnas i samma eller en annan Azure-prenumeration i samma Active Directory-klientorganisation.

* [Offentlig åtkomst](container-registry-access-selected-networks.md#disable-public-network-access) till källregistret kan vara inaktiverad. Om offentlig åtkomst är inaktiverad anger du källregistret efter resurs-ID i stället för efter namnet på registerinloggningsservern.

* Om källregistret och/eller målregistret har en privat slutpunkt eller om brandväggsreglerna för registret tillämpas ser du till att det begränsade registret tillåter att betrodda tjänster får åtkomst till nätverket. [](allow-access-trusted-services.md)

### <a name="import-from-a-registry-in-the-same-subscription"></a>Importera från ett register i samma prenumeration

Du kan till exempel importera `aci-helloworld:latest` avbildningen från ett *källregister mysourceregistry* *till myregistry* i samma Azure-prenumeration.

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld:latest \
  --image aci-helloworld:latest
```

I följande exempel importeras `aci-helloworld:latest` avbildningen till *myregistry* från källregistret *mysourceregistry* där åtkomsten till registrets offentliga slutpunkt är inaktiverad. Ange resurs-ID för källregistret med `--registry` parametern . Observera att `--source` parametern endast anger källdatabasen och taggen, inte namnet på registrets inloggningsserver.

```azurecli
az acr import \
  --name myregistry \
  --source aci-helloworld:latest \
  --image aci-helloworld:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

I följande exempel importeras en bild av manifestet digest (SHA-256-hash, representerad som `sha256:...` ) i stället för taggen :

```azurecli
az acr import \
  --name myregistry \
  --source mysourceregistry.azurecr.io/aci-helloworld@sha256:123456abcdefg 
```

### <a name="import-from-a-registry-in-a-different-subscription"></a>Importera från ett register i en annan prenumeration

I följande exempel finns *mysourceregistry* i en annan prenumeration än *myregistry* i samma Active Directory-klientorganisation. Ange resurs-ID för källregistret med `--registry` parametern . Observera att `--source` parametern endast anger källdatabasen och taggen, inte namnet på registrets inloggningsserver.

```azurecli
az acr import \
  --name myregistry \
  --source samples/aci-helloworld:latest \
  --image aci-hello-world:latest \
  --registry /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sourceResourceGroup/providers/Microsoft.ContainerRegistry/registries/mysourceregistry
```

### <a name="import-from-a-registry-using-service-principal-credentials"></a>Importera från ett register med autentiseringsuppgifter för tjänstens huvudnamn

Om du vill importera från ett register som du inte kan komma åt med integrerade Active Directory-behörigheter kan du använda autentiseringsuppgifter för tjänstens huvudnamn (om det är tillgängligt) till källregistret. Ange appID och lösenord för ett Active [Directory-tjänsthuvudnamn](container-registry-auth-service-principal.md) som har ACRPull-åtkomst till källregistret. Att använda tjänstens huvudnamn är användbart för att skapa system och andra obevakade system som behöver importera avbildningar till registret.

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-an-azure-container-registry-in-a-different-ad-tenant"></a>Importera från ett Azure-containerregister i en annan AD-klientorganisation

Om du vill importera från ett Azure-containerregister i en annan Azure Active Directory-klientorganisation anger du källregistret efter inloggningsservernamn och anger autentiseringsuppgifter för användarnamn och lösenord som ger pull-åtkomst till registret. Du kan till exempel använda en token och ett lösenord för lagringsplatsen, eller appID och lösenord för ett Active Directory-tjänsthuvudnamn som har ACRPull-åtkomst till källregistret. [](container-registry-repository-scoped-permissions.md) [](container-registry-auth-service-principal.md) 

```azurecli
az acr import \
  --name myregistry \
  --source sourceregistry.azurecr.io/sourcerrepo:tag \
  --image targetimage:tag \
  --username <SP_App_ID> \
  --password <SP_Passwd>
```

## <a name="import-from-a-non-azure-private-container-registry"></a>Importera från ett privat containerregister som inte kommer från Azure

Importera en avbildning från ett privat register som inte är ett Azure-register genom att ange autentiseringsuppgifter som ger pull-åtkomst till registret. Hämta till exempel en avbildning från ett privat Docker-register: 

```azurecli
az acr import \
  --name myregistry \
  --source docker.io/sourcerepo/sourceimage:tag \
  --image sourceimage:tag \
  --username <username> \
  --password <password>
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du importerar containeravbildningar till ett Azure-containerregister från ett offentligt register eller ett annat privat register. Ytterligare alternativ för avbildningsimport finns i [kommandoreferensen az acr import.][az-acr-import] 


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-import]: /cli/azure/acr#az_acr_import
[azure-cli]: /cli/azure/install-azure-cli
