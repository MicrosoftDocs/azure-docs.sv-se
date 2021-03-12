---
title: GEO-replikera ett register
description: Kom igång med att skapa och hantera ett geo-replikerat Azure Container Registry, vilket gör att registret kan betjäna flera regioner med regionala repliker med flera huvud servrar. Geo-replikering är en funktion i Premium service-nivån.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 4e82be0e81e5e8c0182e061a0fba0f880bd45cc6
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632398"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replikering i Azure Container Registry

Företag som vill ha en lokal närvaro, eller en direkt säkerhetskopia, väljer att köra tjänster från flera Azure-regioner. Som bästa praxis kan ett containerregister placeras i varje region där avbildningarna körs för att medge nätverksnära åtgärder med snabba och tillförlitliga avbildningslageröverföringar. Geo-replikering gör att ett Azure-containerregister kan fungera som ett enskilt register som betjänar flera regioner med regionala register med flera original. 

Ett georeplikerat register ger följande fördelar:

* Enskilda register, avbildningar och taggnamn kan användas i flera regioner
* Förbättra prestanda och tillförlitlighet i regionala distributioner med nätverks åtkomst till register
* Minska kostnaderna för data överföring genom att dra bild lager från ett lokalt replikerat register i samma eller närliggande region som behållar värden
* Enkel hantering av ett register i flera regioner
* Register återhämtning om ett regionalt avbrott inträffar

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
* Hantera en enda konfiguration av avbildnings distributioner som alla regioner använder samma bild-URL: `contoso.azurecr.io/public/products/web:1.2`
* Push-överför till ett enda register medan ACR hanterar geo-replikeringen. ACR replikerar bara unika lager, vilket minskar data överföringen mellan regioner. 
* Konfigurera regionala [webhookar](container-registry-webhook.md) som meddelar dig om händelser i vissa repliker.
* Tillhandahålla ett register med hög tillgänglighet som är flexibelt för regionala avbrott.

Azure Container Registry stöder också [tillgänglighets zoner](zone-redundancy.md) för att skapa ett flexibelt och hög tillgänglighet för Azure Container Registry i en Azure-region. Kombinationen av tillgänglighets zoner för redundans inom en region och geo-replikering över flera regioner förbättrar både tillförlitlighet och prestanda i ett register.

## <a name="configure-geo-replication"></a>Konfigurera geo-replikering

Konfiguration av geo-replikering är så enkelt som att klicka på regioner på en karta. Du kan också hantera geo-replikering med hjälp av verktyg som [AZ ACR Replication](/cli/azure/acr/replication) -kommandon i Azure CLI, eller distribuera ett register som är aktiverat för geo-replikering med en [Azure Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication).

