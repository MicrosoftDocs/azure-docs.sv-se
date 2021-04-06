---
title: Skicka och ta emot OCI-artefakt
description: Skicka och ta emot OCI-artefakter (Open container Initiative) med ett privat behållar register i Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 02/03/2021
ms.author: stevelas
ms.openlocfilehash: 8a73f295999888dab20531ffdd0fb042790a5357
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99988221"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Push-överför och hämta en OCI-artefakt med ett Azure Container Registry

Du kan använda ett Azure Container Registry för att lagra och hantera [OCI-artefakter (Open container Initiative)](container-registry-image-formats.md#oci-artifacts) samt Docker-och Docker-kompatibla behållar avbildningar.

Den här artikeln visar hur du kan använda verktyget [OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) för att visa den här funktionen för att skicka ett exempel på en artefakt – en textfil till ett Azure Container Registry. Hämta sedan artefakten från registret. Du kan hantera en mängd OCI-artefakter i ett Azure Container Registry med olika kommando rads verktyg som är lämpliga för varje artefakt.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-containerregister** – Skapa ett containerregister i din Azure-prenumeration. Använd till exempel [Azure Portal](container-registry-get-started-portal.md) eller [Azure CLI](container-registry-get-started-azure-cli.md).
* **Verktyget ORAS** – hämta och installera en aktuell ORAS-version för operativ systemet från [GitHub-lagrings platsen](https://github.com/deislabs/oras/releases). Verktyget släpps som en komprimerad tarball ( `.tar.gz` fil). Extrahera och installera filen med standard procedurer för ditt operativ system.
* **Azure Active Directory tjänstens huvud namn (valfritt)** – om du vill autentisera direkt med ORAS skapar du ett [huvud namn för tjänsten](container-registry-auth-service-principal.md) för att få åtkomst till registret. Se till att tjänstens huvud namn har tilldelats en roll som AcrPush så att den har behörighet att skicka och ta emot artefakter.
* **Azure CLI (valfritt)** – om du vill använda en enskild identitet behöver du en lokal installation av Azure CLI. Version 2.0.71 eller senare rekommenderas. Kör `az --version ` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).
* **Docker (valfritt)** – om du vill använda en enskild identitet måste du också ha Docker installerat lokalt för att kunna autentisera med registret. Docker innehåller paket som enkelt kan konfigurera Docker på ett [macOS][docker-mac]-, [Windows][docker-windows]- eller [Linux][docker-linux]-system.


## <a name="sign-in-to-a-registry"></a>Logga in på ett register

I det här avsnittet visas två föreslagna arbets flöden för att logga in i registret, beroende på vilken identitet som används. Välj lämplig metod för din miljö.

### <a name="sign-in-with-oras"></a>Logga in med ORAS

Med hjälp av ett [huvud namn för tjänsten](container-registry-auth-service-principal.md) med push-behörighet kör du `oras login` kommandot för att logga in i registret med tjänstens huvud NAMNS program-ID och lösen ord. Ange det fullständigt kvalificerade register namnet (alla gemener) i det här fallet *myregistry.azurecr.io*. Tjänstens huvud program-ID skickas i miljövariabeln `$SP_APP_ID` och lösen ordet i variabeln `$SP_PASSWD` .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Om du vill läsa lösen ordet från STDIN använder du `--password-stdin` .

### <a name="sign-in-with-azure-cli"></a>Logga in med Azure CLI

[Logga in](/cli/azure/authenticate-azure-cli) på Azure CLI med din identitet för att skicka och ta emot artefakter från behållar registret.

Använd sedan Azure CLI-kommandot [AZ ACR login](/cli/azure/acr#az-acr-login) för att få åtkomst till registret. Om du till exempel vill autentisera till ett register med namnet *unregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login` använder Docker-klienten för att ange en Azure Active Directory token i `docker.config` filen. Docker-klienten måste vara installerad och igång för att slutföra det enskilda autentiseringsschemat.

## <a name="push-an-artifact"></a>Skicka en artefakt

Skapa en textfil i en lokal arbets katalog med lite exempel text. Till exempel i ett bash-gränssnitt:

```bash
echo "Here is an artifact" > artifact.txt
```

Använd `oras push` kommandot för att skicka den här text filen till registret. I följande exempel skickas exempel text filen till `samples/artifact` lagrings platsen. Registret identifieras med det fullständigt kvalificerade register namnet *myregistry.azurecr.io* (alla gemener). Artefakten är taggad `1.0` . Artefakten har en odefinierad typ som standard identifieras av *medie typ* strängen efter fil namnet `artifact.txt` . Se [OCI-artefakter](https://github.com/opencontainers/artifacts) för ytterligare typer. 

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

Utdata för en lyckad push ser ut ungefär så här:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Om du använder Azure CLI för att hantera artefakter i registret kan du köra standard `az acr` kommandon för att hantera avbildningar. Hämta till exempel attributen för artefakten med hjälp av kommandot [AZ ACR-lagringsplatsen show][az-acr-repository-show] :

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

Kör `oras pull` kommandot för att hämta artefakten från registret.

Ta först bort text filen från den lokala arbets katalogen:

```bash
rm artifact.txt
```

Kör `oras pull` för att hämta artefakten och ange den medietyp som används för att skicka artefakten:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Verifiera att hämtningen lyckades:

```bash
$ cat artifact.txt
Here is an artifact
```

## <a name="remove-the-artifact-optional"></a>Ta bort artefakten (valfritt)

Om du vill ta bort artefakten från Azure Container Registry använder du kommandot [AZ ACR databas Delete][az-acr-repository-delete] . I följande exempel tar vi bort artefakten som du har lagrat där:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="example-build-docker-image-from-oci-artifact"></a>Exempel: bygga Docker-avbildning från OCI-artefakt

Käll koden och binärfiler för att bygga en behållar avbildning kan lagras som OCI-artefakter i ett Azure Container Registry. Du kan referera till en käll artefakt som bygg kontext för en [ACR-aktivitet](container-registry-tasks-overview.md). I det här exemplet visas hur du lagrar en Dockerfile som en OCI-artefakt och sedan refererar artefakten för att skapa en behållar avbildning.

Skapa till exempel en Dockerfile med en rad:

```bash
echo "FROM mcr.microsoft.com/hello-world" > hello-world.dockerfile
```

Logga in på mål behållar registret.

```azurecli
az login
az acr login --name myregistry
```

Skapa och skicka en ny OCI-artefakt till mål registret med hjälp av `oras push` kommandot. I det här exemplet anges standard medie typen för artefakten.

```bash
oras push myregistry.azurecr.io/dockerfile:1.0 hello-world.dockerfile
```

Kör kommandot [AZ ACR build](/cli/azure/acr#az-acr-build) för att skapa Hello-World-avbildningen med den nya artefakten som bygg kontext:

```azurecli
az acr build --registry myregistry --image builds/hello-world:v1 \
  --file hello-world.dockerfile \
  oci://myregistry.azurecr.io/dockerfile:1.0
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [ORAS-biblioteket](https://github.com/deislabs/oras/tree/master/docs), inklusive hur du konfigurerar ett manifest för en artefakt
* Besök [OCI-artefakterna](https://github.com/opencontainers/artifacts) lagrings platsen för referensinformation om nya artefakt typer



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
