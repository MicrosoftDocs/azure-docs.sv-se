---
title: Översikt över Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: overview
author: mlearned
ms.author: mlearned
description: Den här artikeln innehåller en översikt över Azure Arc-aktiverade Kubernetes.
keywords: Kubernetes, båge, Azure, behållare
ms.custom: references_regions
ms.openlocfilehash: 42a04bb349b2acbd68c7088bc0348deda1ee09e1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101652276"
---
# <a name="what-is-azure-arc-enabled-kubernetes"></a>Vad är Azure Arc-aktiverade Kubernetes?

Med Azure Arc aktiverat Kubernetes kan du ansluta och konfigurera Kubernetes-kluster som finns antingen i eller utanför Azure. När du ansluter ett Kubernetes-kluster till Azure-bågen kommer det att:
* Visas i Azure Portal med ett Azure Resource Manager-ID och en hanterad identitet. 
* Placeras i en Azure-prenumeration och resurs grupp.
* Ta emot Taggar precis som andra Azure-resurser. 

För att ansluta ett Kubernetes-kluster till Azure måste kluster administratören distribuera agenter. Dessa agenter:
* Kör i `azure-arc` Kubernetes-namnområdet som standard distributioner av Kubernetes.
* Hantera anslutning till Azure.
* Samla in Azure-båg loggar och mått.
* Titta efter konfigurations begär Anden. 

Azure Arc Enabled Kubernetes har stöd för branschstandardiserade SSL för att skydda data under överföring. Dessutom lagras data som krypteras i vila i en Azure Cosmos DB databas för att säkerställa data sekretessen.

## <a name="supported-kubernetes-distributions"></a>Kubernetes-distributioner som stöds

Azure Arc-aktiverade Kubernetes fungerar med alla CNCF-certifierade (Cloud Native Computing Foundation) Kubernetes-kluster. Azure båg-teamet har arbetat med [viktiga bransch partner för att verifiera att](./validation-program.md) deras Kubernetes-distributioner har Aktiver ATS med Azure Arc-aktiverad Kubernetes.

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
* USA, västra centrala
* USA, södra centrala
* Sydostasien
* Storbritannien, södra
* USA, västra 2
* Australien, östra
* USA, östra 2
* Europa, norra

## <a name="next-steps"></a>Nästa steg

* [Ansluta ett kluster till Azure-bågen](./quickstart-connect-cluster.md)
