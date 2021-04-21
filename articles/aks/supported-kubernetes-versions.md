---
title: Kubernetes-versioner som stöds i Azure Kubernetes Service
description: Förstå stödprincipen för Kubernetes-versionen och livscykeln för kluster i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/29/2021
author: palma21
ms.author: jpalma
ms.openlocfilehash: 43b0304ec6ac4266c3727990bf013c3dcfe523a3
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750256"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes Service (AKS)

Kubernetes-communityn släpper mindre versioner ungefär var tredje månad. Nyligen har Kubernetes-communityn ökat supportfönstret för varje version från 9 månader till [12](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/)månader, från version 1.19. 

Mindre versioner innehåller nya funktioner och förbättringar. Korrigeringsversionerna är mer frekventa (ibland veckovisa) och är avsedda för kritiska felkorrigeringar i en mindre version. Korrigeringsuppdateringar innehåller korrigeringar för säkerhetsproblem eller större buggar.

## <a name="kubernetes-versions"></a>Kubernetes-versioner

Kubernetes använder [standardschemat för semantisk](https://semver.org/) versionshantering för varje version:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Varje tal i versionen anger allmän kompatibilitet med den tidigare versionen:

* **Större versioner ändras** när inkompatibla API-uppdateringar eller bakåtkompatibilitet kan brytas.
* **Mindre versioner** ändras när funktionsuppdateringar görs som är bakåtkompatibla till de andra mindre versionerna.
* **Korrigeringsversioner** ändras när bakåtkompatibla felkorrigeringar görs.

Sikta på att köra den senaste korrigeringsversionen av delversionen som du kör. Till exempel finns ditt produktionskluster på **`1.17.7`** . **`1.17.8`** är den senaste tillgängliga korrigeringsversionen för *1.17-serien.* Du bör uppgradera till **`1.17.8`** så snart som möjligt för att säkerställa att klustret är helt korrigerat och stöds.

## <a name="kubernetes-version-support-policy"></a>Stödprincip för Kubernetes-version

AKS definierar en allmänt tillgänglig version som en version som är aktiverad i alla SLO- eller SLA-mått och är tillgänglig i alla regioner. AKS stöder tre mindre GA-versioner av Kubernetes:

* Den senaste versionen av GA-delversionen som släpps i AKS (som vi kallar N).
* Två tidigare mindre versioner.
    * Varje delversion som stöds stöder också högst två (2) stabila korrigeringar.

AKS kan också ha stöd för förhandsversioner, som uttryckligen är märkta och omfattas [av villkoren i förhandsversionen.][preview-terms]

> [!NOTE]
> AKS använder säkra distributionsmetoder som inbegriper gradvis regiondistribution. Det innebär att det kan ta upp till 10 arbetsdagar innan en ny version eller en ny version blir tillgänglig i alla regioner.

Fönstret som stöds i Kubernetes-versioner i AKS kallas "N-2": (N (senaste versionen) – 2 (mindre versioner)).

Om AKS till exempel introducerar *1.17.a* idag finns stöd för följande versioner:

Ny delversion    |    Lista över versioner som stöds
-----------------    |    ----------------------
1.17.a               |    1.17.a, 1.17.b, 1.16.c, 1.16.d, 1.15.e, 1.15.f

Där ".letter" är representativt för korrigeringsversioner.

När en ny delversion introduceras blir de äldsta delversionerna och korrigeringsversionerna som stöds inaktuella och tas bort. Till exempel är den aktuella versionslistan som stöds:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

AKS version 1.18. tar bort alla \* 1.15. versioner som inte \* har stöd inom 30 dagar.

> [!NOTE]
> Om kunderna kör en Kubernetes-version som inte stöds uppmanas de att uppgradera när de begär stöd för klustret. Kluster som kör Kubernetes-versioner som inte stöds omfattas inte av [AKS-supportprinciperna.](./support-policies.md)

Utöver ovanstående stöder AKS högst två **korrigeringsutgåvor** av en viss delversion. Så givet följande versioner som stöds:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Om AKS släpper och blir de äldsta korrigeringsversionerna inaktuella och `1.17.9` borttagna, och listan över versioner som stöds `1.16.11` blir:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Versioner som `kubectl` stöds

Du kan använda en äldre eller senare delversion av i förhållande till `kubectl` *kube-apiserver-versionen,* i enlighet med [Kubernetes-stödprincipen för kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Om *kube-apiserver* till exempel är *på 1.17* kan du använda version *1.16* till *1.18* av med `kubectl` den *kube-apiserver*.

Om du vill installera eller uppdatera din version `kubectl` av kör du `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Lanserings- och utfasningsprocess

Du kan referera till kommande versioner och utfasningar i [AKS Kubernetes Release Calendar](#aks-kubernetes-release-calendar).

För nya **mindre** versioner av Kubernetes:
  * AKS publicerar ett förhandsmeddelande med det planerade datumet för en ny version [](https://aka.ms/aks/releasenotes) och respektive gammal version utfasning på AKS-versionsanteckningarna minst 30 dagar före borttagningen.
  * AKS använder [Azure Advisor för att](https://docs.microsoft.com/azure/advisor/advisor-overview) varna användare om en ny version kommer att orsaka problem i sitt kluster på grund av inaktuella API:er. Azure Advisor används också för att varna användaren om supporten för närvarande är slut.
  * AKS publicerar ett meddelande om [tjänsthälsa](../service-health/service-health-overview.md) som är tillgängligt för alla användare med åtkomst till AKS och portalen och skickar ett e-postmeddelande till prenumerationsadministratörerna med planerade datum för borttagning av version.

    > [!NOTE]
    > Om du vill ta reda på vem som är din prenumerationsadministratör eller ändra den kan du läsa [hantera Azure-prenumerationer.](../cost-management-billing/manage/add-change-subscription-administrator.md#assign-a-subscription-administrator)
    
  * Användare har **30 dagar från borttagning** av version till uppgradering till en lägre version som stöds för att fortsätta få support.

För nya **korrigeringsversioner** av Kubernetes:
  * På grund av den brådskande typen av korrigeringsversioner kan de introduceras i tjänsten när de blir tillgängliga.
  * I allmänhet kommunicerar AKS inte allmänt versionen av nya korrigeringsversioner. AKS övervakar dock ständigt och validerar tillgängliga CVE-korrigeringar för att stödja dem i AKS inom rimlig tid. Om en kritisk korrigering hittas eller en användaråtgärd krävs, meddelar AKS användarna att de ska uppgradera till den nyligen tillgängliga korrigeringen.
  * Användare har **30 dagar på sig** från att en korrigeringsutgåvor tas bort från AKS för att uppgradera till en korrigering som stöds och fortsätta att få support.

### <a name="supported-versions-policy-exceptions"></a>Principundantag som stöds

AKS förbehåller sig rätten att lägga till eller ta bort nya/befintliga versioner med en eller flera kritiska produktionspåverkande buggar eller säkerhetsproblem utan föregående meddelande.

Specifika korrigeringsuppdateringar kan hoppas över eller distribueras snabbare, beroende på allvarlighetsgraden för buggen eller säkerheten.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal- och CLI-versioner

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI får klustret som standard delversionen N-1 och den senaste korrigeringen. Om AKS till exempel stöder *1.17.a*, *1.17.b*, *1.16.c*, *1.16.d*, *1.15.e* och *1.15.f* är den valda standardversionen *1.16.c.*

Om du vill ta reda på vilka versioner som för närvarande är tillgängliga för din prenumeration och region använder du [kommandot az aks get-versions.][az-aks-get-versions] I följande exempel visas tillgängliga Kubernetes-versioner för *regionen EastUS:*

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes Release Calendar

Tidigare versioner finns i [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  K8s-version | Överordnad version  | AKS-förhandsgranskning  | AKS GA  | Livslängdens slut |
|--------------|-------------------|--------------|---------|-------------|
| 1.18  | Mar-23-20  | Maj 2020   | Augusti 2020  | 1.21 GA | 
| 1.19  | Aug-04-20  | Sep 2020   | Nov 2020  | 1.22 GA | 
| 1.20  | Dec-08-20  | Januari 2021   | Mar 2021  | 1.23 GA |
| 1.21  | Apr-08-21 | Maj 2021   | Jun 2021  | 1.24 GA |



## <a name="faq"></a>Vanliga frågor

**Hur meddelar Microsoft mig om nya Kubernetes-versioner?**

AKS-teamet publicerar förmeddelanden med planerade datum för de nya Kubernetes-versionerna i vår dokumentation, vår [GitHub](https://github.com/Azure/AKS/releases) samt e-postmeddelanden till prenumerationsadministratörer som äger kluster som kommer att få slut på support.  Förutom meddelanden använder AKS även [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-overview) för att meddela kunden i Azure-portalen för att varna användare om de inte har stöd, samt för att varna dem om inaktuella API:er som påverkar deras program eller utvecklingsprocess. 

**Hur ofta ska jag förvänta mig att uppgradera Kubernetes-versioner för att fortsätta att stödja?**

Från och med Kubernetes 1.19 har communityn med öppen källkod [utökat stödet till 1 år.](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/) AKS förbinder sig att aktivera korrigeringar och stödja matchning av överordnade åtaganden. För AKS-kluster på 1.19 och senare kommer du att kunna uppgradera minst en gång om året för att stanna kvar på en version som stöds. 

För versioner på 1.18 eller lägre är supportfönstret kvar på 9 månader, vilket kräver en uppgradering en gång var 9:e månad för att stanna kvar på en version som stöds. Testa regelbundet nya versioner och var beredd på att uppgradera till nyare versioner för att samla in de senaste stabila förbättringarna i Kubernetes.

**Vad händer när en användare uppgraderar ett Kubernetes-kluster med en lägre version som inte stöds?**

Om du använder *n-3-versionen* eller en äldre version innebär det att du inte har någon support och uppmanas att uppgradera. När uppgraderingen från version n-3 till n-2 lyckas är du tillbaka inom våra supportprinciper. Exempel:

- Om den äldsta AKS-versionen som stöds *är 1.15.a* och du har *1.14.b* eller äldre, är du utanför supporten.
- När du har uppgraderat *från 1.14.b* till *1.15.a* eller senare är du tillbaka i våra supportprinciper.

Nedgradering stöds inte.

**Vad betyder "Utanför support"**

"Utanför support" innebär att:
* Den version som du kör ligger utanför listan över versioner som stöds.
* Du uppmanas att uppgradera klustret till en version som stöds när du begär support, såvida du inte är inom respitperioden på 30 dagar efter att versionen har utfasning. 

Dessutom ger inte AKS någon körning eller andra garantier för kluster utanför listan över versioner som stöds.

**Vad händer när en användare skalar ett Kubernetes-kluster med en lägre version som inte stöds?**

För mindre versioner som inte stöds av AKS bör in- eller utskalning fortsätta att fungera. Eftersom det inte finns några tjänstkvalitetsgarantier rekommenderar vi att du uppgraderar för att få tillbaka ditt kluster till support.

**Kan en användare stanna kvar i en Kubernetes-version för alltid?**

Om ett kluster inte har stöd för fler än tre (3) mindre versioner och har visat sig medföra säkerhetsrisker kontaktar Azure dig proaktivt för att uppgradera klustret. Om du inte vidta ytterligare åtgärder förbehåller sig Azure rätten att automatiskt uppgradera klustret åt dig.

**Vilken version stöder kontrollplanet om nodpoolen inte finns i någon av de AKS-versioner som stöds?**

Kontrollplanet måste finnas inom ett fönster med versioner från alla nodpooler. Mer information om hur du uppgraderar kontrollplanet eller nodpoolerna finns i dokumentationen om hur du [uppgraderar nodpooler.](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools)

**Kan jag hoppa över flera AKS-versioner under klusteruppgraderingen?**

När du uppgraderar ett AKS-kluster som stöds går det inte att hoppa över Kubernetes-mindre versioner. Till exempel uppgraderingar mellan:
  * *1.12.x*  ->  *1.13.x*: tillåts.
  * *1.13.x*  ->  *1.14.x*: tillåts.
  * *1.12.x*  ->  *1.14.x*: tillåts inte.

Uppgradera från *1.12.x*  ->  *1.14.x:*
1. Uppgradera från *1.12.x*  ->  *1.13.x*.
1. Uppgradera från *1.13.x*  ->  *1.14.x*.

Du kan bara hoppa över flera versioner när du uppgraderar från en version som inte stöds tillbaka till en version som stöds. Du kan till exempel uppgradera från ett *1.10.x* som inte stöds till *ett 1.15.x som stöds.*

**Kan jag skapa ett nytt 1.xx.x-kluster under supportfönstret på 30 dagar?**

Nej. När en version är inaktuell/borttagen kan du inte skapa ett kluster med den versionen. När ändringen distribueras börjar du se den gamla versionen som tagits bort från versionslistan. Den här processen kan ta upp till två veckor från tillkännagivandet, progressivt efter region.

**Jag har en nyligen inaktuell version, kan jag fortfarande lägga till nya nodpooler? Eller måste jag uppgradera?**

Nej. Du kommer inte att kunna lägga till nodpooler av den inaktuella versionen i klustret. Du kan lägga till nodpooler i en ny version. Detta kan dock kräva att du uppdaterar kontrollplanet först. 

## <a name="next-steps"></a>Nästa steg

Information om hur du uppgraderar klustret finns i [Uppgradera ett Azure Kubernetes Service-kluster (AKS).][aks-upgrade]

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
