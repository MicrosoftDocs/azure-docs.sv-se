---
title: Registrera metodtips
description: Lär dig att använda Azure Container Registry på ett effektivt sätt genom att följa dessa bästa metoder.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 0811cc4a5bffc21ffba19e64a3887eab6bc36fbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784145"
---
# <a name="best-practices-for-azure-container-registry"></a>Bästa metoder för Azure Container Registry

Genom att följa dessa metodtips kan du maximera prestanda och kostnadseffektiv användning av ditt privata register i Azure för att lagra och distribuera containeravbildningar och andra artefakter.

Bakgrundsinformation om registerbegrepp finns [i Om register, lagringsdatabaser och avbildningar.](container-registry-concepts.md) Se även [Recommendations for tagging and versioning container images (Rekommendationer](container-registry-image-tag-version.md) för taggning och versionshantering av containeravbildningar) för strategier för att tagga och versionsavbildningar i registret. 

## <a name="network-close-deployment"></a>Nätverksnära distribution

Skapa containerregistret i samma Azure-region som du distribuerar containrar i. Genom att placera registret i en region som är nätverksnära till containervärdarna kan du bidra till att sänka både svarstid och kostnader.

Nätverksnära distribution är en av de främsta orsakerna till att använda ett privat containerregister. Docker-avbildningar har en effektiv [lagerkonstruktion](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) som möjliggör inkrementella distributioner. Nya noder måste dock hämta alla lager som krävs för en viss avbildning. Den första `docker pull` kan snabbt växa till flera gigabyte. Med ett privat register nära distributionen minimeras nätverkssvarstiden.
Dessutom implementerar alla offentliga moln, inklusive Azure, avgifter för nätverksöverskridande. Om avbildningar hämtas från ett datacenter till ett annat läggs avgifter för nätverksöverskridande till, utöver svarstiden.

## <a name="geo-replicate-multi-region-deployments"></a>Geo-replikering av distributioner över flera regioner

Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Azure Container Registry om du distribuerar behållare till flera regioner. Oavsett om du arbetar med globala kunder från lokala datacenter eller om utvecklingsteamet finns på olika platser kan du förenkla registerhanteringen och minimera svarstiden genom geo-replikering i registret. Du kan också konfigurera regionala [webhooks för](container-registry-webhook.md) att meddela dig om händelser i specifika repliker, till exempel när bilder pushas.

Geo-replikering är tillgängligt [med Premium-register.](container-registry-skus.md) Information om hur du använder geo-replikering finns i självstudien [Geo-replication in Azure Container Registry](container-registry-tutorial-prepare-registry.md) (Geo-replikering i Azure Container Registry) i tre delar.

## <a name="maximize-pull-performance"></a>Maximera pull-prestanda

Förutom att placera avbildningar nära dina distributioner kan egenskaperna för själva avbildningarna påverka pull-prestandan.

