---
title: Efterlevnad med Azure Policy
description: Tilldela inbyggda principer i Azure Policy för att granska efterlevnad för dina Azure SignalR Service resurser.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: c8776102602f5bdcf29139d808a6f603cc5c7473
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784581"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Granska efterlevnad för Azure SignalR Service resurser med Azure Policy

[Azure Policy](../governance/policy/overview.md) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. De här principerna tillämpar olika regler och effekter på dina resurser så att resurserna efterlever dina företagsstandarder och serviceavtal.

Den här artikeln introducerar inbyggda principer (förhandsversion) för Azure SignalR Service. Använd dessa principer för att granska nya och befintliga SignalR-resurser för efterlevnad.

Det tillkommer inga avgifter för att använda Azure Policy.

## <a name="built-in-policy-definitions"></a>Inbyggda principdefinitioner

Följande inbyggda principdefinitioner är specifika för Azure SignalR Service:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Tilldela principdefinitioner

* Tilldela principdefinitioner [med hjälp Azure Portal,](../governance/policy/assign-policy-portal.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md) [en Resource Manager mall](../governance/policy/assign-policy-template.md)eller Azure Policy-SDK:er.
* Omfång för en principtilldelning till en resursgrupp, en prenumeration eller en [Azure-hanteringsgrupp.](../governance/management-groups/overview.md) SignalR-principtilldelningar gäller för befintliga och nya SignalR-resurser inom omfånget.
* Aktivera eller inaktivera [principtvingande](../governance/policy/concepts/assignment-structure.md#enforcement-mode) när som helst.

> [!NOTE]
> När du har tilldelar eller uppdaterar en princip tar det lite tid innan tilldelningen tillämpas på resurser i det definierade omfånget. Se information om [utlösare för principutvärdering.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Granska principefterlevnad

Få åtkomst till efterlevnadsinformation som genereras av dina principtilldelningar med hjälp Azure Portal, Azure-kommandoradsverktyg eller Azure Policy-SDK:er. Mer information finns i [Hämta efterlevnadsdata för Azure-resurser.](../governance/policy/how-to/get-compliance-data.md)

När en resurs inte är kompatibel finns det många möjliga orsaker. Om du vill ta reda på orsaken eller hitta den ansvariga ändringen kan du gå [till Fastställa bristande efterlevnad.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Principefterlevnad i portalen:

1. Välj **Alla tjänster** och sök efter **Princip.**
1. Välj **Efterlevnad.**
1. Använd filtren för att begränsa kompatibilitets tillstånd eller för att söka efter principer
   
    [![Principefterlevnad i portalen ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Välj en princip för att granska sammanställd efterlevnadsinformation och händelser. Om du vill väljer du en specifik SignalR för resursefterlevnad.

### <a name="policy-compliance-in-the-azure-cli"></a>Principefterlevnad i Azure CLI

Du kan också använda Azure CLI för att hämta efterlevnadsdata. Använd till exempel kommandot [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) i CLI för att hämta princip-ID:erna för de principer Azure SignalR Service som tillämpas:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Exempel på utdata:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Kör sedan [az policy state list för](/cli/azure/policy/state#az_policy_state_list) att returnera det JSON-formaterade kompatibilitetstillståndet för alla resurser under en specifik resursgrupp:

```azurecli
az policy state list --g <resourceGroup>
```

Eller kör [az policy state list för](/cli/azure/policy/state#az_policy_state_list) att returnera det JSON-formaterade kompatibilitetstillståndet för en specifik SignalR-resurs:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Policy [definitioner](../governance/policy/concepts/definition-structure.md) och [effekter](../governance/policy/concepts/effects.md)

* Skapa en [anpassad principdefinition](../governance/policy/tutorials/create-custom-policy-definition.md)

* Läs mer om [styrningsfunktioner](../governance/index.yml) i Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/