---
title: Konfigurera stöd för hanterad identitet i ett befintligt Service Fabric-kluster
description: Så här aktiverar du stöd för hanterade identiteter i ett befintligt Azure Service Fabric-kluster
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: dc341688cae6d98f53be10351e4e4572a3539e4e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98790047"
---
# <a name="configure-managed-identity-support-in-an-existing-service-fabric-cluster"></a>Konfigurera stöd för hanterad identitet i ett befintligt Service Fabric-kluster

Om du vill använda [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) i dina Service Fabric-program aktiverar du först den *hanterade identitets-token* i klustret. Den här tjänsten ansvarar för autentiseringen av Service Fabric program med hjälp av deras hanterade identiteter och för att få åtkomst till token för deras räkning. När tjänsten är aktive rad kan du se den i Service Fabric Explorer under **system** avsnittet i det vänstra fönstret, som körs under namnet **Fabric:/system/ManagedIdentityTokenService**.

> [!NOTE]
> Service Fabric runtime-version 6.5.658.9590 eller högre krävs för att aktivera den **hanterade Identity token-tjänsten**.  
>
> Du kan hitta Service Fabric versionen av ett kluster från Azure Portal genom att öppna kluster resursen och kontrol lera egenskapen **Service Fabric version** i avsnittet **Essentials** .
>
> Om klustret är i **manuellt** uppgraderings läge måste du först uppgradera det till 6.5.658.9590 eller senare.

## <a name="enable-managed-identity-token-service-in-an-existing-cluster"></a>Aktivera *hanterad identitets-token* i ett befintligt kluster

Om du vill aktivera tjänsten hanterad identitets-token i ett befintligt kluster måste du initiera en kluster uppgradering som anger två ändringar: (1) som aktiverar hanterad Identity token-tjänst och (2) begär en omstart av varje nod. Lägg först till följande fragment ditt kluster Azure Resource Manager-mall:

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

För att ändringarna ska träda i kraft måste du också ändra uppgraderings principen för att ange en tvingande omstart av Service Fabric runtime på varje nod när uppgraderingen fortskrider genom klustret. Den här omstarten säkerställer att den nyligen aktiverade system tjänsten startas och körs på varje nod. I kodfragmentet nedan `forceRestart` är den viktigaste inställningen för att aktivera omstart. Använd värden som beskrivs nedan eller använd befintliga anpassade värden som redan har angetts för kluster resursen för de återstående parametrarna. Anpassade inställningar för uppgraderings principen för infrastruktur (' upgradeDescription ') kan visas från Azure Portal genom att välja alternativet för Fabric-uppgraderingar på Service Fabric resurs eller resources.azure.com. Standard alternativ för uppgraderings principen (' upgradeDescription ') kan inte visas från PowerShell eller resources.azure.com. Mer information finns i [ClusterUpgradePolicy](/dotnet/api/microsoft.azure.management.servicefabric.models.clusterupgradepolicy) .  

```json
"upgradeDescription": {
    "forceRestart": true,
    "healthCheckRetryTimeout": "00:45:00",
    "healthCheckStableDuration": "00:05:00",
    "healthCheckWaitDuration": "00:05:00",
    "upgradeDomainTimeout": "02:00:00",
    "upgradeReplicaSetCheckTimeout": "1.00:00:00",
    "upgradeTimeout": "12:00:00"
}
```

> [!NOTE]
> När uppgraderingen är klar ska du inte glömma att återställa `forceRestart` inställningen för att minimera effekten av efterföljande uppgraderingar. 

## <a name="errors-and-troubleshooting"></a>Fel och fel sökning

Om distributionen Miss lyckas med följande meddelande innebär det att klustret inte körs på en hög nog Service Fabric version:

```json
{
    "code": "ParameterNotAllowed",
    "message": "Section 'ManagedIdentityTokenService' and Parameter 'IsEnabled' is not allowed."
}
```

## <a name="next-steps"></a>Nästa steg
* [Distribuera ett Azure Service Fabric-program med en systemtilldelad hanterad identitet](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)
* [Distribuera ett Azure Service Fabric-program med en användardefinierad hanterad identitet](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)
* [Utnyttja den hanterade identiteten för ett Service Fabric program från service code](./how-to-managed-identity-service-fabric-app-code.md)
* [Bevilja ett Azure Service Fabric program åtkomst till andra Azure-resurser](./how-to-grant-access-other-resources.md)
