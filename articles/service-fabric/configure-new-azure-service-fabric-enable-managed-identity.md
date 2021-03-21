---
title: Konfigurera stöd för hanterad identitet för ett nytt Service Fabric-kluster
description: Så här aktiverar du stöd för hanterade identiteter i ett nytt Azure Service Fabric-kluster
ms.topic: article
ms.date: 12/09/2019
ms.openlocfilehash: ae4fec452a2342a68843d874ba955b594014c46d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96574674"
---
# <a name="configure-managed-identity-support-for-a-new-service-fabric-cluster"></a>Konfigurera stöd för hanterad identitet för ett nytt Service Fabric-kluster

Om du vill använda [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i dina Service Fabric-program aktiverar du först den *hanterade identitets-token* i klustret. Den här tjänsten ansvarar för autentiseringen av Service Fabric program med hjälp av deras hanterade identiteter och för att få åtkomst till token för deras räkning. När tjänsten är aktive rad kan du se den i Service Fabric Explorer under **system** avsnittet i det vänstra fönstret, som körs under namnet **Fabric:/system/ManagedIdentityTokenService** bredvid andra system tjänster.

> [!NOTE]
> Service Fabric runtime-version 6.5.658.9590 eller högre krävs för att aktivera den **hanterade Identity token-tjänsten**.  

## <a name="enable-the-managed-identity-token-service"></a>Aktivera hanterad identitets-token

Om du vill aktivera tjänsten för hanterad identitets-token när klustret skapas lägger du till följande kodfragment till klustrets Azure Resource Manager mall:

```json
"fabricSettings": [
    {
        "name": "ManagedIdentityTokenService",
        "parameters": [
            {
                "name": "IsEnabled",
                "value": "true"
            }
        ]
    }
]
```

## <a name="errors"></a>Fel

Om distributionen Miss lyckas med det här meddelandet innebär det att klustret inte är på den nödvändiga Service Fabric versionen (den lägsta körning som stöds är 6,5 CU2):


```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="related-articles"></a>Relaterade artiklar

* Granska [stöd för hanterad identitet](./concepts-managed-identity.md) i Azure Service Fabric

* [Aktivera stöd för hanterad identitet i ett befintligt Azure Service Fabric-kluster](./configure-existing-cluster-enable-managed-identity-token-service.md)

## <a name="next-steps"></a>Nästa steg

* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Utnyttja den hanterade identiteten för ett Service Fabric program från service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Bevilja ett Azure Service Fabric program åtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)