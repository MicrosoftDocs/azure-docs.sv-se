---
title: Distribuera resurser med Azure CLI och en mall
description: Använd Azure Resource Manager och Azure CLI för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager mall eller en Bicep-fil.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: f616a40f2683268f0cc26314fcc88ecca23bdbcf
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782071"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Distribuera resurser med ARM-mallar och Azure CLI

Den här artikeln beskriver hur du använder Azure CLI med Azure Resource Manager -mallar (ARM-mallar) eller Bicep-filer för att distribuera dina resurser till Azure. Om du inte är bekant med begreppen för att distribuera och hantera dina Azure-lösningar kan du gå till Översikt över [malldistribution](overview.md) eller [Bicep-översikt.](bicep-overview.md)

Distributionskommandona har ändrats i Azure CLI version 2.2.0. Exemplen i den här artikeln kräver Azure CLI version 2.2.0 eller senare. Om du vill distribuera Bicep-filer behöver [du Azure CLI version 2.20.0 eller senare.](/cli/azure/install-azure-cli)

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Om du inte har Installerat Azure CLI kan du använda Azure Cloud Shell. Mer information finns i Distribuera [ARM-mallar från Azure Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Distributionsomfång

Du kan rikta distributionen till en resursgrupp, prenumeration, hanteringsgrupp eller klientorganisation. Beroende på distributionens omfattning använder du olika kommandon.

* Om du vill distribuera **till en resursgrupp** använder [du az deployment group create](/cli/azure/deployment/group#az_deployment_group_create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template-or-bicep>
  ```

* Om du vill distribuera till **en prenumeration** använder du az deployment [sub create](/cli/azure/deployment/sub#az_deployment_sub_create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Mer information om distributioner på prenumerationsnivå finns [i Skapa resursgrupper och resurser på prenumerationsnivå.](deploy-to-subscription.md)

* Om du vill distribuera **till en hanteringsgrupp** använder [du az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Mer information om distributioner på hanteringsgruppsnivå finns [i Skapa resurser på hanteringsgruppsnivå.](deploy-to-management-group.md)

* Om du vill distribuera till **en klient** använder du az deployment [tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Mer information om distributioner på klientorganisationsnivå finns [i Skapa resurser på klientorganisationsnivå.](deploy-to-tenant.md)

För varje omfång måste användaren som distribuerar mallen eller Bicep-filen ha de behörigheter som krävs för att skapa resurser.

## <a name="deploy-local-template-or-bicep-file"></a>Distribuera lokal mall eller Bicep-fil

Du kan distribuera en mall från din lokala dator eller en som lagras externt. I det här avsnittet beskrivs hur du distribuerar en lokal mall.

Om du distribuerar till en resursgrupp som inte finns skapar du resursgruppen. Namnet på resursgruppen får bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Namnet får inte sluta inom en punkt.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

Om du vill distribuera en lokal mall eller Bicep-fil använder du `--template-file` parametern i distributionskommandot. I följande exempel visas också hur du anger ett parametervärde.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Det kan ta några minuter att slutföra distributionen. När den är klar visas ett meddelande som innehåller resultatet:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuera fjärrmall

> [!NOTE]
> Azure CLI stöder för närvarande inte distribution av bicep-fjärrfiler. Använd [Bicep CLI för](./bicep-install.md#development-environment) att kompilera Bicep-filen till en JSON-mall och läs sedan in JSON-filen till fjärrplatsen.

I stället för att lagra ARM-mallar på den lokala datorn kanske du föredrar att lagra dem på en extern plats. Du kan lagra mallar på en lagringsplats för versionskontroll (till exempel GitHub). Eller så kan du lagra dem i ett Azure Storage-konto för delad åtkomst i din organisation.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Om du distribuerar till en resursgrupp som inte finns skapar du resursgruppen. Namnet på resursgruppen får bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Namnet får inte sluta inom en punkt.

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

Föregående exempel kräver en offentligt tillgänglig URI för mallen, som fungerar för de flesta scenarier eftersom mallen inte ska innehålla känsliga data. Om du behöver ange känsliga data (till exempel ett administratörslösenord) skickar du det värdet som en säker parameter. Men om du vill hantera åtkomsten till mallen bör du överväga att använda [mallspecifikt .](#deploy-template-spec)

Om du vill distribuera fjärranslutna länkade mallar med relativ sökväg som lagras i ett lagringskonto använder du `query-string` för att ange SAS-token:

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

Mer information finns i Använda [relativ sökväg för länkade mallar.](./linked-templates.md#linked-template)

## <a name="deployment-name"></a>Distributionsnamn

När du distribuerar en ARM-mall kan du ge distributionen ett namn. Det här namnet kan hjälpa dig att hämta distributionen från distributionshistoriken. Om du inte anger ett namn för distributionen används namnet på mallfilen. Om du till exempel distribuerar en mall `azuredeploy.json` med namnet och inte anger ett distributionsnamn får distributionen namnet `azuredeploy` .

Varje gång du kör en distribution läggs en post till i resursgruppens distributionshistorik med distributionsnamnet. Om du kör en annan distribution och ger den samma namn ersätts den tidigare posten med den aktuella distributionen. Om du vill behålla unika poster i distributionshistoriken ger du varje distribution ett unikt namn.

Om du vill skapa ett unikt namn kan du tilldela ett slumptal.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Du kan också lägga till ett datumvärde.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Om du kör samtidiga distributioner till samma resursgrupp med samma distributionsnamn slutförs bara den senaste distributionen. Alla distributioner med samma namn som inte har slutförts ersätts av den senaste distributionen. Om du till exempel kör en distribution med namnet som distribuerar ett lagringskonto med namnet och samtidigt kör en annan distribution med namnet som distribuerar ett lagringskonto med namnet , distribuerar du bara ett `newStorage` `storage1` `newStorage` `storage2` lagringskonto. Det resulterande lagringskontot heter `storage2` .

Men om du kör en distribution med namnet som distribuerar ett lagringskonto med namnet och omedelbart efter att den är klar kör du en annan distribution med namnet som distribuerar ett lagringskonto med namnet , så har `newStorage` `storage1` du två `newStorage` `storage2` lagringskonton. Den ena `storage1` heter och den andra heter `storage2` . Men du har bara en post i distributionshistoriken.

När du anger ett unikt namn för varje distribution kan du köra dem samtidigt utan konflikter. Om du kör en distribution med namnet som distribuerar ett lagringskonto med namnet och samtidigt kör en annan distribution med namnet som distribuerar ett lagringskonto med namnet har du två lagringskonton och två poster i `newStorage1` `storage1` `newStorage2` `storage2` distributionshistoriken.

För att undvika konflikter med samtidiga distributioner och för att säkerställa unika poster i distributionshistoriken ger du varje distribution ett unikt namn.

## <a name="deploy-template-spec"></a>Distribuera mallspecifikt

> [!NOTE]
> Azure CLI stöder för närvarande inte skapande av mallspecifikter genom att tillhandahålla Bicep-filer. Du kan dock skapa en Bicep-fil med [resursen Microsoft.Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) för att distribuera en mallspecifikt. Här är ett [exempel.](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep)

I stället för att distribuera en lokal mall eller en fjärrmall kan du skapa en [mallspecifikt](template-specs.md). Mallspecifikationen är en resurs i din Azure-prenumeration som innehåller en ARM-mall. Det gör det enkelt att på ett säkert sätt dela mallen med användare i din organisation. Du använder rollbaserad åtkomstkontroll i Azure (Azure RBAC) för att bevilja åtkomst till mallspecifikationen. Den här funktionen är för närvarande i förhandsversion.

I följande exempel visas hur du skapar och distribuerar en mallspecifikt.

Skapa först mallspecifikationen genom att tillhandahålla ARM-mallen.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Hämta sedan ID:t för mallspecifikt och distribuera det.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Mer information finns i [Azure Resource Manager mallspecifikter (förhandsversion).](template-specs.md)

## <a name="preview-changes"></a>Förhandsgranska ändringar

Innan du distribuerar mallen kan du förhandsgranska de ändringar som mallen kommer att göra i din miljö. Använd vad [om-åtgärden för](template-deploy-what-if.md) att verifiera att mallen gör de ändringar som du förväntar dig. What-if validerar även mallen för fel.

## <a name="parameters"></a>Parametrar

Om du vill skicka parametervärden kan du använda antingen infogade parametrar eller en parameterfil.

### <a name="inline-parameters"></a>Infogade parametrar

Om du vill skicka infogade parametrar anger du värdena i `parameters` . Om du till exempel vill skicka en sträng och matris till en mall i ett Bash-gränssnitt använder du:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Om du använder Azure CLI med Windows-kommandotolk (CMD) eller PowerShell skickar du matrisen i formatet: `exampleArray="['value1','value2']"` .

Du kan också hämta innehållet i filen och ange innehållet som en infogade parameter.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Att hämta ett parametervärde från en fil är användbart när du behöver ange konfigurationsvärden. Du kan till exempel ange [cloud-init-värden för en virtuell Linux-dator.](../../virtual-machines/linux/using-cloud-init.md)

Formatet _arrayContent.jsär:_

```json
[
    "value1",
    "value2"
]
```

Om du till exempel vill skicka in ett objekt för att ange taggar använder du JSON. Mallen kan till exempel innehålla en parameter som den här:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

I det här fallet kan du skicka in en JSON-sträng för att ange parametern enligt följande Bash-skript:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Använd dubbla citattecken runt den JSON som du vill skicka till objektet.

### <a name="parameter-files"></a>Parameterfiler

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara lättare att använda en JSON-fil som innehåller parametervärdena. Parameterfilen måste vara en lokal fil. Externa parameterfiler stöds inte med Azure CLI. Både ARM-mallen och Bicep-filen använder JSON-parameterfiler.

Mer information om parameterfilen finns i [Skapa en parameterfil för Resource Manager](parameter-files.md).

Om du vill skicka en lokal parameterfil använder `@` du för att ange en lokal filstorage.parameters.js _på_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Hantera utökat JSON-format

Om du vill distribuera en mall med flerradssträngar eller kommentarer med hjälp av Azure CLI med version 2.3.0 eller äldre måste du använda `--handle-extended-json-format` växeln .  Exempel:

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

* Om du vill återställa till en lyckad distribution när du får ett fel kan du se [Återställning vid fel till lyckad distribution.](rollback-on-error.md)
* Information om hur du hanterar resurser som finns i resursgruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributionslägen](deployment-modes.md).
* Information om hur du definierar parametrar i mallen finns i [Förstå strukturen och syntaxen för ARM-mallar.](template-syntax.md)
* Tips om hur du löser vanliga distributionsfel finns i [Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).
