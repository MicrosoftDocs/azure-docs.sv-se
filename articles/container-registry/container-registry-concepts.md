---
title: Om register, lagringsplatsen, avbildningar och artefakter
description: Introduktion till viktiga begrepp för Azure-containerregister, lagringsplatsen, containeravbildningar och andra artefakter.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 64ab3812b3f23a7b3a480d3530c82bd39f2d29a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784091"
---
# <a name="about-registries-repositories-and-artifacts"></a>Om register, lagringsplatsen och artefakter

Den här artikeln beskriver viktiga begrepp för containerregister, lagringsplatsen, containeravbildningar och relaterade artefakter. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Register, lagringsplatsen och artefakter":::

## <a name="registry"></a>Register

Ett *containerregister* är en tjänst som lagrar och distribuerar containeravbildningar och relaterade artefakter. Docker Hub är ett exempel på ett offentligt containerregister som fungerar som en allmän katalog med Docker-containeravbildningar. Azure Container Registry ger användarna direkt kontroll över sitt containerinnehåll, med integrerad autentisering, [geo-replikering](container-registry-geo-replication.md) som stöder global distribution och tillförlitlighet för nätverks stäng distributioner, konfiguration av virtuella nätverk [med Private Link,](container-registry-private-link.md) [tagglåsning](container-registry-image-lock.md)och många andra förbättrade funktioner. 

Förutom Docker-kompatibla containeravbildningar stöder Azure Container Registry [](container-registry-image-formats.md) en mängd innehållsartefakter, inklusive Helm-diagram och OCI-avbildningsformat (Open Container Initiative).

## <a name="repository"></a>Lagringsplats

