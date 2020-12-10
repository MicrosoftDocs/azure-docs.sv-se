---
title: Skicka ett e-postmeddelande från en Azure Automation Runbook
description: Den här artikeln beskriver hur du skickar ett e-postmeddelande inifrån en Runbook.
services: automation
ms.subservice: process-automation
ms.date: 07/15/2019
ms.topic: conceptual
ms.openlocfilehash: a81ae680a5f04eca0a6cc01ee24b474cc5daabea
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97005213"
---
# <a name="send-an-email-from-a-runbook"></a>Skicka ett e-postmeddelande från en runbook

Du kan skicka ett e-postmeddelande från en Runbook med [SendGrid](https://sendgrid.com/solutions) med hjälp av PowerShell. 

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har något än kan du  [aktivera din prenumeration på MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Ett SendGrid-konto](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account).
* [Automation-konto](./index.yml) med **AZ** -moduler.
* [Kör som-konto](./manage-runas-account.md) för att lagra och köra runbooken.

## <a name="create-an-azure-key-vault"></a>Skapa ett Azure Key Vault

Du kan skapa en Azure Key Vault med hjälp av följande PowerShell-skript. Ersätt variabel värden med värden som är speciella för din miljö. Använd den inbäddade Azure Cloud Shell via knappen **prova** , som finns i det övre högra hörnet av kod blocket. Du kan också kopiera och köra koden lokalt om du har AZ- [modulerna](/powershell/azure/install-az-ps) installerade på den lokala datorn.

> [!NOTE]
> Hämta din API-nyckel genom att följa stegen i [hitta din SENDGRID API-nyckel](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key).

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

För andra sätt att skapa en Azure Key Vault och lagra en hemlighet, se [Key Vault snabb starter](../key-vault/index.yml).

## <a name="import-required-modules-into-your-automation-account"></a>Importera nödvändiga moduler till ditt Automation-konto

Om du vill använda Azure Key Vault i en Runbook måste du importera följande moduler till ditt Automation-konto:

* [Az.Profile](https://www.powershellgallery.com/packages/Az.Profile)
* [Az.KeyVault](https://www.powershellgallery.com/packages/Az.KeyVault)

Instruktioner finns i [Importera AZ-moduler](shared-resources/modules.md#import-az-modules).

## <a name="create-the-runbook-to-send-an-email"></a>Skapa en Runbook för att skicka ett e-postmeddelande

När du har skapat en Key Vault och sparat `SendGrid` API-nyckeln är det dags att skapa den Runbook som hämtar API-nyckeln och skickar ett e-postmeddelande. Vi använder en Runbook som använder `AzureRunAsConnection` som ett [Kör som-konto](./manage-runas-account.md) för att autentisera med Azure för att hämta hemligheten från Azure Key Vault. Vi kallar Runbook **send-GridMailMessage**. Du kan ändra PowerShell-skriptet som används i exempel syfte och återanvända det för olika scenarier.

1. Gå till ditt Azure Automation-konto.
2. Under **process automatisering** väljer du **Runbooks**.
3. Längst upp i listan över Runbooks väljer du **+ skapa en Runbook**.
4. På sidan Lägg till Runbook anger du **send-GridMailMessage** som Runbook-namn. För Runbook-typen väljer du **PowerShell**. Välj sedan **Skapa**.
   ![Skapa Runbook](./media/automation-send-email/automation-send-email-runbook.png)
5. Runbooken skapas och sidan Redigera PowerShell-runbook öppnas.
   ![Redigera Runbook](./media/automation-send-email/automation-send-email-edit.png)
6. Kopiera följande PowerShell-exempel till sidan Redigera. Kontrol lera att `VaultName` anger det namn som du har valt för Key Vault.

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
    $VaultName = "<Enter your vault name>"
    $SENDGRID_API_KEY = (Get-AzKeyVaultSecret -VaultName $VaultName -Name "SendGridAPIKey").SecretValue
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

7. Välj **publicera** för att spara och publicera runbooken.

Du kan kontrol lera att runbooken körs utan problem genom att följa stegen under [testa en Runbook](manage-runbooks.md#test-a-runbook) eller [starta en Runbook](start-runbooks.md).

Om du inte först ser ditt test-e-postmeddelande kontrollerar du mapparna för **skräp post** och **skräp** post.

## <a name="clean-up-resources-after-the-email-operation"></a>Rensa resurser efter e-poståtgärden

1. När runbooken inte längre behövs väljer du den i Runbook-listan och klickar på **ta bort**.

2. Ta bort Key Vault med hjälp av cmdleten [Remove-AzKeyVault](/powershell/module/az.keyvault/remove-azkeyvault?view=azps-3.7.0) .

```azurepowershell-interactive
$VaultName = "<your KeyVault name>"
$ResourceGroupName = "<your ResourceGroup name>"
Remove-AzKeyVault -VaultName $VaultName -ResourceGroupName $ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du skickar Runbook-jobb till din Log Analytics-arbetsyta finns i [vidarebefordra Azure Automation jobb data till Azure Monitor loggar](automation-manage-send-joblogs-log-analytics.md).
* Information om hur du övervakar mått och loggar på bas nivå finns i [använda en avisering för att utlösa en Azure Automation Runbook](automation-create-alert-triggered-runbook.md).
* Information om hur du korrigerar problem som uppstår under Runbook-åtgärder finns i [Felsöka Runbook-problem](./troubleshoot/runbooks.md).
