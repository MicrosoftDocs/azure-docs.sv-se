---
title: 'Snabb start: skapa ett Azure avdelningens kontroll-konto med Azure PowerShell/Azure CLI (för hands version)'
description: I den här snabb starten beskrivs hur du skapar ett Azure avdelningens kontroll-konto med hjälp av Azure PowerShell/Azure CLI.
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: quickstart
ms.date: 11/23/2020
ms.openlocfilehash: 0698295688a4587a704e8cdba0a4796e8d1e6fcd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98880007"
---
# <a name="quickstart-create-an-azure-purview-account-using-azure-powershellazure-cli"></a>Snabb start: skapa ett Azure avdelningens kontroll-konto med Azure PowerShell/Azure CLI

> [!IMPORTANT]
> Azure avdelningens kontroll är för närvarande en för hands version. Kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner innehåller ytterligare juridiska villkor som gäller för Azure-funktioner som är beta, för hands version eller på annat sätt ännu inte har publicerats i allmän tillgänglighet.

I den här snabb starten skapar du ett Azure avdelningens kontroll-konto med hjälp av Azure PowerShell/Azure CLI.

## <a name="prerequisites"></a>Förutsättningar

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Det användar konto som du använder för att logga in på Azure måste vara medlem i rollen deltagare eller ägare, eller en administratör för Azure-prenumerationen.

* En egen [klientorganisation i Azure Active Directory](../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

* Installera antingen Azure PowerShell eller Azure CLI på klient datorn för att distribuera mallen: [distribution av kommando raden](../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-cli#command-line-deployment)

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på [Azure-portalen](https://portal.azure.com) med ditt Azure-konto.

## <a name="configure-your-subscription"></a>Konfigurera din prenumeration

Om det behövs följer du de här stegen för att konfigurera din prenumeration så att Azure-avdelningens kontroll kan köras i din prenumeration:

   1. Sök efter och välj **prenumerationer** i Azure Portal.

   1. I listan över prenumerationer väljer du den prenumeration som du vill använda. Administrativ behörighet för prenumerationen krävs.

      :::image type="content" source="./media/create-catalog-portal/select-subscription.png" alt-text="Skärm bild som visar hur du väljer en prenumeration i Azure Portal.":::

   1. För din prenumeration väljer du **resurs leverantörer**. I fönstret **resurs leverantörer** söker du efter och registrerar alla tre resurs leverantörer: 
       1. **Microsoft. avdelningens kontroll**
       1. **Microsoft.Storage**
       1. **Microsoft. EventHub** 
      
      Om de inte är registrerade registrerar du dem genom att välja **Registrera**.

      :::image type="content" source="./media/create-catalog-portal/register-purview-resource-provider.png" alt-text="Skärm bild som visar hur du registrerar Microsoft dot Azure avdelningens kontroll Resource Provider i Azure Portal.":::

## <a name="create-an-azure-purview-account-instance"></a>Skapa en instans av Azure avdelningens kontroll-konto

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

1. Skapa en resurs grupp för ditt avdelningens kontroll-konto. Du kan hoppa över det här steget om du redan har ett:

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

1. Skapa en mall för avdelningens kontroll, till exempel `purviewtemplate.json` . Du kan uppdatera `name` , `location` och `capacity` ( `4` eller `16` ):

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

1. Distribuera avdelningens kontroll-mall

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

1. Distributions kommandot returnerar resultat. Leta efter för `ProvisioningState` att se om distributionen har slutförts.
    
## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du skapar ett Azure avdelningens kontroll-konto.

Gå vidare till nästa artikel om du vill lära dig hur du ger användare åtkomst till ditt Azure avdelningens kontroll-konto. 

> [!div class="nextstepaction"]
> [Lägg till användare till ditt Azure avdelningens kontroll-konto](catalog-permissions.md)