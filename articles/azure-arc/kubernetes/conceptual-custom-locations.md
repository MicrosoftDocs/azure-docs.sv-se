---
title: Anpassade platser – Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en översikt över funktioner för anpassade platser i Azure Arc-aktiverade Kubernetes
ms.openlocfilehash: 1235c6adfe97a943ef77584a6a0c8683d691be91
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451128"
---
# <a name="custom-locations-on-top-of-azure-arc-enabled-kubernetes"></a>Anpassade platser ovanpå Azure Arc-aktiverade Kubernetes

Som en utökning av Azures plats konstruktion ger *anpassade platser* ett sätt för klient administratörer att använda sina Azure-Kubernetes-kluster som mål platser för att distribuera Azure Services-instanser. Exempel på Azure-resurser är Azure Arc-aktiverad SQL-hanterad instans och Azure Arc-aktiverat PostgreSQL-skalning.

På samma sätt som för Azure-platser kan slutanvändare inom klienten med åtkomst till anpassade platser distribuera resurser där deras privata data bearbetning används.

[![Båg plattforms lager ](./media/conceptual-arc-platform-layers.png)](./media/conceptual-arc-platform-layers.png#lightbox)

Du kan visualisera anpassade platser som ett abstraktions lager ovanpå Azure Arc-aktiverade Kubernetes-kluster, kluster Connect och kluster tillägg. Anpassade platser skapar de detaljerade [RoleBindings och ClusterRoleBindings](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) som krävs för andra Azure-tjänster för att få åtkomst till klustret. Dessa andra Azure-tjänster kräver kluster åtkomst för att hantera resurser som kunden vill distribuera i sina kluster.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Arkitektur

När administratören aktiverar funktionen anpassade platser i klustret, skapas en ClusterRoleBinding i klustret och Azure AD-programmet som används av den anpassade plats resurs leverantören (RP) auktoriseras. När de har auktoriserats kan anpassade platser skapa ClusterRoleBindings eller RoleBindings som krävs av andra Azure-RPs för att skapa anpassade resurser i det här klustret. Kluster tilläggen som är installerade i klustret bestämmer listan över RPs som ska auktoriseras.

[![Använd anpassade platser ](./media/conceptual-custom-locations-usage.png)](./media/conceptual-custom-locations-usage.png#lightbox)

När användaren skapar en data tjänst instans i klustret: 
1. Begäran om placering skickas till Azure Resource Manager.
1. PLACERINGs förfrågan vidarebefordras till Azure-bågen som är aktive rad Data Services RP. 
1. RP: en hämtar `kubeconfig` filen som är associerad med Azure Arc-Kubernetes klustret, där den anpassade platsen finns. 
   * Den anpassade platsen refereras till som `extendedLocation` i den ursprungliga begäran. 
1. Azure-båge aktive rad Data Services RP använder `kubeconfig` för att kommunicera med klustret för att skapa en anpassad resurs för Azure-bågen som är aktive rad Data Services typ i namn området som är mappat till den anpassade platsen. 
   * Azure Arc-aktiverad Data Services-operatören har distribuerats via skapande av kluster tillägg innan den anpassade platsen fanns. 
1. Azure-bågen aktive rad Data Services operatören läser den nya anpassade resursen som skapats i klustret och skapar datakontrollanten, vilket översätts till realiseringen av det önskade läget i klustret. 

Sekvensen av steg för att skapa SQL-hanterad instans och PostgreSQL-instansen är identiska med stegen som beskrivs ovan.

## <a name="next-steps"></a>Nästa steg

* Använd vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./quickstart-connect-cluster.md).
* [Skapa en anpassad plats](./custom-locations.md) på ditt Azure Arc-aktiverade Kubernetes-kluster.