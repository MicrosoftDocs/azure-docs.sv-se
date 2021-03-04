---
title: Konfigurationer och GitOps – Azure Arc-aktiverad Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en översikt över GitOps-och konfigurations funktionerna i Azure Arc Enabled Kubernetes.
keywords: Kubernetes, båge, Azure, behållare, konfiguration, GitOps
ms.openlocfilehash: 88a30876b25730e4cb0b4b1e19fac94b9e556adc
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121804"
---
# <a name="configurations-and-gitops-with-azure-arc-enabled-kubernetes"></a>Konfigurationer och GitOps med Azure Arc-aktiverade Kubernetes

I förhållande till Kubernetes är GitOps en metod för att deklarera det önskade läget för Kubernetes (distributioner, namnrymder osv.) på en git-lagringsplats. Denna deklaration följs av en avsöknings-och pull-baserad distribution av dessa klusterkonfigurationer med hjälp av en operatör. Git-lagringsplatsen kan innehålla:
* YAML-format manifest som beskriver eventuella giltiga Kubernetes-resurser, inklusive namnrymder, ConfigMaps, distributioner, DaemonSets osv.
* Helm-diagram för att distribuera program.

Flow, ett populärt verktyg med öppen källkod i GitOps- [utrymmet, kan](https://docs.fluxcd.io/)distribueras i Kubernetes-klustret för att under lätta flödet av konfigurationer från en git-lagringsplats till ett Kubernetes-kluster. Flöde stöder distribution av sin operatör både i klustret och namn områdes omfånget. En flödes operator som distribueras med namn områdes omfånget kan bara distribuera Kubernetes-objekt inom den angivna namn rymden. Möjligheten att välja mellan kluster-eller namn områdes omfånget hjälper dig att nå distributions mönster för flera innehavare i samma Kubernetes-kluster.

## <a name="configurations"></a>Konfigurationer

[![Arkitektur ](./media/conceptual-configurations.png) för konfigurationer](./media/conceptual-configurations.png#lightbox)

Anslutningen mellan klustret och git-lagringsplatsen skapas som en konfigurations resurs ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` ) ovanpå den Azure Arc-aktiverade Kubernetes-resursen (representeras av `Microsoft.Kubernetes/connectedClusters` ) i Azure Resource Manager. 

Konfigurations resursens egenskaper används för att distribuera flödes operatorn i klustret med lämpliga parametrar, till exempel git-lagrings platsen som hämtar manifest och avsöknings intervall för att hämta dem. Konfigurations resurs data lagras krypterade i vila i en Azure Cosmos DB databas för att säkerställa data sekretess.

Den som `config-agent` körs i klustret ansvarar för:
* Spåra nya eller uppdaterade konfigurations resurser på den Azure Arc-aktiverade Kubernetes-resursen.
* Distribuera en flödes operatör för att se git-lagringsplatsen för varje konfigurations resurs.
* Tillämpar alla uppdateringar som gjorts på alla konfigurations resurser. 

Du kan skapa flera namn rymds konfigurations resurser i samma Azure Arc-Kubernetes-kluster för att uppnå flera innehavare.

> [!NOTE]
> * `config-agent` Övervakare för nya eller uppdaterade konfigurations resurser som ska vara tillgängliga på den Arc-aktiverade Kubernetes-resursen. Därför kräver agenter anslutning för det önskade läget för att kunna hämtas till klustret. Om agenter inte kan ansluta till Azure finns det en fördröjning i att sprida det önskade läget till klustret.
> * Känsliga kund uppgifter som privat nyckel, kända värdar innehåll, HTTPS-användarnamn och token/Password lagras inte i mer än 48 timmar i Azure Arc-aktiverade Kubernetes-tjänster. Om du använder känsliga indata för konfigurationer ska du ta klustret online så regelbundet som möjligt.

## <a name="apply-configurations-at-scale"></a>Använd konfigurationer i skala

Eftersom Azure Resource Manager hanterar dina konfigurationer, kan du automatisera skapandet av samma konfiguration i alla Azure Arc-aktiverade Kubernetes-resurser med hjälp av Azure Policy, inom en prenumerations omfattning eller en resurs grupp. 

Den här funktionen garanterar att en gemensam bas linje konfiguration (som innehåller konfigurationer som ClusterRoleBindings, RoleBindings och NetworkPolicy) kan användas i hela flottan eller inventering av Azure Arc-aktiverade Kubernetes-kluster.

## <a name="next-steps"></a>Nästa steg

* Gå igenom vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./connect-cluster.md).
* Har du redan ett Kubernetes-kluster anslutet Azure Arc? [Skapa konfigurationer på ditt Arc-aktiverade Kubernetes-kluster](./use-gitops-connected-cluster.md).
* Lär dig hur du [använder Azure policy för att tillämpa konfigurationer i stor skala](./use-azure-policy.md).