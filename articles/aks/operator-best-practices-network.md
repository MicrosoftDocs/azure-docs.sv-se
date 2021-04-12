---
title: Metod tips för nätverks resurser
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för virtuella nätverks resurser och anslutningar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 1e0212766e7d5443664d57a97cfa9ea9d0035da3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104957"
---
# <a name="best-practices-for-network-connectivity-and-security-in-azure-kubernetes-service-aks"></a>Metodtips för nätverksanslutning och säkerhet i Azure Kubernetes Service (AKS)

När du skapar och hanterar kluster i Azure Kubernetes service (AKS), ger du nätverks anslutning för dina noder och program. Dessa nätverks resurser omfattar IP-adressintervall, belastningsutjämnare och ingångs styrenheter. För att upprätthålla en hög tjänst kvalitet för dina program måste du utarbeta och konfigurera dessa resurser.

I den här artikeln fokuserar vi på nätverks anslutning och säkerhet för kluster operatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Jämför nätverks lägena Kubernetes och Azure Container Network Interface (CNI) i AKS.
> * Planera för nödvändig IP-adressering och anslutning.
> * Distribuera trafik med belastnings utjämning, ingångs styrenheter eller en brand vägg för webbaserade program (WAF).
> * Anslut säkert till klusternoder.

## <a name="choose-the-appropriate-network-model"></a>Välj lämplig nätverks modell

> **Vägledning och metodtips** 
> 
> Använd Azure CNI Networking i AKS för integrering med befintliga virtuella nätverk eller lokala nätverk. Den här nätverks modellen ger större separering av resurser och kontroller i en företags miljö.

Virtuella nätverk ger grundläggande anslutning för AKS-noder och kunder för att få åtkomst till dina program. Det finns två olika sätt att distribuera AKS-kluster i virtuella nätverk:

* **Azure CNI-nätverk**

    Distribuerar till ett virtuellt nätverk och använder [Azure cni][cni-networking] Kubernetes-plugin-programmet. Poddar tar emot enskilda IP-adresser som kan vidarebefordra till andra nätverks tjänster eller lokala resurser.
* **Kubernetes nätverk**

    Azure hanterar de virtuella nätverks resurserna när klustret distribueras och använder [Kubernetes][kubenet] Kubernetes-plugin-programmet.


För produktions distributioner är både Kubernetes och Azure CNI giltiga alternativ.

### <a name="cni-networking"></a>CNI nätverk

Azure CNI är ett leverantörs oberoende protokoll som gör det möjligt för behållar körningen att göra förfrågningar till en nätverks leverantör. Den tilldelar IP-adresser till poddar och noder, och tillhandahåller funktioner för IP-adressinformation (IPAM) när du ansluter till befintliga virtuella Azure-nätverk. Varje nod och Pod-resurs får en IP-adress i det virtuella Azure-nätverket – inget behov av extra routning för att kommunicera med andra resurser eller tjänster.

![Diagram som visar två noder med bryggor som ansluter var och en till ett enda Azure VNet](media/operator-best-practices-network/advanced-networking-diagram.png)

I synnerhet kan Azure CNI Networking för produktion göra det möjligt att separera kontroll och hantering av resurser. Från ett säkerhets perspektiv vill du ofta att olika team ska kunna hantera och skydda resurserna. Med Azure CNI Networking ansluter du till befintliga Azure-resurser, lokala resurser eller andra tjänster direkt via IP-adresser tilldelade till varje pod.

