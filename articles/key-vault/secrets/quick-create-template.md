---
title: Azure snabbstart – Skapa ett Azure-nyckelvalv och en hemlighet med hjälp av Azure Resource Manager mall | Microsoft Docs
description: Snabbstart som visar hur du skapar Azure-nyckelvalv och lägger till hemligheter i valven med hjälp Azure Resource Manager mall.
services: key-vault
author: mumian
manager: dougeby
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc,subject-armqs
ms.date: 02/27/2020
ms.author: jgao
ms.openlocfilehash: 214c86eb7272c87e067b1d5f6df0b09ce9e7095c
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814144"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-an-arm-template"></a>Snabbstart: Ange och hämta en hemlighet från Azure Key Vault med hjälp av en ARM-mall

[Azure Key Vault](../general/overview.md) är en molntjänst som tillhandahåller ett säkert lager för hemligheter, till exempel nycklar, lösenord, certifikat och andra hemligheter. Den här snabbstarten fokuserar på processen att distribuera en Azure Resource Manager (ARM-mall) för att skapa ett nyckelvalv och en hemlighet.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Om din miljö uppfyller förhandskraven och du är van att använda ARM-mallar väljer du knappen **Distribuera till Azure**. Mallen öppnas på Azure-portalen.

[![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Krav

Så här slutför du den här artikeln:

* Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Mallen behöver ditt användarobjekts-ID för Azure AD för att konfigurera behörigheter. Följande procedur hämtar objekt-ID:t (GUID).

    1. Kör följande kommando Azure PowerShell Azure CLI genom att **välja Prova** och klistra sedan in skriptet i gränssnittsfönstret. Klistra in skriptet genom att högerklicka på gränssnittet och sedan välja Klistra **in.**

        # <a name="cli"></a>[CLI](#tab/CLI)
        ```azurecli-interactive
        echo "Enter your email address that is used to sign in to Azure:" &&
        read upn &&
        az ad user show --id $upn --query "objectId" &&
        echo "Press [ENTER] to continue ..."
        ```

        # <a name="powershell"></a>[PowerShell](#tab/PowerShell)
        ```azurepowershell-interactive
        $upn = Read-Host -Prompt "Enter your email address used to sign in to Azure"
        (Get-AzADUser -UserPrincipalName $upn).Id
        Write-Host "Press [ENTER] to continue..."
        ```

        ---

    2. Anteckna objekt-ID:t. Du behöver den i nästa avsnitt i den här snabbstarten.

## <a name="review-the-template"></a>Granska mallen

Mallen som används i den här snabbstarten kommer från [Azure-snabbstartsmallar](https://azure.microsoft.com/resources/templates/101-key-vault-create/).

:::code language="json" source="~/quickstart-templates/101-key-vault-create/azuredeploy.json":::

Två Azure-resurser definieras i mallen:

* [**Microsoft.KeyVault/vaults:**](/azure/templates/microsoft.keyvault/vaults)skapa ett Azure-nyckelvalv.
* [**Microsoft.KeyVault/vaults/secrets:**](/azure/templates/microsoft.keyvault/vaults/secrets)skapa en nyckelvalvshemlighet.

Fler Azure Key Vault finns i [Azure-snabbstartsmallar.](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault&pageNumber=1&sort=Popular)

## <a name="deploy-the-template"></a>Distribuera mallen

1. Välj följande bild för att logga in på Azure och öppna en mall. Mallen skapar ett nyckelvalv och en hemlighet.

    [![Distribuera till Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-key-vault-create%2Fazuredeploy.json)

2. Välj eller ange följande värden.

    ![ARM-mall, Key Vault,distribuera portal](../media/quick-create-template/create-key-vault-using-template-portal.png)

    Om det inte anges använder du standardvärdet för att skapa nyckelvalvet och en hemlighet.

    * **Prenumeration**: välj en Azure-prenumeration.
    * **Resursgrupp:** välj **Skapa ny,** ange ett unikt namn för resursgruppen och klicka sedan på **OK.**
    * **Plats**: välj en plats. Välj till exempel **USA, centrala**.
    * **Key Vault namn:** Ange ett namn för nyckelvalvet, som måste vara globalt unikt inom namnområdet .vault.azure.net. Du behöver namnet i nästa avsnitt när du verifierar distributionen.
    * **Klientorganisations-ID:** Mallfunktionen hämtar automatiskt ditt klientorganisations-ID. Ändra inte standardvärdet.
    * **AD-användar-ID:** Ange det Objekt-ID för Azure AD-användare som du hämtade [från Krav.](#prerequisites)
    * **Hemligt namn:** Ange ett namn på hemligheten som du lagrar i nyckelvalvet. Till exempel **adminpassword**.
    * **Hemligt värde:** ange det hemliga värdet. Om du lagrar ett lösenord rekommenderar vi att du använder det genererade lösenordet som du skapade i Krav.
    * **Jag godkänner villkoren ovan**: Välj.
3. Välj **Köp**. När nyckelvalvet har distribuerats får du ett meddelande:

    ![ARM-mall, Key Vault,distribuera portalmeddelande](../media/quick-create-template/resource-manager-template-portal-deployment-notification.png)

Azure-portalen används för att distribuera mallen. Förutom de här Azure Portal kan du även använda Azure PowerShell, Azure CLI och REST API. Mer information om andra distributionsmetoder finns i [Distribuera mallar.](../../azure-resource-manager/templates/deploy-powershell.md)

## <a name="review-deployed-resources"></a>Granska distribuerade resurser

Du kan antingen använda Azure Portal för att kontrollera nyckelvalvet och hemligheten, eller använda följande Azure CLI eller Azure PowerShell för att lista hemligheten som skapats.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your key vault name:" &&
read keyVaultName &&
az keyvault secret list --vault-name $keyVaultName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$keyVaultName = Read-Host -Prompt "Enter your key vault name"
Get-AzKeyVaultSecret -vaultName $keyVaultName
Write-Host "Press [ENTER] to continue..."
```

---

Utdata ser ut ungefär så här:

# <a name="cli"></a>[CLI](#tab/CLI)

![Skärmbild som visar utdata för att distribuera portalverifiering i CLI.](../media/quick-create-template/resource-manager-template-portal-deployment-cli-output.png)

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

![ARM-mall, Key Vault,distribuera portalverifieringsutdata](../media/quick-create-template/resource-manager-template-portal-deployment-powershell-output.png)

---

## <a name="clean-up-resources"></a>Rensa resurser

Andra snabbstarter och självstudier om Key Vault bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre så tar du bort den. Då tas även nyckelvalvet och relaterade resurser bort. Så här tar du bort resursgruppen med hjälp av Azure CLI eller Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat ett nyckelvalv och en hemlighet med hjälp av en ARM-mall och verifierat distributionen. Mer information om Key Vault och Azure Resource Manager finns i artiklarna nedan.

- Läs en [översikt över Azure Key Vault](../general/overview.md)
- Läs mer om [Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- Granska [Key Vault säkerhetsöversikten](../general/security-features.md)
