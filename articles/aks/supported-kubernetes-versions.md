---
title: Kubernetes-versioner som stöds i Azure Kubernetes Service
description: Förstå Kubernetes-versionens support policy och livs cykel för kluster i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 09/08/2020
author: palma21
ms.author: jpalma
ms.openlocfilehash: 53093edb1d3c142336f06ec8544aaa7b55e37477
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/20/2021
ms.locfileid: "98611261"
---
# <a name="supported-kubernetes-versions-in-azure-kubernetes-service-aks"></a>Kubernetes-versioner som stöds i Azure Kubernetes Service (AKS)

Kubernetes community släpper lägre versioner ungefär var tredje månad. Nyligen Kubernetes-communityn har [ökat support perioden för varje version från 9 månader till 12 månader](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/), från och med version 1,19. Dessa versioner innehåller nya funktioner och förbättringar. Uppdaterings versioner är oftare (ibland veckovis) och är avsedda för viktiga fel korrigeringar i en lägre version. Dessa korrigerings versioner innehåller korrigeringar för säkerhets sårbarheter eller större buggar.

## <a name="kubernetes-versions"></a>Kubernetes-versioner

Kubernetes använder standard versions schemat för [semantisk versions](https://semver.org/) hantering, vilket innebär att varje version av Kubernetes följer det här numreringsschema:

```
[major].[minor].[patch]

Example:
  1.17.7
  1.17.8
```

Varje tal i versionen indikerar allmän kompatibilitet med den tidigare versionen:

* Huvud versioner ändras när inkompatibla API-ändringar eller bakåtkompatibilitet kan vara brutna.
* Lägre versioner ändras när funktions förändringar görs som är bakåtkompatibla med andra mindre versioner.
* Korrigerings versioner ändras när bakåtkompatibla fel korrigeringar görs.

Användarna bör sträva efter att köra den senaste korrigerings versionen av den lägre version som de kör, till exempel om ditt produktions kluster är på **`1.17.7`** och **`1.17.8`** är den senaste tillgängliga korrigerings versionen som är tillgänglig för *1,17* -serien, bör du uppgradera till så snart som möjligt för att **`1.17.8`** säkerställa att klustret är fullständigt korrigerat och stöds.

## <a name="kubernetes-version-support-policy"></a>Support princip för Kubernetes-version

AKS definierar en allmänt tillgänglig version, som en version som är aktive rad i alla service nivå mål eller SLA-mätningar och som är tillgänglig i alla regioner. AKS stöder tre sekundär-versioner av Kubernetes:

* Den senaste sekundär versions versionen som släpps i AKS (som vi refererar till som N).
* Två tidigare del versioner.
* Varje lägre version som stöds har även stöd för högst två (2) stabila korrigeringar.
* AKS kan också ha stöd för för hands versioner, som uttryckligen märks och omfattas av för [hands versions][preview-terms]villkor.

> [!NOTE]
> AKS använder säker distributions praxis som inbegriper stegvis distribution av regioner. Det innebär att det kan ta upp till 10 arbets dagar för en ny version eller en ny version som är tillgänglig i alla regioner.

Det fönster som stöds av Kubernetes-versioner på AKS kallas "N-2": (N (senaste versionen)-2 (del versioner)).

Om AKS till exempel introducerar *1.17. a* idag tillhandahålls support för följande versioner:

Ny del version    |    Versions lista som stöds
-----------------    |    ----------------------
1.17. a               |    1.17. a, 1.17. b, 1.16. c, 1.16. d, 1.15. e, 1.15. f

Där ". us" är representativ för korrigerings versioner.

När en ny del version introduceras, är den äldsta lägre versionen och de nya korrigerings versionerna som stöds föråldrade och tas bort. Till exempel, om den aktuella versions listan som stöds är:

```
1.17.a
1.17.b
1.16.c
1.16.d
1.15.e
1.15.f
```

Och AKS släpper 1,18. \* , innebär det att alla 1,15.- \* versioner kommer att tas bort och att de inte kommer att fungera på 30 dagar.

> [!NOTE]
> Observera att om kunderna kör en Kubernetes-version som inte stöds uppmanas de att uppgradera när de begär support för klustret. Kluster som kör Kubernetes-versioner som inte stöds omfattas inte av [support principerna för AKS](./support-policies.md).

Förutom ovanstående, stöder AKS högst två **korrigerings** versioner av en specifik del version. Så få följande versioner som stöds:

```
Current Supported Version List
------------------------------
1.17.8, 1.17.7, 1.16.10, 1.16.9
```

Om AKS-versioner `1.17.9` och `1.16.11` , de äldsta korrigerings versionerna är föråldrade och tas bort, och den versions lista som stöds blir:

```
New Supported Version List
----------------------
1.17.*9*, 1.17.*8*, 1.16.*11*, 1.16.*10*
```

### <a name="supported-kubectl-versions"></a>Versioner som stöds `kubectl`

Du kan använda en lägre version som är äldre än eller senare i `kubectl` förhållande till din *Kube-apiserver-* version, som är konsekvent med [Kubernetes-Supportens princip för kubectl](https://kubernetes.io/docs/setup/release/version-skew-policy/#kubectl).

Om din *Kube-apiserver* till exempel är på *1,17*, kan du använda versioner *1,16* till *1,18* av `kubectl` med *Kube-apiserver*.

För att installera eller uppdatera din version av `kubectl` , kör `az aks install-cli` .

## <a name="release-and-deprecation-process"></a>Version och utfasnings process

Du kan referera till kommande versions utgåvor och utfasningar i [AKS Kubernetes release-kalendern](#aks-kubernetes-release-calendar).

För nya **mindre** versioner av Kubernetes
1. AKS publicerar ett förmeddelande med planerat datum för en ny versions version och respektive gamla versions utfasning på AKS- [versionen](https://aka.ms/aks/releasenotes) , minst 30 dagar innan borttagning.
2. AKS publicerar ett [meddelande om tjänst hälsa](../service-health/service-health-overview.md) som är tillgänglig för alla användare med AKS och Portal åtkomst och skickar ett e-postmeddelande till prenumerations administratörerna med den planerade versionen av borttagnings datum.
````
To find out who is your subscription administrators or to change it, please refer to [manage Azure subscriptions](https://docs.microsoft.com/azure/cost-management-billing/manage/add-change-subscription-administrator#assign-a-subscription-administrator).
````
3. Användare har **30 dagar** från borttagnings versionen för att uppgradera till en lägre versions version som stöds för att fortsätta få support.

För nya **korrigerings** versioner av Kubernetes
  * På grund av den brådskande typen av korrigerings versioner kan dessa införas i tjänsten när de blir tillgängliga.
  * I allmänhet gör AKS inte en bred kommunikation för att lansera nya korrigerings versioner. Men AKS övervakar och validerar alltid tillgängliga CVE-korrigeringsfiler för att stödja dem i AKS i rimlig tid. Om en kritisk korrigering påträffas eller om en användar åtgärd krävs kommer AKS att meddela användarna om att de kan uppgradera till den nyligen tillgängliga korrigeringen.
  * Användare har **30 dagar** från den tidpunkt då en korrigerings version tas bort från AKS för att uppgraderas till en korrigerings fil som stöds och fortsätta få support.

### <a name="supported-versions-policy-exceptions"></a>Princip undantag för versioner som stöds

AKS förbehåller sig rätten att lägga till eller ta bort nya/befintliga versioner som har identifierats ha en eller flera kritiska produktioner som påverkar buggar eller säkerhets problem utan föregående meddelande.

Vissa korrigerings versioner kan hoppas över eller att distributionen accelereras beroende på fel-eller säkerhets problemets allvarlighets grad.

## <a name="azure-portal-and-cli-versions"></a>Azure Portal-och CLI-versioner

När du distribuerar ett AKS-kluster i portalen eller med Azure CLI, är klustret standardvärdet för den lägre versionen av N-1 och den senaste korrigeringen. Om AKS till exempel stöder *1.17. a*, *1.17. b*, *1.16. c*, *1.16. d*, *1.15. e*, och *1.15. f*, är den valda standard versionen *1.16. c*.

Om du vill ta reda på vilka versioner som för närvarande är tillgängliga för din prenumeration och region använder du kommandot [AZ AKS get-versions][az-aks-get-versions] . I följande exempel visas tillgängliga Kubernetes-versioner för regionen *östra* :

```azurecli-interactive
az aks get-versions --location eastus --output table
```

## <a name="aks-kubernetes-release-calendar"></a>AKS Kubernetes, versions kalender

Tidigare versions historik finns i [Kubernetes](https://en.wikipedia.org/wiki/Kubernetes#History).

|  K8s-version | Överordnad version  | AKS för hands version  | AKS GA  | Uttjänta |
|--------------|-------------------|--------------|---------|-------------|
| 1.16  | Sep-19-19  | Jan 2019   | Mar 2020  | Jan 2021| 
| 1,17  | Dec-09-19  | Jan 2019   | Jul 2020  | 1,20 GA | 
| 1,18  | Mar – 23-20  | Maj 2020   | Aug 2020  | 1,21 GA | 
| 1,19  | Aug – 04-20  | Sep 2020   | Nov 2020  | 1,22 GA | 
| 1,20  | Dec-08-20  | Jan 2021   | Mar 2021  | 1,23 GA |
| 1,21  | Apr-08-21 * | Maj 2021   | Jul 2021  | 1,24 GA |

\* Kubernetes 1,21-versionen av överordnade versioner kan komma att ändras när den överordnade kalendern har slutförts.


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Hur ofta ska jag förvänta mig att uppgradera Kubernetes-versioner för att stanna kvar i supporten?**

Med beaktande av Kubernetes 1,19 [har communityn öppen källkod utökad support till 1 år](https://kubernetes.io/blog/2020/08/31/kubernetes-1-19-feature-one-year-support/). AKS åtar sig att aktivera korrigeringar och stöd för att matcha de överordnade åtagandena minst. Det innebär att du börjar med AKS-kluster på 1,19, du kommer att kunna uppgradera minst en gång om året till en version som stöds. För versioner på 1,18 eller lägre finns support perioden på 9 månader, vilket kräver en uppgradering var 9: e månad för att stanna kvar på en version som stöds. Vi rekommenderar starkt att du testar nya versioner regelbundet och är redo att uppgradera till nyare versioner för att avbilda de senaste stabila förbättringarna i Kubernetes.

**Vad händer när en användare uppgraderar ett Kubernetes-kluster med en lägre version som inte stöds?**

Om du använder *n-3-* versionen eller äldre, innebär det att du inte har stöd för och att du kommer att bli ombedd att uppgradera. När uppgraderingen från version n-3 till n-2 lyckas, är du tillbaka i våra support principer. Till exempel:

- Om den äldsta AKS-versionen som stöds är *1.15. a* och du är på *1.14. b* eller äldre är du utanför supporten.
- När du har uppgraderat från *1.14. b* till *1.15. en* eller fler lyckas är du tillbaka i våra support principer.

Nedgradering stöds inte.

**Vad innebär "utanför supporten"**

Utanför supporten innebär att den version som du kör ligger utanför listan över versioner som stöds och du uppmanas att uppgradera klustret till en version som stöds när du begär support, om du inte är inom 30 dagar efter versionens utfasnings period. Dessutom gör AKS inte någon körning eller andra garantier för kluster utanför listan över versioner som stöds.

**Vad händer när en användare skalar ett Kubernetes-kluster med en lägre version som inte stöds?**

För mindre versioner som inte stöds av AKS, måste skala in eller ut fortsätta att fungera, men det finns inga garantier för tjänst kvalitet, så vi rekommenderar att du uppgraderar för att återställa klustret till support.

**Kan en användare stanna kvar på en Kubernetes-version för alltid?**

Om ett kluster har stöd för fler än tre (3) lägre versioner och har befunnits medföra säkerhets risker, kontaktar Azure dig för att proaktivt uppgradera klustret. Om du inte vidtar ytterligare åtgärder förbehåller Azure rätten att automatiskt uppgradera ditt kluster för din räkning.

**Vilken version kontrollerar kontroll planet om Node-poolen inte finns i någon av de AKS-versioner som stöds?**

Kontroll planet måste vara inom ett fönster med versioner från alla noder i pooler. Mer information om hur du uppgraderar kontroll planet eller Node-pooler finns i dokumentationen om hur du [uppgraderar Node-pooler](use-multiple-node-pools.md#upgrade-a-cluster-control-plane-with-multiple-node-pools).

**Kan jag hoppa över flera AKS-versioner under kluster uppgraderingen?**

När du uppgraderar ett AKS-kluster som stöds kan Kubernetes minor-versioner inte hoppas över. Till exempel tillåts uppgraderingar mellan *1.12. x*  ->  *1.13. x* eller *1.13. x*  ->  *1.14. x* , men *1.12.* x  ->  *1.14. x* är inte.

Uppgradera från *1.12. x*  ->  *1.14. x* genom att först uppgradera från *1.12.* x  ->  *1.13. x* och sedan uppgradera från *1.13. x*  ->  *1.14. x*.

Det går bara att hoppa över flera versioner när du uppgraderar från en version som inte stöds tillbaka till en version som stöds. Du kan t. ex. uppgradera från en *1.10. x* --> en 1.15 som stöds *. x* kan slutföras.

## <a name="next-steps"></a>Nästa steg

Information om hur du uppgraderar klustret finns i [uppgradera ett Azure Kubernetes service-kluster (AKS)][aks-upgrade].

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[azure-update-channel]: https://azure.microsoft.com/updates/?product=kubernetes-service

<!-- LINKS - Internal -->
[aks-upgrade]: upgrade-cluster.md
[az-aks-get-versions]: /cli/azure/aks#az-aks-get-versions
[preview-terms]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
