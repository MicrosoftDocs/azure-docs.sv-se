---
title: Skapa och distribuera mallspecifikationer
description: Beskriver hur du skapar specifikationer för mallar och delar dem med andra användare i din organisation.
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: tomfitz
author: tfitzmac
ms.openlocfilehash: e4efc63ffa49b1c8ca44fc806e37e4aa91cd76c8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700396"
---
# <a name="azure-resource-manager-template-specs-preview"></a>Specifikationer för Azure Resource Manager mall (för hands version)

En mall specifikation är en resurs typ för att lagra en Azure Resource Manager-mall (ARM-mall) i Azure för senare distribution. Med den här resurs typen kan du dela ARM-mallar med andra användare i din organisation. Precis som med andra Azure-resurser kan du använda rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att dela mallen specifikation.

**Microsoft. Resources/templateSpecs** är resurs typen för mall-specifikationer. Det består av en huvudmall och ett valfritt antal länkade mallar. Azure sparar säkert mall-specifikationer i resurs grupper. Mall-specifikationer stöder [versions hantering](#versioning).

Om du vill distribuera en mall använder du standard Azure-verktyg som PowerShell, Azure CLI, Azure Portal, REST och andra SDK: er och klienter som stöds. Du använder samma kommandon som för mallen.

> [!NOTE]
> Mallens specifikationer är för närvarande en för hands version. Om du vill använda den med Azure PowerShell måste du installera [version 5.0.0 eller senare](/powershell/azure/install-az-ps). Använd [version 2.14.2 eller senare](/cli/azure/install-azure-cli)för att använda den med Azure CLI.

## <a name="why-use-template-specs"></a>Varför ska jag använda mall-specifikationer?

Om du för närvarande har dina mallar i ett GitHub-lagrings platsen eller lagrings konto kan du köra flera utmaningar när du försöker dela och använda mallarna. För att en användare ska kunna distribuera den måste mallen antingen vara lokal eller URL: en för mallen måste vara offentligt tillgänglig. För att komma runt den här begränsningen kan du dela kopior av mallen med användare som behöver distribuera den, eller öppna åtkomst till lagrings platsen eller lagrings kontot. När användare äger lokala kopior av en mall kan de här kopiorna gå till och med den ursprungliga mallen. När du gör en lagrings platsen eller ett lagrings konto offentligt tillgängligt kan du tillåta oönskade användare att komma åt mallen.

Fördelen med att använda mall-specifikationer är att du kan skapa kanoniska mallar och dela dem med team i din organisation. Mall-specifikationerna är säkra eftersom de är tillgängliga för att Azure Resource Manager för distribution, men inte tillgängliga för användare utan Azure RBAC-behörighet. Användare behöver bara Läs behörighet till mallen mall för att distribuera dess mall, så att du kan dela mallen utan att tillåta andra att ändra den.

Mallarna som du lägger till i en mall specifikation bör verifieras av administratörer i organisationen för att följa organisationens krav och vägledning.

## <a name="create-template-spec"></a>Skapa mall-specifikation

I följande exempel visas en enkel mall för att skapa ett lagrings konto i Azure.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

När du skapar mallen för mallen skickas filen PowerShell-eller CLI-kommandon till huvud mal len. Om huvudmallen refererar till länkade mallar, hittar kommandona dem och paketerar dem för att skapa mallen specifikation. Mer information finns i [skapa en mall-specifikation med länkade mallar](#create-a-template-spec-with-linked-templates).

Skapa en mall specifikation genom att använda:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0a -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

Du kan visa alla mall-specifikationer i din prenumeration genom att använda:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

Du kan visa information om en mall-specifikation, inklusive dess versioner med:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0a"
```

---

## <a name="deploy-template-spec"></a>Specifikation för att distribuera mall

När du har skapat specifikationen för mallen kan användare med **Läs** behörighet till mallen distribuera den. Information om hur du beviljar åtkomst finns i [Självstudier: bevilja en grupp åtkomst till Azure-resurser med hjälp av Azure PowerShell](../../role-based-access-control/tutorial-role-assignments-group-powershell.md).

Specifikationer för mallar kan distribueras via portalen, PowerShell, Azure CLI eller som en länkad mall i en större mall-distribution. Användare i en organisation kan distribuera en mall specifikation till valfri omfattning i Azure (resurs grupp, prenumeration, hanterings grupp eller klient organisation).

I stället för att skicka in en sökväg eller URI för en mall distribuerar du en mall-specifikation genom att ange dess resurs-ID. Resurs-ID: t har följande format:

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

Observera att resurs-ID: t innehåller ett versions namn för mallen specifikation.

Du kan till exempel distribuera en mall-specifikation med följande kommando.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

I praktiken kommer du vanligt vis att köra `Get-AzTemplateSpec` eller `az ts show` Hämta ID: t för den mall specifikation som du vill distribuera.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0a).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0a" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

## <a name="parameters"></a>Parametrar

Att skicka parametrar till mallkod är precis som att skicka parametrar till en ARM-mall. Lägg till parametervärdena antingen i rad eller i en parameter fil.

Om du vill skicka en parameter infogad, använder du:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

Använd följande för att skapa en lokal parameter fil:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

Och skicka parameter filen med:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="versioning"></a>Versionshantering

När du skapar en mall-specifikation anger du ett versions namn för den. När du itererar på mallkod kan du antingen uppdatera en befintlig version (för snabb korrigeringar) eller publicera en ny version. Versionen är en text sträng. Du kan välja att följa alla versioner av systemet, inklusive semantisk versions hantering. Användare av mallen specifikation kan ange det versions namn som de vill använda när de distribuerar den.

## <a name="use-tags"></a>Använd taggar

Med [taggar](../management/tag-resources.md) kan du logiskt organisera dina resurser. Du kan lägga till taggar till mall-specifikationer genom att använda Azure PowerShell och Azure CLI:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts update \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

När du skapar eller ändrar en mall-specifikation med parametern version angiven, men utan parametern tag/Taggar:

- Om mallen finns och har taggar, men versionen inte finns, ärver den nya versionen samma taggar som den befintliga specifikationen för mallen.

När du skapar eller ändrar en mall-specifikation med både taggen tag/Taggar och parametern version angiven:

- Om både mallens specifikation och versionen inte finns, läggs taggarna till i både den nya mallen och den nya versionen.
- Om mallen finns, men versionen inte finns, läggs taggarna bara till i den nya versionen.
- Om både specifikationerna för mallen och versionen finns, gäller taggarna endast för-versionen.

När du ändrar en mall med parametern tag/taggar, men utan den angivna versions parametern, läggs taggarna bara till i specifikationen för mallen.

## <a name="create-a-template-spec-with-linked-templates"></a>Skapa en mall-specifikation med länkade mallar

Om huvud mal len för din mall-spec refererar till länkade mallar kan PowerShell-och CLI-kommandona automatiskt hitta och paketera de länkade mallarna från den lokala enheten. Du behöver inte manuellt konfigurera lagrings konton eller lagrings platser som är värdar för mallens specifikationer – allt är självständigt i resursens Specifikations resurs.

Följande exempel består av en huvud-mall med två länkade mallar. Exemplet är bara utdrag ur mallen. Observera att den använder en egenskap med namnet `relativePath` för att länka till de andra mallarna. Du måste använda `apiVersion` `2020-06-01` eller senare för distributions resursen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

När PowerShell-eller CLI-kommandot för att skapa mallen körs i föregående exempel, hittar kommandot tre filer – huvud mal len, webbappens mall ( `webapp.json` ) och databas mal len ( `database.json` )-och paketerar dem i mallen specifikation.

Mer information finns i [Självstudier: skapa en mall-specifikation med länkade mallar](template-specs-create-linked.md).

## <a name="deploy-template-spec-as-a-linked-template"></a>Distribuera mallens specifikation som en länkad mall

När du har skapat en mall är det enkelt att återanvända den från en ARM-mall eller någon annan mall-specifikation. Du länkar till en mall-specifikation genom att lägga till dess resurs-ID i mallen. Den länkade mallens specifikation distribueras automatiskt när du distribuerar huvud mal len. Med det här beteendet kan du utveckla specifikationer för modulär mall och återanvända dem efter behov.

Du kan till exempel skapa en mall-specifikation som distribuerar nätverks resurser och en annan mall-specifikation som distribuerar lagrings resurser. I ARM-mallar länkar du till dessa två mallar för mallar varje gång du behöver konfigurera nätverks-eller lagrings resurser.

Följande exempel liknar det tidigare exemplet, men du använder `id` egenskapen för att länka till en mall-specifikation snarare än `relativePath` egenskapen för att länka till en lokal mall. Använd `2020-06-01` för API-versionen för distributions resursen. I exemplet finns mall-specifikationerna i en resurs grupp med namnet **templateSpecsRG**.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0a')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0a')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

Mer information om hur du länkar mall-specifikationer finns i [Självstudier: Distribuera en mall specifikation som en länkad mall](template-specs-deploy-linked-template.md).

## <a name="next-steps"></a>Nästa steg

* Information om hur du skapar och distribuerar en mall finns i [snabb start: skapa och distribuera mall specifikation](quickstart-create-template-specs.md).

* Mer information om hur du länkar mallar i specifikationer för mallar finns i [Självstudier: skapa en mall-specifikation med länkade mallar](template-specs-create-linked.md).

* Mer information om hur du distribuerar en mall specifikation som en länkad mall finns i [självstudie: Distribuera en mall specifikation som en länkad mall](template-specs-deploy-linked-template.md).
