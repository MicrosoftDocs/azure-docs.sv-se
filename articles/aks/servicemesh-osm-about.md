---
title: Öppet service nät (förhands granskning)
description: Öppet service nät (OSM) i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 0052c8d2f9b85c34d50a3e9d01253ecaf2d02bab
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106106721"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Öppna service nät AKS-tillägg (för hands version)

## <a name="overview"></a>Översikt

[Open Service-nätet (OSM)](https://docs.openservicemesh.io/) är ett enkelt, utöknings Bart, moln nätverk med ett nät som gör det möjligt för användare att enhetligt hantera, säkra och få välkomst funktioner för att få till gång till de funktioner som är tillgängliga för mycket dynamiska mikrotjänster.

OSM kör ett mottagare kontroll plan på Kubernetes, kan konfigureras med [SMI](https://smi-spec.io/) -API: er och fungerar genom att mata in en mottagare-proxy som en sidvagn-behållare bredvid varje instans av programmet. Mottagare proxy innehåller och kör regler kring principer för åtkomst kontroll, implementerar routnings konfiguration och samlar in mått. Kontroll planet konfigurerar kontinuerligt proxyservrar för att se till att principer och regler för routning är uppdaterade och säkerställer att proxyservrar är felfria.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Funktioner och funktioner

OSM tillhandahåller följande funktioner för att tillhandahålla ett internt Cloud Service-nät för dina Azure Kubernetes service-kluster (AKS):

- Skydda tjänst-till-tjänst-kommunikation genom att aktivera mTLS

- Du kan enkelt publicera program på nätet genom att aktivera automatisk sidvagn insprutning av mottagare proxy

- Enkelt och transparenta konfigurationer för trafik växling vid distributioner

- Möjlighet att definiera och utföra detaljerade principer för åtkomst kontroll för tjänster

- Observationer och insikter om program mått för fel sökning och övervaknings tjänster

- Integrering med externa certifikat hanterings tjänster/-lösningar med ett anslutnings Bart gränssnitt

## <a name="scenarios"></a>Scenarier

OSM kan hjälpa dina AKS-distributioner med följande scenarier:

- Tillhandahålla krypterad kommunikation mellan tjänst slut punkter som distribueras i klustret

- Trafik tillstånd för både HTTP/HTTPS och TCP-trafik i nätet

- Konfiguration av viktade trafik kontroller mellan två eller flera tjänster för A/B-eller Kanarie-distributioner

- Insamling och visning av KPI: er från program trafik

## <a name="osm-service-quotas-and-limits-preview"></a>Kvoter och begränsningar för OSM-tjänsten (för hands version)

OSM för för hands versions begränsningar för tjänst kvoter och begränsningar finns på [sidan AKS-kvoter och regionala gränser](https://docs.microsoft.com/azure/aks/quotas-skus-regions).

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/install-osm-binary-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/install-osm-binary-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/install-osm-binary-windows.md)]

::: zone-end

> [!WARNING]
> Försök inte att installera OSM från binärfilen med hjälp av `osm install` . Detta leder till en installation av OSM som inte är integrerad som ett tillägg för AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Registrera `AKS-OpenServiceMesh` förhands gransknings funktionen

Om du vill skapa ett AKS-kluster som kan använda Open Service nät-tillägget måste du aktivera `AKS-OpenServiceMesh` funktions flaggan i din prenumeration.

Registrera `AKS-OpenServiceMesh` funktions flaggan med hjälp av kommandot [AZ Feature register][az-feature-register] , som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Det tar några minuter för statusen att visa _registrerad_. Verifiera registrerings statusen med hjälp av kommandot [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av resurs leverantören _Microsoft. container service_ med hjälp av kommandot [AZ Provider register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Installera OSM (Open Service nät) Azure Kubernetes service (AKS)-tillägg för ett nytt AKS-kluster

För ett nytt AKS kluster distributions scenario börjar du med en helt ny distribution av ett AKS-kluster som aktiverar OSM-tillägget i klustret skapa åtgärd.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Skapa en resursgrupp med hjälp av [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet _myOsmAksGroup_ på _eastus2_ -platsen (region):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Distribuera ett AKS-kluster med OSM-tillägget aktiverat

Nu ska du distribuera ett nytt AKS-kluster med OSM-tillägget aktiverat.

> [!NOTE]
> Tänk på följande AKS distributions kommando använder sig av SYSTEMets tillfälliga diskar. Du hittar mer information här om [tillfälliga OS-diskar för AKS](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Hämta autentiseringsuppgifter för AKS för kluster åtkomst

Få åtkomst behörighet för det nya hanterade Kubernetes-klustret.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Aktivera OSM (Open Service nät) Azure Kubernetes service (AKS)-tillägg för ett befintligt AKS-kluster

För ett befintligt kluster scenario för AKS kan du aktivera OSM-tillägget till ett befintligt AKS-kluster som redan har distribuerats.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Aktivera OSM-tillägget för det befintliga AKS-klustret

Om du vill aktivera AKS OSM-tillägget måste du köra kommandot genom att `az aks enable-addons --addons` skicka parametern `open-service-mesh`

```azurecli-interactive
az aks enable-addons --addons open-service-mesh -g <resource group name> -n <AKS cluster name>
```

Du bör se utdata som liknar de utdata som visas nedan för att bekräfta att AKS OSM-tillägget har installerats.

```json
{- Finished ..
  "aadProfile": null,
  "addonProfiles": {
    "KubeDashboard": {
      "config": null,
      "enabled": false,
      "identity": null
    },
    "openServiceMesh": {
      "config": {},
      "enabled": true,
      "identity": {
...
```

## <a name="validate-the-aks-osm-add-on-installation"></a>Verifiera installationen av AKS OSM-tillägget

Det finns flera kommandon att köra för att kontrol lera att alla komponenter i AKS OSM-tillägget är aktiverade och körs:

Först kan vi skicka frågor till tilläggs profilerna för klustret för att kontrol lera aktiverade tillstånd för de installerade tilläggen. Följande kommando ska returnera "true".

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Följande `kubectl` kommandon rapporterar status för OSM-kontrollanten.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Åtkomst till AKS OSM-tillägget

För närvarande kan du komma åt och konfigurera konfigurationen av OSM-kontrollanten via configmap. Om du vill visa konfigurations inställningarna för OSM-kontrollanten frågar du OSM-config configmap via `kubectl` för att Visa konfigurations inställningarna.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Utdata från OSM configmap bör se ut så här:

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254/32,168.63.129.16/32,<YOUR_API_SERVER_PUBLIC_IP>/32",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Observera att **permissive_traffic_policy_mode** har kon figurer ATS till **True**. Läge för tillstånds trafik i OSM är ett läge där [SMI](https://smi-spec.io/) -trafikens princip tvång kringgås. I det här läget identifierar OSM automatiskt tjänster som är en del av policy reglerna för service nät och program trafik på varje mottagare-för att kunna kommunicera med dessa tjänster.

> [!WARNING]
> Innan du fortsätter kontrollerar du att läget för tillstånds principen är inställt på sant, om du inte ändrar värdet till **Sant** med kommandot nedan

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Distribuera ett nytt program som ska hanteras av AKS-tillägget (Open Service nät (OSM) Azure Kubernetes service ()

### <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här genom gången förutsätter att du har skapat ett AKS-kluster (Kubernetes `1.19+` och senare, med KUBERNETES RBAC aktiverat), har upprättat en `kubectl` anslutning till klustret (om du behöver hjälp med något av dessa objekt kan du se [AKS snabb start](./kubernetes-walkthrough.md)och installera AKS OSM-tillägget.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.20.0 eller senare
- `aks-preview`Tilläggs version 0.5.5 eller senare
- OSM, version v 0.8.0 eller senare
- apt – Hämta installations JQ

### <a name="create-namespaces-for-the-application"></a>Skapa namn rymder för programmet

I den här genom gången ska vi använda OSM bok handels program som har följande Kubernetes-tjänster:

- bookbuyer
- bookthief
- hittar
- bookwarehouse

Skapa namn områden för var och en av dessa program komponenter.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Du bör se följande utdata:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Publicera de namn områden som ska hanteras av OSM

När du lägger till namn områdena i OSM-nätnätet gör det att OSM-kontrollanten automatiskt kan mata in mottagare-enheter med sidvagn med ditt program. Kör följande kommando för att publicera OSM bok i bok sluts namn områden.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Du bör se följande utdata:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuera programmet för bok handeln till AKS-klustret

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Alla utdata för distributionen sammanfattas nedan.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="checkpoint-what-got-installed"></a>Kontroll punkt: Vad har installerats?

Exempel programmet bok handel är en app med flera nivåer som består av fyra tjänster, som är bookbuyer, bookthief, bok handel och bookwarehouse. Både bookbuyer-och bookthief-tjänsten kommunicerar med bok handeln för att hämta böcker från bok handeln. I bok handels tjänsten hämtas böcker från bookwarehouse-tjänsten för att tillhandahålla bookbuyer och bookthief. Detta är ett enkelt program med flera nivåer som fungerar bra när du visar hur ett tjänst nät kan användas för att skydda och auktorisera kommunikation mellan program tjänsterna. När vi fortsätter genom genom gången, kommer vi att aktivera och inaktivera SMI-principer (service nät gränssnitt) för att både tillåta och neka tjänsterna att kommunicera via OSM. Nedan visas ett arkitektur diagram över vad som har installerats för programmet i bok handeln.

![Arkitektur för OSM bookbuyer-appar](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Kontrol lera att programmet i bok handeln körs i AKS-klustret

Från och med nu har vi distribuerat programmet bok handel mulit, men det är bara tillgängligt i AKS-klustret. Senare självstudier hjälper dig att exponera programmet utanför klustret via en ingångs kontroll. För tillfället kommer vi att använda port vidarebefordring för att få åtkomst till bookbuyer-programmet i AKS-klustret för att kontrol lera att det är inköps böcker från bok handeln.

För att verifiera att programmet körs i klustret använder vi en port för att se användar gränssnittet för bookbuyer och bookthief-komponenter.

Först ska vi hämta bookbuyer-Pod namn

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Du bör se utdata som liknar följande. Bookbuyer-Pod kommer att ha ett unikt namn.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

När vi har fått Pod namn kan vi nu använda kommandot Port-Forward för att konfigurera en tunnel från vårt lokala system till programmet i AKS-klustret. Kör följande kommando för att konfigurera porten framåt för den lokala system porten 8080. Använd det angivna bookbuyer Pod-namnet igen.

> [!NOTE]
> För alla port vidarebefordrings kommandon är det bäst att använda en ytterligare Terminal så att du kan fortsätta att arbeta med den här genom gången och inte koppla från tunneln. Det är också bäst att du upprättar Port Forward-tunneln utanför Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Du bör se utdata som liknar detta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

När sessionen för port vidarebefordring är på plats, navigerar du till följande URL från en webbläsare `http://localhost:8080` . Du bör nu kunna se användar gränssnittet för bookbuyer i webbläsaren som liknar bilden nedan.

![Avbildning av OSM bookbuyer app UI](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Du kommer också att märka att det totala antalet böcker som har köpts fortsätter att öka till service of Book v1. Tjänsten för bok handel v2 har ännu inte distribuerats. Vi kommer att distribuera tjänsten för bok handeln i böcker v2 När vi demonstrerar de delade principerna för SMI-trafik.

Du kan också kontrol lera samma för bookthief-tjänsten.

```azurecli-interactive
kubectl get pod -n bookthief
```

Du bör se utdata som liknar följande. Bookthief-Pod kommer att ha ett unikt namn.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Port vidarebefordra till bookthief-pod.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Navigera till följande URL från en webbläsare `http://localhost:8080` . Du bör se att bookthief för närvarande stjäl böcker från bok handeln! Senare kommer vi att implementera en trafik princip för att stoppa bookthief.

![Avbildning av OSM bookthief app UI](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Inaktivera OSM läge för tillstånds trafik för nätet

Som vi nämnt tidigare när du visade kluster konfigurationen för OSM, så använder OSM-konfigurationen standardinställningen att principen för tillstånds trafik läge aktive ras. I det här läget kringgås trafik policyn och OSM identifieras automatiskt av tjänster som är en del av policy reglerna för service nät och program trafik på varje mottagare-för att kunna kommunicera med dessa tjänster.

Vi kommer nu att inaktivera principen för tillåtelse trafik läge och OSM kommer att behöva explicit [SMI](https://smi-spec.io/) -principer distribuerade till klustret för att tillåta kommunikation i nätet från varje tjänst. Om du vill inaktivera läget för tillstånds trafik kan du köra följande kommando för att uppdatera egenskapen configmap för att ändra värdet från `true` till `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Du bör se utdata som liknar följande. Bookthief-Pod kommer att ha ett unikt namn.

```Output
configmap/osm-config patched
```

Om du vill kontrol lera att läget för tillstånds trafik har inaktiverats kan du gå tillbaka till antingen bookbuyer-eller bookthief-Pod för att Visa användar gränssnittet i webbläsaren och se om de köpta böckerna eller böckerna som är stulna inte längre ökar. Se till att uppdatera webbläsaren. Om ökningen har stoppats tillämpas principen på rätt sätt. Du har stoppat bookthief från att stjäla böcker, men varken bookbuyer kan köpa från bok handeln eller i bok handeln kan hämta böcker från bookwarehouse. Härnäst ska vi implementera [SMI](https://smi-spec.io/) -principer för att bara tillåta de tjänster i nätet som du vill kommunicera med detta.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Tillämpa principer för trafik åtkomst för service nät gränssnitt (SMI)

Nu när vi har inaktiverat all kommunikation i nätet är det dags att vår bookbuyer-tjänst kommunicerar med vår bok handel för inköps böcker, och gör att vår bok handel kan kommunicera med vår bookwarehouse-tjänst för att hämta böcker för försäljning.

Distribuera följande [SMI](https://smi-spec.io/) -principer.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookstore-access-bookwarehouse
  namespace: bookwarehouse
spec:
  destination:
    kind: ServiceAccount
    name: bookwarehouse
    namespace: bookwarehouse
  rules:
  - kind: HTTPRouteGroup
    name: bookwarehouse-service-routes
    matches:
    - restock-books
  sources:
  - kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  - kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookwarehouse-service-routes
  namespace: bookwarehouse
spec:
  matches:
    - name: restock-books
      methods:
      - POST
      headers:
      - host: bookwarehouse.bookwarehouse
EOF
```

Du bör se utdata som liknar följande.

```Output
traffictarget.access.smi-spec.io/bookbuyer-access-bookstore-v1 created
httproutegroup.specs.smi-spec.io/bookstore-service-routes created
traffictarget.access.smi-spec.io/bookstore-access-bookwarehouse created
httproutegroup.specs.smi-spec.io/bookwarehouse-service-routes created
```

Nu kan du konfigurera en session för vidarebefordran av en port i bookbuyer-eller bok-poddar och se att både de böcker som har köpts och de sålda måtten för böcker ökar. Du kan också göra samma för bookthief-Pod för att kontrol lera att det fortfarande inte längre går att stjäla böcker.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Tillämpa SMI-trafik (service näts Interface) delade principer för trafik

För vår slutgiltiga demonstration kommer vi att skapa en [SMI](https://smi-spec.io/) Traffic Split-princip för att konfigurera vikten av kommunikation från en tjänst till flera tjänster som en server del. Med funktionen för trafik delning kan du gradvis flytta anslutningar till en tjänst till en annan genom att väga trafiken på en skala från 0 till 100.

Bilden nedan är ett diagram över den [SMI](https://smi-spec.io/) Traffic Split-princip som ska distribueras. Vi kommer att distribuera en ytterligare bok i version 2 och sedan dela in inkommande trafik från bookbuyer, med en vikt på 25% av trafiken till bok handel v1 och 75% till tjänsten i bok handeln.

![OSM bookbuyer Traffic Split diagram](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Distribuera tjänsten för bok handeln v2.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: v1
kind: Service
metadata:
  name: bookstore-v2
  namespace: bookstore
  labels:
    app: bookstore-v2
spec:
  ports:
  - port: 14001
    name: bookstore-port
  selector:
    app: bookstore-v2
---
# Deploy bookstore-v2 Service Account
apiVersion: v1
kind: ServiceAccount
metadata:
  name: bookstore-v2
  namespace: bookstore
---
# Deploy bookstore-v2 Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: bookstore-v2
  namespace: bookstore
spec:
  replicas: 1
  selector:
    matchLabels:
      app: bookstore-v2
  template:
    metadata:
      labels:
        app: bookstore-v2
    spec:
      serviceAccountName: bookstore-v2
      containers:
        - name: bookstore
          image: openservicemesh/bookstore:v0.8.0
          imagePullPolicy: Always
          ports:
            - containerPort: 14001
              name: web
          command: ["/bookstore"]
          args: ["--path", "./", "--port", "14001"]
          env:
            - name: BOOKWAREHOUSE_NAMESPACE
              value: bookwarehouse
            - name: IDENTITY
              value: bookstore-v2
---
kind: TrafficTarget
apiVersion: access.smi-spec.io/v1alpha3
metadata:
  name: bookbuyer-access-bookstore-v2
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore-v2
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
EOF
```

Du bör se följande utdata.

```Output
service/bookstore-v2 configured
serviceaccount/bookstore-v2 created
deployment.apps/bookstore-v2 created
traffictarget.access.smi-spec.io/bookstore-v2 created
```

Distribuera nu trafik delnings policyn för att dela upp bookbuyer-trafiken mellan de två bok handel v1 och v2.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha2
kind: TrafficSplit
metadata:
  name: bookstore-split
  namespace: bookstore
spec:
  service: bookstore.bookstore
  backends:
  - service: bookstore
    weight: 25
  - service: bookstore-v2
    weight: 75
EOF
```

Du bör se följande utdata.

```Output
trafficsplit.split.smi-spec.io/bookstore-split created
```

Konfigurera en port termins tunnel till bookbuyer-Pod och nu bör du se böcker som du har köpt från tjänsten i Book v2. Om du fortsätter att titta på ökningen av köp bör du notera att en snabbare ökning av köp sker via tjänsten för bok handeln v2.

![OSM bookbuyer Books boough UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Hantera befintliga distribuerade program som ska hanteras av AKS-tillägget (Open Service nät (OSM) Azure Kubernetes service ()

### <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här genom gången förutsätter att du tidigare har aktiverat OSM AKS-tillägget för ditt AKS-kluster. Om inte kan du läsa avsnittet [Aktivera AKS-tillägget (Open Service nät (OSM) Azure Kubernetes service () för ett befintligt AKS-kluster](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) innan du fortsätter. Dessutom måste ditt AKS-kluster vara version Kubernetes `1.19+` och högre, ha KUBERNETES RBAC aktiverat och har upprättat en `kubectl` anslutning till klustret (om du behöver hjälp med något av dessa objekt kan du se [AKS snabb start](./kubernetes-walkthrough.md)och installera AKS OSM-tillägget.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.20.0 eller senare
- `aks-preview`Tilläggs version 0.5.5 eller senare
- OSM, version v 0.8.0 eller senare
- apt – Hämta installations JQ

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Kontrol lera princip läge för OSM (Open Service nät)

OSM tillstånds princip läge är ett läge där [SMI](https://smi-spec.io/) -trafikens princip tvång kringgås. I det här läget identifierar OSM automatiskt tjänster som är en del av policy reglerna för service nät och program trafik på varje mottagare-för att kunna kommunicera med dessa tjänster.

Kör följande kommando för att kontrol lera det aktuella läget för OSM för klustret:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Utdata från OSM configmap bör se ut så här:

```Output
{
  "egress": "true",
  "enable_debug_server": "true",
  "envoy_log_level": "error",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Om **permissive_traffic_policy_mode** har kon figurer ATS till **True** kan du på ett säkert sätt publicera dina namn områden utan avbrott i tjänst-till-tjänst-kommunikationen. Om **permissive_traffic_policy_mode** har kon figurer ATS till **false** måste du se till att du har rätt manifest för [SMI](https://smi-spec.io/) -trafikåtkomst principer distribuerade och att du har ett tjänst konto som representerar varje tjänst som distribueras i namn området. Följ rikt linjerna för att [publicera befintliga distribuerade program med OSM-trafik (Open Service nät) som kon figurer ATS som falskt](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Publicera befintliga distribuerade program med OSM (Open Service nät) princip för tillstånds trafik konfigurerad som sant

Det första vi ska göra är att lägga till de distribuerade program namn rymderna till OSM som ska hanteras.

```azurecli-interactive
osm namespace add bookstore
```

Du bör se följande utdata:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Nu ska vi ta en titt på den aktuella Pod-distributionen i namn området. Kör följande kommando för att Visa poddar i den angivna namn rymden.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Du bör se följande liknande utdata:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Observera kolumnen **klar** som visar **1/1**, vilket innebär att programmet Pod endast har en behållare. Därefter måste vi starta om dina program distributioner så att OSM kan mata in mottagare-behållaren för sidvagn med ditt program pod. Nu ska vi hämta en lista över distributioner i namn området.

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Du bör se följande utdata:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Nu kommer vi att starta om distributionen för att mata in mottagare-den sidvagn-proxy-behållaren med ditt program pod. Kör följande kommando.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Du bör se följande utdata:

```Output
deployment.apps/bookbuyer restarted
```

Om vi tar en titt på poddar i namn området igen:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Nu ser du att kolumnen **klart** visar att **2/2** behållare är klara för din POD. Den andra behållaren är den mottagare sidvagn-proxyn.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Vi kan granska Pod ytterligare och Visa mottagare-proxyn genom att köra kommandot beskriva för att visa konfigurationen.

```azurecli-interactive
kubectl describe pod bookbuyer-84446dd5bd-j4tlr -n bookbuyer
```

```Output
Containers:
  bookbuyer:
    Container ID:  containerd://b7503b866f915711002292ea53970bd994e788e33fb718f1c4f8f12cd4a88198
    Image:         openservicemesh/bookbuyer:v0.8.0
    Image ID:      docker.io/openservicemesh/bookbuyer@sha256:813874bd2dc9c5a259b9657995348cf0822b905e29c4e86f21fdefa0ef21dcee
    Port:          <none>
    Host Port:     <none>
    Command:
      /bookbuyer
    State:          Running
      Started:      Tue, 23 Mar 2021 10:52:53 -0400
    Ready:          True
    Restart Count:  0
    Environment:
      BOOKSTORE_NAMESPACE:  bookstore
      BOOKSTORE_SVC:        bookstore
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from bookbuyer-token-zft2r (ro)
  envoy:
    Container ID:  containerd://f5f1cb5db8d5304e23cc984eb08146ea162a3e82d4262c4472c28d5579c25e10
    Image:         envoyproxy/envoy-alpine:v1.17.1
    Image ID:      docker.io/envoyproxy/envoy-alpine@sha256:511e76b9b73fccd98af2fbfb75c34833343d1999469229fdfb191abd2bbe3dfb
    Ports:         15000/TCP, 15003/TCP, 15010/TCP
    Host Ports:    0/TCP, 0/TCP, 0/TCP
```

Kontrol lera att programmet fortfarande fungerar efter mottagare-insprutning av sidvagn.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Publicera befintliga distribuerade program med OSM (Open Service nät) princip för tillstånds trafik konfigurerad som falsk

När OSM-konfigurationen för principen för tillstånds trafik är inställd på `false` , kräver OSM uttryckliga [SMI](https://smi-spec.io/) -trafik åtkomst principer som distribueras för kommunikation mellan tjänster och tjänster i klustret. För närvarande använder OSM också Kubernetes-tjänstekonton som en del av auktoriseringen av tjänst-till-tjänst-kommunikation. För att se till att dina befintliga distribuerade program kommunicerar när de hanteras av OSM-nätet måste vi kontrol lera att det finns ett tjänst konto som ska användas, uppdatera program distributionen med tjänst konto informationen, tillämpa [SMI](https://smi-spec.io/) Traffic Access-principer.

#### <a name="verify-kubernetes-service-accounts"></a>Verifiera Kubernetes-tjänstekonton

Kontrol lera att du har ett Kubernetes-tjänstkonto i namn området som programmet distribueras till.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

I följande finns ett tjänst konto med namnet `bookbuyer` i namn området bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Om du inte har något annat tjänst konto än standard kontot måste du skapa ett för ditt program. Använd följande kommando som exempel för att skapa ett tjänst konto i programmets distribuerade namnrymd.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Visa programmets aktuella distributions specifikation

Om du hade skapat ett tjänst konto från det tidigare avsnittet är risken att program distributionen inte är konfigurerad med en specifik `serviceAccountName` i distributions specifikationen. Vi kan se programmets distributions specifikation med följande kommandon:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

En lista över distributioner visas i utdata.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Nu ska vi beskriva distributionen som en kontroll för att se om det finns ett tjänst konto som anges i avsnittet Pod Template.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

I den här specifika distributionen kan du se att det finns ett tjänst konto som är kopplat till distributionen som anges i avsnittet Pod Template. Den här distributionen använder tjänst kontot bookbuyer. Om du inte ser egenskapen **tjänsten Account:** är din distribution inte konfigurerad att använda ett tjänst konto.

```Output
Pod Template:
  Labels:           app=bookbuyer
                    version=v1
  Annotations:      kubectl.kubernetes.io/restartedAt: 2021-03-23T10:52:49-04:00
  Service Account:  bookbuyer
  Containers:
   bookbuyer:
    Image:      openservicemesh/bookbuyer:v0.8.0

```

Det finns flera tekniker som du kan använda för att uppdatera distributionen för att lägga till ett Kubernetes-tjänstkonto. Läs Kubernetes-dokumentationen om att [Uppdatera en distributions](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) intern eller [Konfigurera tjänst konton för poddar](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/). När du har uppdaterat distributions specifikationen med tjänst kontot kan du distribuera om (kubectl tillämpa-f din-Deployment. yaml) distributionen till klustret.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Distribuera de nödvändiga principerna för service nät gränssnitt (SMI)

Det sista steget för att tillåta auktoriserad trafik att flöda i nätet är att distribuera de nödvändiga åtkomst principerna för [SMI](https://smi-spec.io/) -trafik för ditt program. Mängden konfiguration som du kan uppnå med [SMI](https://smi-spec.io/) -trafikens åtkomst principer ligger utanför omfånget för den här genom gången, men vi kommer att se några av de vanliga komponenterna i specifikationen och visa hur du konfigurerar både en enkel TrafficTarget och HTTPRouteGroup-princip för att aktivera kommunikation mellan tjänster och tjänster för ditt program.

Med hjälp av [SMI](https://smi-spec.io/) - [**trafiken Access Control**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) specifikation kan användarna definiera principen för åtkomst kontroll för sina program. Vi fokuserar på **TrafficTarget** -och **HTTPRoutGroup** API-resurser.

TrafficTarget-resursen består av tre huvudsakliga konfigurations inställningar mål, regler och källor. Ett exempel på en TrafficTarget visas nedan.

```TrafficTarget Example spec
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
```

I ovanstående TrafficTarget-spec noterar det `destination` tjänst konto som har kon figurer ATS för mål käll tjänsten. Kom ihåg att det tjänst konto som lades till i distributionen tidigare kommer att användas för att ge åtkomst till den distribution som det är kopplat till. `rules`Avsnittet i det här exemplet definierar vilken typ av HTTP-trafik som tillåts över anslutningen. Du kan konfigurera fina regex-mönster för att HTTP-huvudena ska vara mer information om vilken trafik som tillåts via HTTP. `sources`Avsnittet är tjänsten som kommer från kommunikationen. Den här specifikationen läser bookbuyer måste kommunicera med bok handeln.

HTTPRouteGroup-resursen består av en eller en matris med matchningar av HTTP-huvudinformation och är ett krav för TrafficTarget-specifikationen. I exemplet nedan kan du se att HTTPRouteGroup auktoriserar tre HTTP-åtgärder, två GET-och ett-inlägg.

```HTTPRouteGroup Example Spec
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
```

Om du inte är bekant med den typ av HTTP-trafik som ditt klient program gör till andra nivåer av programmet, eftersom TrafficTarget-specifikationen kräver en regel, kan du skapa motsvarigheten till en Tillåt alla regler med hjälp av nedanstående specifikation för HTTPRouteGroup.

```HTTPRouteGroup Allow All Example
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: allow-all
  namespace: yournamespace
spec:
  matches:
  - name: allow-all
    pathRegex: '.*'
    methods: ["GET","PUT","POST","DELETE","PATCH"]
```

När du har konfigurerat din TrafficTarget-och HTTPRouteGroup-specifikation kan du placera dem tillsammans som en YAML och distribuera. Nedan visas exempel konfigurationen för bok handeln.

```Bookstore Example TrafficTarget and HTTPRouteGroup configuration
kubectl apply -f - <<EOF
---
apiVersion: access.smi-spec.io/v1alpha3
kind: TrafficTarget
metadata:
  name: bookbuyer-access-bookstore-v1
  namespace: bookstore
spec:
  destination:
    kind: ServiceAccount
    name: bookstore
    namespace: bookstore
  rules:
  - kind: HTTPRouteGroup
    name: bookstore-service-routes
    matches:
    - buy-a-book
    - books-bought
  sources:
  - kind: ServiceAccount
    name: bookbuyer
    namespace: bookbuyer
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: bookstore-service-routes
  namespace: bookstore
spec:
  matches:
  - name: books-bought
    pathRegex: /books-bought
    methods:
    - GET
    headers:
    - "user-agent": ".*-http-client/*.*"
    - "client-app": "bookbuyer"
  - name: buy-a-book
    pathRegex: ".*a-book.*new"
    methods:
    - GET
  - name: update-books-bought
    pathRegex: /update-books-bought
    methods:
    - POST
EOF
```

Besök webbplatsen [SMI](https://smi-spec.io/) om du vill ha mer detaljerad information om specifikationen.

### <a name="manage-the-applications-namespace-with-osm"></a>Hantera programmets namnrymd med OSM

Nu ska vi konfigurera OSM för att hantera namn området och starta om distributionerna för att hämta mottagare-den sidvagn-proxyn som injiceras med programmet.

Kör följande kommando för att konfigurera `azure-vote` namn området för hantering av mina OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Starta sedan om både `azure-vote-front` och- `azure-vote-back` distributionerna med följande kommandon.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Om vi visar poddar för `azure-vote` namn området kommer vi att se det **färdiga** steget i både `azure-vote-front` och `azure-vote-back` som 2/2, vilket innebär att den mottagarea sidvagn-proxyn har matats in tillsammans med programmet.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Självstudie: Distribuera ett program som hanteras av Open Service nät (OSM) med NGINX ingress

Open Service-nätet (OSM) är ett enkelt, utöknings Bart, moln nätverk med ett nät som gör det möjligt för användare att enhetligt hantera, säkra och få välkomst funktioner för att få till gång till de funktioner som är tillgängliga för mycket dynamiska mikrotjänster.

I de här självstudierna får du:

> [!div class="checklist"]
>
> - Visa den aktuella kluster konfigurationen för OSM
> - Skapa namn område (er) för OSM för att hantera distribuerade program i namn områdena
> - Publicera de namn områden som ska hanteras av OSM
> - Distribuera exempelprogrammet
> - Verifiera att programmet körs i AKS-klustret
> - Skapa en NGINX ingress-kontrollant som används för programmet
> - Exponera en tjänst via Azure Application Gateway ingress till Internet

### <a name="before-you-begin"></a>Innan du börjar

I de steg som beskrivs i den här artikeln förutsätter vi att du har skapat ett AKS-kluster (Kubernetes `1.19+` och senare, med KUBERNETES RBAC aktiverat), har upprättat en `kubectl` anslutning till klustret (om du behöver hjälp med något av dessa objekt kan du läsa [AKS-snabb](./kubernetes-walkthrough.md)starten och installera AKS OSM-tillägget.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.20.0 eller senare
- `aks-preview`Tilläggs version 0.5.5 eller senare
- OSM, version v 0.8.0 eller senare
- apt – Hämta installations JQ

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visa och verifiera den aktuella kluster konfigurationen för OSM

När OSM-tillägget för AKS har Aktiver ATS i AKS-klustret kan du Visa de aktuella konfigurations parametrarna i OSM-config Kubernetes ConfigMap. Kör följande kommando för att Visa ConfigMap-egenskaperna:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Utdata visar den aktuella OSM-konfigurationen för klustret.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Observera att **permissive_traffic_policy_mode** har kon figurer ATS till **True**. Läge för tillstånds trafik i OSM är ett läge där [SMI](https://smi-spec.io/) -trafikens princip tvång kringgås. I det här läget identifierar OSM automatiskt tjänster som är en del av policy reglerna för service nät och program trafik på varje mottagare-för att kunna kommunicera med dessa tjänster.

### <a name="create-namespaces-for-the-application"></a>Skapa namn rymder för programmet

I den här självstudien kommer vi att använda OSM bok handels program som har följande program komponenter:

- bookbuyer
- bookthief
- hittar
- bookwarehouse

Skapa namn områden för var och en av dessa program komponenter.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Du bör se följande utdata:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Publicera de namn områden som ska hanteras av OSM

Genom att lägga till namn områdena i OSM-kommer OSM-styrenheten att automatiskt mata in mottagare-enheter med sidvagn med ditt program. Kör följande kommando för att publicera OSM bok i bok sluts namn områden.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Du bör se följande utdata:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuera programmet för bok handeln till AKS-klustret

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Alla utdata för distributionen sammanfattas nedan.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Uppdatera Bookbuyer-tjänsten

Uppdatera bookbuyer-tjänsten till rätt inkommande port konfiguration med följande tjänst manifest.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Kontrol lera att programmet i bok handeln körs i AKS-klustret

Från och med nu har vi distribuerat programmet bok handel mulit, men det är bara tillgängligt i AKS-klustret. Senare kommer vi att lägga till Azure Application Gateway-ingångs styrenheten för att exponera programmet utanför AKS-klustret. För att verifiera att programmet körs i klustret använder vi en port för att se användar gränssnittet för bookbuyer-komponenten.

Först ska vi hämta bookbuyer-Pod namn

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Du bör se utdata som liknar följande. Bookbuyer-Pod kommer att ha ett unikt namn.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

När vi har fått Pod namn kan vi nu använda kommandot Port-Forward för att konfigurera en tunnel från vårt lokala system till programmet i AKS-klustret. Kör följande kommando för att konfigurera porten framåt för den lokala system porten 8080. Använd det angivna bookbuyer Pod-namnet igen.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Du bör se utdata som liknar detta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

När sessionen för port vidarebefordring är på plats, navigerar du till följande URL från en webbläsare `http://localhost:8080` . Du bör nu kunna se användar gränssnittet för bookbuyer i webbläsaren som liknar bilden nedan.

![OSM bookbuyer-app för NGINX UI-avbildning](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Skapa en NGINX ingress-kontrollant i Azure Kubernetes service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Vi använder ingångs styrenheten för att exponera programmet som hanteras av OSM till Internet. Om du vill skapa en ingångs kontroll, använder du Helm för att installera nginx-ingress. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Se till att det finns fler än en nod i ditt AKS-kluster för att få full nytta av att köra repliker av ingångs styrenheten.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för de ingress-resurser som heter _ingress-Basic_. Ange ett namn område för din egen miljö efter behov.

```azurecli-interactive
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the ingress-nginx repository
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx

# Update the helm repo(s)
helm repo update

# Use Helm to deploy an NGINX ingress controller in the ingress-basic namespace
helm install nginx-ingress ingress-nginx/ingress-nginx \
    --namespace ingress-basic \
    --set controller.replicaCount=1 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.admissionWebhooks.patch.nodeSelector."beta\.kubernetes\.io/os"=linux
```

När belastnings Utjämnings tjänsten för Kubernetes skapas för NGINX-ingångs styrenheten tilldelas en dynamisk offentlig IP-adress, som visas i följande exempel:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Inga ingångs regler har skapats ännu. därför visas sidan NGINX ingress Controller standard 404 om du bläddrar till den interna IP-adressen. Ingress-regler konfigureras i följande steg.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exponera bookbuyer-tjänsten för Internet

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: nginx

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Du bör se följande utdata:

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

### <a name="view-the-nginx-logs"></a>Visa NGINX-loggarna

```azurecli-interactive
POD=$(kubectl get pods -n ingress-basic | grep 'nginx-ingress' | awk '{print $1}')

kubectl logs $POD -n ingress-basic -f
```

Utdata visar status för ingångs styrenheten för NGINX när ingångs regeln har tillämpats:

```Output
I0321 <date>       6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Ingress", Namespace:"bookbuyer", Name:"bookbuyer-ingress", UID:"e1018efc-8116-493c-9999-294b4566819e", APIVersion:"networking.k8s.io/v1beta1", ResourceVersion:"5460", FieldPath:""}): type: 'Normal' reason: 'Sync' Scheduled for sync
I0321 <date>        6 controller.go:146] "Configuration changes detected, backend reload required"
I0321 <date>        6 controller.go:163] "Backend successfully reloaded"
I0321 <date>        6 event.go:282] Event(v1.ObjectReference{Kind:"Pod", Namespace:"ingress-basic", Name:"nginx-ingress-ingress-nginx-controller-54cf6c8bf4-jdvrw", UID:"3ebbe5e5-50ef-481d-954d-4b82a499ebe1", APIVersion:"v1", ResourceVersion:"3272", FieldPath:""}): type: 'Normal' reason: 'RELOAD' NGINX reload triggered due to a change in configuration
```

### <a name="view-the-nginx-services-and-bookbuyer-service-externally"></a>Visa NGINX-tjänsterna och bookbuyer-tjänsten externt

```azurecli-interactive
kubectl get services -n ingress-basic
```

```Output
NAME                                               TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-ingress-nginx-controller             LoadBalancer   10.0.100.23   20.193.1.74   80:31742/TCP,443:32683/TCP   4m15s
nginx-ingress-ingress-nginx-controller-admission   ClusterIP      10.0.163.98   <none>        443/TCP                      4m15s
```

Eftersom värd namnet i ingress-manifestet är ett psuedo namn som används för testning, kommer DNS-namnet inte att vara tillgängligt på Internet. Vi kan också använda programmet för att aktivera och klistra in hostname-huvudet till den offentliga IP-adressen för NGINX och få en 200-kod som ansluter oss till bookbuyer-tjänsten.

```azurecli-interactive
curl -H 'Host: bookbuyer.contoso.com' http://EXTERNAL-IP/
```

Du bör se följande utdata:

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>1833</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>1556</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 15:02:53 UTC</strong>
  </body>
</html>
```

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Självstudie: Distribuera ett program som hanteras av Open Service nät (OSM) med Azure Application Gateway ingress AKS-tillägg

Open Service-nätet (OSM) är ett enkelt, utöknings Bart, moln nätverk med ett nät som gör det möjligt för användare att enhetligt hantera, säkra och få välkomst funktioner för att få till gång till de funktioner som är tillgängliga för mycket dynamiska mikrotjänster.

I de här självstudierna får du:

> [!div class="checklist"]
>
> - Visa den aktuella kluster konfigurationen för OSM
> - Skapa namn område (er) för OSM för att hantera distribuerade program i namn områdena
> - Publicera de namn områden som ska hanteras av OSM
> - Distribuera exempelprogrammet
> - Verifiera att programmet körs i AKS-klustret
> - Skapa en Azure Application Gateway som ska användas som ingångs kontroll för programmet
> - Exponera en tjänst via Azure Application Gateway ingress till Internet

### <a name="before-you-begin"></a>Innan du börjar

De steg som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes `1.19+` och senare, med KUBERNETES RBAC aktiverat), har upprättat en `kubectl` anslutning till klustret (om du behöver hjälp med något av dessa objekt kan du läsa [AKS-snabb](./kubernetes-walkthrough.md)starten, ha installerat AKS OSM-tillägget och skapa en ny Azure Application Gateway för ingångs åtgärder.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.20.0 eller senare
- `aks-preview`Tilläggs version 0.5.5 eller senare
- AKS Cluster version 1.19 + använda Azure CNI Networking (ansluten till ett Azure VNet)
- OSM, version v 0.8.0 eller senare
- apt – Hämta installations JQ

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visa och verifiera den aktuella kluster konfigurationen för OSM

När OSM-tillägget för AKS har Aktiver ATS i AKS-klustret kan du Visa de aktuella konfigurations parametrarna i OSM-config Kubernetes ConfigMap. Kör följande kommando för att Visa ConfigMap-egenskaperna:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Utdata visar den aktuella OSM-konfigurationen för klustret.

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.57.43",
  "permissive_traffic_policy_mode": "false",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

Observera att **permissive_traffic_policy_mode** har kon figurer ATS till **True**. Läge för tillstånds trafik i OSM är ett läge där [SMI](https://smi-spec.io/) -trafikens princip tvång kringgås. I det här läget identifierar OSM automatiskt tjänster som är en del av policy reglerna för service nät och program trafik på varje mottagare-för att kunna kommunicera med dessa tjänster.

### <a name="create-namespaces-for-the-application"></a>Skapa namn rymder för programmet

I den här självstudien kommer vi att använda OSM bok handels program som har följande program komponenter:

- bookbuyer
- bookthief
- hittar
- bookwarehouse

Skapa namn områden för var och en av dessa program komponenter.

```azurecli-interactive
for i in bookstore bookbuyer bookthief bookwarehouse; do kubectl create ns $i; done
```

Du bör se följande utdata:

```Output
namespace/bookstore created
namespace/bookbuyer created
namespace/bookthief created
namespace/bookwarehouse created
```

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Publicera de namn områden som ska hanteras av OSM

När du lägger till namn områdena i OSM-nätnätet gör det att OSM-kontrollanten automatiskt kan mata in mottagare-enheter med sidvagn med ditt program. Kör följande kommando för att publicera OSM bok i bok sluts namn områden.

```azurecli-interactive
osm namespace add bookstore bookbuyer bookthief bookwarehouse
```

Du bör se följande utdata:

```Output
Namespace [bookstore] successfully added to mesh [osm]
Namespace [bookbuyer] successfully added to mesh [osm]
Namespace [bookthief] successfully added to mesh [osm]
Namespace [bookwarehouse] successfully added to mesh [osm]
```

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuera programmet för bok handeln till AKS-klustret

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookbuyer.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookthief.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookstore.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/release-v0.8/docs/example/manifests/apps/bookwarehouse.yaml
```

Alla utdata för distributionen sammanfattas nedan.

```Output
serviceaccount/bookbuyer created
service/bookbuyer created
deployment.apps/bookbuyer created

serviceaccount/bookthief created
service/bookthief created
deployment.apps/bookthief created

service/bookstore created
serviceaccount/bookstore created
deployment.apps/bookstore created

serviceaccount/bookwarehouse created
service/bookwarehouse created
deployment.apps/bookwarehouse created
```

### <a name="update-the-bookbuyer-service"></a>Uppdatera Bookbuyer-tjänsten

Uppdatera bookbuyer-tjänsten till rätt inkommande port konfiguration med följande tjänst manifest.

```azurecli-interactive
kubectl apply -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: bookbuyer
  namespace: bookbuyer
  labels:
    app: bookbuyer
spec:
  ports:
  - port: 14001
    name: inbound-port
  selector:
    app: bookbuyer
EOF
```

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Kontrol lera att programmet i bok handeln körs i AKS-klustret

Från och med nu har vi distribuerat bok handel med flera behållare, men det är bara tillgängligt i AKS-klustret. Senare kommer vi att lägga till Azure Application Gateway-ingångs styrenheten för att exponera programmet utanför AKS-klustret. För att verifiera att programmet körs i klustret använder vi en port för att se användar gränssnittet för bookbuyer-komponenten.

Först ska vi hämta bookbuyer-Pod namn

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Du bör se utdata som liknar följande. Bookbuyer-Pod kommer att ha ett unikt namn.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

När vi har fått Pod namn kan vi nu använda kommandot Port-Forward för att konfigurera en tunnel från vårt lokala system till programmet i AKS-klustret. Kör följande kommando för att konfigurera porten framåt för den lokala system porten 8080. Använd det aktuella bookbuyer Pod-namnet igen.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Du bör se utdata som liknar detta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

När sessionen för port vidarebefordring är på plats, navigerar du till följande URL från en webbläsare `http://localhost:8080` . Du bör nu kunna se användar gränssnittet för bookbuyer i webbläsaren som liknar bilden nedan.

![Avbildning av OSM bookbuyer-app för app Gateway-gränssnitt](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Skapa en Azure Application-Gateway för att exponera bookbuyer-programmet utanför AKS-klustret

> [!NOTE]
> I följande riktningar skapas en ny instans av Azure Application Gateway som ska användas för ingress. Om du har en befintlig Azure Application Gateway som du vill använda går du vidare till avsnittet för att aktivera tillägget Application Gateway ingångs kontroll.

#### <a name="deploy-a-new-application-gateway"></a>Distribuera en ny Application Gateway

> [!NOTE]
> Vi refererar till befintlig dokumentation för att aktivera tillägget Application Gateway ingress-kontrollant för ett befintligt AKS-kluster. Vissa ändringar har gjorts för att passa OSM-material. Mer detaljerad dokumentation om ämnet hittar du [här](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing).

Nu ska du distribuera en ny Application Gateway, för att simulera att ha en befintlig Application Gateway som du vill använda för att belastningsutjämna trafik till ditt AKS-kluster, för _klustret_. Namnet på Application Gateway kommer att vara _myApplicationGateway_, men du måste först skapa en offentlig IP-resurs med namnet _myPublicIp_ och ett nytt virtuellt nätverk som kallas _myVnet_ med adress utrymme 11.0.0.0/8 och ett undernät med adress utrymme 11.1.0.0/16 som kallas för _undernät_ och distribuera Application Gateway i _under nätet_ med _myPublicIp_.

När du använder ett AKS-kluster och Application Gateway i separata virtuella nätverk, får inte adress utrymmena för de två virtuella nätverken överlappa varandra. Standard adress utrymmet som ett AKS-kluster distribuerar i är 10.0.0.0/8, så vi ställer in det Application Gateway virtuella nätverkets adressprefix till 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> AGIC-tillägget (Application Gateway ingress Controller) stöder **bara** Application Gateway v2 SKU: er (standard och WAF) och **inte** Application Gateway v1 SKU: er.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Aktivera AGIC-tillägget för ett befintligt AKS-kluster via Azure CLI

Om du vill fortsätta att använda Azure CLI kan du fortsätta att aktivera AGIC-tillägget i AKS-klustret som du skapade, till exempel ett _kluster_ och ange AGIC-tillägget för att använda den befintliga Application Gateway du skapade, _myApplicationGateway_.

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Du kan kontrol lera att Azure Application Gateway AKS-tillägget har Aktiver ATS med följande kommando.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Det här kommandot ska visa utdata som `true` .

#### <a name="peer-the-two-virtual-networks-together"></a>Peer de två virtuella nätverken tillsammans

Eftersom vi har distribuerat AKS-klustret i ett eget virtuellt nätverk och Application Gateway i ett annat virtuellt nätverk, måste du koppla ihop de två virtuella nätverken för att trafik ska flöda från Application Gateway till poddar i klustret. Peering de två virtuella nätverken kräver att du kör Azure CLI-kommandot två separata gånger för att säkerställa att anslutningen är dubbelriktad. Det första kommandot skapar en peering-anslutning från Application Gateway virtuella nätverket till det virtuella AKS-nätverket. det andra kommandot skapar en peering-anslutning i den andra riktningen.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exponera bookbuyer-tjänsten för Internet

Använd följande ingress-manifest till AKS-klustret för att exponera bookbuyer-tjänsten på Internet via Azure Application Gateway.

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: bookbuyer-ingress
  namespace: bookbuyer
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway

spec:

  rules:
    - host: bookbuyer.contoso.com
      http:
        paths:
        - path: /
          backend:
            serviceName: bookbuyer
            servicePort: 14001

  backend:
    serviceName: bookbuyer
    servicePort: 14001
EOF
```

Du bör se följande resultat

```Output
Warning: extensions/v1beta1 Ingress is deprecated in v1.14+, unavailable in v1.22+; use networking.k8s.io/v1 Ingress
ingress.extensions/bookbuyer-ingress created
```

Eftersom värd namnet i ingress-manifestet är ett pseudo-namn som används för testning, kommer DNS-namnet inte att vara tillgängligt på Internet. Vi kan också använda programmet för att aktivera och ta del av hostname-huvudet till den offentliga IP-adressen Azure Application Gateway och få en 200-kod som ansluter oss till bookbuyer-tjänsten.

```azurecli-interactive
appGWPIP=$(az network public-ip show -g MyResourceGroup -n myPublicIp -o tsv --query "ipAddress")
curl -H 'Host: bookbuyer.contoso.com' http://$appGWPIP/
```

Du bör se följande resultat

```Output
<!doctype html>
<html itemscope="" itemtype="http://schema.org/WebPage" lang="en">
  <head>
      <meta content="Bookbuyer" name="description">
      <meta content="text/html; charset=UTF-8" http-equiv="Content-Type">
      <title>Bookbuyer</title>
      <style>
        #navbar {
            width: 100%;
            height: 50px;
            display: table;
            border-spacing: 0;
            white-space: nowrap;
            line-height: normal;
            background-color: #0078D4;
            background-position: left top;
            background-repeat-x: repeat;
            background-image: none;
            color: white;
            font: 2.2em "Fira Sans", sans-serif;
        }
        #main {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.8em "Fira Sans", sans-serif;
        }
        li {
            padding: 10pt 10pt 10pt 10pt;
            font: 1.2em "Consolas", sans-serif;
        }
      </style>
      <script>
        setTimeout(function(){window.location.reload(1);}, 1500);
      </script>
  </head>
  <body bgcolor="#fff">
    <div id="navbar">
      &#128214; Bookbuyer
    </div>
    <div id="main">
      <ul>
        <li>Total books bought: <strong>5969</strong>
          <ul>
            <li>from bookstore V1: <strong>277</strong>
            <li>from bookstore V2: <strong>5692</strong>
          </ul>
        </li>
      </ul>
    </div>

    <br/><br/><br/><br/>
    <br/><br/><br/><br/>
    <br/><br/><br/><br/>

    Current Time: <strong>Fri, 26 Mar 2021 16:34:30 UTC</strong>
  </body>
</html>
```

### <a name="troubleshooting"></a>Felsökning

- [AGIC-felsöknings dokumentation](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Ytterligare fel söknings verktyg finns på AGIC GitHub-lagrings platsen](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Öppna service nät (OSM) övervakning och observationer med hjälp av Azure Monitor och program insikter

Både Azure Monitor och Azure Application insikter hjälper dig att maximera tillgängligheten och prestandan för dina program och tjänster genom att leverera en omfattande lösning för att samla in, analysera och agera på telemetri från molnet och lokala miljöer.

OSM AKS-tillägget kommer att ha djupgående integreringar i båda dessa Azure-tjänster och ger en miljö som ser ut att svara på kritiska KPI: er som tillhandahålls av OSM mått. Mer information om hur du aktiverar och konfigurerar dessa tjänster för OSM AKS-tillägget finns på sidan [Azure Monitor för OSM](https://aka.ms/azmon/osmpreview) .

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Självstudie: Distribuera Prometheus, Grafana och Jaeger manuellt för att Visa OSM-mått (Open Service nät) för att se om det går att Observera

> [!WARNING]
> Installationen av Prometheus, Grafana och Jaeger tillhandahålls som allmän vägledning för att visa hur dessa verktyg kan användas för att Visa OSM Metric-data. Installations vägledningen kan inte användas för produktions inställningar. Se varje verktygs dokumentation om hur bäst passar deras installationer. Det viktigaste är att det inte finns tillräckligt med lagrings utrymme, vilket innebär att alla data försvinner när en Prometheus-Grafana och/eller Jaeger Pod (s) har avslut ATS.

Öppna service nät (OSM) genererar detaljerade mått som rör all trafik i nätet. Dessa mått ger insikter om beteendet hos program i nätet som hjälper användarna att felsöka, underhålla och analysera sina program.

Från och med idag OSM samlar mått direkt från de sidvagn-proxyservrarna (mottagare). OSM tillhandahåller omfattande mått för inkommande och utgående trafik för alla tjänster i nätet. Med dessa mått kan användaren hämta information om den totala trafiken, fel i trafiken och svars tiden för förfrågningar.

OSM använder Prometheus för att samla in och lagra konsekventa trafik mått och statistik för alla program som körs i nätet. Prometheus är en övervaknings-och aviserings verktyg med öppen källkod som vanligt vis används på (men inte begränsat till) Kubernetes-och nät mask miljöer.

Varje program som är en del av nätet körs i en pod som innehåller en mottagare-sidvagn som visar mått (proxyadresser) i Prometheus-format. Dessutom har varje Pod som är en del av nätet Prometheus anteckningar, vilket gör det möjligt för Prometheus-servern att kassera programmet dynamiskt. Den här mekanismen aktiverar automatiskt kasse ring av mått när en ny namnrymd/Pod/tjänst läggs till i nätet.

OSM-mått kan visas med Grafana, som är en visualiserings-och analys program med öppen källkod. Du kan fråga, visualisera, Varna på och utforska dina mått.

I de här självstudierna får du:

> [!div class="checklist"]
>
> - Skapa och distribuera en Prometheus-instans
> - Konfigurera OSM för att tillåta Prometheus-kassationing
> - Uppdatera Prometheus-Configmap
> - Skapa och distribuera en Grafana-instans
> - Konfigurera Grafana med Prometheus-datakällan
> - Importera OSM-instrumentpanelen för Grafana
> - Skapa och distribuera en Jaeger-instans
> - Konfigurera Jaeger-spårning för OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Distribuera och konfigurera en Prometheus-instans för OSM

Vi kommer att använda Helm för att distribuera Prometheus-instansen. Kör följande kommandon för att installera Prometheus via Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Du bör se liknande utdata nedan om installationen lyckades. Anteckna Prometheus-serverns port och klustrets DNS-namn. Den här informationen kommer att användas senare för att konfigurera Prometheus som en data källa för Grafana.

```Output
NAME: stable
LAST DEPLOYED: Fri Mar 26 13:34:51 2021
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
The Prometheus server can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-server.default.svc.cluster.local


Get the Prometheus server URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9090


The Prometheus alertmanager can be accessed via port 80 on the following DNS name from within your cluster:
stable-prometheus-alertmanager.default.svc.cluster.local


Get the Alertmanager URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=alertmanager" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9093
#################################################################################
######   WARNING: Pod Security Policy has been moved to a global property.  #####
######            use .Values.podSecurityPolicy.enabled with pod-based      #####
######            annotations                                               #####
######            (e.g. .Values.nodeExporter.podSecurityPolicy.annotations) #####
#################################################################################


The Prometheus PushGateway can be accessed via port 9091 on the following DNS name from within your cluster:
stable-prometheus-pushgateway.default.svc.cluster.local


Get the PushGateway URL by running these commands in the same shell:
  export POD_NAME=$(kubectl get pods --namespace default -l "app=prometheus,component=pushgateway" -o jsonpath="{.items[0].metadata.name}")
  kubectl --namespace default port-forward $POD_NAME 9091

For more information on running Prometheus, visit:
https://prometheus.io/
```

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Konfigurera OSM för att tillåta Prometheus-kassationing

För att säkerställa att OSM-komponenterna har kon figurer ATS för Prometheus-skrot, vill vi kontrol lera **prometheus_scraping** konfiguration som finns i konfigurations filen OSM-config. Visa konfigurationen med följande kommando:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

Utdata från föregående kommando ska returneras `true` om OSM har kon figurer ATS för Prometheus-kassationing. Om det returnerade värdet är måste du `false` uppdatera konfigurationen `true` . Kör följande kommando för att aktivera **OSM Prometheus** -kasse ring:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Du bör se följande utdata.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Uppdatera Prometheus-Configmap

Standard installationen av Prometheus kommer att innehålla två Kubernetes-configmaps. Du kan visa listan med Prometheus-configmaps med följande kommando.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Vi måste ersätta Prometheus. yml-konfigurationen som finns i mappen **stabil-Prometheus-Server** configmap med följande OSM-konfiguration. Det finns flera fil redigerings tekniker för att utföra den här uppgiften. Ett enkelt och säkert sätt är att exportera configmap, skapa en kopia av den för säkerhets kopiering och sedan redigera den med en redigerare som Visual Studio Code.

> [!NOTE]
> Om du inte har Visual Studio Code installerat kan du hämta och installera den [här](https://code.visualstudio.com/Download).

Låt oss först exportera **Prometheus-Server-** configmap och sedan göra en kopia för säkerhets kopiering.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Nu ska vi öppna filen med Visual Studio Code som ska redige ras.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

När du har öppnat configmap i Visual Studio Code Editor ersätter du filen Prometheus. yml med OSM-konfigurationen nedan och sparar filen.

> [!WARNING]
> Det är mycket viktigt att du ser till att du behåller yaml-filens indrags struktur. Ändringar i fil strukturen yaml kan leda till att configmap inte kan tillämpas igen.

```OSM Prometheus Configmap Configuration
prometheus.yml: |
    global:
      scrape_interval: 10s
      scrape_timeout: 10s
      evaluation_interval: 1m

    scrape_configs:
      - job_name: 'kubernetes-apiservers'
        kubernetes_sd_configs:
        - role: endpoints
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
          # TODO need to remove this when the CA and SAN match
          insecure_skip_verify: true
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(apiserver_watch_events_total|apiserver_admission_webhook_rejection_count)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_namespace, __meta_kubernetes_service_name, __meta_kubernetes_endpoint_port_name]
          action: keep
          regex: default;kubernetes;https

      - job_name: 'kubernetes-nodes'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics

      - job_name: 'kubernetes-pods'
        kubernetes_sd_configs:
        - role: pod
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(envoy_server_live|envoy_cluster_upstream_rq_xx|envoy_cluster_upstream_cx_active|envoy_cluster_upstream_cx_tx_bytes_total|envoy_cluster_upstream_cx_rx_bytes_total|envoy_cluster_upstream_cx_destroy_remote_with_active_rq|envoy_cluster_upstream_cx_connect_timeout|envoy_cluster_upstream_cx_destroy_local_with_active_rq|envoy_cluster_upstream_rq_pending_failure_eject|envoy_cluster_upstream_rq_pending_overflow|envoy_cluster_upstream_rq_timeout|envoy_cluster_upstream_rq_rx_reset|^osm.*)'
          action: keep
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        - source_labels: [__meta_kubernetes_namespace]
          action: replace
          target_label: source_namespace
        - source_labels: [__meta_kubernetes_pod_name]
          action: replace
          target_label: source_pod_name
        - regex: '(__meta_kubernetes_pod_label_app)'
          action: labelmap
          replacement: source_service
        - regex: '(__meta_kubernetes_pod_label_osm_envoy_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
          action: drop
        # for non-ReplicaSets (DaemonSet, StatefulSet)
        # __meta_kubernetes_pod_controller_kind=DaemonSet
        # __meta_kubernetes_pod_controller_name=foo
        # =>
        # workload_kind=DaemonSet
        # workload_name=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          target_label: source_workload_kind
        - source_labels: [__meta_kubernetes_pod_controller_name]
          action: replace
          target_label: source_workload_name
        # for ReplicaSets
        # __meta_kubernetes_pod_controller_kind=ReplicaSet
        # __meta_kubernetes_pod_controller_name=foo-bar-123
        # =>
        # workload_kind=Deployment
        # workload_name=foo-bar
        # deplyment=foo
        - source_labels: [__meta_kubernetes_pod_controller_kind]
          action: replace
          regex: ^ReplicaSet$
          target_label: source_workload_kind
          replacement: Deployment
        - source_labels:
          - __meta_kubernetes_pod_controller_kind
          - __meta_kubernetes_pod_controller_name
          action: replace
          regex: ^ReplicaSet;(.*)-[^-]+$
          target_label: source_workload_name

      - job_name: 'smi-metrics'
        kubernetes_sd_configs:
        - role: pod
        relabel_configs:
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
          action: keep
          regex: true
        - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
          action: replace
          target_label: __metrics_path__
          regex: (.+)
        - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
          action: replace
          regex: ([^:]+)(?::\d+)?;(\d+)
          replacement: $1:$2
          target_label: __address__
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: 'envoy_.*osm_request_(total|duration_ms_(bucket|count|sum))'
          action: keep
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_(\d{3})_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: response_code
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_total
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_total
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_response_code_\d{3}_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_total
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_total)
          target_label: __name__

        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_(.*)_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_(.*)_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_(.*)_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_(.*)_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: source_pod
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_(.*)_destination_kind_.*_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_namespace
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_(.*)_destination_name_.*_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_kind
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_(.*)_destination_pod_.*_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_name
        - source_labels: [__name__]
          action: replace
          regex: envoy_source_namespace_.*_source_kind_.*_source_name_.*_source_pod_.*_destination_namespace_.*_destination_kind_.*_destination_name_.*_destination_pod_(.*)_osm_request_duration_ms_(bucket|sum|count)
          target_label: destination_pod
        - source_labels: [__name__]
          action: replace
          regex: .*(osm_request_duration_ms_(bucket|sum|count))
          target_label: __name__

      - job_name: 'kubernetes-cadvisor'
        scheme: https
        tls_config:
          ca_file: /var/run/secrets/kubernetes.io/serviceaccount/ca.crt
        bearer_token_file: /var/run/secrets/kubernetes.io/serviceaccount/token
        kubernetes_sd_configs:
        - role: node
        metric_relabel_configs:
        - source_labels: [__name__]
          regex: '(container_cpu_usage_seconds_total|container_memory_rss)'
          action: keep
        relabel_configs:
        - action: labelmap
          regex: __meta_kubernetes_node_label_(.+)
        - target_label: __address__
          replacement: kubernetes.default.svc:443
        - source_labels: [__meta_kubernetes_node_name]
          regex: (.+)
          target_label: __metrics_path__
          replacement: /api/v1/nodes/${1}/proxy/metrics/cadvisor
```

Använd den uppdaterade configmap yaml-filen med följande kommando.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Du kan få ett meddelande om att en Kubernetes-anteckning som saknas krävs. Detta kan ignoreras för tillfället.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Kontrol lera att Prometheus har kon figurer ATS för att kassera OSM-nätet och API-slutpunkter

För att kontrol lera att Prometheus har kon figurer ATS korrekt för att kassera OSM-nät-och API-slutpunkter kommer vi att vidarebefordra till Prometheus-Pod och Visa mål konfigurationen. Kör följande kommandon.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Öppna en webbläsare upp till `http://localhost:9090/targets`

Om du bläddrar nedåt bör du se alla status lägen för SMI-mått och andra OSM- **mått som definieras** som i bilden nedan.

![GRÄNSSNITTs avbildning för OSM Prometheus Target Metrics](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Distribuera och konfigurera en Grafana-instans för OSM

Vi kommer att använda Helm för att distribuera Grafana-instansen. Kör följande kommandon för att installera Grafana via Helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Härnäst ska vi hämta standard lösen ordet för Grafana för att logga in på Grafana-webbplatsen.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Anteckna Grafana-lösenordet.

Härnäst ska vi hämta Grafana-Pod till porten till Grafana-instrumentpanelen för att logga in.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Öppna en webbläsare upp till `http://localhost:3000`

På inloggnings skärmen som visas nedan anger du **admin** som användar namn och använder Grafana-lösenordet som fångats tidigare.

![Användar gränssnitts avbildning för OSM Grafana inloggnings sida](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Konfigurera data källan Grafana Prometheus

När du har loggat in på Grafana är nästa steg att lägga till Prometheus som data källor för Grafana. Det gör du genom att gå till konfigurations ikonen på den vänstra menyn och välja data källor som visas nedan.

![OSM Grafana DataSources sid GRÄNSSNITTs avbildning](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Klicka på knappen **Lägg till data källa** och välj Prometheus under Time Series-databaser.

![OSM Grafana DataSources-gränssnitt avbildning](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

På sidan **Konfigurera din Prometheus-datakälla** anger du KUBERNETES Cluster FQDN för Prometheus-tjänsten för HTTP URL-inställningen. Standard-FQDN ska vara `stable-prometheus-server.default.svc.cluster.local` . När du har angett att Prometheus-tjänstens slut punkt bläddrar du längst ned på sidan och väljer **spara & test**. Du bör få en grön kryss ruta som anger att data källan fungerar.

#### <a name="importing-osm-dashboards"></a>Importera OSM-instrumentpaneler

OSM-instrumentpaneler är tillgängliga både via:

- [Vår lagrings plats](/charts/osm/grafana)och kan importeras som JSON-blobbar via webb administrations portalen
- eller [online på Grafana.com](https://grafana.com/grafana/dashboards/14145)

Om du vill importera en instrument panel söker du efter `+` inloggningen på den vänstra menyn och väljer `import` .
Du kan importera instrument panelen direkt efter deras ID `Grafana.com` . Vår `OSM Mesh Details` instrument panel använder exempelvis ID `14145` , du kan använda ID: t direkt i formuläret och välja `import` :

![OSM Grafana Dashboard import Page UI image](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

När du väljer Importera så kommer du automatiskt till den importerade instrument panelen.

![OSM Grafana Dashboard netinformation Page UI-avbildning](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Distribuera och konfigurera en Jaeger-operatör på Kubernetes för OSM

[Jaeger](https://www.jaegertracing.io/) är ett spårnings system med öppen källkod som används för att övervaka och felsöka distribuerade system. Den kan distribueras med OSM som en ny instans, eller så kan du ta med din egen instans. Följande anvisningar distribuerar en ny instans av Jaeger till `jaeger` namn området i AKS-klustret.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Distribuera Jaeger till AKS-klustret

Använd följande manifest för att installera Jaeger:

```azurecli-interactive
kubectl apply -f - <<EOF
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  replicas: 1
  selector:
    matchLabels:
      app: jaeger
  template:
    metadata:
      labels:
        app: jaeger
    spec:
      containers:
        - name: jaeger
          image: jaegertracing/all-in-one
          args:
          - --collector.zipkin.host-port=9411
          imagePullPolicy: IfNotPresent
          ports:
          - containerPort: 9411
          resources:
            limits:
              cpu: 500m
              memory: 512M
            requests:
              cpu: 100m
              memory: 256M
---
kind: Service
apiVersion: v1
metadata:
  name: jaeger
  namespace: jaeger
  labels:
    app: jaeger
spec:
  selector:
    app: jaeger
  ports:
  - protocol: TCP
    # Service port and target port are the same
    port: 9411
  type: ClusterIP
EOF
```

```Output
deployment.apps/jaeger created
service/jaeger created
```

#### <a name="enable-tracing-for-the-osm-add-on"></a>Aktivera spårning för OSM-tillägget

Därefter måste vi aktivera spårning för OSM-tillägget.

> [!NOTE]
> Nu synlig inte spårnings egenskaperna i OSM-config-configmap för tillfället. Detta kommer att göras synlig i en ny version av OSM AKS-tillägget.

Kör följande kommando för att aktivera spårning för OSM-tillägget:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Visa Jaeger-ANVÄNDARGRÄNSSNITTET med port vidarebefordring

Jaeger användar gränssnitt körs på port 16686. Om du vill visa webb gränssnittet kan du använda kubectl Port-Forward:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

I webbläsaren bör du se en listruta för tjänster som gör att du kan välja bland de olika program som distribueras i försäljnings demon för böcker. Välj en tjänst om du vill visa alla sträcker från den. Om du till exempel väljer bookbuyer med en lookback på en timme kan du se dess interaktioner med bok handel – v1 och bok handel – v2 sorterade efter tid.

![Avbildning av GRÄNSSNITTs sidan för OSM Jaeger](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Markera ett objekt om du vill visa det mer detaljerat. Välj flera objekt för att jämföra spår. Du kan till exempel jämföra bookbuyer-interaktioner med bok slutet och bok slutet – v2 vid en viss tidpunkt.

Du kan också välja fliken System arkitektur om du vill visa ett diagram över hur de olika programmen har varit interagerande/kommunicerar. Detta ger en uppfattning om hur trafiken flödar mellan programmen.

![Avbildning av OSM Jaeger system Architecture UI](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Open Service nät (OSM) AKS-tillägg fel söknings guider

När du distribuerar OSM AKS-tillägget kan det ibland uppstå problem. Följande guider hjälper dig att felsöka fel och lösa vanliga problem.

### <a name="verifying-and-troubleshooting-osm-components"></a>Verifiera och felsöka OSM-komponenter

#### <a name="check-osm-controller-deployment"></a>Kontrol lera distribution av OSM-styrenhet

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

En felfria OSM-styrenhet skulle se ut så här:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Kontrol lera OSM Controller-Pod

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

En felfri OSM-pod skulle se ut så här:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Även om vi hade en kontrollant avlägsnad någon gång har vi en annan som är redo 1/1 och som körs med 0 omstarter. Om kolumnen är klar något annat än 1/1 skulle service nätet vara i ett trasigt tillstånd.
Kolumnen är klar med 0/1 anger att kontroll Plans behållaren kraschar – vi behöver hämta loggar. Se avsnittet Hämta OSM Controller-loggar från Azure Support Center nedan. Kolumnen är klar med ett tal som är högre än 1 efter/anger att det finns sidvagn installerat. OSM Controller skulle förmodligen inte fungera med alla sidvagn som är kopplade till den.

> [!NOTE]
> Från och med version v 0.8.2 är OSM-kontrollanten inte i HA-läge och kommer att köras i ett distribuerat med replik antalet på 1 – en pod. Pod har hälso avsökningar och kommer att startas om av kubelet vid behov.

#### <a name="check-osm-controller-service"></a>Kontrol lera OSM Controller-tjänsten

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

En felfri OSM Controller-tjänst ser ut så här:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> KLUSTRETs IP-adress skulle vara annorlunda. Tjänstens namn och PORT (er) måste vara samma som exemplet ovan.

#### <a name="check-osm-controller-endpoints"></a>Kontrol lera OSM-styrenhetens slut punkter

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

En felfria OSM (a) slut punkt (er) ser ut så här:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Kontrol lera OSM-inmatnings distribution

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

En felfri OSM-distribution skulle se ut så här:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Kontrol lera OSM Injektionr Pod

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

En felfri OSM-pod skulle se ut så här:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Kontrol lera OSM-inmatnings tjänsten

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

En felfri OSM-inmatnings tjänst skulle se ut så här:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Kontrol lera OSM-slutpunkter

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

En felfria OSM-slutpunkt skulle se ut så här:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Kontrol lera verifiering och mutation av Webhooks

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

En felfri OSM som verifierar att webhooken ser ut så här:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

En felfri OSM-webhook skulle se ut så här:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Sök efter tjänsten och CA-paketet för den verifierande webhooken

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

En välkonfigurerad validering av webhook-konfigurationen ser exakt ut så här:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Sök efter tjänsten och CA-paketet för den Mutations-webhooken

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

En välkonfigurerad konfiguration av webhook-konfigurationen ser exakt ut så här:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Kontrol lera om OSM-kontrollanten har fått en certifikat utfärdare för att verifiera (eller mutation) webhook

> [!NOTE]
> Från och med v 0.8.2 är det viktigt att veta att AKS RP installerar verifierings-webhook, AKS reconcile garanterar att den finns, men OSM Controller är den som fyller CA-paketet.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Det här talet anger antalet byte, eller storleken på CA-paketet. Om detta är tomt, 0 eller ett tal under 1000 skulle det tyda på att CA-paketet inte är korrekt allokerat. Om du inte har rätt CA-paket kan verifieringen av webhooken bli fel och hindra användaren från att göra ändringar i OSM-config-ConfigMap i namn området Kube-system.

Ett exempel fel när CA-paketet är felaktigt:

- Ett försök att ändra OSM-config-ConfigMap:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Fel:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Undvik att använda när den **verifierande** webhook-konfigurationen har ett felaktigt certifikat:

- Alternativ 1 – Starta om OSM-kontrollanten. då startas om OSM-styrenheten. Vid start kommer den att skriva över CA-paketet för både Mutations-och validerings-webhookar.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Alternativ 2 – alternativ 2. Ta bort valideringen av webhooken – om du tar bort verifierings-webhooken tas `osm-config` ConfigMap inte längre att verifieras. Alla korrigeringar går igenom. AKS-avstämningen kommer att se till att verifiera webhook finns och kommer att återskapa den. OSM-styrenheten kanske måste startas om för att snabbt skriva om CA-paketet.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Alternativ 3 – borttagning och korrigering: följande kommando tar bort verifierings-webhooken, så att vi kan lägga till värden och omedelbart försöka tillämpa en korrigering. Troligen har AKS reconcile inte tillräckligt med tid för att stämma av och återställa en verifierad webhook som ger oss möjlighet att tillämpa en ändring som en sista utväg:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Kontrol lera `osm-config` **ConfigMap**

> [!NOTE]
> OSM-kontrollanten kräver inte att `osm-config` ConfigMap finns i Kube-systemets namnrymd. Kontrollanten har rimliga standardvärden för config och kan användas utan det.

Sök efter förekomsten:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Kontrol lera innehållet i OSM-config-ConfigMap

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config -o json | jq '.data'
```

```json
{
  "egress": "true",
  "enable_debug_server": "true",
  "enable_privileged_init_container": "false",
  "envoy_log_level": "error",
  "outbound_ip_range_exclusion_list": "169.254.169.254,168.63.129.16,20.193.20.233",
  "permissive_traffic_policy_mode": "true",
  "prometheus_scraping": "false",
  "service_cert_validity_duration": "24h",
  "use_https_ingress": "false"
}
```

`osm-config` ConfigMap-värden:

| Nyckel                              | Typ   | Tillåtna värden                                          | Standardvärde                          | Funktion                                                                                                                                                                                                                                |
| -------------------------------- | ------ | ------------------------------------------------------- | -------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| utgående                           | boolesk   | SANT, FALSKT                                             | `"false"`                              | Aktiverar utgående trafik i nätet.                                                                                                                                                                                                             |
| enable_debug_server              | boolesk   | SANT, FALSKT                                             | `"true"`                               | Aktiverar en fel söknings slut punkt på OSM-Pod för att visa information om nätet, till exempel proxy-anslutningar, certifikat och SMI-principer.                                                                                    |
| enable_privileged_init_container | boolesk   | SANT, FALSKT                                             | `"false"`                              | Aktiverar privilegierade init-behållare för poddar i nät. När värdet är false har init-behållare bara NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | sträng | spåra, felsöka, info, varning, Varna, fel, kritiskt, av | `"error"`                              | Ställer in utförlig loggning av mottagare-proxy sidvagn, som endast gäller nyligen skapade poddar som ansluter till nätet. Om du vill uppdatera logg nivån för befintliga poddar startar du om distributionen med `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | sträng | kommaavgränsad lista med IP-intervall i formatet a. b. c. d/x | `-`                                    | Global lista över IP-adressintervall som ska undantas från avgående trafik avlyssning av den sidvagn-proxyn.                                                                                                                                    |
| permissive_traffic_policy_mode   | boolesk   | SANT, FALSKT                                             | `"false"`                              | Inställningen till `true` , aktiverar läget Tillåt-alla i nätet, d.v.s. ingen trafik princip tillämpning i nätet. Om detta är inställt på `false` , aktiverar neka-all trafik princip i nät, d.v.s. en `SMI Traffic Target` krävs för att tjänsterna ska kunna kommunicera. |
| prometheus_scraping              | boolesk   | SANT, FALSKT                                             | `"true"`                               | Gör det möjligt att Prometheus mått på sidvagn-proxyservrar.                                                                                                                                                                                 |
| service_cert_validity_duration   | sträng | 24 timmarna, 1h30m (alla tids perioder)                          | `"24h"`                                | Anger giltighets tiden för tjänst certifikatet, som representeras som en sekvens med decimal tal var och en med valfria bråk och enhets suffix.                                                                                             |
| tracing_enable                   | boolesk   | SANT, FALSKT                                             | `"false"`                              | Aktiverar Jaeger-spårning för nätet.                                                                                                                                                                                                    |
| tracing_address                  | sträng | Jaeger. mesh-namespace. svc. Cluster. local                 | `jaeger.kube-system.svc.cluster.local` | Adressen för Jaeger-distributionen, om spårning är aktiverat.                                                                                                                                                                                |
| tracing_endpoint                 | sträng | /api/v2/spans                                           | /api/v2/spans                          | Slut punkt för spårnings data, om spårning aktive rad.                                                                                                                                                                                          |
| tracing_port                     | int    | ett heltal som inte är noll                              | `"9411"`                               | Den port där spårning har Aktiver ATS.                                                                                                                                                                                                       |
| use_https_ingress                | boolesk   | SANT, FALSKT                                             | `"false"`                              | Aktiverar HTTPS-ingångar på nätet.                                                                                                                                                                                                      |
| config_resync_interval           | sträng | under en minut inaktive ras detta                            | 0 (inaktive rad)                           | När ett värde över 1 miljon (60 s) anges skickar OSM Controller all tillgänglig konfiguration till varje ansluten mottagare med det angivna intervallet                                                                                                    |

#### <a name="check-namespaces"></a>Kontrol lera namn områden

> [!NOTE]
> Namn området Kube deltar aldrig i ett tjänst nät och märks aldrig och/eller kommenteras med nyckel/värden nedan.

Vi använder `osm namespace add` kommandot för att ansluta namn områden till ett specifikt service nät.
När ett K8s-namnområde är en del av nätet (eller för att det ska vara en del av nätet) måste följande vara sant:

Visa anteckningarna med

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.annotations'
```

Följande anteckning måste finnas:

```Output
{
  "openservicemesh.io/sidecar-injection": "enabled"
}
```

Visa etiketterna med

```azurecli-interactive
kubectl get namespace bookbuyer -o json | jq '.metadata.labels'
```

Följande etikett måste finnas:

```Output
{
  "openservicemesh.io/monitored-by": "osm"
}
```

Om ett namn område inte är kommenterat med `"openservicemesh.io/sidecar-injection": "enabled"` eller inte är märkt med `"openservicemesh.io/monitored-by": "osm"` OSM-inmatningen kommer inte att lägga till mottagare-sidvagn.

> Obs: efter `osm namespace add` kallas att endast **nya** poddar matas in med en mottagare-sidvagn. Befintliga poddar måste startas om `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Verifiera SMI-CRDs:

Kontrol lera om klustret har de nödvändiga CRDs:

```azurecli-interactive
kubectl get crds
```

Vi måste ha följande installerat i klustret:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Hämta de versioner av CRDs som installeras med det här kommandot:

```azurecli-interactive
for x in $(kubectl get crds --no-headers | awk '{print $1}' | grep 'smi-spec.io'); do
    kubectl get crd $x -o json | jq -r '(.metadata.name, "----" , .spec.versions[].name, "\n")'
done
```

Förväntad utdata:

```Output
httproutegroups.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


tcproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1


trafficsplits.split.smi-spec.io
----
v1alpha2


traffictargets.access.smi-spec.io
----
v1alpha3
v1alpha2
v1alpha1


udproutes.specs.smi-spec.io
----
v1alpha4
v1alpha3
v1alpha2
v1alpha1
```

OSM Controller v 0.8.2 kräver följande versioner:

- traffictargets.access.smi-spec.io – [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io – stöds inte
- trafficsplits.split.smi-spec.io – [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*. metrics.smi-spec.io- [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Om CRDs saknas använder du följande kommandon för att installera dessa på klustret:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Inaktivera OSM-tillägg (Open Service nät) för ditt AKS-kluster

Om du vill inaktivera OSM-tillägget kör du följande kommando:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register