---
title: Använd Azure Policy till klusterkonfigurationer i stor skala (förhandsversion)
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använd Azure Policy för att tillämpa klusterkonfigurationer i stor skala
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.openlocfilehash: 23cd42458c396afd31741c648d713934250a4112
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587790"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale-preview"></a>Använd Azure Policy till klusterkonfigurationer i stor skala (förhandsversion)

## <a name="overview"></a>Översikt

Du kan använda Azure Policy för att genomdriva någon av följande resurser för att få en speciell `Microsoft.KubernetesConfiguration/sourceControlConfigurations` tillämpning:
*  `Microsoft.Kubernetes/connectedclusters` klusterresursen.
* GitOps-aktiverad `Microsoft.ContainerService/managedClusters` resurs. 

Om du vill använda Azure Policy väljer du en befintlig princip definition och skapar en princip tilldelning. När du skapar princip tilldelningen:
1. Ange omfånget för tilldelningen.
    * Omfånget är en Azure-resurs grupp eller-prenumeration. 
2. Ange parametrarna för `sourceControlConfiguration` som ska skapas. 

När tilldelningen har skapats identifierar Azure Policy-motorn alla `connectedCluster` eller `managedCluster` resurser som finns inom omfånget och tillämpar på `sourceControlConfiguration` var och en.

Du kan aktivera flera git-databaser som källor för sanningen för varje kluster genom att använda flera princip tilldelningar. Varje princip tilldelning konfigureras för att använda en annan git-lagrings platsen; till exempel en lagrings platsen för den centrala IT/kluster-operatören och andra databaser för program team.

## <a name="prerequisite"></a>Förutsättning

Kontrol lera att du har `Microsoft.Authorization/policyAssignments/write` behörighet för omfånget (prenumeration eller resurs grupp) där du vill skapa den här princip tilldelningen.

## <a name="create-a-policy-assignment"></a>Skapa en principtilldelning

1. Gå till **princip** i Azure Portal.
1. I avsnittet **redigering** på sid panelen väljer du **definitioner**.
1. I kategorin "Kubernetes" väljer du den inbyggda principen "distribuera GitOps till Kubernetes-kluster". 
1. Klicka på **tilldela**.
1. Ange **omfattningen** för hanterings gruppen, prenumerationen eller resurs gruppen som princip tilldelningen ska gälla för.
    * Om du vill undanta resurser från princip omfattningen ställer du in **undantag**.
1. Ge princip tilldelningen ett enkelt identifierbart **namn** och en **Beskrivning**.
1. Se till att **princip tvång** har **Aktiver ATS**.
1. Välj **Nästa**.
1. Ange de parameter värden som ska användas när du skapar `sourceControlConfiguration` .
1. Välj **Nästa**.
1. Aktivera **skapa en reparations uppgift**.
1. Verifiera att **skapa en hanterad identitet** är markerad och att identiteten har **deltagar** behörighet. 
    * Mer information finns i guiden [skapa en princip tilldelning snabb start](../../governance/policy/assign-policy-portal.md) och [Reparera icke-kompatibla resurser med Azure policy artikeln](../../governance/policy/how-to/remediate-resources.md).
1. Välj **Granska + skapa**.

När du har skapat princip tilldelningen `sourceControlConfiguration` kommer att tillämpas för någon av följande resurser som finns inom tilldelnings omfånget:
* Nya `connectedCluster` resurser.
* Nya `managedCluster` resurser med GitOps-agenterna installerade. 

För befintliga kluster måste du köra en reparations uppgift manuellt. Den här aktiviteten tar vanligt vis 10 till 20 minuter innan princip tilldelningen börjar gälla.

## <a name="verify-a-policy-assignment"></a>Verifiera en princip tilldelning

1. I Azure Portal navigerar du till en av dina `connectedCluster` resurser.
1. I avsnittet **Inställningar** på sid panelen väljer du **principer**. 
    * AKS-klustrets UX är inte implementerat ännu.
    * I listan principer ser du princip tilldelningen som du skapade tidigare med **kompatibilitetstillstånd** inställt som *kompatibelt*.
1. I avsnittet **Inställningar** på sid panelen väljer du **konfigurationer**.
    * I listan konfigurationer bör du se `sourceControlConfiguration` att princip tilldelningen har skapats.
1. Används `kubectl` för att göra en fråga till klustret. 
    * Du bör se namn området och artefakterna som har skapats av `sourceControlConfiguration` .
    * Inom 5 minuter bör du se i klustret de artefakter som beskrivs i manifesten i den konfigurerade git-lagrings platsen.

## <a name="next-steps"></a>Nästa steg

* [Konfigurera Azure Monitor för behållare med ARC-aktiverade Kubernetes-kluster](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md)
