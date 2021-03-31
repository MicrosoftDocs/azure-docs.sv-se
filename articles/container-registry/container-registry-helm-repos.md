---
title: Lagra Helm-diagram
description: Lär dig hur du lagrar Helm-diagram för dina Kubernetes-program med hjälp av databaser i Azure Container Registry
ms.topic: article
ms.date: 06/12/2020
ms.openlocfilehash: 69b16f35589586787e1c31a0e9755b9030af755d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86537875"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Push-och pull-Helm diagram till ett Azure Container Registry

Om du snabbt vill hantera och distribuera program för Kubernetes kan du använda [paket hanteraren med öppen källkod för Helm][helm]. Med Helm definieras programpaket som [diagram](https://helm.sh/docs/topics/charts/), som samlas in och lagras i en Helm- [diagram lagrings plats](https://helm.sh/docs/topics/chart_repository/).

Den här artikeln visar hur du kan vara värd för Helm Charts-databaser i ett Azure Container Registry med hjälp av Helm 3-kommandon. I många fall skulle du skapa och ladda upp dina egna diagram för de program som du utvecklar. Mer information om hur du skapar egna Helm-diagram finns i [Developer-Guide för diagram mal len][develop-helm-charts]. Du kan också lagra ett befintligt Helm-diagram från en annan Helm lagrings platsen.

> [!IMPORTANT]
> Stöd för Helm-diagram i Azure Container Registry är för närvarande en för hands version. För hands versionerna görs tillgängliga för dig på villkor att du godkänner kompletterande [användnings villkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="helm-3-or-helm-2"></a>Helm 3 eller Helm 2?

Om du vill lagra, hantera och installera Helm-diagram använder du en Helm-klient och Helm CLI. Större versioner av Helm-klienten är Helm 3 och Helm 2. Mer information om versions skillnaderna finns i [vanliga frågor och svar](https://helm.sh/docs/faq/)om versioner. 

Helm 3 ska användas som värd för Helm-diagram i Azure Container Registry. Med Helm 3 kan du:

* Kan skapa en eller flera Helm-databaser i ett Azure Container Registry
* Lagra Helm 3-diagram i ett register som [OCI-artefakter](container-registry-image-formats.md#oci-artifacts). Helm 3-support för OCI är för närvarande *experimentellt*.
* Autentisera med ditt register med hjälp av `helm registry login` kommandot.
* Använd `helm chart` kommandon i Helm CLI för att skicka, hämta och hantera Helm-diagram i ett register
* Använd `helm install` för att installera diagram till ett Kubernetes-kluster från en lokal cache för lagring.
> [!NOTE]
> Från och med Helm 3 är [AZ ACR Helm][az-acr-helm] -kommandon för användning med Helm 2-klienten inaktuella. Se [produkt översikten](https://github.com/Azure/acr/blob/master/docs/acr-roadmap.md#acr-helm-ga). Om du tidigare har distribuerat Helm 2-diagram, se [migrera Helm v2 till v3](https://helm.sh/docs/topics/v2_v3_migration/).

## <a name="prerequisites"></a>Förutsättningar

Följande resurser krävs för scenariot i den här artikeln:

- **Ett Azure Container Registry** i din Azure-prenumeration. Om det behövs skapar du ett register med hjälp av [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
- **Helm klient version 3.1.0 eller senare** – kör `helm version` för att hitta din aktuella version. Mer information om hur du installerar och uppgraderar Helm finns i [Installera Helm][helm-install].
- **Ett Kubernetes-kluster** där du ska installera ett Helm-diagram. Om det behövs skapar du ett [Azure Kubernetes service-kluster][aks-quickstart]. 
- **Azure CLI version 2.0.71 eller senare** – kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="enable-oci-support"></a>Aktivera OCI-stöd

Använd `helm version` kommandot för att kontrol lera att du har installerat Helm 3:

```console
helm version
```

Ange följande miljö variabel för att aktivera OCI-stöd i Helm 3-klienten. Den här supporten är för närvarande experimentell. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Skapa ett exempel diagram

Skapa ett test diagram med följande kommandon:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Som ett grundläggande exempel ändrar du katalogen till `templates` mappen och tar först bort innehållet där:

```console
cd hello-world/templates
rm -rf *
```

I `templates` mappen skapar du en fil med namnet `configmap.yaml` , genom att köra följande kommando:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Mer information om hur du skapar och kör det här exemplet finns [komma igång](https://helm.sh/docs/chart_template_guide/getting_started/) i Helm-dokumenten.

## <a name="save-chart-to-local-registry-cache"></a>Spara diagrammet i det lokala registrets cacheminne

Ändra katalog till under `hello-world` katalogen. Sedan kör `helm chart save` du för att spara en kopia av diagrammet lokalt och även skapa ett alias med det fullständigt kvalificerade namnet på registret (alla gemener) och mål databasen och taggen. 

I följande exempel är register namnet *mycontainerregistry*, mål lagrings platsen är *Hello-World* och mål diagram tag gen är *v1*, men ersätter värden för din miljö:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Kör `helm chart list` för att bekräfta att du har sparat diagrammen i det lokala cacheminnet för registret. Utdata liknar följande:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Autentisera med registret

Kör `helm registry login` kommandot i Helm 3 CLI för att [autentisera med registret](container-registry-authentication.md) med de autentiseringsuppgifter som är lämpliga för ditt scenario.

Skapa till exempel en Azure Active Directory [tjänstens huvud namn med pull-och push-behörigheter](container-registry-auth-service-principal.md#create-a-service-principal) (AcrPush-rollen) till registret. Ange sedan autentiseringsuppgifterna för tjänstens huvud namn till `helm registry login` . I följande exempel förses lösen ordet med en miljö variabel:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Skicka diagram till registret

Kör `helm chart push` kommandot i Helm 3 CLI för att skicka diagrammet till det fullständigt kvalificerade mål lagrings platsen:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Efter en lyckad push liknar utdata följande:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Visa lista över diagram i databasen

Precis som med bilder som lagras i ett Azure Container Registry kan du använda [AZ ACR-lagringsplatsen][az-acr-repository] för att visa de databaser som är värdar för dina diagram och diagram etiketter och-manifest. 

Du kan till exempel köra [AZ ACR-lagringsplatsen][az-acr-repository-show] för att se egenskaperna för lagrings platsen som du skapade i föregående steg:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

Utdata liknar följande:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Kör kommandot [AZ ACR-lagringsplatsen show-Manifests][az-acr-repository-show-manifests] för att se information om diagrammet som lagras i lagrings platsen. Exempel:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Utdata, förkortat i det här exemplet, visar en `configMediaType` av `application/vnd.cncf.helm.config.v1+json` :

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Hämta diagram till lokalt cacheminne

Om du vill installera ett Helm-diagram på Kubernetes måste diagrammet vara i det lokala cacheminnet. I det här exemplet ska du först köra `helm chart remove` för att ta bort det befintliga lokala diagrammet med namnet `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Kör `helm chart pull` för att ladda ned diagrammet från Azure Container Registry till ditt lokala cacheminne:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Exportera Helm-diagram

Om du vill arbeta mer med diagrammet exporterar du det till en lokal katalog med hjälp av `helm chart export` . Exportera till exempel diagrammet som du hämtade till `install` katalogen:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Om du vill visa information om det exporterade diagrammet i lagrings platsen kör du `helm show chart` kommandot i den katalog där du exporterade diagrammet.

```console
cd install
helm show chart hello-world
```

Helm returnerar detaljerad information om den senaste versionen av diagrammet, som visas i följande exempel på utdata:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Installera Helm-diagram

Kör `helm install` för att installera Helm-diagrammet som du hämtade till den lokala cachen och exporterade. Ange ett versions namn, till exempel *myhelmtest*, eller skicka `--generate-name` parametern. Exempel:

```console
helm install myhelmtest ./hello-world
```

Utdata efter lyckad diagram installation liknar:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Verifiera installationen genom att köra `helm get manifest` kommandot. 

```console
helm get manifest myhelmtest
```

Kommandot returnerar YAML-data i `configmap.yaml` mallfilen.

Kör `helm uninstall` för att avinstallera diagram versionen på klustret:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Ta bort diagram från registret

Om du vill ta bort ett diagram från behållar registret använder du kommandot [AZ ACR databas Delete][az-acr-repository-delete] . Kör följande kommando och bekräfta åtgärden när du uppmanas till det:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar och distribuerar Helm-diagram finns i [utveckla Helm-diagram][develop-helm-charts].
* Lär dig mer om att installera program med Helm i [Azure Kubernetes service (AKS)](../aks/kubernetes-helm.md).
* Helm-diagram kan användas som en del av behållar Bygg processen. Mer information finns i [använda Azure Container Registry uppgifter][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[acr-tasks]: container-registry-tasks-overview.md