Geo-replikering är en funktion i [Premium register](container-registry-skus.md). Om ditt register ännu inte är Premium kan du ändra från Basic och Standard till Premium i [Azure-portalen](https://portal.azure.com):

![Växla tjänst nivåer i Azure Portal](media/container-registry-skus/update-registry-sku.png)

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

* Varje region i ett geo-replikerat register är oberoende när den har kon figurer ATS. Azure Container Registry service avtal gäller för varje geo-replikerad region.
* När du push-överför eller hämtar bilder från ett geo-replikerat register skickar Azure Traffic Manager i bakgrunden begäran till registret i den region som är närmast dig vad gäller nätverks fördröjning.
* När du har push-överfört en avbildning eller tagga till den närmaste regionen tar det lite tid för Azure Container Registry att replikera manifest och lager till de återstående regioner som du har valt. Större bilder tar längre tid att replikera än de mindre. Bilder och taggar synkroniseras i de replikerade regionerna med en eventuell konsekvens modell.
* För att hantera arbets flöden som är beroende av push-uppdateringar till ett geo-replikerat register, rekommenderar vi att du konfigurerar [Webhooks](container-registry-webhook.md) så att de svarar på push-händelserna. Du kan ställa in regionala webhookar i ett geo-replikerat register för att spåra push-händelser när de är klara i de geo-replikerade regionerna.
* Azure Container Registry använder data slut punkter för att hantera blobbar som representerar innehålls lager. Du kan aktivera [dedikerade data slut punkter](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) för ditt register i var och en av dina registers geo-replikerade regioner. Med dessa slut punkter kan du konfigurera regler för att begränsa brand Väggs åtkomst. I fel söknings syfte kan du välja [att inaktivera routning till en replikering samtidigt som](#temporarily-disable-routing-to-replication) du behåller replikerade data.
* Om du konfigurerar en [privat länk](container-registry-private-link.md) för registret med privata slut punkter i ett virtuellt nätverk, aktive ras dedikerade data slut punkter i varje geo-replikerad region som standard. 

## <a name="delete-a-replica"></a>Ta bort en replik

När du har konfigurerat en replik för registret kan du när som helst ta bort den om den inte längre behövs. Ta bort en replik med hjälp av Azure Portal eller andra verktyg som kommandot [AZ ACR Replication Delete](/cli/azure/acr/replication#az-acr-replication-delete) i Azure CLI.

Så här tar du bort en replik i Azure Portal:

1. Navigera till Azure Container Registry och välj **replikeringar**.
1. Välj namnet på en replik och välj **ta bort**. Bekräfta att du vill ta bort repliken.

Så här använder du Azure CLI för att ta bort en replik av *registret* i regionen USA, östra:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Prissättning för Geo-replikering

Geo-replikering är en funktion i [Premium service-nivån](container-registry-skus.md) för Azure Container Registry. När du replikerar ett register till din önskade regioner debiteras du avgifter för Premium-register för varje region.

I föregående exempel konsoliderade Contoso två register till ett och lade till repliker i USA, östra; Kanada, centrala samt Europa, västra. Contoso betalade då fyra gånger Premium per månad, utan ytterligare konfiguration eller hantering. Varje region hämtar nu sina avbildningar lokalt, vilket förbättrar prestanda och tillförlitlighet utan utgående nätverksavgifter från USA, västra till Kanada och USA, östra.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Felsöka push-åtgärder med geo-replikerade register
 
En Docker-klient som skickar en avbildning till ett geo-replikerat register får inte skicka alla avbildnings lager och dess manifest till en enda replikerad region. Detta kan inträffa eftersom Azure Traffic Manager dirigerar register begär anden till det nätverks närmast replikerade registret. Om registret har två *närliggande* replikerade regioner kan bild lager och manifestet distribueras till de två platserna och push-åtgärden Miss lyckas när manifestet verifieras. Det här problemet beror på hur DNS-namnet på registret matchas på vissa Linux-värdar. Det här problemet uppstår inte i Windows, som tillhandahåller en DNS-cache på klient sidan.
 
Om det här problemet uppstår är en lösning att tillämpa en DNS-cache på klient sidan, till exempel `dnsmasq` på Linux-värden. Detta säkerställer att register namnet matchas konsekvent. Om du använder en virtuell Linux-dator i Azure för att skicka till ett register, se alternativ i alternativ [för DNS-namnmatchning för virtuella Linux-datorer i Azure](../virtual-machines/linux/azure-dns.md).

Om du vill optimera DNS-matchningen till den närmaste repliken när du skickar avbildningar konfigurerar du ett geo-replikerat register i samma Azure-regioner som källan till push-åtgärderna eller den närmaste regionen när du arbetar utanför Azure.

### <a name="temporarily-disable-routing-to-replication"></a>Inaktivera routning till replikering tillfälligt

Om du vill felsöka åtgärder med ett geo-replikerat register kan du tillfälligt inaktivera Traffic Manager routning till en eller flera replikeringar. Från och med Azure CLI version 2,8 kan du konfigurera ett `--region-endpoint-enabled` alternativ (förhands granskning) när du skapar eller uppdaterar en replikerad region. När du ställer in en replikerings `--region-endpoint-enabled` alternativ för `false` , Traffic Manager inte längre dirigerar Docker push-eller pull-begäranden till den regionen. Som standard aktive ras routning till alla replikeringar och datasynkronisering över alla replikeringar sker oavsett om routning är aktiverat eller inaktiverat.

Om du vill inaktivera routning till en befintlig replikering ska du först köra [AZ ACR-replikeringsgruppen][az-acr-replication-list] för att visa en lista över replikeringarna i registret. Kör sedan [AZ ACR Replication Update][az-acr-replication-update] och Ställ in `--region-endpoint-enabled false` för en speciell replikering. Om du till exempel vill konfigurera inställningen för den *västra* replikeringen i *registret*:

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

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
