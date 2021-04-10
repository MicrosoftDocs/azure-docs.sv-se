---
title: Efterlevnad med Azure Policy
description: Tilldela inbyggda principer i Azure Policy för att granska kompatibiliteten för dina Azure Container register
ms.topic: article
ms.date: 03/01/2021
ms.openlocfilehash: 0fed0c4132043e1eaed7e634e1f45b27f7c6e933
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014306"
---
# <a name="audit-compliance-of-azure-container-registries-using-azure-policy"></a>Granska kompatibilitet för Azure Container register med hjälp av Azure Policy

[Azure policy](../governance/policy/overview.md) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. De här principerna tillämpar olika regler och effekter på dina resurser så att resurserna efterlever dina företagsstandarder och serviceavtal.

I den här artikeln beskrivs inbyggda principer för Azure Container Registry. Använd dessa principer för att granska nya och befintliga register för efterlevnad.

Det kostar inget att använda Azure Policy.

## <a name="built-in-policy-definitions"></a>Inbyggda princip definitioner

Följande inbyggda princip definitioner är bara för Azure Container Registry:

[!INCLUDE [azure-policy-reference-rp-containerreg](../../includes/policy/reference/byrp/microsoft.containerregistry.md)]

## <a name="assign-policies"></a>Tilldela principer

* Tilldela principer med hjälp av [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), en [Resource Manager-mall](../governance/policy/assign-policy-template.md)eller Azure policy SDK: er.
* Begränsa en princip tilldelning till en resurs grupp, en prenumeration eller en [Azure-hanteringsserver](../governance/management-groups/overview.md). Princip tilldelningar för container registret gäller befintliga och nya behållar register i omfånget.
* Aktivera eller inaktivera [princip tillämpning](../governance/policy/concepts/assignment-structure.md#enforcement-mode) när som helst.

> [!NOTE]
> När du har tilldelat eller uppdaterat en princip tar det lite tid för tilldelningen att tillämpas på resurser i det definierade omfånget. Se information om [princip utvärderings utlösare](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Granska efterlevnad av policy

Åtkomst till kompatibilitetsinformation som genereras av dina princip tilldelningar med hjälp av Azure Portal, Azures kommando rads verktyg eller Azure Policy SDK: er. Mer information finns i [Hämta kompatibilitetstillstånd för Azure-resurser](../governance/policy/how-to/get-compliance-data.md).

När en resurs är icke-kompatibel finns det många möjliga orsaker. Information om hur du avgör orsaken eller hur du hittar den ansvarige ändrings funktionen finns i [bestämma inkompatibilitet](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Efterlevnadsprincip i portalen:

1. Välj **alla tjänster** och Sök efter **princip**.
1. Välj **efterlevnad**.
1. Använd filtren för att begränsa kompatibilitetstillstånd eller söka efter principer.

    ![Efterlevnad av principer i portalen](./media/container-registry-azure-policy/azure-policy-compliance.png)
    
1. Välj en princip för att granska sammanställd information om efterlevnad och händelser. Om du vill kan du välja ett visst register för resursens efterlevnad.

### <a name="policy-compliance-in-the-azure-cli"></a>Efterlevnad av principer i Azure CLI

Du kan också använda Azure CLI för att hämta efterlevnads data. Använd exempelvis kommandot [AZ policy Assignment List](/cli/azure/policy/assignment#az-policy-assignment-list) i CLI för att hämta princip-ID: n för de Azure Container Registry principer som tillämpas:

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

Kör sedan [AZ policy State List](/cli/azure/policy/state#az-policy-state-list) för att returnera det JSON-formaterade kompatibilitetstillstånd för alla resurser under ett angivet princip-ID:

```azurecli
az policy state list \
  --resource <policyID>
```

Eller kör [AZ policy State List](/cli/azure/policy/state#az-policy-state-list) för att returnera det JSON-formaterade kompatibilitetstillstånd för en speciell register resurs, till exempel *registret*:

```azurecli
az policy state list \
 --resource myregistry \
 --namespace Microsoft.ContainerRegistry \
 --resource-type registries \
 --resource-group myresourcegroup
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att Azure Policy [definitioner](../governance/policy/concepts/definition-structure.md) och [effekter](../governance/policy/concepts/effects.md).

* Skapa en [anpassad princip definition](../governance/policy/tutorials/create-custom-policy-definition.md).

* Lär dig mer om [styrnings funktioner](../governance/index.yml) i Azure.
