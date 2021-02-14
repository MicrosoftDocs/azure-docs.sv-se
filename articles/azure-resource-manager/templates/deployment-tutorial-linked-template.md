---
title: Självstudie – distribuera en länkad mall
description: Lär dig hur du distribuerar en länkad mall
ms.date: 02/12/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: b69d4e8d2748cffec6a4f0cddfa20e6722653c76
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519022"
---
# <a name="tutorial-deploy-a-linked-template"></a>Självstudie: Distribuera en länkad mall

I de [föregående självstudierna](./deployment-tutorial-local-template.md)har du lärt dig hur du distribuerar en mall som lagras på din lokala dator. Om du vill distribuera komplexa lösningar kan du dela upp en mall i flera mallar och distribuera dessa mallar via en huvud-mall. I den här självstudien får du lära dig hur du distribuerar en huvud-mall som innehåller referensen till en länkad mall. När huvudmallen distribueras utlöser den den länkade mallens distribution. Du lär dig också hur du lagrar och skyddar mallarna med hjälp av SAS-token. Det tar ungefär **12 minuter** att slutföra.

## <a name="prerequisites"></a>Förutsättningar

Vi rekommenderar att du slutför den föregående själv studie kursen, men det är inget krav.

## <a name="review-template"></a>Granska mall

I de föregående självstudierna distribuerar du en mall som skapar ett lagrings konto, App Service plan och en webbapp. Den mall som användes var:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Skapa en länkad mall

Du kan dela upp lagrings konto resursen i en länkad mall:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Följande mall är huvud mal len. Det markerade `Microsoft.Resources/deployments` objektet visar hur du anropar en länkad mall. Den länkade mallen kan inte lagras som en lokal fil eller en fil som bara är tillgänglig i det lokala nätverket. Du kan antingen ange ett URI-värde för den länkade mallen som innehåller antingen HTTP eller HTTPS eller använda egenskapen _relativePath_ för att distribuera en fjärran sluten länkad mall på en plats i förhållande till den överordnade mallen. Ett alternativ är att placera både huvud mal len och den länkade mallen i ett lagrings konto.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="34-52":::

## <a name="store-the-linked-template"></a>Lagra den länkade mallen

Båda huvud mallarna och den länkade mallen lagras i GitHub:

Följande PowerShell-skript skapar ett lagrings konto, skapar en behållare och kopierar de två mallarna från en GitHub-lagringsplats till behållaren. Dessa två mallar är:

- Huvud mal len: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json
- Den länkade mallen: https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json

Välj **försök –** öppna Cloud Shell, Välj **Kopiera** för att kopiera PowerShell-skriptet och högerklicka på Shell-fönstret för att klistra in skriptet:

> [!IMPORTANT]
> Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får inte innehålla siffror och gemener. Namnet måste vara unikt. I mallen är lagrings kontots namn det projekt namn där **Store** lagts till och projekt namnet måste innehålla mellan 3 och 11 tecken. Projekt namnet måste uppfylla kraven på lagrings kontots namn och innehålla färre än 11 tecken.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$mainTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/azuredeploy.json"
$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json"

$mainFileName = "azuredeploy.json" # A file name used for downloading and uploading the main template.Add-PSSnapin
$linkedFileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the templates
Invoke-WebRequest -Uri $mainTemplateURL -OutFile "$home/$mainFileName"
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$linkedFileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context -Permission Container

# Upload the templates
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$mainFileName" `
    -Blob $mainFileName `
    -Context $context

Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$linkedFileName" `
    -Blob $linkedFileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Distribuera mallen

Om du vill distribuera mallar i ett lagrings konto genererar du en SAS-token och anger den till parametern _-QueryString_ . Ange förfallo tid för att tillåta tillräckligt med tid för att slutföra distributionen. Blobbarna som innehåller mallarna är bara tillgängliga för kontots ägare. Men när du skapar en SAS-token för en BLOB, är blobben tillgänglig för alla med den SAS-token. Om en annan användare fångar upp URI: n och SAS-token kan den användaren komma åt mallen. En SAS-token är ett bra sätt att begränsa åtkomsten till dina mallar, men du bör inte inkludera känsliga data som lösen ord direkt i mallen.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> I följande Azure CLI-kod `date` är parametern `-d` ett ogiltigt argument i MacOS. Så att macOS-användare lägger till 2 timmar till den aktuella tiden i terminalen på macOS som du bör använda `-v+2H` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive

$projectName = Read-Host -Prompt "Enter the same project name:"   # This name is used to generate names for Azure resources, such as storage account name.

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

$mainTemplateUri = $context.BlobEndPoint + "$containerName/azuredeploy.json"
$sasToken = New-AzStorageContainerSASToken `
    -Context $context `
    -Container $containerName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0)
$newSas = $sasToken.substring(1)


New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri $mainTemplateUri `
  -QueryString $newSas `
  -projectName $projectName `
  -verbose

Write-Host "Press [ENTER] to continue ..."
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
echo "Enter a project name that is used to generate resource names:" &&
read projectName &&

resourceGroupName="${projectName}rg" &&
storageAccountName="${projectName}store" &&
containerName="templates" &&

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv) &&

sasToken=$(az storage container generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --name $containerName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'`) &&
sasToken=$(echo $sasToken | sed 's/"//g')&&

blobUri=$(az storage account show -n $storageAccountName -g $resourceGroupName -o tsv --query primaryEndpoints.blob) &&
templateUri="${blobUri}${containerName}/azuredeploy.json" &&

az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-uri $templateUri \
  --parameters projectName=$projectName \
  --query-string $sasToken \
  --verbose
```

---

## <a name="clean-up-resources"></a>Rensa resurser

Rensa de resurser som du har distribuerat genom att ta bort resurs gruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

Du har lärt dig hur du distribuerar en länkad mall. I nästa självstudie får du lära dig hur du skapar en DevOps-pipeline för att distribuera en mall.

> [!div class="nextstepaction"]
> [Skapa en pipeline](./deployment-tutorial-pipeline.md)
