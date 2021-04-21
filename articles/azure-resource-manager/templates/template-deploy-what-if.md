---
title: Malldistribution om
description: Bestäm vilka ändringar som ska ske för dina resurser innan du distribuerar Azure Resource Manager mall.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.openlocfilehash: 7e300f896bb11ed7c77738836f894cff41cc8bf3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781837"
---
# <a name="arm-template-deployment-what-if-operation"></a>Åtgärden what-if för distribution av ARM-mall

Innan du distribuerar Azure Resource Manager en mall (ARM-mall) kan du förhandsgranska de ändringar som kommer att ske. Azure Resource Manager innehåller vad om-åtgärden så att du kan se hur resurser kommer att ändras om du distribuerar mallen. Konsekvensgranskningen ändrar inga befintliga resurser. I stället får du se vilka ändringar som görs om du distribuerar den angivna mallen.

Du kan använda vad om-åtgärden med Azure PowerShell, Azure CLI eller REST API åtgärder. What-if stöds för distributioner på resursgrupps-, prenumerations-, hanteringsgrupps- och klientorganisationsnivå.

## <a name="install-azure-powershell-module"></a>Installera Azure PowerShell modul

Om du vill använda Vad om i PowerShell måste du ha version **4.2 eller senare av Az-modulen**.

Om du vill installera modulen använder du:

```powershell
Install-Module -Name Az -Force
```

