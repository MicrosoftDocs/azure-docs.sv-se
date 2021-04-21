---
title: Skicka och hämta OCI-artefakt
description: Skicka och hämta OCI-artefakter (Open Container Initiative) med hjälp av ett privat containerregister i Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 399bb001432759556cd0ba8bf15f7738dd4edb7c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781495"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Skicka och hämta en OCI-artefakt med hjälp av ett Azure-containerregister

Du kan använda ett Azure-containerregister för att lagra och hantera [OCI-artefakter (Open Container Initiative)](container-registry-image-formats.md#oci-artifacts) samt Docker- och Docker-kompatibla containeravbildningar.

För att demonstrera den här funktionen visar den här artikeln hur du använder [ORAS-verktyget (OCI Registry as Storage)](https://github.com/deislabs/oras) för att skicka en exempelartefakt – en textfil – till ett Azure-containerregister. Hämta sedan artefakten från registret. Du kan hantera en mängd olika OCI-artefakter i ett Azure-containerregister med hjälp av olika kommandoradsverktyg som är lämpliga för varje artefakt.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-containerregister** – Skapa ett containerregister i din Azure-prenumeration. Använd till exempel [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **ORAS-verktyget** – Ladda ned och installera en aktuell ORAS-version för ditt operativsystem från [GitHub-lagringsplatsen](https://github.com/deislabs/oras/releases). Verktyget släpps som en komprimerad tarball `.tar.gz` (-fil). Extrahera och installera filen med hjälp av standardprocedurer för ditt operativsystem.
* **Azure Active Directory tjänstens huvudnamn (valfritt)** – Om du vill autentisera direkt med ORAS skapar du ett [huvudnamn](container-registry-auth-service-principal.md) för tjänsten för att få åtkomst till registret. Se till att tjänstens huvudnamn har tilldelats en roll, till exempel AcrPush, så att den har behörighet att skicka och hämta artefakter.
* **Azure CLI (valfritt)** – Om du vill använda en enskild identitet behöver du en lokal installation av Azure CLI. Version 2.0.71 eller senare rekommenderas. Kör `az --version ` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).
* **Docker (valfritt)** – Om du vill använda en enskild identitet måste du också ha Docker installerat lokalt för att autentisera med registret. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.


## <a name="sign-in-to-a-registry"></a>Logga in på ett register

Det här avsnittet visar två föreslagna arbetsflöden för att logga in i registret, beroende på vilken identitet som används. Välj den metod som passar din miljö.

### <a name="sign-in-with-oras"></a>Logga in med ORAS

Använd ett [huvudnamn för](container-registry-auth-service-principal.md) tjänsten med push-behörighet och kör kommandot för att logga in i registret `oras login` med program-ID och lösenord för tjänstens huvudnamn. Ange det fullständigt kvalificerade registernamnet (endast gemener), i det här *myregistry.azurecr.io*. Program-ID:t för tjänstens huvudnamn skickas i `$SP_APP_ID` miljövariabeln och lösenordet i variabeln `$SP_PASSWD` .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Om du vill läsa lösenordet från Stdin använder du `--password-stdin` .

### <a name="sign-in-with-azure-cli"></a>Logga in med Azure CLI

[Logga in](/cli/azure/authenticate-azure-cli) på Azure CLI med din identitet för att skicka och hämta artefakter från containerregistret.

Använd sedan Azure CLI-kommandot [az acr login för att](/cli/azure/acr#az_acr_login) komma åt registret. Till exempel för att autentisera till ett register med *namnet myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` använder Docker-klienten för att ange Azure Active Directory-token i `docker.config` filen. Docker-klienten måste installeras och köras för att slutföra det enskilda autentiseringsflödet.

## <a name="push-an-artifact"></a>Push-pusha en artefakt

Skapa en textfil i en lokal arbetskatalog med exempeltext. Till exempel i ett bash-gränssnitt:

```bash
echo "Here is an artifact" > artifact.txt
```

Använd kommandot `oras push` för att skicka den här textfilen till registret. I följande exempel push-filen med exempeltext till `samples/artifact` lagringsplatsen. Registret identifieras med det fullständigt kvalificerade registernamnet som *myregistry.azurecr.io* (endast gemener). Artefakten har taggats `1.0` . Artefakten har som standard en odefinierad typ som identifieras av *mediatypssträngen* efter filnamnet `artifact.txt` . Se [OCI Artifacts (OCI-artefakter)](https://github.com/opencontainers/artifacts) för ytterligare typer. 

**Linux eller macOS**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Utdata för en lyckad push liknar följande:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Om du vill hantera artefakter i registret, om du använder Azure CLI, kör du `az acr` standardkommandon för att hantera avbildningar. Du kan till exempel hämta attributen för artefakten med kommandot [az acr repository show:][az-acr-repository-show]

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

De utdata som genereras liknar följande:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Hämta en artefakt

Kör kommandot `oras pull` för att hämta artefakten från registret.

Ta först bort textfilen från din lokala arbetskatalog:

```bash
rm artifact.txt
```

Kör `oras pull` för att hämta artefakten och ange medietypen som används för att skicka artefakten:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Kontrollera att pull-programmet lyckades:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Ta bort artefakten (valfritt)

Om du vill ta bort artefakten från Azure-containerregistret använder du [kommandot az acr repository delete.][az-acr-repository-delete] I följande exempel tas artefakten som du lagrade där bort:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Exempel: Skapa Docker-avbildning från OCI-artefakt

Källkod och binärfiler för att skapa en containeravbildning kan lagras som OCI-artefakter i ett Azure-containerregister. Du kan referera till en källartefakt som byggkontext för en [ACR-uppgift.](container-registry-tasks-overview.md) Det här exemplet visar hur du lagrar en Dockerfile som en OCI-artefakt och sedan refererar till artefakten för att skapa en containeravbildning.

Skapa till exempel en Dockerfile med en rad:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Logga in på målcontainerregistret.

```azurecli
az login
az acr login --name myregistry
```

Skapa och push-skicka en ny OCI-artefakt till målregistret med hjälp av `oras push` kommandot . Det här exemplet anger standardmedietypen för artefakten.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Kör kommandot [az acr build för](/cli/azure/acr#az_acr_build) att skapa hello-world-avbildningen med den nya artefakten som byggkontext:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [ORAS-biblioteket](https://github.com/deislabs/oras/tree/master/docs), inklusive hur du konfigurerar ett manifest för en artefakt
* Besök [OCI Artifacts-lagringsplatsen](https://github.com/opencontainers/artifacts) för referensinformation om nya artefakttyper



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
