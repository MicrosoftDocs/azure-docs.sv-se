---
title: Använda Istio för intelligent routning
titleSuffix: Azure Kubernetes Service
description: Lär dig hur du använder Istio för att tillhandahålla intelligent Routning och distribuera Kanarie-versioner i ett Azure Kubernetes service-kluster (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: d66f3099ba225fbdd2bfc3d54db56ffd8ed2c43f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94684040"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Använda intelligent Routning och Kanarie-versioner med Istio i Azure Kubernetes service (AKS)

[Istio][istio-github] är ett nät med öppen källkod som ger en nyckel uppsättning över mikrotjänster i ett Kubernetes-kluster. Dessa funktioner omfattar trafik hantering, tjänst identitet och säkerhet, princip tillämpning och upprätthållande. Mer information om Istio finns i den officiella [Istio?][istio-docs-concepts] -dokumentationen.

Den här artikeln visar hur du använder funktionerna för trafik hantering i Istio. Ett exempel på en AKS röstnings app används för att utforska intelligenta routing-och Kanarie-versioner.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Distribuera programmet
> * Uppdatera programmet
> * Distribuera en Kanarie-version av programmet
> * Slutför distributionen

## <a name="before-you-begin"></a>Innan du börjar

> [!NOTE]
> Det här scenariot har testats mot Istio-versionen `1.3.2` .

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes `1.13` och senare, med KUBERNETES RBAC aktiverat) och har upprättat en `kubectl` anslutning till klustret. Du måste också ha Istio installerat i klustret.

Om du behöver hjälp med något av dessa objekt kan du läsa snabb starten för [AKS][aks-quickstart] och [Installera Istio i AKS][istio-install] -vägledningen.

## <a name="about-this-application-scenario"></a>Om det här program scenariot

Exemplet på AKS röstning innehåller två röstnings alternativ (**katter** eller **hundar**) till användare. Det finns en lagrings komponent som behåller antalet röster för varje alternativ. Det finns dessutom en analys komponent som innehåller information kring de röster som omvandlas för varje alternativ.

I det här program scenariot börjar du med att distribuera versionen `1.0` av röstnings appen och versionen `1.0` av Analytics-komponenten. Analytics-komponenten ger enkla räknare för antalet röster. Röstnings appen och analys komponenten interagerar med versionen `1.0` av Storage-komponenten, som backas upp av Redis.

Du uppgraderar Analytics-komponenten till version `1.1` , som innehåller antal och nu summor och procent.

En delmängd användare test version `2.0` av appen via en Kanarie-version. Den här nya versionen använder en lagrings komponent som backas upp av en MySQL-databas.

När du är säker på att versionen `2.0` fungerar som förväntat på din delmängd av användarna kan du distribuera versionen `2.0` till alla dina användare.

## <a name="deploy-the-application"></a>Distribuera programmet

Vi börjar med att distribuera programmet till ditt Azure Kubernetes service-kluster (AKS). Följande diagram visar vad som körs i slutet av det här avsnittet – versionen `1.0` av alla komponenter med inkommande begär Anden som servas via Istio ingress Gateway:

![Diagram som visar version 1,0 av alla komponenter med inkommande begär Anden som servas via Istio ingress Gateway.](media/servicemesh/istio/scenario-routing-components-01.png)

De artefakter som du måste följa tillsammans med den här artikeln finns i avsnittet [Azure-samples/AKS-röstning-app][github-azure-sample] GitHub lagrings platsen. Du kan antingen hämta artefakterna eller klona lagrings platsen på följande sätt:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Ändra till följande mapp i den nedladdade/klonade lagrings platsen och kör alla efterföljande steg från den här mappen:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Börja med att skapa ett namn område i AKS-klustret för exempel AKS röstnings appen `voting` enligt följande:

```console
kubectl create namespace voting
```

Namnge namn området med `istio-injection=enabled` . Den här etiketten instruerar Istio att automatiskt mata in Istio-proxyservrarna som sidvagn i alla dina poddar i det här namn området.

