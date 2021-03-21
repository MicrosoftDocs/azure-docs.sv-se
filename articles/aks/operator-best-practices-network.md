---
title: Metod tips för nätverks resurser
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för virtuella nätverks resurser och anslutningar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/10/2018
ms.openlocfilehash: 2bd332dbf9412f5c42e77b14ada3aab67ec8b66a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508596"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Metodtips för nätverksanslutning och säkerhet i Azure Kubernetes Service (AKS)

När du skapar och hanterar kluster i Azure Kubernetes service (AKS), ger du nätverks anslutning för dina noder och program. Dessa nätverks resurser omfattar IP-adressintervall, belastningsutjämnare och ingångs styrenheter. För att upprätthålla en hög tjänst kvalitet för dina program måste du planera för och sedan konfigurera dessa resurser.

I den här artikeln fokuserar vi på nätverks anslutning och säkerhet för kluster operatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Jämför nätverks lägena Kubernetes och Azure Container Network Interface (CNI) i AKS
> * Planera för nödvändig IP-adressering och anslutning
> * Distribuera trafik med belastningsutjämnare, ingångs styrenheter eller en brand vägg för webbaserade program (WAF)
> * Anslut säkert till klusternoder

## <a name="choose-the-appropriate-network-model"></a>Välj lämplig nätverks modell

**Vägledning för bästa praxis** – för integrering med befintliga virtuella nätverk eller lokala nätverk använder du Azure cni Networking i AKS. Den här nätverks modellen ger också större separering av resurser och kontroller i en företags miljö.

Virtuella nätverk ger grundläggande anslutning för AKS-noder och kunder för att få åtkomst till dina program. Det finns två olika sätt att distribuera AKS-kluster i virtuella nätverk:

* **Kubernetes Network** – Azure hanterar de virtuella nätverks resurserna när klustret distribueras och använder [Kubernetes][kubenet] Kubernetes-pluginprogrammet.
* **Azure cni Network** – distribuerar till ett virtuellt nätverk och använder [cni-plugin-programmet (Azure Container Network Interface)][cni-networking] . Poddar tar emot enskilda IP-adresser som kan vidarebefordra till andra nätverks tjänster eller lokala resurser.

För produktions distributioner är både Kubernetes och Azure CNI giltiga alternativ.

### <a name="cni-networking"></a>CNI nätverk

CNI (container Networking Interface) är ett oberoende protokoll som gör det möjligt för container runtime att göra förfrågningar till en nätverks leverantör. Azure-CNI tilldelar IP-adresser till poddar och noder, och tillhandahåller funktioner för IP-adress hantering (IPAM) när du ansluter till befintliga virtuella Azure-nätverk. Varje nod och Pod resurs får en IP-adress i det virtuella Azure-nätverket och ingen extra routning behövs för att kommunicera med andra resurser eller tjänster.

![Diagram som visar två noder med bryggor som ansluter var och en till ett enda Azure VNet](media/operator-best-practices-network/advanced-networking-diagram.png)

En viktig fördel med Azure CNI Networking för produktion är att nätverks modellen gör det möjligt att separera kontroll och hantering av resurser. Från ett säkerhets perspektiv vill du ofta att olika team ska kunna hantera och skydda resurserna. Med Azure CNI Networking kan du ansluta till befintliga Azure-resurser, lokala resurser eller andra tjänster direkt via IP-adresser tilldelade till varje pod.