Mer information om hur du installerar moduler finns i [Installera Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="install-azure-cli-module"></a>Installera Azure CLI-modul

Om du vill använda Vad om i Azure CLI måste du ha Azure CLI 2.14.0 eller senare. [Installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) om det behövs.

## <a name="see-results"></a>Visa resultat

När du använder vad om i PowerShell eller Azure CLI innehåller utdata färgkodade resultat som hjälper dig att se de olika typerna av ändringar.

![Resource Manager distributionsåtgärd fullresourcepayload och ändringstyper](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Textutdata är:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> Vad om-åtgärden kan inte matcha [referensfunktionen](template-functions-resource.md#reference). Varje gång du ställer in en egenskap till ett malluttryck som innehåller referensfunktionen ändras vad om rapporterar egenskapen. Det här beteendet beror på vad som händer om jämför det aktuella värdet för egenskapen (till exempel eller för ett booleskt värde) med det `true` `false` olösta malluttrycket. Dessa värden kommer naturligtvis inte att matcha. När du distribuerar mallen ändras egenskapen endast när malluttrycket matchas till ett annat värde.

## <a name="what-if-commands"></a>What-if-kommandon

### <a name="azure-powershell"></a>Azure PowerShell

Om du vill förhandsgranska ändringar innan du distribuerar en mall använder du [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) eller [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment). Lägg till `-Whatif` växlingsparametern i distributionskommandot.

* `New-AzResourceGroupDeployment -Whatif` för distributioner av resursgrupper
* `New-AzSubscriptionDeployment -Whatif` och `New-AzDeployment -Whatif` för distributioner på prenumerationsnivå

Du kan använda `-Confirm` växlingsparametern för att förhandsgranska ändringarna och uppmanas att fortsätta med distributionen.

* `New-AzResourceGroupDeployment -Confirm` för distributioner av resursgrupper
* `New-AzSubscriptionDeployment -Confirm` och `New-AzDeployment -Confirm` för distributioner på prenumerationsnivå

Föregående kommandon returnerar en textsammanfattning som du kan granska manuellt. Om du vill hämta ett objekt som du kan kontrollera programmässigt för ändringar använder du [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) eller [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult).

* `$results = Get-AzResourceGroupDeploymentWhatIfResult` för distributioner av resursgrupper
* `$results = Get-AzSubscriptionDeploymentWhatIfResult` eller `$results = Get-AzDeploymentWhatIfResult` för distributioner på prenumerationsnivå

### <a name="azure-cli"></a>Azure CLI

Om du vill förhandsgranska ändringar innan du distribuerar en mall använder du:

* [az deployment group what-if](/cli/azure/deployment/group#az_deployment_group_what_if) for resource group deployments
* [az deployment sub what-if](/cli/azure/deployment/sub#az_deployment_sub_what_if) for subscription level deployments
* [az deployment mg what-if](/cli/azure/deployment/mg#az_deployment_mg_what_if) for management group deployments
* [az deployment tenant what-if](/cli/azure/deployment/tenant#az_deployment_tenant_what_if) for tenant deployments

Du kan använda `--confirm-with-what-if` växeln (eller dess kortform ) för att förhandsgranska ändringarna och `-c` uppmanas att fortsätta med distributionen. Lägg till den här växeln i:

* [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)
* [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create).
* [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)

Du kan till exempel `az deployment group create --confirm-with-what-if` använda `-c` eller för resursgruppsdistributioner.

Föregående kommandon returnerar en textsammanfattning som du kan granska manuellt. Om du vill hämta ett JSON-objekt som du kan kontrollera programmatiskt för ändringar använder du `--no-pretty-print` växeln . Använd till exempel `az deployment group what-if --no-pretty-print` för resursgruppsdistributioner.

Om du vill returnera resultatet utan färger öppnar du Azure [CLI-konfigurationsfilen.](/cli/azure/azure-cli-configuration) Ange **no_color** till **ja.**

### <a name="azure-rest-api"></a>Azure REST API

För REST API använder du:

* [Distributioner – What If](/rest/api/resources/deployments/whatif) för distributioner av resursgrupper
* [Distributioner – What If prenumerationsomfång för](/rest/api/resources/deployments/whatifatsubscriptionscope) prenumerationsdistributioner
* [Distributioner – What If Omfång för hanteringsgrupp för](/rest/api/resources/deployments/whatifatmanagementgroupscope) distributioner av hanteringsgrupp
* [Distributioner – What If klientorganisationsomfång för](/rest/api/resources/deployments/whatifattenantscope) klientdistributioner.

## <a name="change-types"></a>Ändringstyper

Vad om-åtgärden visar sex olika typer av ändringar:

- **Skapa:** Resursen finns inte för närvarande men definieras i mallen. Resursen skapas.

- **Ta** bort: Den här ändringstypen gäller endast när [du använder fullständigt läge](deployment-modes.md) för distribution. Resursen finns, men den definieras inte i mallen. I fullständigt läge tas resursen bort. Endast resurser som stöder [borttagning i fullständigt läge](complete-mode-deletion.md) ingår i den här ändringstypen.

- **Ignorera**: Resursen finns, men har inte definierats i mallen. Resursen kommer inte att distribueras eller ändras.

- **NoChange:** Resursen finns och definieras i mallen. Resursen distribueras på nytt, men egenskaperna ändras inte. Den här ändringstypen [returneras när ResultFormat](#result-format) är inställt `FullResourcePayloads` på , vilket är standardvärdet.

- **Ändra**: Resursen finns och definieras i mallen. Resursen distribueras på nytt och resursens egenskaper ändras. Den här ändringstypen [returneras när ResultFormat](#result-format) är inställt `FullResourcePayloads` på , vilket är standardvärdet.

- **Distribuera:** Resursen finns och definieras i mallen. Resursen distribueras på nytt. Egenskaperna för resursen ändras eventuellt. Åtgärden returnerar den här ändringstypen när det inte finns tillräckligt med information för att avgöra om några egenskaper kommer att ändras. Du ser bara det här villkoret när [ResultFormat](#result-format) är inställt på `ResourceIdOnly` .

## <a name="result-format"></a>Resultatformat

Du styr detaljnivån som returneras om de förutsagda ändringarna. Du kan välja mellan två alternativ:

* **FullResourcePayloads** – returnerar en lista över resurser som kommer att ändras och information om de egenskaper som kommer att ändras
* **ResourceIdOnly** – returnerar en lista över resurser som kommer att ändras

Standardvärdet är **FullResourcePayloads**.

För PowerShell-distributionskommandon använder du `-WhatIfResultFormat` parametern . Använd parametern i programmatiska `ResultFormat` objektkommandon.

För Azure CLI använder du `--result-format` parametern .
 
Följande resultat visar de två olika utdataformaten:

- Fullständiga resursnyttolaster

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- Endast resurs-ID

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>Köra vad om-åtgärd

### <a name="set-up-environment"></a>Konfigurera miljö

För att se hur vad om fungerar kör vi några tester. Distribuera först en mall [som skapar ett virtuellt nätverk](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json). Du kommer att använda det här virtuella nätverket för att testa hur ändringar rapporteras av vad om.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>Teständring

När distributionen är klar är du redo att testa vad om-åtgärden. Den här gången distribuerar du [en mall som ändrar det virtuella nätverket](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json). Det saknas en av de ursprungliga taggarna, ett undernät har tagits bort och adressprefixet har ändrats.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

Utdata från konsekvensgranskningen bör se ut ungefär så här:

![Resource Manager utdata från en malldistributionsåtgärd](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

Textutdata är:

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

Observera att färger definieras överst i utdata för att ange typen av ändringar.

Längst ned i utdata visas taggen Ägare har tagits bort. Adressprefixet ändrades från 10.0.0.0/16 till 10.0.0.0/15. Undernätet med namnet subnet001 har tagits bort. Kom ihåg att dessa ändringar inte har distribuerats. Du ser en förhandsgranskning av de ändringar som kommer att ske om du distribuerar mallen.

Vissa av de egenskaper som listas som borttagna ändras inte i själva verket. Egenskaper kan rapporteras felaktigt som borttagna när de inte finns i mallen, men ställs automatiskt in under distributionen som standardvärden. Det här resultatet anses vara "brus" i what-if-svaret. Den slutliga distribuerade resursen har värdena inställda för egenskaperna. När vad om-åtgärden mognar filtreras dessa egenskaper bort från resultatet.

## <a name="programmatically-evaluate-what-if-results"></a>Utvärdera vad om-resultat programmässigt

Nu ska vi programmatiskt utvärdera vad om-resultatet genom att ange kommandot till en variabel.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

Du kan se en sammanfattning av varje ändring.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>Bekräfta borttagning

Vad om-åtgärden stöder användning av [distributionsläget](deployment-modes.md). När det är inställt på slutfört läge tas resurser som inte finns i mallen bort. I följande exempel distribueras en [mall som inte har några resurser definierade](https://github.com/Azure/azure-docs-json-samples/blob/master/empty-template/azuredeploy.json) i fullständigt läge.

Om du vill förhandsgranska ändringarna innan du distribuerar en mall använder du parametern confirm switch (bekräfta ändring) i distributionskommandot. Om ändringarna är som förväntat svarar du att du vill att distributionen ska slutföras.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/empty-template/azuredeploy.json"
```

---

Eftersom inga resurser har definierats i mallen och distributionsläget är inställt på att slutföras tas det virtuella nätverket bort.

![Resource Manager distributionen av vad om-åtgärdens utdatadistributionsläge har slutförts](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

Textutdata är:

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001&quot;
      location:        &quot;centralus&quot;
      name:            &quot;vnet-001&quot;
      tags.CostCenter: &quot;12345&quot;
      tags.Owner:      &quot;Team A&quot;
      type:            &quot;Microsoft.Network/virtualNetworks&quot;

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;Y"):
```

Du ser de förväntade ändringarna och kan bekräfta att du vill att distributionen ska köras.

## <a name="sdks"></a>SDK:er

Du kan använda vad om-åtgärden via Azure-SDK:erna.

* För Python använder du [vad om](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-).

* För Java använder du [DeploymentWhatIf-klassen](/java/api/com.microsoft.azure.management.resources.deploymentwhatif).

* För .NET använder du [DeploymentWhatIf-klassen](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif).

## <a name="next-steps"></a>Nästa steg

- Information om hur du använder vad om-åtgärden i en pipeline finns i [Testa ARM-mallar med What-If i en pipeline](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/).
- Om du upptäcker felaktiga resultat från vad om-åtgärden kan du rapportera problemen på [https://aka.ms/whatifissues](https://aka.ms/whatifissues) .
- En modul Microsoft Learn som beskriver hur du använder vad om finns i Förhandsgranska ändringar och validera Azure-resurser med hjälp av vad om och [ARM-mallens testverktyg.](/learn/modules/arm-template-test/)
