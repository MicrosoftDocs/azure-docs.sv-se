---
title: Öppna Service Mesh (förhandsversion)
description: Öppna Service Mesh (OSM) i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 3/12/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: b0a37400d02ff324e7191bb0b04243d7f16090bd
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877164"
---
# <a name="open-service-mesh-aks-add-on-preview"></a>Open Service Mesh AKS-tillägg (förhandsversion)

## <a name="overview"></a>Översikt

[Open Service Mesh (OSM)](https://docs.openservicemesh.io/) är ett enkelt, utökningsbart molninbyggt tjänstnät som gör att användarna kan hantera, skydda och få inbyggda observerbarhetsfunktioner för mycket dynamiska mikrotjänstmiljöer.

OSM kör ett Envoy-baserat kontrollplan på Kubernetes, kan konfigureras med [SMI-API:er](https://smi-spec.io/) och fungerar genom att mata in en Envoy-proxy som en sidovagnscontainer bredvid varje instans av ditt program. Envoy-proxyn innehåller och kör regler kring principer för åtkomstkontroll, implementerar routningskonfiguration och samlar in mått. Kontrollplanet konfigurerar kontinuerligt proxys för att säkerställa att principer och routningsregler är uppdaterade och ser till att proxys är felfria.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="capabilities-and-features"></a>Funktioner

OSM innehåller följande funktioner och funktioner för att tillhandahålla ett moln inbyggt tjänstnät för dina Azure Kubernetes Service-kluster (AKS):

- Säker tjänst-till-tjänst-kommunikation genom att aktivera mTLS

- Publicera enkelt program i nätet genom att aktivera automatisk sidovagnsinjektion av Envoy-proxyn

- Enkla och transparenta konfigurationer för trafikväxling i distributioner

- Möjlighet att definiera och köra finkorniga principer för åtkomstkontroll för tjänster

- Observerbarhet och insikter om programmått för felsöknings- och övervakningstjänster

- Integrering med externa certifikathanteringstjänster/-lösningar med ett anslutbart gränssnitt

## <a name="scenarios"></a>Scenarier

OSM kan hjälpa dina AKS-distributioner med följande scenarier:

- Tillhandahålla krypterad kommunikation mellan tjänstslutpunkter som distribueras i klustret

- Trafikauktorisering för både HTTP/HTTPS- och TCP-trafik i nät

- Konfiguration av viktade trafikkontroller mellan två eller flera tjänster för A/B- eller kanariedistributioner

- Insamling och visning av KPI:er från programtrafik

## <a name="osm-service-quotas-and-limits-preview"></a>OSM-tjänstkvoter och -gränser (förhandsversion)

Begränsningar i förhandsversionen av OSM för tjänstkvoter och -gränser finns på sidan om [AKS-kvoter och regionala gränser.](https://docs.microsoft.com/azure/aks/quotas-skus-regions)

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
> Försök inte att installera OSM från binärfil med `osm install` . Detta resulterar i en installation av OSM som inte är integrerad som ett tillägg för AKS.

### <a name="register-the-aks-openservicemesh-preview-feature"></a>Registrera `AKS-OpenServiceMesh` förhandsgranskningsfunktionen

Om du vill skapa ett AKS-kluster som kan använda Open Service Mesh-tillägget måste du aktivera `AKS-OpenServiceMesh` funktionsflaggan för din prenumeration.

Registrera `AKS-OpenServiceMesh` funktionsflaggan med [kommandot az feature register,][az-feature-register] som du ser i följande exempel:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-OpenServiceMesh"
```

Det tar några minuter för statusen att visa _Registrerad._ Kontrollera registreringsstatusen med kommandot [az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-OpenServiceMesh')].{Name:name,State:properties.state}"
```

När du är klar uppdaterar du registreringen av _resursprovidern Microsoft.ContainerService_ med kommandot [az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="install-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-a-new-aks-cluster"></a>Installera tillägget Open Service Mesh (OSM) Azure Kubernetes Service (AKS) för ett nytt AKS-kluster

För ett nytt scenario för distribution av AKS-kluster börjar du med en helt ny distribution av ett AKS-kluster som aktiverar OSM-tillägget vid åtgärden för att skapa kluster.

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

I Azure allokerar du relaterade resurser till en resursgrupp. Skapa en resursgrupp med hjälp av [az group create](/cli/azure/group#az_group_create). I följande exempel skapas en resursgrupp med _namnet myOsmAksGroup_ på _platsen eastus2_ (region):

```azurecli-interactive
az group create --name <myosmaksgroup> --location <eastus2>
```

### <a name="deploy-an-aks-cluster-with-the-osm-add-on-enabled"></a>Distribuera ett AKS-kluster med OSM-tillägget aktiverat

Nu ska du distribuera ett nytt AKS-kluster med OSM-tillägget aktiverat.

> [!NOTE]
> Tänk på att följande AKS-distributionskommando använder os-tillfälliga diskar. Du hittar mer information här om [tillfälliga OS-diskar för AKS](https://docs.microsoft.com/azure/aks/cluster-configuration#ephemeral-os)

```azurecli-interactive
az aks create -n osm-addon-cluster -g <myosmaksgroup> --kubernetes-version 1.19.6 --node-osdisk-type Ephemeral --node-osdisk-size 30 --network-plugin azure --enable-managed-identity -a open-service-mesh
```

#### <a name="get-aks-cluster-access-credentials"></a>Hämta autentiseringsuppgifter för AKS-klusteråtkomst

Hämta autentiseringsuppgifter för det nya hanterade Kubernetes-klustret.

```azurecli-interactive
az aks get-credentials -n <myosmakscluster> -g <myosmaksgroup>
```

## <a name="enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster"></a>Aktivera AKS-tillägg (Open Service Mesh) Azure Kubernetes Service (OSM) för ett befintligt AKS-kluster

I ett befintligt AKS-klusterscenario aktiverar du OSM-tillägget till ett befintligt AKS-kluster som redan har distribuerats.

### <a name="enable-the-osm-add-on-to-existing-aks-cluster"></a>Aktivera OSM-tillägget till ett befintligt AKS-kluster

Om du vill aktivera AKS OSM-tillägget måste du köra kommandot `az aks enable-addons --addons` som anger parametern `open-service-mesh`

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

Det finns flera kommandon att köra för att kontrollera att alla komponenter i AKS OSM-tillägget är aktiverade och körs:

Först kan vi köra frågor mot klustrets tilläggsprofiler för att kontrollera det aktiverade tillståndet för de installerade tilläggen. Följande kommando ska returnera "true".

```azurecli-interactive
az aks list -g <resource group name> -o json | jq -r '.[].addonProfiles.openServiceMesh.enabled'
```

Följande kommandon `kubectl` rapporterar status för osm-controller.

```azurecli-interactive
kubectl get deployments -n kube-system --selector app=osm-controller
kubectl get pods -n kube-system --selector app=osm-controller
kubectl get services -n kube-system --selector app=osm-controller
```

## <a name="accessing-the-aks-osm-add-on"></a>Åtkomst till AKS OSM-tillägget

För närvarande kan du komma åt och konfigurera OSM-kontrollantkonfigurationen via konfigurationskartan. Om du vill visa konfigurationsinställningarna för OSM-styrenheten frågar du osm-configmap via `kubectl` för att visa dess konfigurationsinställningar.

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Utdata från OSM-konfigurationskartan bör se ut så här:

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

Observera att **permissive_traffic_policy_mode** har konfigurerats till **sant**. Tillåtet trafikprincipläge i OSM är ett läge där [tvingande SMI-trafikprincip](https://smi-spec.io/) kringgås. I det här läget identifierar OSM automatiskt tjänster som är en del av tjänstnätet och program trafikprincipregler på varje Envoy-proxy sidovagn för att kunna kommunicera med dessa tjänster.

> [!WARNING]
> Innan du fortsätter bör du kontrollera att principläget för tillåtande trafik är inställt på sant. Om du inte gör det ändrar du det till **sant** med hjälp av kommandot nedan

```OSM Permissive Mode to True
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"true"}}'
```

## <a name="deploy-a-new-application-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Distribuera ett nytt program som ska hanteras av tillägget Open Service Mesh (OSM) Azure Kubernetes Service (AKS)

### <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här genomgången förutsätter att du har skapat ett AKS-kluster (Kubernetes och högre, med Kubernetes RBAC aktiverat), har upprättat en anslutning till klustret (Om du behöver hjälp med något av dessa objekt kan du gå till `1.19+` `kubectl` [AKS-snabbstarten](./kubernetes-walkthrough.md)och ha installerat AKS OSM-tillägget.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.20.0 eller senare
- Tilläggsversion `aks-preview` 0.5.5 eller senare
- OSM version v0.8.0 eller senare
- apt-get install jq

### <a name="create-namespaces-for-the-application"></a>Skapa namnområden för programmet

I den här genomgången använder vi OSM-bokhandelsprogrammet som har följande Kubernetes-tjänster:

- bookbuyer
- bookthief
- Bokhandel
- bookwarehouse

Skapa namnområden för var och en av dessa programkomponenter.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Publicera de namnområden som ska hanteras av OSM

När du lägger till namnrymderna i OSM-nät, gör detta att OSM-kontrollanten automatiskt kan mata in Envoy-containern för sidovagnsproxy med ditt program. Kör följande kommando för att publicera OSM-bokhandelsprogrammets namnrymder.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuera bokhandelsprogrammet till AKS-klustret

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

Alla distributionsutdata sammanfattas nedan.

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

### <a name="checkpoint-what-got-installed"></a>Kontrollpunkt: Vad har installerats?

Exempelprogrammet Bookstore är en app med flera nivåer som består av fyra tjänster, där det är bookbuyer, bookthief, bookstore och bookwarehouse. Både bookbuyer- och bookthief-tjänsten kommunicerar med bokhandelstjänsten för att hämta böcker från bokhandelstjänsten. Bokhandelstjänsten hämtar böcker från tjänsten bookwarehouse för att tillhandahålla bookbuyer och bookthief. Det här är ett enkelt flernivåprogram som fungerar bra för att visa hur ett tjänstnät kan användas för att skydda och auktorisera kommunikation mellan programtjänsterna. När vi fortsätter genom genomgången kommer vi att aktivera och inaktivera SMI-principer (Service Mesh Interface) för att både tillåta och inte tillåta att tjänsterna kommunicerar via OSM. Nedan visas ett arkitekturdiagram över vad som har installerats för bokhandelsprogrammet.

![ARKITEKTUR för OSM-bokköparapp](./media/aks-osm-addon/osm-bookstore-app-arch.png)

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verifiera bokhandelsprogrammet som körs i AKS-klustret

Från och med nu har vi distribuerat bokhandelsprogrammet multiit-container, men det är bara tillgängligt från AKS-klustret. Senare självstudier hjälper dig att exponera programmet utanför klustret via en ingress-kontrollant. För tillfället kommer vi att använda port vidarebefordran för att få åtkomst till bookbuyer-programmet i AKS-klustret för att verifiera att det köper böcker från bokhandelstjänsten.

För att kontrollera att programmet körs i klustret använder vi en port framåt för att visa användargränssnittet för både bookbuyer- och bookthief-komponenterna.

Först hämtar vi bookbuyer-poddens namn

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Du bör se utdata som liknar följande. Din bookbuyer-podd har ett unikt namn tillagt.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

När vi har poddens namn kan vi nu använda kommandot port-forward för att konfigurera en tunnel från vårt lokala system till programmet i AKS-klustret. Kör följande kommando för att konfigurera port framåt för den lokala systemporten 8080. Använd återigen det angivna poddnamnet bookbuyer.

> [!NOTE]
> För alla kommandon för vidarebefordran av portar är det bäst att använda ytterligare en terminal så att du kan fortsätta att gå igenom den här genomgången och inte koppla från tunneln. Det är också bäst att du etablerar tunneln för port framåt utanför Azure Cloud Shell.

```Bash
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Du bör se utdata som liknar detta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

När sessionen för vidarebefordran av portar är på plats navigerar du till följande URL från en webbläsare `http://localhost:8080` . Du bör nu kunna se användargränssnittet för bookbuyer-programmet i webbläsaren, ungefär som på bilden nedan.

![Avbildning av ANVÄNDArgränssnittet för OSM-bookbuyer-appen](./media/aks-osm-addon/osm-bookbuyer-service-ui.png)

Du kommer också att märka att det totala antalet böcker som köpts fortsätter att öka till tjänsten bookstore v1. Tjänsten bookstore v2 har inte distribuerats ännu. Vi distribuerar bokhandelstjänsten v2 när vi demonstrerar SMI-trafikdelningsprinciperna.

Du kan också kontrollera samma sak för bookthief-tjänsten.

```azurecli-interactive
kubectl get pod -n bookthief
```

Du bör se utdata som liknar följande. Din bookthief-podd har ett unikt namn tillagt.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookthief-59549fb69c-cr8vl   2/2     Running   0          15m54s
```

Port framåt till bookthief pod.

```Bash
kubectl port-forward bookthief-59549fb69c-cr8vl -n bookthief 8080:14001
```

Gå till följande URL från en webbläsare `http://localhost:8080` . Du bör se att bookthief just nu stjäl böcker från bokhandelstjänsten! Senare implementerar vi en trafikprincip för att stoppa bookthief.

![Bild av ANVÄNDArgränssnittet för OSM-bookthief-appen](./media/aks-osm-addon/osm-bookthief-service-ui.png)

### <a name="disable-osm-permissive-traffic-mode-for-the-mesh"></a>Inaktivera OSM-tillåtet trafikläge för nät

Som tidigare nämnts när du visar OSM-klusterkonfigurationen använder OSM-konfigurationen som standard principen för tillåtet trafikläge. I det här läget kringgås trafikprincipens tillämpning och OSM identifierar automatiskt tjänster som är en del av tjänstnätet och programtrafikprincipregler på varje Envoy-proxysidavagn för att kunna kommunicera med dessa tjänster.

Vi inaktiverar nu principen för tillåtet trafikläge och OSM behöver explicita [SMI-principer](https://smi-spec.io/) distribuerade till klustret för att tillåta kommunikation i nät från varje tjänst. Om du vill inaktivera tillåtet trafikläge kör du följande kommando för att uppdatera configmap-egenskapen och ändra värdet `true` från till `false` .

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"permissive_traffic_policy_mode":"false"}}'
```

Du bör se utdata som liknar följande. Din bookthief-podd har ett unikt namn tillagt.

```Output
configmap/osm-config patched
```

För att kontrollera att tillåtet trafikläge har inaktiverats, vidarebefordrar du porten tillbaka till antingen bookbuyer- eller bookthief-podden för att visa deras användargränssnitt i webbläsaren och se om böckerna som köpts eller böckerna som stulits inte längre ökar. Se till att uppdatera webbläsaren. Om ökningen har stoppats har principen tillämpats korrekt. Du har stoppat bokbutiken från att stjäla böcker, men varken bokköparen kan köpa från bokbutiken eller bokbutiken kan hämta böcker från bokboken. Därefter implementerar vi [SMI-principer](https://smi-spec.io/) för att endast tillåta de tjänster i nät som du vill kommunicera för att göra det.

### <a name="apply-service-mesh-interface-smi-traffic-access-policies"></a>Tillämpa SMI-trafikåtkomstprinciper (Service Mesh Interface)

Nu när vi har inaktiverat all kommunikation i nätet låter vi vår bokköpstjänst kommunicera med vår bokhandelstjänst för att köpa böcker och låta vår bokhandelstjänst kommunicera med vår boklagertjänst för att hämta böcker för att sälja.

Distribuera följande [SMI-principer.](https://smi-spec.io/)

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

Nu kan du konfigurera en session för vidarebefordran av portar på antingen bokköparen eller bokhandelspoddarna och se att måtten för både böcker som köpts och böcker som sålts ökar. Du kan också göra samma sak för bookthief-podden för att verifiera att den fortfarande inte längre kan stjäla böcker.

### <a name="apply-service-mesh-interface-smi-traffic-split-policies"></a>Tillämpa SMI-trafikdelningsprinciper (Service Mesh Interface)

I den slutliga demonstrationen [](https://smi-spec.io/) skapar vi en SMI-trafikuppdelningsprincip för att konfigurera kommunikationsvikten från en tjänst till flera tjänster som en backend. Med funktionen för trafikdelning kan du progressivt flytta anslutningar till en tjänst över till en annan genom att vikta trafiken på en skala från 0 till 100.

Bilden nedan är ett [](https://smi-spec.io/) diagram över SMI-trafikdelningsprincipen som ska distribueras. Vi distribuerar ytterligare en bookstore version 2 och delar sedan upp den inkommande trafiken från bookbuyer, vilket viktar 25 % av trafiken till bookstore v1-tjänsten och 75 % till bookstore v2-tjänsten.

![DIAGRAM över OSM-bokdelning av trafik](./media/aks-osm-addon/osm-bookbuyer-traffic-split-diagram.png)

Distribuera tjänsten bookstore v2.

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

Distribuera nu trafikdelningsprincipen för att dela upp bookbuyer-trafiken mellan de två bokhandels-v1- och v2-tjänsten.

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

Konfigurera en port framåt-tunnel till bookbuyer-podden. Nu bör du se böcker som köps från bookstore v2-tjänsten. Om du fortsätter att titta på ökningen av köp bör du se en snabbare ökning av inköp som görs via bokbutik v2-tjänsten.

![OSM bookbuyer books boough UI](./media/aks-osm-addon/osm-bookbuyer-traffic-split-ui.png)

## <a name="manage-existing-deployed-applications-to-be-managed-by-the-open-service-mesh-osm-azure-kubernetes-service-aks-add-on"></a>Hantera befintliga distribuerade program som ska hanteras av AKS-tillägget (Open Service Mesh) Azure Kubernetes Service (OSM)

### <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här genomgången förutsätter att du tidigare har aktiverat OSM AKS-tillägget för ditt AKS-kluster. Om inte, läs avsnittet [Aktivera AKS-tillägg (Open Service Mesh Azure Kubernetes Service) (OSM)](#enable-open-service-mesh-osm-azure-kubernetes-service-aks-add-on-for-an-existing-aks-cluster) för ett befintligt AKS-kluster innan du fortsätter. Dessutom måste AKS-klustret vara version Kubernetes och senare, ha Kubernetes RBAC aktiverat och ha upprättat en anslutning till klustret (Om du behöver hjälp med något av dessa objekt kan du se `1.19+` `kubectl` [AKS-snabbstarten](./kubernetes-walkthrough.md)och ha installerat AKS OSM-tillägget.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.20.0 eller senare
- Tilläggsversionen `aks-preview` 0.5.5 eller senare
- OSM version v0.8.0 eller senare
- apt-get install jq

### <a name="verify-the-open-service-mesh-osm-permissive-traffic-mode-policy"></a>Verifiera principen För tillåtet trafikläge i Open Service Mesh (OSM)

Läget för OSM-tillåtande trafikprincip är ett läge där [tvingande SMI-trafikprincip](https://smi-spec.io/) kringgås. I det här läget identifierar OSM automatiskt tjänster som är en del av tjänstnätet och program trafikprincipregler på varje Envoy-proxy sidovagn för att kunna kommunicera med dessa tjänster.

Kontrollera det aktuella tillåtande trafikläget för OSM för klustret genom att köra följande kommando:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data'
```

Utdata från OSM-konfigurationskartan bör se ut så här:

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

Om **permissive_traffic_policy_mode** har konfigurerats till **sant** kan du på ett säkert sätt registrera dina namnområden utan avbrott i din tjänst-till-tjänst-kommunikation. Om **permissive_traffic_policy_mode** har konfigurerats till **falskt** måste du se till att du har rätt distribuerade principmanifest för [SMI-trafik](https://smi-spec.io/) samt att du har ett tjänstkonto som representerar varje tjänst som distribueras i namnområdet. Följ riktlinjerna för publicera [befintliga distribuerade program med Open Service Mesh (OSM) tillåtande trafikprincip konfigurerad som falskt](#onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false)

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-true"></a>Registrera befintliga distribuerade program med Open Service Mesh (OSM) Tillåtande trafikprincip konfigurerad som True

Det första vi ska göra är att lägga till de distribuerade programnamnrymderna i OSM för hantering.

```azurecli-interactive
osm namespace add bookstore
```

Du bör se följande utdata:

```Output
Namespace [bookstore] successfully added to mesh [osm]
```

Nu ska vi ta en titt på den aktuella podddistributionen i namnområdet . Kör följande kommando för att visa poddarna i det avsedda namnområdet.

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Du bör se följande utdata:

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-78666dcff8-wh6wl   1/1     Running   0          43s
```

Observera att **kolumnen READY** visar **1/1,** vilket innebär att programpodden bara har en container. Därefter måste vi starta om programdistributionerna så att OSM kan mata in Envoy-containern för sidovagnsproxy med programpodden. Nu ska vi hämta en lista över distributioner i namnområdet .

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

Du bör se följande utdata:

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           23h
```

Nu startar vi om distributionen för att mata in Envoy-proxycontainern med programpodden. Kör följande kommando.

```azurecli-interactive
kubectl rollout restart deployment bookbuyer -n bookbuyer
```

Du bör se följande utdata:

```Output
deployment.apps/bookbuyer restarted
```

Om vi tittar på poddarna i namnområdet igen:

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Nu ser du att kolumnen **READY** nu visar **2/2 containrar som** är redo för din podd. Den andra containern är Envoy-sidovagnsproxyn.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-84446dd5bd-j4tlr   2/2     Running   0          3m30s
```

Vi kan granska podden ytterligare för att visa Envoy-proxyn genom att köra kommandot describe för att visa konfigurationen.

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

Kontrollera att programmet fortfarande fungerar efter injectionen av Envoy-sidovagnsproxyn.

### <a name="onboard-existing-deployed-applications-with-open-service-mesh-osm-permissive-traffic-policy-configured-as-false"></a>Publicera befintliga distribuerade program med Open Service Mesh (OSM) Tillåtande trafikprincip konfigurerad som Falskt

När OSM-konfigurationen för principen för tillåtande trafik är inställd på kräver OSM explicita SMI-trafikåtkomstprinciper som distribueras för att `false` tjänst-till-tjänst-kommunikationen ska ske i klustret. [](https://smi-spec.io/) OSM använder för närvarande även Kubernetes-tjänstkonton som en del av auktorisering av tjänst-till-tjänst-kommunikation. För att säkerställa att dina befintliga distribuerade program kommunicerar när de hanteras av OSM-nät måste vi verifiera att det [](https://smi-spec.io/) finns ett tjänstkonto att använda, uppdatera programdistributionen med tjänstkontoinformationen och tillämpa SMI-trafikåtkomstprinciperna.

#### <a name="verify-kubernetes-service-accounts"></a>Verifiera Kubernetes-tjänstkonton

Kontrollera om du har ett kubernetes-tjänstkonto i det namnområde som programmet distribueras till.

```azurecli-interactive
kubectl get serviceaccounts -n bookbuyer
```

I följande avsnitt finns ett tjänstkonto med `bookbuyer` namnet i namnområdet bookbuyer.

```Output
NAME        SECRETS   AGE
bookbuyer   1         25h
default     1         25h
```

Om du inte har något annat tjänstkonto än standardkontot måste du skapa ett för ditt program. Använd följande kommando som exempel för att skapa ett tjänstkonto i programmets distribuerade namnområde.

```azurecli-interactive
kubectl create serviceaccount myserviceaccount -n bookbuyer
```

```Output
serviceaccount/myserviceaccount created
```

#### <a name="view-your-applications-current-deployment-specification"></a>Visa programmets aktuella distributionsspecifikation

Om du var tvungen att skapa ett tjänstkonto från det tidigare avsnittet är det risk att programdistributionen inte har konfigurerats med en specifik `serviceAccountName` i distributionsspecifikationen. Vi kan visa programmets distributionsspecifikt med följande kommandon:

```azurecli-interactive
kubectl get deployment -n bookbuyer
```

En lista över distributioner visas i utdata.

```Output
NAME        READY   UP-TO-DATE   AVAILABLE   AGE
bookbuyer   1/1     1            1           25h
```

Nu ska vi beskriva distributionen som en kontroll för att se om det finns ett tjänstkonto i avsnittet Poddmall.

```azurecli-interactive
kubectl describe deployment bookbuyer -n bookbuyer
```

I den här specifika distributionen kan du se att det finns ett tjänstkonto associerat med distributionen som anges under avsnittet Pod template (Poddmall). Den här distributionen använder servicekontots bookbuyer. Om du inte ser egenskapen **Serivce Account:** har distributionen inte konfigurerats för att använda ett tjänstkonto.

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

Det finns flera metoder för att uppdatera distributionen för att lägga till ett Kubernetes-tjänstkonto. Läs Kubernetes-dokumentationen [om att uppdatera en](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment) infogade distribution eller Konfigurera [tjänstkonton för poddar.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) När du har uppdaterat distributionsspecifikationen med tjänstkontot distribuerar du om (kubectl apply -f your-deployment.yaml) distributionen till klustret.

#### <a name="deploy-the-necessary-service-mesh-interface-smi-policies"></a>Distribuera nödvändiga SMI-principer (Service Mesh Interface)

Det sista steget för att tillåta att auktoriserad trafik flödar i nätnätet är att distribuera nödvändiga [SMI-trafikåtkomstprinciper](https://smi-spec.io/) för ditt program. Mängden konfiguration som du [](https://smi-spec.io/) kan uppnå med SMI-trafikåtkomstprinciper ligger utanför omfånget för den här genomgången, men vi beskriver några av de vanliga komponenterna i specifikationen och visar hur du konfigurerar både en enkel TrafficTarget- och HTTPRouteGroup-princip för att aktivera tjänst-till-tjänst-kommunikation för ditt program.

Med specifikationen [**för SMI Access Control trafik**](https://github.com/servicemeshinterface/smi-spec/blob/main/apis/traffic-access/v1alpha3/traffic-access.md#traffic-access-control) kan användarna definiera åtkomstkontrollprincipen för sina program. [](https://smi-spec.io/) Vi fokuserar på **api-resurserna TrafficTarget** **och HTTPRoutGroup.**

TrafficTarget-resursen består av tre huvudsakliga konfigurationsinställningar: mål, regler och källor. Ett exempel på TrafficTarget visas nedan.

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

I ovanstående TrafficTarget-specifikation `destination` anger det tjänstkonto som har konfigurerats för målkälltjänsten. Kom ihåg att tjänstkontot som lades till i distributionen tidigare kommer att användas för att ge åtkomst till distributionen som det är kopplat till. Avsnittet `rules` , i det här exemplet, definierar vilken typ av HTTP-trafik som tillåts via anslutningen. Du kan konfigurera korniga regex-mönster för HTTP-huvuden så att de är specifika för vilken trafik som tillåts via HTTP. Avsnittet `sources` är den tjänst som kommer från kommunikationen. Den här specifikationen läser bookbuyer behöver kommunicera med bokhandel.

HTTPRouteGroup-resursen består av en eller en matris med matchningar av HTTP-huvudinformation och är ett krav för TrafficTarget-specifikationen. I exemplet nedan ser du att HTTPRouteGroup auktoriserar tre HTTP-åtgärder, två GET och en POST.

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

Om du inte är bekant med typen av HTTP-trafik som ditt frontend-program använder till andra nivåer av programmet, eftersom TrafficTarget-specifikationen kräver en regel, kan du skapa motsvarigheten till en tillåt alla regler med hjälp av specifikationen nedan för HTTPRouteGroup.

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

När du har konfigurerat din TrafficTarget- och HTTPRouteGroup-specifikation kan du sätta ihop dem som en YAML och distribuera. Nedan visas exempelkonfigurationen för bokhandel.

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

Besök [SMI-webbplatsen](https://smi-spec.io/) för mer detaljerad information om specifikationen.

### <a name="manage-the-applications-namespace-with-osm"></a>Hantera programmets namnområde med OSM

Nu ska vi konfigurera OSM för att hantera namnområdet och starta om distributionerna för att få Envoy-sidovagnsproxyn inmatad med programmet.

Kör följande kommando för att konfigurera `azure-vote` namnområdet som ska hanteras av min OSM.

```azurecli-interactive
osm namespace add azure-vote
```

```Output
Namespace [azure-vote] successfully added to mesh [osm]
```

Starta sedan om både `azure-vote-front` - `azure-vote-back` och -distributionerna med följande kommandon.

```azurecli-interactive
kubectl rollout restart deployment azure-vote-front -n azure-vote
kubectl rollout restart deployment azure-vote-back -n azure-vote
```

```Output
deployment.apps/azure-vote-front restarted
deployment.apps/azure-vote-back restarted
```

Om vi visar poddarna för namnområdet ser vi READY-stadiet för både och som 2/2, vilket innebär att `azure-vote`  `azure-vote-front` `azure-vote-back` Envoy-sidovagnsproxyn har matats in tillsammans med programmet.

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-with-nginx-ingress"></a>Självstudie: Distribuera ett program som hanteras av Open Service Mesh (OSM) med NGINX-ingress

Open Service Mesh (OSM) är ett enkelt, utökningsbart molninbyggt tjänstnät som gör det möjligt för användare att på ett enhetligt sätt hantera, skydda och få inbyggda observerbarhetsfunktioner för dynamiska mikrotjänstmiljöer.

I de här självstudierna får du:

> [!div class="checklist"]
>
> - Visa den aktuella OSM-klusterkonfigurationen
> - Skapa namnområden för OSM för att hantera distribuerade program i namnrymderna
> - Publicera de namnområden som ska hanteras av OSM
> - Distribuera exempelprogrammet
> - Kontrollera att programmet körs i AKS-klustret
> - Skapa en NGINX-ingresskontrollant som används för programmet
> - Exponera en tjänst via Azure Application Gateway ingress till Internet

### <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes och högre, med Kubernetes RBAC aktiverat), har upprättat en anslutning till klustret (om du behöver hjälp med något av dessa objekt kan du läsa `1.19+` `kubectl` [AKS-snabbstarten](./kubernetes-walkthrough.md)och ha installerat AKS OSM-tillägget.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.20.0 eller senare
- Tilläggsversion `aks-preview` 0.5.5 eller senare
- OSM version v0.8.0 eller senare
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visa och verifiera den aktuella OSM-klusterkonfigurationen

När OSM-tillägget för AKS har aktiverats i AKS-klustret kan du visa de aktuella konfigurationsparametrarna i kubernetes-konfigurationskartan osm-configMap. Kör följande kommando för att visa ConfigMap-egenskaperna:

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

Observera att **permissive_traffic_policy_mode** har konfigurerats till **sant**. Tillåtet trafikprincipläge i OSM är ett läge där [tvingande SMI-trafikprincip](https://smi-spec.io/) kringgås. I det här läget identifierar OSM automatiskt tjänster som är en del av tjänstnätet och program trafikprincipregler på varje Envoy-proxy sidovagn för att kunna kommunicera med dessa tjänster.

### <a name="create-namespaces-for-the-application"></a>Skapa namnområden för programmet

I den här självstudien använder vi OSM-bokhandelsprogrammet som har följande programkomponenter:

- bookbuyer
- bookthief
- Bokhandel
- bookwarehouse

Skapa namnrymder för var och en av dessa programkomponenter.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Publicera de namnområden som ska hanteras av OSM

Genom att lägga till namnrymderna i OSM-nät kan OSM-kontrollanten automatiskt mata in Envoy-proxycontainrarna för sidovagn med ditt program. Kör följande kommando för att publicera OSM-bokhandelsprogrammets namnrymder.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuera bokhandelsprogrammet till AKS-klustret

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

Alla distributionsutdata sammanfattas nedan.

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

### <a name="update-the-bookbuyer-service"></a>Uppdatera Bookbuyer Service

Uppdatera bookbuyer-tjänsten till rätt konfiguration för inkommande portar med följande tjänstmanifest.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Kontrollera att bokhandelsprogrammet körs i AKS-klustret

Från och med nu har vi distribuerat bokhandelsprogrammet multiit-container, men det är bara tillgängligt från AKS-klustret. Senare lägger vi till Azure Application Gateway ingress-kontrollanten för att exponera programmet utanför AKS-klustret. För att kontrollera att programmet körs i klustret använder vi en port framåt för att visa användargränssnittet för bookbuyer-komponenten.

Först hämtar vi bookbuyer-poddens namn

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Du bör se utdata som liknar följande. Din bookbuyer-podd har ett unikt namn tillagt.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

När vi har poddens namn kan vi nu använda kommandot port-forward för att konfigurera en tunnel från vårt lokala system till programmet i AKS-klustret. Kör följande kommando för att konfigurera port framåt för den lokala systemporten 8080. Använd återigen det angivna poddnamnet bookbuyer.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Du bör se utdata som liknar detta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

När sessionen för vidarebefordran av portar är på plats navigerar du till följande URL från en webbläsare `http://localhost:8080` . Du bör nu kunna se användargränssnittet för bookbuyer-programmet i webbläsaren, ungefär som på bilden nedan.

![Avbildning av OSM bookbuyer-appen för NGINX-användargränssnittet](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-nginx-ingress-controller-in-azure-kubernetes-service-aks"></a>Skapa en NGINX-ingresskontrollant i Azure Kubernetes Service (AKS)

En ingress-kontrollant är en del av programvaran som tillhandahåller omvänd proxy, konfigurerbar trafikroutning och TLS-Avslut för Kubernetes-tjänster. Kubernetes ingress-resurser används för att konfigurera inkommande regler och vägar för enskilda Kubernetes-tjänster. Med hjälp av en ingress-kontrollant och ingress-regler kan en IP-adress användas för att dirigera trafik till flera tjänster i ett Kubernetes-kluster.

Vi kommer att använda ingress-kontrollanten för att exponera programmet som hanteras av OSM på Internet. Om du vill skapa ingress-kontrollanten använder du Helm för att installera nginx-ingress. För ytterligare redundans distribueras två repliker av NGINX-ingresskontrollanterna med parametern `--set controller.replicaCount`. Om du vill dra full nytta av att köra repliker av ingress-kontrollanten kontrollerar du att det finns fler än en nod i AKS-klustret.

Ingresskontrollanten måste också schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. En nodväljare anges med parametern `--set nodeSelector` för att instruera Kubernetes-schemaläggaren att köra NGINX-ingresskontrollanten på en Linux-baserad nod.

> [!TIP]
> I följande exempel skapas ett Kubernetes-namnområde för ingressresurserna med _namnet ingress-basic_. Ange ett namnområde för din egen miljö efter behov.

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

När Kubernetes-lastbalanseringstjänsten skapas för NGINX-indatakontrollanten tilldelas en dynamisk offentlig IP-adress, som du ser i följande exempelutdata:

```Output
$ kubectl --namespace ingress-basic get services -o wide -w nginx-ingress-ingress-nginx-controller

NAME                                     TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)                      AGE   SELECTOR
nginx-ingress-ingress-nginx-controller   LoadBalancer   10.0.74.133   EXTERNAL_IP     80:32486/TCP,443:30953/TCP   44s   app.kubernetes.io/component=controller,app.kubernetes.io/instance=nginx-ingress,app.kubernetes.io/name=ingress-nginx
```

Inga inkommande regler har skapats ännu, så NGINX-inkommande kontrollantens standardsida 404 visas om du bläddrar till den interna IP-adressen. Ingress-regler konfigureras i följande steg.

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exponera bookbuyer-tjänsten på Internet

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

Utdata visar NGINX-ingresskontrollantens status när ingressregeln har tillämpats:

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

Eftersom värdnamnet i ingressmanifestet är ett psuedo-namn som används för testning är DNS-namnet inte tillgängligt på Internet. Vi kan också använda curl-programmet och förbi hostname-rubriken till nginx offentliga IP-adressen och få en 200-kod som ansluter oss till bookbuyer-tjänsten.

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

## <a name="tutorial-deploy-an-application-managed-by-open-service-mesh-osm-using-azure-application-gateway-ingress-aks-add-on"></a>Självstudie: Distribuera ett program som hanteras av Open Service Mesh (OSM) med Azure Application Gateway AKS-tillägg för ingress

Open Service Mesh (OSM) är ett enkelt, utökningsbart molninbyggt tjänstnät som gör att användarna kan hantera, skydda och få inbyggda observerbarhetsfunktioner för mycket dynamiska mikrotjänstmiljöer.

I de här självstudierna får du:

> [!div class="checklist"]
>
> - Visa den aktuella OSM-klusterkonfigurationen
> - Skapa namnområden för OSM för att hantera distribuerade program i namnrymderna
> - Publicera de namnrymder som ska hanteras av OSM
> - Distribuera exempelprogrammet
> - Kontrollera att programmet körs i AKS-klustret
> - Skapa en Azure Application Gateway som ska användas som ingress-kontrollant för programmet
> - Exponera en tjänst via Azure Application Gateway ingress till Internet

### <a name="before-you-begin"></a>Innan du börjar

Stegen som beskrivs i den här artikeln förutsätter att du har skapat ett AKS-kluster (Kubernetes och högre, med Kubernetes RBAC aktiverat), har upprättat en anslutning till klustret (om du behöver hjälp med något av dessa objekt kan du läsa `1.19+` `kubectl` [AKS-snabbstarten](./kubernetes-walkthrough.md), har installerat AKS OSM-tillägget och kommer att skapa en ny Azure Application Gateway för ingress.

Du måste ha följande resurser installerade:

- Azure CLI, version 2.20.0 eller senare
- Tilläggsversion `aks-preview` 0.5.5 eller senare
- AKS-klusterversion 1.19+ med hjälp Azure CNI nätverk (anslutet till ett azure-Vnet)
- OSM version v0.8.0 eller senare
- apt-get install jq

### <a name="view-and-verify-the-current-osm-cluster-configuration"></a>Visa och verifiera den aktuella OSM-klusterkonfigurationen

När OSM-tillägget för AKS har aktiverats i AKS-klustret kan du visa de aktuella konfigurationsparametrarna i Kubernetes ConfigMap för osm-config. Kör följande kommando för att visa configMap-egenskaperna:

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

Observera att **permissive_traffic_policy_mode** har konfigurerats till **sant**. Tillåtet trafikprincipläge i OSM är ett läge där [tvingande SMI-trafikprincip](https://smi-spec.io/) kringgås. I det här läget identifierar OSM automatiskt tjänster som är en del av trafikprincipreglerna för tjänstnät och program på varje Envoy-proxysidavagn för att kunna kommunicera med dessa tjänster.

### <a name="create-namespaces-for-the-application"></a>Skapa namnområden för programmet

I den här självstudien använder vi OSM-bokhandelsprogrammet som har följande programkomponenter:

- bookbuyer
- bookthief
- Bokhandel
- bookwarehouse

Skapa namnrymder för var och en av dessa programkomponenter.

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

### <a name="onboard-the-namespaces-to-be-managed-by-osm"></a>Publicera de namnrymder som ska hanteras av OSM

När du lägger till namnrymderna i OSM-nät gör detta att OSM-kontrollanten automatiskt kan mata in Envoy-containern för sidovagnsproxy med ditt program. Kör följande kommando för att publicera OSM-bokhandelsprogrammets namnrymder.

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

### <a name="deploy-the-bookstore-application-to-the-aks-cluster"></a>Distribuera bokhandelsprogrammet till AKS-klustret

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

Alla distributionsutdata sammanfattas nedan.

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

Uppdatera bookbuyer-tjänsten till rätt konfiguration av inkommande portar med följande tjänstmanifest.

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

### <a name="verify-the-bookstore-application-running-inside-the-aks-cluster"></a>Verifiera bokhandelsprogrammet som körs i AKS-klustret

Från och med nu har vi distribuerat bokhandelsprogrammet för flera containrar, men det är bara tillgängligt från AKS-klustret. Senare ska vi lägga till Azure Application Gateway ingress-kontrollanten för att exponera programmet utanför AKS-klustret. För att kontrollera att programmet körs i klustret använder vi en port framåt för att visa användargränssnittet för bookbuyer-komponenten.

Först hämtar vi bookbuyer-poddens namn

```azurecli-interactive
kubectl get pod -n bookbuyer
```

Du bör se utdata som liknar följande. Din bookbuyer-podd har ett unikt namn tillagt.

```Output
NAME                         READY   STATUS    RESTARTS   AGE
bookbuyer-7676c7fcfb-mtnrz   2/2     Running   0          7m8s
```

När vi har poddens namn kan vi nu använda kommandot port-forward för att konfigurera en tunnel från vårt lokala system till programmet i AKS-klustret. Kör följande kommando för att konfigurera port framåt för den lokala systemporten 8080. Använd återigen det specifika poddnamnet bookbuyer.

```azurecli-interactive
kubectl port-forward bookbuyer-7676c7fcfb-mtnrz -n bookbuyer 8080:14001
```

Du bör se utdata som liknar detta.

```Output
Forwarding from 127.0.0.1:8080 -> 14001
Forwarding from [::1]:8080 -> 14001
```

När sessionen för vidarebefordran av portar är på plats navigerar du till följande URL från en webbläsare `http://localhost:8080` . Du bör nu kunna se användargränssnittet för bookbuyer-programmet i webbläsaren som liknar bilden nedan.

![Avbildning av OSM bookbuyer app for App Gateway UI (OSM-bokköparapp för App Gateway användargränssnitt)](./media/aks-osm-addon/osm-agic-bookbuyer-img.png)

### <a name="create-an-azure-application-gateway-to-expose-the-bookbuyer-application-outside-the-aks-cluster"></a>Skapa en Azure Application Gateway att exponera bookbuyer-programmet utanför AKS-klustret

> [!NOTE]
> I följande anvisningar skapas en ny instans av Azure Application Gateway som ska användas för ingress. Om du har en befintlig Azure Application Gateway som du vill använda går du vidare till avsnittet för att aktivera Application Gateway för ingress-kontrollant.

#### <a name="deploy-a-new-application-gateway"></a>Distribuera en ny Application Gateway

> [!NOTE]
> Vi refererar till befintlig dokumentation för att aktivera Application Gateway Ingress Controller-tillägg för ett befintligt AKS-kluster. Vissa ändringar har gjorts för att passa OSM-materialen. Mer detaljerad dokumentation om ämnet finns [här.](https://docs.microsoft.com/azure/application-gateway/tutorial-ingress-controller-add-on-existing)

Nu ska du distribuera en ny Application Gateway för att simulera att du har en befintlig Application Gateway som du vill använda för att belastningsutjämna trafik till ditt AKS-kluster, _myCluster_. Namnet på Application Gateway är _myApplicationGateway_, men du måste först skapa en offentlig IP-resurs med namnet _myPublicIp_ och ett nytt virtuellt nätverk med namnet _myVnet_ med adressutrymmet 11.0.0.0/8 och ett undernät med adressutrymmet 11.1.0.0/16 med namnet _mySubnet_ och distribuera din Application Gateway i mySubnet med _mySubIpPublicIp_. 

När du använder ett AKS-Application Gateway i separata virtuella nätverk får adressutrymmena för de två virtuella nätverken inte överlappa varandra. Standardadressutrymmet som ett AKS-kluster distribuerar i är 10.0.0.0/8, så vi anger adressprefixet för det virtuella Application Gateway-nätverket till 11.0.0.0/8.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2
az network public-ip create -n myPublicIp -g MyResourceGroup --allocation-method Static --sku Standard
az network vnet create -n myVnet -g myResourceGroup --address-prefix 11.0.0.0/8 --subnet-name mySubnet --subnet-prefix 11.1.0.0/16
az network application-gateway create -n myApplicationGateway -l eastus2 -g myResourceGroup --sku Standard_v2 --public-ip-address myPublicIp --vnet-name myVnet --subnet mySubnet
```

> [!NOTE]
> Application Gateway AGIC-tillägg (Ingress Controller)  stöder endast SKU:er för Application Gateway v2  (Standard och WAF) och inte Application Gateway v1-SKU:er.

#### <a name="enable-the-agic-add-on-for-an-existing-aks-cluster-through-azure-cli"></a>Aktivera AGIC-tillägget för ett befintligt AKS-kluster via Azure CLI

Om du vill fortsätta använda Azure CLI kan du fortsätta att aktivera AGIC-tillägget i AKS-klustret som du skapade, _myCluster,_ och ange AGIC-tillägget för att använda den befintliga Application Gateway som du skapade, _myApplicationGateway._

```azurecli-interactive
appgwId=$(az network application-gateway show -n myApplicationGateway -g myResourceGroup -o tsv --query "id")
az aks enable-addons -n myCluster -g myResourceGroup -a ingress-appgw --appgw-id $appgwId
```

Du kan kontrollera Azure Application Gateway AKS-tillägget har aktiverats med följande kommando.

```azurecli-interactive
az aks list -g osm-aks-rg -o json | jq -r .[].addonProfiles.ingressApplicationGateway.enabled
```

Det här kommandot ska visa utdata som `true` .

#### <a name="peer-the-two-virtual-networks-together"></a>Peering av de två virtuella nätverken

Eftersom vi distribuerade AKS-klustret i ett eget virtuellt nätverk och Application Gateway i ett annat virtuellt nätverk måste du peer-peera de två virtuella nätverken för att trafiken ska flöda från Application Gateway till poddarna i klustret. Peering av de två virtuella nätverken kräver att Azure CLI-kommandot körs två separata gånger för att säkerställa att anslutningen är dubbelriktad. Det första kommandot skapar en peering-anslutning från det Application Gateway nätverket till det virtuella AKS-nätverket. det andra kommandot skapar en peering-anslutning i den andra riktningen.

```azurecli-interactive
nodeResourceGroup=$(az aks show -n myCluster -g myResourceGroup -o tsv --query "nodeResourceGroup")
aksVnetName=$(az network vnet list -g $nodeResourceGroup -o tsv --query "[0].name")

aksVnetId=$(az network vnet show -n $aksVnetName -g $nodeResourceGroup -o tsv --query "id")
az network vnet peering create -n AppGWtoAKSVnetPeering -g myResourceGroup --vnet-name myVnet --remote-vnet $aksVnetId --allow-vnet-access

appGWVnetId=$(az network vnet show -n myVnet -g myResourceGroup -o tsv --query "id")
az network vnet peering create -n AKStoAppGWVnetPeering -g $nodeResourceGroup --vnet-name $aksVnetName --remote-vnet $appGWVnetId --allow-vnet-access
```

### <a name="expose-the-bookbuyer-service-to-the-internet"></a>Exponera bookbuyer-tjänsten på Internet

Tillämpa följande ingressmanifest på AKS-klustret för att exponera bookbuyer-tjänsten mot Internet via Azure Application Gateway.

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

Eftersom värdnamnet i ingressmanifestet är ett pseudonamn som används för testning är DNS-namnet inte tillgängligt på Internet. Vi kan också använda curl-programmet och förbi värddatornamnets rubrik till den offentliga IP-adressen för Azure Application Gateway och få en 200-kod som ansluter oss till bookbuyer-tjänsten.

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

- [Dokumentation om AGIC-felsökning](https://docs.microsoft.com/azure/application-gateway/ingress-controller-troubleshoot)
- [Ytterligare felsökningsverktyg finns på AGIC:s GitHub-lagringsplatsen](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/master/docs/troubleshootings/troubleshooting-installing-a-simple-application.md)

## <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Övervakning och observerbarhet med Open Service Mesh (OSM) med hjälp Azure Monitor och Applications Insights

Både Azure Monitor och Azure Application Insights hjälper dig att maximera tillgängligheten och prestandan för dina program och tjänster genom att leverera en heltäckande lösning för att samla in, analysera och agera utifrån telemetri från dina molnmiljöer och lokala miljöer.

OSM AKS-tillägget har djupgående integreringar i båda dessa Azure-tjänster och ger en till synes olös Azure-upplevelse för att visa och svara på kritiska KPI:er som tillhandahålls av OSM-mått. Mer information om hur du aktiverar och konfigurerar dessa tjänster för OSM AKS-tillägget finns på [Azure Monitor för OSM](https://aka.ms/azmon/osmpreview) för mer information.

## <a name="tutorial-manually-deploy-prometheus-grafana-and-jaeger-to-view-open-service-mesh-osm-metrics-for-observability"></a>Självstudie: Distribuera Prometheus, Grafana och Jaeger manuellt för att visa OSM-mått (Open Service Mesh) för observerbarhet

> [!WARNING]
> Installationen av Prometheus, Grafana och Jaeger tillhandahålls som allmän vägledning för att visa hur dessa verktyg kan användas för att visa OSM-måttdata. Installationsvägledningen ska inte användas för en produktionskonfiguration. Läs dokumentationen för varje verktyg om hur du bäst passar deras installationer efter dina behov. Det viktigaste är bristen på beständig lagring, vilket innebär att alla data går förlorade när prometheus Grafana och/eller Jaeger-poddar avslutas.

Open Service Mesh (OSM) genererar detaljerade mått som är relaterade till all trafik i nätet. De här måtten ger insikter om beteendet för program i näten och hjälper användarna att felsöka, underhålla och analysera sina program.

Från och med idag samlar OSM in mått direkt från sidovagnsproxies (Envoy). OSM tillhandahåller omfattande mått för inkommande och utgående trafik för alla tjänster i nätet. Med dessa mått kan användaren få information om den totala mängden trafik, fel i trafiken och svarstiden för begäranden.

OSM använder Prometheus för att samla in och lagra konsekventa trafikmått och statistik för alla program som körs i näten. Prometheus är ett verktyg för övervakning och avisering med öppen källkod som ofta används i (men inte begränsat till) Kubernetes- och Service Mesh-miljöer.

Varje program som ingår i nät körs i en podd som innehåller en Envoy-sidovagn som exponerar mått (proxymått) i Prometheus-format. Dessutom har alla poddar som är en del av näten Prometheus-anteckningar, vilket gör det möjligt för Prometheus-servern att göra en dynamisk avklippning av programmet. Den här mekanismen möjliggör automatiskt avskraning av mått när en ny namnrymd/pod/tjänst läggs till i nätnätet.

OSM-mått kan visas med Grafana, som är en programvara för visualisering och analys med öppen källkod. Det gör att du kan fråga, visualisera, varna för och utforska dina mått.

I de här självstudierna får du:

> [!div class="checklist"]
>
> - Skapa och distribuera en Prometheus-instans
> - Konfigurera OSM för att tillåta Prometheus-avskraning
> - Uppdatera Prometheus-konfigurationskartan
> - Skapa och distribuera en Grafana-instans
> - Konfigurera Grafana med Prometheus-datakällan
> - Importera OSM-instrumentpanelen för Grafana
> - Skapa och distribuera en Jaeger-instans
> - Konfigurera Jaeger-spårning för OSM

### <a name="deploy-and-configure-a-prometheus-instance-for-osm"></a>Distribuera och konfigurera en Prometheus-instans för OSM

Vi använder Helm för att distribuera Prometheus-instansen. Kör följande kommandon för att installera Prometheus via Helm:

```azurecli-interactive
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm install stable prometheus-community/prometheus
```

Du bör se liknande utdata nedan om installationen lyckades. Anteckna Prometheus-serverporten och klustrets DNS-namn. Den här informationen kommer att användas senare för att konfigurera Prometheus som datakälla för Grafana.

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

#### <a name="configure-osm-to-allow-prometheus-scraping"></a>Konfigurera OSM för att tillåta Prometheus-avskraning

För att säkerställa att OSM-komponenterna har konfigurerats för Prometheus-avskalade, vill vi kontrollera **den prometheus_scraping-konfiguration** som finns i osm-config-konfigurationsfilen. Visa konfigurationen med följande kommando:

```azurecli-interactive
kubectl get configmap -n kube-system osm-config -o json | jq '.data.prometheus_scraping'
```

Utdata från föregående kommando ska returnera `true` om OSM har konfigurerats för Prometheus-avskraning. Om det returnerade `false` värdet är måste vi uppdatera konfigurationen till `true` . Kör följande kommando för att **aktivera** OSM Prometheus-avskraning:

```azurecli-interactive
kubectl patch ConfigMap -n kube-system osm-config --type merge --patch '{"data":{"prometheus_scraping":"true"}}'
```

Du bör se följande utdata.

```Output
configmap/osm-config patched
```

#### <a name="update-the-prometheus-configmap"></a>Uppdatera Prometheus-konfigurationskartan

Standardinstallationen av Prometheus innehåller två Kubernetes-konfigurationskartor. Du kan visa listan över Prometheus-konfigurationskartor med följande kommando.

```azurecli-interactive
kubectl get configmap | grep prometheus
```

```Output
stable-prometheus-alertmanager   1      4h34m
stable-prometheus-server         5      4h34m
```

Vi måste ersätta prometheus.yml-konfigurationen som finns i **configmap stable-prometheus-server** med följande OSM-konfiguration. Det finns flera filredigeringstekniker för att utföra den här uppgiften. Ett enkelt och säkert sätt är att exportera konfigurationskartan, skapa en kopia av den för säkerhetskopiering och sedan redigera den med en redigerare som Visual Studio kod.

> [!NOTE]
> Om du inte har Visual Studio Code kan du hämta och installera det [här.](https://code.visualstudio.com/Download)

Först exporterar vi configmap **för stable-prometheus-server** och gör sedan en kopia för säkerhetskopiering.

```azurecli-interactive
kubectl get configmap stable-prometheus-server -o yaml > cm-stable-prometheus-server.yml
cp cm-stable-prometheus-server.yml cm-stable-prometheus-server.yml.copy
```

Nu ska vi öppna filen med hjälp Visual Studio kod för att redigera.

```azurecli-interactive
code cm-stable-prometheus-server.yml
```

När du har öppnat konfigurationskartan i Visual Studio Code-redigeraren ersätter du filen prometheus.yml med OSM-konfigurationen nedan och sparar filen.

> [!WARNING]
> Det är mycket viktigt att du behåller yaml-filens indragsstruktur. Eventuella ändringar i yaml-filstrukturen kan leda till att konfigurationskartan inte kan tillämpas igen.

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

Tillämpa den uppdaterade yaml-filen configmap med följande kommando.

```azurecli-interactive
kubectl apply -f cm-stable-prometheus-server.yml
```

```Output
configmap/stable-prometheus-server configured
```

> [!NOTE]
> Du kan få ett meddelande om att en Kubernetes-anteckning saknas. Detta kan ignoreras för tillfället.

#### <a name="verify-prometheus-is-configured-to-scrape-the-osm-mesh-and-api-endpoints"></a>Kontrollera att Prometheus har konfigurerats för att skrapa OSM-nät och API-slutpunkter

För att verifiera att Prometheus är korrekt konfigurerat för att skrapa OSM-nät och API-slutpunkter portas vi till Prometheus-podden och visar målkonfigurationen. Kör följande kommandon.

```azurecli-interactive
PROM_POD_NAME=$(kubectl get pods -l "app=prometheus,component=server" -o jsonpath="{.items[0].metadata.name}")
kubectl --namespace <promNamespace> port-forward $PROM_POD_NAME 9090
```

Öppna en webbläsare upp till `http://localhost:9090/targets`

Om du rullar nedåt bör du se att alla tillstånd för SMI-måttslutpunkter är **UPP** samt andra OSM-mått som definierats enligt bilden nedan.

![Bild av användargränssnittet för OSM Prometheus-målmått](./media/aks-osm-addon/osm-prometheus-smi-metrics-target-scrape.png)

### <a name="deploy-and-configure-a-grafana-instance-for-osm&quot;></a>Distribuera och konfigurera en Grafana-instans för OSM

Vi använder Helm för att distribuera Grafana-instansen. Kör följande kommandon för att installera Grafana via Helm:

```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
helm install osm-grafana grafana/grafana
```

Nu ska vi hämta grafana-standardlösenordet för att logga in på Grafana-webbplatsen.

```azurecli-interactive
kubectl get secret --namespace default osm-grafana -o jsonpath=&quot;{.data.admin-password}&quot; | base64 --decode ; echo
```

Anteckna Grafana-lösenordet.

Nu ska vi hämta Grafana-podden till port framåt till Grafana-instrumentpanelen för inloggning.

```azurecli-interactive
GRAF_POD_NAME=$(kubectl get pods -l &quot;app.kubernetes.io/name=grafana&quot; -o jsonpath=&quot;{.items[0].metadata.name}")
kubectl port-forward $GRAF_POD_NAME 3000
```

Öppna en webbläsare upp till `http://localhost:3000`

På inloggningsskärmen som visas nedan anger du **admin** som användarnamn och använder Grafana-lösenordet som du avbildade tidigare.

![Bild av användargränssnittet för OSM Grafana-inloggningssidan](./media/aks-osm-addon/osm-grafana-ui-login.png)

#### <a name="configure-the-grafana-prometheus-data-source"></a>Konfigurera Grafana Prometheus-datakällan

När du har loggat in på Grafana är nästa steg att lägga till Prometheus som datakällor för Grafana. Det gör du genom att gå till konfigurationsikonen på den vänstra menyn och välja Datakällor enligt nedan.

![Bild av användargränssnittet för OSM Grafana-datakällor](./media/aks-osm-addon/osm-grafana-ui-datasources.png)

Klicka på **knappen Lägg till datakälla** och välj Prometheus under tidsseriedatabaser.

![BILD av ANVÄNDARGRÄNSSNITTET för OSM Grafana-datakällor](./media/aks-osm-addon/osm-grafana-ui-datasources-select-prometheus.png)

På sidan Configure your Prometheus data source below (Konfigurera din **Prometheus-datakälla** nedan) anger du Kubernetes-klustrets FQDN för Prometheus-tjänsten för HTTP-URL-inställningen. Standard-FQDN ska vara `stable-prometheus-server.default.svc.cluster.local` . När du har angett prometheus-tjänstslutpunkten bläddrar du längst ned på sidan och väljer **Spara & Testa**. Du bör få en grön kryssruta som anger att datakällan fungerar.

#### <a name="importing-osm-dashboards"></a>Importera OSM-instrumentpaneler

OSM-instrumentpaneler är tillgängliga både via:

- [Vår lagringsplats](https://github.com/grafana/grafana)och kan importeras som json-blobar via webbadministratörsportalen
- eller [online på Grafana.com](https://grafana.com/grafana/dashboards/14145)

Om du vill importera en instrumentpanel letar du `+` upp inloggningen på den vänstra menyn och väljer `import` .
Du kan importera instrumentpanelen direkt efter deras ID på `Grafana.com` . Vår instrumentpanel `OSM Mesh Details` använder till exempel ID , du kan använda `14145` ID:t direkt i formuläret och välja `import` :

![BILD av ANVÄNDARGRÄNSSNITTET för OSM Grafana-instrumentpanelens importsida](./media/aks-osm-addon/osm-grafana-dashboard-import.png)

När du väljer importera kommer du automatiskt till din importerade instrumentpanel.

![BILD av ANVÄNDARGRÄNSSNITTET för OSM Grafana Dashboard Mesh-informationssidan](./media/aks-osm-addon/osm-grafana-mesh-dashboard-details.png)

### <a name="deploy-and-configure-a-jaeger-operator-on-kubernetes-for-osm"></a>Distribuera och konfigurera en Jaeger-operatör på Kubernetes för OSM

[Jaeger är](https://www.jaegertracing.io/) ett spårningssystem med öppen källkod som används för övervakning och felsökning av distribuerade system. Den kan distribueras med OSM som en ny instans eller så kan du ta med din egen instans. Följande anvisningar distribuerar en ny instans av Jaeger till `jaeger` namnområdet i AKS-klustret.

#### <a name="deploy-jaeger-to-the-aks-cluster"></a>Distribuera Jaeger till AKS-klustret

Installera Jaeger med följande manifest:

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
> Från och med nu kan spårningsegenskaperna inte visas i konfigurationskartan för osm-config just nu. Detta görs synligt i en ny version av OSM AKS-tillägget.

Kör följande kommando för att aktivera spårning för OSM-tillägget:

```azurecli-interactive
kubectl patch configmap osm-config -n kube-system -p '{"data":{"tracing_enable":"true", "tracing_address":"jaeger.jaeger.svc.cluster.local", "tracing_port":"9411", "tracing_endpoint":"/api/v2/spans"}}' --type=merge
```

```Output
configmap/osm-config patched
```

#### <a name="view-the-jaeger-ui-with-port-forwarding"></a>Visa Jaeger-användargränssnittet med port vidarebefordran

Jaegers användargränssnitt körs på port 16686. Om du vill visa webbgränssnittet kan du använda kubectl port-forward:

```azurecli-interactive
JAEGER_POD=$(kubectl get pods -n jaeger --no-headers  --selector app=jaeger | awk 'NR==1{print $1}')
kubectl port-forward -n jaeger $JAEGER_POD  16686:16686
http://localhost:16686/
```

I webbläsaren bör du se en listrutan Tjänst där du kan välja bland de olika program som distribueras av bokhandelsdemo. Välj en tjänst för att visa alla intervall från den. Om du till exempel väljer bookbuyer med en lookback på en timme kan du se dess interaktioner med bookstore-v1 och bookstore-v2 sorterade efter tid.

![BILD av användargränssnittet för OSM Jaeger Tracing Page](./media/aks-osm-addon/osm-jaeger-trace-view-ui.png)

Välj ett objekt för att visa det i detalj. Välj flera objekt för att jämföra spårningar. Du kan till exempel jämföra bookbuyers interaktioner med bokhandel och bokhandel-v2 vid en viss tidpunkt.

Du kan också välja fliken Systemarkitektur för att visa ett diagram över hur de olika programmen har interagerat/kommunicerat. Detta ger en uppfattning om hur trafiken flödar mellan programmen.

![Avbildning av användargränssnittet för OSM Jaeger-systemarkitektur](./media/aks-osm-addon/osm-jaeger-sys-arc-view-ui.png)

## <a name="open-service-mesh-osm-aks-add-on-troubleshooting-guides"></a>Felsökningsguider för Open Service Mesh (OSM) AKS-tillägg

När du distribuerar OSM AKS-tillägget kan det ibland hända att det uppstår problem. Följande guider hjälper dig att felsöka fel och lösa vanliga problem.

### <a name="verifying-and-troubleshooting-osm-components"></a>Verifiera och felsöka OSM-komponenter

#### <a name="check-osm-controller-deployment"></a>Kontrollera distributionen av OSM-kontrollanten

```azurecli-interactive
kubectl get deployment -n kube-system --selector app=osm-controller
```

En felfri OSM-styrenhet skulle se ut så här:

```Output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
osm-controller   1/1     1            1           59m
```

#### <a name="check-the-osm-controller-pod"></a>Kontrollera OSM-styrenhetens podd

```azurecli-interactive
kubectl get pods -n kube-system --selector app=osm-controller
```

En felfri OSM-podd skulle se ut så här:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-controller-b5bd66db-wglzl   0/1     Evicted   0          61m
osm-controller-b5bd66db-wvl9w   1/1     Running   0          31m
```

Även om en kontrollant har avlägsnats någon gång har vi en annan som är REDO 1/1 och Körs med 0 omstarter. Om kolumnen READY är något annat än 1/1 skulle tjänstnätet vara i ett brutet tillstånd.
Kolumnen READY med 0/1 anger att kontrollplanscontainern kraschar – vi måste hämta loggar. Se Hämta OSM-styrenhetsloggar från Azure Support Center nedan. Kolumnen READY med ett tal som är högre än 1 efter /anger att det finns sidovagn installerade. OSM-styrenheten skulle troligen inte fungera med några sidovagnar som är kopplade till den.

> [!NOTE]
> Från och med version v0.8.2 är OSM-styrenheten inte i HA-läge och körs i en distribuerad med replikantalet 1 – enskild podd. Podden har hälsoavsökningar och startas om av kubelet om det behövs.

#### <a name="check-osm-controller-service"></a>Kontrollera OSM-styrenhetstjänsten

```azurecli-interactive
kubectl get service -n kube-system osm-controller
```

En felfri OSM-styrenhetstjänst skulle se ut så här:

```Output
NAME             TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)              AGE
osm-controller   ClusterIP   10.0.31.254   <none>        15128/TCP,9092/TCP   67m
```

> [!NOTE]
> KLUSTER-IP skulle vara annorlunda. Tjänstens NAMN och PORT(S) måste vara samma som i exemplet ovan.

#### <a name="check-osm-controller-endpoints"></a>Kontrollera OSM-styrenhetens slutpunkter

```azurecli-interactive
kubectl get endpoints -n kube-system osm-controller
```

En eller flera felfria slutpunkter för OSM-styrenheten skulle se ut så här:

```Output
NAME             ENDPOINTS                              AGE
osm-controller   10.240.1.115:9092,10.240.1.115:15128   69m
```

#### <a name="check-osm-injector-deployment"></a>Kontrollera distribution av OSM-injektor

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

En felfri OSM Injector-distribution skulle se ut så här:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-pod"></a>Kontrollera OSM-injektorpodden

```azurecli-interactive
kubectl get pod -n kube-system --selector app=osm-injector
```

En felfri OSM Injector-podd skulle se ut så här:

```Output
NAME                            READY   STATUS    RESTARTS   AGE
osm-injector-5986c57765-vlsdk   1/1     Running   0          73m
```

#### <a name="check-osm-injector-service"></a>Kontrollera OSM-injektortjänsten

```azurecli-interactive
kubectl get service -n kube-system osm-injector
```

En felfri OSM Injector-tjänst skulle se ut så här:

```Output
NAME           TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)    AGE
osm-injector   ClusterIP   10.0.39.54   <none>        9090/TCP   75m
```

#### <a name="check-osm-endpoints"></a>Kontrollera OSM-slutpunkter

```azurecli-interactive
kubectl get endpoints -n kube-system osm-injector
```

En felfri OSM-slutpunkt skulle se ut så här:

```Output
NAME           ENDPOINTS           AGE
osm-injector   10.240.1.172:9090   75m
```

#### <a name="check-validating-and-mutating-webhooks"></a>Kontrollera verifiera och mutera webhooks

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration --selector app=osm-controller
```

En felfri OSM-verifierande webhook skulle se ut så här:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      81m
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration --selector app=osm-injector
```

En felfri OSM-muterande webhook skulle se ut så här:

```Output
NAME              WEBHOOKS   AGE
aks-osm-webhook-osm   1      102m
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-validating-webhook"></a>Sök efter tjänsten och CA-paketet för verifierande webhook

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

En välkonfigurerad validering av webhookkonfiguration skulle se ut exakt så här:

```json
{
  "name": "osm-config-validator",
  "namespace": "kube-system",
  "path": "/validate-webhook",
  "port": 9093
}
```

#### <a name="check-for-the-service-and-the-ca-bundle-of-the-mutating-webhook"></a>Sök efter tjänsten och CA-paketet för den muterande webhooken

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq '.webhooks[0].clientConfig.service'
```

En välkonfigurerad konfiguration av muterande webhook skulle se ut exakt så här:

```json
{
  "name": "osm-injector",
  "namespace": "kube-system",
  "path": "/mutate-pod-creation",
  "port": 9090
}
```

#### <a name="check-whether-osm-controller-has-given-the-validating-or-mutating-webhook-a-ca-bundle"></a>Kontrollera om OSM-kontrollanten har gett webhooken för validering (eller mutering) ett CA-paket

> [!NOTE]
> Från och med v0.8.2 Det är viktigt att veta att AKS RP installerar den verifierande webhooken, AKS Reconciler säkerställer att den finns, men OSM-kontrollanten är den som fyller CA-paketet.

```azurecli-interactive
kubectl get ValidatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```azurecli-interactive
kubectl get MutatingWebhookConfiguration aks-osm-webhook-osm -o json | jq -r '.webhooks[0].clientConfig.caBundle' | wc -c
```

```Example Output
1845
```

Det här numret anger antalet byte eller storleken på CA-paketet. Om detta är tomt, 0 eller någon siffra under 1 000 indikerar det att CA-paketet inte är korrekt etablerat. Utan ett korrekt CA-paket skulle den verifierande webhooken bli felaktig och hindra användaren från att göra ändringar i osm-config ConfigMap i kube-system-namnområdet.

Ett exempelfel när CA-paketet är felaktigt:

- Ett försök att ändra osm-config ConfigMap:

```azurecli-interactive
kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"2m"}}'
```

- Fel:

```
Error from server (InternalError): Internal error occurred: failed calling webhook "osm-config-webhook.k8s.io": Post https://osm-config-validator.kube-system.svc:9093/validate-webhook?timeout=30s: x509: certificate signed by unknown authority
```

Gå runt för när **validerande** Webhook-konfiguration har ett felaktigt certifikat:

- Alternativ 1 – Starta om OSM-styrenheten – detta startar om OSM-styrenheten. När du startar skriver den över CA-paketet för både mutating- och validating-webhooks.

```azurecli-interactive
kubectl rollout restart deployment -n kube-system osm-controller
```

- Alternativ 2 – Alternativ 2. Ta bort den verifierande webhooken – om du tar bort den verifierande webhooken så verifieras inte `osm-config` configMap längre. Alla korrigeringar går igenom. AKS Reconciler ser vid något tillfälle till att verifierande webhook finns och återskapar den. OSM-kontrollanten kan behöva startas om för att snabbt skriva om CA-paketet.

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm
```

- Alternativ 3 – Ta bort och Korrigera: Följande kommando tar bort den verifierande webhooken så att vi kan lägga till värden och försöker omedelbart tillämpa en korrigering. Förmodligen har AKS Reconciler inte tillräckligt med tid för att stämma av och återställa den verifierande webhooken, vilket ger oss möjlighet att tillämpa en ändring som en sista utväg:

```azurecli-interactive
kubectl delete ValidatingWebhookConfiguration aks-osm-webhook-osm; kubectl patch ConfigMap osm-config -n kube-system --type merge --patch '{"data":{"config_resync_interval":"15s"}}'
```

#### <a name="check-the-osm-config-configmap"></a>Kontrollera `osm-config` **configMap**

> [!NOTE]
> OSM-kontrollanten kräver inte att `osm-config` ConfigMap finns i namnområdet kube-system. Kontrollanten har rimliga standardvärden för -konfiguration och kan fungera utan den.

Kontrollera om det finns:

```azurecli-interactive
kubectl get ConfigMap -n kube-system osm-config
```

Kontrollera innehållet i konfigurationskartan för osm-config

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
| Avstigning                           | boolesk   | SANT, FALSKT                                             | `"false"`                              | Aktiverar utgående i nät.                                                                                                                                                                                                             |
| enable_debug_server              | boolesk   | SANT, FALSKT                                             | `"true"`                               | Aktiverar en felsökningsslutpunkt på osm-controller-podden för att visa information om nät, till exempel proxyanslutningar, certifikat och SMI-principer.                                                                                    |
| enable_privileged_init_container | boolesk   | SANT, FALSKT                                             | `"false"`                              | Aktiverar privilegierade init-containrar för poddar i nät. När det är falskt har init-containrar bara NET_ADMIN.                                                                                                                                   |
| envoy_log_level                  | sträng | trace, debug, info, warning, warn, error, critical, off | `"error"`                              | Anger loggningsverositeten för Envoy-proxysidans sidovagn, som endast gäller för nyligen skapade poddar som ansluter till nätet. Om du vill uppdatera loggnivån för befintliga poddar startar du om distributionen med `kubectl rollout restart` .                            |
| outbound_ip_range_exclusion_list | sträng | kommaavgränsad lista över IP-intervall i form av a.b.c.d/x | `-`                                    | Global lista över IP-adressintervall som ska undantas från utgående trafikskärning av sidovagnsproxyn.                                                                                                                                    |
| permissive_traffic_policy_mode   | boolesk   | SANT, FALSKT                                             | `"false"`                              | Om du `true` anger till aktiverar du läget allow-all i nätet, dvs. ingen trafikprincip används i nätet. Om det är `false` inställt på aktiverar neka-all trafikprincip i mesh, dvs. en `SMI Traffic Target` krävs för att tjänster ska kunna kommunicera. |
| prometheus_scraping              | boolesk   | SANT, FALSKT                                             | `"true"`                               | Aktiverar Prometheus-måttskrasning på sidovagnsproxies.                                                                                                                                                                                 |
| service_cert_validity_duration   | sträng | 24h, 1h30m (valfri tidslängd)                          | `"24h"`                                | Anger giltighetstiden för tjänstcertifikatet, som representeras som en sekvens med decimaltal med ett valfritt bråktal och ett enhetssuffix.                                                                                             |
| tracing_enable                   | boolesk   | SANT, FALSKT                                             | `"false"`                              | Aktiverar Jaeger-spårning för näten.                                                                                                                                                                                                    |
| tracing_address                  | sträng | jaeger.mesh-namespace.svc.cluster.local                 | `jaeger.kube-system.svc.cluster.local` | Adress för Jaeger-distributionen, om spårning har aktiverats.                                                                                                                                                                                |
| tracing_endpoint                 | sträng | /api/v2/spans                                           | /api/v2/spans                          | Slutpunkt för spårning av data, om spårning har aktiverats.                                                                                                                                                                                          |
| tracing_port                     | int    | ett heltalsvärde som inte är noll                              | `"9411"`                               | Port där spårning är aktiverat.                                                                                                                                                                                                       |
| use_https_ingress                | boolesk   | SANT, FALSKT                                             | `"false"`                              | Aktiverar HTTPS-ingress i nätnätet.                                                                                                                                                                                                      |
| config_resync_interval           | sträng | inaktiverar detta under 1 minut                            | 0 (inaktiverat)                           | När ett värde över 1 m (60 sekunder) anges skickar OSM-kontrollanten all tillgänglig konfiguration till varje anslutet Envoy med det angivna intervallet                                                                                                    |

#### <a name="check-namespaces"></a>Kontrollera namnområden

> [!NOTE]
> Namnområdet kube-system deltar aldrig i ett tjänstnät och märks aldrig och/eller kommenteras aldrig med nyckeln/värdena nedan.

Vi använder kommandot `osm namespace add` för att ansluta namnrymder till ett visst tjänstnät.
När ett k8s-namnområde är en del av nätet (eller för att det ska ingå i nätet) måste följande vara sant:

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

Om ett namnområde inte kommenteras med eller inte är märkt med `"openservicemesh.io/sidecar-injection": "enabled"` `"openservicemesh.io/monitored-by": "osm"` OSM-injektorn lägger du inte till Envoy-sidovagner.

> Obs! När `osm namespace add` anropas **matas endast** nya poddar in med en Envoy-sidovagn. Befintliga poddar måste startas om med `kubectl rollout restart deployment ...`

#### <a name="verify-the-smi-crds"></a>Kontrollera SMI-CRD:erna:

Kontrollera om klustret har nödvändiga CRD:er:

```azurecli-interactive
kubectl get crds
```

Vi måste ha följande installerat i klustret:

- httproutegroups.specs.smi-spec.io
- tcproutes.specs.smi-spec.io
- trafficsplits.split.smi-spec.io
- traffictargets.access.smi-spec.io
- udproutes.specs.smi-spec.io

Hämta versionerna av CRD:erna installerade med det här kommandot:

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

OSM Controller v0.8.2 kräver följande versioner:

- traffictargets.access.smi-spec.io – [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)
- httproutegroups.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup)
- tcproutes.specs.smi-spec.io – [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute)
- udproutes.specs.smi-spec.io – stöds inte
- trafficsplits.split.smi-spec.io – [v1alpha2](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha2/traffic-split.md)
- \*.metrics.smi-spec.io – [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md)

Om CRD:er saknas använder du följande kommandon för att installera dem i klustret:

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/access.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/specs.yaml
```

```azurecli-interactive
kubectl apply -f https://raw.githubusercontent.com/openservicemesh/osm/v0.8.2/charts/osm/crds/split.yaml
```

## <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>Inaktivera Open Service Mesh-tillägg (OSM) för ditt AKS-kluster

Om du vill inaktivera OSM-tillägget kör du följande kommando:

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```

<!-- LINKS - internal -->

[kubernetes-service]: concepts-network.md#services
[az-feature-register]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_register
[az-feature-list]: /cli/azure/feature?view=azure-cli-latest&preserve-view=true#az_feature_list
[az-provider-register]: /cli/azure/provider?view=azure-cli-latest&preserve-view=true#az_provider_register
