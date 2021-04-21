---
title: GEO-replikera ett register
description: Kom igång med att skapa och hantera ett geo-replikerat Azure-containerregister, vilket gör att registret kan hantera flera regioner med regionala repliker med flera original. Geo-replikering är en funktion på Premium-tjänstnivån.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 3e5b064ec37b855186f633677e2b1a3f615a6736
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783871"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replikering i Azure Container Registry

Företag som vill ha en lokal närvaro, eller en direkt säkerhetskopia, väljer att köra tjänster från flera Azure-regioner. Som bästa praxis kan ett containerregister placeras i varje region där avbildningarna körs för att medge nätverksnära åtgärder med snabba och tillförlitliga avbildningslageröverföringar. Geo-replikering gör att ett Azure-containerregister kan fungera som ett enskilt register som betjänar flera regioner med regionala register med flera original. 

Ett georeplikerat register ger följande fördelar:

* Namn på enskilda register, avbildningar och taggar kan användas i flera regioner
* Förbättra prestanda och tillförlitlighet för regionala distributioner med nätverks stäng registeråtkomst
* Minska dataöverföringskostnaderna genom att hämta avbildningslager från ett lokalt, replikerat register i samma region eller i närheten som containervärden
* Enkel hantering av ett register i flera regioner
* Registrets återhämtningsförmåga om ett regionalt avbrott inträffar

> [!NOTE]
> Om du behöver upprätthålla kopior av containeravbildningar i mer än ett Azure-containerregister har Azure Container Registry även stöd för [avbildningsimport](container-registry-import-images.md). Till exempel kan du i ett DevOps-arbetsflöde importera en avbildning från ett utvecklingsregister till ett produktionsregister utan att behöva använda Docker-kommandon.
>

## <a name="example-use-case"></a>Exempel på användningsfall
Contoso kör en webbplats med offentlig närvaro i USA, Kanada och Europa. För att betjäna dessa marknader med lokalt och nätverksnära innehåll kör Contoso [Azure Kubernetes Service](../aks/index.yml)-kluster (AKS) i USA, västra; USA, östra; Kanada, centrala samt Europa, västra. Webbappen, som distribueras som en Docker-avbildning, använder samma kod och avbildning i alla regioner. Innehåll, som är lokalt för den regionen, hämtas från en databas som etableras unikt i varje region. Varje regional distribution har en unik konfiguration för resurser som den lokala databasen.

Utvecklingsteamet finns i Seattle, WA, och använder datacentret för USA, västra.

