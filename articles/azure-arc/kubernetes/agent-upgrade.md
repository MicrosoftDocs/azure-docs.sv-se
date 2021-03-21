---
title: Uppgradera Azure Arc-aktiverade Kubernetes-agenter
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Kontroll av agent uppgraderingar för Azure Arc-aktiverade Kubernetes
keywords: Kubernetes, båge, Azure, K8s, behållare, agent, uppgradering
ms.openlocfilehash: 172654f655e594c295a8807b417c32d7849bd4b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121923"
---
# <a name="upgrading-azure-arc-enabled-kubernetes-agents"></a>Uppgradera Azure Arc-aktiverade Kubernetes-agenter

Azure Arc Enabled Kubernetes tillhandahåller funktioner för automatisk uppgradering och manuell uppgradering för agenterna. Om Använd inaktivera automatisk uppgradering och i stället förlitar sig på manuell uppgradering, gäller versions support principen för Arc-agenter och det underliggande Kubernetes-klustret.

## <a name="toggle-auto-upgrade-on-or-off-when-connecting-cluster-to-azure-arc"></a>Växla automatisk uppgradering på eller av när du ansluter klustret till Azure-bågen

Azure Arc-aktiverade Kubernetes tillhandahåller agenter med färdiga funktioner för automatisk uppgradering.

Följande kommando ansluter ett kluster till Azure-bågen med automatisk uppgradering **aktiverat**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

När automatisk uppgradering är aktiverat avsöker agenten Azure varje timme för att ha till gång till en nyare version av agenterna. Om agenten hittar en tillgänglig nyare version, utlöses en Helm-diagram uppgradering för Azure-båg agenterna.

Om du vill inaktivera automatisk uppgradering anger du `--disable-auto-upgrade` parametern när du ansluter klustret till Azure-bågen. Följande kommando ansluter ett kluster till Azure-bågen med automatisk uppgradering **inaktiverat**:

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest --disable-auto-upgrade
```

> [!TIP]
> Om du planerar att inaktivera automatisk uppgradering, se [versions support policy](#version-support-policy) för Azure Arc-aktiverade Kubernetes.

## <a name="toggle-auto-upgrade-onoff-after-connecting-cluster-to-azure-arc"></a>Växla automatisk uppgradering på/av när du har anslutit klustret till Azure-bågen

När du har anslutit ett kluster till Azure-bågen kan du växla mellan funktionen för automatisk uppgradering med `az connectedk8s update` kommandot, som du ser nedan:

```console
az connectedk8s update --name AzureArcTest1 --resource-group AzureArcTest --auto-upgrade false
```

## <a name="manually-upgrade-agents"></a>Uppgradera agenter manuellt

Om du har inaktiverat automatisk uppgradering för agenter kan du manuellt starta uppgraderingar av de här agenterna med hjälp av `az connectedk8s upgrade` kommandot som visas nedan:

```console
az connectedk8s upgrade -g AzureArcTest1 -n AzureArcTest --agent-version 1.0.1
```

Azure Arc Enabled Kubernetes följer det standardiserade [semantiska versions schemat](https://semver.org/) `MAJOR.MINOR.PATCH` för för versions hantering av agenterna. 

Varje tal i versionen indikerar allmän kompatibilitet med den tidigare versionen:

* **Huvud versioner** ändras när det finns inkompatibla API-uppdateringar eller bakåtkompatibla-kompatibiliteten kan vara bruten.
* **Lägre versioner** ändras när funktions förändringar är bakåtkompatibla – kompatibla med andra mindre versioner.
* **Korrigerings versioner** ändras när bakåtkompatibla fel korrigeringar görs.

## <a name="version-support-policy"></a>Versions support princip

När du skapar support ärenden är Azure Arc Enabled Kubernetes-praxis följande princip för versions support:

* Azure Arc-aktiverade Kubernetes-agenter har ett support fönster på "N-2" där "N" är den senaste del versionen av agenterna. 
  * Om till exempel Azure Arc Enabled Kubernetes introducerar 0.28. a idag, versioner 0.28. a, 0.28. b, 0.27. c, 0.27. d, 0.26. e, och 0.26. f stöds av Azure-bågen.

* Kubernetes-kluster som ansluter till Azure-bågen har ett support fönster på "N-2", där "N" är den senaste stabila mindre versionen av [överordnade Kubernetes](https://github.com/kubernetes/kubernetes/releases). 
  * Om Kubernetes till exempel introducerar 1.20. a idag, versionerna 1.20. a, 1.20. b, 1.19. c, 1.19. d, 1.18. e och 1.18. f stöds.

### <a name="how-often-are-minor-version-releases-of-azure-arc-enabled-kubernetes-available"></a>Hur ofta är lägre versioner av Azure Arc-aktiverade Kubernetes tillgängliga?

En lägre version av Azure Arc-aktiverade Kubernetes-agenter lanseras ungefär en gång i månaden.

### <a name="what-happens-if-im-using-an-agent-version-or-a-kubernetes-version-outside-the-official-support-window"></a>Vad händer om jag använder en agent version eller en Kubernetes-version utanför det officiella support fönstret?

Utanför support innebär att de versioner som du kör ligger utanför de "N-2"-versioner som stöds och överordnade Kubernetes-kluster. Om du vill fortsätta med support ärendet uppmanas du att uppgradera klustret och agenterna till en version som stöds.

## <a name="next-steps"></a>Nästa steg

* Gå igenom vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./connect-cluster.md).
* Har du redan ett Kubernetes-kluster anslutet Azure Arc? [Skapa konfigurationer på ditt Arc-aktiverade Kubernetes-kluster](./use-gitops-connected-cluster.md).
* Lär dig hur du [använder Azure policy för att tillämpa konfigurationer i stor skala](./use-azure-policy.md).