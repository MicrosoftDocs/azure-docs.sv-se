---
title: Bästa praxis för Operator – hantering av behållar avbildningar i Azure Kubernetes Services (AKS)
description: Lär dig metod tips för kluster operatörer för att hantera och säkra behållar avbildningar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 1d2f5465356a94b9ad7014e75aa6fe1515411a81
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102564925"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Metod tips för hantering av behållar avbildningar och säkerhet i Azure Kubernetes service (AKS)

När du utvecklar och kör program i Azure Kubernetes service (AKS) är säkerheten för dina behållare och behållar avbildningar en viktig faktor. Behållare som innehåller inaktuella bas avbildningar eller program körningar utan korrigering introducerar en säkerhets risk och en eventuell attack vektor. För att minimera dessa risker bör du integrera verktyg som söker efter och åtgärdar problem i dina behållare vid Bygg tiden och körning. Den tidigare i den process där sårbarheten eller den aktuella bas avbildningen är infångad, desto säkrare kluster. I den här artikeln innebär *behållare* både de behållar avbildningar som lagras i ett behållar register och de behållare som körs.

Den här artikeln fokuserar på hur du skyddar dina behållare i AKS. Lär dig att:

> [!div class="checklist"]
> * Sök efter och åtgärda avbildnings sårbarheter
> * Utlös och omdistribuera behållar avbildningar automatiskt när en bas avbildning uppdateras

Du kan också läsa metod tips för [kluster säkerhet][best-practices-cluster-security] och Pod- [säkerhet][best-practices-pod-security].

Du kan också använda [behållar säkerhet i Security Center][security-center-containers] för att söka igenom behållarna efter sårbarheter.  Det finns också [Azure Container Registry-integrering][security-center-acr] med Security Center som hjälper till att skydda dina avbildningar och register från sårbarheter.

## <a name="secure-the-images-and-run-time"></a>Skydda avbildningarna och körnings tiden

**Vägledning för bästa praxis** – Sök igenom behållar avbildningar efter sårbarheter och distribuera bara avbildningar som har klarat verifieringen. Uppdatera bas avbildningarna och program körningen regelbundet och distribuera sedan om arbets belastningarna i AKS-klustret.

Ett problem med antagandet av containerbaserade arbets belastningar är att kontrol lera säkerheten för avbildningar och körningar som används för att bygga dina egna program. Hur ser du till att du inte inför säkerhets risker i dina distributioner? Distributions arbets flödet bör innehålla en process för att skanna behållar avbildningar med hjälp av verktyg som [twistlock][twistlock] eller [turkos][aqua], och sedan bara tillåta att verifierade avbildningar distribueras.

![Genomsök och reparera behållar avbildningar, validera och distribuera](media/operator-best-practices-container-security/scan-container-images-simplified.png)

I ett verkligt exempel kan du använda en pipeline för kontinuerlig integrering och distribution (CI/CD) för att automatisera avbildnings genomsökningar, verifiering och distributioner. Azure Container Registry innehåller genomsöknings funktionerna för sårbarheter.

## <a name="automatically-build-new-images-on-base-image-update"></a>Bygg automatiskt nya avbildningar på bas avbildnings uppdatering

**Vägledning för bästa praxis** – när du använder bas avbildningar för program avbildningar använder du Automation för att bygga nya avbildningar när bas avbildningen uppdateras. Eftersom dessa bas avbildningar vanligt vis innehåller säkerhets korrigeringar, uppdaterar du alla underordnade program behållar avbildningar.

Varje gång en bas avbildning uppdateras bör även eventuella underordnade behållar avbildningar uppdateras. Den här bygg processen bör integreras i pipeline för validering och distribution, till exempel [Azure-pipeliner][azure-pipelines] eller Jenkins. Dessa pipelines ser till att programmen fortsätter att köras på de uppdaterade baserade avbildningarna. När dina program behållar avbildningar har verifierats kan AKS-distributionerna uppdateras för att köra de senaste, säkra avbildningarna.

Azure Container Registry uppgifter kan också automatiskt uppdatera behållar avbildningar när bas avbildningen uppdateras. Med den här funktionen kan du skapa ett litet antal bas avbildningar och regelbundet hålla dem uppdaterade med fel-och säkerhets korrigeringar.

Mer information om uppdateringar av bas avbildningar finns i avsnittet [om att automatisera avbildningar på bas avbildnings uppdatering med Azure Container Registry uppgifter][acr-base-image-update].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på hur du skyddar dina behållare. Information om hur du implementerar vissa av dessa områden finns i följande artiklar:

* [Automatisera avbildning bygger på en bas avbildnings uppdatering med Azure Container Registry uppgifter][acr-base-image-update]

<!-- EXTERNAL LINKS -->
[azure-pipelines]: /azure/devops/pipelines/
[twistlock]: https://www.twistlock.com/
[aqua]: https://www.aquasec.com/

<!-- INTERNAL LINKS -->
[best-practices-cluster-security]: operator-best-practices-cluster-security.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[acr-base-image-update]: ../container-registry/container-registry-tutorial-base-image-update.md
[security-center-containers]: ../security-center/container-security.md
[security-center-acr]: ../security-center/defender-for-container-registries-introduction.md