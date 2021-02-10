---
title: Om databaser & avbildningar
description: Introduktion till viktiga begrepp för Azure Container register,-databaser och behållar avbildningar.
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: 0cc7df22236c60bd473385d92c8db563be68f688
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008527"
---
# <a name="about-registries-repositories-and-images"></a>Om register, databaser och avbildningar

Den här artikeln beskriver viktiga begrepp för behållar register, lagrings platser och behållar avbildningar och relaterade artefakter. 

## <a name="registry"></a>Register

Ett behållar *register* är en tjänst som lagrar och distribuerar behållar avbildningar. Docker Hub är ett offentligt behållar register som stöder communityn för öppen källkod och fungerar som en allmän katalog med avbildningar. Azure Container Registry ger användare direkt kontroll över sina avbildningar, med integrerad autentisering, [geo-replikering](container-registry-geo-replication.md) stöder global distribution och tillförlitlighet för nätverks nära distributioner, [konfiguration av virtuella nätverk och brand väggar](container-registry-vnet.md), [tagg låsning](container-registry-image-lock.md)och många andra förbättrade funktioner. 

Förutom Docker-behållar avbildningar stöder Azure Container Registry relaterade [innehålls artefakter](container-registry-image-formats.md) , inklusive OCI-bildformat (Open container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Innehålls adresser bara element i en artefakt

Adressen till en artefakt i ett Azure Container Registry innehåller följande element. 

`[loginUrl]/[repository:][tag]`

* **loginUrl** – det fullständigt kvalificerade namnet på register värden. Register värden i ett Azure Container Registry har formatet *register*. azurecr.IO (alla gemener). Du måste ange loginUrl när du använder Docker eller andra klient verktyg för att hämta eller push-artefakter till ett Azure Container Registry. 
* **databas** namn för en logisk gruppering av en eller flera relaterade bilder eller artefakter, till exempel bilder för ett program eller ett bas operativ system. Kan innehålla sökväg till *namn område* . 
* **tagga** -ID för en angiven version av en bild eller artefakt som lagras i en lagrings plats.

Till exempel kan det fullständiga namnet på en avbildning i ett Azure Container Registry se ut så här:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

I följande avsnitt finns mer information om de här elementen.

## <a name="repository-name"></a>Namn på databas

En *lagrings plats* är en samling behållar avbildningar eller andra artefakter med samma namn, men olika taggar. Följande tre avbildningar finns till exempel i databasen "ACR-HelloWorld":


- *ACR-HelloWorld: senaste*
- *ACR-HelloWorld: v1*
- *ACR-HelloWorld: v2*

Namn på databaser kan även innehålla [namn områden](container-registry-best-practices.md#repository-namespaces). Med namn områden kan du identifiera relaterade databaser och artefakt ägarskap i organisationen med hjälp av snedstreck-avgränsade namn. Registret hanterar dock alla databaser oberoende av varandra, inte som en hierarki. Exempel:

- *Marketing/campaign10-18/webb: v2*
- *Marketing/campaign10-18/API: v3*
- *Marketing/campaign10-18/e-post-Sender: v2*
- *produkt returer/webb sändning: 20180604*
- *produkt retur/Legacy-Integrator: 20180715*

Namn på databaser får bara innehålla gemena alfanumeriska tecken, punkter, bindestreck, under streck och snedstreck. 

För fullständiga namngivnings regler för databaser, se [distributions specifikationen Open container Initiative](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="image"></a>Bild

En behållar avbildning eller en annan artefakt i ett register är kopplad till en eller flera taggar, har ett eller flera skikt, och identifieras av ett manifest. Att förstå hur dessa komponenter är relaterade till varandra kan hjälpa dig att hantera registret effektivt.

### <a name="tag"></a>Tagg

*Taggen* för en bild eller en annan artefakt anger dess version. En enda artefakt i en lagrings plats kan tilldelas en eller flera taggar, och kan också vara "omärkt". Det innebär att du kan ta bort alla Taggar från en bild, medan bildens data (dess lager) finns kvar i registret.

Lagrings platsen (eller lagrings platsen och namn området) plus en tagg definierar namnet på en avbildning. Du kan push-överföra och hämta en avbildning genom att ange dess namn i push-eller pull-åtgärden. Taggen `latest` används som standard om du inte anger någon i Docker-kommandona.

Hur du taggar behållar avbildningar vägleds av dina scenarier för att utveckla eller distribuera dem. Till exempel rekommenderas stabila taggar för att underhålla dina bas avbildningar och unika taggar för att distribuera avbildningar. Mer information finns i [rekommendationer för taggning och versions behållar avbildningar](container-registry-image-tag-version.md).

För namngivnings regler för taggar, se [Docker-dokumentationen](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Skikt

Behållar avbildningar består av ett eller flera *lager*, var och en som motsvarar en linje i Dockerfile som definierar bilden. Avbildningar i ett register delar gemensamma lager, vilket ökar lagrings effektiviteten. Till exempel kan flera avbildningar i olika lagrings platser dela samma Alpine Linux-basadress, men endast en kopia av det lagret lagras i registret.

Lager delning optimerar också lager distribution till noder med flera bilder som delar gemensamma lager. Om en avbildning som redan finns på en nod t. ex. inkluderar Alpine Linux-lagret som bas, överförs inte lagret till noden när den efterföljande hämtningen av en annan bild som refererar till samma lager. I stället refererar den till det lager som redan finns på noden.

För att tillhandahålla säker isolering och skydd från potentiell lager hantering delas inte lagren mellan register.

### <a name="manifest"></a>Manifest

Varje behållar avbildning eller artefakt som flyttas till ett behållar register är associerad med ett *manifest*. Manifestet, som genereras av registret när avbildningen skickas, identifierar avbildningen unikt och anger dess lager. 

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

Du kan visa manifesten för en lagrings plats med Azure CLI [-kommandot AZ ACR-lagringsplats show-manifest][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Ange till exempel manifesten för databasen "ACR-HelloWorld":

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Manifest Sammanfattning

Manifest identifieras av en unik SHA-256-hash eller *manifest sammandrag*. Varje bild eller artefakt – om Taggad eller inte--har identifierats av sammanfattningen. Digest-värdet är unikt även om avbildningens lager data är identiska med en annan bild. Den här metoden gör det möjligt att upprepade gånger skicka identiska märkta bilder till ett register. Du kan t. ex. skicka flera gånger `myimage:latest` till registret utan fel eftersom varje avbildning identifieras med en unik Sammanfattning.

Du kan hämta en avbildning från ett register genom att ange dess Digest i pull-åtgärden. Vissa system kan konfigureras att hämta genom sammandrag eftersom det garanterar att avbildnings versionen hämtas, även om en identiskt taggad bild sedan skickas till registret.

Hämta till exempel en bild från lagrings platsen "ACR-HelloWorld" genom manifest sammandrag:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Om du ofta skickar ändrade bilder med identiska taggar kan du skapa överblivna bilder – bilder som är otaggade, men som fortfarande använder utrymme i registret. Otaggade bilder visas inte i Azure CLI eller i Azure Portal när du listar eller visar bilder efter tagg. Men deras lager finns fortfarande kvar och tar upp utrymme i registret. När du tar bort en otaggade bild frigörs register utrymmet när manifestet är det enda, eller det sista, som pekar på ett visst lager. Information om hur du frigör utrymme som används av otaggade bilder finns i [ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Nästa steg

Läs mer om [bild lagring](container-registry-storage.md) och [innehålls format som stöds](container-registry-image-formats.md) i Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


