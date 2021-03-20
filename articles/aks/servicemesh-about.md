---
title: Om tjänst nät
description: Få en översikt över tjänst nät, deras arkitektur och funktioner och vilka kriterier du bör tänka på när du väljer en att distribuera.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "77594319"
---
# <a name="about-service-meshes"></a>Om tjänst nät

Ett tjänst nät ger funktioner som trafik hantering, återhämtning, policy, säkerhet, stark identitet och rätt till dina arbets belastningar. Programmet är frikopplat från dessa operativa funktioner och service nätet flyttar dem från program lagret och nedåt till infrastruktur skiktet.

## <a name="scenarios"></a>Scenarier

Detta är några av de scenarier som kan aktive ras för dina arbets belastningar när du använder ett tjänst nät:

- **Kryptera all trafik i kluster** – aktivera ömsesidig TLS mellan angivna tjänster i klustret. Detta kan utökas till att tränga in och ut vid nätverks omkretsen. Är ett säkert alternativ som standard utan några ändringar som behövs för program kod och infrastruktur.

- Distributioner av **Kanarie-och faser** – ange villkor för en delmängd trafik som ska dirigeras till en uppsättning nya tjänster i klustret. Vid lyckad testning av Kanarie-versionen tar du bort villkorlig Routning och fas gradvis ökande% av all trafik till den nya tjänsten. Till sist kommer all trafik att dirigeras till den nya tjänsten.

- **Trafik hantering och manipulering** – skapa en princip för en tjänst som begränsar all trafik till en version av en tjänst från ett visst ursprung. Eller en princip som tillämpar en strategi för återförsök i klasser med fel mellan angivna tjänster. Spegla Live-trafik till nya versioner av tjänster under en migrering eller till fel söknings problem. Injicera fel mellan tjänster i en test miljö för att testa återhämtning.

- **Observera** att du får insikt om hur dina tjänster är anslutna till den trafik som flödar mellan dem. Hämta mått, loggar och spårningar för all trafik i klustret och ingress/utgående. Lägg till distribuerade spårnings möjligheter i dina program.

## <a name="architecture"></a>Arkitektur

Ett service nät består vanligt vis av ett kontroll plan och data planet.

**Kontroll planet** har ett antal komponenter som stöder hantering av tjänst nätet. Detta omfattar vanligt vis ett hanterings gränssnitt som kan vara ett gränssnitt eller ett API. Det finns också normalt komponenter som hanterar regeln och princip definitionerna som definierar hur service nätet ska implementera vissa funktioner. Det finns också komponenter som hanterar säkerhets aspekter som stark identitet och certifikat för mTLS. Service nät har vanligt vis ett mått eller en komponent för en komponent som samlar in och aggregerar mått och telemetri från arbets belastningarna.

**Data planet** består vanligt vis av en proxy som är transparent inmatad som en sidvagn till dina arbets belastningar. Den här proxyn är konfigurerad för att kontrol lera all nätverks trafik i och ut ur Pod som innehåller din arbets belastning. Detta gör att proxyn kan konfigureras för att skydda trafik via mTLS, dynamiskt dirigera trafik, tillämpa principer för trafik och samla in mått och spårnings information. 

![Typisk service nät arkitektur](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Funktioner

Vart och ett av tjänst näten har en naturlig passning och fokuserar på stöd för vissa scenarier, men du kommer normalt att upptäcka att de flesta implementerar ett antal, om inte alla, av följande funktioner.

### <a name="traffic-management"></a>Trafik hantering 

- **Protokoll** – Layer 7 (http, grpc)
- **Dynamisk routning** – villkorlig, viktning, spegling
- **Återhämtning** – timeout, återförsök, krets brytare
- **Princip** – åtkomst kontroll, hastighets begränsningar, kvoter
- **Testar** – fel inmatning

### <a name="security"></a>Säkerhet

- **Kryptering** – mTLS, certifikat hantering, extern ca
- **Stark identitet** – SPIFFE eller liknande
- **Autentisering – autentisering** , auktorisering

### <a name="observability"></a>Överskådlighet

- **Mått** – gyllene mått, Prometheus, Grafana
- **Spårning** – spårning över arbets belastningar
- **Trafik** – kluster, ingress/utgående

### <a name="mesh"></a>Nät

- **Beräknings** -Kubernetes, virtuella datorer som stöds
- **Flera-kluster** – gatewayer, Federation

## <a name="selection-criteria"></a>Urvals villkor

Innan du väljer ett service nät bör du se till att du förstår dina krav och orsakerna till att du installerar ett tjänst nät. Försök att ställa följande frågor.

- **Är en ingångs kontroll tillräckligt för mina behov?** – Ibland räcker det med en funktion som a/b-testning eller trafik delning vid ingångs tiden för att stödja det scenario som krävs. Lägg inte till komplexitet i din miljö utan någon upp.

- **Kan mina arbets belastningar och miljö tolerera ytterligare omkostnader?** – Alla ytterligare komponenter som krävs för att stödja tjänst nätet kräver ytterligare resurser som processor och minne. Dessutom kontrollerar alla proxyservrar och tillhör ande princip en fördröjning i trafiken. Om du har arbets belastningar som är mycket känsliga för svars tider eller inte kan tillhandahålla de ytterligare resurserna för att ta del av service nät komponenterna, kan du överväga att överväga om.

- **Är det här att lägga till ytterligare komplexitet i onödan?** – Om anledningen till att installera ett service nät är att få en funktion som inte nödvändigt vis är kritisk för affärs-eller drift teamen, kan du överväga om den ytterligare komplexiteten vid installation, underhåll och konfiguration är värda.

- **Kan detta antas i en stegvis metod?** – Några av de tjänst nät som ger många funktioner kan antas i en mer stegvis metod. Installera bara de komponenter du behöver för att se till att du lyckas. När du är mer trygg och ytterligare funktioner krävs kan du utforska dem. Gå på och uppmana att installera *allt* från början.

Om du efter noggranna överväganden bestämmer att du behöver ett service nät för att tillhandahålla de funktioner som krävs, är nästa beslut *vilket service nät?*

Tänk på följande områden och vilka av dem som är mest justerade med dina krav. På så sätt får du den bästa anpassningen för din miljö och dina arbets belastningar. Avsnittet [Nästa steg](#next-steps) tar dig till mer detaljerad information om särskilda service nät och hur de mappar till dessa områden.

- Hantering av **teknisk** trafik, policy, säkerhet, observerbarhet

- **Business** -kommersiell support, grund (CNCF), oss-licens, styrning

- **Drift** – installation/uppgraderingar, resurs krav, prestanda krav, integreringar (mått, telemetri, instrument paneler, verktyg, SMI), blandade arbets belastningar (Linux-och Windows Node-pooler), Compute (Kubernetes, Virtual Machines), flera kluster

- **Säkerhet** -auth, identitet, certifikat hantering och rotation, anslutnings Bart extern ca


## <a name="next-steps"></a>Nästa steg

Följande dokumentation innehåller mer information om tjänst nät som du kan prova på Azure Kubernetes service (AKS):

> [!div class="nextstepaction"]
> [Läs mer om Istio...][istio-about]

> [!div class="nextstepaction"]
> [Läs mer om Linkerd...][linkerd-about]

> [!div class="nextstepaction"]
> [Läs mer om konsulär...][consul-about]

Du kanske också vill utforska service nät gränssnitt (SMI), ett standard gränssnitt för service nät på Kubernetes:

- [Service nät gränssnitt (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md