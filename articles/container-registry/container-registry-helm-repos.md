---
title: Lagra Helm-diagram
description: Lär dig hur du lagrar Helm-diagram för dina Kubernetes-program med hjälp av lagringsplatsen i Azure Container Registry
ms.topic: article
ms.date: 04/15/2021
ms.openlocfilehash: 6698eb8f5e18511717e44bf5dc06a51d8f3903b8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537323"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Skicka och hämta Helm-diagram till ett Azure-containerregister

Om du snabbt vill hantera och distribuera program för Kubernetes kan du använda [Helm-pakethanteraren med öppen källkod.][helm] Med Helm definieras programpaket som diagram , [som](https://helm.sh/docs/topics/charts/)samlas in och lagras på en [Helm-diagramdatabas.](https://helm.sh/docs/topics/chart_repository/)

Den här artikeln visar hur du är värd för Helm-diagramlagringsplatsen i ett Azure-containerregister med hjälp av Helm 3-kommandon. I många scenarier skulle du skapa och ladda upp egna diagram för de program som du utvecklar. Mer information om hur du skapar egna Helm-diagram finns i utvecklarhandboken [för diagrammallen.][develop-helm-charts] Du kan också lagra ett befintligt Helm-diagram från en annan Helm-lagringsplats.

## <a name="helm-3-or-helm-2"></a>Helm 3 eller Helm 2?

Om du vill lagra, hantera och installera Helm-diagram använder du en Helm-klient och Helm CLI. Större versioner av Helm-klienten är Helm 3 och Helm 2. Mer information om versionsskillnader finns i vanliga frågor [och svar om versionen.](https://helm.sh/docs/faq/) 

Helm 3 bör användas som värd för Helm-diagram i Azure Container Registry. Med Helm 3 gör du följande:

* Kan skapa en eller flera Helm-lagringsplatsen i ett Azure-containerregister
* Lagra Helm 3-diagram i ett register som [OCI-artefakter](container-registry-image-formats.md#oci-artifacts). Azure Container Registry ger GA-stöd för [OCI-artefakter,](container-registry-oci-artifacts.md)inklusive Helm-diagram.
* Autentisera med registret med hjälp av `helm registry login` kommandot .
* Använda `helm chart` kommandon i Helm CLI för att skicka, hämta och hantera Helm-diagram i ett register
* Använd `helm install` för att installera diagram i ett Kubernetes-kluster från en lokal lagringsplatscache.
> [!NOTE]
> Från och med Helm 3 [blir az acr helm-kommandon][az-acr-helm] för användning med Helm 2-klienten inaktuella. Minst tre månaders varsel ges innan kommandot tas bort. Om du tidigare har distribuerat Helm 2-diagram kan du se [Migrera Helm v2 till v3.](https://helm.sh/docs/topics/v2_v3_migration/)

## <a name="prerequisites"></a>Förutsättningar

Följande resurser behövs för scenariot i den här artikeln:

- **Ett Azure-containerregister** i din Azure-prenumeration. Om det behövs skapar du ett register [med hjälp Azure Portal](container-registry-get-started-portal.md) eller Azure [CLI.](container-registry-get-started-azure-cli.md)
- **Helm-klientversion 3.1.0 eller senare –** Kör för `helm version` att hitta din aktuella version. Mer information om hur du installerar och uppgraderar Helm finns [i Installera Helm.][helm-install]
- **Ett Kubernetes-kluster** där du installerar ett Helm-diagram. Om det behövs skapar du [ett Azure Kubernetes Service kluster][aks-quickstart]. 
- **Azure CLI version 2.0.71 eller senare –** Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="enable-oci-support"></a>Aktivera OCI-stöd

Använd kommandot `helm version` för att kontrollera att du har installerat Helm 3:

```console
helm version
```

Ange följande miljövariabel för att aktivera OCI-stöd i Helm 3-klienten. För närvarande är det här stödet experimentellt. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Skapa ett exempeldiagram

Skapa ett testdiagram med följande kommandon:

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

I mappen `templates` skapar du en fil med namnet genom att köra följande `configmap.yaml` kommando:

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

Mer information om hur du skapar och kör det här [exemplet finns Komma igång](https://helm.sh/docs/chart_template_guide/getting_started/) i Helm Docs.

## <a name="save-chart-to-local-registry-cache"></a>Spara diagram till lokal registercache

Ändra katalog till `hello-world` underkatalogen. Kör sedan för att spara en kopia av diagrammet lokalt och skapa även ett alias med det fullständigt kvalificerade namnet på registret `helm chart save` (endast gemener) och måldatabasen och taggen. 

I följande exempel är registernamnet *mycontainerregistry,* måldatabasen *är hello-world* och måldiagramstaggen *är v1,* men ersätter värdena för din miljö:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Kör `helm chart list` för att bekräfta att du sparade diagrammen i den lokala registercachen. Utdata liknar följande:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Autentisera med registret

Kör kommandot `helm registry login` i Helm 3 CLI för att autentisera [med registret med de autentiseringsuppgifter](container-registry-authentication.md) som är lämpliga för ditt scenario.

Du kan till exempel skapa Azure Active Directory [tjänstens huvudnamn](container-registry-auth-service-principal.md#create-a-service-principal) med pull- och push-behörigheter (rollen AcrPush) till registret. Ange sedan autentiseringsuppgifterna för tjänstens huvudnamn till `helm registry login` . I följande exempel anges lösenordet med hjälp av en miljövariabel:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Push-diagram till registret

Kör kommandot `helm chart push` i Helm 3 CLI för att skicka diagrammet till den fullständigt kvalificerade måldatabasen:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Efter en lyckad push-push ser utdata ut ungefär så här:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Lista diagram på lagringsplatsen

Precis som med avbildningar som lagras i ett Azure-containerregister kan du använda [az acr repository-kommandon][az-acr-repository] för att visa lagringsplatserna som är värdar för dina diagram samt diagramtaggar och manifest. 

Kör till exempel [az acr repository show för][az-acr-repository-show] att se egenskaperna för lagringsplatsen som du skapade i föregående steg:

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

Kör kommandot [az acr repository show-manifests för][az-acr-repository-show-manifests] att se information om diagrammet som lagras på lagringsplatsen. Exempel:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

Utdata, förkortat i det här exemplet, visar `configMediaType` ett av `application/vnd.cncf.helm.config.v1+json` :

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

## <a name="pull-chart-to-local-cache"></a>Hämta diagram till lokal cache

Om du vill installera ett Helm-diagram i Kubernetes måste diagrammet finnas i den lokala cachen. I det här exemplet kör du först `helm chart remove` för att ta bort det befintliga lokala diagrammet med namnet `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Kör `helm chart pull` för att ladda ned diagrammet från Azure-containerregistret till din lokala cache:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Exportera Helm-diagram

Om du vill arbeta vidare med diagrammet exporterar du det till en lokal katalog med hjälp av `helm chart export` . Exportera till exempel diagrammet som du har dragit till `install` katalogen:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Om du vill visa information om det exporterade diagrammet i lagringsplatsen kör `helm show chart` du kommandot i katalogen där du exporterade diagrammet.

```console
cd install
helm show chart hello-world
```

Helm returnerar detaljerad information om den senaste versionen av diagrammet, som du ser i följande exempelutdata:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Installera Helm-diagram

Kör `helm install` för att installera Helm-diagrammet som du har dragit till den lokala cachen och exporterat. Ange ett versionnamn, till *exempel myhelmtest*, eller skicka `--generate-name` parametern . Exempel:

```console
helm install myhelmtest ./hello-world
```

Utdata efter lyckad diagraminstallation liknar:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Kontrollera installationen genom att köra `helm get manifest` kommandot . 

```console
helm get manifest myhelmtest
```

Kommandot returnerar YAML-data i `configmap.yaml` mallfilen.

Kör `helm uninstall` för att avinstallera diagramutgågåren i klustret:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Ta bort diagram från registret

Om du vill ta bort ett diagram från containerregistret använder du [kommandot az acr repository delete.][az-acr-repository-delete] Kör följande kommando och bekräfta åtgärden när du uppmanas att göra det:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du skapar och distribuerar Helm-diagram finns i [Utveckla Helm-diagram.][develop-helm-charts]
* Läs mer om att installera program med Helm [i Azure Kubernetes Service (AKS).](../aks/kubernetes-helm.md)
* Helm-diagram kan användas som en del av containerbyggprocessen. Mer information finns i [Använda Azure Container Registry-uppgifter][acr-tasks].

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