![Push-överföra till flera register](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Push-överföra till flera register*

Innan Contoso började använda de geo-replikerade funktionerna hade de ett USA-baserat register i USA, västra med ytterligare ett register i Europa, västra. För att betjäna dessa olika regioner push-överförde utvecklingsteamet till två olika register.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Hämta från flera register](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Hämta från flera register*

Typiska utmaningar med flera register är:

* Klustren för USA, västra; USA, östra och Kanada, centrala hämtar alla från registret USA, västra och ådrar sig utgående avgifter när dessa fjärrcontainervärdar hämtar avbildningar från datacenter för USA, västra.
* Utvecklingsteamet måste push-överföra avbildningar till registren för USA, västra och Europa, västra.
* Utvecklingsteamet måste konfigurera och underhålla varje regional distribution med avbildningsnamn som refererar till det lokala registret.
* Registeråtkomst måste konfigureras för varje region.

## <a name="benefits-of-geo-replication"></a>Fördelar med geo-replikering

![Hämta från ett geo-replikerat register](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Användning av funktionen för geo-replikering i Azure Container Registry ger följande fördelar:

* Hantera ett enskilt register i alla regioner: `contoso.azurecr.io`
* Hantera en enda konfiguration av avbildningsdistributioner eftersom alla regioner använder samma avbildnings-URL: `contoso.azurecr.io/public/products/web:1.2`
* Skicka till ett enda register, medan ACR hanterar geo-replikeringen. ACR replikerar bara unika lager, vilket minskar dataöverföringen mellan regioner. 
* Konfigurera regionala [webhooks för](container-registry-webhook.md) att meddela dig om händelser i specifika repliker.
* Tillhandahålla ett register med hög tillgång som är motståndskraftigt mot regionala avbrott.

Azure Container Registry också stöd för [tillgänglighetszoner för](zone-redundancy.md) att skapa ett elastiskt Azure-containerregister med hög tillgänglighet i en Azure-region. Kombinationen av tillgänglighetszoner för redundans inom en region och geo-replikering över flera regioner förbättrar både tillförlitligheten och prestandan för ett register.

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering

Konfiguration av geo-replikering är så enkelt som att klicka på regioner på en karta. Du kan också hantera geo-replikering med hjälp av verktyg som [az acr replication-kommandon](/cli/azure/acr/replication) i Azure CLI eller distribuera ett register som är aktiverat för geo-replikering med [en Azure Resource Manager mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Geo-replikering är en funktion [i Premium-register](container-registry-skus.md). Om ditt register ännu inte är Premium kan du ändra från Basic och Standard till Premium i [Azure-portalen](https://portal.azure.com):

![Växla tjänstnivåer i Azure Portal](media/container-registry-skus/update-registry-sku.png)

För att konfigurera geo-replikering för Premium-registret loggar du in på Azure-portalen på https://portal.azure.com.

Gå till ditt Azure Container Registry och välj **Replikeringar**:

![Replikeringar i användargränssnittet i containerregister i Azure-portalen](media/container-registry-geo-replication/registry-services.png)

En karta visas med alla aktuella Azure-regioner:

 ![Regionskarta i Azure Portal](media/container-registry-geo-replication/registry-geo-map.png)

* Blå sexhörningar representerar aktuella repliker
* Gröna sexhörningar representerar möjliga replikregioner
* Grå sexhörningar representerar Azure-regioner som ännu inte är tillgängliga för replikering

Om du vill konfigurera en replik markerar du en grön sexhörning och väljer **Skapa**:

 ![Skapa replikerings-UI i Azure-portalen](media/container-registry-geo-replication/create-replication.png)

Om du vill konfigurera ytterligare repliker markerar du de gröna sexhörningarna för andra regioner och klickar på **Skapa**.

ACR börjar synkronisera avbildningar mellan de konfigurerade replikerna. När det är klart visar portalen *Klar*. Replikstatus i portalen uppdateras inte automatiskt. Använd uppdateringsknappen om du vill visa uppdaterad status.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Att tänka på när du använder ett geo-replikerat register

* Varje region i ett geo-replikerat register är oberoende när det har ställts in. Azure Container Registry serviceavtal gäller för varje geo-replikerad region.
* När du push-överför eller hämtar avbildningar från ett geo-replikerat register skickar Azure Traffic Manager i bakgrunden begäran till registret i den region som är närmast dig vad gäller nätverksfördröjning.
* När du har push-pushat en avbildning eller tagguppdatering till den närmaste regionen tar det lite tid för Azure Container Registry att replikera manifesten och lagren till de återstående regioner som du har valt. Större avbildningar tar längre tid att replikera än mindre. Avbildningar och taggar synkroniseras i replikeringsregionerna med en eventuell konsekvensmodell.
* Om du vill hantera arbetsflöden som är beroende av push-uppdateringar till ett geo-replikerat register rekommenderar vi att du konfigurerar [webhooks](container-registry-webhook.md) för att svara på push-händelserna. Du kan konfigurera regionala webhooks i ett geo-replikerat register för att spåra push-händelser när de slutförs i de geo-replikerade regionerna.
* För att betjäna blobar som representerar innehållslager använder Azure Container Registry dataslutpunkter. Du kan aktivera [dedikerade dataslutpunkter](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) för ditt register i varje registers geo-replikerade regioner. Dessa slutpunkter tillåter konfiguration av begränsade åtkomstregler för brandväggen. I felsökningssyfte kan du inaktivera routning [till en replikering samtidigt som](#temporarily-disable-routing-to-replication) replikerade data behålls.
* Om du konfigurerar [en](container-registry-private-link.md) privat länk för registret med privata slutpunkter i ett virtuellt nätverk aktiveras dedikerade dataslutpunkter i var och en av de geo-replikerade regionerna som standard. 

## <a name="delete-a-replica"></a>Ta bort en replik

När du har konfigurerat en replik för registret kan du ta bort den när som helst om den inte längre behövs. Ta bort en replik med Azure Portal eller andra verktyg, till exempel [kommandot az acr replication delete](/cli/azure/acr/replication#az_acr_replication_delete) i Azure CLI.

Så här tar du bort en replik i Azure Portal:

1. Gå till Azure Container Registry och välj **Replikeringar.**
1. Välj namnet på en replik och välj Ta **bort.** Bekräfta att du vill ta bort repliken.

Så här använder du Azure CLI för att ta bort en *replik av myregistry* i regionen USA, östra:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Prissättning för Geo-replikering

Geo-replikering är en funktion i [Premium-tjänstnivån](container-registry-skus.md) för Azure Container Registry. När du replikerar ett register till din önskade regioner debiteras du avgifter för Premium-register för varje region.

I föregående exempel konsoliderade Contoso två register till ett och lade till repliker i USA, östra; Kanada, centrala samt Europa, västra. Contoso betalade då fyra gånger Premium per månad, utan ytterligare konfiguration eller hantering. Varje region hämtar nu sina avbildningar lokalt, vilket förbättrar prestanda och tillförlitlighet utan utgående nätverksavgifter från USA, västra till Kanada och USA, östra.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Felsöka push-åtgärder med geo-replikerade register
 
En Docker-klient som skickar en avbildning till ett geo-replikerat register kanske inte skickar alla avbildningslager och dess manifest till en enda replikerad region. Detta kan inträffa Azure Traffic Manager dirigerar registerbegäranden till det nätverks närmaste replikerade registret. Om registret har *två* närliggande replikeringsregioner kan avbildningslager och manifestet distribueras till de två platserna, och push-åtgärden misslyckas när manifestet verifieras. Det här problemet uppstår på grund av hur DNS-namnet på registret löses på vissa Linux-värdar. Det här problemet uppstår inte i Windows, som tillhandahåller en DNS-cache på klientsidan.
 
Om det här problemet uppstår är en lösning att tillämpa en DNS-cache på klientsidan, till exempel `dnsmasq` på Linux-värden. Detta säkerställer att registrets namn matchas konsekvent. Om du använder en virtuell Linux-dator i Azure för att skicka till ett register, se alternativ i [DNS-namnmatchningsalternativ för virtuella Linux-datorer i Azure](../virtual-machines/linux/azure-dns.md).

Om du vill optimera DNS-upplösningen till den närmaste repliken när du push-pushar avbildningar konfigurerar du ett geo-replikerat register i samma Azure-regioner som källan för push-åtgärderna eller den närmaste regionen när du arbetar utanför Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Inaktivera tillfälligt routning till replikering

Om du vill felsöka åtgärder med ett geo-replikerat register kanske du tillfälligt vill inaktivera Traffic Manager routning till en eller flera replikeringar. Från och med Azure CLI version 2.8 kan du konfigurera ett alternativ `--region-endpoint-enabled` (förhandsversion) när du skapar eller uppdaterar en replikerad region. När du anger alternativet för en replikering till dirigerar Traffic Manager inte `--region-endpoint-enabled` `false` längre docker push- eller pull-begäranden till den regionen. Som standard är routning till alla replikeringar aktiverat och datasynkronisering över alla replikeringar sker oavsett om routning är aktiverad eller inaktiverad.

Om du vill inaktivera routning till en befintlig replikering kör du [först az acr replication list][az-acr-replication-list] för att lista replikeringarna i registret. Kör sedan [az acr replication update och][az-acr-replication-update] ange för en specifik `--region-endpoint-enabled false` replikering. Om du till exempel vill konfigurera inställningen för *replikeringen westus* i *myregistry*:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Så här återställer du routning till en replikering:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Nästa steg

Läs självstudieserien i tre delar, [Geo-replikering i Azure Container Registry](container-registry-tutorial-prepare-registry.md). Gå igenom att skapa ett geo-replikerat register samt skapa en container och sedan distribuera den med ett enda `docker push`-kommando till flera regionala Web Apps for Containers-instanser.

> [!div class="nextstepaction"]
> [Geo-replikering i Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az_acr_replication_list
[az-acr-replication-update]: /cli/azure/acr/replication#az_acr_replication_update
