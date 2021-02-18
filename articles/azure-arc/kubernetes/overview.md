---
title: Översikt över Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Den här artikeln innehåller en översikt över Azure Arc-aktiverade Kubernetes.
keywords: Kubernetes, båge, Azure, behållare
ms.custom: references_regions
ms.openlocfilehash: 3d96c8c8764db89501da6fb9c498f0a3d20461af
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652538"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Vad är Azure Arc-aktiverade Kubernetes?

Med Azure Arc aktiverat Kubernetes kan du ansluta och konfigurera Kubernetes-kluster som finns antingen i eller utanför Azure. När du ansluter ett Kubernetes-kluster till Azure-bågen kommer det att:
* Visas i Azure Portal med ett Azure Resource Manager-ID och en hanterad identitet. 
* Bifogas till vanliga Azure-prenumerationer.
* Placeras i en resurs grupp.
* Ta emot Taggar precis som andra Azure-resurser. 

För att ansluta ett Kubernetes-kluster till Azure måste kluster administratören distribuera agenter. Dessa agenter:
* Kör i `azure-arc` Kubernetes-namnområdet som standard distributioner av Kubernetes.
* Hantera anslutning till Azure.
* Samla in Azure-båg loggar och mått.
* Titta efter konfigurations begär Anden. 

Azure Arc Enabled Kubernetes har stöd för branschstandardiserade SSL för att skydda data under överföring. Dessa data lagras krypterade och i vila i en Azure Cosmos DB Database för att säkerställa data sekretess.
 
## <a name="supported-kubernetes-distributions"></a>Kubernetes-distributioner som stöds

Azure Arc-aktiverade Kubernetes fungerar med alla CNCF-certifierade (Cloud Native Computing Foundation) Kubernetes-kluster, till exempel:
* AKS-motor på Azure
* AKS-motor på Azure Stack hubb
* GKE
* EKS
* VMware vSphere

Azure Arc-aktiverade Kubernetes-funktioner har testats av Arc-teamet vid följande distributioner:
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

* Distribuera program och tillämpa konfigurationen med GitOps konfigurations hantering. 

* Visa och övervaka kluster med hjälp av Azure Monitor för behållare. 

* Tillämpa principer med Azure Policy för Kubernetes. 

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Regioner som stöds 

Azure Arc-aktiverade Kubernetes stöds för närvarande i följande regioner: 

* East US 
* Europa, västra

## <a name="next-steps"></a>Nästa steg

* [Anslut ett kluster](./connect-cluster.md)
