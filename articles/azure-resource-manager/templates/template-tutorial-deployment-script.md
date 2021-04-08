---
title: Använd mallar för mall distribution | Microsoft Docs
description: Lär dig hur du använder distributions skript i Azure Resource Manager mallar (ARM-mallar).
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/16/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 36fb54b4b6521d87c7461936c84a644bf22f7e31
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963971"
---
# <a name="tutorial-use-deployment-scripts-to-create-a-self-signed-certificate"></a>Självstudie: använda distributions skript för att skapa ett självsignerat certifikat

Lär dig hur du använder distributions skript i Azure Resource Manager mallar (ARM-mallar). Distributions skript kan användas för att utföra anpassade steg som inte kan utföras av ARM-mallar. Du kan till exempel skapa ett självsignerat certifikat. I den här självstudien skapar du en mall för att distribuera ett Azure Key Vault och använder sedan en `Microsoft.Resources/deploymentScripts` resurs i samma mall för att skapa ett certifikat och sedan lägga till certifikatet i nyckel valvet. Mer information om distributions skript finns [i använda distributions skript i arm-mallar](./deployment-script-template.md).

> [!IMPORTANT]
> Två distributions skript resurser, ett lagrings konto och en behållar instans, skapas i samma resurs grupp för skript körning och fel sökning. Dessa resurser tas vanligt vis bort av skript tjänsten när skript körningen blir i ett Terminal-tillstånd. Du debiteras för resurserna tills resurserna tas bort. Mer information finns i [Rensa distribution skript resurser](./deployment-script-template.md#clean-up-deployment-script-resources).

Den här självstudien omfattar följande uppgifter:

> [!div class="checklist"]
> * Öppna en snabbstartsmall
> * Redigera mallen
> * Distribuera mallen
> * Felsöka skriptet som misslyckades
> * Rensa resurser

En Microsoft Learn-modul som täcker distributions skript finns i [utöka arm-mallar med hjälp av distributions skript](/learn/modules/extend-resource-manager-template-deployment-scripts/).

## <a name="prerequisites"></a>Förutsättningar

För att kunna följa stegen i den här artikeln behöver du:

* **[Visual Studio Code](https://code.visualstudio.com/) med tillägget Resource Manager-verktyg**. Se [snabb start: skapa arm-mallar med Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md).

* **En användare som tilldelats en hanterad identitet**. Den här identiteten används för att utföra Azure-/regionsspecifika åtgärder i skriptet. Information om hur du skapar ett finns i [användardefinierad hanterad identitet](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). Du behöver identitets-ID när du distribuerar mallen. Formatet på identiteten är:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Använd följande CLI-skript för att hämta ID: t genom att ange resurs gruppens namn och identitetens namn.

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  az identity list -g $resourceGroupName
  ```

## <a name="open-a-quickstart-template"></a>Öppna en snabbstartsmall

I stället för att skapa en mall från början öppnar du en mall från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/). Azure snabb starts mallar är en lagrings plats för ARM-mallar.

Mallen som används i den här snabb starten kallas för att [skapa en Azure Key Vault och en hemlighet](https://azure.microsoft.com/resources/templates/101-key-vault-create/). Mallen skapar ett nyckel valv och lägger sedan till en hemlighet i nyckel valvet.

1. Från Visual Studio **Code väljer du**  >  **Öppna fil**.
2. I **Filnamn** klistrar du in följande URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-key-vault-create/azuredeploy.json
    ```

3. Välj **Öppna** för att öppna filen.
4. Välj **Arkiv**  >  **Spara som** för att spara filen som _azuredeploy.jspå_ den lokala datorn.

## <a name="edit-the-template"></a>Redigera mallen

Gör följande ändringar i mallen:

### <a name="clean-up-the-template-optional"></a>Rensa mallen (valfritt)

Den ursprungliga mallen lägger till en hemlighet till nyckel valvet. För att förenkla självstudien tar du bort följande resurs:

* `Microsoft.KeyVault/vaults/secrets`

Ta bort följande två parameter definitioner:

* `secretName`
* `secretValue`

Om du väljer att inte ta bort dessa definitioner måste du ange parameter värden under distributionen.

### <a name="configure-the-key-vault-access-policies"></a>Konfigurera åtkomst principer för nyckel valv

Distributions skriptet lägger till ett certifikat i nyckel valvet. Konfigurera åtkomst principer för nyckel valv för att ge behörighet till den hanterade identiteten:

1. Lägg till en parameter för att hämta ID: t för den hanterade identiteten:

    ```json
    "identityId": {
      "type": "string",
      "metadata": {
        "description": "Specifies the ID of the user-assigned managed identity."
      }
    },
    ```

    > [!NOTE]
    > Resurs hanterarens mall tillägg för Visual Studio Code kan inte formatera distributions skript ännu. Använd inte Skift + Alt + F för att formatera `deploymentScripts` resurserna, t. ex. följande.

1. Lägg till en parameter för att konfigurera åtkomst principer för nyckel valv så att den hanterade identiteten kan lägga till certifikat i nyckel valvet:

    ```json
    "certificatesPermissions": {
      "type": "array",
      "defaultValue": [
        "get",
        "list",
        "update",
        "create"
      ],
      "metadata": {
      "description": "Specifies the permissions to certificates in the vault. Valid values are: all, get, list, update, create, import, delete, recover, backup, restore, manage contacts, manage certificate authorities, get certificate authorities, list certificate authorities, set certificate authorities, delete certificate authorities."
      }
    }
    ```

1. Uppdatera de befintliga åtkomst principerna för Key Vault till:

    ```json
    "accessPolicies": [
      {
        "objectId": "[parameters('objectId')]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      },
      {
        "objectId": "[reference(parameters('identityId'), '2018-11-30').principalId]",
        "tenantId": "[parameters('tenantId')]",
        "permissions": {
          "keys": "[parameters('keysPermissions')]",
          "secrets": "[parameters('secretsPermissions')]",
          "certificates": "[parameters('certificatesPermissions')]"
        }
      }
    ],
    ```

    Det finns två definierade principer, en för den inloggade användaren och den andra för den hanterade identiteten. Den inloggade användaren behöver bara *list* behörighet för att verifiera distributionen. För att förenkla självstudierna tilldelas samma certifikat både den hanterade identiteten och de inloggade användarna.

### <a name="add-the-deployment-script"></a>Lägg till distributions skriptet

1. Lägg till tre parametrar som används av distributions skriptet:

    ```json
    "certificateName": {
      "type": "string",
      "defaultValue": "DeploymentScripts2019"
    },
    "subjectName": {
      "type": "string",
      "defaultValue": "CN=contoso.com"
    },
    "utcValue": {
      "type": "string",
      "defaultValue": "[utcNow()]"
    }
    ```

1. Lägg till en `deploymentScripts` resurs:

    > [!NOTE]
    > Eftersom de infogade distributions skripten omges av dubbla citat tecken måste strängarna inuti distributions skripten omges av enkla citat tecken i stället. [PowerShell-escape-tecken](/powershell/module/microsoft.powershell.core/about/about_quoting_rules#single-and-double-quoted-strings) är bakticket ( `` ` `` ).

    ```json
    {
      "type": "Microsoft.Resources/deploymentScripts",
      "apiVersion": "2020-10-01",
      "name": "createAddCertificate",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
      ],
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[parameters('identityId')]": {
          }
        }
      },
      "kind": "AzurePowerShell",
      "properties": {
        "forceUpdateTag": "[parameters('utcValue')]",
        "azPowerShellVersion": "3.0",
        "timeout": "PT30M",
        "arguments": "[format(' -vaultName {0} -certificateName {1} -subjectName {2}', parameters('keyVaultName'), parameters('certificateName'), parameters('subjectName'))]", // can pass an argument string, double quotes must be escaped
        "scriptContent": "
          param(
            [string] [Parameter(Mandatory=$true)] $vaultName,
            [string] [Parameter(Mandatory=$true)] $certificateName,
            [string] [Parameter(Mandatory=$true)] $subjectName
          )

          $ErrorActionPreference = 'Stop'
          $DeploymentScriptOutputs = @{}

          $existingCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

          if ($existingCert -and $existingCert.Certificate.Subject -eq $subjectName) {

            Write-Host 'Certificate $certificateName in vault $vaultName is already present.'

            $DeploymentScriptOutputs['certThumbprint'] = $existingCert.Thumbprint
            $existingCert | Out-String
          }
          else {
            $policy = New-AzKeyVaultCertificatePolicy -SubjectName $subjectName -IssuerName Self -ValidityInMonths 12 -Verbose

            # private key is added as a secret that can be retrieved in the Resource Manager template
            Add-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName -CertificatePolicy $policy -Verbose

            $newCert = Get-AzKeyVaultCertificate -VaultName $vaultName -Name $certificateName

            # it takes a few seconds for KeyVault to finish
            $tries = 0
            do {
              Write-Host 'Waiting for certificate creation completion...'
              Start-Sleep -Seconds 10
              $operation = Get-AzKeyVaultCertificateOperation -VaultName $vaultName -Name $certificateName
              $tries++

              if ($operation.Status -eq 'failed')
              {
                throw 'Creating certificate $certificateName in vault $vaultName failed with error $($operation.ErrorMessage)'
              }

              if ($tries -gt 120)
              {
                throw 'Timed out waiting for creation of certificate $certificateName in vault $vaultName'
              }
            } while ($operation.Status -ne 'completed')

            $DeploymentScriptOutputs['certThumbprint'] = $newCert.Thumbprint
            $newCert | Out-String
          }
        ",
        "cleanupPreference": "OnSuccess",
        "retentionInterval": "P1D"
      }
    }
    ```

    `deploymentScripts`Resursen är beroende av Key Vault-resursen och roll tilldelnings resursen. Den har följande egenskaper:

    * `identity`: Ett distributions skript använder en användardefinierad hanterad identitet för att utföra åtgärderna i skriptet.
    * `kind`: Ange typ av skript. För närvarande stöds endast PowerShell-skript.
    * `forceUpdateTag`: Avgör om distributions skriptet ska köras även om skript källan inte har ändrats. Kan vara aktuell tidstämpel eller ett GUID. Mer information finns i [Kör skript mer än en gång](./deployment-script-template.md#run-script-more-than-once).
    * `azPowerShellVersion`: Anger den version av Azure PowerShell-modul som ska användas. För närvarande stöder distributions skriptet version 2.7.0, 2.8.0 och 3.0.0.
    * `timeout`: Ange den högsta tillåtna körnings tiden för skript som anges i [ISO 8601-formatet](https://en.wikipedia.org/wiki/ISO_8601). Standardvärdet är **P1D**.
    * `arguments`: Ange parameter värden. Värdena avgränsas med blank steg.
    * `scriptContent`: Ange skript innehållet. Använd i stället om du vill köra ett externt skript `primaryScriptURI` . Mer information finns i [Använd externt skript](./deployment-script-template.md#use-external-scripts).
        Att deklarera `$DeploymentScriptOutputs` krävs endast när du testar skriptet på en lokal dator. Att deklarera variabeln gör att skriptet kan köras på en lokal dator och i en `deploymentScript` resurs utan att behöva göra några ändringar. Värdet `$DeploymentScriptOutputs` som tilldelas är tillgängligt som utdata i distributionerna. Mer information finns i [arbeta med utdata från PowerShell-distributions skript](./deployment-script-template.md#work-with-outputs-from-powershell-script) eller [arbeta med utdata från skript för cli-distribution](./deployment-script-template.md#work-with-outputs-from-cli-script).
    * `cleanupPreference`: Ange inställningen när du vill ta bort distributions skript resurser. Standardvärdet är **Always**, vilket innebär att distributions skript resurserna tas bort trots att terminalens tillstånd (lyckades, misslyckades, avbröts). I den här självstudien används **OnSuccess** så att du får chansen att visa skript körnings resultaten.
    * `retentionInterval`: Ange med vilket intervall som tjänsten ska behålla skript resurserna när den har nått ett Terminal-tillstånd. Resurserna tas bort när denna varaktighet går ut. Varaktigheten baseras på ISO 8601-mönstret. I den här självstudien används **P1D**, vilket innebär en dag. Den här egenskapen används när `cleanupPreference` är inställd på **OnExpiration**. Den här egenskapen har inte Aktiver ATS för tillfället.

    Distributions skriptet tar tre parametrar: `keyVaultName` , `certificateName` och `subjectName` . Det skapar ett certifikat och lägger sedan till certifikatet i nyckel valvet.

    `$DeploymentScriptOutputs` används för att lagra utdata svärdet. Mer information finns i [arbeta med utdata från PowerShell-distributions skript](./deployment-script-template.md#work-with-outputs-from-powershell-script) eller [arbeta med utdata från skript för cli-distribution](./deployment-script-template.md#work-with-outputs-from-cli-script).

    Du hittar den färdiga mallen [här](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json).

1. Om du vill se fel söknings processen placerar du ett fel i koden genom att lägga till följande rad i distributions skriptet:

    ```powershell
    Write-Output1 $keyVaultName
    ```

    Det korrekta kommandot är `Write-Output` i stället för `Write-Output1` .

1. Spara filen genom att välja **Arkiv**  >  **Spara** .

## <a name="deploy-the-template"></a>Distribuera mallen

1. Logga in på [Azure Cloud Shell](https://shell.azure.com)

1. Välj önskad miljö genom att välja antingen **PowerShell** eller **bash** (för CLI) i det övre vänstra hörnet. Du måste starta om gränssnittet när du byter.

    ![Azure Portal Cloud Shell Ladda upp fil](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Välj **Ladda upp/ned filer** och välj sedan **Ladda upp**. Se föregående skärmbild.  Välj den fil som du sparade i föregående avsnitt. När du har överfört filen kan du använda `ls` kommandot och `cat` kommandot för att kontrol lera att filen har överförts.

1. Kör följande PowerShell-skript för att distribuera mallen.

    ```azurepowershell-interactive
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource names"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
    $identityId = Read-Host -Prompt "Enter the user-assigned managed identity ID"

    $adUserId = (Get-AzADUser -UserPrincipalName $upn).Id
    $resourceGroupName = "${projectName}rg"
    $keyVaultName = "${projectName}kv"

    New-AzResourceGroup -Name $resourceGroupName -Location $location

    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -identityId $identityId -keyVaultName $keyVaultName -objectId $adUserId

    Write-Host "Press [ENTER] to continue ..."
    ```

    Distributions skript tjänsten måste skapa ytterligare distributions skript resurser för skript körning. Förberedelse-och rensnings processen kan ta upp till en minut att slutföra förutom den faktiska skript körnings tiden.

    Distributionen misslyckades eftersom det ogiltiga kommandot `Write-Output1` används i skriptet. Du får ett fel meddelande som säger:

    ```error
    The term 'Write-Output1' is not recognized as the name of a cmdlet, function, script file, or operable
    program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
    ```

    Resultatet av distributions skript körningen lagras i distributions skriptets resurser för fel söknings ändamål.

## <a name="debug-the-failed-script"></a>Felsöka skriptet som misslyckades

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Öppna resurs gruppen. Det är projekt namnet med **RG** tillagda. Du kommer att se två ytterligare resurser i resurs gruppen. Dessa resurser kallas för *distributions skript resurser*.

    ![Distributions skript resurser för Resource Manager-mall](./media/template-tutorial-deployment-script/resource-manager-template-deployment-script-resources.png)

    Båda filerna har _azscripts_ -suffixet. Ett är ett lagrings konto och det andra är en behållar instans.

    Välj **Visa dolda typer** för att visa `deploymentScripts` resursen.

1. Välj lagrings kontot med _azscripts_ -suffixet.
1. Välj panelen **fil resurser** . Du kommer att se en _azscripts_ -mapp som innehåller distributions skriptets körnings filer.
1. Välj _azscripts_. Du kommer att se två mappar _azscriptinput_ och _azscriptoutput_. Mappen indata innehåller en system PowerShell-skriptfil och skript filen för användar distribution. Mappen utdata innehåller en _executionresult.jspå_ och skript utdatafilen. Du kan se fel meddelandet i _executionresult.jspå_. Utdatafilen saknas eftersom körningen misslyckades.

Ta bort `Write-Output1` raden och distribuera om mallen.

När den andra distributionen körs tas distributions skript resurserna bort av skript tjänsten eftersom `cleanupPreference` egenskapen är inställd på **OnSuccess**.

## <a name="clean-up-resources"></a>Rensa resurser

När Azure-resurserna inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

1. Från Azure Portal väljer du **resurs grupp** på den vänstra menyn.
2. Ange resursgruppens namn i fältet **Filtrera efter namn**.
3. Välj resursgruppens namn.  Du får totalt sex resurser i resurs gruppen.
4. Välj **ta bort resurs grupp** på den översta menyn.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder ett distributions skript i ARM-mallar. Information om hur du lär dig att distribuera Azure-resurser baserat på villkor finns i:

> [!div class="nextstepaction"]
> [Använda villkor](./template-tutorial-use-conditions.md)
