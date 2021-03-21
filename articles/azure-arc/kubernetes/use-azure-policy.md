---
title: Använd Azure Policy för att tillämpa klusterkonfigurationer i stor skala
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Använd Azure Policy för att tillämpa klusterkonfigurationer i stor skala
keywords: Kubernetes, båge, Azure, K8s, behållare
ms.openlocfilehash: 05a6665a985ef8b229ee58082dc9b2c10cdcece3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121464"
---
# <a name="use-azure-policy-to-apply-cluster-configurations-at-scale"></a>Använd Azure Policy för att tillämpa klusterkonfigurationer i stor skala

Du kan använda Azure Policy för att tillämpa konfigurationer ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations` resurs typ) i skala på Azure Arc-aktiverade Kubernetes-kluster ( `Microsoft.Kubernetes/connectedclusters` ).

Om du vill använda Azure Policy väljer du en befintlig princip definition och skapar en princip tilldelning. När du skapar princip tilldelningen:
1. Ange omfånget för tilldelningen.
    * Omfånget är en Azure-resurs grupp eller-prenumeration. 
2. Ange parametrarna för den konfiguration som ska skapas. 

När tilldelningen har skapats identifierar Azure Policy-motorn alla Azure Arc-aktiverade Kubernetes-kluster som finns inom omfånget och tillämpar konfigurationen på varje kluster.

Du kan skapa flera konfigurationer, var och en som pekar på en annan git-lagrings platsen, med hjälp av flera princip tilldelningar. Till exempel en lagrings platsen för den centrala IT/kluster operatören och andra databaser för program team.

## <a name="prerequisite"></a>Förutsättning

Kontrol lera att du har `Microsoft.Authorization/policyAssignments/write` behörighet för omfånget (prenumeration eller resurs grupp) där du skapar den här princip tilldelningen.

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

När du har skapat princip tilldelningen tillämpas konfigurationen på nya Azure Arc-aktiverade Kubernetes-kluster som skapats inom omfånget för princip tilldelningen.

För befintliga kluster måste du köra en reparations uppgift manuellt. Den här aktiviteten tar vanligt vis 10 till 20 minuter innan princip tilldelningen börjar gälla.

## <a name="verify-a-policy-assignment"></a>Verifiera en princip tilldelning

1. I Azure Portal navigerar du till ett av dina Azure Arc-aktiverade Kubernetes-kluster.
1. I avsnittet **Inställningar** på sid panelen väljer du **principer**. 
    * I listan principer ser du princip tilldelningen som du skapade tidigare med **kompatibilitetstillstånd** inställt som *kompatibelt*.
1. I avsnittet **Inställningar** på sid panelen väljer du **konfigurationer**.
    * I listan konfigurationer bör du se konfigurationen som skapats av princip tilldelningen.
1. Används `kubectl` för att göra en fråga till klustret. 
    * Du bör se namn området och artefakterna som har skapats av konfigurations resurserna.
    * Inom 5 minuter (förutsatt att klustret har nätverks anslutning till Azure) bör du se de objekt som beskrivs av manifesten i git-lagrings platsen, som skapas i klustret.

## <a name="next-steps"></a>Nästa steg

[Konfigurera Azure Monitor för behållare med Azure Arc-aktiverade Kubernetes-kluster](../../azure-monitor/containers/container-insights-enable-arc-enabled-clusters.md).
