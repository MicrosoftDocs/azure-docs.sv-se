---
title: Mall med beroende resurser
description: Lär dig hur du skapar en Azure Resource Manager mall (ARM-mall) med flera resurser och hur du distribuerar den med hjälp av Azure Portal
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: a77f64e51a26e1f916f9f96704c55412a870a509
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673944"
---
# <a name="tutorial-create-arm-templates-with-dependent-resources"></a>Självstudie: skapa ARM-mallar med beroende resurser

Lär dig hur du skapar en Azure Resource Manager mall (ARM-mall) för att distribuera flera resurser och konfigurera distributions ordningen. När du har skapat mallen distribuerar du mallen med hjälp av Cloud Shell från Azure Portal.

I den här självstudien skapar du ett lagringskonto, en virtuell dator, ett virtuellt nätverk och några andra beroende resurser. Vissa resurser kan inte distribueras förrän en annan resurs finns. Till exempel kan du inte skapa den virtuella datorn förrän dess lagringskonto och nätverksgränssnitt finns. Du kan definiera den här relationen genom att göra en resurs beroende av de andra resurserna. Resource Manager utvärderar beroenden mellan resurser och distribuerar dem i beroendeordning. När resurserna inte är beroende av varandra distribuerar Resource Manager dem parallellt. Mer information finns i [definiera ordningen för att distribuera resurser i arm-mallar](./define-resource-dependency.md).

![Distributions ordnings diagram för resurser i Resource Manager-mall](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Utforska mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

En Microsoft Learn-modul som täcker resurs beroenden finns i [hantera komplexa moln distributioner med hjälp av avancerade funktioner för ARM-mallar](/learn/modules/manage-deployments-advanced-arm-template-features/).

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

1. Från Visual Studio **Code väljer du**  >  **Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen.
4. Välj **Arkiv**  >  **Spara som** om du vill spara en kopia av filen på den lokala datorn med namnet _azuredeploy.jspå_.

## <a name="explore-the-template"></a>Utforska mallen

När du utforskar mallen i det här avsnittet kan du försöka besvara följande frågor:

* Hur många Azure-resurser är definierade i den här mallen?
* En av resurserna är ett Azure-lagringskonto. Ser definitionen ut som den som används i den senaste självstudien?
* Kan du hitta mallreferenserna för de resurser som är definierade i mallen?
* Kan du hitta resursernas beroenden?

1. Dölj elementen från Visual Studio Code tills du bara ser de första nivå elementen och elementen på den andra nivån i `resources` :

    ![Visual Studio Code ARM-mallar](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Det finns sex resurser som definieras av mallen:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Det är viktigt att du granskar mal len referens innan du anpassar en mall.

1. Expandera den första resursen. Det är ett lagrings konto. Jämför resurs definitionen med [mal len referens](/azure/templates/Microsoft.Storage/storageAccounts).

    ![Definition av lagrings konto för Visual Studio Code ARM-mallar](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

1. Expandera den andra resursen. Resurstypen är `Microsoft.Network/publicIPAddresses`. Jämför resurs definitionen med [mal len referens](/azure/templates/microsoft.network/publicipaddresses).

    ![Definition av den offentliga IP-adressen i Visual Studio Code ARM-mallar](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)

1. Expandera den tredje resursen. Resurstypen är `Microsoft.Network/networkSecurityGroups`. Jämför resurs definitionen med [mal len referens](/azure/templates/microsoft.network/networksecuritygroups).

    ![Visual Studio Code ARM-mallar definition av nätverks säkerhets grupp](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-network-security-group-definition.png)

1. Expandera den fjärde resursen. Resurstypen är `Microsoft.Network/virtualNetworks`:

    ![Visual Studio Code ARM-mallar virtuellt nätverk dependsOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-virtual-network-definition.png)

    Med- `dependsOn` elementet kan du definiera en resurs som beroende av en eller flera resurser. Den här resursen är beroende av en annan resurs:

    * `Microsoft.Network/networkSecurityGroups`

1. Expandera den femte resursen. Resurstypen är `Microsoft.Network/networkInterfaces`. Resursen beror på två resurser:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

1. Expandera den sjätte resursen. Den här resursen är en virtuell dator. Den beror på två resurser:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Följande diagram illustrerar resurserna och beroendeinformation för den här mallen:

![Visual Studio Code ARM-mallar beroende diagram](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Genom att ange beroendena distribuerar Resource Manager effektivt lösningen. Den distribuerar lagringskontot, en offentlig IP-adress och ett virtuellt nätverk parallellt eftersom de inte har några beroenden. När den offentliga IP-adressen och det virtuella nätverket har distribuerats skapas ett nätverksgränssnitt. När alla andra resurser har distribuerats så distribuerar Resource Manager den virtuella datorn.

## <a name="deploy-the-template"></a>Distribuera mallen

1. Logga in på [Azure Cloud Shell](https://shell.azure.com)

1. Välj önskad miljö genom att välja antingen **PowerShell** eller **bash** (för CLI) i det övre vänstra hörnet.  Du måste starta om gränssnittet när du byter.

    ![Azure Portal Cloud Shell Ladda upp fil](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Välj **Ladda upp/ned filer** och välj sedan **Ladda upp**. Se föregående skärmbild. Välj den fil som du sparade tidigare. När du har överfört filen kan du använda `ls` kommandot och `cat` kommandot för att kontrol lera att filen har överförts.

1. Kör följande PowerShell-skript för att distribuera mallen.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    echo "Enter the virtual machine admin username:" &&
    read adminUsername &&
    echo "Enter the DNS label prefix:" &&
    read dnsLabelPrefix &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location $location &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters adminUsername=$adminUsername dnsLabelPrefix=$dnsLabelPrefix
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"

    Write-Host "Press [ENTER] to continue ..."
    ```

    ---

1. RDP till den virtuella datorn för att verifiera att den virtuella datorn har skapats.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn. Du ser totalt sex resurser i resurs gruppen.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du utvecklat och distribuerat en mall för att skapa en virtuell dator, ett virtuellt nätverk och de beroende resurserna. Information om hur du använder distributions skript för att utföra åtgärder före/efter distributionen finns i:

> [!div class="nextstepaction"]
> [Använda distributionsskript](./template-tutorial-deployment-script.md)
