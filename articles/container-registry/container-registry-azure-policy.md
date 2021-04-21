---
title: Efterlevnad med Azure Policy
description: Tilldela inbyggda principer i Azure Policy för att granska efterlevnad för dina Azure-containerregister
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 62a1fd8d3c996fd3a0bac3cadf77fc7e7ace0ce3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784181"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Granska efterlevnad för Azure-containerregister med hjälp av Azure Policy

[Azure Policy](../governance/policy/overview.md) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. De här principerna tillämpar olika regler och effekter på dina resurser så att resurserna efterlever dina företagsstandarder och serviceavtal.

Den här artikeln introducerar inbyggda principer för Azure Container Registry. Använd dessa principer för att granska nya och befintliga register för efterlevnad.

Det tillkommer inga avgifter för att använda Azure Policy.

## <a name="built-in-policy-definitions"></a>Inbyggda principdefinitioner

Följande inbyggda principdefinitioner är specifika för Azure Container Registry:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Tilldela principer

* Tilldela principer med [Azure Portal,](../governance/policy/assign-policy-portal.md) [Azure CLI,](../governance/policy/assign-policy-azurecli.md) [en Resource Manager mall](../governance/policy/assign-policy-template.md)eller Azure Policy-SDK:er.
* Omfång för en principtilldelning till en resursgrupp, en prenumeration eller en [Azure-hanteringsgrupp.](../governance/management-groups/overview.md) Principtilldelningar för containerregister gäller för befintliga och nya containerregister inom omfånget.
* Aktivera eller inaktivera [principtvingande](../governance/policy/concepts/assignment-structure.md#enforcement-mode) när som helst.

> [!NOTE]
> När du har tilldelar eller uppdaterar en princip tar det lite tid innan tilldelningen tillämpas på resurser i det definierade omfånget. Se information om [utlösare för principutvärdering.](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers)

## <a name="review-policy-compliance"></a>Granska principefterlevnad

Få åtkomst till efterlevnadsinformation som genereras av dina principtilldelningar med hjälp Azure Portal, Azure-kommandoradsverktyg eller Azure Policy-SDK:er. Mer information finns i [Hämta efterlevnadsdata för Azure-resurser.](../governance/policy/how-to/get-compliance-data.md)

När en resurs inte är kompatibel finns det många möjliga orsaker. Om du vill ta reda på orsaken eller hitta den ansvariga ändringen kan du gå [till Fastställa bristande efterlevnad.](../governance/policy/how-to/determine-non-compliance.md)

### <a name="policy-compliance-in-the-portal"></a>Principefterlevnad i portalen:

1. Välj **Alla tjänster** och sök efter **Princip.**
1. Välj **Efterlevnad.**
1. Använd filtren för att begränsa kompatibilitets tillstånd eller för att söka efter principer.

    ![Principefterlevnad i portalen](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Välj en princip för att granska sammanställd efterlevnadsinformation och händelser. Om du vill väljer du ett specifikt register för resursefterlevnad.

### <a name="policy-compliance-in-the-azure-cli"></a>Principefterlevnad i Azure CLI

Du kan också använda Azure CLI för att hämta efterlevnadsdata. Använd till exempel kommandot [az policy assignment list](/cli/azure/policy/assignment#az_policy_assignment_list) i CLI för att hämta princip-ID:erna för de principer Azure Container Registry som tillämpas:

```azurecli
az policy assignment list --query "[?contains(displayName,'Container Registries')].{name:displayName, ID:id}" --output table
```

Exempel på utdata:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
Container Registries should not allow unrestricted network access           /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/b4faf132dc344b84ba68a441
Container Registries should be encrypted with a Customer-Managed Key (CMK)  /subscriptions/<subscriptionID>/providers/Microsoft.Authorization/policyAssignments/cce1ed4f38a147ad994ab60a
```

Kör sedan [az policy state list för](/cli/azure/policy/state#az_policy_state_list) att returnera det JSON-formaterade kompatibilitetstillståndet för alla resurser under ett specifikt princip-ID:

```azurecli
az policy state list \
  --resource <policyID>
```

Eller kör [az policy state list för](/cli/azure/policy/state#az_policy_state_list) att returnera det JSON-formaterade kompatibilitetstillståndet för en specifik registerresurs, till exempel *myregistry*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om Azure Policy [och](../governance/policy/concepts/definition-structure.md) [effekter](../governance/policy/concepts/effects.md).

* Skapa en [anpassad principdefinition.](../governance/policy/tutorials/create-custom-policy-definition.md)

* Läs mer om [styrningsfunktioner](../governance/index.yml) i Azure.
