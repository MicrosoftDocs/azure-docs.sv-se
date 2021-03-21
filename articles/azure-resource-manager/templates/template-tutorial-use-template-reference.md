---
title: Använda mallreferens
description: Använd referensen Azure Resource Manager mall (ARM-mall) för att skapa en mall.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97584144"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Självstudie: Använd ARM-mallens referens

Lär dig hur du hittar mallens schema information och använder informationen för att skapa Azure Resource Manager mallar (ARM-mallar).

I den här självstudien använder du en grundläggande mall från Azures snabb starts mallar. Med hjälp av referens dokumentation för mallar kan du anpassa mallen.

![Referens för Resource Manager-mall distribuera lagrings konto](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Förstå mallen
> * Leta upp mallreferensen
> * Redigera mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. Se [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

[Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/) är en lagrings plats för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här snabbstarten kallas [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Skapa ett standardlagringskonto). Mallen definierar en Azure Storage-kontoresurs.

1. Från Visual Studio **Code väljer du**  >  **Öppna fil**.
1. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen.
1. Välj **Arkiv**  >  **Spara som** för att spara filen som _azuredeploy.jspå_ den lokala datorn.

## <a name="understand-the-schema"></a>Förstå schemat

1. Komprimera mallen till rot nivån från Visual Studio Code. Du får den enklaste strukturen med följande element:

    ![Enklaste struktur för Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: Ange platsen för JSON-schemafilen som beskriver versionen av mall språket.
    * `contentVersion`: Ange ett värde för det här elementet för att dokumentera viktiga ändringar i mallen.
    * `parameters`: Ange de värden som anges när distributionen körs för att anpassa resurs distributionen.
    * `variables`: Ange de värden som används som JSON-fragment i mallen för att förenkla mallarnas språk uttryck.
    * `resources`: Ange de resurs typer som distribueras eller uppdateras i en resurs grupp.
    * `outputs`: Ange de värden som returneras efter distributionen.

1. Expandera `resources` . En resurs har `Microsoft.Storage/storageAccounts` definierats. SKU-namnet använder ett parameter värde. Parametern anropas `storageAccountType` .

    ![Definition av lagringskonto för Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Expandera `parameters` för att se hur `storageAccountType` definieras. Parametern har fyra tillåtna värden. Du hittar de andra tillåtna värdena och ändrar sedan parameter definitionen.

    ![Resource Manager-mall lagrings konto resurser SKU: er](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Leta upp mallreferensen

1. Bläddra till [referens för Azure-mall](/azure/templates/).
1. I rutan **Filtrera efter rubrik** anger du **lagrings konton** och väljer de första **lagrings kontona** under **referens > lagring**.

    ![Resource Manager, mallreferens, lagringskonto](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    En resurs leverantör har vanligt vis flera API-versioner:

    ![Referens lagrings konto versioner i Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. Välj **alla resurser** under **lagring** i det vänstra fönstret. Den här sidan visar resurs typer och versioner av Storage Resource-providern. Vi rekommenderar att du använder de senaste API-versionerna för de resurs typer som definierats i mallen.

    ![Referens för lagrings konto typer i Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Välj den senaste versionen av `storageAccount` resurs typen. Den senaste versionen är **2019-06-01** när den här artikeln skrivs. Kontrol lera att den här versionen matchar den version som används för lagrings konto resursen i mallen. Om du uppdaterar API-versionen måste du kontrol lera att resurs definitionen matchar mal Lav referensen.

1. Den här sidan visar information om resurs typen storageAccount. Till exempel visas de tillåtna värdena för **SKU-objektet**. Det finns fler SKU: er än vad som visas i den snabb starts mall som du öppnade tidigare. Du kan anpassa snabb starts mal len så att den innehåller alla tillgängliga lagrings typer.

    ![Referens lagrings konto SKU: er för Resource Manager-mall](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Redigera mallen

Lägg till de ytterligare lagrings konto typer som visas på följande skärm bild i Visual Studio Code:

![Resource Manager-mall lagrings konto resurser](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Logga in på [Azure Cloud Shell](https://shell.azure.com)

1. Välj önskad miljö genom att välja antingen **PowerShell** eller **bash** (för CLI) i det övre vänstra hörnet.  Du måste starta om gränssnittet när du byter.

    ![Azure Portal Cloud Shell Ladda upp fil](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Välj **Ladda upp/ned filer** och välj sedan **Ladda upp**. Se föregående skärmbild. Välj den fil som du sparade i föregående avsnitt. När du har överfört filen kan du använda `ls` kommandot och `cat` kommandot för att kontrol lera att filen har överförts.

1. Kör följande kommandon från Cloud Shell. Välj fliken så att du ser PowerShell-koden eller CLI-koden.

   När du distribuerar mallen anger du `storageAccountType` parametern med ett nyligen tillagt värde, till exempel **Standard_RAGRS**. Det gick inte att distribuera om du använde den ursprungliga snabb starts mal len eftersom **Standard_RAGRS** inte var ett tillåtet värde.

    # <a name="cli"></a>[CLI](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
1. Ange resursgruppens namn i fältet **Filtrera efter namn**.
1. Välj resursgruppens namn.  Du bör se totalt sex resurser i resursgruppen.
1. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du använder mallreferensen för att anpassa en befintlig mall. Du hittar mer information om att skapa flera instanser av lagringskonton i:

> [!div class="nextstepaction"]
> [Skapa flera instanser](./template-tutorial-create-multiple-instances.md)
