---
title: Konfigurera nätverk för Azure dev Spaces i olika nätverkstopologier
services: azure-dev-spaces
ms.date: 03/17/2020
ms.topic: conceptual
description: Beskriver nätverks kraven för att köra Azure dev Spaces i Azure Kubernetes Services
keywords: Azure dev Spaces, dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, CNI, Kubernetes, SDN, Network
ms.openlocfilehash: 09114ab13555cbf9ef42b37c86ffb76a8fe3ab3f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91970346"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Konfigurera nätverk för Azure dev Spaces i olika nätverkstopologier

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure dev Spaces körs på Azure Kubernetes service-kluster (AKS) med standard nätverks konfigurationen. Om du vill ändra nätverks konfigurationen för ditt AKS-kluster, till exempel att placera klustret bakom en brand vägg, använda nätverks säkerhets grupper eller använda nätverks principer, måste du lägga till ytterligare överväganden för att köra Azure dev Spaces.

![Konfiguration av virtuellt nätverk](media/configure-networking/virtual-network-clusters.svg)

## <a name="virtual-network-or-subnet-configurations"></a>Konfiguration av virtuellt nätverk eller undernät

Ditt AKS-kluster kan ha en annan virtuell nätverks-eller under näts konfiguration för att begränsa inkommande eller utgående trafik för ditt AKS-kluster. Klustret kan till exempel ligga bakom en brand vägg, t. ex. Azure-brandvägg, eller så kan du använda nätverks säkerhets grupper eller anpassade roller för att begränsa nätverks trafiken. Du hittar ett exempel på en nätverks konfiguration i [exempel databasen för Azure dev Spaces på GitHub][sample-repo].

Azure dev Spaces har vissa krav för ingångs- *och utgående* nätverks trafik samt *endast* ingångs trafik. Om du använder Azure dev Spaces i ett AKS-kluster med ett virtuellt nätverk eller en under näts konfiguration som begränsar trafiken för ditt AKS-kluster, måste du följa följande ingångs-och ingångs-och utgångs trafik krav för att Azure dev Spaces ska fungera korrekt.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Krav på inkommande och utgående nätverks trafik

Azure dev Spaces behöver inkommande och utgående trafik för följande FQDN: er:

| FQDN                       | Port       | Användning      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Hämta Docker-avbildningar för Azure dev Spaces |
| gcr.io                     | HTTPS: 443 | Hämta Helm-avbildningar för Azure dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Hämta Helm-avbildningar för Azure dev Spaces |

Uppdatera brand väggen eller säkerhets konfigurationen för att tillåta nätverks trafik till och från alla ovanstående FQDN och [infrastruktur tjänster i Azure dev Spaces][service-tags]. Om du till exempel använder en brand vägg för att skydda ditt nätverk, ska ovanstående FQDN-namn läggas till i brand väggens program regel och Azure dev Spaces-tjänst tag gen måste också [läggas till i brand väggen][firewall-service-tags]. Båda dessa uppdateringar av brand väggen krävs för att tillåta trafik till och från de här domänerna.

### <a name="ingress-only-network-traffic-requirements"></a>Ingress endast nätverks trafik krav

Azure dev Spaces tillhandahåller routning av Kubernetes namn områdes nivå samt offentlig åtkomst till tjänster med hjälp av sitt eget fullständiga domän namn. För att båda funktionerna ska fungera uppdaterar du brand väggen eller nätverks konfigurationen för att tillåta att offentliga ingångar till den externa IP-adressen för Azure dev-platser intränger på klustret. Du kan också skapa en [intern belastningsutjämnare][aks-internal-lb] och lägga till en NAT-regel i brand väggen för att översätta den offentliga IP-adressen för brand väggen till IP-adressen för den interna belastningsutjämnaren. Du kan också använda [traefik][traefik-ingress] eller [nginx][nginx-ingress] för att skapa en anpassad ingångs kontroll.

## <a name="aks-cluster-network-requirements"></a>Nätverks krav för AKS-kluster

Med AKS kan du använda [nätverks principer][aks-network-policies] för att styra inkommande och utgående trafik mellan poddar i ett kluster samt utgående trafik från en pod. Azure dev Spaces har vissa krav för ingångs- *och utgående* nätverks trafik samt *endast* ingångs trafik. Om du använder Azure dev Spaces i ett AKS-kluster med AKS-nätverks principer, måste du följa följande ingångs-och ingångs-och utgångs trafik krav för att Azure dev Spaces ska fungera korrekt.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Krav på inkommande och utgående nätverks trafik

Med Azure dev Spaces kan du kommunicera direkt med en POD i ett dev-utrymme i klustret för fel sökning. För att den här funktionen ska fungera lägger du till en nätverks princip som tillåter inkommande och utgående kommunikation till IP-adresserna för Azure dev Spaces-infrastrukturen, som [varierar beroende på region][service-tags].

### <a name="ingress-only-network-traffic-requirements"></a>Ingress endast nätverks trafik krav

