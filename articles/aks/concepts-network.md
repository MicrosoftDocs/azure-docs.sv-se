---
title: Koncept – nätverk i Azure Kubernetes Services (AKS)
description: Lär dig mer om nätverk i Azure Kubernetes service (AKS), inklusive Kubernetes och Azure CNI Networking, ingress controllers, belastningsutjämnare och statiska IP-adresser.
ms.topic: conceptual
ms.date: 03/11/2021
ms.custom: fasttrack-edit
ms.openlocfilehash: 56c98163434fbe2d29cf49bf750d6f7d1cfe0d2b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105348"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Nätverks koncept för program i Azure Kubernetes service (AKS)

I en container-baserad, mikrotjänster-metod för program utveckling arbetar program komponenterna tillsammans för att bearbeta sina uppgifter. Kubernetes tillhandahåller olika resurser som möjliggör det här samarbetet:
* Du kan ansluta till och exponera program internt eller externt. 
* Du kan bygga program med hög tillgänglighet genom belastnings utjämning för dina program. 
* För mer komplexa program kan du konfigurera inkommande trafik för SSL/TLS-terminering eller routning av flera komponenter. 
* Av säkerhets skäl kan du begränsa flödet av nätverks trafik till eller mellan poddar och noder.

I den här artikeln beskrivs de grundläggande koncepten som ger nätverk till dina program i AKS:

