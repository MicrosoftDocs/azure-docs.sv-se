---
title: Självstudie – distribuera en länkad mall
description: Lär dig hur du distribuerar en länkad mall
ms.date: 03/13/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 81a8b6cd5044b31ee80f50448d6610ba6a57281e
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585657"
---
# <a name="tutorial-deploy-a-linked-template"></a>Självstudie: Distribuera en länkad mall

I de [föregående självstudierna](./deployment-tutorial-local-template.md)har du lärt dig hur du distribuerar en mall som lagras på din lokala dator. Om du vill distribuera komplexa lösningar kan du dela upp en mall i flera mallar och distribuera dessa mallar via en huvud-mall. I den här självstudien får du lära dig hur du distribuerar en huvud-mall som innehåller referensen till en länkad mall. När huvudmallen distribueras utlöser den den länkade mallens distribution. Du lär dig också hur du lagrar och skyddar den länkade mallen med hjälp av SAS-token. Det tar ungefär **12 minuter** att slutföra.

## <a name="prerequisites"></a>Krav

Vi rekommenderar att du slutför den föregående själv studie kursen, men det är inget krav.

## <a name="review-template"></a>Granska mall

I de föregående självstudierna distribuerar du en mall som skapar ett lagrings konto, App Service plan och en webbapp. Den mall som användes var:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/local-template/azuredeploy.json":::

## <a name="create-a-linked-template"></a>Skapa en länkad mall

Du kan dela upp lagrings konto resursen i en länkad mall:

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/linkedStorageAccount.json":::

Följande mall är huvud mal len. Det markerade `Microsoft.Resources/deployments` objektet visar hur du anropar en länkad mall. Den länkade mallen kan inte lagras som en lokal fil eller en fil som bara är tillgänglig i det lokala nätverket. Du kan bara ange ett URI-värde som innehåller antingen HTTP eller HTTPS. Resource Manager måste kunna komma åt mallen. Ett alternativ är att placera din länkade mall i ett lagrings konto och använda URI: n för objektet. URI: n skickas till mallen med hjälp av en parameter. Se den markerade parameter definitionen.

:::code language="json" source="~/resourcemanager-templates/get-started-deployment/linked-template/azuredeploy.json" highlight="27-32,40-58":::

Spara en kopia av huvudmallen på din lokala dator med _. JSON_ -tillägget, till exempel _azuredeploy.jspå_. Du behöver inte spara en kopia av den länkade mallen. Den länkade mallen kommer att kopieras från en GitHub-lagringsplats till ett lagrings konto.

## <a name="store-the-linked-template"></a>Lagra den länkade mallen

Följande PowerShell-skript skapar ett lagrings konto, skapar en behållare och kopierar den länkade mallen från en GitHub-lagringsplats till behållaren. En kopia av den länkade mallen lagras i [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json).

Välj **försök –** öppna Cloud Shell, Välj **Kopiera** för att kopiera PowerShell-skriptet och högerklicka på Shell-fönstret för att klistra in skriptet:

> [!IMPORTANT]
> Lagrings konto namn måste innehålla mellan 3 och 24 tecken och får inte innehålla siffror och gemener. Namnet måste vara unikt. I mallen är lagrings kontots namn det projekt namn där **Store** lagts till och projekt namnet måste innehålla mellan 3 och 11 tecken. Projekt namnet måste uppfylla kraven på lagrings kontots namn och innehålla färre än 11 tecken.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectName + "rg"
$storageAccountName = $projectName + "store"
$containerName = "templates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/get-started-deployment/linked-template/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

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

# Upload the template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

Write-Host "Press [ENTER] to continue ..."
```

## <a name="deploy-template"></a>Distribuera mallen

Om du vill distribuera en privat mall i ett lagrings konto genererar du en SAS-token och inkluderar den i URI: n för mallen. Ange förfallo tid för att tillåta tillräckligt med tid för att slutföra distributionen. Bloben som innehåller mallen är bara tillgänglig för kontots ägare. Men när du skapar en SAS-token för blobben, är blobben tillgänglig för alla med denna URI. Om en annan användare fångar upp URI: n, kan användaren komma åt mallen. En SAS-token är ett bra sätt att begränsa åtkomsten till dina mallar, men du bör inte inkludera känsliga data som lösen ord direkt i mallen.

Om du inte har skapat resurs gruppen, se [skapa resurs grupp](./deployment-tutorial-local-template.md#create-resource-group).

> [!NOTE]
> I följande Azure CLI-kod `date` är parametern `-d` ett ogiltigt argument i MacOS. Så att macOS-användare lägger till 2 timmar till den aktuella tiden i terminalen på macOS som du bör använda `-v+2H` .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell

$projectName = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$templateFile = Read-Host -Prompt "Enter the main template file and path"

$resourceGroupName="${projectName}rg"
$storageAccountName="${projectName}store"
$containerName = "templates"
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

$key = (Get-AzStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName).Value[0]
$context = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $key

# Generate a SAS token
$linkedTemplateUri = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(2.0) `
    -FullUri

# Deploy the template
New-AzResourceGroupDeployment `
  -Name DeployLinkedTemplate `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile $templateFile `
  -projectName $projectName `
  -linkedTemplateUri $linkedTemplateUri `
  -verbose
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli

echo "Enter a project name that is used to generate resource names:"
read projectName
echo "Enter the main template file:"
read templateFile

resourceGroupName="${projectName}rg"
storageAccountName="${projectName}store"
containerName="templates"
fileName="linkedStorageAccount.json"

key=$(az storage account keys list -g $resourceGroupName -n $storageAccountName --query [0].value -o tsv)

linkedTemplateUri=$(az storage blob generate-sas \
  --account-name $storageAccountName \
  --account-key $key \
  --container-name $containerName \
  --name $fileName \
  --permissions r \
  --expiry `date -u -d "120 minutes" '+%Y-%m-%dT%H:%MZ'` \
  --full-uri)

linkedTemplateUri=$(echo $linkedTemplateUri | sed 's/"//g')
az deployment group create \
  --name DeployLinkedTemplate \
  --resource-group $resourceGroupName \
  --template-file $templateFile \
  --parameters projectName=$projectName linkedTemplateUri=$linkedTemplateUri \
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