När du använder Azure CNI-nätverk finns den virtuella nätverks resursen i en separat resurs grupp till AKS-klustret. Delegera behörigheter för AKS-klustrets identitet för att komma åt och hantera dessa resurser. Den kluster identitet som används av AKS-klustret måste ha minst [nätverks deltagar](../role-based-access-control/built-in-roles.md#network-contributor) behörighet för under nätet i det virtuella nätverket. Om du vill definiera en [anpassad roll](../role-based-access-control/custom-roles.md) i stället för att använda den inbyggda rollen nätverks deltagare, krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Som standard använder AKS en hanterad identitet för dess kluster identitet, men du har möjlighet att använda ett huvud namn för tjänsten i stället. Mer information om delegering av AKS tjänst objekt finns i [Delegera åtkomst till andra Azure-resurser][sp-delegation]. Mer information om hanterade identiteter finns i [använda hanterade identiteter](use-managed-identity.md).

När varje nod och Pod tar emot sin egen IP-adress ska du planera ut adress intervallen för AKS-undernätet. Under nätet måste vara tillräckligt stort för att tillhandahålla IP-adresser för varje nod, poddar och nätverks resurser som du distribuerar. Varje AKS-kluster måste placeras i sitt eget undernät. Använd inte IP-adressintervall som överlappar befintliga nätverks resurser för att tillåta anslutning till lokala eller peer-anslutna nätverk i Azure. Det finns standard gränser för antalet poddar som varje nod kör med både Kubernetes och Azure CNI-nätverk. Om du vill hantera utskalning av händelser eller kluster uppgraderingar behöver du även extra IP-adresser som är tillgängliga för användning i det tilldelade under nätet. Det här extra adress utrymmet är särskilt viktigt om du använder Windows Server-behållare, eftersom de noderna kräver en uppgradering för att tillämpa de senaste säkerhets korrigeringarna. Mer information om Windows Server-noder finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].

Information om vilka IP-adresser som krävs finns i [Konfigurera Azure cni Networking i AKS][advanced-networking].

När du skapar ett kluster med Azure CNI Networking anger du andra adress intervall som ska användas av klustret, till exempel Docker Bridge-adress, DNS-tjänstens IP-adress och tjänst adress intervall. I allmänhet bör dessa adress intervall inte överlappa varandra och ska inte överlappa några nätverk som är associerade med klustret, inklusive virtuella nätverk, undernät, lokala och peer-baserade nätverk. Mer information kring gränser och storlek för dessa adress intervall finns i [Konfigurera Azure cni-nätverk i AKS][advanced-networking].

### <a name="kubenet-networking"></a>Kubernetes nätverk

Även om Kubernetes inte kräver att du konfigurerar de virtuella nätverken innan klustret distribueras, finns det några nack delar:

* Noder och poddar placeras i olika IP-undernät. Användardefinierad routning (UDR) och IP-vidarebefordring används för att dirigera trafik mellan poddar och noder. Den här extra routningen kan minska nätverks prestandan.
* Anslutningar till befintliga lokala nätverk eller peering till andra virtuella Azure-nätverk kan vara komplexa.

Kubernetes är lämpligt för små utvecklings-eller test arbets belastningar eftersom du inte behöver skapa det virtuella nätverket och undernät separat från AKS-klustret. Enkla webbplatser med låg trafik, eller för att lyfta och byta arbets belastningar till behållare, kan också dra nytta av att förenkla AKS-kluster som distribueras med Kubernetes-nätverk. För de flesta produktions distributioner bör du planera för och använda Azure CNI-nätverk.

