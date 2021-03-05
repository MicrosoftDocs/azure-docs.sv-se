---
title: Konfigurera utvecklings miljön för distributions skript i mallar | Microsoft Docs
description: Konfigurera utvecklings miljön för distributions skript i Azure Resource Manager mallar (ARM-mallar).
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 12/14/2020
ms.author: jgao
ms.openlocfilehash: b2e1ffb3cbd513766945864e33589c46284bf942
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200945"
---
# <a name="configure-development-environment-for-deployment-scripts-in-arm-templates"></a>Konfigurera utvecklings miljön för distributions skript i ARM-mallar

Lär dig hur du skapar en utvecklings miljö för att utveckla och testa distributions skript för ARM-mallar med en distributions skript avbildning. Du kan antingen skapa en [Azure Container instance](../../container-instances/container-instances-overview.md) eller använda [Docker](https://docs.docker.com/get-docker/). Båda alternativen beskrivs i den här artikeln.

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-powershell-container"></a>Azure PowerShell behållare

Om du inte har ett skript för Azure PowerShell distribution kan du skapa en *hello.ps1* -fil med hjälp av följande innehåll:

```powershell
param([string] $name)
$output = 'Hello {0}' -f $name
Write-Output $output
$DeploymentScriptOutputs = @{}
$DeploymentScriptOutputs['text'] = $output
```

### <a name="azure-cli-container"></a>Azure CLI-behållare

För en Azure CLI-behållar avbildning kan du skapa en *Hello.sh* -fil med hjälp av följande innehåll:

```bash
firstname=$1
lastname=$2
output="{\"name\":{\"displayName\":\"$firstname $lastname\",\"firstName\":\"$firstname\",\"lastName\":\"$lastname\"}}"
echo -n "Hello "
echo $output | jq -r '.name.displayName'
```

> [!NOTE]
> När du kör ett skript för Azure CLI-distribution, kallas en miljö variabel `AZ_SCRIPTS_OUTPUT_PATH` som innehåller platsen för skript utdatafilen. Miljövariabeln är inte tillgänglig i utvecklings miljöns behållare. Mer information om hur du arbetar med Azure CLI-utdata finns i [arbeta med utdata från CLI-skript](deployment-script-template.md#work-with-outputs-from-cli-script).

## <a name="use-azure-powershell-container-instance"></a>Använd Azure PowerShell container instance

Om du vill redigera dina skript på datorn måste du skapa ett lagrings konto och montera lagrings kontot till behållar instansen. Så att du kan ladda upp ditt skript till lagrings kontot och köra skriptet på behållar instansen.

> [!NOTE]
> Lagrings kontot som du skapar för att testa skriptet är inte samma lagrings konto som distributions skript tjänsten använder för att köra skriptet. Distributions skript tjänsten skapar ett unikt namn som en fil resurs vid varje körning.

### <a name="create-an-azure-powershell-container-instance"></a>Skapa en instans av Azure PowerShell container

Följande Azure Resource Manager mall (ARM-mall) skapar en behållar instans och en fil resurs och monterar sedan fil resursen till behållar avbildningen.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azuredeploymentscripts-powershell:az5.2",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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

Standardvärdet för monterings Sök vägen är `/mnt/azscripts/azscriptinput` . Detta är sökvägen i behållar instansen där den monteras till fil resursen.

Standard behållar avbildningen som anges i mallen är **MCR.Microsoft.com/azuredeploymentscripts-PowerShell:az5.2**. Se en lista över alla [Azure PowerShell-versioner som stöds](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

Mallen pausar behållar instansen efter 1 800 sekunder. Du har 30 minuter innan behållar instansen övergår i ett avslutat tillstånd och sessionen avslutas.

Distribuera mallen:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Överför distributions skriptet

Överför distributions skriptet till lagrings kontot. Här är ett exempel på ett PowerShell-skript:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name that you used earlier"
$fileName = Read-Host -Prompt "Enter the deployment script file name with the path"

$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$fileShareName = "${projectName}share"

$context = (Get-AzStorageAccount -ResourceGroupName $resourceGroupName -Name $storageAccountName).Context
Set-AzStorageFileContent -Context $context -ShareName $fileShareName -Source $fileName -Force
```

Du kan också ladda upp filen med hjälp av Azure Portal eller Azure CLI.

### <a name="test-the-deployment-script"></a>Testa distributions skriptet

1. I Azure Portal öppnar du resurs gruppen där du har distribuerat behållar instansen och lagrings kontot.
2. Öppna behållar gruppen. Standard behållar grupps namnet är projekt namnet som lagts till med *CG*. Behållar instansen är i **körnings** läge.
3. I menyn resurs väljer du **behållare**. Namnet på behållar instansen är projekt namnet som lagts till i *behållaren*.

    ![Skärm bild av distributions skriptet Connect container instance i Azure Portal.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

4. Välj **Anslut** och välj sedan **Anslut**. Om du inte kan ansluta till behållar instansen startar du om behållar gruppen och försöker igen.
5. Kör följande kommandon i konsol fönstret:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    pwsh ./hello.ps1 "John Dole"
    ```

    Utdata är **Hej John Dole**.

    ![Skärm bild av distributions skriptet Anslut behållar instansen testa utdata i-konsolen.](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test.png)

## <a name="use-an-azure-cli-container-instance"></a>Använd en instans av Azure CLI-behållare

Om du vill redigera dina skript på datorn skapar du ett lagrings konto och monterar lagrings kontot till behållar instansen. Sedan kan du ladda upp skriptet till lagrings kontot och köra skriptet på behållar instansen.

> [!NOTE]
> Det lagrings konto som du skapar för att testa skriptet är inte samma lagrings konto som distributions skript tjänsten använder för att köra skriptet. Distributions skript tjänsten skapar ett unikt namn som en fil resurs vid varje körning.

### <a name="create-an-azure-cli-container-instance"></a>Skapa en instans av Azure CLI-container

Följande ARM-mall skapar en behållar instans och en fil resurs och monterar sedan fil resursen till behållar avbildningen:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
      "defaultValue": "mcr.microsoft.com/azure-cli:2.9.1",
      "metadata": {
        "description": "Specify the container image."
      }
    },
    "mountPath": {
      "type": "string",
      "defaultValue": "/mnt/azscripts/azscriptinput",
      "metadata": {
        "description": "Specify the mount path."
      }
    }
  },
  "variables": {
    "storageAccountName": "[tolower(concat(parameters('projectName'), 'store'))]",
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
                "/bin/bash",
                "-c",
                "echo hello; sleep 1800"
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

Standardvärdet för monterings Sök vägen är `/mnt/azscripts/azscriptinput` . Detta är sökvägen i behållar instansen där den monteras till fil resursen.

Standard behållar avbildningen som anges i mallen är **MCR.Microsoft.com/Azure-CLI:2.9.1**. Se en lista över [Azure CLI-versioner som stöds](https://mcr.microsoft.com/v2/azure-cli/tags/list).

> [!IMPORTANT]
> Distributions skriptet använder de tillgängliga CLI-avbildningarna från Microsoft Container Registry (MCR). Det tar ungefär en månad att certifiera en CLI-avbildning för ett distributions skript. Använd inte de CLI-versioner som släpptes inom 30 dagar. För att hitta versions datumen för avbildningarna, se [versions information för Azure CLI](/cli/azure/release-notes-azure-cli). Om du använder en version som inte stöds visar fel meddelandet de versioner som stöds.

Mallen pausar behållar instansen efter 1 800 sekunder. Du har 30 minuter innan behållar instansen hamnar i ett Terminal-tillstånd och sessionen avslutas.

Distribuera mallen:

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateFile = Read-Host -Prompt "Enter the template file path and file name"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Location $location -name $resourceGroupName
New-AzResourceGroupDeployment -resourceGroupName $resourceGroupName -TemplateFile $templatefile -projectName $projectName
```

### <a name="upload-the-deployment-script"></a>Överför distributions skriptet

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

Du kan också ladda upp filen med hjälp av Azure Portal eller Azure CLI.

### <a name="test-the-deployment-script"></a>Testa distributions skriptet

1. I Azure Portal öppnar du resurs gruppen där du har distribuerat behållar instansen och lagrings kontot.
1. Öppna behållar gruppen. Standard behållar grupps namnet är projekt namnet som lagts till med *CG*. Behållar instansen visas i **Kör** tillstånd.
1. I menyn resurs väljer du **behållare**. Namnet på behållar instansen är projekt namnet som lagts till i *behållaren*.

    ![instans av distributions skriptets Connect-behållare](./media/deployment-script-template-configure-dev/deployment-script-container-instance-connect.png)

1. Välj **Anslut** och välj sedan **Anslut**. Om du inte kan ansluta till behållar instansen startar du om behållar gruppen och försöker igen.
1. Kör följande kommandon i konsol fönstret:

    ```console
    cd /mnt/azscripts/azscriptinput
    ls
    ./hello.sh John Dole
    ```

    Utdata är **Hej John Dole**.

    ![test av container instans för distributions skript](./media/deployment-script-template-configure-dev/deployment-script-container-instance-test-cli.png)

## <a name="use-docker"></a>Använd Docker

Du kan använda en förkonfigurerad Docker-behållar avbildning som utvecklings miljö för distributions skript. Information om hur du installerar Docker finns i [Hämta Docker](https://docs.docker.com/get-docker/).
Du måste också konfigurera fildelning för att montera katalogen som innehåller distributions skripten till Docker-behållaren.

1. Hämta distributions skriptets behållar avbildning till den lokala datorn:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Exemplet använder version PowerShell 4.3.0.

    Hämta en CLI-avbildning från en MCR:

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    I det här exemplet används version CLI-2.0.80. Distributions skript använder standard avbildningarna för CLI-behållare som hittas [här](https://hub.docker.com/_/microsoft-azure-cli).

1. Kör Docker-avbildningen lokalt.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    Ersätt **&lt; värd driv rutins beteckningen>** och **&lt; värd katalog namnet>** med en befintlig mapp på den delade enheten. Mappen mappas till mappen _/data_ i behållaren. Till exempel för att mappa _D:\docker_:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az4.3
    ```

    **– det** innebär att behållar avbildningen är aktiv.

    Ett CLI-exempel:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Följande skärm bild visar hur du kör ett PowerShell-skript, eftersom du har en *helloworld.ps1* -fil på den delade enheten.

    ![Resource Manager-mall distribution skript Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

När skriptet har testats kan du använda det som ett distributions skript i mallarna.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder distributions skript. För att gå igenom en själv studie kurs om distributions skript:

> [!div class="nextstepaction"]
> [Självstudie: använda distributions skript i ARM-mallar](./template-tutorial-deployment-script.md)
