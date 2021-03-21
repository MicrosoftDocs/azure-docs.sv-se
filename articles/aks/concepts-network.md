---
title: Koncept – nätverk i Azure Kubernetes Services (AKS)
description: Lär dig mer om nätverk i Azure Kubernetes service (AKS), inklusive Kubernetes och Azure CNI Networking, ingress controllers, belastningsutjämnare och statiska IP-adresser.
ms.topic: conceptual
ms.date: 06/11/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: edb195fae2e05a1f746c10482576f7e0b1bff7c9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88243912"
---
# <a name="network-concepts-for-applications-in-azure-kubernetes-service-aks"></a>Nätverks koncept för program i Azure Kubernetes service (AKS)

I en container-baserad mikrotjänster-metod för program utveckling måste program komponenterna arbeta tillsammans för att bearbeta sina uppgifter. Kubernetes tillhandahåller olika resurser som möjliggör den här program kommunikationen. Du kan ansluta till och exponera program internt eller externt. Om du vill bygga program med hög tillgänglighet kan du belastningsutjämna dina program. Mer komplexa program kan kräva konfiguration av inkommande trafik för SSL/TLS-avslutning eller routning av flera komponenter. Av säkerhets skäl kan du också behöva begränsa flödet av nätverks trafik till eller mellan poddar och noder.

I den här artikeln beskrivs de grundläggande koncepten som ger nätverk till dina program i AKS:

- [Tjänster](#services)
- [Virtuella Azure-nätverk](#azure-virtual-networks)
- [Ingresskontrollanter](#ingress-controllers)
- [Nätverks principer](#network-policies)

## <a name="kubernetes-basics"></a>Grundläggande om Kubernetes

För att tillåta åtkomst till dina program, eller för att program komponenter ska kunna kommunicera med varandra, tillhandahåller Kubernetes ett abstraktions lager för virtuella nätverk. Kubernetes-noder är anslutna till ett virtuellt nätverk och kan tillhandahålla inkommande och utgående anslutning för poddar. Komponenten *Kube-proxy* körs på varje nod för att tillhandahålla de här nätverks funktionerna.

I Kubernetes grupperar *tjänster* logiskt poddar för att tillåta direkt åtkomst via en IP-adress eller DNS-namn och på en speciell port. Du kan också distribuera trafik med hjälp av en *belastningsutjämnare*. Mer komplex routning av program trafik kan också uppnås med ingångs *enheter*. Säkerhet och filtrering av nätverks trafik för poddar är möjlig med Kubernetes- *nätverks principer*.

Azure-plattformen hjälper också till att förenkla virtuella nätverk för AKS-kluster. När du skapar en Kubernetes-belastningsutjämnare skapas och konfigureras den underliggande Azure Load Balancer-resursen. När du öppnar nätverks portar till poddar konfigureras motsvarande regler för Azure nätverks säkerhets grupper. För HTTP-programroutning kan Azure också konfigurera *extern DNS* eftersom nya ingress-vägar konfigureras.

## <a name="services"></a>Tjänster

För att förenkla nätverks konfigurationen för program arbets belastningar använder Kubernetes *tjänster* för att logiskt gruppera en uppsättning poddar tillsammans och tillhandahålla nätverks anslutning. Följande tjänst typer är tillgängliga:

- **Kluster-IP** – skapar en intern IP-adress som ska användas i AKS-klustret. Lämpar sig för interna program som har stöd för andra arbets belastningar i klustret.

    ![Diagram över kluster-IP-trafikflöde i ett AKS-kluster][aks-clusterip]

- **NodePort** – skapar en port mappning på den underliggande noden som gör att programmet kan nås direkt med nodens IP-adress och port.

    ![Diagram som visar NodePort trafikflöde i ett AKS-kluster][aks-nodeport]

- **Loadbalancer** -skapar en Azure Load Balancer-resurs, konfigurerar en extern IP-adress och ansluter de begärda poddar till backend-poolen för belastningsutjämnare. Om du vill tillåta att kunders trafik når programmet skapas belastnings Utjämnings regler på önskade portar. 

    ![Diagram som visar Load Balancer trafikflöde i ett AKS-kluster][aks-loadbalancer]

    För ytterligare kontroll och routning av inkommande trafik kan du i stället använda en ingångs [kontroll](#ingress-controllers).

- **ExternalName** – skapar en specifik DNS-post för enklare program åtkomst.

IP-adressen för belastningsutjämnare och tjänster kan tilldelas dynamiskt, eller så kan du ange en befintlig statisk IP-adress som ska användas. Både interna och externa statiska IP-adresser kan tilldelas. Den här befintliga statiska IP-adressen är ofta kopplad till en DNS-post.

Både *interna* och *externa* belastningsutjämnare kan skapas. Interna belastningsutjämnare tilldelas bara en privat IP-adress, så de kan inte nås från Internet.

## <a name="azure-virtual-networks"></a>Virtuella Azure-nätverk

I AKS kan du distribuera ett kluster som använder någon av följande två nätverksmodeller:

- *Kubernetes* -nätverk – nätverks resurserna skapas och konfigureras vanligt vis när AKS-klustret distribueras.
- Nätverk för *Azure Container Network Interface (cni)* – AKS-klustret är anslutet till befintliga virtuella nätverks resurser och konfigurationer.

### <a name="kubenet-basic-networking"></a>Kubernetes (grundläggande) nätverk

Alternativet *Kubernetes* Networking är standard konfigurationen för skapande av AKS-kluster. Med *Kubernetes* hämtar noder en IP-adress från det virtuella nätverkets undernät i Azure. Poddar får en IP-adress från ett annat logiskt adressutrymme än det virtuella Azure-nätverkets undernät för noderna. NAT (Network Address Translation) konfigureras sedan så att poddarna kan komma åt resurser i det virtuella Azure-nätverket. Käll-IP-adressen för trafiken är NAT till nodens primära IP-adress.

Noder använder plugin-programmet [Kubernetes][kubenet] Kubernetes. Du kan låta Azure-plattformen skapa och konfigurera de virtuella nätverken åt dig, eller välja att distribuera ditt AKS-kluster till ett befintligt undernät för virtuella nätverk. Återigen får bara noderna en IP-adress med IP-adresser och poddar använder NAT för att kommunicera med andra resurser utanför AKS-klustret. Den här metoden minskar antalet IP-adresser som du behöver reservera i ditt nätverks utrymme för att poddar ska kunna användas.

Mer information finns i [Konfigurera Kubernetes-nätverk för ett AKS-kluster][aks-configure-kubenet-networking].

### <a name="azure-cni-advanced-networking"></a>Azure CNI (avancerat) nätverk

Med Azure CNI får varje podd en IP-adress från undernätet och kan nås direkt. De här IP-adresserna måste vara unika i ditt nätverks utrymme och måste planeras i förväg. Varje nod har en konfigurations parameter för det maximala antalet poddar som stöds. Motsvarande antal IP-adresser per nod är sedan reserverade för den noden. Den här metoden kräver mer planering, som annars kan leda till IP-adressundernät eller behöver återskapa kluster i ett större undernät när ditt program kräver större belastning.

Till skillnad från Kubernetes är trafik till slut punkter i samma virtuella nätverk inte NAT till nodens primära IP. Käll adressen för trafik i det virtuella nätverket är Pod IP. Trafik som är extern för det virtuella nätverket fortfarande NAT till nodens primära IP.

Noder använder Kubernetes [-plugin-programmet för Azure Container Network Interface (cni)][cni-networking] .

![Diagram som visar två noder med bryggor som ansluter var och en till ett enda Azure VNet][advanced-networking-diagram]

Mer information finns i [Konfigurera Azure-cni för ett AKS-kluster][aks-configure-advanced-networking].

### <a name="compare-network-models"></a>Jämför nätverks modeller

Både Kubernetes och Azure CNI ger nätverks anslutning till dina AKS-kluster. Det finns dock fördelar och nack delar med var och en. På hög nivå gäller följande aspekter:

* **Kubernetes**
    * Bevarar IP-adressutrymmet.
    * Använder intern eller extern belastningsutjämnare i Kubernetes för att uppnå poddar utanför klustret.
    * Du måste manuellt hantera och underhålla användardefinierade vägar (UDR).
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

För DNS erbjuds både Kubernetes-och Azure CNI-plugin-program DNS av CoreDNS, en distribution som körs i AKS med en egen autoskalning. Mer information om CoreDNS i Kubernetes finns i [Anpassa DNS-tjänsten](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/). CoreDNS konfigureras per standard för att vidarebefordra okända domäner till nodens DNS-servrar, med andra ord, till DNS-funktionerna i Azure-Virtual Network där AKS-klustret distribueras. Därför kommer Azure DNS och privata zoner att fungera för poddar som körs i AKS.

### <a name="support-scope-between-network-models"></a>Support omfattning mellan nätverks modeller

Oavsett vilken nätverks modell du använder kan både Kubernetes och Azure CNI distribueras på något av följande sätt:

* Azure-plattformen kan automatiskt skapa och konfigurera virtuella nätverks resurser när du skapar ett AKS-kluster.
* Du kan manuellt skapa och konfigurera virtuella nätverks resurser och koppla dem till resurserna när du skapar ditt AKS-kluster.

Även om funktioner som tjänst slut punkter eller UDR stöds med både Kubernetes och Azure CNI, definierar [support principerna för AKS][support-policies] vilka ändringar du kan göra. Exempel:

* Om du manuellt skapar de virtuella nätverks resurserna för ett AKS-kluster, kan du konfigurera dina egna UDR-eller tjänst slut punkter.
* Om Azure-plattformen automatiskt skapar de virtuella nätverks resurserna för ditt AKS-kluster, stöds det inte för att manuellt ändra de AKS-hanterade resurserna för att konfigurera dina egna UDR-eller tjänst slut punkter.

## <a name="ingress-controllers"></a>Ingresskontrollanter

När du skapar en LoadBalancer-typ tjänst skapas en underliggande resurs för Azure Load Balancer. Belastningsutjämnaren konfigureras för att distribuera trafik till poddar i din tjänst på en specifik port. LoadBalancer fungerar endast på nivå 4 – tjänsten är inte medveten om de faktiska programmen och kan inte göra några ytterligare Dirigerings överväganden.

*Ingress-kontroller* fungerar på nivå 7 och kan använda mer intelligenta regler för att distribuera program trafik. En vanlig användning av en ingångs kontroll är att dirigera HTTP-trafik till olika program baserat på den inkommande URL: en.

![Diagram över ingress trafikflöde i ett AKS-kluster][aks-ingress]

I AKS kan du skapa en ingress-resurs med hjälp av något som NGINX, eller använda funktionen för att dirigera HTTP-program. När du aktiverar HTTP-programroutning för ett AKS-kluster skapar Azure-plattformen ingångs styrenheten och en *extern DNS-* styrenhet. När nya ingångs resurser skapas i Kubernetes skapas de DNS-poster som krävs i en klustrad DNS-zon. Mer information finns i [distribuera HTTP-programroutning][aks-http-routing].

Med tillägget Application Gateway ingress Controller (AGIC) kan AKS-kunder använda Azures interna Application Gateway nivå 7-belastningsutjämnare för att exponera moln program vara till Internet. AGIC övervakar det Kubernetes-kluster som det finns på och uppdaterar kontinuerligt en Application Gateway, så att valda tjänster exponeras för Internet. Mer information om AGIC-tillägget för AKS finns i [Vad är Application Gateway ingress-kontrollant?][agic-overview]

En annan vanlig funktion i ingress är SSL/TLS-avslutning. I stora webb program som nås via HTTPS kan TLS-terminering hanteras av den ingående resursen i stället för i själva programmet. För att tillhandahålla automatisk generering och konfiguration av TLS-certifiering kan du konfigurera ingångs resursen så att leverantörer kan använda providers, till exempel för att kryptera. Mer information om hur du konfigurerar en NGINX ingress-styrenhet med att kryptera finns i [ingress och TLS][aks-ingress-tls].

Du kan också konfigurera ingångs styrenheten för att bevara klientens käll-IP på begär anden till behållare i ditt AKS-kluster. När en klients begäran dirigeras till en behållare i ditt AKS-kluster via din ingångs kontroll, kommer den ursprungliga käll-IP: en för den begäran inte att vara tillgänglig för mål behållaren. När du aktiverar *IP-konservering för klient källa* är käll-IP: en för klienten tillgänglig i begär ande huvudet under *X-forwarded-for*. Om du använder IP-konservering för klient källa på din ingångs kontroll kan du inte använda TLS-vidarekoppling. Klient Källans IP-konservering och TLS-vidarekoppling kan användas med andra tjänster, till exempel *Loadbalancer* -typen.

## <a name="network-security-groups"></a>Nätverkssäkerhetsgrupper

En nätverks säkerhets grupp filtrerar trafik för virtuella datorer, till exempel AKS-noder. När du skapar tjänster, till exempel en LoadBalancer, konfigurerar Azure-plattformen automatiskt eventuella regler för nätverks säkerhets grupper som behövs. Konfigurera inte nätverks säkerhets grupp regler manuellt för att filtrera trafik för poddar i ett AKS-kluster. Definiera nödvändiga portar och vidarebefordran som en del av dina Kubernetes-tjänst manifest och låt Azure-plattformen skapa eller uppdatera lämpliga regler. Du kan också använda nätverks principer, enligt beskrivningen i nästa avsnitt, för att automatiskt tillämpa trafik filter regler på poddar.

## <a name="network-policies"></a>Nätverks principer

Som standard kan alla poddar i ett AKS-kluster skicka och ta emot trafik utan begränsningar. För ökad säkerhet kan du vilja definiera regler som styr flödet av trafik. Backend-program exponeras ofta bara för nödvändiga frontend-tjänster, eller databas komponenter är bara tillgängliga för de program nivåer som ansluter till dem.

Nätverks principen är en Kubernetes-funktion som är tillgänglig i AKS som låter dig styra trafikflödet mellan poddar. Du kan välja att tillåta eller neka trafik baserat på inställningar som tilldelad etikett, namnrymd eller trafik port. Nätverks säkerhets grupper är mer för AKS-noderna, inte poddar. Användningen av nätverks principer är ett mer lämpligt, moln sätt att styra trafik flödet. Allteftersom poddar skapas dynamiskt i ett AKS-kluster kan de nödvändiga nätverks principerna tillämpas automatiskt.

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
