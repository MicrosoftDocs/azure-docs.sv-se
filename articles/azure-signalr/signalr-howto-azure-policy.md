---
title: Efterlevnad med Azure Policy
description: Tilldela inbyggda principer i Azure Policy om du vill granska kompatibiliteten för Azure SignalR service-resurserna.
author: JialinXin
ms.service: signalr
ms.topic: conceptual
ms.date: 06/17/2020
ms.author: jixin
ms.openlocfilehash: 018033d3a6123948191a7261f5a1ee2ae526e25a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89295035"
---
# <a name="audit-compliance-of-azure-signalr-service-resources-using-azure-policy"></a>Granska kompatibilitet för Azure SignalR service-resurser med hjälp av Azure Policy

[Azure policy](../governance/policy/overview.md) är en tjänst i Azure som du använder för att skapa, tilldela och hantera principer. De här principerna tillämpar olika regler och effekter på dina resurser så att resurserna efterlever dina företagsstandarder och serviceavtal.

I den här artikeln beskrivs inbyggda principer (för hands version) för Azure SignalR-tjänsten. Använd dessa principer för att granska nya och befintliga Signals-resurser för efterlevnad.

Det kostar inget att använda Azure Policy.

## <a name="built-in-policy-definitions"></a>Inbyggda princip definitioner

Följande inbyggda princip definitioner är bara för Azure SignalR-tjänsten:

[!INCLUDE [azure-policy-reference-policies-signalr](../../includes/policy/reference/bycat/policies-signalr.md)]

## <a name="assign-policy-definitions"></a>Tilldela princip definitioner

* Tilldela princip definitioner med hjälp av [Azure Portal](../governance/policy/assign-policy-portal.md), [Azure CLI](../governance/policy/assign-policy-azurecli.md), en [Resource Manager-mall](../governance/policy/assign-policy-template.md)eller Azure policy SDK: er.
* Begränsa en princip tilldelning till en resurs grupp, en prenumeration eller en [Azure-hanteringsserver](../governance/management-groups/overview.md). Princip tilldelningar för SignalR gäller befintliga och nya signal resurser i omfånget.
* Aktivera eller inaktivera [princip tillämpning](../governance/policy/concepts/assignment-structure.md#enforcement-mode) när som helst.

> [!NOTE]
> När du har tilldelat eller uppdaterat en princip tar det lite tid för tilldelningen att tillämpas på resurser i det definierade omfånget. Se information om [princip utvärderings utlösare](../governance/policy/how-to/get-compliance-data.md#evaluation-triggers).

## <a name="review-policy-compliance"></a>Granska efterlevnad av policy

Åtkomst till kompatibilitetsinformation som genereras av dina princip tilldelningar med hjälp av Azure Portal, Azures kommando rads verktyg eller Azure Policy SDK: er. Mer information finns i [Hämta kompatibilitetstillstånd för Azure-resurser](../governance/policy/how-to/get-compliance-data.md).

När en resurs är icke-kompatibel finns det många möjliga orsaker. Information om hur du avgör orsaken eller hur du hittar den ansvarige ändrings funktionen finns i [bestämma inkompatibilitet](../governance/policy/how-to/determine-non-compliance.md).

### <a name="policy-compliance-in-the-portal"></a>Efterlevnadsprincip i portalen:

1. Välj **alla tjänster** och Sök efter **princip**.
1. Välj **efterlevnad**.
1. Använd filtren för att begränsa kompatibilitetstillstånd eller söka efter principer
   
    [![Efterlevnad av principer i portalen ](./media/signalr-howto-azure-policy/azure-policy-compliance.png)](./media/signalr-howto-azure-policy/azure-policy-compliance.png#lightbox)
2. Välj en princip för att granska sammanställd information om efterlevnad och händelser. Om du vill kan du välja en speciell signal för resursens efterlevnad.

### <a name="policy-compliance-in-the-azure-cli"></a>Efterlevnad av principer i Azure CLI

Du kan också använda Azure CLI för att hämta efterlevnads data. Använd till exempel kommandot [AZ princip tilldelnings lista](/cli/azure/policy/assignment#az-policy-assignment-list) i CLI för att hämta princip-ID: n för Azure SignalR service-principer som tillämpas:

```azurecli
az policy assignment list --query "[?contains(displayName,'SignalR')].{name:displayName, ID:id}" --output table
```

Exempel på utdata:

```
Name                                                                                   ID
-------------------------------------------------------------------------------------  --------------------------------------------------------------------------------------------------------------------------------
[Preview]: Azure SignalR Service should use private links  /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Authorization/policyAssignments/<assignmentId>
```

Kör sedan [AZ policy State List](/cli/azure/policy/state#az-policy-state-list) för att returnera det JSON-formaterade kompatibilitetstillstånd för alla resurser under en angiven resurs grupp:

```azurecli
az policy state list --g <resourceGroup>
```

Eller kör [AZ policy State List](/cli/azure/policy/state#az-policy-state-list) för att returnera det JSON-formaterade kompatibilitetstillstånd för en angiven Signals-resurs:

```azurecli
az policy state list \
 --resource /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.SignalRService/SignalR/<resourceName> \
 --namespace Microsoft.SignalRService \
 --resource-group <resourceGroup>
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om att Azure Policy [definitioner](../governance/policy/concepts/definition-structure.md) och [effekter](../governance/policy/concepts/effects.md)

* Skapa en [anpassad princip definition](../governance/policy/tutorials/create-custom-policy-definition.md)

* Lär dig mer om [styrnings funktioner](../governance/index.yml) i Azure


<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/