---
title: Introduktion till Azure Red Hat OpenShift
description: Lär dig om funktionerna och fördelarna med Microsoft Azure Red Hat OpenShift för att distribuera och hantera program som baseras på behållare.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: overview
ms.date: 11/13/2020
ms.custom: mvc
ms.openlocfilehash: ec934a8a9907f0c0c3aff047e003c6725c5ab25f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100636228"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Med OpenShift-tjänsten Microsoft *Azure Red Hat* kan du distribuera fullständigt hanterade [OpenShift](https://www.openshift.com/) -kluster.

Azure Red Hat OpenShift utökar [Kubernetes](https://kubernetes.io/). Att köra behållare i produktion med Kubernetes kräver ytterligare verktyg och resurser. Detta omfattar ofta att behöva Juggle avbildnings register, lagrings hantering, nätverks lösningar och verktyg för loggning och övervakning – alla måste vara versioner av och testas tillsammans. Att skapa behållar program kräver ännu mer integrerings arbete med mellanprogram, ramverk, databaser och CI/CD-verktyg. Azure Red Hat OpenShift kombinerar allt detta till en enda plattform, och gör det enkelt för IT-team att ge program team vad de behöver utföra.

Azure Red Hat OpenShift är gemensamt utformat, drivs och stöds av Red Hat och Microsoft för att tillhandahålla en integrerad support upplevelse. Det finns inga virtuella datorer att hantera och ingen uppdatering krävs. Huvud-, infrastruktur-och programnoder korrigeras, uppdateras och övervakas för din räkning av Red Hat och Microsoft. Dina Azure Red Hat OpenShift-kluster distribueras till din Azure-prenumeration och ingår på din Azure-faktura.

Du kan välja egna register, nätverk, lagring och CI/CD-lösningar eller använda de inbyggda lösningarna för automatiserad käll kods hantering, behållare och program versioner, distributioner, skalning, hälso hantering med mera. Azure Red Hat OpenShift ger en integrerad inloggnings upplevelse via Azure Active Directory.

Kom igång genom att slutföra själv studie kursen [skapa en Azure Red Hat OpenShift-kluster](tutorial-create-cluster.md) .

## <a name="access-security-and-monitoring"></a>Åtkomst, säkerhet och övervakning

För förbättrad säkerhet och hantering kan du med hjälp av Azure Red Hat OpenShift integrera med Azure Active Directory (Azure AD) och använda Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC). Du kan också övervaka hälsotillståndet för ditt kluster och dina resurser.

## <a name="cluster-and-node"></a>Kluster och nod

Azure Red Hat OpenShift-noder körs på virtuella Azure-datorer. Du kan ansluta lagring till noder och poddar och uppgradera kluster komponenter.

## <a name="service-level-agreement"></a>Serviceavtal

Azure Red Hat OpenShift erbjuder en Serviceavtal som garanterar att tjänsten är tillgänglig 99,95% av tiden. Mer information om SLA finns i SLA för [Azure Red Hat OpenShift](https://azure.microsoft.com/en-au/support/legal/sla/openshift/v1_0/).

## <a name="next-steps"></a>Nästa steg

Läs om kraven för Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Konfigurera din utvecklingsmiljö](tutorial-create-cluster.md)
