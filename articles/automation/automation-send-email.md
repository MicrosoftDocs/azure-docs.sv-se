---
title: Skicka ett e-postmeddelande från en Azure Automation runbook
description: Den här artikeln beskriver hur du skickar ett e-postmeddelande från en runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/05/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2650e3a9ce58b611c1aff1a569cc1e8f0980fd4
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833498"
---
# <a name="send-an-email-from-a-runbook"></a>Skicka ett e-postmeddelande från en runbook

Du kan skicka ett e-postmeddelande från en runbook med [SendGrid med](https://sendgrid.com/solutions) hjälp av PowerShell. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon ännu kan du aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Ett SendGrid-konto](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account).
* [Automation-konto](./index.yml) med **Az-moduler.**
* [Kör som-konto](./automation-security-overview.md#run-as-accounts) för att lagra och köra runbooken.

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

Du kan skapa en Azure Key Vault med hjälp av följande PowerShell-skript. Ersätt variabelvärdena med värden som är specifika för din miljö. Använd den inbäddade Azure Cloud Shell via **knappen Prova,** som finns i det övre högra hörnet av kodblocket. Du kan också kopiera och köra koden lokalt om du har [Az-modulerna](/powershell/azure/install-az-ps) installerade på den lokala datorn.

> [!NOTE]
> Hämta DIN API-nyckel med hjälp av stegen i [Hitta sendGrid API-nyckeln](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key).

```azurepowershell-interactive
$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
# If you omit the SubscriptionId parameter, the default subscription is selected.
Connect-AzAccount -SubscriptionId $SubscriptionId

# Use Get-AzLocation to see your available locations.
$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "<Enter a universally unique vault name>"
$SendGridAPIKey = "<SendGrid API key>"
$AutomationAccountName = "testaa"

# Create new Resource Group, or omit this step if you already have a resource group.
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName -Location $region
$resourceId = $newKeyVault.ResourceId

# Convert the SendGrid API key into a SecureString
$Secret = ConvertTo-SecureString -String $SendGridAPIKey -AsPlainText -Force
Set-AzKeyVaultSecret -VaultName $VaultName -Name 'SendGridAPIKey' -SecretValue $Secret

# Grant access to the Key Vault to the Automation Run As account.
$connection = Get-AzAutomationConnection -ResourceGroupName $KeyVaultResourceGroupName -AutomationAccountName $AutomationAccountName -Name AzureRunAsConnection
$appID = $connection.FieldDefinitionValues.ApplicationId
Set-AzKeyVaultAccessPolicy -VaultName $VaultName -ServicePrincipalName $appID -PermissionsToSecrets Set, Get
```

Andra sätt att skapa en Azure Key Vault och lagra en hemlighet finns i [Key Vault snabbstarter](../key-vault/index.yml).

## <a name="import-required-modules-into-your-automation-account"></a>Importera nödvändiga moduler till ditt Automation-konto

Om du Azure Key Vault inom en Runbook måste du importera följande moduler till ditt Automation-konto:

* [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Anvisningar finns i Importera [Az-moduler.](shared-resources/modules.md#import-az-modules)

## <a name="create-the-runbook-to-send-an-email"></a>Skapa en runbook för att skicka ett e-postmeddelande

När du har skapat en Key Vault och lagrat DIN API-nyckel är det dags att skapa runbooken som hämtar API-nyckeln och `SendGrid` skickar ett e-postmeddelande. Nu ska vi använda en runbook som använder som ett Kör som-konto för att autentisera med Azure för att `AzureRunAsConnection` hämta hemligheten från Azure Key Vault. [](./automation-security-overview.md#run-as-accounts) Vi anropar runbooken **Send-GridMailMessage**. Du kan ändra det PowerShell-skript som används i exempelsyfte och återanvända det för olika scenarier.

1. Gå till ditt Azure Automation konto.
2. Under **Processautomatisering** väljer **du Runbooks**.
3. Längst upp i listan över runbooks väljer du **+ Skapa en runbook.**
4. På sidan Lägg till runbook anger du **Send-GridMailMessage** som runbooknamn. Som Runbook-typ väljer du **PowerShell**. Välj sedan **Skapa**.
   ![Skapa runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbooken skapas och sidan Redigera PowerShell-runbook öppnas.
   ![Redigera Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Kopiera följande PowerShell-exempel till sidan Redigera. Kontrollera att `VaultName` anger det namn som du har valt för Key Vault.

    ```powershell-interactive
    Param(
      [Parameter(Mandatory=$True)]
      [String] $destEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $fromEmailAddress,
      [Parameter(Mandatory=$True)]
      [String] $subject,
      [Parameter(Mandatory=$True)]
      [String] $content
    )

    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Out-Null
    $VaultName = "<Enter your vault name>&quot;
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name &quot;SendGridAPIKey").SecretValue
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Authorization", "Bearer " + $SENDGRID_API_KEY)
    $headers.Add("Content-Type", "application/json")

    $body = @{
    personalizations = @(
        @{
            to = @(
                    @{
                        email = $destEmailAddress
                    }
            )
        }
    )
    from = @{
        email = $fromEmailAddress
    }
    subject = $subject
    content = @(
        @{
            type = "text/plain"
            value = $content
        }
    )
    }

    $bodyJson = $body | ConvertTo-Json -Depth 4

    $response = Invoke-RestMethod -Uri https://api.sendgrid.com/v3/mail/send -Method Post -Headers $headers -Body $bodyJson
    ```

7. Välj **Publicera** för att spara och publicera runbooken.

Du kan kontrollera att runbooken körs korrekt genom att följa stegen under [Testa en runbook](manage-runbooks.md#test-a-runbook) eller [Starta en runbook.](start-runbooks.md)

Om du inte ser testmeddelandet kontrollerar du  **skräppostmapparna.**

## <a name="clean-up-resources-after-the-email-operation"></a>Rensa resurser efter e-poståtgärden

1. När runbooken inte längre behövs markerar du den i runbook-listan och klickar på Ta **bort.**

2. Ta bort Key Vault med hjälp av [cmdleten Remove-AzKeyVault.](/powershell/module/az.keyvault/remove-azkeyvault)

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du skickar runbook-jobbdata till Log Analytics-arbetsytan finns [i Vidarebefordra Azure Automation jobbdata till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).
* Information om hur du övervakar mått och loggar på basnivå finns i [Använda en avisering för att utlösa en Azure Automation runbook](automation-create-alert-triggered-runbook.md).
* Information om hur du korrigerar problem som uppstår under Runbook-åtgärder [finns i Felsöka runbook-problem.](./troubleshoot/runbooks.md)
