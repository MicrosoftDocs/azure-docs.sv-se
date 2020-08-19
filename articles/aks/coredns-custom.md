---
title: Anpassa CoreDNS för Azure Kubernetes service (AKS)
description: Lär dig hur du anpassar CoreDNS för att lägga till under domäner eller utöka anpassade DNS-slutpunkter med Azure Kubernetes service (AKS)
services: container-service
author: jnoller
ms.topic: article
ms.date: 03/15/2019
ms.author: jenoller
ms.openlocfilehash: e99d841dcfb18b41df128283c37f46682e3fa129
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/16/2020
ms.locfileid: "88257119"
---
# <a name="customize-coredns-with-azure-kubernetes-service"></a>Anpassa CoreDNS med Azure Kubernetes Service

I Azure Kubernetes service (AKS) används ett [CoreDNS][coredns] -projekt för kluster-DNS-hantering och-matchning med alla *1.12. x* och högre kluster. Tidigare användes Kube-DNS-projektet. Detta Kube-DNS-projekt är nu föråldrat. Mer information om anpassnings-och Kubernetes för CoreDNS finns i den [officiella överströms dokumentationen][corednsk8s].

Eftersom AKS är en hanterad tjänst kan du inte ändra huvud konfigurationen för CoreDNS (en *CoreFile*). I stället använder du en Kubernetes- *ConfigMap* för att åsidosätta standardinställningarna. Om du vill se standard AKS CoreDNS-ConfigMaps använder du `kubectl get configmaps --namespace=kube-system coredns -o yaml` kommandot.

Den här artikeln visar hur du använder ConfigMaps för grundläggande anpassnings alternativ för CoreDNS i AKS. Den här metoden skiljer sig från att konfigurera CoreDNS i andra kontexter, t. ex. genom att använda CoreFile. Kontrol lera vilken version av CoreDNS som körs eftersom konfigurations värdena kan ändras mellan olika versioner.

> [!NOTE]
> `kube-dns` Erbjud olika [anpassnings alternativ][kubednsblog] via en Kubernetes-konfigurations karta. CoreDNS är **inte** bakåtkompatibla med Kube-DNS. Alla anpassningar som du använt tidigare måste uppdateras för användning med CoreDNS.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln förutsätter att du har ett befintligt AKS-kluster. Om du behöver ett AKS-kluster kan du läsa snabb starten för AKS [med hjälp av Azure CLI][aks-quickstart-cli] eller [Azure Portal][aks-quickstart-portal].

När du skapar en konfiguration som exemplen nedan måste dina namn i *data* avsnittet avslutas med antingen *. Server* eller *. override*. Den här namngivnings konventionen definieras i standard AKS CoreDNS-Configmap som du kan visa med `kubectl get configmaps --namespace=kube-system coredns -o yaml` kommandot.

## <a name="what-is-supportedunsupported"></a>Vad stöds/stöds inte

Alla inbyggda plugin-program för CoreDNS stöds. Inga plugin-program för tillägg/tredje part stöds.

## <a name="rewrite-dns"></a>Skriv över DNS

Ett scenario du har för att utföra omskrivning av DNS-namn skrivs om. I följande exempel ersätter `<domain to be written>` du med ditt eget fullständigt kvalificerade domän namn. Skapa en fil med namnet `corednsms.yaml` och klistra in följande exempel konfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        errors
        cache 30
        rewrite name substring <domain to be rewritten>.com default.svc.cluster.local
        kubernetes cluster.local in-addr.arpa ip6.arpa {
          pods insecure
          upstream
          fallthrough in-addr.arpa ip6.arpa
        }
        forward .  /etc/resolv.conf # you can redirect this to a specific DNS server such as 10.0.0.10, but that server must be able to resolve the rewritten domain name
    }
```

> [!IMPORTANT]
> Om du omdirigerar till en DNS-server, till exempel CoreDNS-tjänstens IP-adress, måste DNS-servern kunna matcha det omskrivna domän namnet.

Skapa ConfigMap med kommandot [kubectl Apply ConfigMap][kubectl-apply] och ange namnet på din yaml-manifest:

```console
kubectl apply -f corednsms.yaml
```

Om du vill kontrol lera att anpassningarna har tillämpats använder du [kubectl Hämta configmaps][kubectl-get] och anger din *coredns-anpassade* ConfigMap:

```
kubectl get configmaps --namespace=kube-system coredns-custom -o yaml
```

Tvinga nu CoreDNS att läsa in ConfigMap igen. Kommandot [kubectl Delete Pod][kubectl delete] är inte destruktivt och orsakar inte någon tids gräns. `kube-dns`Poddar tas bort och Schemaläggaren för Kubernetes återskapar dem. Dessa nya poddar innehåller ändringen i TTL-värdet.

```console
kubectl delete pod --namespace kube-system -l k8s-app=kube-dns
```

> [!Note]
> Kommandot ovan är korrekt. Medan vi ändrar `coredns` är distributionen under **Kube-DNS-** namnet.

## <a name="custom-forward-server"></a>Anpassad Forward Server

Om du behöver ange en Forward-Server för nätverks trafiken kan du skapa en ConfigMap för att anpassa DNS. I följande exempel uppdaterar du `forward` namn och adress med värdena för din egen miljö. Skapa en fil med namnet `corednsms.yaml` och klistra in följande exempel konfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    <domain to be rewritten>.com:53 {
        forward foo.com 1.1.1.1
    }
```

