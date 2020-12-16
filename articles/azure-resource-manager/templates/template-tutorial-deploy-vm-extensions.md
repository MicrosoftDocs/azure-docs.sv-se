---
title: Distribuera VM-tillägg med mall
description: Lär dig hur du distribuerar tillägg för virtuella datorer med Azure Resource Manager mallar (ARM-mallar).
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9e04006a0908832c623230d89caa62b0985f32e4
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97587952"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Självstudie: distribuera tillägg för virtuella datorer med ARM-mallar

Lär dig hur du använder [Azure-tillägg för virtuell dator](../../virtual-machines/extensions/features-windows.md) för att utföra konfigurations- och automatiseringsuppgifter efter distribution på virtuella Azure-datorer. Det finns många olika VM-tillägg för användning med virtuella Azure-datorer. I den här självstudien distribuerar du ett anpassat skript tillägg från en Azure Resource Manager-mall (ARM-mall) för att köra ett PowerShell-skript på en virtuell Windows-dator. Skriptet installerar webbserver på den virtuella datorn.

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Förbereda ett PowerShell-skript
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Krav

För att kunna följa stegen i den här artikeln behöver du:

* Visual Studio Code med Resource Manager Tools-tillägg. Se [snabb start: skapa arm-mallar med Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* För att förbättra säkerheten bör du använda ett genererat lösenord för den virtuella datorns administratörskonto. Här är ett exempel för att generera ett lösenord:

    ```console
    openssl rand -base64 32
    ```

    Azure Key Vault är utformat för att skydda kryptografiska nycklar och andra hemligheter. Mer information finns i [Självstudier: integrera Azure Key Vault i din distribution av arm-mallar](./template-tutorial-use-key-vault.md). Vi rekommenderar även att du uppdaterar ditt lösenord var tredje månad.

## <a name="prepare-a-powershell-script"></a>Förbereda ett PowerShell-skript

Du kan använda ett infogat PowerShell-skript eller en skript fil. I den här självstudien visas hur du använder en skript fil. Ett PowerShell-skript med följande innehåll delas från [GitHub](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Om du väljer att publicera filen på din plats kan du uppdatera `fileUri` elementet i mallen senare i självstudien.

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

Azure snabb starts mallar är en lagrings plats för ARM-mallar. I stället för att skapa en mall från början får du en exempelmall som du anpassar. Den mall som används i den här självstudien heter [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Distribuera en enkel virtuell Windows-dator).

1. I Visual Studio **Code väljer du**  >  **Öppna fil**.
1. I rutan **Filnamn** klistrar du in följande webbadress: 

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Välj **Öppna** för att öppna filen.
    Mallen definierar fem resurser:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. Compute/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Det är bra om du har grundläggande förståelse av mallen innan du anpassar den.

1. Spara en kopia av filen lokalt med namnet *azuredeploy.json* genom att välja **Arkiv** > **Spara som**.

## <a name="edit-the-template"></a>Redigera mallen

Lägg till en resurs för tillägg för virtuell dator i den befintliga mallen med följande innehåll:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
  "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
  "location": "[parameters('location')]",
  "dependsOn": [
      "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
  ],
  "properties": {
      "publisher": "Microsoft.Compute",
      "type": "CustomScriptExtension",
      "typeHandlerVersion": "1.7",
      "autoUpgradeMinorVersion":true,
      "settings": {
        "fileUris": [
          "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
        ],
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
      }
  }
}
```

Mer information om den här resursdefinitionen finns i [tilläggsreferensen](/azure/templates/microsoft.compute/virtualmachines/extensions). Här följer några viktiga element:

* `name`: Eftersom tilläggs resursen är en underordnad resurs för det virtuella datorobjektet måste namnet ha prefixet för den virtuella datorn. Se [Ange namn och typ för underordnade resurser](child-resource-name-type.md).
* `dependsOn`: Skapa tilläggs resursen när du har skapat den virtuella datorn.
* `fileUris`: De platser där skriptfilerna lagras. Om du väljer att inte använda den angivna platsen måste du uppdatera värdena.
* `commandToExecute`: Det här kommandot anropar skriptet.

Om du vill använda ett infogat skript, ta bort `fileUris` och uppdatera `commandToExecute` till:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Detta infogade skript uppdaterar också _iisstart.html_ -innehållet.

Du måste också öppna HTTP-porten så att du kan komma åt webb servern.

1. Sök `securityRules` i mallen.
1. Lägg till följande regel bredvid **standard-Allow-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Distribuera mallen

Information om distributions proceduren finns i avsnittet **distribuera mallen** i [Självstudier: skapa arm-mallar med beroende resurser](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Du bör använda ett genererat lösenord för den virtuella datorns administratörskonto. Läs mer i avsnittet om [förutsättningar](#prerequisites) i den här artikeln.

Kör följande kommando från Cloud Shell för att hämta den offentliga IP-adressen för den virtuella datorn:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Klistra in IP-adressen i en webbläsare. Välkomstsidan för Internet Information Services (IIS) öppnas:

![Välkomstsida för Internet Information Services](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Rensa resurser

När du inte längre behöver Azure-resurserna du har distribuerat rensar du bort dem genom att ta bort resursgruppen.

1. Välj **Resursgrupp** i den vänstra rutan i Azure Portal.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.
    Sex resurser visas i resursgruppen.
4. Välj **Ta bort resursgrupp** på menyn längst upp.

## <a name="next-steps"></a>Nästa steg

I den här självstudien distribuerade du en virtuell dator och ett tillägg för virtuell dator. Tillägget installerade IIS-webbservern på den virtuella datorn. Du kan läsa om hur du använder Azure SQL Database-tillägget till att importera en BACPAC-fil här:

> [!div class="nextstepaction"]
> [Distribuera SQL-tillägg](./template-tutorial-deploy-sql-extensions-bacpac.md)
