---
title: Använda Azure Key Vault i mallar
description: Lär dig hur du använder Azure Key Vault för att skicka säkra parameter värden vid distribution av Azure Resource Manager mall (ARM-mall).
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 75eb977559573b72883de3ddbc27391c7e299a6f
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929325"
---
# <a name="tutorial-integrate-azure-key-vault-in-your-arm-template-deployment"></a>Självstudie: Integrera Azure Key Vault i din ARM-malldistribution

Lär dig hur du hämtar hemligheter från ett Azure Key Vault och skickar hemligheterna som parametrar när du distribuerar en Azure Resource Manager-mall (ARM-mall). Parametervärdet exponeras aldrig, eftersom du bara refererar till nyckel valvets ID. Du kan referera till Key Vault-hemligheten genom att använda ett statiskt ID eller ett dynamiskt ID. I den här självstudien används ett statiskt ID. Med den statiska ID-metoden hänvisar du till nyckel valvet i mallens parameter fil, inte mallfilen. Mer information om båda metoderna finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](./key-vault-parameter.md).

I självstudien [Ange distributions ordning för resurser](./template-tutorial-create-templates-with-dependent-resources.md) skapar du en virtuell dator (VM). Du måste ange användar namn och lösen ord för VM-administratören. I stället för att ange lösen ordet kan du i förväg lagra lösen ordet i ett Azure Key Vault och sedan anpassa mallen för att hämta lösen ordet från nyckel valvet under distributionen.

