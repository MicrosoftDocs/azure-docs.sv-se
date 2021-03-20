---
title: Distribuera en app med en användardefinierad hanterad identitet
description: Den här artikeln visar hur du distribuerar Service Fabric program med en användardefinierad hanterad identitet
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: 79d8654733b580be96d59e78f31105077929ac78
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86260085"
---
# <a name="deploy-service-fabric-application-with-a-user-assigned-managed-identity"></a>Distribuera Service Fabric program med en User-Assigned hanterad identitet

Om du vill distribuera ett Service Fabric program med hanterad identitet måste programmet distribueras via Azure Resource Manager, vanligt vis med en Azure Resource Manager mall. Mer information om hur du distribuerar Service Fabric program via Azure Resource Manager finns i [hantera program och tjänster som Azure Resource Manager resurser](service-fabric-application-arm-resource.md).

> [!NOTE] 
> 
> Program som inte distribueras som en Azure-resurs **kan inte** ha hanterade identiteter. 
>
> Service Fabric program distribution med hanterad identitet stöds med API-versionen `"2019-06-01-preview"` . Du kan också använda samma API-version för program typ, version av program typ och tjänst resurser.
>

## <a name="user-assigned-identity"></a>User-Assigned identitet

Om du vill aktivera program med User-Assigned identitet måste du först lägga till egenskapen **Identity** i program resursen med typen **userAssigned** och de referenser som tilldelats av användaren. Lägg sedan till ett **managedIdentities** -avsnitt i avsnittet **Egenskaper** för **program** resursen som innehåller en lista med ett eget namn för principalId-mappningen för var och en av de användare som tilldelats identiteter. Mer information om tilldelade identiteter finns i [skapa, lista eller ta bort en hanterad identitet som tilldelats av användare](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

### <a name="application-template"></a>Programmall

Om du vill aktivera program med tilldelad identitet måste du först lägga till **identitets** egenskap till program resursen med typen **userAssigned** och den refererade användaren som tilldelats identiteter och sedan lägga till ett **managedIdentities** -objekt i avsnittet **Egenskaper** som innehåller en lista med ett eget namn för principalId-mappning för var och en av de användare som tilldelats identiteter.

```json
{
  "apiVersion": "2019-06-01-preview",
  "type": "Microsoft.ServiceFabric/clusters/applications",
  "name": "[concat(parameters('clusterName'), '/', parameters('applicationName'))]",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.ServiceFabric/clusters/', parameters('clusterName'), '/applicationTypes/', parameters('applicationTypeName'), '/versions/', parameters('applicationTypeVersion'))]",
    "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]"
  ],
  "identity": {
    "type" : "userAssigned",
    "userAssignedIdentities": {
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName'))]": {}
    }
  },
  "properties": {
    "typeName": "[parameters('applicationTypeName')]",
    "typeVersion": "[parameters('applicationTypeVersion')]",
    "parameters": {
    },
    "managedIdentities": [
      {
        "name" : "[parameters('userAssignedIdentityName')]",
        "principalId" : "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', parameters('userAssignedIdentityName')), '2018-11-30').principalId]"
      }
    ]
  }
}
```

I exemplet ovan används resurs namnet för den användare som tilldelats identiteten som det egna namnet på den hanterade identiteten för programmet. Följande exempel förutsätter att det faktiska egna namnet är "AdminUser".

### <a name="application-package"></a>Programpaket

1. `managedIdentities`Lägg till en `<ManagedIdentity>` tagg i program manifestet under avsnittet **huvud namn** för varje identitet som definieras i avsnittet i Azure Resource Manager mall. `Name`Attributet måste matcha den egenskap som `name` definierats i `managedIdentities` avsnittet.

    **ApplicationManifest.xml**

    ```xml
      <Principals>
        <ManagedIdentities>
          <ManagedIdentity Name="AdminUser" />
        </ManagedIdentities>
      </Principals>
    ```

2. I avsnittet **service manifest import** lägger du till en **IdentityBindingPolicy** för tjänsten som använder den hanterade identiteten. Den här principen mappar `AdminUser` identiteten till ett tjänstspecifikt identitets namn som måste läggas till i tjänst manifestet senare.

    **ApplicationManifest.xml**

    ```xml
      <ServiceManifestImport>
        <Policies>
          <IdentityBindingPolicy ServiceIdentityRef="WebAdmin" ApplicationIdentityRef="AdminUser" />
        </Policies>
      </ServiceManifestImport>
    ```

3. Uppdatera tjänst manifestet för att lägga till en **ManagedIdentity** i avsnittet **resurser** med namnet som matchar `ServiceIdentityRef` i `IdentityBindingPolicy` program manifestet:

    **ServiceManifest.xml**

    ```xml
      <Resources>
        ...
        <ManagedIdentities DefaultIdentity="WebAdmin">
          <ManagedIdentity Name="WebAdmin" />
        </ManagedIdentities>
      </Resources>
    ```

## <a name="next-steps"></a>Nästa steg

* [Använda hanterad identitet i Service Fabric program kod](how-to-managed-identity-service-fabric-app-code.md)
* [Så här beviljar du Service Fabric program åtkomst till andra Azure-resurser](how-to-grant-access-other-resources.md)
