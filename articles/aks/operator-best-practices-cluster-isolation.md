---
title: Metodtips för isolering av kluster
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för isolering i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: cdeecabf569e3c6f9b280e6b0179e5378f5b1c95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011378"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Metod tips för kluster isolering i Azure Kubernetes service (AKS)

När du hanterar kluster i Azure Kubernetes service (AKS) behöver du ofta isolera team och arbets belastningar. AKS ger flexibilitet i hur du kan köra kluster med flera klienter och isolera resurser. För att maximera din investering i Kubernetes bör dessa funktioner för flera innehavare och isolering förstås och implementeras.

I den här artikeln fokuserar vi på isolering för kluster operatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Planera för kluster med flera klienter och separering av resurser
> * Använd logisk eller fysisk isolering i AKS-kluster

## <a name="design-clusters-for-multi-tenancy"></a>Design kluster för flera innehavare

Kubernetes innehåller funktioner som gör det möjligt att logiskt isolera team och arbetsbelastningar i samma kluster. Målet bör vara att ange minsta möjliga antal privilegier, som är begränsade till de resurser som varje team behöver. Ett [namn område][k8s-namespaces] i Kubernetes skapar en logisk isolerings gränser. Ytterligare Kubernetes funktioner och överväganden för isolering och flera organisationer inkluderar följande områden:

* **Schemaläggning** inkluderar användning av grundläggande funktioner som resurs kvoter och Pod avbrott i budget. Mer information om dessa funktioner finns i [metod tips för grundläggande funktioner i Schemaläggaren i AKS][aks-best-practices-scheduler].
  * Fler avancerade funktioner i Schemaläggaren är bland annat-och-skydd, Node-väljare och Node-och Pod-tillhörighet eller anti-tillhörighet. Mer information om dessa funktioner finns i [metod tips för avancerade funktioner i Schemaläggaren i AKS][aks-best-practices-advanced-scheduler].
* **Nätverk** omfattar användningen av nätverks principer för att styra trafik flödet i och ut ur poddar.
* **Autentisering och auktorisering** inkluderar användaren av rollbaserad åtkomst kontroll (RBAC) och Azure Active Directory (AD)-integration, Pod-identiteter och hemligheter i Azure Key Vault. Mer information om dessa funktioner finns i [metod tips för autentisering och auktorisering i AKS][aks-best-practices-identity].
* I **behållare** ingår Azure policy-tillägget för AKS för att upprätthålla Pod-säkerhet, användning av Pod säkerhets kontexter och genomsökning av både avbildningar och körning av säkerhets risker. Inbegriper också användning av app-skydd eller Seccomp (säker data behandling) för att begränsa behållar åtkomsten till den underliggande noden.

## <a name="logically-isolate-clusters"></a>Isolera kluster logiskt

**Vägledning för bästa praxis** – Använd logisk isolering för att separera team och projekt. Försök att minimera antalet fysiska AKS-kluster som du distribuerar för att isolera team eller program.

Med logisk isolering kan ett enda AKS-kluster användas för flera arbets belastningar, team eller miljöer. Kubernetes- [namnområden][k8s-namespaces] utgör den logiska isolerings gränser för arbets belastningar och resurser.

![Logisk isolering av ett Kubernetes-kluster i AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logisk separering av kluster ger vanligt vis en högre Pod-densitet än fysiskt isolerade kluster. Det finns mindre överskott av beräknings kapaciteten som är inaktiv i klustret. När du kombineras med Kubernetes-klustret kan du skala upp eller ned antalet noder för att uppfylla kraven. Med den här bästa metoden för automatisk skalning kan du bara köra antalet noder som krävs och minimera kostnaderna.

Kubernetes-miljöer, i AKS eller någon annan stans, är inte helt säkra för att ta skydd på flera klienter. I en miljö med flera klienter arbetar flera klienter på en gemensam, delad infrastruktur. Om alla klienter inte kan vara betrodda måste du göra ytterligare planeringen för att undvika att en klient som påverkar säkerheten och tjänsten för en annan. Ytterligare säkerhetsfunktioner som *Pod säkerhets policy* och mer detaljerad rollbaserad åtkomst kontroll (RBAC) för noder gör det svårare att utnyttja dem. Men för verklig säkerhet när du kör en skydds arbets belastning med flera innehavare, är en hypervisor den enda säkerhets nivå som du bör lita på. Säkerhets domänen för Kubernetes blir hela klustret, inte en enskild nod. För dessa typer av farliga arbets belastningar med flera klienter bör du använda fysiskt isolerade kluster.

## <a name="physically-isolate-clusters"></a>Isolera kluster fysiskt

**Vägledning för bästa praxis** – minimera användningen av fysisk isolering för varje separat grupp eller program distribution. Använd i stället *logisk* isolering, enligt beskrivningen i föregående avsnitt.

En vanlig metod för kluster isolering är att använda fysiskt separata AKS-kluster. I den här isolerings modellen tilldelas team eller arbets belastningar sina egna AKS-kluster. Den här metoden ser ofta ut som det enklaste sättet att isolera arbets belastningar eller team, men lägger till ytterligare hantering och ekonomisk omkostnader. Du måste nu underhålla dessa flera kluster och måste individuellt ge åtkomst och tilldela behörigheter. Du debiteras också för alla enskilda noder.

![Fysisk isolering av enskilda Kubernetes-kluster i AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fysiskt separata kluster har vanligt vis en låg Pod-densitet. Eftersom varje team eller arbets belastning har sitt eget AKS-kluster, är klustret ofta överallokerat med beräknings resurser. Ofta schemaläggs ett litet antal poddar på noderna. Oanvänd kapacitet på noderna kan inte användas för program eller tjänster som utvecklas av andra team. Dessa överskott resurser bidrar till ytterligare kostnader i fysiskt separata kluster.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på kluster isolering. Mer information om kluster åtgärder i AKS finns i följande metod tips:

* [Basic Kubernetes Scheduler-funktioner][aks-best-practices-scheduler]
* [Avancerade funktioner i Kubernetes Scheduler][aks-best-practices-advanced-scheduler]
* [Autentisering och auktorisering][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->

<!-- INTERNAL LINKS -->
[k8s-namespaces]: concepts-clusters-workloads.md#namespaces
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
