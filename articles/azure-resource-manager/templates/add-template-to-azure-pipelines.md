---
title: CI/CD med Azure-pipeline och-mallar
description: Beskriver hur du konfigurerar kontinuerlig integrering i Azure-pipeline med hjälp av Azure Resource Manager mallar. Det visar hur du använder ett PowerShell-skript eller kopierar filer till en mellanlagringsplats och distribuerar därifrån.
ms.topic: conceptual
ms.date: 02/05/2021
ms.openlocfilehash: ea1ccac00f121bd81fd8b9b1f182b565fc53d214
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594205"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Integrera ARM-mallar med Azure Pipelines

Du kan integrera Azure Resource Manager mallar (ARM-mallar) med Azure-pipelines för kontinuerlig integrering och kontinuerlig distribution (CI/CD). I den här artikeln får du lära dig ytterligare två avancerade sätt att distribuera mallar med Azure-pipeliner.

## <a name="select-your-option"></a>Välj ditt alternativ

Innan du fortsätter med den här artikeln ska vi ta en titt på de olika alternativen för att distribuera en ARM-mall från en pipeline.

* **Använd distributions uppgift för arm-mall**. Det här alternativet är det enklaste alternativet. Den här metoden fungerar när du vill distribuera en mall direkt från en lagrings plats. Det här alternativet beskrivs inte i den här artikeln, utan beskrivs i stället i självstudien [för kontinuerlig integrering av arm-mallar med Azure-pipeliner](deployment-tutorial-pipeline.md). Det visar hur du använder [distributions uppgiften för arm-mallen](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md) för att distribuera en mall från din GitHub-lagrings platsen.

