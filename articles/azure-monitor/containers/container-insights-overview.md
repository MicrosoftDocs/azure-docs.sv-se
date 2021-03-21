---
title: Översikt över container Insights | Microsoft Docs
description: Den här artikeln beskriver behållar insikter som övervakar AKS container Insights-lösning och det värde den ger genom att övervaka hälso tillståndet för dina AKS-kluster och Container Instances i Azure.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: a29890f569c47c48aae7d5d61badee7edeef58a0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441524"
---
# <a name="container-insights-overview"></a>Översikt över container Insights

Container Insights är en funktion som har utformats för att övervaka prestanda för behållar arbets belastningar som distribueras till:

- Managed Kubernetes kluster som finns i [Azure Kubernetes service (AKS)](../../aks/intro-kubernetes.md)
- Självhanterade Kubernetes-kluster som finns i Azure med hjälp av [AKS-motorn](https://github.com/Azure/aks-engine)
- [Azure Container Instances](../../container-instances/container-instances-overview.md)
- Självhanterade Kubernetes-kluster som finns på [Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview) eller lokalt
- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)
- [Azure Arc Enabled-Kubernetes](../../azure-arc/kubernetes/overview.md) (för hands version)

Container Insights stöder kluster som kör operativ systemet Linux och Windows Server 2019. De behållar körningar som stöds är Docker, Moby och alla CRI-kompatibla kör som CRI-O och container.

Övervakning av behållare är avgörande, särskilt när du kör ett produktions kluster, i skala med flera program.

Med behållar insikter får du bättre prestanda genom att samla in minnes-och processor mått från styrenheter, noder och behållare som är tillgängliga i Kubernetes via Metrics-API: et. Containerloggar samlas också in.  När du har aktiverat övervakning från Kubernetes-kluster samlas mått och loggar in automatiskt åt dig via en behållar version av Log Analytics agent för Linux. Måtten skrivs till mått arkivet och loggdata skrivs till logg lagret som är kopplat till din [Log Analytics](../logs/log-query-overview.md) -arbetsyta.

![Arkitektur för container Insights](./media/container-insights-overview/azmon-containers-architecture-01.png)

## <a name="what-does-container-insights-provide"></a>Vad ger behållar insikter?

Behållar insikter ger en omfattande övervaknings upplevelse som använder olika funktioner i Azure Monitor. Med de här funktionerna kan du förstå prestanda och hälsa för ditt Kubernetes-kluster som kör operativ systemet Linux och Windows Server 2019 och behållar arbets belastningarna. Med behållar insikter kan du:

* Identifiera AKS-behållare som körs på noden och deras genomsnittliga processor-och minnes användning. Den här kunskapen kan hjälpa dig att identifiera resurs Flask halsar.
* Identifiera processor-och minnes användning för behållar grupper och deras behållare som finns i Azure Container Instances.
* Identifiera var behållaren finns i en styrenhet eller en pod. Den här kunskapen kan hjälpa dig att visa enhetens eller Pod allmänna prestanda.
* Granska resursutnyttjande för arbets belastningar som körs på värden och som inte är relaterade till de standard processer som stöder pod.
* Förstå klustrets beteende i genomsnitt och tyngsta belastningar. Den här kunskapen kan hjälpa dig att identifiera kapacitets behov och fastställa den maximala belastning som klustret kan hantera.
* Konfigurera aviseringar för att proaktivt meddela dig eller registrera den när processor-och minnes användning på noder eller behållare överskrider tröskelvärdena, eller när en hälso tillstånds ändring sker i klustret vid en infrastruktur eller noders hälso insamling.
* Integrera med [Prometheus](https://prometheus.io/docs/introduction/overview/) för att visa program-och arbets belastnings mått som samlas in från noder och Kubernetes med hjälp av [frågor](container-insights-log-search.md) för att skapa anpassade aviseringar, instrument paneler och detaljerad analys.
* Övervaka arbets belastningar för behållare [som distribueras till AKS-motorn](https://github.com/Azure/aks-engine) lokalt och [AKS-motorn på Azure Stack](/azure-stack/user/azure-stack-kubernetes-aks-engine-overview).
* Övervaka arbets belastningar [för behållare som distribueras till Azure Red Hat OpenShift](../../openshift/intro-openshift.md).

    >[!NOTE]
    >Stöd för Azure Red Hat OpenShift är en funktion i offentlig för hands version för tillfället.
    >

* Övervaka arbets belastningar [för behållare distribuerade till Azure Arc-aktiverade Kubernetes (för hands version)](../../azure-arc/kubernetes/overview.md).

De största skillnaderna vid övervakning av ett Windows Server-kluster jämfört med ett Linux-kluster är följande:

- Det finns inget RSS-minne i Windows och det är därför inte tillgängligt för Windows-noden och behållare. Måttet på [arbets minnet](/windows/win32/memory/working-set) är tillgängligt.
- Information om disk lagrings kapacitet är inte tillgänglig för Windows-noder.
- Endast Pod-miljöer övervakas, inte Docker-miljöer.
- I för hands versionen stöds maximalt 30 Windows Server-behållare. Den här begränsningen gäller inte för Linux-behållare.

Titta på följande videoklipp med en mellanliggande nivå för att hjälpa dig att lära dig att övervaka ditt AKS-kluster med behållar insikter.

> [!VIDEO https://www.youtube.com/embed/XEdwGvS2AwA]

## <a name="how-do-i-access-this-feature"></a>Hur gör jag för att åtkomst till den här funktionen?

Du kan komma åt behållar insikter på två sätt, från Azure Monitor eller direkt från det valda AKS-klustret. Från Azure Monitor har du ett globalt perspektiv för alla behållare som distribueras, som övervakas och som inte är så att du kan söka efter och filtrera i dina prenumerationer och resurs grupper och sedan gå vidare till behållar insikter från den valda behållaren.  Annars kan du komma åt funktionen direkt från en vald AKS-behållare från sidan AKS.

![Översikt över metoder för att komma åt container Insights](./media/container-insights-overview/azmon-containers-experience.png)

Om du är intresse rad av att övervaka och hantera dina Docker-och Windows-behållarobjekt som körs utanför AKS för att Visa konfigurations-, gransknings-och resursutnyttjande, se [lösningen för övervakning av behållare](./containers.md).

## <a name="next-steps"></a>Nästa steg

Om du vill börja övervaka ditt Kubernetes-kluster kan du läsa [så här aktiverar du behållar insikter](container-insights-onboard.md) för att förstå krav och tillgängliga metoder för att aktivera övervakning.