När du använder Azure CNI-nätverk finns den virtuella nätverks resursen i en separat resurs grupp till AKS-klustret. Delegera behörigheter för AKS-klustrets identitet för att komma åt och hantera dessa resurser. Den kluster identitet som används av AKS-klustret måste ha minst [nätverks deltagar](../role-based-access-control/built-in-roles.md#network-contributor) behörighet för under nätet i det virtuella nätverket. 

Om du vill definiera en [anpassad roll](../role-based-access-control/custom-roles.md) i stället för att använda den inbyggda rollen nätverks deltagare, krävs följande behörigheter:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

Som standard använder AKS en hanterad identitet för dess kluster identitet. Du kan dock använda ett huvud namn för tjänsten i stället. Mer information om:
* Delegering av AKS-tjänstens huvud namn finns i [Delegera åtkomst till andra Azure-resurser][sp-delegation]. 
* Hanterade identiteter finns i [använda hanterade identiteter](use-managed-identity.md).

När varje nod och Pod tar emot sin egen IP-adress ska du planera ut adress intervallen för AKS-undernätet. Tänk på följande:
* Under nätet måste vara tillräckligt stort för att tillhandahålla IP-adresser för varje nod, poddar och nätverks resurs som du distribuerar. 
    * Med både Kubernetes-och Azure CNI-nätverk har varje nod som kör standard gränser till antalet poddar.
* Varje AKS-kluster måste placeras i sitt eget undernät. 
* Undvik att använda IP-adressintervall som överlappar befintliga nätverks resurser. 
    * Krävs för att tillåta anslutning till lokala eller peer-anslutna nätverk i Azure.
* Om du vill hantera utskalning av händelser eller kluster uppgraderingar behöver du ytterligare IP-adresser tillgängliga i det tilldelade under nätet. 
    * Det här extra adress utrymmet är särskilt viktigt om du använder Windows Server-behållare, eftersom de noderna kräver en uppgradering för att tillämpa de senaste säkerhets korrigeringarna. Mer information om Windows Server-noder finns [i uppgradera en Node-pool i AKS][nodepool-upgrade].

Information om vilka IP-adresser som krävs finns i [Konfigurera Azure cni Networking i AKS][advanced-networking].

När du skapar ett kluster med Azure CNI Networking anger du andra adress intervall för klustret, till exempel Docker Bridge-adress, DNS-tjänstens IP-adress och tjänst adress intervall. I allmänhet ser du till att dessa adress intervall:
* Överlappar inte varandra.
* Överlappar inte några nätverk som är associerade med klustret, inklusive virtuella nätverk, undernät, lokala och peer-anslutna nätverk. 

Mer information kring gränser och storlek för dessa adress intervall finns i [Konfigurera Azure cni-nätverk i AKS][advanced-networking].

### <a name="kubenet-networking"></a>Kubernetes nätverk

Även om Kubernetes inte kräver att du konfigurerar de virtuella nätverken innan klustret distribueras, finns det nack delar med att vänta:

* Eftersom noder och poddar placeras i olika IP-undernät dirigerar UDR (User Defined routing routing) och IP-vidarebefordring trafik mellan poddar och noder. Den här extra routningen kan minska nätverks prestandan.
* Anslutningar till befintliga lokala nätverk eller peering till andra virtuella Azure-nätverk kan vara komplexa.

Eftersom du inte skapar det virtuella nätverket och undernät separat från AKS-klustret är Kubernetes idealiskt för:
* Små utvecklings-eller test arbets belastningar. 
* Enkla webbplatser med låg trafik.
* Lyft och flytta arbets belastningar till behållare.

För de flesta produktions distributioner bör du planera för och använda Azure CNI-nätverk.

Du kan också [Konfigurera egna IP-adressintervall och virtuella nätverk med Kubernetes][aks-configure-kubenet-networking]. Precis som med Azure CNI-nätverk överlappar inte de här adress intervallen varandra och bör inte överlappa varandra med de nätverk som är kopplade till klustret (virtuella nätverk, undernät, lokala och peer-anslutna nätverk). 

Mer information kring gränser och storlek för dessa adress intervall finns i [använda Kubernetes-nätverk med dina egna IP-adressintervall i AKS][aks-configure-kubenet-networking].

## <a name="distribute-ingress-traffic"></a>Distribuera inkommande trafik

> **Vägledning och metodtips** 
> 
> Använd ingress-resurser och styrenheter för att distribuera HTTP-eller HTTPS-trafik till dina program. I jämförelse med en Azure Load Balancer tillhandahåller ingångs styrenheter extra funktioner och kan hanteras som inbyggda Kubernetes-resurser.

Även om en Azure Load Balancer kan distribuera kund trafik till program i ditt AKS-kluster, är det begränsat att förstå trafiken. En belastnings Utjämnings resurs arbetar på nivå 4 och distribuerar trafik baserat på protokoll eller portar. 

De flesta webb program som använder HTTP eller HTTPS bör använda Kubernetes ingress-resurser och kontrollanter som arbetar på nivå 7. Ingress kan distribuera trafik baserat på programmets URL och hantera TLS/SSL-avslutning. Ingressen minskar också antalet IP-adresser som du exponerar och mappar. 

Med en belastningsutjämnare behöver varje program vanligt vis en offentlig IP-adress som tilldelats och mappas till tjänsten i AKS-klustret. Med en ingress-resurs kan en enskild IP-adress distribuera trafik till flera program.

![Diagram över ingress trafikflöde i ett AKS-kluster](media/operator-best-practices-network/aks-ingress.png)

 Det finns två komponenter för ingress:

 * En ingress- *resurs*
 * En ingångs *kontroll*

### <a name="ingress-resource"></a>Ingress-resurs

*Ingress-resursen* är ett yaml-manifest `kind: Ingress` . Den definierar värden, certifikat och regler för att dirigera trafik till tjänster som körs i ditt AKS-kluster. 

Följande exempel på YAML-manifest distribuerar trafik för *MyApp.com* till en av två tjänster, *blogservice* eller *storeservice*. Kunden dirigeras till en tjänst eller till en annan utifrån den URL som de har åtkomst till.

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

### <a name="ingress-controller"></a>Ingress-styrenhet

En ingångs *kontroll* är en daemon som körs på en AKS-nod och som söker efter inkommande begär Anden. Trafiken distribueras sedan baserat på de regler som definierats i ingress-resursen. Medan den vanligaste ingångs styrenheten är baserad på [nginx], begränsar AKS inte dig till en speciell kontrollant. Du kan använda [kontur][contour], [haproxy][haproxy], [Traefik][traefik]osv.

Ingångs styrenheter måste schemaläggas på en Linux-nod. Ange att resursen ska köras på en Linux-baserad nod med hjälp av en Node-selektor i YAML-manifestet eller Helm-diagrammets distribution. Mer information finns i [använda Node Selector för att styra var poddar schemaläggs i AKS][concepts-node-selectors].

> [!NOTE]
> Windows Server-noder bör inte köra ingresskontrollanten.

Det finns många scenarier för ingress, inklusive följande instruktions guider:

* [Skapa en grundläggande ingångs kontroll med extern nätverks anslutning][aks-ingress-basic]
* [Skapa en ingångs kontroll enhet som använder ett internt, privat nätverk och IP-adress][aks-ingress-internal]
* [Skapa en ingångs kontroll enhet som använder dina egna TLS-certifikat][aks-ingress-own-tls]
* Skapa en ingångs kontroll enhet som använder kryptera för att automatiskt generera TLS [-certifikat med en dynamisk offentlig IP-adress][aks-ingress-tls] eller [med en statisk offentlig IP-adress][aks-ingress-static-tls]

## <a name="secure-traffic-with-a-web-application-firewall-waf"></a>Skydda trafik med en brand vägg för webbaserade program (WAF)

> **Vägledning och metodtips**
> 
> Om du vill genomsöka inkommande trafik för potentiella attacker använder du en brand vägg för webbaserade program (WAF) som [Barracuda WAF för Azure][barracuda-waf] eller Azure Application Gateway. Dessa mer avancerade nätverks resurser kan också dirigera trafik bortom bara HTTP-och HTTPS-anslutningar eller för grundläggande TLS-avslutning.

En ingress-kontrollant är vanligt vis en Kubernetes-resurs i ditt AKS-kluster som distribuerar trafik till tjänster och program. Kontrollanten körs som en daemon på en AKS-nod och använder en del av nodens resurser, t. ex. processor, minne och nätverks bandbredd. I större miljöer ska du:
* Avlasta en del trafik dirigering eller TLS-avslutning till en nätverks resurs utanför AKS-klustret.
* Genomsök inkommande trafik för potentiella attacker.

![En brand vägg för webbaserade program (WAF) som Azure App Gateway kan skydda och distribuera trafik för ditt AKS-kluster](media/operator-best-practices-network/web-application-firewall-app-gateway.png)

För det extra säkerhets lagret filtrerar en brand vägg för webbaserade program (WAF) inkommande trafik. Med en regel uppsättning söker OWASP (Open Web Application Security Project) efter attacker som skript körning över olika webbplatser eller cookie-förgiftning. [Azure Application Gateway][app-gateway] (för närvarande i för hands version i AKS) är en WAF som integreras med AKS-kluster och låser i dessa säkerhetsfunktioner innan trafiken når ditt AKS-kluster och-program. 

Eftersom andra lösningar från tredje part även utför dessa funktioner kan du fortsätta att använda befintliga investeringar eller expertis i din önskade produkt.

Belastnings Utjämnings-eller ingångs resurser körs kontinuerligt i ditt AKS-kluster och förfinar trafik distributionen. App Gateway kan hanteras centralt som en ingångs hanterare med en resurs definition. Kom igång genom att [skapa en Application Gateway][app-gateway-ingress]ingångs kontroll.

## <a name="control-traffic-flow-with-network-policies"></a>Kontrol lera trafikflödet med nätverks principer

> **Vägledning och metodtips** 
>
> Använd Nätverks principer för att tillåta eller neka trafik till poddar. Som standard tillåts all trafik mellan poddar i ett kluster. För förbättrad säkerhet, definiera regler som begränsar Pod-kommunikation.

Nätverks principen är en Kubernetes-funktion som är tillgänglig i AKS som låter dig styra trafikflödet mellan poddar. Du tillåter eller nekar trafik till Pod baserat på inställningar som tilldelad etikett, namnrymd eller trafik port. Nätverks principer är ett moln – inbyggt sätt att styra trafik flödet för poddar. Allteftersom poddar skapas dynamiskt i ett AKS-kluster kan nödvändiga nätverks principer tillämpas automatiskt.

Aktivera funktionen när du skapar ett nytt AKS-kluster om du vill använda nätverks principer. Du kan inte aktivera nätverks princip i ett befintligt AKS-kluster. Planera framåt för att aktivera nätverks princip på nödvändiga kluster. 

>[!NOTE]
>Nätverks principen bör endast användas för Linux-baserade noder och poddar i AKS.

Du skapar en nätverks princip som en Kubernetes-resurs med hjälp av ett YAML-manifest. Principer tillämpas på definierade poddar, med ingångs-eller utgångs regler som definierar trafikflöde. 

I följande exempel används en nätverks princip för att poddar med *appen: Server dels* etikett som används för dem. Regeln ingångs regel tillåter endast trafik från poddar med *appen:-frontend* -etiketten:

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

> **Vägledning och metodtips** 
>
> Exponera inte fjärr anslutning till dina AKS-noder. Skapa en skydds-värd eller hopp Box i ett virtuellt hanterings nätverk. Använd skydds-värden för att på ett säkert sätt dirigera trafik till ditt AKS-kluster till fjärrhanterings uppgifter.

Du kan slutföra de flesta åtgärder i AKS med hjälp av Azures hanterings verktyg eller Kubernetes API-servern. AKS-noder är bara tillgängliga i ett privat nätverk och är inte anslutna till det offentliga Internet. För att ansluta till noder och tillhandahålla underhåll och support, dirigera dina anslutningar via en skydds-värd eller hopp box. Kontrol lera att den här värden finns i ett separat, betrott, peer-kopplat virtuella nätverk till AKS-klustrets virtuella nätverk.

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