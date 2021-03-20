---
title: Översikt över Linkerd
description: Få en översikt över Linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77593775"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Översikt

[Linkerd][linkerd] är ett enkelt sätt att använda och lätta service nät.

## <a name="architecture"></a>Arkitektur

Linkerd tillhandahåller ett data plan som består av Ultralight [Linkerd][linkerd-proxy] specialiserad proxy-sidvagn. Dessa intelligenta proxyservrar styr all nätverks trafik in i och ut ur dina nätappar och arbets belastningar. Proxyservrarna visar också Mät värden via [Prometheus][prometheus] -slutpunkter.

Kontroll planet hanterar konfigurationen och aggregerad telemetri via följande [komponenter][linkerd-architecture]:

- **Controller** – tillhandahåller API som driver Linkerd CLI och instrument panel. Innehåller konfiguration för proxyservrar.

- **Knacka** för att bestämma real tids arm på begär Anden och svar.

- **Identitet** – tillhandahåller identitets-och säkerhets funktioner som tillåter mTLS mellan tjänster.

- **Webb** – tillhandahåller Linkerd-instrumentpanelen.


Följande arkitektur diagram visar hur de olika komponenterna i data planet och kontroll planet interagerar.


![Översikt över Linkerd-komponenter och arkitektur.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Urvals villkor

Det är viktigt att förstå och ta hänsyn till följande områden när du utvärderar Linkerd för dina arbets belastningar:

- [Design principer](#design-principles)
- [Funktioner](#capabilities)
- [Scenarier](#scenarios)


### <a name="design-principles"></a>Designprinciper

Följande design principer [vägleder][design-principles] Linkerd-projektet:

- **Håll det enkelt** – måste vara lätt att använda och förstå.

- **Minimera resurs krav** – ge minimala prestanda-och resurs kostnader.

- Du behöver **bara** avbryta befintliga program och inte kräva komplex konfiguration.


### <a name="capabilities"></a>Funktioner

Linkerd tillhandahåller följande funktioner:

- **Nät** – skapat fel söknings alternativ

- **Trafik hantering** – delning, timeout, återförsök, ingångar

- **Säkerhet** – kryptering (mTLS), certifikat som roteras automatisk var 24: e timme

- **Iakttagithet** – gyllene mått, tryck, spårning, tjänst profiler och linje mått för varje flöde, webb instrument panel med topologier för topologi, Prometheus, Grafana


### <a name="scenarios"></a>Scenarier

Linkerd passar bra för och föreslås för följande scenarier:

- Enkelt att använda med bara den viktigaste uppsättningen kapacitets krav

- Låg latens, låg belastning, med fokus på observerad och enkel trafik hantering


## <a name="next-steps"></a>Nästa steg

I följande dokumentation beskrivs hur du kan installera Linkerd på Azure Kubernetes service (AKS):

> [!div class="nextstepaction"]
> [Installera Linkerd i Azure Kubernetes service (AKS)][linkerd-install]

Du kan också ytterligare utforska Linkerd-funktioner och arkitektur:

- [Linkerd-funktioner][linkerd-features]
- [Linkerd-arkitektur][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md