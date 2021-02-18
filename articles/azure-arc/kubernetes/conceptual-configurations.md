---
title: Konfigurationer och GitOps – Azure Arc-aktiverad Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en översikt över GitOps-och konfigurations funktionerna i Azure Arc Enabled Kubernetes.
keywords: Kubernetes, båge, Azure, behållare, konfiguration, GitOps
ms.openlocfilehash: f8fe1522eee4cc855ae1f396d9c98323114a25ce
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652555"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfigurationer och GitOps med Azure Arc-aktiverade Kubernetes

I förhållande till Kubernetes är GitOps en metod för att deklarera det önskade läget för Kubernetes (distributioner, namnrymder osv.) på en git-lagringsplats. Denna deklaration följs av en avsöknings-och pull-baserad distribution av dessa klusterkonfigurationer med hjälp av en operatör. Git-lagringsplatsen kan innehålla:
* YAML-format manifest som beskriver eventuella giltiga Kubernetes-resurser, inklusive namnrymder, ConfigMaps, distributioner, DaemonSets osv.
* Helm-diagram för att distribuera program.

Flow, ett populärt verktyg med öppen källkod i GitOps- [utrymmet, kan](https://docs.fluxcd.io/)distribueras i Kubernetes-klustret för att under lätta flödet av konfigurationer från en git-lagringsplats till ett Kubernetes-kluster. Flöde stöder distribution av sin operatör både i klustret och namn områdes omfånget. En flödes operator som distribueras med namn områdes omfånget kan bara distribuera Kubernetes-objekt inom den angivna namn rymden. Möjligheten att välja mellan kluster-eller namn områdes omfånget hjälper dig att nå distributions mönster för flera innehavare i samma Kubernetes-kluster.

## <a name="configurations"></a>Konfigurationer

[![Arkitektur ](./media/conceptual-configurations.png) för konfigurationer](./media/conceptual-configurations.png#lightbox)

Anslutningen mellan klustret och git-lagringsplatsen skapas som en `Microsoft.KubernetesConfiguration/sourceControlConfigurations` tilläggs resurs ovanpå den Azure Arc-aktiverade Kubernetes-resursen (representeras av `Microsoft.Kubernetes/connectedClusters` ) i Azure Resource Manager. 

`sourceControlConfiguration`Resurs egenskaperna används för att distribuera flödes operatorn i klustret med lämpliga parametrar, till exempel den git-lagringsplats från vilken du vill hämta manifest och avsöknings intervall för att hämta dem. `sourceControlConfiguration`Data lagras krypterade och i vila i en Azure Cosmos DB Database för att säkerställa data sekretessen.

Den som `config-agent` körs i klustret ansvarar för:
* Spåra nya eller uppdaterade `sourceControlConfiguration` tilläggs resurser på den Azure Arc-aktiverade Kubernetes-resursen.
* Distribuera en flödes operator för att se git-lagringsplatsen för var och en `sourceControlConfiguration` .
* Tillämpar alla uppdateringar som görs i någon `sourceControlConfiguration` . 

Du kan skapa flera namn rymds `sourceControlConfiguration` resurser i samma Azure Arc-Kubernetes-kluster för att uppnå flera innehavare.

> [!NOTE]
> * `config-agent` övervakar kontinuerligt för nya eller uppdaterade `sourceControlConfiguration` tilläggs resurser som är tillgängliga på den Azure Arc-aktiverade Kubernetes-resursen. Därför kräver agenter en konsekvent anslutning för att hämta önskade tillstånds egenskaper till klustret. Om agenter inte kan ansluta till Azure tillämpas det önskade läget inte i klustret.
> * Känsliga kund uppgifter som privat nyckel, kända värdar innehåll, HTTPS-användarnamn och token eller lösen ord lagras i upp till 48 timmar i Azure Arc-aktiverade Kubernetes-tjänster. Om du använder känsliga indata för konfigurationer ska du ta klustret online så regelbundet som möjligt.

## <a name="apply-configurations-at-scale"></a>Använd konfigurationer i skala

Eftersom Azure Resource Manager hanterar dina konfigurationer, kan du automatisera skapandet av samma konfiguration i alla Azure Arc-aktiverade Kubernetes-resurser med hjälp av Azure Policy, inom en prenumerations omfattning eller en resurs grupp. 

Den här funktionen garanterar att en gemensam bas linje konfiguration (som innehåller konfigurationer som ClusterRoleBindings, RoleBindings och NetworkPolicy) kan användas i hela flottan eller inventering av Azure Arc-aktiverade Kubernetes-kluster.

## <a name="next-steps"></a>Nästa steg

* [Ansluta ett kluster till Azure-bågen](./connect-cluster.md)
* [Skapa konfigurationer på ditt Arc-aktiverade Kubernetes-kluster](./use-gitops-connected-cluster.md)
* [Använd Azure Policy för att tillämpa konfigurationer i skala](./use-azure-policy.md)