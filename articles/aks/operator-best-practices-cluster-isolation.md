---
title: Metodtips för isolering av kluster
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tips för kluster operatörer för isolering i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: e51689d33711f127f775c63c9d7fc8ad4c901604
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105178"
---
# <a name="best-practices-for-cluster-isolation-in-azure-kubernetes-service-aks"></a>Metod tips för kluster isolering i Azure Kubernetes service (AKS)

När du hanterar kluster i Azure Kubernetes service (AKS) behöver du ofta isolera team och arbets belastningar. AKS ger flexibilitet i hur du kan köra kluster med flera klienter och isolera resurser. För att maximera din investering i Kubernetes ska du först förstå och implementera AKS funktioner för flera organisationer och isolering.

I den här artikeln fokuserar vi på isolering för kluster operatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Planera för kluster med flera klienter och separering av resurser
> * Använd logisk eller fysisk isolering i AKS-kluster

## <a name="design-clusters-for-multi-tenancy"></a>Design kluster för flera innehavare

Med Kubernetes kan du logiskt isolera team och arbets belastningar i samma kluster. Målet är att ge det minsta antalet privilegier som är begränsat till resurserna som varje grupp behöver. Ett Kubernetes- [namnområde][k8s-namespaces] skapar en logisk isolerings gränser. Ytterligare Kubernetes funktioner och överväganden för isolering och flera organisationer inkluderar följande områden:

### <a name="scheduling"></a>Schemaläggning

*Schemaläggning* använder grundläggande funktioner som resurs kvoter och Pod avbrott i budget. Mer information om dessa funktioner finns i [metod tips för grundläggande funktioner i Schemaläggaren i AKS][aks-best-practices-scheduler].

Fler avancerade funktioner i Schemaläggaren är:
* Bismakar och tolererande
* Node-väljare
* Nod-och Pod tillhörighet eller anti-tillhörighet. 

Mer information om dessa funktioner finns i [metod tips för avancerade funktioner i Schemaläggaren i AKS][aks-best-practices-advanced-scheduler].

### <a name="networking"></a>Nätverk

*Nätverk* använder nätverks principer för att styra trafik flödet i och ut ur poddar.

### <a name="authentication-and-authorization"></a>Autentisering och auktorisering

*Autentisering och auktorisering* använder:
* Rollbaserad åtkomstkontroll (RBAC)
* Azure Active Directory (AD)-integration
* Pod identiteter
* Hemligheter i Azure Key Vault 

Mer information om dessa funktioner finns i [metod tips för autentisering och auktorisering i AKS][aks-best-practices-identity].

### <a name="containers"></a>Containers
*Behållare* är:
* Azure Policy-tillägget för AKS för att genomdriva pod-säkerhet.
* Användning av Pod säkerhets kontexter.
* Genomsöker både avbildningar och körnings miljön för sårbarheter. 
* Med hjälp av app-skydd eller Seccomp (säker data behandling) kan du begränsa behållar åtkomsten till den underliggande noden.

## <a name="logically-isolate-clusters"></a>Isolera kluster logiskt

> **Vägledning och metodtips**
>
> Separera team och projekt med hjälp av *logisk isolering*. Minimera antalet fysiska AKS-kluster som du distribuerar för att isolera team eller program.

Med logisk isolering kan ett enda AKS-kluster användas för flera arbets belastningar, team eller miljöer. Kubernetes- [namnområden][k8s-namespaces] utgör den logiska isolerings gränser för arbets belastningar och resurser.

![Logisk isolering av ett Kubernetes-kluster i AKS](media/operator-best-practices-cluster-isolation/logical-isolation.png)

Logisk separering av kluster ger vanligt vis en högre Pod-densitet än fysiskt isolerade kluster, med mindre överskott beräknings kapacitet inaktiv i klustret. När du kombineras med Kubernetes-klustret kan du skala upp eller ned antalet noder för att uppfylla kraven. Den här bästa metoden för automatisk skalning minimerar kostnaderna genom att bara köra antalet noder som krävs.

För närvarande är Kubernetes-miljöer inte helt säkra för att ta skydd på flera klienter. I en miljö med flera klienter fungerar flera klienter på en gemensam, delad infrastruktur. Om alla klienter inte är betrodda behöver du ytterligare planering för att förhindra att klienter påverkar säkerheten och tjänsten för andra.

Ytterligare säkerhetsfunktioner, t. ex. *Pod säkerhets principer* eller Kubernetes RBAC för noder, kan effektivt blockera sårbarheter. För verklig säkerhet vid körning av skydds arbets belastningar med flera innehavare bör du bara lita på en hypervisor. Säkerhets domänen för Kubernetes blir hela klustret, inte en enskild nod. 

För dessa typer av farliga arbets belastningar med flera klienter bör du använda fysiskt isolerade kluster.

## <a name="physically-isolate-clusters"></a>Isolera kluster fysiskt

> **Vägledning och metodtips** 
>
> Minimera användningen av fysisk isolering för varje separat grupp eller program distribution. Använd i stället *logisk* isolering, enligt beskrivningen i föregående avsnitt.

Fysiskt avgränsade AKS-kluster är ett gemensamt tillvägagångs sätt för kluster isolering. I den här isolerings modellen tilldelas team eller arbets belastningar sina egna AKS-kluster. Även om den fysiska isoleringen kan se ut som det enklaste sättet att isolera arbets belastningar eller team, lägger till hantering och ekonomisk omkostnader. Nu måste du underhålla dessa flera kluster och individuellt ge åtkomst och tilldela behörigheter. Du debiteras också för varje enskild nod.

![Fysisk isolering av enskilda Kubernetes-kluster i AKS](media/operator-best-practices-cluster-isolation/physical-isolation.png)

Fysiskt separata kluster har vanligt vis en låg Pod-densitet. Eftersom varje team eller arbets belastning har sitt eget AKS-kluster, är klustret ofta överallokerat med beräknings resurser. Ofta schemaläggs ett litet antal poddar på noderna. Det går inte att använda kapacitet för lediga noder för program eller tjänster i utveckling av andra team. Dessa överskott resurser bidrar till ytterligare kostnader i fysiskt separata kluster.

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
