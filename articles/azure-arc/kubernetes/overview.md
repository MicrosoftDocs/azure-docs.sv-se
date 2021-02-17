---
title: Översikt över Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Den här artikeln innehåller en översikt över Azure Arc-aktiverade Kubernetes.
keywords: Kubernetes, båge, Azure, behållare
ms.custom: references_regions
ms.openlocfilehash: 54b439d73ed5dfb0709d33dea1f588b8bdf4489f
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100560276"
---
# <a name="what-is-azure-arc-enabled-kubernetes-preview"></a>Vad är Azure Arc-aktiverat Kubernetes (förhandsversion)?

Du kan ansluta och konfigurera Kubernetes-kluster i eller utanför Azure med hjälp av Azure Arc Enabled Kubernetes Preview. När ett Kubernetes-kluster är kopplat till Azure-bågen visas det i Azure Portal. Det kommer att ha ett Azure Resource Manager-ID och en hanterad identitet. Kluster är anslutna till Azures standard prenumerationer, finns i en resurs grupp och kan ta emot Taggar precis som andra Azure-resurser. 

För att ansluta ett Kubernetes-kluster till Azure måste kluster administratören distribuera agenter. Dessa agenter körs i ett Kubernetes-namnområde med namnet `azure-arc` och är vanliga Kubernetes-distributioner. Agenterna ansvarar för anslutning till Azure, samlar in Azure Arc-loggar och-mått och tittar efter konfigurations begär Anden. 

Azure Arc Enabled Kubernetes har stöd för branschstandardiserade SSL för att skydda data under överföring. Dessutom lagras data som krypteras i vila i en Azure Cosmos DB databas för att säkerställa data sekretessen.
 
> [!NOTE]
> Azure Arc-aktiverade Kubernetes är en för hands version. Vi rekommenderar det inte för produktions arbets belastningar.

## <a name="supported-kubernetes-distributions"></a>Kubernetes-distributioner som stöds

Azure Arc-aktiverade Kubernetes fungerar med alla CNCF-certifierade (Cloud Native Computing Foundation) Kubernetes-kluster som AKS-motor på Azure, AKS-Engine på Azure Stack Hub, GKE, EKS och VMware vSphere-kluster.

Azure Arc-aktiverade Kubernetes-funktioner har testats av Arc-teamet på följande distributioner:
* RedHat OpenShift 4,3
* Rancher RKE 1.0.8
* Kanoniskt snabb Kubernetes 1,18
* AKS-motor
* AKS-motor på Azure Stack hubb
* AKS på Azure Stack HCI
* Kluster-API-Provider Azure

## <a name="supported-scenarios"></a>Scenarier som stöds 

Azure Arc-aktiverade Kubernetes har stöd för följande scenarier: 

* Anslut Kubernetes som körs utanför Azure för inventering, gruppering och taggning.

* Distribuera program och tillämpa konfigurationen med hjälp av GitOps konfigurations hantering. 

* Använd Azure Monitor för behållare för att visa och övervaka dina kluster. 

* Tillämpa principer med Azure Policy för Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regioner som stöds 

Azure Arc-aktiverade Kubernetes stöds för närvarande i följande regioner: 

* East US 
* Europa, västra

## <a name="next-steps"></a>Nästa steg

* [Anslut ett kluster](./connect-cluster.md)
