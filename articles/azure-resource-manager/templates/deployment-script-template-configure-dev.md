---
title: Konfigurera utvecklings miljön för distributions skript i mallar | Microsoft Docs
description: Konfigurera utvecklings miljön för distributions skript i Azure Resource Manager mallar.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: d12ec5e3fef45429741fff1665f435d68e6c83f6
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734189"
---
# <a name="configure-development-environment-for-deployment-scripts-in-templates"></a>Konfigurera utvecklings miljön för distributions skript i mallar

Lär dig hur du skapar en utvecklings miljö för att utveckla och testa distributions skript med en distributions skript avbildning. Du kan antingen skapa en [Azure Container instance](../../container-instances/container-instances-overview.md) eller använda [Docker](https://docs.docker.com/get-docker/). Båda beskrivs i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

Om du inte har ett distributions skript kan du skapa en **hello.ps1** -fil med följande innehåll:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

## <a name="use-azure-container-instance"></a>Använd Azure Container instance

Om du vill redigera dina skript på datorn måste du skapa ett lagrings konto och montera lagrings kontot till behållar instansen. Så att du kan ladda upp ditt skript till lagrings kontot och köra skriptet på behållar instansen.

> [!NOTE]
> Lagrings kontot som du skapar för att testa skriptet är inte samma lagrings konto som distributions skript tjänsten använder för att köra skriptet. Distributions skript tjänsten skapar ett unikt namn som en fil resurs vid varje körning.

### <a name="create-an-azure-container-instance"></a>Skapa en Azure Container instance

Följande ARM-mall skapar en behållar instans och en fil resurs och monterar sedan fil resursen till behållar avbildningen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "projectName": {
      "type": "string",
      "metadata": {
        "description": "Specify a project name that is used for generating resource names."
      }
    },
    "containerImage": {
      "type": "string",
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "deploymentScript",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(parameters('projectName'), 'store')]",
    "fileShareName": "[concat(parameters('projectName'), 'share')]",
    "containerGroupName": "[concat(parameters('projectName'), 'cg')]",
    "containerName": "[concat(parameters('projectName'), 'container')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "StorageV2",
      "properties": {
        "accessTier": "Hot"
      }
    },
    {
      "type": "Microsoft.Storage/storageAccounts/fileServices/shares",
      "apiVersion": "2019-06-01",
      "name": "[concat(variables('storageAccountName'), '/default/', variables('fileShareName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ]
    },
    {
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "name": "[variables('containerGroupName')]",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]"
      ],
      "properties": {
        "containers": [
          {
            "name": "[variables('containerName')]",
            "properties": {
              "image": "[parameters('containerImage')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "protocol": "TCP",
                  "port": 80
                }
              ],
              "volumeMounts": [
                {
                  "name": "filesharevolume",
                  "mountPath": "[parameters('mountPath')]"
                }
              ],
              "command": [
                "/bin/sh",
                "-c",
                "pwsh -c 'Start-Sleep -Seconds 1800'"
              ]
            }
          }
        ],
        "osType": "Linux",
        "volumes": [
          {
            "name": "filesharevolume",
            "azureFile": {
              "readOnly": false,
              "shareName": "[variables('fileShareName')]",
              "storageAccountName": "[variables('storageAccountName')]",
              "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
            }
          }
        ]
      }
    }
  ]
}
```
Standardvärdet för monterings Sök vägen är **deploymentScript**.  Detta är sökvägen i behållar instansen där den monteras på fil resursen.

Standard behållar avbildningen som anges i mallen är **MCR.Microsoft.com/azuredeploymentscripts-PowerShell:AZ4.3**.   Se en lista över [Azure PowerShell-versioner som stöds](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Se en lista över [Azure CLI-versioner som stöds](https://mcr.microsoft.com/v2/azure-cli/tags/list).

  >[!IMPORTANT]
  > Distributions skriptet använder de tillgängliga CLI-avbildningarna från Microsoft Container Registry (MCR). Det tar ungefär en månad att certifiera en CLI-avbildning för distributions skript. Använd inte de CLI-versioner som släpptes inom 30 dagar. För att hitta versions datumen för avbildningarna, se [versions information för Azure CLI](/cli/azure/release-notes-azure-cli?view=azure-cli-latest&preserve-view=true). Om en version som inte stöds används listas de versioner som stöds i fel meddelandet.

Mallen pausar behållar instansen 1800 sekunder. Du har 30 minuter innan behållar instansen hamnar i Terminal-tillstånd och sessionen avslutas.

Distribuera mallen:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-azResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-deployment-script"></a>Överför distributions skript

Överför distributions skriptet till lagrings kontot. Följande är ett PowerShell-exempel:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Du kan också ladda upp filen med hjälp av Azure Portal och Azure CLI.

### <a name="test-the-deployment-script"></a>Testa distributions skriptet

1. Från Azure Portal öppnar du resurs gruppen där du har distribuerat behållar instansen och lagrings kontot.
1. Öppna behållar gruppen. Standard behållar grupps namnet är projekt namnet med **CG** tillagt. Du ser att behållar instansen är i **Kör** tillstånd.
1. Välj **behållare** på den vänstra menyn. Du ska se en behållar instans.  Namnet på behållar instansen är projekt namnet med **containern** .

    ![instans av distributions skriptets Connect-behållare](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Välj **Anslut** och välj sedan **Anslut**. Om du inte kan ansluta till behållar instansen startar du om behållar gruppen och försöker igen.
1. Kör följande kommandon i konsol fönstret:

    ```console
    cd deploymentScript
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    Utdata är **Hej John Dole**.

    ![test av container instans för distributions skript](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

1. Om du använder AZ CLI-behållar avbildningen kör du den här koden:

   ```console
   cd /mnt/azscripts/azscriptinput
   ls
   ./userscript.sh
   ```

## <a name="use-docker"></a>Använd Docker

Du kan använda en förkonfigurerad Docker-behållar avbildning som utvecklings miljö för distributions skript. Information om hur du installerar Docker finns i [Hämta Docker](https://docs.docker.com/get-docker/).
Du måste också konfigurera fildelning för att montera katalogen som innehåller distributions skripten till Docker-behållaren.

1. Hämta distributions skriptets behållar avbildning till den lokala datorn:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Exemplet använder version PowerShell 4.3.0.

    Hämta en CLI-avbildning från en Microsoft Container Registry (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    I det här exemplet används version CLI-2.0.80. Distributions skript använder standard avbildningarna för CLI-behållare som hittas [här](https://hub.docker.com/_/microsoft-azure-cli).

1. Kör Docker-avbildningen lokalt.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Ersätt **&lt; värd driv rutins beteckningen>** och **&lt; värd katalog namnet>** med en befintlig mapp på den delade enheten.  Mappen mappas till mappen **/data** i behållaren. Till exempel för att mappa D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **– det** innebär att behållar avbildningen är aktiv.

    Ett CLI-exempel:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Följande skärm bild visar hur du kör ett PowerShell-skript, eftersom du har en helloworld.ps1-fil på den delade enheten.

    ![Resource Manager-mall distribution skript Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

När skriptet har testats kan du använda det som ett distributions skript i mallarna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder distributions skript. För att gå igenom en själv studie kurs om distributions skript:

> [!div class="nextstepaction"]
> [Självstudie: använda distributions skript i Azure Resource Manager mallar](./template-tutorial-deployment-script.md)
