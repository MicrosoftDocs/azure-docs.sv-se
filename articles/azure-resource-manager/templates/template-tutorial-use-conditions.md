---
title: Använd villkor i mallar
description: Lär dig hur du distribuerar Azure-resurser baserat på villkor. Visar hur du antingen distribuerar en ny resurs eller använder en befintlig resurs.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 1f4e8c0bc6a066e0d82d393474bfc804be5e3fb3
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96931375"
---
# <a name="tutorial-use-condition-in-arm-templates"></a>Självstudie: använda villkor i ARM-mallar

Lär dig hur du distribuerar Azure-resurser baserat på villkor i en Azure Resource Manager-mall (ARM-mall).

I självstudien [Ange resursdistributionsordning](./template-tutorial-create-templates-with-dependent-resources.md) skapar du en virtuell dator, ett virtuellt nätverk och några andra beroende resurser, däribland ett lagringskonto. I stället för att skapa ett nytt lagringskonto varje gång låter du användarna välja mellan att skapa ett nytt lagringskonto eller att använda ett befintligt. För att uppnå det här målet definierar du en extra parameter. Om värdet för parametern är ”new” (nytt) skapas ett nytt lagringskonto. Annars används ett befintligt lagrings konto med det angivna namnet.

![Användnings villkors diagram för Resource Manager-mall](./media/template-tutorial-use-conditions/resource-manager-template-use-condition-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Ändra mallen
> * Distribuera mallen
> * Rensa resurser

Den här kursen täcker bara ett grundläggande scenario med att använda villkor. Mer information finns i:

* [Mallens fil struktur: villkor](conditional-resource-deployment.md).
* [Distribuera en resurs villkorligt i en arm-mall](/azure/architecture/building-blocks/extending-templates/conditional-deploy).
* [Template-funktion: IF](./template-functions-logical.md#if).
* [Jämförelse funktioner för ARM-mallar](./template-functions-comparison.md)

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. Se [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudier: integrera Azure Key Vault i distribution av arm-mallar](./template-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure snabb starts mallar är en lagrings plats för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. Från Visual Studio **Code väljer du** > **Öppna fil**.
1. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen.
1. Det finns sex resurser som definieras av mallen:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

    Det är bra att granska mal len referens innan du anpassar en mall.

1. Välj **Arkiv** > **Spara som** om du vill spara en kopia av filen på den lokala datorn med namnet **azuredeploy.jspå**.

## <a name="modify-the-template"></a>Ändra mallen

Gör två ändringar av den befintliga mallen:

* Lägg till namnparameter för lagringskonto. Användare kan ange antingen ett nytt lagringskontonamn eller ett befintligt lagringskontonamn.
* Lägg till en ny parameter med namnet **newOrExisting**. Distributionen använder den här parametern för att avgöra om du ska skapa ett nytt lagrings konto eller använda ett befintligt lagrings konto.

Här följer proceduren för att göra ändringarna:

1. Öppna **azuredeploy.json** i Visual Studio Code.
1. Ersätt de tre **variablerna (' storageAccountName ')** med **parametrar (' storageAccountName ')** i hela mallen.
1. Ta bort följande variabeldefinition:

    ![Skärm bild som visar de variabel definitioner som du behöver ta bort.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-remove-storageaccountname.png)

1. Lägg till följande två parametrar i början av avsnittet parametrar:

    ```json
    "storageAccountName": {
      "type": "string"
    },
    "newOrExisting": {
      "type": "string",
      "allowedValues": [
        "new",
        "existing"
      ]
    },
    ```

    Tryck på **[ALT] + [SHIFT] + F** för att formatera mallen i Visual Studio Code.

    Den uppdaterade parameterdefinitionen ser ut så här:

    ![Resource Manager-användningsvillkor](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template-parameters.png)

1. Lägg till följande rad i början av lagringskontots definition.

    ```json
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    ```

    Villkoret kontrollerar värdet för parametern med namnet **newOrExisting**. Om parametervärdet är **new** (nytt) skapar distributionen lagringskontot.

    Den uppdaterade lagringskontodefinitionen ser ut så här:

    ![Skärm bild som visar den uppdaterade lagrings konto definitionen.](./media/template-tutorial-use-conditions/resource-manager-tutorial-use-condition-template.png)
1. Uppdatera **storageUri** -egenskapen för den virtuella datorns resurs definition med följande värde:

    ```json
    "storageUri": "[concat('https://', parameters('storageAccountName'), '.blob.core.windows.net')]"
    ```

    Den här ändringen är nödvändig när du använder ett befintligt lagringskonto under en annan resursgrupp.

1. Spara ändringarna.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Logga in på [Azure Cloud Shell](https://shell.azure.com)

1. Välj önskad miljö genom att välja antingen **PowerShell** eller **bash** (för CLI) i det övre vänstra hörnet.  Du måste starta om gränssnittet när du byter.

    ![Azure Portal Cloud Shell Ladda upp fil](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Välj **Ladda upp/ned filer** och välj sedan **Ladda upp**. Se föregående skärmbild. Välj den fil som du sparade i föregående avsnitt. När du har överfört filen kan du använda kommandot **ls** och kommandot **Cat** för att kontrol lera att filen har laddats upp.

1. Kör följande PowerShell-skript för att distribuera mallen.

    > [!IMPORTANT]
    > Namnet på lagringskontot måste vara unikt i Azure. Namnet får bara innehålla gemena bokstäver eller siffror. Det får inte vara längre än 24 tecken. Lagrings kontots namn är projekt namnet med tillägget "Store". Kontrol lera att projekt namnet och det genererade lagrings konto namnet uppfyller kraven för lagrings kontots namn.

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name and resource names"
    $newOrExisting = Read-Host -Prompt "Create new or use existing (Enter new or existing)"
    $location = Read-Host -Prompt "Enter the Azure location (i.e. centralus)"
    $vmAdmin = Read-Host -Prompt "Enter the admin username"
    $vmPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS Label prefix"

    $resourceGroupName = "${projectName}rg"
    $storageAccountName = "${projectName}store"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $vmAdmin `
        -adminPassword $vmPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -storageAccountName $storageAccountName `
        -newOrExisting $newOrExisting `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    > [!NOTE]
    > Distributionen misslyckas om **newOrExisting** är **new** (nytt) men lagringskontonamnet som anges redan finns.

Försök att göra en annan distribution med **newOrExisting** inställd på "befintlig" och ange ett befintligt lagrings konto. Information om hur du skapar ett lagringskonto i förväg finns i [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen. Om du vill ta bort resurs gruppen väljer du **prova** att öppna Cloud Shell. Om du vill klistra in PowerShell-skriptet högerklickar du på fönstret Shell och väljer **Klistra in**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -Name $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien utvecklade du en mall som gör att användarna kan välja mellan att skapa ett nytt lagringskonto och att använda ett befintligt lagringskonto. Om du vill lära dig hur du hämtar hemligheter från Azure Key Vault och använder hemligheterna som lösenord i malldistributionen läser du:

> [!div class="nextstepaction"]
> [Integrera Key Vault i malldistribution](./template-tutorial-use-key-vault.md)
