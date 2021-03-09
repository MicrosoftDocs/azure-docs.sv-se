---
title: Distribuera resurser med Azure CLI och mall
description: Använd Azure Resource Manager och Azure CLI för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall eller en bicep-fil.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: d0c48a5cf05d6cec495a7a96e181910a0849a1ac
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521704"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Distribuera resurser med ARM-mallar och Azure CLI

Den här artikeln förklarar hur du använder Azure CLI med Azure Resource Manager mallar (ARM-mallar) eller bicep-filer för att distribuera dina resurser till Azure. Om du inte är bekant med principerna för att distribuera och hantera dina Azure-lösningar kan du läsa [Översikt över mall-distribution](overview.md) eller [bicep översikt](bicep-overview.md).

Distributions kommandona ändrades i Azure CLI-version 2.2.0. Exemplen i den här artikeln kräver Azure CLI version 2.2.0 eller senare. Om du vill distribuera bicep-filer behöver du [Azure CLI-version 2.20.0 eller senare](/cli/azure/install-azure-cli).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Om du inte har Azure CLI installerat kan du använda Azure Cloud Shell. Mer information finns i [distribuera arm-mallar från Azure Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Distributions omfång

Du kan rikta distributionen till en resurs grupp, prenumeration, hanterings grupp eller klient organisation. Beroende på distributionens omfattning använder du olika kommandon.

* Om du vill distribuera till en **resurs grupp** använder du [AZ distributions grupp skapa](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template-or-bicep>
  ```

* Om du vill distribuera till en **prenumeration** använder du [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Mer information om distributioner på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md).

* Om du vill distribuera till en **hanterings grupp** använder du [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Mer information om distributioner på hanterings grupp nivå finns i [Skapa resurser på hanterings grupps nivå](deploy-to-management-group.md).

* Använd [AZ Deployment Tenant Create](/cli/azure/deployment/tenant#az-deployment-tenant-create)för att distribuera till en **klient organisation**:

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Mer information om distributioner på klient nivå finns i [Skapa resurser på klient nivå](deploy-to-tenant.md).

För varje omfång måste användaren som distribuerar mallen eller bicep-filen ha de behörigheter som krävs för att skapa resurser.

## <a name="deploy-local-template-or-bicep-file"></a>Distribuera lokal mall eller bicep-fil

Du kan distribuera en mall från den lokala datorn eller en som lagras externt. I det här avsnittet beskrivs hur du distribuerar en lokal mall.

Om du distribuerar till en resurs grupp som inte finns skapar du resurs gruppen. Namnet på resurs gruppen får bara innehålla alfanumeriska tecken, punkter, under streck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Namnet får inte sluta med en punkt.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Om du vill distribuera en lokal mall eller en bicep-fil använder du `--template-file` parametern i distributions kommandot. I följande exempel visas också hur du anger ett parameter värde.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Det kan ta några minuter att slutföra distributionen. När det är klart visas ett meddelande som innehåller resultatet:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuera fjärran sluten mall

> [!NOTE]
> För närvarande stöder inte Azure CLI distribution av bicep-filer. Om du vill distribuera en bicep-fil använder du CLI-bicep för att kompilera bicep-filen till en JSON-mall först.

I stället för att lagra ARM-mallar på den lokala datorn kanske du föredrar att lagra dem på en extern plats. Du kan lagra mallar på en lagringsplats för versionskontroll (till exempel GitHub). Eller så kan du lagra dem i ett Azure Storage-konto för delad åtkomst i din organisation.

Om du distribuerar till en resurs grupp som inte finns skapar du resurs gruppen. Namnet på resurs gruppen får bara innehålla alfanumeriska tecken, punkter, under streck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Namnet får inte sluta med en punkt.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Om du vill distribuera en extern mall använder du parametern `template-uri`.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

I föregående exempel krävs en offentligt tillgänglig URI för mallen som fungerar i de flesta fall eftersom din mall inte ska innehålla känsliga data. Om du behöver ange känsliga data (som ett administratörs lösen ord) skickar du det värdet som en säker parameter. Men om du vill hantera åtkomst till mallen kan du överväga att använda [mall-specifikationer](#deploy-template-spec).

Använd `query-string` för att ange SAS-token för att distribuera fjärranslutna länkade mallar med relativa sökvägar som lagras i ett lagrings konto:

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

Mer information finns i [Använd relativ sökväg för länkade mallar](./linked-templates.md#linked-template).

## <a name="deployment-name"></a>Distributions namn

När du distribuerar en ARM-mall kan du ge distributionen ett namn. Det här namnet kan hjälpa dig att hämta distributionen från distributions historiken. Om du inte anger något namn på distributionen används namnet på mallfilen. Om du till exempel distribuerar en mall med namnet `azuredeploy.json` och inte anger ett distributions namn, namnges distributionen `azuredeploy` .

Varje gång du kör en distribution läggs en post till i resurs gruppens distributions historik med distributions namnet. Om du kör en annan distribution och ger den samma namn ersätts den tidigare posten med den aktuella distributionen. Om du vill behålla unika poster i distributions historiken ger du varje distribution ett unikt namn.

Om du vill skapa ett unikt namn kan du tilldela ett slumpmässigt nummer.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Eller Lägg till ett datum värde.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Om du kör samtidiga distributioner till samma resurs grupp med samma distributions namn slutförs bara den senaste distributionen. Alla distributioner med samma namn som inte har avslut ATS ersätts av den senaste distributionen. Om du till exempel kör en distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage1` , och samtidigt kör en annan distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage2` , distribuerar du bara ett lagrings konto. Det resulterande lagrings kontot namnges `storage2` .

Men om du kör en distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage1` och omedelbart efter att det har slutförts, kör du en annan distribution med namnet `newStorage` som distribuerar ett lagrings konto med namnet `storage2` , så har du två lagrings konton. En är namngiven `storage1` och den andra heter `storage2` . Men du har bara en post i distributions historiken.

När du anger ett unikt namn för varje distribution kan du köra dem samtidigt utan konflikter. Om du kör en distribution med namnet `newStorage1` som distribuerar ett lagrings konto med namnet `storage1` , och samtidigt kör en annan distribution med namnet `newStorage2` som distribuerar ett lagrings konto med namnet `storage2` , har du två lagrings konton och två poster i distributions historiken.

För att undvika konflikter med samtidiga distributioner och för att säkerställa unika poster i distributions historiken ger du varje distribution ett unikt namn.

## <a name="deploy-template-spec"></a>Specifikation för att distribuera mall

> [!NOTE]
> För närvarande stöder inte Azure CLI skapandet av mall-specifikationer genom att tillhandahålla bicep-filer. Du kan dock skapa en bicep-fil med resursen [Microsoft. Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) för att distribuera en mall-specifikation. Här är ett [exempel](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep).

I stället för att distribuera en lokal mall eller en fjärran sluten mall kan du skapa en [mall-specifikation](template-specs.md). Mallen specifikation är en resurs i din Azure-prenumeration som innehåller en ARM-mall. Det gör det enkelt att på ett säkert sätt dela mallen med användare i din organisation. Du använder rollbaserad åtkomst kontroll i Azure (Azure RBAC) för att ge åtkomst till mallen specifikation. Den här funktionen är för närvarande en för hands version.

I följande exempel visas hur du skapar och distribuerar en mall-specifikation.

Börja med att skapa en malls specifikation genom att tillhandahålla ARM-mallen.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Hämta sedan ID för mall-specifikation och distribuera den.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Mer information finns i [specifikationer för Azure Resource Manager mallar (för hands version)](template-specs.md).

## <a name="preview-changes"></a>Förhandsgranska ändringar

Innan du distribuerar din mall kan du förhandsgranska de ändringar som mallen kommer att göra i din miljö. Använd [åtgärden konsekvens](template-deploy-what-if.md) för att kontrol lera att mallen gör de ändringar som du förväntar dig. Vad händer om även validerar mallen för fel.

## <a name="parameters"></a>Parametrar

Om du vill skicka parameter värden kan du använda antingen infogade parametrar eller en parameter fil.

### <a name="inline-parameters"></a>Infogade parametrar

Ange värdena i om du vill skicka in infogade parametrar `parameters` . Om du till exempel vill skicka en sträng och matris till en mall i ett bash-gränssnitt använder du:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Om du använder Azure CLI med kommando tolken i Windows (CMD) eller PowerShell skickar du matrisen i formatet: `exampleArray="['value1','value2']"` .

Du kan också hämta innehållet i filen och ange innehållet som en infogad parameter.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Att hämta ett parameter värde från en fil är användbart när du behöver ange konfigurations värden. Du kan till exempel ange [värden för Cloud-Init för en virtuell Linux-dator](../../virtual-machines/linux/using-cloud-init.md).

_arrayContent.jsi_ formatet är:

```json
[
    "value1",
    "value2"
]
```

Använd JSON för att skicka in ett objekt, till exempel för att ange taggar. Mallen kan till exempel innehålla en parameter som den här:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

I det här fallet kan du skicka in en JSON-sträng för att ange parametern som visas i följande bash-skript:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Använd dubbla citat tecken runt JSON som du vill skicka till objektet.

### <a name="parameter-files"></a>Parameter-filer

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara lättare att använda en JSON-fil som innehåller parametervärdena. Parameter filen måste vara en lokal fil. Externa parameter-filer stöds inte med Azure CLI. Både ARM-mall och bicep-fil använder JSON-parameter-filer.

Mer information om parameterfilen finns i [Skapa en parameterfil för Resource Manager](parameter-files.md).

Om du vill skicka en lokal parameter fil använder `@` du för att ange en lokal fil med namnet _storage.parameters.jspå_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Hantera utökat JSON-format

Om du vill distribuera en mall med strängar med flera rader eller kommentarer med hjälp av Azure CLI med version 2.3.0 eller äldre måste du använda `--handle-extended-json-format` växeln.  Exempel:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>Nästa steg

* Om du vill återställa till en lyckad distribution när du får ett fel, se [återställa vid fel till lyckad distribution](rollback-on-error.md).
* Information om hur du hanterar resurser som finns i resurs gruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
* Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
* Tips om hur du löser vanliga distributions fel finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](common-deployment-errors.md).
