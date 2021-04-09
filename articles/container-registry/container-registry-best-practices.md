---
title: Registrera metodtips
description: Lär dig att använda Azure Container Registry på ett effektivt sätt genom att följa dessa bästa metoder.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 01c8c7f547be9dd225022fb3315a4bdecc48c2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578130"
---
# <a name="best-practices-for-azure-container-registry"></a>Bästa metoder för Azure Container Registry

Genom att följa dessa rekommendationer kan du maximera prestanda och kostnads effektiv användning av ditt privata register i Azure för att lagra och distribuera behållar avbildningar och andra artefakter.

För bakgrund i register koncept, se [om register, databaser och avbildningar](container-registry-concepts.md). Se även [rekommendationer för taggning och versions behållar avbildningar](container-registry-image-tag-version.md) för strategier till tagg-och versions avbildningar i registret. 

## <a name="network-close-deployment"></a>Nätverksnära distribution

Skapa containerregistret i samma Azure-region som du distribuerar containrar i. Genom att placera registret i en region som är nätverksnära till containervärdarna kan du bidra till att sänka både svarstid och kostnader.

Nätverksnära distribution är en av de främsta orsakerna till att använda ett privat containerregister. Docker-avbildningar har en effektiv [lagerkonstruktion](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) som möjliggör inkrementella distributioner. Nya noder måste dock hämta alla lager som krävs för en viss avbildning. Den första `docker pull` kan snabbt växa till flera gigabyte. Med ett privat register nära distributionen minimeras nätverkssvarstiden.
Dessutom implementerar alla offentliga moln, inklusive Azure, avgifter för nätverksöverskridande. Om avbildningar hämtas från ett datacenter till ett annat läggs avgifter för nätverksöverskridande till, utöver svarstiden.

## <a name="geo-replicate-multi-region-deployments"></a>Geo-replikering av distributioner över flera regioner

Använd funktionen [geo-replikering](container-registry-geo-replication.md) i Azure Container Registry om du distribuerar behållare till flera regioner. Oavsett om du arbetar med globala kunder från lokala datacenter eller om utvecklingsteamet finns på olika platser kan du förenkla registerhanteringen och minimera svarstiden genom geo-replikering i registret. Du kan också konfigurera regionala [webhookar](container-registry-webhook.md) för att meddela dig om händelser i vissa repliker, till exempel när avbildningar skickas.

Geo-replikering är tillgängligt med [Premium](container-registry-skus.md) register. Information om hur du använder geo-replikering finns i självstudien [Geo-replication in Azure Container Registry](container-registry-tutorial-prepare-registry.md) (Geo-replikering i Azure Container Registry) i tre delar.

## <a name="maximize-pull-performance"></a>Maximera prestanda för hämtning

Förutom att placera bilder nära dina distributioner kan egenskaperna för dina avbildningar påverka hämtnings prestanda.