Du kan också [Konfigurera egna IP-adressintervall och virtuella nätverk med Kubernetes][aks-configure-kubenet-networking]. På liknande sätt som med Azure CNI-nätverk överlappar inte dessa adress intervall varandra och får inte överlappa några nätverk som är associerade med klustret, inklusive virtuella nätverk, undernät, lokala och peer-anslutna nätverk. Mer information kring gränser och storlek för dessa adress intervall finns i [använda Kubernetes-nätverk med dina egna IP-adressintervall i AKS][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuera inkommande trafik

**Metod tips** om hur du distribuerar http-eller HTTPS-trafik till dina program med hjälp av ingress-resurser och styrenheter. Ingångs styrenheter ger extra funktioner över en vanlig Azure Load Balancer och kan hanteras som interna Kubernetes-resurser.

En Azure Load Balancer kan distribuera kund trafik till program i ditt AKS-kluster, men det är begränsat i vad den förstår för trafiken. En belastnings Utjämnings resurs arbetar på nivå 4 och distribuerar trafik baserat på protokoll eller portar. De flesta webb program som använder HTTP eller HTTPS bör använda Kubernetes ingress-resurser och kontrollanter som arbetar på nivå 7. Ingress kan distribuera trafik baserat på programmets URL och hantera TLS/SSL-avslutning. Den här möjligheten minskar också antalet IP-adresser som du exponerar och mappar. Med en belastningsutjämnare behöver varje program vanligt vis en offentlig IP-adress som tilldelats och mappas till tjänsten i AKS-klustret. Med en ingress-resurs kan en enskild IP-adress distribuera trafik till flera program.

![Diagram över ingress trafikflöde i ett AKS-kluster](media/operator-best-practices-network/aks-ingress.png)

 Det finns två komponenter för ingress:

 * En ingress- *resurs* och
 * En ingångs *kontroll*

Ingångs resursen är ett YAML manifest `kind: Ingress` som definierar värden, certifikat och regler för att dirigera trafik till tjänster som körs i ditt AKS-kluster. Följande exempel på YAML-manifest distribuerar trafik för *MyApp.com* till en av två tjänster, *blogservice* eller *storeservice*. Kunden dirigeras till en tjänst eller till en annan utifrån den URL som de har åtkomst till.

```yaml
kind: Ingress
metadata:
 name: myapp-ingress
   annotations: kubernetes.io/ingress.class: "PublicIngress"
spec:
 tls:
 - hosts:
   - myapp.com
   secretName: myapp-secret
 rules:
   - host: myapp.com
     http:
      paths:
      - path: /blog
        backend:
         serviceName: blogservice
         servicePort: 80
      - path: /store
        backend:
         serviceName: storeservice
         servicePort: 80
```

En ingångs kontroll är en daemon som körs på en AKS-nod och som söker efter inkommande begär Anden. Trafiken distribueras sedan baserat på de regler som definierats i ingress-resursen. Den vanligaste ingångs styrenheten baseras på [nginx]. AKS begränsar dig till en speciell kontrollant, så du kan använda andra kontrollanter som till exempel [kontur][contour], [haproxy][haproxy]eller [Traefik][traefik].

Ingångs styrenheter måste schemaläggas på en Linux-nod. Windows Server-noder bör inte köra ingresskontrollanten. Använd en Node-selektor i YAML-manifestet eller Helm diagram distributionen för att ange att resursen ska köras på en Linux-baserad nod. Mer information finns i [använda Node Selector för att styra var poddar schemaläggs i AKS][concepts-node-selectors].

Det finns många scenarier för ingress, inklusive följande instruktions guider:

* [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
* [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
* [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
* Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Skydda trafik med en brand vägg för webbaserade program (WAF)

**Vägledning för bästa praxis** – om du vill genomsöka inkommande trafik för potentiella attacker använder du en brand vägg för webbaserade program (WAF) som [Barracuda WAF för Azure][barracuda-waf] eller Azure Application Gateway. Dessa mer avancerade nätverks resurser kan också dirigera trafik bortom bara HTTP-och HTTPS-anslutningar eller för grundläggande TLS-avslutning.

En ingångs kontroll som distribuerar trafik till tjänster och program är vanligt vis en Kubernetes-resurs i ditt AKS-kluster. Kontrollanten körs som en daemon på en AKS-nod och använder en del av nodens resurser, till exempel processor, minne och nätverks bandbredd. I större miljöer vill du ofta kunna avlasta en del trafik dirigering eller TLS-avslutning till en nätverks resurs utanför AKS-klustret. Du vill också genomsöka inkommande trafik för potentiella attacker.

![En brand vägg för webbaserade program (WAF) som Azure App Gateway kan skydda och distribuera trafik för ditt AKS-kluster](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

En brand vägg för webbaserade program (WAF) ger ett extra säkerhets lager genom att filtrera inkommande trafik. Det öppna webb programmet säkerhets projekt (OWASP) innehåller en uppsättning regler för att se om det finns attacker som Cross Site Scripting eller cookie-förgiftning. [Azure Application Gateway][app-gateway] (för närvarande i för hands version i AKS) är en WAF som kan integreras med AKS-kluster för att tillhandahålla dessa säkerhetsfunktioner, innan trafiken når ditt AKS-kluster och-program. Andra lösningar från tredje part utför även dessa funktioner, så du kan fortsätta att använda befintliga investeringar eller expertis i en specifik produkt.

Belastnings Utjämnings-eller ingress-resurser fortsätter att köras i AKS-klustret för att ytterligare förfina trafik distributionen. App Gateway kan hanteras centralt som en ingångs hanterare med en resurs definition. Kom igång genom att [skapa en Application Gateway][app-gateway-ingress]ingångs kontroll.

## <a name="control-traffic-flow-with-network-policies"></a>Kontrol lera trafikflödet med nätverks principer

**Vägledning för bästa praxis** – Använd Nätverks principer för att tillåta eller neka trafik till poddar. Som standard tillåts all trafik mellan poddar i ett kluster. För förbättrad säkerhet, definiera regler som begränsar Pod-kommunikation.

Nätverks principen är en Kubernetes-funktion som gör att du kan styra trafikflödet mellan poddar. Du kan välja att tillåta eller neka trafik baserat på inställningar som tilldelad etikett, namnrymd eller trafik port. Användningen av nätverks principer ger ett Cloud-inbyggt sätt att kontrol lera trafikflödet. Allteftersom poddar skapas dynamiskt i ett AKS-kluster kan de nödvändiga nätverks principerna tillämpas automatiskt. Använd inte Azures nätverks säkerhets grupper för att styra Pod-till-Pod-trafik, Använd Nätverks principer.

Om du vill använda nätverks principen måste funktionen vara aktive rad när du skapar ett AKS-kluster. Du kan inte aktivera nätverks princip i ett befintligt AKS-kluster. Planera framåt för att se till att du aktiverar nätverks princip i kluster och kan använda dem efter behov. Nätverks principen bör endast användas för Linux-baserade noder och poddar i AKS.

En nätverks princip skapas som en Kubernetes-resurs med hjälp av ett YAML-manifest. Principerna tillämpas på definierade poddar, sedan ingångs-eller utgångs regler definierar hur trafiken kan flöda. I följande exempel används en nätverks princip för att poddar med *appen: Server dels* etikett som används för dem. Regeln ingångs regel tillåter sedan bara trafik från poddar med *appen:-frontend* -etiketten:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

För att komma igång med principer, se [säker trafik mellan poddar med hjälp av nätverks principer i Azure Kubernetes service (AKS)][use-network-policies].

## <a name="securely-connect-to-nodes-through-a-bastion-host"></a>Anslut säkert till noder via en skydds-värd

**Vägledning för bästa praxis** – exponera inte fjärr anslutning till dina AKS-noder. Skapa en skydds-värd eller hopp Box i ett virtuellt hanterings nätverk. Använd skydds-värden för att på ett säkert sätt dirigera trafik till ditt AKS-kluster till fjärrhanterings uppgifter.

De flesta åtgärder i AKS kan utföras med hjälp av Azures hanterings verktyg eller Kubernetes API-servern. AKS-noder är inte anslutna till det offentliga Internet och är bara tillgängliga i ett privat nätverk. Om du vill ansluta till noderna och utföra underhåll eller felsöka problem kan du dirigera dina anslutningar via en skydds-värd eller genom att hoppa. Den här värden bör vara i ett separat virtuellt hanterings nätverk som är säkert peer-kopplat till AKS-klustrets virtuella nätverk.

![Ansluta till AKS-noder med en skydds-värd eller hopp ruta](media/operator-best-practices-network/connect-using-bastion-host-simplified.png)

Hanterings nätverket för skydds-värden bör också skyddas. Använd en [Azure-ExpressRoute][expressroute] eller [VPN-gateway][vpn-gateway] för att ansluta till ett lokalt nätverk och kontrol lera åtkomst med hjälp av nätverks säkerhets grupper.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på nätverks anslutning och säkerhet. Mer information om grunderna i nätverk i Kubernetes finns i [nätverks koncept för program i Azure Kubernetes service (AKS)][aks-concepts-network]

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet
[app-gateway-ingress]: https://github.com/Azure/application-gateway-kubernetes-ingress
[nginx]: https://www.nginx.com/products/nginx/kubernetes-ingress-controller
[contour]: https://github.com/heptio/contour
[haproxy]: https://www.haproxy.org
[traefik]: https://github.com/containous/traefik
[barracuda-waf]: https://www.barracuda.com/products/webapplicationfirewall/models/5

<!-- INTERNAL LINKS -->
[aks-concepts-network]: concepts-network.md
[sp-delegation]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[expressroute]: ../expressroute/expressroute-introduction.md
[vpn-gateway]: ../vpn-gateway/vpn-gateway-about-vpngateways.md
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-own-tls]: ingress-own-tls.md
[app-gateway]: ../application-gateway/overview.md
[use-network-policies]: use-network-policies.md
[advanced-networking]: configure-azure-cni.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[concepts-node-selectors]: concepts-clusters-workloads.md#node-selectors
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool