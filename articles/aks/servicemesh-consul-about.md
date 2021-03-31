---
title: Översikt över konsulär
description: Få en översikt över konsulär
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "83774008"
---
# <a name="consul"></a>Konsulat

## <a name="overview"></a>Översikt

[Konsulär][consul] är en nätverks lösning med flera data Center som är medveten om att ansluta och säkra tjänster mellan körnings plattformar. [Connect][consul-features] är den komponent som tillhandahåller funktioner för service nät.

## <a name="architecture"></a>Arkitektur

Konsulär tillhandahåller ett data plan som består av [mottagare][envoy-proxy]-baserade [sidvagn][consul-sidecar] som standard. Konsulär har en pluggbar proxy-arkitektur. Dessa intelligenta proxyservrar styr all nätverks trafik in i och ut ur dina nätappar och arbets belastningar.

Kontroll planet hanterar konfigurationen och principen via följande [komponenter][consul-architecture]:

- **Server** – en konsulär agent som körs i server läge och som upprätthåller konsulärt kluster tillstånd.

- **Client** – en konsulär agent som körs i läget för Lightweight-klienter. Varje Compute-nod måste ha en klient agent som kör. Den här klient anordnings konfigurationen och principen mellan arbets belastningarna och konsulär konfiguration. 

Följande arkitektur diagram visar hur de olika komponenterna i data planet och kontroll planet interagerar.

![Översikt över konsulära komponenter och arkitektur.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Urvals villkor

Det är viktigt att förstå och ta hänsyn till följande områden när du bedömer konsulär för dina arbets belastningar:

- [Konsulära principer](#consul-principles)
- [Funktioner](#capabilities)
- [Scenarier](#scenarios)


### <a name="consul-principles"></a>Konsulära principer

Följande principer [vägleder][consul-principles] konsulärt projekt:

- **API-driven** -Codify all konfiguration och princip.

- **Kör och Anslut var som helst** – Anslut arbets belastningar mellan körnings plattformar (Kubernetes, virtuella datorer, utan server).

- **Utöka och integrera** säker anslutning av arbets belastningar i infrastrukturen.


### <a name="capabilities"></a>Funktioner

Konsulär tillhandahåller följande uppsättning funktioner:

- **Nät** – Gateway (flera data Center), virtuella datorer (out-of-Cluster Nodes), service Sync, skapat fel söknings alternativ

- **Proxyservrar** – mottagare, inbyggd proxy, pluggable, L4 proxy tillgänglig för Windows-arbetsbelastningar

- **Trafik hantering** – routning, delning, upplösning

- **Princip** – avsikter, ACL: er

- **Säkerhet** – auktorisering, autentisering, kryptering, SPIFFE-baserade identiteter, extern certifikat utfärdare (valv), certifikat hantering och rotation

- **Iakttagithet** – mått, UI-instrumentpanel, Prometheus, Grafana


### <a name="scenarios"></a>Scenarier

Konsulär är väl lämpad för och föreslås i följande scenarier:

- Utöka befintliga konsulära anslutna arbets belastningar

- Krav på efterlevnad kring certifikat hantering

- Nätverk med flera kluster tjänster

- VM-baserade arbets belastningar som ska ingå i tjänst nätet



## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan installera konsulär på Azure Kubernetes service (AKS):

> [!div class="nextstepaction"]
> [Installera konsulär i Azure Kubernetes service (AKS)][consul-install]

Du kan också utforska konsulära funktioner och arkitektur ytterligare:

- [Konsulär Komma igång själv studie kurser][consul-getting-started]
- [Konsulära funktioner][consul-features]
- [Konsulär arkitektur][consul-architecture]
- [Konsulär – Hur ansluter fungerar][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
