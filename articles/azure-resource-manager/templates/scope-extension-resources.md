---
title: Omfattning av resurs typer för tillägg
description: Beskriver hur du använder egenskapen omfattning när du distribuerar tillägg resurs typer.
ms.topic: conceptual
ms.date: 01/13/2021
ms.openlocfilehash: ce08ca951e24c1c0a5450052cf814a68888837c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492169"
---
# <a name="setting-scope-for-extension-resources-in-arm-templates"></a>Ange omfång för tilläggs resurser i ARM-mallar

En tilläggs resurs är en resurs som ändrar en annan resurs. Du kan till exempel tilldela en roll till en resurs. Roll tilldelningen är en tilläggs resurs typ.

En fullständig lista över resurs typer för tillägg finns i [resurs typer som utökar funktionerna i andra resurser](../management/extension-resource-types.md).

Den här artikeln visar hur du ställer in omfånget för en tilläggs resurs typ när den distribueras med en Azure Resource Manager-mall (ARM-mall). Den beskriver omfattnings egenskapen som är tillgänglig för tilläggs resurser när den tillämpas på en resurs.

> [!NOTE]
> Egenskapen omfattning är bara tillgänglig för resurs typer av typen tillägg. Om du vill ange ett annat omfång för en resurs typ som inte är en tilläggs typ, använder du en kapslad eller länkad distribution. Mer information finns i [distributioner av resurs grupper](deploy-to-resource-group.md), [prenumerations distributioner](deploy-to-subscription.md), [distributioner av hanterings grupper](deploy-to-management-group.md)och [klient distributioner](deploy-to-tenant.md).

## <a name="apply-at-deployment-scope"></a>Tillämpa vid distributions omfång

Om du vill använda en tilläggs resurs typ vid mål distributions omfånget lägger du till resursen i mallen, precis som med vilken resurs typ som helst. Tillgängliga omfattningar är [resurs grupp](deploy-to-resource-group.md), [prenumeration](deploy-to-subscription.md), [hanterings grupp](deploy-to-management-group.md)och [klient organisation](deploy-to-tenant.md). Distributions omfånget måste ha stöd för resurs typen.

Följande mall distribuerar ett lås.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/locktargetscope.json":::

När den distribueras till en resurs grupp låser den resurs gruppen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
 New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/locktargetscope.json"
```

---

I nästa exempel tilldelas en roll.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/roletargetscope.json":::

När den distribueras till en prenumeration tilldelar den rollen till prenumerationen.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az deployment sub create \
  --name demoSubDeployment \
  --location centralus \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzSubscriptionDeployment `
  -Name demoSubDeployment `
  -Location centralus `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/scope/roletargetscope.json"
```

---

## <a name="apply-to-resource"></a>Använd för resurs

Använd egenskapen för att tillämpa en tilläggs resurs till en resurs `scope` . Ange egenskapen omfattning till namnet på den resurs som du vill lägga till tillägget i. Egenskapen omfattning är en rot egenskap för resurs typen tillägg.

I följande exempel skapas ett lagrings konto och en roll tillämpas.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/storageandrole.json" highlight="56":::

## <a name="next-steps"></a>Nästa steg

* Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Tips om hur du löser vanliga distributions fel finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat arm-mall med SAS-token](secure-template-with-sas-token.md).