```console
kubectl label namespace voting istio-injection=enabled
```

Nu ska vi skapa komponenterna för AKS röstning-appen. Skapa de här komponenterna i `voting` namn området som du skapade i föregående steg.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

Följande exempel på utdata visar de resurser som skapas:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio har vissa särskilda krav kring poddar och tjänster. Mer information finns i Istio- [krav för poddar-och tjänst dokumentation][istio-requirements-pods-and-services].

Om du vill se poddar som har skapats använder du kommandot [kubectl get poddar][kubectl-get] enligt följande:

```console
kubectl get pods -n voting --show-labels
```

Följande exempel på utdata visar att det finns tre instanser av `voting-app` Pod och en enda instans av både `voting-analytics` och `voting-storage` poddar. Varje poddar har två behållare. En av dessa behållare är komponenten och den andra är `istio-proxy` :

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Om du vill se information om Pod använder vi kommandot [kubectl beskriver Pod][kubectl-describe] med etikett väljare för att välja `voting-analytics` pod. Vi kommer att filtrera utdata för att visa information om de två behållare som finns i pod:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

Du kan inte ansluta till röstnings appen förrän du har skapat Istio- [gatewayen][istio-reference-gateway] och den [virtuella tjänsten][istio-reference-virtualservice]. Dessa Istio-resurser dirigerar trafik från standardvärdet för Istio-inkommande Gateway till vårt program.

> [!NOTE]
> En **Gateway** är en komponent i kanten av service nätet som tar emot inkommande eller utgående http-och TCP-trafik.
> 
> En **virtuell tjänst** definierar en uppsättning regler för routning av en eller flera mål tjänster.

Använd `kubectl apply` kommandot för att distribuera gatewayen och yaml för virtuella tjänster. Kom ihåg att ange det namn område som resurserna distribueras till.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

Följande exempel på utdata visar den nya gatewayen och den virtuella tjänst som skapas:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Hämta IP-adressen för Istio-gatewayen med hjälp av följande kommando:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

Följande exempel på utdata visar IP-adressen för den inkommande gatewayen:

```output
20.188.211.19
```

Öppna en webbläsare och klistra in den i IP-adressen. Exemplet på AKS röstning visas.