Som i föregående exempel skapar du ConfigMap med kommandot [kubectl Apply ConfigMap][kubectl-apply] och anger namnet på ditt yaml-manifest. Sedan tvingar du CoreDNS att läsa in ConfigMap igen med [kubectl ta bort Pod][kubectl delete] för Kubernetes Scheduler för att återskapa dem:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="use-custom-domains"></a>Använd anpassade domäner

Du kanske vill konfigurera anpassade domäner som bara kan lösas internt. Du kanske till exempel vill matcha den anpassade domänen *puglife. local*, vilket inte är en giltig toppnivå domän. Utan en anpassad domän ConfigMap kan AKS-klustret inte matcha adressen.

I följande exempel uppdaterar du den anpassade domänen och IP-adressen för att dirigera trafik till med värdena för din egen miljö. Skapa en fil med namnet `corednsms.yaml` och klistra in följande exempel konfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  puglife.server: | # you may select any name here, but it must end with the .server file extension
    puglife.local:53 {
        errors
        cache 30
        forward . 192.11.0.1  # this is my test/dev DNS server
    }
```

Som i föregående exempel skapar du ConfigMap med kommandot [kubectl Apply ConfigMap][kubectl-apply] och anger namnet på ditt yaml-manifest. Sedan tvingar du CoreDNS att läsa in ConfigMap igen med [kubectl ta bort Pod][kubectl delete] för Kubernetes Scheduler för att återskapa dem:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="stub-domains"></a>Stub-domäner

CoreDNS kan också användas för att konfigurera stub-domäner. I följande exempel uppdaterar du de anpassade domänerna och IP-adresserna med värdena för din egen miljö. Skapa en fil med namnet `corednsms.yaml` och klistra in följande exempel konfiguration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  test.server: | # you may select any name here, but it must end with the .server file extension
    abc.com:53 {
        errors
        cache 30
        forward . 1.2.3.4
    }
    my.cluster.local:53 {
        errors
        cache 30
        forward . 2.3.4.5
    }

```

Som i föregående exempel skapar du ConfigMap med kommandot [kubectl Apply ConfigMap][kubectl-apply] och anger namnet på ditt yaml-manifest. Sedan tvingar du CoreDNS att läsa in ConfigMap igen med [kubectl ta bort Pod][kubectl delete] för Kubernetes Scheduler för att återskapa dem:

```console
kubectl apply -f corednsms.yaml
kubectl delete pod --namespace kube-system --selector k8s-app=kube-dns
```

## <a name="hosts-plugin"></a>Värd-plugin

Eftersom alla inbyggda plugin-program stöds betyder det att CoreDNS är [värd][coredns hosts] för plugin-programmet är tillgängligt för anpassning:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom # this is the name of the configmap you can overwrite with your changes
  namespace: kube-system
data:
    test.override: | # you may select any name here, but it must end with the .override file extension
          hosts example.hosts example.org { # example.hosts must be a file
              10.0.0.1 example.org
              fallthrough
          }
```

## <a name="enable-logging-for-dns-query-debugging"></a>Aktivera loggning för fel sökning av DNS-frågor 

Använd följande konfiguration i din coredns-anpassade ConfigMap för att aktivera loggning av DNS-frågor:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  log.override: | # you may select any name here, but it must end with the .override file extension
        log
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln visades några exempel scenarier för CoreDNS-anpassning. Information om CoreDNS-projektet finns [på sidan CoreDNS uppströms projekt][coredns].

Mer information om grundläggande nätverks koncept finns i [nätverks koncept för program i AKS][concepts-network].

<!-- LINKS - external -->
[kubednsblog]: https://www.danielstechblog.io/using-custom-dns-server-for-domain-specific-name-resolution-with-azure-kubernetes-service/
[coredns]: https://coredns.io/
[corednsk8s]: https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/#coredns
[dnscache]: https://coredns.io/plugins/cache/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[coredns hosts]: https://coredns.io/plugins/hosts/

<!-- LINKS - internal -->
[concepts-network]: concepts-network.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
