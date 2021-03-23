---
title: Avbildningar med flera arkitektur i registret
description: Använd Azure Container Registry för att skapa, importera, lagra och distribuera multi-Architecture-avbildningar (Multi-Architecture)
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802462"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Avbildningar med flera arkitekturer i Azure Container Registry

Den här artikeln beskriver *multi* -Architecture *-* avbildningar och hur du kan använda Azure Container Registry funktioner för att skapa, lagra och använda dem. 

En bild med flera bilder är en typ av behållar avbildning som kan kombinera varianter för olika arkitekturer och ibland för olika operativ system. När du kör en avbildning med stöd för flera arkitekturer väljer behållar klienterna automatiskt en avbildnings-variant som matchar ditt operativ system och din arkitektur.

## <a name="manifests-and-manifest-lists"></a>Manifest och manifest listor

Bilder med flera bågar baseras på bild manifest och manifest listor.

### <a name="manifest"></a>Manifest

Varje behållar avbildning representeras av ett [manifest](container-registry-concepts.md#manifest). Ett manifest är en JSON-fil som unikt identifierar avbildningen och som refererar till dess lager och deras motsvarande storlekar. 

Ett grundläggande manifest för en Linux- `hello-world` avbildning ser ut ungefär så här:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Du kan visa ett manifest i Azure Container Registry med hjälp av Azure Portal eller verktyg, till exempel kommandot [AZ ACR-lagringsplatsen show-manifestes](/cli/azure/acr/repository#az_acr_repository_show_manifests) i Azure CLI.

### <a name="manifest-list"></a>Manifest lista

En *manifest lista* för en bild med flera bilder (allmänt sett som ett [bild index](https://github.com/opencontainers/image-spec/blob/master/image-index.md) för OCI-bilder) är en samling (index) av bilder och du skapar en genom att ange ett eller flera avbildnings namn. Den innehåller information om var och en av de avbildningar som stöds, till exempel operativ system och arkitektur, storlek och manifest Sammanfattning. Manifest listan kan användas på samma sätt som ett avbildnings namn i och- `docker pull` `docker run` kommandon. 

`docker`CLI hanterar manifest och manifest listor med hjälp av kommandot [Docker manifest](https://docs.docker.com/engine/reference/commandline/manifest/) .

> [!NOTE]
> `docker manifest`Kommandot och under kommandona är för närvarande experimentella. I Docker-dokumentationen finns information om hur du använder experimentella kommandon.

Du kan visa en manifest lista med hjälp av `docker manifest inspect` kommandot. Följande är utdata för bilden med flera bågar `mcr.microsoft.com/mcr/hello-world:latest` , som har tre manifest: två för Linux OS-arkitekturer och en för en Windows-arkitektur.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

När en manifest lista för flera bågar lagras i Azure Container Registry kan du också Visa manifest listan med hjälp av Azure Portal eller med verktyg som [AZ ACR-lagringsplatsen show-Manifests-](/cli/azure/acr/repository#az_acr_repository_how_manifests) kommandot.

## <a name="import-a-multi-arch-image"></a>Importera en bild med flera bågar 

En befintlig bild med flera bilder kan importeras till ett Azure Container Registry med hjälp av kommandot [AZ ACR import](/cli/azure/acr#az_acr_import) . Syntaxen för bild import är samma som med en avbildning med en enda arkitektur. Import av en bild med flera bilder använder inte Docker-kommandon om du vill importera en avbildning med en enda arkitektur. 

Mer information finns i [Importera behållar avbildningar till ett behållar register](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Push-överför en bild med flera bågar

När du har skapat arbets flöden för att skapa behållar avbildningar för olika arkitekturer, följer du dessa steg för att skicka en avbildning med flera bilder till Azure Container Registry.

1. Tagga och skicka varje arkitektur bestämd avbildning till behållar registret. Följande exempel förutsätter två Linux-arkitekturer: arm64 och amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Kör `docker manifest create` för att skapa en manifest lista för att kombinera föregående bilder till en bild med flera bilder.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Skicka manifestet till behållar registret med `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Använd `docker manifest inspect` kommandot för att Visa manifest listan. Ett exempel på kommandoutdata visas i föregående avsnitt.

När du har push-överfört manifestet med flera bågar till ditt register, arbetar du med en bild med flera bilder på samma sätt som du gör med en avbildning med en enda arkitektur. Hämta till exempel avbildningen med hjälp av `docker pull` och Använd [AZ ACR](/cli/azure/acr/repository#az_acr_repository) för att Visa taggar, manifest och andra egenskaper för avbildningen.

## <a name="build-and-push-a-multi-arch-image"></a>Bygga och skicka en bild med flera bilder

Med hjälp av funktionerna i [ACR-uppgifter](container-registry-tasks-overview.md)kan du bygga och push-överföra en avbildning med flera bilder till ditt Azure Container Registry. Definiera till exempel en [aktivitet med flera steg](container-registry-tasks-multi-step.md) i en yaml-fil som skapar en Linux-avbildning med flera bilder.

I följande exempel förutsätter vi att du har separata Dockerfiles för två arkitekturer, arm64 och amd64. Den skapar och pushrar de arkitekturbaserade avbildningarna och skapar och skickar sedan ett manifest med flera bågar som har `latest` taggen:

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Nästa steg

* Använd [Azure-pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) för att bygga behållar avbildningar för olika arkitekturer.
* Lär dig mer om att skapa avbildningar med flera plattformar med hjälp av experimentell Docker [buildx](https://docs.docker.com/buildx/working-with-buildx/) -plugin-programmet.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
