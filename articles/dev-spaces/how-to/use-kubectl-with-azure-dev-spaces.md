---
title: Använda kubectl med Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: Lär dig hur du använder kubectl-kommandon inom ett dev-utrymme på ett Azure Kubernetes service-kluster med Azure dev-utrymmen aktiverade
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes service, Containers, Helm, service nät, service nät-routning, kubectl, K8s '
ms.openlocfilehash: e6f79d98cf209d1bc19753f19c9b17b06017c2b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960176"
---
# <a name="use-kubectl-with-an-azure-dev-space"></a>Använda kubectl med ett Azure dev-utrymme

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

Du kan komma åt Kubernetes-klustret i ett Azure dev-utrymme och använda befintliga Kubernetes-verktyg som `kubectl` .

`az aks use-dev-spaces`Om du kör kommandot läggs automatiskt en `kubectl` konfigurations kontext åt dig, så kubectl bör redan vara ansluten till ditt Azure dev Spaces Kubernetes-kluster. Exempel:
- Bekräfta den aktuella kontexten: `kubectl config current-context`
- Lista alla tillgängliga kontexter: `kubectl config get-contexts` . 
- Ändrings kontext: `kubectl config use-context <context-name>`
- Visa Kubernetes-instrumentpanelen: kör `kubectl proxy` och öppna sedan webbläsaren till den adress som kommandot avger (Lägg till i `/ui` URL: en för att navigera till Kubernetes-instrumentpanelen).
- Visa en lista med de tjänster som körs i standard utrymmet för Azure dev Spaces som *standard*: `kubectl get services --namespace=default`