- [Tjänster](#services)
- [Virtuella Azure-nätverk](#azure-virtual-networks)
- [Ingresskontrollanter](#ingress-controllers)
- [Nätverks principer](#network-policies)

## <a name="kubernetes-basics"></a>Grundläggande om Kubernetes

Om du vill tillåta åtkomst till dina program eller mellan program komponenter tillhandahåller Kubernetes ett abstraktions lager för virtuella nätverk. Kubernetes-noder ansluter till ett virtuellt nätverk som tillhandahåller inkommande och utgående anslutning för poddar. Komponenten *Kube-proxy* körs på varje nod för att tillhandahålla de här nätverks funktionerna.

I Kubernetes:
* *Tjänster* logiskt grupperar poddar för att tillåta direkt åtkomst på en speciell port via en IP-adress eller DNS-namn. 
* Du kan distribuera trafik med hjälp av en *belastningsutjämnare*. 
* Mer komplex routning av program trafik kan också uppnås med ingångs *enheter*. 
* Säkerhet och filtrering av nätverks trafik för poddar är möjlig med Kubernetes- *nätverks principer*.

Azure-plattformen fören klar också virtuella nätverk för AKS-kluster. När du skapar en Kubernetes-belastningsutjämnare skapar och konfigurerar du även den underliggande resursen för Azure Load Balancer. När du öppnar nätverks portar till poddar konfigureras motsvarande regler för Azure nätverks säkerhets grupper. För HTTP-programroutning kan Azure också konfigurera *extern DNS* eftersom nya ingress-vägar konfigureras.

## <a name="services"></a>Tjänster

För att förenkla nätverks konfigurationen för program arbets belastningar använder Kubernetes *tjänster* för att logiskt gruppera en uppsättning poddar tillsammans och tillhandahålla nätverks anslutning. Följande tjänst typer är tillgängliga:

- **Kluster-IP** 
  
  Skapar en intern IP-adress som ska användas i AKS-klustret. Lämpar sig för interna program som har stöd för andra arbets belastningar i klustret.

    ![Diagram över kluster-IP-trafikflöde i ett AKS-kluster][aks-clusterip]

- **NodePort** 

  Skapar en port mappning på den underliggande noden som gör att programmet kan nås direkt med nodens IP-adress och port.

    ![Diagram som visar NodePort trafikflöde i ett AKS-kluster][aks-nodeport]

- **LoadBalancer** 

  Skapar en Azure Load Balancer-resurs, konfigurerar en extern IP-adress och ansluter de begärda poddar till backend-poolen för belastningsutjämnare. Om du vill tillåta att kunders trafik når programmet skapas belastnings Utjämnings regler på önskade portar. 

    ![Diagram som visar Load Balancer trafikflöde i ett AKS-kluster][aks-loadbalancer]

    För extra kontroll och routning av inkommande trafik kan du i stället använda en ingångs [kontroll](#ingress-controllers).

- **ExternalName** 

  Skapar en specifik DNS-post för enklare program åtkomst.

Antingen IP-adressen för belastningsutjämnare och tjänster kan tilldelas dynamiskt eller så kan du ange en befintlig statisk IP-adress. Du kan tilldela både interna och externa statiska IP-adresser. Befintliga statiska IP-adresser är ofta kopplade till en DNS-post.

Du kan skapa både *interna* och *externa* belastningsutjämnare. Interna belastningsutjämnare tilldelas bara en privat IP-adress, så de kan inte nås från Internet.

## <a name="azure-virtual-networks"></a>Virtuella Azure-nätverk

I AKS kan du distribuera ett kluster som använder någon av följande två nätverksmodeller:

- *Kubernetes* nätverk

  Nätverks resurserna skapas och konfigureras vanligt vis när AKS-klustret distribueras.

- Nätverk för *Azure Container Network Interface (cni)*
 
  AKS-klustret ansluts till befintliga resurser och konfigurationer för virtuella nätverk.

### <a name="kubenet-basic-networking"></a>Kubernetes (grundläggande) nätverk

Alternativet *Kubernetes* Networking är standard konfigurationen för skapande av AKS-kluster. Med *Kubernetes*:
1. Noder får en IP-adress från det virtuella nätverkets undernät i Azure. 
1. Poddar tar emot en IP-adress från ett logiskt annorlunda adress utrymme än noderna i det virtuella nätverkets undernät i Azure. 
1. NAT (Network Address Translation) konfigureras sedan så att poddarna kan komma åt resurser i det virtuella Azure-nätverket. 
1. Trafikens käll-IP-adress översätts till nodens primära IP-adress.

Noder använder plugin-programmet [Kubernetes][kubenet] Kubernetes. Du kan:
* Låt Azure-plattformen skapa och konfigurera virtuella nätverk åt dig, eller 
* Välj att distribuera ditt AKS-kluster till ett befintligt virtuellt nätverks under nät. 

Kom ihåg att endast de noder som tar emot en IP-adress för routning. Poddar använder NAT för att kommunicera med andra resurser utanför AKS-klustret. Den här metoden minskar antalet IP-adresser som du behöver reservera i ditt nätverks utrymme för att poddar ska kunna användas.

Mer information finns i [Konfigurera Kubernetes-nätverk för ett AKS-kluster][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (avancerat) nätverk

Med Azure CNI får varje podd en IP-adress från undernätet och kan nås direkt. De här IP-adresserna måste planeras i förväg och vara unika i ditt nätverks utrymme. Varje nod har en konfigurations parameter för det maximala antalet poddar som stöds. Motsvarande antal IP-adresser per nod reserveras sedan fram. Utan planering kan den här metoden leda till att IP-adressen är överbelastad eller att du behöver återskapa kluster i ett större undernät när ditt program kräver ökad tillväxt.

Till skillnad från Kubernetes är trafik till slut punkter i samma virtuella nätverk inte NAT till nodens primära IP. Käll adressen för trafik i det virtuella nätverket är Pod IP. Trafik som är extern för det virtuella nätverket fortfarande NAT till nodens primära IP.

Noder använder [Azure cni][cni-networking] Kubernetes-plugin-programmet.

![Diagram som visar två noder med bryggor som ansluter var och en till ett enda Azure VNet][advanced-networking-diagram]

Mer information finns i [Konfigurera Azure-cni för ett AKS-kluster][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Jämför nätverks modeller

Både Kubernetes och Azure CNI ger nätverks anslutning till dina AKS-kluster. Det finns dock fördelar och nack delar med var och en. På hög nivå gäller följande aspekter:

* **Kubernetes**
    * Bevarar IP-adressutrymmet.
    * Använder intern eller extern belastningsutjämnare i Kubernetes för att uppnå poddar utanför klustret.
    * Du hanterar och underhåller UDR (User-Defined routing) manuellt.
    * Högst 400 noder per kluster.
* **Azure-CNI**
    * Poddar får fullständig virtuell nätverks anslutning och kan nås direkt via deras privata IP-adress från anslutna nätverk.
    * Kräver mer IP-adressutrymme.

Det finns följande skillnader mellan Kubernetes och Azure CNI:

| Funktion                                                                                   | Kubernetes   | Azure-CNI |
|----------------------------------------------------------------------------------------------|-----------|-----------|
| Distribuera kluster i befintligt eller nytt virtuellt nätverk                                            | Stödd-UDR manuellt tillämpat | Stöds |
| Pod – Pod-anslutning                                                                         | Stöds | Stöds |
| Pod – VM-anslutning; Virtuell dator i samma virtuella nätverk                                          | Fungerar när det initieras av Pod | Fungerar på båda sätten |
| Pod – VM-anslutning; Virtuell dator i peer-kopplat virtuellt nätverk                                            | Fungerar när det initieras av Pod | Fungerar på båda sätten |
| Lokal åtkomst med hjälp av VPN eller Express Route                                                | Fungerar när det initieras av Pod | Fungerar på båda sätten |
| Åtkomst till resurser som skyddas av tjänst slut punkter                                             | Stöds | Stöds |
| Exponera Kubernetes Services med hjälp av en belastningsutjämnare, app-gateway eller ingångs kontroll | Stöds | Stöds |
| Standard Azure DNS och privata zoner                                                          | Stöds | Stöds |

För DNS erbjuds både Kubernetes-och Azure CNI-plugin-program DNS av CoreDNS, en distribution som körs i AKS med en egen autoskalning. Mer information om CoreDNS på Kubernetes finns i [Anpassa DNS-tjänsten](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS som standard är konfigurerat för att vidarebefordra okända domäner till DNS-funktionerna i Azure-Virtual Network där AKS-klustret distribueras. Därför kommer Azure DNS och privata zoner att fungera för poddar som körs i AKS.

### <a name="support-scope-between-network-models"></a>Support omfattning mellan nätverks modeller

Vilken nätverks modell du använder kan både Kubernetes och Azure CNI distribueras på något av följande sätt:

* Azure-plattformen kan automatiskt skapa och konfigurera virtuella nätverks resurser när du skapar ett AKS-kluster.
* Du kan manuellt skapa och konfigurera virtuella nätverks resurser och koppla dem till resurserna när du skapar ditt AKS-kluster.

Även om funktioner som tjänst slut punkter eller UDR stöds med både Kubernetes och Azure CNI, definierar [support principerna för AKS][support-policies] vilka ändringar du kan göra. Exempel:

* Om du manuellt skapar de virtuella nätverks resurserna för ett AKS-kluster, kan du konfigurera dina egna UDR-eller tjänst slut punkter.
* Om Azure-plattformen automatiskt skapar de virtuella nätverks resurserna för ditt AKS-kluster kan du inte manuellt ändra de AKS-hanterade resurserna för att konfigurera dina egna UDR-eller tjänst slut punkter.

## <a name="ingress-controllers"></a>Ingresskontrollanter

När du skapar en tjänst för LoadBalancer-typ skapar du även en underliggande resurs för Azure Load Balancer. Belastningsutjämnaren konfigureras för att distribuera trafik till poddar i din tjänst på en specifik port. 

LoadBalancer fungerar endast på nivå 4. På nivå 4 är tjänsten inte medveten om de faktiska programmen och kan inte göra några fler Dirigerings överväganden.

*Ingress-kontroller* fungerar på nivå 7 och kan använda mer intelligenta regler för att distribuera program trafik. Ingångs kontroller dirigerar vanligt vis HTTP-trafik till olika program baserat på den inkommande URL: en.

![Diagram över ingress trafikflöde i ett AKS-kluster][aks-ingress]

### <a name="create-an-ingress-resource"></a>Skapa en ingress-resurs
I AKS kan du skapa en ingångs resurs med hjälp av NGINX, ett liknande verktyg eller funktionen för att dirigera HTTP-program för HTTP. När du aktiverar HTTP-programroutning för ett AKS-kluster skapar Azure-plattformen ingångs styrenheten och en *extern DNS-* styrenhet. När nya ingångs resurser skapas i Kubernetes skapas de DNS-poster som krävs i en klustrad DNS-zon. 

Mer information finns i [distribuera HTTP-programroutning][aks-http-routing].

### <a name="application-gateway-ingress-controller-agic"></a>Application Gateway inkommande styrenhet (AGIC)

Med AGIC-tillägget (Application Gateway ingress Controller) kan AKS-kunder använda Azures interna Application Gateway nivå 7-belastningsutjämnare för att exponera moln program vara till Internet. AGIC övervakar Kubernetes-klustret och uppdaterar kontinuerligt en Application Gateway, exponerar valda tjänster på Internet. 

Mer information om AGIC-tillägget för AKS finns i [Vad är Application Gateway ingress-kontrollant?][agic-overview].

### <a name="ssltls-termination"></a>SSL/TLS-terminering

SSL/TLS-avslutning är en annan vanlig funktion i ingress. I stora webb program som nås via HTTPS hanterar ingångs resursen TLS-avslutning i stället för själva programmet. För att tillhandahålla automatisk generering och konfiguration av TLS-certifiering kan du konfigurera ingångs resursen för att använda providrar som "att kryptera". 

Mer information om hur du konfigurerar en NGINX ingress-styrenhet med att kryptera finns i [ingress och TLS][aks-ingress-tls].

### <a name="client-source-ip-preservation"></a>IP-konservering för klient källa

Konfigurera ingångs styrenheten för att bevara klientens käll-IP på begär anden till behållare i ditt AKS-kluster. När din ingångs kontroll dirigerar en klients begäran till en behållare i AKS-klustret, är den ursprungliga käll-IP: en för den begäran inte tillgänglig för mål behållaren. När du aktiverar *IP-konservering för klient källa* är käll-IP: en för klienten tillgänglig i begär ande huvudet under *X-forwarded-for*. 

Om du använder IP-konservering för klient källa på din ingångs kontroll kan du inte använda TLS-vidarekoppling. Klient Källans IP-konservering och TLS-vidarekoppling kan användas med andra tjänster, till exempel *Loadbalancer* -typen.

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En nätverks säkerhets grupp filtrerar trafik för virtuella datorer som AKS-noderna. När du skapar tjänster, till exempel en LoadBalancer, konfigurerar Azure-plattformen automatiskt eventuella nödvändiga regler för nätverks säkerhets grupper. 

Du behöver inte konfigurera regler för nätverks säkerhets grupper manuellt för att filtrera trafik för poddar i ett AKS-kluster. Definiera bara de portar och vidarebefordran som krävs som en del av dina Kubernetes-tjänst manifest. Låt Azure-plattformen skapa eller uppdatera rätt regler. 

Du kan också använda nätverks principer för att automatiskt tillämpa trafik filter regler på poddar.

## <a name="network-policies"></a>Nätverks principer

Som standard kan alla poddar i ett AKS-kluster skicka och ta emot trafik utan begränsningar. För förbättrad säkerhet måste du definiera regler som styr flödet av trafik, till exempel:
* Backend-program exponeras endast för nödvändiga frontend-tjänster. 
* Databas komponenter är bara tillgängliga för de program nivåer som ansluter till dem.

Nätverks principen är en Kubernetes-funktion som är tillgänglig i AKS som låter dig styra trafikflödet mellan poddar. Du tillåter eller nekar trafik till Pod baserat på inställningar som tilldelad etikett, namnrymd eller trafik port. Nätverks säkerhets grupper är bättre för AKS-noder, men nätverks principer är ett mer lämpligt, molnet sätt att styra trafik flödet för poddar. Allteftersom poddar skapas dynamiskt i ett AKS-kluster kan nödvändiga nätverks principer tillämpas automatiskt.

Mer information finns i [skydda trafik mellan poddar med hjälp av nätverks principer i Azure Kubernetes service (AKS)][use-network-policies].

## <a name="next-steps"></a>Nästa steg

Kom igång med AKS-nätverk genom att skapa och konfigurera ett AKS-kluster med dina egna IP-adressintervall med hjälp av [Kubernetes][aks-configure-kubenet-networking] eller [Azure cni][aks-configure-advanced-networking].

För associerade metod tips, se [metod tips för nätverks anslutning och säkerhet i AKS][operator-best-practices-network].

Mer information om kärn Kubernetes-och AKS-koncept finns i följande artiklar:

- [Kubernetes/AKS-kluster och arbets belastningar][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-åtkomst och identitet][aks-concepts-identity]
- [Kubernetes/AKS-säkerhet][aks-concepts-security]
- [Kubernetes/AKS-lagring][aks-concepts-storage]
- [Kubernetes/AKS-skala][aks-concepts-scale]

<!-- IMAGES -->
[aks-clusterip]: ./media/concepts-network/aks-clusterip.png
[aks-nodeport]: ./media/concepts-network/aks-nodeport.png
[aks-loadbalancer]: ./media/concepts-network/aks-loadbalancer.png
[advanced-networking-diagram]: ./media/concepts-network/advanced-networking-diagram.png
[aks-ingress]: ./media/concepts-network/aks-ingress.png

<!-- LINKS - External -->
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[aks-http-routing]: http-application-routing.md
[aks-ingress-tls]: ./ingress-tls.md
[aks-configure-kubenet-networking]: configure-kubenet.md
[aks-configure-advanced-networking]: configure-azure-cni.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[agic-overview]: ../application-gateway/ingress-controller-overview.md
[use-network-policies]: use-network-policies.md
[operator-best-practices-network]: operator-best-practices-network.md
[support-policies]: support-policies.md
