---
title: Kluster Connect – Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en översikt över hur du kan aktivera Azure-Kubernetes i kluster Connect-funktionen
ms.openlocfilehash: 716ffe0c1f123c2a6abe8f407f6435e157ba5b6a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451096"
---
# <a name="cluster-connect-on-azure-arc-enabled-kubernetes"></a>Kluster anslutning på Azure Arc-aktiverade Kubernetes

Funktionen Azure Arc Enabled Kubernetes *cluster Connect* ger anslutning till `apiserver` klustret utan att någon inkommande port måste vara aktive rad i brand väggen. En reverse proxy-agent som körs i klustret kan på ett säkert sätt starta en session med Azure Arc-tjänsten på ett utgående sätt. 

Med kluster anslutning kan utvecklare komma åt sina kluster var som helst för interaktiv utveckling och fel sökning. Du kan också låta kluster användare och administratörer komma åt eller hantera sina kluster var de än befinner sig. Du kan till och med använda värdbaserade agenter/löpare för Azure-pipeliner, GitHub-åtgärder eller någon annan värdbaserad CI/CD-tjänst för att distribuera program till lokal-kluster, utan att kräva egna agenter.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Arkitektur

[![Arkitektur ](./media/conceptual-cluster-connect.png) för kluster anslutning](./media/conceptual-cluster-connect.png#lightbox)

En reverse proxy-agent `clusterconnect-agent` som kallas distribuerad som en del av agent Helm-diagrammet på kluster sidan gör utgående anrop till Azure Arc-tjänsten för att upprätta sessionen.

När användaren anropar `az connectedk8s proxy` :
1. Azure Arc-binärfilen laddas ned och summeras som en process på klient datorn. 
1. Azure Arc-proxy hämtar en `kubeconfig` fil som är associerad med Azure Arc-Kubernetes-klustret där `az connectedk8s proxy` anropas.
    * Azure Arc-proxyn använder anroparens Azure-åtkomsttoken och Azure Resource Manager-ID-namnet. 
1. `kubeconfig`Filen, som sparas på datorn av Azure Arc-proxy, pekar på Server-URL: en till en slut punkt i Azure Arc-proxyn.

När en användare skickar en begäran med hjälp av den här `kubeconfig` filen:
1. Azure båg proxy mappar slut punkten som tar emot begäran till Azure Arc-tjänsten. 
1. Azure båg service vidarebefordrar sedan begäran till `clusterconnect-agent` kluster som körs i klustret. 
1. `clusterconnect-agent`Skickar begäran till `kube-aad-proxy` komponenten, som utför Azure AD-autentisering på den anropande entiteten. 
1. Efter Azure AD-autentisering `kube-aad-proxy` använder Kubernetes [användar personifiering](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#user-impersonation) för att vidarebefordra begäran till klustrets `apiserver` .

## <a name="next-steps"></a>Nästa steg

* Använd vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./quickstart-connect-cluster.md).
* [Få åtkomst till klustret](./cluster-connect.md) på ett säkert sätt från var som helst med kluster anslutning.