* **Lägg till aktivitet som kör ett Azure PowerShell-skript**. Det här alternativet har fördelen att tillhandahålla konsekvens under utvecklings livs cykeln eftersom du kan använda samma skript som du använde när du körde lokala tester. Skriptet distribuerar mallen men kan också utföra andra åtgärder, till exempel att hämta värden som parametrar. Det här alternativet visas i den här artikeln. Se [Azure PowerShell aktivitet](#azure-powershell-task).

   Visual Studio innehåller ett [Azure-resurs grupps projekt](create-visual-studio-deployment-project.md) som innehåller ett PowerShell-skript. Skript stegen artefakter från ditt projekt till ett lagrings konto som Resource Manager har åtkomst till. Artefakter är objekt i projektet, till exempel länkade mallar, skript och binärfiler för program. Om du vill fortsätta att använda skriptet från projektet kan du använda PowerShell-skriptet som visas i den här artikeln.

* **Lägg till aktiviteter för att kopiera och distribuera uppgifter**. Det här alternativet erbjuder ett användbart alternativ till projekt skriptet. Du konfigurerar två aktiviteter i pipelinen. En uppgift stadier artefakter till en tillgänglig plats. Den andra aktiviteten distribuerar mallen från den platsen. Det här alternativet visas i den här artikeln. Se [Kopiera och distribuera uppgifter](#copy-and-deploy-tasks).

## <a name="prepare-your-project"></a>Förbered ditt projekt

Den här artikeln förutsätter att din ARM-mall och Azure DevOps-organisationen är redo att skapa pipelinen. Följande steg visar hur du kan se till att du är redo:

* Du har en Azure DevOps-organisation. Om du inte har ett kan du [skapa ett kostnads fritt](/azure/devops/pipelines/get-started/pipelines-sign-up). Om ditt team redan har en Azure DevOps-organisation, se till att du är administratör för det Azure DevOps-projekt som du vill använda.

* Du har konfigurerat en [tjänst anslutning](/azure/devops/pipelines/library/connect-to-azure) till din Azure-prenumeration. Aktiviteterna i pipelinen körs under identiteten för tjänstens huvud namn. Anvisningar för hur du skapar anslutningen finns i [skapa ett DevOps-projekt](deployment-tutorial-pipeline.md#create-a-devops-project).

* Du har en [arm-mall](quickstart-create-templates-use-visual-studio-code.md) som definierar infrastrukturen för ditt projekt.

## <a name="create-pipeline"></a>Skapa pipeline

1. Om du inte har lagt till en pipeline tidigare måste du skapa en ny pipeline. Välj **pipeliner** och **ny pipeline** från din Azure DevOps-organisation.

   ![Lägg till ny pipeline](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Ange var koden lagras. Följande bild visar hur du väljer **Azure databaser git**.

   ![Välj kod källa](./media/add-template-to-azure-pipelines/select-source.png)

1. Från den källan väljer du den lagrings plats som innehåller koden för projektet.

   ![Välj databas](./media/add-template-to-azure-pipelines/select-repo.png)

1. Välj den typ av pipeline som ska skapas. Du kan välja **starter pipeline**.

   ![Välj pipeline](./media/add-template-to-azure-pipelines/select-pipeline.png)

Du är redo att antingen lägga till en Azure PowerShell uppgift eller kopiera filen och distribuera uppgifter.

## <a name="azure-powershell-task"></a>Azure PowerShell-uppgift

I det här avsnittet visas hur du konfigurerar kontinuerlig distribution med hjälp av en enda aktivitet som kör PowerShell-skriptet i projektet. Om du behöver ett PowerShell-skript som distribuerar en mall, se [Deploy-AzTemplate.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzTemplate.ps1) eller [Deploy-AzureResourceGroup.ps1](https://github.com/Azure/azure-quickstart-templates/blob/master/Deploy-AzureResourceGroup.ps1).

Följande YAML-fil skapar en [Azure PowerShell-aktivitet](/azure/devops/pipelines/tasks/deploy/azure-powershell):

```yml
trigger:
- master

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: AzurePowerShell@5
  inputs:
    azureSubscription: 'script-connection'
    ScriptType: 'FilePath'
    ScriptPath: './Deploy-AzTemplate.ps1'
    ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
    azurePowerShellVersion: 'LatestVersion'
```

När du anger uppgiften till `AzurePowerShell@5` använder pipelinen [AZ-modulen](/powershell/azure/new-azureps-module-az). Om du använder AzureRM-modulen i skriptet anger du uppgiften till `AzurePowerShell@3` .

```yaml
steps:
- task: AzurePowerShell@3
```

För `azureSubscription` , anger du namnet på den tjänst anslutning som du har skapat.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

För `scriptPath` anger du den relativa sökvägen från pipeline-filen till skriptet. Du kan titta på lagrings platsen för att se sökvägen.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

I `ScriptArguments` anger du de parametrar som krävs av ditt skript. I följande exempel visas några parametrar för ett skript, men du måste anpassa parametrarna för skriptet.

```yaml
ScriptArguments: -Location 'centralus' -ResourceGroupName 'demogroup' -TemplateFile templates\mainTemplate.json
```

När du väljer **Spara** körs Bygg pipelinen automatiskt. Gå tillbaka till sammanfattningen för din build-pipeline och se status.

![Visa resultat](./media/add-template-to-azure-pipelines/view-results.png)

Du kan välja den pipeline som körs för tillfället för att se information om aktiviteterna. När den är klar visas resultaten för varje steg.

## <a name="copy-and-deploy-tasks"></a>Kopiera och distribuera uppgifter

I det här avsnittet visas hur du konfigurerar kontinuerlig distribution med hjälp av två uppgifter. Den första aktiviteten steg för steg skickar artefakterna till ett lagrings konto och den andra aktiviteten distribuerar mallen.

För att kunna kopiera filer till ett lagrings konto måste tjänstens huvud namn för tjänst anslutningen tilldelas till rollen lagrings-BLOB-data deltagare eller rollen lagrings-BLOB data ägare. Mer information finns i [Kom igång med AZCopy](../../storage/common/storage-use-azcopy-v10.md).

Följande YAML visar [Azure File Copy-aktiviteten](/azure/devops/pipelines/tasks/deploy/azure-file-copy).

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
```

Det finns flera delar av den här uppgiften att ändra för din miljö. `SourcePath`Anger platsen för artefakterna i förhållande till pipelin filen.

```yaml
SourcePath: '<path-to-artifacts>'
```

För `azureSubscription` , anger du namnet på den tjänst anslutning som du har skapat.

```yaml
azureSubscription: '<your-connection-name>'
```

För lagrings-och behållar namn anger du namnen på det lagrings konto och den behållare som du vill använda för att lagra artefakterna. Lagrings kontot måste finnas.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

När du har skapat aktiviteten Kopiera fil är du redo att lägga till uppgiften för att distribuera den mellanlagrade mallen.

Följande YAML visar [distributions uppgiften för Azure Resource Manager mallar](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md):

```yaml
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

Det finns flera delar av den här uppgiften att granska mer detaljerat.

- `deploymentScope`: Välj distributions område från alternativen: `Management Group` , `Subscription` och `Resource Group` . Mer information om omfattningarna finns i [distributions omfång](deploy-rest.md#deployment-scope).

- `azureResourceManagerConnection`: Ange namnet på den tjänst anslutning som du har skapat.

- `subscriptionId`: Ange prenumerations-ID för mål. Den här egenskapen gäller endast för distributions omfånget för resurs gruppen och distributions omfånget för prenumerationen.

- `resourceGroupName` och `location` : Ange namn och plats för den resurs grupp som du vill distribuera till. Aktiviteten skapar resurs gruppen om den inte finns.

   ```yml
   resourceGroupName: '<resource-group-name>'
   location: '<location>'
   ```

- `csmFileLink`: Ange en länk till mellanlagrad mall. När du ställer in värdet använder du variabler som returnerades från fil kopierings aktiviteten. Följande exempel länkar till en mall med namnet mainTemplate.jspå. Mappen med namnet **mallar** inkluderas, eftersom fil kopierings aktiviteten kopierade filen till. Ange sökvägen till mallen och namnet på mallen i din pipeline.

   ```yml
   csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
   ```

Din pipeline ser ut så här:

```yml
trigger:
- master

pool:
  vmImage: 'windows-latest'

steps:
- task: AzureFileCopy@4
  inputs:
    SourcePath: 'templates'
    azureSubscription: 'copy-connection'
    Destination: 'AzureBlob'
    storage: 'demostorage'
    ContainerName: 'projecttemplates'
  name: AzureFileCopy
- task: AzureResourceManagerTemplateDeployment@3
  inputs:
    deploymentScope: 'Resource Group'
    azureResourceManagerConnection: 'copy-connection'
    subscriptionId: '00000000-0000-0000-0000-000000000000'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'West US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.json$(AzureFileCopy.StorageContainerSasToken)'
    csmParametersFileLink: '$(AzureFileCopy.StorageContainerUri)templates/mainTemplate.parameters.json$(AzureFileCopy.StorageContainerSasToken)'
    deploymentMode: 'Incremental'
    deploymentName: 'deploy1'
```

När du väljer **Spara** körs Bygg pipelinen automatiskt. Gå tillbaka till sammanfattningen för din build-pipeline och se status.

## <a name="next-steps"></a>Nästa steg

Information om hur du använder ARM-mallar med GitHub-åtgärder finns i [distribuera Azure Resource Manager mallar med GitHub-åtgärder](deploy-github-actions.md).
