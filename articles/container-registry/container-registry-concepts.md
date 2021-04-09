---
title: Om register, databaser, avbildningar och artefakter
description: Introduktion till viktiga begrepp för Azure Container register, databaser, behållar avbildningar och andra artefakter.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578094"
---
# <a name="about-registries-repositories-and-artifacts"></a>Om register, databaser och artefakter

Den här artikeln beskriver viktiga begrepp för behållar register, lagrings platser och behållar avbildningar och relaterade artefakter. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Register, databaser och artefakter":::

## <a name="registry"></a>Register

Ett behållar *register* är en tjänst som lagrar och distribuerar behållar avbildningar och relaterade artefakter. Docker Hub är ett exempel på ett offentligt behållar register som fungerar som en allmän katalog med Docker-behållar avbildningar. Azure Container Registry ger användare direkt kontroll över sitt behållar innehåll, med integrerad autentisering, [geo-replikering](container-registry-geo-replication.md) stöder global distribution och tillförlitlighet för nätverks nära distributioner, [konfiguration av virtuellt nätverk med privat länk](container-registry-private-link.md), [tagg låsning](container-registry-image-lock.md)och många andra förbättrade funktioner. 

Förutom Docker-kompatibla behållar avbildningar har Azure Container Registry stöd för en mängd [innehålls artefakter](container-registry-image-formats.md) , inklusive Helm-diagram och OCI-bildformat (Open container Initiative).

## <a name="repository"></a>Lagringsplats

En *lagrings plats* är en samling behållar avbildningar eller andra artefakter i ett register som har samma namn men olika taggar. Följande tre avbildningar finns till exempel i `acr-helloworld` databasen:

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

## <a name="artifact"></a>Artefakt

En behållar avbildning eller en annan artefakt i ett register är kopplad till en eller flera taggar, har ett eller flera skikt, och identifieras av ett manifest. Att förstå hur dessa komponenter är relaterade till varandra kan hjälpa dig att hantera registret effektivt.

### <a name="tag"></a>Tagg

*Taggen* för en bild eller en annan artefakt anger dess version. En enda artefakt i en lagrings plats kan tilldelas en eller flera taggar, och kan också vara "omärkt". Det innebär att du kan ta bort alla Taggar från en bild, medan bildens data (dess lager) finns kvar i registret.

Lagrings platsen (eller lagrings platsen och namn området) plus en tagg definierar namnet på en avbildning. Du kan push-överföra och hämta en avbildning genom att ange dess namn i push-eller pull-åtgärden. Taggen `latest` används som standard om du inte anger någon i Docker-kommandona.

Hur du taggar behållar avbildningar vägleds av dina scenarier för att utveckla eller distribuera dem. Till exempel rekommenderas stabila taggar för att underhålla dina bas avbildningar och unika taggar för att distribuera avbildningar. Mer information finns i [rekommendationer för taggning och versions behållar avbildningar](container-registry-image-tag-version.md).

För namngivnings regler för taggar, se [Docker-dokumentationen](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Skikt

Behållar avbildningar och artefakter består av ett eller flera *lager*. Olika artefakt typer definierar lager på olika sätt. I en Docker-behållar avbildning motsvarar till exempel varje lager en rad i Dockerfile som definierar avbildningen:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Lager i en behållar avbildning":::

Artefakter i ett register som delar gemensamma lager, vilket ökar lagrings effektiviteten. Till exempel kan flera avbildningar i olika lagrings platser ha ett vanligt ASP.NET Core grund lager, men endast en kopia av det lagret lagras i registret. Lager delning optimerar också lager distribution till noder, med flera artefakter som delar gemensamma lager. Om en avbildning som redan finns på en nod innehåller ASP.NET Core lagret som bas, överförs inte lagret till noden när den efterföljande hämtningen av en annan bild som refererar till samma lager. I stället refererar den till det lager som redan finns på noden.

För att tillhandahålla säker isolering och skydd från potentiell lager hantering delas inte lagren mellan register.

### <a name="manifest"></a>Manifest

Varje behållar avbildning eller artefakt som flyttas till ett behållar register är associerad med ett *manifest*. Manifestet som genereras av registret när innehållet skickas identifierar unikt artefakterna och anger lagren. Du kan visa manifesten för en lagrings plats med Azure CLI [-kommandot AZ ACR-lagringsplats show-manifest][az-acr-repository-show-manifests]. 

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
    "timestamp&quot;: &quot;2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp&quot;: &quot;2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp&quot;: &quot;2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Manifest Sammanfattning

Manifest identifieras av en unik SHA-256-hash eller *manifest sammandrag*. Varje bild eller artefakt – om Taggad eller inte--har identifierats av sammanfattningen. Digest-värdet är unikt även om artefaktens lager data är identiska med det för en annan artefakt. Den här metoden gör det möjligt att upprepade gånger skicka identiska märkta bilder till ett register. Du kan t. ex. skicka flera gånger `myimage:latest` till registret utan fel eftersom varje avbildning identifieras med en unik Sammanfattning.

Du kan hämta en artefakt från ett register genom att ange dess Digest i pull-åtgärden. Vissa system kan konfigureras att hämta genom sammandrag eftersom det garanterar att avbildnings versionen hämtas, även om en identiskt taggad bild skickas till registret senare.

> [!IMPORTANT]
> Om du upprepade gånger skickar ändrade artefakter med identiska taggar kan du skapa "överblivna"--artefakter som är otaggade, men fortfarande använda utrymme i registret. Otaggade bilder visas inte i Azure CLI eller i Azure Portal när du listar eller visar bilder efter tagg. Men deras lager finns fortfarande kvar och tar upp utrymme i registret. När du tar bort en otaggade bild frigörs register utrymmet när manifestet är det enda, eller det sista, som pekar på ett visst lager. Information om hur du frigör utrymme som används av otaggade bilder finns i [ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Adressera en artefakt

Om du vill ta itu med en register artefakt för push-och pull-åtgärder med Docker eller andra klient verktyg kombinerar du det fullständigt kvalificerade register namnet, databas namnet (inklusive sökväg till namn området om tillämpligt) och en artefakt tagg eller manifest Sammanfattning. I föregående avsnitt finns förklaringar av de här villkoren.

  **Adress efter tagg**: `[loginServerUrl]/[repository][:tag]`
    
  **Adress efter sammandrag**: `[loginServerUrl]/[repository@sha256][:digest]`  

När du använder Docker eller andra klient verktyg för att hämta eller skicka artefakter till ett Azure Container Registry, använder du registrets fullständigt kvalificerade URL, även kallat *inloggnings Server* namnet. I Azure-molnet är den fullständigt kvalificerade URL: en för ett Azure Container Registry i formatet `myregistry.azurecr.io` (alla gemener).

> [!NOTE]
> * Du kan inte ange ett port nummer i URL: en för inloggnings servern för registret, till exempel `myregistry.azurecr.io:443` . 
> * Taggen `latest` används som standard om du inte anger en tagg i kommandot.  

   
### <a name="push-by-tag"></a>Push by-tagg

Exempel: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Hämta efter tagg

Exempel: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Hämta med manifest Sammanfattning


Exempel:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Nästa steg

Läs mer om [register lagring](container-registry-storage.md) och [innehålls format som stöds](container-registry-image-formats.md) i Azure Container Registry.

Lär dig att [skicka och ta emot bilder](container-registry-get-started-docker-cli.md) från Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