Azure dev Spaces tillhandahåller routning mellan poddar över namn områden. Namn områden med aktiverade Azure dev-utrymmen kan till exempel ha en överordnad/underordnad-relation, vilket gör att nätverks trafiken kan dirigeras mellan poddar över de överordnade och underordnade namn områdena. Azure dev Spaces exponerar också tjänstens slut punkter med sitt eget fullständiga domän namn. Om du vill konfigurera olika sätt att exponera tjänster och hur det påverkar routning på namn områdes nivå, se [använda olika slut punkts alternativ][endpoint-options].

## <a name="using-azure-cni"></a>Använda Azure-CNI

Som standard är AKS-kluster konfigurerade för att använda [Kubernetes][aks-kubenet] för nätverk, vilket fungerar med Azure dev Spaces. Du kan också konfigurera ditt AKS-kluster för att använda [cni (Azure Container Networking Interface)][aks-cni]. Om du vill använda Azure dev Spaces med Azure CNI i ditt AKS-kluster kan du tillåta ditt virtuella nätverk och under nätets adress utrymme upp till 10 privata IP-adresser för poddar som distribueras av Azure dev Spaces. Mer information om hur du tillåter privata IP-adresser finns i [AKS Azure cni-dokumentationen][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges"></a>Använda tillåtna IP-intervall för API-Server

Med AKS-kluster kan du konfigurera ytterligare säkerhet som begränsar vilken IP-adress som kan interagera med dina kluster, till exempel att använda anpassade virtuella nätverk eller [Skydda åtkomsten till API-servern med hjälp av auktoriserade IP-adressintervall][aks-ip-auth-ranges]. Om du vill använda Azure dev Spaces när du använder den här extra säkerheten när du [skapar][aks-ip-auth-range-create] klustret, måste du [tillåta ytterligare intervall baserat på din region][service-tags]. Du kan också [Uppdatera][aks-ip-auth-range-update] ett befintligt kluster för att tillåta dessa ytterligare intervall. Du måste också tillåta IP-adressen för alla utvecklings datorer som ansluter till ditt AKS-kluster för fel sökning för att kunna ansluta till din API-Server.

## <a name="using-aks-private-clusters"></a>Använda privata AKS-kluster

För närvarande stöds inte Azure dev Spaces med [privata AKS-kluster][aks-private-clusters].

## <a name="using-different-endpoint-options"></a>Använda olika slut punkts alternativ

Azure dev Spaces har möjlighet att exponera slut punkter för dina tjänster som körs på AKS. När du aktiverar Azure dev Spaces i klustret har du följande alternativ för att konfigurera slut punkts typen för klustret:

* En *offentlig* slut punkt, som är standard, distribuerar en ingångs kontroll med en offentlig IP-adress. Den offentliga IP-adressen är registrerad på klustrets DNS och möjliggör offentlig åtkomst till dina tjänster med hjälp av en URL. Du kan visa den här URL: en med hjälp av `azds list-uris` .
* En *privat* slut punkt distribuerar en ingress-styrenhet med en privat IP-adress. Med en privat IP-adress är belastningsutjämnaren för klustret bara tillgänglig i det virtuella nätverket i klustret. Belastnings utjämningens privata IP-adress är registrerad på klustrets DNS så att tjänsterna i klustrets virtuella nätverk kan nås via en URL. Du kan visa den här URL: en med hjälp av `azds list-uris` .
* Om *ingen* inställning anges för slut punkts alternativet distribueras ingen ingångs kontroll. När ingen ingångs kontroll har distribuerats fungerar inte [cirkulations funktionerna i Azure dev][dev-spaces-routing] . Om du vill kan du implementera en egen lösning för ingångs kontroll med [traefik][traefik-ingress] eller [nginx][nginx-ingress], vilket gör att routningsfunktioner kan fungera igen.

Om du vill konfigurera slut punkts alternativet använder du *-e* eller *--Endpoint* när du aktiverar Azure dev Spaces i klustret. Exempel:

> [!NOTE]
> Slut punkts alternativet kräver att du kör Azure CLI version 2.2.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

```azurecli
az aks use-dev-spaces -g MyResourceGroup -n MyAKS -e private
```

## <a name="client-requirements"></a>Klientkrav

Azure dev Spaces använder verktyg på klient sidan, till exempel CLI-tillägget för Azure dev Spaces, Visual Studio Code extension och Visual Studio-tillägget för att kommunicera med ditt AKS-kluster för fel sökning. Om du vill använda verktyget för Azure dev-verktyg på klient sidan kan du tillåta trafik från utvecklings datorerna till [Azure dev Spaces-infrastrukturen][dev-spaces-allow-infrastructure]. Om du använder [tillåtna IP-intervall för API-Server][auth-range-section]måste du också tillåta IP-adressen för alla utvecklings datorer som ansluter till ditt AKS-kluster för fel sökning för att kunna ansluta till din API-Server.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](how-dev-spaces-works.md)

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges
[azure-cli-install]: /cli/azure/install-azure-cli
[dev-spaces-allow-infrastructure]: #virtual-network-or-subnet-configurations
[dev-spaces-routing]: how-dev-spaces-works-routing.md
[endpoint-options]: #using-different-endpoint-options
[firewall-service-tags]: ../firewall/service-tags.md
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[sample-repo]: https://github.com/Azure/dev-spaces/tree/master/advanced%20networking
[service-tags]: ../virtual-network/service-tags-overview.md#available-service-tags