* **Bild storlek** – minimera storleken på dina bilder genom att ta bort onödiga [lager](container-registry-concepts.md#manifest) eller minska storleken på lagren. Ett sätt att minska bild storleken är att använda Docker-metoden för [flera steg](https://docs.docker.com/develop/develop-images/multistage-build/) för att bara inkludera nödvändiga körnings komponenter. 

  Kontrol lera också om avbildningen kan innehålla en ljusare bas operativ system avbildning. Om du använder en distributions miljö, till exempel Azure Container Instances som cachelagrar vissa bas avbildningar, kontrollerar du om du kan växla ett bild lager för en av de cachelagrade avbildningarna. 
* **Antal lager** – balansera antalet lager som används. Om du har för lite kan du inte dra nytta av lagrets åter användning och cachelagring på värden. För många och distributions miljön ägnar mer tid åt att dra och expandera. Fem till 10 lager är optimala.

Välj också en [service nivå](container-registry-skus.md) med Azure Container Registry som uppfyller dina prestanda behov. Premium-nivån ger den största bandbredden och den högsta mängden samtidiga Läs-och skriv åtgärder när du har stora distributioner av volymer.

## <a name="repository-namespaces"></a>Namnrymder för lagringsplatser

Genom att använda databas namn rymder kan du dela ett enda register över flera grupper i din organisation. Registren kan delas mellan distributioner och team. Azure Container Registry har stöd för kapslade namnrymder, vilket möjliggör isolering av grupper. Registret hanterar dock alla databaser oberoende av varandra, inte som en hierarki.

Du kan till exempel överväga följande taggar för containeravbildningar. Bilder som används i hela företaget, till exempel `aspnetcore` , placeras i rot namn området, medan behållar avbildningar som ägs av produkterna och marknadsförings grupperna använder sina egna namn rymder.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedikerad resursgrupp

Eftersom behållar register är resurser som används över flera behållar värdar bör ett register finnas i en egen resurs grupp.

Även om du kan experimentera med en speciell värd typ, till exempel [Azure Container instances](../container-instances/container-instances-overview.md), vill du förmodligen ta bort behållar instansen när du är klar. Du kan dock även vilja behålla avbildningssamlingen som du överförde till Azure Container Registry. Genom att placera registret i en egen resursgrupp minimerar du risken för att oavsiktligen ta bort avbildningssamlingen i registret när du tar bort containerinstansens resursgrupp.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

Vid autentisering med ett Azure-containerregister finns det två huvudsakliga scenarier: individuell autentisering och tjänstautentisering (eller "fjärradministrering"). Följande tabell innehåller en kort översikt över dessa scenarier och den rekommenderade metoden för autentisering av dem.

| Typ | Exempelscenario | Rekommenderad metod |
|---|---|---|
| Individuell identitet | En utvecklare hämtar avbildningar till eller skickar bilder från sin egen utvecklingsdator. | [az acr login](/cli/azure/acr#az-acr-login) |
| Fjärradministrerad/tjänstidentitet | Bygg- och distributionsledningar där användaren inte är direkt inblandad. | [Tjänstens huvudnamn](container-registry-authentication.md#service-principal) |

Detaljerad information om dessa och andra Azure Container Registry-autentiseringar scenarier finns i [autentisera med ett Azure Container Registry](container-registry-authentication.md).

Azure Container Registry stöder säkerhets rutiner i din organisation för att distribuera uppgifter och privilegier för olika identiteter. Använd [rollbaserad åtkomst kontroll](container-registry-roles.md), tilldela lämpliga behörigheter till olika användare, tjänstens huvud namn eller andra identiteter som utför olika register åtgärder. Du kan till exempel tilldela push-behörigheter till ett huvud namn för tjänsten som används i en build-pipeline och tilldela pull-behörigheter till en annan identitet som används för distribution. Skapa [token](container-registry-repository-scoped-permissions.md) för detaljerad, tidsbegränsad åtkomst till vissa databaser.

## <a name="manage-registry-size"></a>Hantera registerstorlek      

Lagrings begränsningarna för varje [behållar register tjänst nivå][container-registry-skus] är avsedda att överensstämma med ett typiskt scenario: **Basic** för att komma igång, **standard** för de flesta produktions program och **Premium** för Hyper-Scale-prestanda och [geo-replikering][container-registry-geo-replication]. Du bör hantera registrets storlek genom att regelbundet ta bort innehåll som inte används under hela registrets livslängd.

Använd Azure CLI-kommandot [AZ ACR show-Usage][az-acr-show-usage] för att visa den aktuella storleken på ditt register:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Du kan också hitta det aktuella lagrings utrymmet som används i **översikten** över registret i Azure Portal:

![Information om registeranvändning i Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Ta bort avbildnings data

Azure Container Registry stöder flera metoder för att ta bort avbildnings data från behållar registret. Du kan ta bort avbildningar efter tagg eller manifest sammandrag eller ta bort en hel lagrings plats.

Mer information om hur du tar bort bilddata från registret, inklusive otaggade (som ibland kallas "Dangling" eller "överblivna") avbildningar, finns i [ta bort behållar avbildningar i Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Nästa steg

Azure Container Registry finns på flera nivåer (kallas även SKU: er) som tillhandahåller olika funktioner. Mer information om tillgängliga tjänst nivåer finns i [Azure Container Registry tjänst nivåer](container-registry-skus.md).

Rekommendationer för att förbättra säkerhets position i dina behållar register finns i [Azures säkerhets bas linje för Azure Container Registry](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md