* **Bildstorlek** – Minimera bildernas storlek genom att ta bort [onödiga lager](container-registry-concepts.md#manifest) eller minska storleken på skikt. Ett sätt att minska avbildningsstorleken är att använda [Docker-bygget](https://docs.docker.com/develop/develop-images/multistage-build/) i flera steg för att endast inkludera de nödvändiga körningskomponenterna. 

  Kontrollera också om avbildningen kan innehålla en lättare basoperativsystemavbildning. Och om du använder en distributionsmiljö, till exempel Azure Container Instances som cachelagrar vissa basavbildningar, kontrollerar du om du kan byta ett avbildningslager mot en av de cachelagrade avbildningarna. 
* **Antal lager –** Balansera antalet lager som används. Om du har för få kan du inte dra nytta av återanvändning av lager och cachelagring på värden. För många och din distributionsmiljö ägnar mer tid åt att hämta och dekomprimera. Fem till tio lager är optimala.

Välj även en [tjänstnivå](container-registry-skus.md) för Azure Container Registry som uppfyller dina prestandabehov. Premium-nivån ger störst bandbredd och högsta hastighet för samtidiga läs- och skrivåtgärder när du har distributioner med stora volymer.

## <a name="repository-namespaces"></a>Namnrymder för lagringsplatser

Genom att använda namnområden för lagringsplatsen kan du tillåta delning av ett enda register över flera grupper i din organisation. Registren kan delas mellan distributioner och team. Azure Container Registry har stöd för kapslade namnrymder, vilket möjliggör isolering av grupper. Registret hanterar dock alla lagringsplatsen oberoende av varandra, inte som en hierarki.

Du kan till exempel överväga följande taggar för containeravbildningar. Avbildningar som används i hela företaget, t.ex. , placeras i rotnamnrymden, medan containeravbildningar som ägs av produkt- och marknadsföringsgrupperna använder sina `aspnetcore` egna namnrymder.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedikerad resursgrupp

Eftersom containerregister är resurser som används över flera containervärdar bör ett register finnas i en egen resursgrupp.

Även om du kan experimentera med en viss värdtyp, till [exempel Azure Container Instances](../container-instances/container-instances-overview.md), vill du förmodligen ta bort containerinstansen när du är klar. Du kan dock även vilja behålla avbildningssamlingen som du överförde till Azure Container Registry. Genom att placera registret i en egen resursgrupp minimerar du risken för att oavsiktligen ta bort avbildningssamlingen i registret när du tar bort containerinstansens resursgrupp.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

Vid autentisering med ett Azure-containerregister finns det två huvudsakliga scenarier: individuell autentisering och tjänstautentisering (eller "fjärradministrering"). Följande tabell innehåller en kort översikt över dessa scenarier och den rekommenderade metoden för autentisering av dem.

| Typ | Exempelscenario | Rekommenderad metod |
|---|---|---|
| Individuell identitet | En utvecklare hämtar avbildningar till eller skickar bilder från sin egen utvecklingsdator. | [az acr login](/cli/azure/acr#az_acr_login) |
| Fjärradministrerad/tjänstidentitet | Bygg- och distributionsledningar där användaren inte är direkt inblandad. | [Tjänstens huvudnamn](container-registry-authentication.md#service-principal) |

Detaljerad information om dessa och andra scenarier Azure Container Registry autentisering finns i [Autentisera med ett Azure-containerregister.](container-registry-authentication.md)

Azure Container Registry har stöd för säkerhetsrutiner i din organisation för att distribuera uppgifter och behörigheter till olika identiteter. Med [rollbaserad åtkomstkontroll tilldelar](container-registry-roles.md)du lämpliga behörigheter till olika användare, tjänstens huvudnamn eller andra identiteter som utför olika registeråtgärder. Du kan till exempel tilldela push-behörigheter till ett huvudnamn för tjänsten som används i en bygg-pipeline och tilldela pull-behörigheter till en annan identitet som används för distribution. Skapa [token för](container-registry-repository-scoped-permissions.md) mer specifik, tidsbegränsad åtkomst till specifika lagringsplatsen.

## <a name="manage-registry-size"></a>Hantera registerstorlek      

Lagringsbegränsningarna för varje [containerregistertjänstnivå][container-registry-skus] är avsedda att anpassas till ett typiskt scenario: **Basic** för att komma igång, **Standard** för de flesta produktionsprogram och **Premium** för prestanda i hyperskala och [geo-replikering][container-registry-geo-replication]. Du bör hantera registrets storlek genom att regelbundet ta bort innehåll som inte används under hela registrets livslängd.

Använd Azure CLI-kommandot [az acr show-usage][az-acr-show-usage] för att visa registrets aktuella storlek:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Du hittar även den aktuella lagringen som används **i Översikt** över registret i Azure Portal:

![Information om registeranvändning i Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Ta bort bilddata

Azure Container Registry flera metoder för att ta bort avbildningsdata från containerregistret. Du kan ta bort bilder efter tagg eller manifest, eller ta bort en hel lagringsplats.

Mer information om hur du tar bort avbildningsdata från registret, inklusive otaggade (kallas ibland "dinglande" eller "överblivna") avbildningar, finns i Ta bort containeravbildningar [i Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Nästa steg

Azure Container Registry finns i flera nivåer (kallas även SKU:er) som har olika funktioner. Mer information om tillgängliga tjänstnivåer finns i [Azure Container Registry tjänstnivåer.](container-registry-skus.md)

Rekommendationer för att förbättra säkerhetsstatusen för dina containerregister finns i [Azure Security Baseline for Azure Container Registry](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md