---
title: Vad är Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Lär dig hur Azure dev Spaces erbjuder en snabb, iterativ Kubernetes utvecklings upplevelse för team i Azure Kubernetes service-kluster
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, kubectl, K8s
manager: gwallace
ms.openlocfilehash: fd6f52c71c81f253f3f40f05408e45b6a6c0dbce
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199483"
---
# <a name="what-is-azure-dev-spaces"></a>Vad är Azure Dev Spaces?

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Med Azure dev Spaces får du en snabb, iterativ Kubernetes utvecklings upplevelse för team i Azure Kubernetes service (AKS)-kluster. Med Azure dev Spaces kan du också felsöka och testa alla komponenter i ditt program i AKS med minimal installation av utvecklings datorer, utan att replikera eller modellera beroenden.

![Diagrammet visar två versioner av ett program som har utvecklats oberoende av varandra. De kombineras sedan till en i en utvecklings miljö för Azure dev Spaces.](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Så förenklas Kubernetes-utveckling av Azure Dev Spaces

Med Azure dev Spaces kan grupper fokusera på utveckling och snabb iteration av deras mikrotjänstprogram genom att låta grupper arbeta direkt med sin hela mikrotjänsters arkitektur eller program som körs i AKS. Azure dev Spaces är också ett sätt att uppdatera delar av mikrotjänsternas arkitektur i isolering utan att påverka resten av AKS-klustret eller andra utvecklare. Azure dev Spaces är för utveckling och testning i utvecklings-och testnings miljöer på lägre nivå och är inte avsedd att köras på produktions AKS-kluster.

Eftersom team kan arbeta med hela programmet och samar beta direkt i AKS, Azure dev Spaces:

* Minimerar installationen av den lokala datorn
* Minskar installations tiden för nya utvecklare i teamet
* Ökar en Teams hastighet genom snabbare upprepning
* Minskar antalet redundanta miljöer för utveckling och integrering eftersom team medlemmar kan dela ett kluster
* Tar bort behovet av att replikera eller modellera beroenden
* Förbättrar samarbetet mellan utvecklings grupper och de team som de arbetar med, till exempel DevOps Teams

Azure dev Spaces innehåller verktyg för att skapa Docker-och Kubernetes-tillgångar för dina projekt. Med det här verktyget kan du enkelt lägga till nya och befintliga program i både ett dev-utrymme och andra AKS-kluster.

Mer information om hur Azure dev Spaces fungerar finns i [så här fungerar Azure dev Spaces och har kon figurer ATS][how-dev-spaces-works].

## <a name="supported-regions-and-configurations"></a>Regioner och konfigurationer som stöds

Azure dev Spaces stöds endast av AKS-kluster i [vissa regioner][supported-regions]. Azure dev Spaces stöder användningen av [Azure CLI](/cli/azure/install-azure-cli) -eller [Visual Studio-koden](https://code.visualstudio.com/download) med [Azure dev Spaces-tillägget](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) installerat på Linux, MacOS eller Windows 8 eller senare för att skapa och köra dina program på AKS. Den har också stöd för att använda [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) installerat i Windows med arbets belastningen Azure Development.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur Azure dev Spaces fungerar.

> [!div class="nextstepaction"]
> [Så här fungerar Azure Dev Spaces](how-dev-spaces-works.md)

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
