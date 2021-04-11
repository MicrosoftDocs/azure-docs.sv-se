---
title: Azure RBAC – Azure Arc-aktiverad Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en översikt över Azure RBAC-funktionen på Azure Arc-aktiverade Kubernetes
ms.openlocfilehash: 7eb55ed819b6487697b5c2d64cdbabe2bbdae8a3
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451103"
---
# <a name="azure-rbac-on-azure-arc-enabled-kubernetes"></a>Azure RBAC på Azure Arc-aktiverade Kubernetes

Kubernetes [ClusterRoleBinding och RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) objekt typer hjälper till att definiera auktorisering i Kubernetes internt. Med Azure RBAC kan du använda Azure Active Directory (Azure AD) och roll tilldelningar i Azure för att kontrol lera auktoriserings kontroller i klustret.

Med den här funktionen blir alla fördelar med roll tilldelningar i Azure, till exempel aktivitets loggar som visar alla Azure RBAC-ändringar till en Azure-resurs, nu tillämpliga för ditt Azure Arc-Kubernetes-kluster.

## <a name="architecture---azure-rbac-on-azure-arc-enabled-kubernetes"></a>Arkitektur – Azure RBAC på Azure Arc-aktiverade Kubernetes

[![Azure RBAC-arkitektur ](./media/conceptual-azure-rbac.png)](./media/conceptual-azure-rbac.png#lightbox)

En webhook-Server ([Guard](https://github.com/appscode/guard)) distribueras på klustret för att kunna dirigera alla åtkomst kontroller för auktorisering till Authorization service i Azure.

`apiserver`Klustret har kon figurer ATS för att använda [webhook-token-autentisering](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication) och [webhook-auktorisering](https://kubernetes.io/docs/reference/access-authn-authz/webhook/) så att `TokenAccessReview` och `SubjectAccessReview` begär Anden dirigeras till Guard-webhook-servern. - `TokenAccessReview` Och- `SubjectAccessReview` begärandena utlöses av begär Anden för Kubernetes-resurser som skickas till `apiserver` .

Guard gör ett `checkAccess` anrop till Authorization service i Azure för att se om den begär ande Azure AD-entiteten har åtkomst till resursen. 

Om en roll i tilldelningen som tillåter åtkomsten finns, `allowed` skickas ett svar från Authorization service Guard. Guard skickar i sin tur ett `allowed` svar till som `apiserver` gör det möjligt för den anropande entiteten att komma åt den begärda Kubernetes-resursen.


Om en roll i tilldelningen som tillåter den här åtkomsten inte finns, `denied` skickas ett svar från Authorization service to Guard. Guard skickar ett `denied` svar till `apiserver` , vilket ger den anropande entiteten ett 403 förbjudet fel på den begärda resursen.

## <a name="next-steps"></a>Nästa steg

* Använd vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./quickstart-connect-cluster.md).
* [Konfigurera Azure RBAC](./azure-rbac.md) på Azure-bågen Kubernetes kluster kluster.