![AKS röstnings app som körs i vårt Istio-aktiverade AKS-kluster.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

Informationen längst ned på skärmen visar att appen använder version `1.0` av `voting-app` och version `1.0` av `voting-storage` (Redis).

## <a name="update-the-application"></a>Uppdatera programmet

Nu ska vi distribuera en ny version av Analytics-komponenten. Den här nya versionen `1.1` visar summor och procent satser utöver antalet för varje kategori.

Följande diagram visar vad som kommer att köras i slutet av den här delen – endast versionen `1.1` av vår `voting-analytics` komponent har trafik som dirigerats från `voting-app` komponenten. Även om versionen `1.0` av vår `voting-analytics` komponent fortsätter att köras och refereras av `voting-analytics` tjänsten, tillåter Istio-proxyservrarna trafik till och från den.

![Diagram som bara visar version 1,1 av röstnings analys komponenten har trafik som dirigerats från röstnings-app-komponenten.](media/servicemesh/istio/scenario-routing-components-02.png)

Nu ska vi distribuera version `1.1` av `voting-analytics` komponenten. Skapa den här komponenten i `voting` namn området:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

Följande exempel på utdata visar de resurser som skapas:

```output
deployment.apps/voting-analytics-1-1 created
```

Öppna AKS röstnings app i en webbläsare igen med hjälp av IP-adressen för Istio ingress gateway som hämtades i föregående steg.

Webbläsaren växlar mellan de två vyer som visas nedan. Eftersom du använder en Kubernetes [-tjänst][kubernetes-service] för `voting-analytics` komponenten med endast en enda etikett väljare ( `app: voting-analytics` ) använder Kubernetes standard beteendet för resursallokering mellan den poddar som matchar den väljaren. I det här fallet är det både version `1.0` och `1.1` `voting-analytics` poddar.

![Version 1,0 av analys komponenten som körs i vår AKS röstnings app.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Version 1,1 av analys komponenten som körs i vår AKS röstnings app.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Du kan visualisera växlingen mellan de två versionerna av `voting-analytics` komponenten på följande sätt. Kom ihåg att använda IP-adressen för din egen Istio ingress-Gateway.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Följande exempel på utdata visar relevant del av den returnerade webbplatsen som plats växlar mellan versioner:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Lås trafik till version 1,1 av programmet

Nu ska vi låsa trafiken till endast version `1.1` av `voting-analytics` komponenten och till version `1.0` av `voting-storage` komponenten. Du definierar sedan routningsregler för alla andra komponenter.

> * En **virtuell tjänst** definierar en uppsättning regler för routning av en eller flera mål tjänster.
> * En **mål regel** definierar Traffic policies-och versions principer.
> * En **princip** definierar vilka autentiseringsmetoder som kan godkännas för arbets belastning (ar).

Använd `kubectl apply` kommandot för att ersätta den virtuella tjänst definitionen i `voting-app` och lägga till [mål regler][istio-reference-destinationrule] och [virtuella tjänster][istio-reference-virtualservice] för de andra komponenterna. Du lägger till en [princip][istio-reference-policy] i `voting` namn området för att säkerställa att all kommunikation mellan tjänster skyddas med ömsesidig TLS och klient certifikat.

* Principen har `peers.mtls.mode` ställt in för att `STRICT` säkerställa att ömsesidig TLS upprätthålls mellan dina tjänster i `voting` namn området.
* Vi anger också `trafficPolicy.tls.mode` till `ISTIO_MUTUAL` i alla våra mål regler. Istio tillhandahåller tjänster med starka identiteter och skyddar kommunikationen mellan tjänster med ömsesidiga TLS-och klient certifikat som Istio transparent hanterar.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

Följande exempel på utdata visar den nya principen, mål reglerna och de virtuella tjänster som uppdateras/skapas:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Om du öppnar AKS röstnings app i en webbläsare igen används bara den nya versionen `1.1` av `voting-analytics` komponenten `voting-app` .

![Version 1,1 av analys komponenten som körs i vår AKS röstnings app.](media/servicemesh/istio/scenario-routing-update-app-01.png)

Du kan visualisera att du nu bara dirigeras till versionen `1.1` av- `voting-analytics` komponenten enligt följande. Kom ihåg att använda IP-adressen för din egen Istio ingress-Gateway:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

Följande exempel på utdata visar relevant del av den returnerade webbplatsen:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Nu ska vi bekräfta att Istio använder ömsesidig TLS för att säkra kommunikationen mellan var och en av våra tjänster. För det här ska vi använda kommandot [authn TLS-check][istioctl-authn-tls-check] på `istioctl` klientens binärfil, som har följande formulär.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Den här uppsättningen kommandon ger information om åtkomst till de angivna tjänsterna, från alla poddar som finns i ett namn område och matchar en uppsättning etiketter:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

I följande exempel på utdata visas att ömsesidig TLS upprätthålls för var och en av våra frågor ovan. Utdata visar också principen och mål reglerna som tillämpar ömsesidig TLS:

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Distribuera en Kanarie-version av programmet

Nu ska vi distribuera en ny version `2.0` av `voting-app` -, `voting-analytics` -och- `voting-storage` komponenterna. Den nya `voting-storage` komponenten använder mysql i stället för Redis, och `voting-app` - `voting-analytics` komponenterna uppdateras så att de kan använda den här nya `voting-storage` komponenten.

`voting-app`Komponenten stöder nu funktioner för funktions flagga. Med den här funktions flaggan kan du testa funktionen för Istio för en delmängd av användarna.

Följande diagram visar vad du kommer att ha i slutet av det här avsnittet.

* Versionen `1.0` av `voting-app` komponenten, versionen `1.1` av komponenten `voting-analytics` och versionen `1.0` av komponenten kan `voting-storage` kommunicera med varandra.
* Versionen `2.0` av `voting-app` komponenten, versionen `2.0` av komponenten `voting-analytics` och versionen `2.0` av komponenten kan `voting-storage` kommunicera med varandra.
* Versionen `2.0` av `voting-app` komponenten är endast tillgänglig för användare som har en angiven funktions flagga angiven. Den här ändringen hanteras med en funktions flagga via en cookie.

![Diagram som visar vad du kommer att köra i slutet av det här avsnittet.](media/servicemesh/istio/scenario-routing-components-03.png)

Börja med att uppdatera Istio-destinations reglerna och de virtuella tjänsterna så att de passar dessa nya komponenter. De här uppdateringarna säkerställer att du inte dirigerar trafik felaktigt till de nya komponenterna och att användarna inte får oväntad åtkomst:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

Följande exempel på utdata visar de mål regler och virtuella tjänster som uppdateras:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Nu ska vi lägga till Kubernetes-objekten för de nya versions `2.0` komponenterna. Du uppdaterar också `voting-storage` tjänsten så att den inkluderar `3306` porten för MySQL:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

Följande exempel på utdata visar att Kubernetes-objekten har uppdaterats eller skapats:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Vänta tills alla versions `2.0` poddar körs. Använd kommandot [kubectl get poddar][kubectl-get] med se- `-w` växeln för att se ändringar i alla poddar i `voting` namn området:

```console
kubectl get pods --namespace voting -w
```

Nu bör du kunna växla mellan version `1.0` och version `2.0` (Kanarie) för röstnings programmet. Funktions flaggan växla längst ned på skärmen anger en cookie. Den här cookien används av den `voting-app` virtuella tjänsten för att dirigera användare till den nya versionen `2.0` .

![Version 1,0 av AKS röstning-appen-funktions flagga har inte angetts.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Version 2,0 av AKS röstning app – funktions flagga har angetts.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

Antalet röstningar skiljer sig mellan versionerna av appen. Den här skillnaden fokuserar på att du använder två olika lagrings Server delar.

## <a name="finalize-the-rollout"></a>Slutför distributionen

När du har testat Kanarie-versionen uppdaterar du den `voting-app` virtuella tjänsten för att dirigera all trafik till version `2.0` av `voting-app` komponenten. Alla användare ser sedan versionen `2.0` av programmet, oavsett om funktions flaggan är inställd eller inte:

![Diagram som visar att användarna ser version 2,0 av programmet, oavsett om funktions flaggan har angetts eller inte.](media/servicemesh/istio/scenario-routing-components-04.png)

Uppdatera alla mål regler för att ta bort de versioner av de komponenter som du inte längre vill ha aktiva. Uppdatera sedan alla virtuella tjänster för att sluta referera till dessa versioner.

Eftersom det inte längre finns någon trafik till någon av de äldre versionerna av komponenterna kan du nu på ett säkert sätt ta bort alla distributioner för dessa komponenter.

![AKS röstnings program komponenter och routning.](media/servicemesh/istio/scenario-routing-components-05.png)

Nu har du distribuerat en ny version av AKS röstnings appen.

## <a name="clean-up"></a>Rensa 

Du kan ta bort AKS röstnings appen som vi använde i det här scenariot från ditt AKS-kluster genom att ta bort `voting` namn området enligt följande:

```console
kubectl delete namespace voting
```

Följande exempel på utdata visar att alla komponenter i AKS röstnings program har tagits bort från ditt AKS-kluster.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Nästa steg

Du kan utforska ytterligare scenarier med hjälp av [Istio Bookinfo-programexempelet][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.mesh.v1alpha1/#AuthenticationPolicy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md