![Diagram som visar integrationen av en Resource Manager-mall med ett nyckel valv](./media/template-tutorial-use-key-vault/resource-manager-template-key-vault-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda ett nyckelvalv
> * Öppna en snabbstartsmall
> * Redigera parameterfilen
> * Distribuera mallen
> * Verifiera distributionen
> * Rensa resurser

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. Se [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Om du vill öka säkerheten använder du ett genererat lösen ord för administratörs kontot för den virtuella datorn. Här är ett exempel på att skapa ett lösen ord:

    ```console
    openssl rand -base64 32
    ```
    Kontrol lera att det genererade lösen ordet uppfyller kraven för den virtuella datorns lösen ord. Varje Azure-tjänst har specifika lösenordskrav. Information om kraven för den virtuella datorns lösen ord finns i [lösen ords kraven när du skapar en virtuell dator?](../../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="prepare-a-key-vault"></a>Förbereda ett nyckelvalv

I det här avsnittet skapar du ett nyckel valv och lägger till en hemlighet i det, så att du kan hämta hemligheten när du distribuerar mallen. Det finns många sätt att skapa ett nyckel valv. I den här självstudien använder du Azure PowerShell för att distribuera en [arm-mall](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json). Den här mallen gör två saker:

* Skapar ett nyckel valv med `enabledForTemplateDeployment` egenskapen aktive rad. Den här egenskapen måste vara *sann* innan mallens distributions process kan komma åt de hemligheter som har definierats i nyckel valvet.
* Lägger till en hemlighet i nyckel valvet. Hemligheten lagrar lösen ordet för den virtuella dator administratören.

> [!NOTE]
> Som en användare som distribuerar mallen för virtuella datorer, om du inte är ägare till eller en deltagare i nyckel valvet, måste ägaren eller en deltagare ge dig åtkomst till *Microsoft. Key Vault/valv/Deploy/åtgärd* -behörighet för nyckel valvet. Mer information finns i [använda Azure Key Vault för att skicka ett säkert parameter värde under distributionen](./key-vault-parameter.md).

Om du vill köra följande Azure PowerShell skript väljer du **försök** att öppna Azure Cloud Shell. Om du vill klistra in skriptet högerklickar du på fönstret Shell och väljer **Klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used for generating resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$upn = Read-Host -Prompt "Enter your user principal name (email address) used to sign in to Azure"
$secretValue = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$keyVaultName = $projectName
$adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorials-use-key-vault/CreateKeyVault.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -keyVaultName $keyVaultName -adUserId $adUserId -secretValue $secretValue

Write-Host "Press [ENTER] to continue ..."
```

> [!IMPORTANT]
> * Resurs gruppens namn är projekt namnet, men med **RG** bifogat till det. För att göra det enklare att [Rensa resurserna som du skapade i den här självstudien](#clean-up-resources)använder du samma projekt namn och resurs grupp namn när du [distribuerar nästa mall](#deploy-the-template).
> * Standard namnet för hemligheten är **vmAdminPassword**. Den är hårdkodad i mallen.
> * Om du vill aktivera mallen för att hämta hemligheten måste du aktivera en åtkomst princip med namnet **Aktivera åtkomst till Azure Resource Manager för** nyckel valvet. Den här principen är aktive rad i mallen. Mer information om åtkomst principen finns i [distribuera nyckel valv och hemligheter](./key-vault-parameter.md#deploy-key-vaults-and-secrets).

Mallen har ett värde för utdata, som kallas *keyVaultId*. Du kommer att använda det här ID: t tillsammans med det hemliga namnet för att hämta det hemliga värdet senare i självstudien. Resurs-ID-formatet är:

```json
/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>
```

När du kopierar och klistrar in ID: t kan det delas upp i flera rader. Slå samman raderna och trimma extra blank steg.

Verifiera distributionen genom att köra följande PowerShell-kommando i samma Shell-fönster för att hämta den hemliga texten i klartext. Kommandot fungerar bara i samma Shell-session, eftersom variabeln *$keyVaultName* som definieras i föregående PowerShell-skript används.

```azurepowershell
(Get-AzKeyVaultSecret -vaultName $keyVaultName  -name "vmAdminPassword").SecretValueText
```

Nu har du för berett ett nyckel valv och en hemlighet. I följande avsnitt visas hur du anpassar en befintlig mall för att hämta hemligheten under distributionen.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure snabb starts mallar är en lagrings plats för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Mallen som används i den här självstudien kallas för att [distribuera en enkel virtuell Windows-dator](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/).

1. I Visual Studio **Code väljer du**  >  **Öppna fil**.

1. I rutan **Filnamn** klistrar du in följande webbadress: 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen. Scenariot är detsamma som det som används i [Självstudier: skapa arm-mallar med beroende resurser](./template-tutorial-create-templates-with-dependent-resources.md).
   Mallen definierar sex resurser:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

   Det är praktiskt att ha lite grundläggande förståelse för mallen innan du anpassar den.

1. Välj **Arkiv**  >  **Spara som** och spara sedan en kopia av filen på den lokala datorn med namnet *azuredeploy.jspå*.

1. Upprepa steg 1-3 för att öppna följande URL och spara sedan filen som *azuredeploy.parameters.jspå*.

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.parameters.json
    ```

## <a name="edit-the-parameters-file"></a>Redigera parameterfilen

Genom att använda metoden med statisk ID behöver du inte göra några ändringar i mallfilen. Att hämta det hemliga värdet görs genom att konfigurera mallens parameter fil.

1. Öppna *azuredeploy.parameters.jspå* om den inte redan är öppen i Visual Studio Code.
1. Uppdatera `adminPassword` parametern till:

    ```json
    "adminPassword": {
        "reference": {
            "keyVault": {
            "id": "/subscriptions/<SubscriptionID>/resourceGroups/mykeyvaultdeploymentrg/providers/Microsoft.KeyVault/vaults/<KeyVaultName>"
            },
            "secretName": "vmAdminPassword"
        }
    },
    ```

    > [!IMPORTANT]
    > Ersätt värdet för **ID** med resurs-ID för nyckel valvet som du skapade i föregående procedur. SecretName är hårdkodad som **vmAdminPassword**.  Se [förbereda ett nyckel valv](#prepare-a-key-vault).

    ![Integrera Key Vault och Resource Manager-mall distributions parameter fil för virtuell dator](./media/template-tutorial-use-key-vault/resource-manager-tutorial-create-vm-parameters-file.png)

1. Uppdatera följande värden:

    * **adminUsername**: namnet på den virtuella datorns administratörs konto.
    * **dnsLabelPrefix**: Namnge värdet för dnsLabelPrefix.

    Exempel på namn finns i föregående bild.

1. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Logga in på [Azure Cloud Shell](https://shell.azure.com)

1. Välj önskad miljö genom att välja antingen **PowerShell** eller **bash** (för CLI) i det övre vänstra hörnet.  Du måste starta om gränssnittet när du byter.

    ![Azure Portal Cloud Shell Ladda upp fil](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Välj **Ladda upp/ned filer** och välj sedan **Ladda upp**. Ladda upp både *azuredeploy.js* och *azuredeploy.parameters.jspå* Cloud Shell. När du har överfört filen kan du använda kommandot **ls** och kommandot **Cat** för att kontrol lera att filen har laddats upp.

1. Kör följande PowerShell-skript för att distribuera mallen.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
    $location = Read-Host -Prompt "Enter the same location that is used for creating the key vault (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$HOME/azuredeploy.json" `
        -TemplateParameterFile "$HOME/azuredeploy.parameters.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    När du distribuerar mallen använder du samma resurs grupp som du använde i nyckel valvet. Den här metoden gör det enklare för dig att rensa resurserna, eftersom du bara behöver ta bort en resurs grupp i stället för två.

## <a name="validate-the-deployment"></a>Verifiera distributionen

När du har distribuerat den virtuella datorn testar du inloggnings uppgifterna med hjälp av lösen ordet som lagras i nyckel valvet.

1. Öppna [Azure-portalen](https://portal.azure.com).

1. Välj **resurs grupper**  >  **\<*YourResourceGroupName*>**  >  **simpleWinVM**.
1. Välj **Anslut** högst upp.
1. Välj **Ladda ned RDP-fil** och följ sedan anvisningarna för att logga in på den virtuella datorn med hjälp av lösen ordet som lagras i nyckel valvet.

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver dina Azure-resurser rensar du de resurser som du har distribuerat genom att ta bort resurs gruppen.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that is used for creating the key vault"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du hämtat en hemlighet från ditt Azure Key Vault. Du använde sedan hemligheten i din mall-distribution. Information om hur du använder tillägg för virtuell dator för att utföra distributionsuppgifter finns i:

> [!div class="nextstepaction"]
> [Distribuera tillägg för virtuella datorer](./template-tutorial-deploy-vm-extensions.md)
