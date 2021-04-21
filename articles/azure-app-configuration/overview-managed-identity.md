---
title: Konfigurera hanterade identiteter med Azure App Configuration
description: Lär dig hur hanterade identiteter fungerar i Azure App Configuration och hur du konfigurerar en hanterad identitet
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e4fdff2515dde941b2e9037a21ad931ac27b6fef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764233"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Använda hanterade identiteter för Azure App Configuration

Det här avsnittet visar hur du skapar en hanterad identitet för Azure App Configuration. Med en hanterad Azure Active Directory (AAD) kan Azure App Configuration enkelt komma åt andra AAD-skyddade resurser, till exempel Azure Key Vault. Identiteten hanteras av Azure-plattformen. Du behöver inte etablera eller rotera några hemligheter. Mer information om hanterade identiteter i AAD finns i [Hanterade identiteter för Azure-resurser.](../active-directory/managed-identities-azure-resources/overview.md)

Programmet kan beviljas två typer av identiteter:

- En **system tilldelad identitet är** kopplad till ditt konfigurationsarkiv. Den tas bort om konfigurationsarkivet tas bort. Ett konfigurationsarkiv kan bara ha en system tilldelad identitet.
- En **användar tilldelad identitet** är en fristående Azure-resurs som kan tilldelas till ditt konfigurationsarkiv. Ett konfigurationsarkiv kan ha flera användar tilldelade identiteter.

## <a name="adding-a-system-assigned-identity"></a>Lägga till en system tilldelad identitet

Om du App Configuration ett arkiv med en system tilldelad identitet måste ytterligare en egenskap anges i arkivet.

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med hjälp av Azure CLI använder du [kommandot az appconfig identity assign] mot ett befintligt konfigurationsarkiv. Du har tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure Portal.
- Använd den inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) (2.1 eller senare) om du föredrar att använda en lokal CLI-konsol.

Följande steg hjälper dig att skapa en App Configuration och tilldela den en identitet med hjälp av CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login]. Använd ett konto som är associerat med din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

1. Skapa ett App Configuration store med hjälp av CLI. Fler exempel på hur du använder CLI med Azure App Configuration finns i [App Configuration CLI-exempel:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Kör kommandot [az appconfig identity assign för] att skapa den system tilldelade identiteten för det här konfigurationsarkivet:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Lägga till en användar tilldelad identitet

När du App Configuration ett arkiv med en användar tilldelad identitet måste du skapa identiteten och sedan tilldela dess resurs-ID till ditt arkiv.

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med hjälp av Azure CLI använder du kommandot [az appconfig identity assign] mot ett befintligt konfigurationsarkiv. Du har tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure Portal.
- Använd den inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda en lokal CLI-konsol.

Följande steg hjälper dig att skapa en användar tilldelad identitet och ett App Configuration store och sedan tilldela identiteten till arkivet med hjälp av CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login]. Använd ett konto som är associerat med din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

1. Skapa ett App Configuration store med hjälp av CLI. Fler exempel på hur du använder CLI med Azure App Configuration finns i [App Configuration CLI-exempel:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Skapa en användar tilldelad identitet med namnet `myUserAssignedIdentity` med hjälp av CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Observera värdet för egenskapen i kommandots `id` utdata.

1. Kör kommandot [az appconfig identity assign för] att tilldela den nya användar-tilldelade identiteten till det här konfigurationsarkivet. Använd värdet för egenskapen `id` som du antecknade i föregående steg.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Ta bort en identitet

Du kan ta bort en system tilldelad identitet genom att inaktivera funktionen med kommandot [az appconfig identity remove](/cli/azure/appconfig/identity#az_appconfig_identity_remove) i Azure CLI. Användar tilldelade identiteter kan tas bort individuellt. Om du tar bort en system tilldelad identitet på det här sättet tas den även bort från AAD. Systemtilldelningar tas också bort automatiskt från AAD när appresursen tas bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-app med Azure App Configuration](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity#az_appconfig_identity_assign
[az login]: /cli/azure/reference-index#az_login
