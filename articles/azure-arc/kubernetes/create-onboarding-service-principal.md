---
title: Skapa en Azure Arc-aktiverad onboarding service-huvudobjekt (för hands version)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Skapa en Azure Arc-aktiverad onboarding-tjänstens huvud namn '
keywords: Kubernetes, båge, Azure, behållare
ms.openlocfilehash: 8772cf7634d9a833af120784e3e7868b41d202c4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390495"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Skapa en Azure Arc-aktiverad onboarding service-huvudobjekt (för hands version)

## <a name="overview"></a>Översikt

Du kan publicera Kubernetes-kluster till Azure-bågen med hjälp av tjänstens huvud namn med roll tilldelningar med begränsad behörighet. Den här funktionen är användbar i pipeline för kontinuerlig integrering och kontinuerlig distribution (CI/CD), till exempel Azure-pipelines och GitHub-åtgärder.

Gå igenom följande steg för att lära dig hur du använder tjänstens huvud namn för att registrera Kubernetes-kluster i Azure-bågen.

## <a name="create-a-new-service-principal"></a>Skapa ett nytt huvud namn för tjänsten

Skapa ett nytt huvud namn för tjänsten med ett informativt namn som är unikt för din Azure Active Directory klient.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Utdataparametrar**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Tilldela behörigheter

Tilldela rollen "Kubernetes kluster – Azure Arc onboarding" till den nyligen skapade tjänstens huvud namn. Den här inbyggda Azure-rollen med begränsade behörigheter tillåter endast att huvudobjektet registrerar kluster i Azure. Huvudobjektet med den här tilldelade rollen kan inte uppdatera, ta bort eller ändra andra kluster eller resurser i prenumerationen.

Med tanke på de begränsade förmågorna kan kunder enkelt använda den här huvudobjektet för att publicera flera kluster.

Du kan begränsa behörigheterna ytterligare genom att skicka i lämpligt `--scope` argument när du tilldelar rollen. Detta gör det möjligt för kunderna att begränsa kluster registreringen. Följande scenarier stöds av olika `--scope` Parametrar:

| Resurs  | `scope`-argument| Effekt |
| ------------- | ------------- | ------------- |
| Prenumeration | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Tjänstens huvud namn kan registrera alla kluster i en befintlig resurs grupp i den aktuella prenumerationen. |
| Resursgrupp | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Tjänstens huvud namn kan __bara__ registrera kluster i resurs gruppen `myGroup` . |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Utdataparametrar**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>Använd tjänstens huvud namn med Azure CLI

Referera till det nyligen skapade tjänstens huvud namn med följande kommandon:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Policy för att styra kluster konfigurationen](./use-azure-policy.md)
