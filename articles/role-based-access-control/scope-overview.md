---
title: Förstå omfattning för Azure RBAC
description: Lär dig mer om omfång för rollbaserad åtkomst kontroll i Azure (Azure RBAC) och hur du fastställer omfånget för en resurs.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 10/08/2020
ms.author: rolyon
ms.openlocfilehash: deee42c46c9b08bb265c972695b9319413d4fcb1
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555926"
---
# <a name="understand-scope-for-azure-rbac"></a>Förstå omfattning för Azure RBAC

*Omfattning* är den uppsättning resurser som åtkomsten gäller för. När du tilldelar en roll är det viktigt att förstå omfånget så att du kan ge ett säkerhets objekt bara den åtkomst som det verkligen behöver. Genom att begränsa omfattningen begränsar du vilka resurser som är utsatta för risk om säkerhets objekts skyddet aldrig äventyras.

## <a name="scope-levels"></a>Omfattnings nivåer

I Azure kan du ange ett omfång på fyra nivåer: [hanterings grupp](../governance/management-groups/overview.md), prenumeration, [resurs grupp](../azure-resource-manager/management/overview.md#resource-groups)och resurs. Omfång är strukturerade i en överordnad/underordnad relation. Varje nivå i hierarkin gör omfånget mer information. Du kan tilldela roller på någon av dessa nivåer av omfång. Nivån som du väljer avgör hur mycket rollen tillämpas. Lägre nivåer ärver roll behörigheter från högre nivåer. 

![Omfång för en rolltilldelning](./media/scope-overview/rbac-scope-no-label.png)

Hanterings grupper är en nivå av omfånget över prenumerationer, men hanterings grupper stöder mer komplexa hierarkier. I följande diagram visas ett exempel på en hierarki med hanterings grupper och prenumerationer som du kan definiera. Mer information om hanterings grupper finns i [Vad är Azures hanterings grupper?](../governance/management-groups/overview.md).

![Hanterings grupp och prenumerationsbegäran](./media/scope-overview/rbac-scope-management-groups.png)

## <a name="scope-format"></a>Omfattnings format

Om du tilldelar roller med hjälp av kommando raden måste du ange omfånget. För kommando rads verktyg är omfånget en potentiellt lång sträng som identifierar roll tilldelningens exakta omfång. I Azure Portal visas det här omfånget vanligt vis som *resurs-ID*.

Omfattningen består av en serie identifierare avgränsade med snedstreck (/). Du kan tänka på den här strängen som att uttrycka följande hierarki, där text utan plats hållare ( `{}` ) är fasta identifierare:

```
/subscriptions
    /{subscriptionId}
        /resourcegroups
            /{resourceGroupName}
                /providers
                    /{providerName}
                        /{resourceType}
                            /{resourceSubType1}
                                /{resourceSubType2}
                                    /{resourceName}
```

- `{subscriptionId}` är ID: t för den prenumeration som ska användas (en GUID).
- `{resourcesGroupName}` är namnet på den resurs grupp som innehåller.
- `{providerName}` är namnet på den [resurs leverantör](../azure-resource-manager/management/azure-services-resource-providers.md) som hanterar resursen `{resourceType}` och `{resourceSubType*}` identifiera ytterligare nivåer inom den resurs leverantören.
- `{resourceName}` är den sista delen av strängen som identifierar en speciell resurs.

Hanterings grupper är en nivå över prenumerationer och har det bredaste (minst aktuella) omfånget. Roll tilldelningar på den här nivån gäller för prenumerationer i hanterings gruppen. Omfånget för en hanterings grupp har följande format:

```
/providers
    /Microsoft.Management
        /managementGroups
            /{managmentGroupName}
```

## <a name="scope-examples"></a>Omfattnings exempel

> [!div class="mx-tableFixed"]
> | Omfång | Exempel |
> | --- | --- |
> | Hanteringsgrupp | `/providers/Microsoft.Management/managementGroups/marketing-group` |
> | Prenumeration | `/subscriptions/00000000-0000-0000-0000-000000000000` |
> | Resursgrupp | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/pharma-sales` |
> | Resurs | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01` |
> |  | `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MyVirtualNetworkResourceGroup/providers/Microsoft.Network/virtualNetworks/MyVirtualNetwork12345` |

## <a name="how-to-determine-the-scope-for-a-resource"></a>Så här fastställer du omfånget för en resurs

Det är ganska enkelt att fastställa omfattningen för en hanterings grupp, en prenumeration eller en resurs grupp. Du behöver bara känna till namnet och prenumerations-ID: t. Att fastställa omfattningen för en resurs tar dock lite mer arbete. Här är några exempel på hur du kan fastställa omfånget för en resurs.

- Öppna resursen i Azure Portal och titta sedan på egenskaperna. Resursen ska ange **resurs-ID** där du kan fastställa omfånget. Här är till exempel resurs-ID: n för ett lagrings konto.

    ![Resurs-ID för ett lagrings konto i Azure Portal](./media/scope-overview/scope-resource-id.png)

- Ett annat sätt är att använda Azure Portal för att tilldela en roll tillfälligt i resurs omfånget och sedan använda [Azure PowerShell](role-assignments-list-powershell.md) eller [Azure CLI](role-assignments-list-cli.md) för att Visa roll tilldelningen. I utdata visas omfånget som en egenskap.

    ```azurepowershell
    RoleAssignmentId   : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/pro
                         viders/Microsoft.Authorization/roleAssignments/<roleAssignmentId>
    Scope              : /subscriptions/<subscriptionId>/resourceGroups/test-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01
    DisplayName        : User
    SignInName         : user@contoso.com
    RoleDefinitionName : Storage Blob Data Reader
    RoleDefinitionId   : 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1
    ObjectId           : <principalId>
    ObjectType         : User
    CanDelegate        : False
    Description        :
    ConditionVersion   :
    Condition          :
    ```

    ```azurecli
    {
        "canDelegate": null,
        "condition": null,
        "conditionVersion": null,
        "description": null,
        "id": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
        "name": "{roleAssignmentId}",
        "principalId": "{principalId}",
        "principalName": "user@contoso.com",
        "principalType": "User",
        "resourceGroup": "test-rg",
        "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
        "roleDefinitionName": "Storage Blob Data Reader",
        "scope": "/subscriptions/{subscriptionId}/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/azurestorage12345/blobServices/default/containers/blob-container-01",
        "type": "Microsoft.Authorization/roleAssignments"
      }
    ```

## <a name="next-steps"></a>Nästa steg

- [Steg för att tilldela en Azure-roll](role-assignments-steps.md)
- [Resursleverantörer för Azure-tjänster](../azure-resource-manager/management/azure-services-resource-providers.md)
- [Vad är hanteringsgrupper i Azure?](../governance/management-groups/overview.md)