En *lagringsplats* är en samling containeravbildningar eller andra artefakter i ett register som har samma namn, men olika taggar. Följande tre avbildningar finns till exempel på `acr-helloworld` lagringsplatsen:

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Lagringsplatsnamn kan också [innehålla namnrymder](container-registry-best-practices.md#repository-namespaces). Med namnrymder kan du identifiera relaterade lagringsplatsen och ägarskapet för artefakter i din organisation med hjälp av snedstrecksavgränsade namn. Registret hanterar dock alla lagringsplatsen oberoende av varandra, inte som en hierarki. Exempel:

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

Lagringsplatsens namn får bara innehålla gemena alfanumeriska tecken, punkter, bindestreck, understreck och snedstreck. 

Fullständiga namngivningsregler för lagringsplatsen finns i [Open Container Initiative Distribution Specification](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Artefakt

En containeravbildning eller annan artefakt i ett register är associerad med en eller flera taggar, har ett eller flera lager och identifieras av ett manifest. Att förstå hur dessa komponenter är relaterade till varandra kan hjälpa dig att hantera registret på ett effektivt sätt.

### <a name="tag"></a>Tagg

*Taggen* för en avbildning eller annan artefakt anger dess version. En enskild artefakt i en lagringsplats kan tilldelas en eller flera taggar och kan också vara "otaggad". Det innebär att du kan ta bort alla taggar från en avbildning, medan avbildningens data (dess lager) finns kvar i registret.

Lagringsplatsen (eller lagringsplatsen och namnområdet) plus en tagg definierar en avbildnings namn. Du kan skicka och hämta en avbildning genom att ange dess namn i push- eller pull-åtgärden. Taggen `latest` används som standard om du inte anger någon i dina Docker-kommandon.

Hur du taggar containeravbildningar vägleds av dina scenarier för att utveckla eller distribuera dem. Stabila taggar rekommenderas till exempel för att underhålla dina basavbildningar och unika taggar för distribution av avbildningar. Mer information finns i [Rekommendationer för taggning och versionshantering av containeravbildningar.](container-registry-image-tag-version.md)

Information om namngivningsregler för taggar finns i [Docker-dokumentationen.](https://docs.docker.com/engine/reference/commandline/tag/)

### <a name="layer"></a>Skikt

Containeravbildningar och artefakter består av ett eller flera *lager.* Olika artefakttyper definierar lager på olika sätt. I en Docker-containeravbildning motsvarar till exempel varje lager en rad i Dockerfile som definierar avbildningen:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Lager i en containeravbildning":::

Artefakter i ett register delar gemensamma lager, vilket ökar lagringseffektiviteten. Flera avbildningar i olika lagringslager kan till exempel ha ett ASP.NET Core-basskikt, men endast en kopia av det lagret lagras i registret. Lagerdelning optimerar också lagerfördelningen till noder, med flera artefakter som delar gemensamma lager. Om en avbildning som redan finns på en nod innehåller ASP.NET Core-lagret som bas, överför efterföljande flyttning av en annan avbildning som refererar till samma lager inte lagret till noden. I stället refererar den till det lager som redan finns på noden.

Lager delas inte mellan register för att ge säker isolering och skydd mot potentiell lagermanipulering.

### <a name="manifest"></a>Manifest

Varje containeravbildning eller artefakt som skickas till ett containerregister associeras med ett *manifest*. Manifestet, som genereras av registret när innehållet push-skickas, identifierar artefakterna unikt och anger lagren. Du kan visa manifesten för en lagringsplats med Azure CLI-kommandot [az acr repository show-manifests][az-acr-repository-show-manifests]. 

Ett grundläggande manifest för en `hello-world` Linux-avbildning ser ut ungefär så här:

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

Du kan visa manifesten för en lagringsplats med Azure CLI-kommandot [az acr repository show-manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Visa till exempel manifesten för lagringsplatsen "acr-helloworld":

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

### <a name="manifest-digest"></a>Manifestet sammanfattas

Manifest identifieras med en unik SHA-256-hash eller *manifestet digest*. Varje avbildning eller artefakt – oavsett om den är taggad eller inte – identifieras av sammanfattningen. Det sammanfattade värdet är unikt även om artefaktens lagerdata är identiska med den för en annan artefakt. Med den här mekanismen kan du skicka identiskt taggade avbildningar upprepade gånger till ett register. Du kan till exempel skicka upprepade `myimage:latest` gånger till registret utan fel eftersom varje avbildning identifieras av dess unika sammanfattning.

Du kan hämta en artefakt från ett register genom att ange dess sammanfattning i pull-åtgärden. Vissa system kan konfigureras för att hämta enligt sammanfattning eftersom det garanterar att avbildningsversionen hämtas, även om en identiskt taggad avbildning skickas senare till registret.

> [!IMPORTANT]
> Om du upprepade gånger push-pushar ändrade artefakter med identiska taggar kan du skapa "överblivna" artefakter som inte har taggats, men som fortfarande förbrukar utrymme i registret. Otaggade bilder visas inte i Azure CLI eller i Azure Portal när du listar eller visar bilder efter tagg. Deras lager finns dock fortfarande och förbrukar utrymme i registret. Om du tar bort en otaggad avbildning frigörs registerutrymmet när manifestet är det enda, eller det sista, som pekar på ett visst lager. Information om hur du frigör utrymme som används av otaggade avbildningar finns i [Ta bort containeravbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Hantera en artefakt

Om du vill åtgärda en registerartefakt för push- och pull-åtgärder med Docker eller andra klientverktyg kombinerar du det fullständigt kvalificerade registernamnet, lagringsplatsens namn (inklusive namnområdessökvägen om tillämpligt) och en artefakttagg eller manifest sammanfattad. Se föregående avsnitt för förklaringar av dessa termer.

  **Adress efter tagg**: `[loginServerUrl]/[repository][:tag]`
    
  **Adress efter sammanfattad**: `[loginServerUrl]/[repository@sha256][:digest]`  

När du använder Docker eller andra klientverktyg för att hämta eller skicka artefakter till ett Azure-containerregister ska du använda registrets fullständigt kvalificerade URL, som även kallas *inloggningsservernamnet.* I Azure-molnet har det fullständigt kvalificerade URL:en för ett Azure-containerregister formatet `myregistry.azurecr.io` (endast gemener).

> [!NOTE]
> * Du kan inte ange ett portnummer i registrets inloggningsserver-URL, till exempel `myregistry.azurecr.io:443` . 
> * Taggen `latest` används som standard om du inte anger en tagg i kommandot.  

   
### <a name="push-by-tag"></a>Push-by-tagg

Exempel: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Hämta efter tagg

Exempel: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Hämta efter manifest


Exempel:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Nästa steg

Läs mer om [registerlagring och](container-registry-storage.md) [innehållsformat som stöds](container-registry-image-formats.md) i Azure Container Registry.

Lär dig hur [du push-pushar och hämtar](container-registry-get-started-docker-cli.md) avbildningar Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
