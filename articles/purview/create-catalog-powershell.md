---
title: 'Snabbstart: Skapa ett Azure Purview-konto med Azure PowerShell/Azure CLI (förhandsversion)'
description: Den här snabbstarten beskriver hur du skapar ett Azure Purview-konto med Azure PowerShell/Azure CLI.
author: hophanms
ms.author: hophan
ms.date: 11/23/2020
ms.topic: quickstart
ms.service: purview
ms.subservice: purview-data-catalog
ms.custom:
- mode-api
ms.openlocfilehash: 6266aedaec8f171a1a6ff3e0d15abdad0263767a
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530869"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Snabbstart: Skapa ett Azure Purview-konto med Azure PowerShell/Azure CLI

> [!IMPORTANT]
> Azure Purview finns för närvarande i FÖRHANDSVERSION. De [kompletterande användningsvillkoren för Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) förhandsversioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som inte har släppts i allmän tillgänglighet ännu.

I den här snabbstarten skapar du ett Azure Purview-konto med Azure PowerShell/Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Det användarkonto som du använder för att logga in på Azure måste vara medlem i rollen deltagare eller ägare eller administratör för Azure-prenumerationen.

* En egen [klientorganisation i Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Installera antingen Azure PowerShell eller Azure CLI på klientdatorn för att distribuera mallen: [Kommandoradsdistribution](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration

Om det behövs följer du dessa steg för att konfigurera din prenumeration så att Azure Purview kan köras i din prenumeration:

   1. I Azure Portal du efter och väljer **Prenumerationer**.

   1. I listan över prenumerationer väljer du den prenumeration som du vill använda. Administrativ åtkomstbehörighet för prenumerationen krävs.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Skärmbild som visar hur du väljer en prenumeration i Azure Portal.":::

   1. För din prenumeration väljer du **Resursproviders.** I fönstret **Resursproviders** söker du efter och registrerar alla tre resursproviders: 
       1. **Microsoft.Purview**
       1. **Microsoft.Storage**
       1. **Microsoft.EventHub** 
      
      Om de inte är registrerade registrerar du den genom att välja **Registrera**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Skärmbild som visar hur du registrerar Resursprovidern Microsoft dot Azure Purview i Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Skapa en Azure Purview-kontoinstans

1. Logga in med dina Azure-autentiseringsuppgifter

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Connect-AzAccount
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az login
    ```
    
    ---

1. Om du har flera Azure-prenumerationer väljer du den prenumeration som du vill använda:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    Set-AzContext [SubscriptionID/SubscriptionName]
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az account set --subscription [SubscriptionID/SubscriptionName]
    ```
    
    ---

1. Skapa en resursgrupp för ditt Purview-konto. Du kan hoppa över det här steget om du redan har ett:

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroup `
      -Name myResourceGroup `
      -Location "East US"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create \
      --name myResourceGroup \
      --location "East US"
    ```
    
    ---

1. Skapa en Purview-mallfil, till exempel `purviewtemplate.json` . Du kan uppdatera `name` , och ( eller `location` `capacity` `4` `16` ):

    ```json
    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": [
        {
          "name": "<yourPurviewAccountName>",
          "type": "Microsoft.Purview/accounts",
          "apiVersion": "2020-12-01-preview",
          "location": "EastUs",
          "identity": {
            "type": "SystemAssigned"
          },
          "properties": {
            "networkAcls": {
              "defaultAction": "Allow"
            }
          },
          "dependsOn": [],
          "sku": {
            "name": "Standard",
            "capacity": "4"
          },
          "tags": {}
        }
      ],
      "outputs": {}
    }
    ```

1. Distribuera Purview-mall

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName "<myResourceGroup>" -TemplateFile "<PATH TO purviewtemplate.json>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    För att köra det här distributionskommandot måste du ha den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.
    
    ```azurecli
    az deployment group create --resource-group "<myResourceGroup>" --template-file "<PATH TO purviewtemplate.json>"
    ```
    
    ---

1. Distributionskommandot returnerar resultat. Leta efter `ProvisioningState` för att se om distributionen lyckades.
    
## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du skapar ett Azure Purview-konto.

Gå vidare till nästa artikel om du vill lära dig hur du ger användare åtkomst till ditt Azure Purview-konto. 

> [!div class="nextstepaction"]
> [Lägga till användare i ditt Azure Purview-konto](catalog-permissions.md)
