---
title: Använd en systemtilldelad hanterad identitet för att få åtkomst till Azure Key Vault
description: Lär dig hur du skapar en hanterad identitet för App Service program och hur du använder den för att få åtkomst till Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 0aa9a9728c3a59b6f47ef1427cbcd368d4f1e77c
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586179"
---
# <a name="provide-key-vault-authentication-with-a-managed-identity"></a>Tillhandahåll Key Vault autentisering med en hanterad identitet

Med en hanterad identitet från Azure Active Directory kan din app enkelt komma åt andra Azure AD-skyddade resurser. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information finns i [hanterade identiteter för Azure-resurser](../../active-directory/managed-identities-azure-resources/overview.md). 

Den här artikeln visar hur du skapar en hanterad identitet för ett App Service program och använder den för att få åtkomst till Azure Key Vault. Information om program som är värdar för virtuella Azure-datorer finns i [använda en Windows VM-systemtilldelad hanterad identitet för åtkomst till Azure Key Vault](../../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-nonaad.md).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Förutsättningar 

För att slutföra den här guiden måste du ha följande resurser. 

- Ett nyckel valv. Du kan använda ett befintligt nyckel valv eller skapa ett nytt genom att följa stegen i någon av följande snabb starter:
   - [Skapa ett nyckel valv med Azure CLI](../secrets/quick-create-cli.md)
   - [Skapa ett nyckel valv med Azure PowerShell](../secrets/quick-create-powershell.md)
   - [Skapa ett nyckel valv med Azure Portal](../secrets/quick-create-portal.md).
- Ett befintligt App Service program som du vill bevilja åtkomst till nyckel valvet. Du kan snabbt skapa en genom att följa stegen i [App Service-dokumentationen](../../app-service/overview.md).
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) eller [Azure PowerShell](/powershell/azure/). Du kan också använda [Azure Portal](https://portal.azure.com).


## <a name="adding-a-system-assigned-identity"></a>Lägga till en tilldelad identitet 

Först måste du lägga till en tilldelad identitet till ett program. 
 
### <a name="azure-portal"></a>Azure Portal 

För att konfigurera en hanterad identitet i portalen skapar du först ett program som vanligt och aktiverar sedan funktionen. 

1. Om du använder en Function-app navigerar du till **plattforms funktioner**. För andra typer av appar rullar du ned till **inställnings** gruppen i det vänstra navigerings fältet. 

1. Välj **hanterad identitet**. 

1. Växla **status** till **på på**fliken **systemtilldelad** . Klicka på **Spara**. 

   ![Skärm bild som visar hur du sparar en tilldelad identitet.](../media/managed-identity-system-assigned.png)

### <a name="azure-cli"></a>Azure CLI

Den här snabb starten kräver Azure CLI-version 2.0.4 eller senare. Kör `az --version` för att hitta din nuvarande version. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest). 

Om du vill logga in med Azure CLI använder du kommandot [AZ login](/cli/azure/reference-index?view=azure-cli-latest#az-login) :

```azurecli-interactive
az login
```

Mer information om inloggnings alternativ med Azure CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest). 

Om du vill skapa identiteten för det här programmet använder du kommandot Azure CLI [AZ webapp Identity Assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) eller [AZ functionapp Identity Assign](/cli/azure/functionapp/identity?view=azure-cli-latest#az-functionapp-identity-assign) :


```azurecli-interactive
az webapp identity assign --name myApp --resource-group myResourceGroup
```

```azurecli-interactive
az functionapp identity assign --name myApp --resource-group myResourceGroup
```

Anteckna `PrincipalId` , som kommer att behövas i nästa avsnitt.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```
## <a name="grant-your-app-access-to-key-vault"></a>Ge appen åtkomst till Key Vault 

### <a name="azure-portal"></a>Azure Portal

1.  Navigera till Key Vault resurs. 

1.  Välj **åtkomst principer** och klicka på **Lägg till åtkomst princip**. 

1.  I **hemliga behörigheter**väljer du **Hämta, lista**. 

1.  Välj **Välj huvud konto**och ange namnet på appen i fältet Sök.  Välj appen i resultat listan och klicka på **Välj**. 

1.  Klicka på **Lägg till** för att slutföra tillägget av den nya åtkomst principen.

    ![Skärm bild som visar hur du lägger till en ny åtkomst princip i Azure Portal.](../media/managed-identity-access-policy.png)

### <a name="azure-cli"></a>Azure CLI

Om du vill ge ditt program åtkomst till ditt nyckel valv använder du kommandot för att ange princip för Azure CLI [-AZ](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) och anger **ObjectID** -parametern med **principalId** som du noterade ovan.

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <PrincipalId> --secret-permissions get list 
```

## <a name="next-steps"></a>Nästa steg

- [Azure Key Vault säkerhet: identitets-och åtkomst hantering](overview-security.md#identity-and-access-management)
- [Tillhandahålla Key Vault autentisering med en princip för åtkomst kontroll](group-permissions-for-apps.md)
- [Skydda ditt nyckel valv](secure-your-key-vault.md)).
- [Guide för Azure Key Vault utvecklare](developers-guide.md)
- Granska [Azure Key Vault bästa praxis](best-practices.md)