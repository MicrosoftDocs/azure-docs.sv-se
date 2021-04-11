---
title: Bästa praxis för Operator – hantering av behållar avbildningar i Azure Kubernetes Services (AKS)
description: Lär dig metod tips för kluster operatörer för att hantera och säkra behållar avbildningar i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 998d8602b6aa0e71a04f75aff1c29551ba09c8a3
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105127"
---
# <a name="best-practices-for-container-image-management-and-security-in-azure-kubernetes-service-aks"></a>Metod tips för hantering av behållar avbildningar och säkerhet i Azure Kubernetes service (AKS)

Säkerhet för behållare och behållar avbildning är en viktig prioritet när du utvecklar och kör program i Azure Kubernetes service (AKS). Behållare med inaktuella bas avbildningar eller program körningar utan korrigering introducerar en säkerhets risk och eventuell attack vektor. 

Minimera risker genom att integrera och köra skannings-och reparations verktyg i dina behållare vid build och Runtime. Tidigare har du fångat sårbarheten eller den föråldrade bas avbildningen, desto säkrare kluster. 

I den här artikeln innebär *"behållare"* båda:
* Behållar avbildningarna som lagras i ett behållar register.
* De behållare som körs.

Den här artikeln fokuserar på hur du skyddar dina behållare i AKS. Lär dig att:

> [!div class="checklist"]
> * Sök efter och åtgärda avbildnings sårbarheter.
> * Utlös och omdistribuera behållar avbildningar automatiskt när en bas avbildning uppdateras.

Du kan också läsa metod tips för [kluster säkerhet][best-practices-cluster-security] och Pod- [säkerhet][best-practices-pod-security].

Du kan också använda [behållar säkerhet i Security Center][security-center-containers] för att söka igenom behållarna efter sårbarheter. [Azure Container Registry-integrering][security-center-acr] med Security Center hjälper till att skydda dina avbildningar och register från sårbarheter.

## <a name="secure-the-images-and-run-time"></a>Skydda avbildningarna och körnings tiden

> **Vägledning och metodtips** 
>
> Sök igenom behållar avbildningarna efter sårbarheter. Distribuera bara verifierade avbildningar. Uppdatera bas avbildningarna och program körningen regelbundet. Distribuera om arbets belastningar i AKS-klustret.

När du implementerar behållar arbets belastningar ska du kontrol lera säkerheten för avbildningar och körnings miljön som används för att bygga dina egna program. Hur undviker du att införa säkerhets risker i dina distributioner? 
* Ta med i distributions arbets flödet en process för att skanna behållar avbildningar med hjälp av verktyg som [twistlock][twistlock] eller [turkos][aqua].
* Tillåt endast att verifierade avbildningar distribueras.

![Genomsök och reparera behållar avbildningar, validera och distribuera](media/operator-best-practices-container-security/scan-container-images-simplified.png)

Du kan till exempel använda en pipeline för kontinuerlig integrering och distribution (CI/CD) för att automatisera avbildnings genomsökningar, verifiering och distributioner. Azure Container Registry innehåller genomsöknings funktionerna för sårbarheter.

## <a name="automatically-build-new-images-on-base-image-update"></a>Bygg automatiskt nya avbildningar på bas avbildnings uppdatering

> **Vägledning och metodtips** 
>
> När du använder bas avbildningar för program avbildningar kan du använda Automation för att bygga nya avbildningar när bas avbildningen uppdateras. Eftersom uppdaterade bas avbildningar inkluderar vanligt vis säkerhets korrigeringar, uppdaterar du alla underordnade program behållar avbildningar.

Varje gång en bas avbildning uppdateras bör du även uppdatera eventuella underordnade behållar avbildningar. Integrera den här bygg processen i drift sättnings-och distributions pipeliner, till exempel [Azure-pipeliner][azure-pipelines] eller Jenkins. Dessa pipelines ser till att programmen fortsätter att köras på de uppdaterade baserade avbildningarna. När dina program behållar avbildningar har verifierats kan AKS-distributionerna uppdateras för att köra de senaste, säkra avbildningarna.

Azure Container Registry uppgifter kan också automatiskt uppdatera behållar avbildningar när bas avbildningen uppdateras. Med den här funktionen skapar du några grundläggande avbildningar och håller dem uppdaterade med fel-och säkerhets korrigeringar.

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