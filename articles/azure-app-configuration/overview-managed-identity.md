---
title: Konfigurera hanterade identiteter med Azure App konfiguration
description: Lär dig hur hanterade identiteter fungerar i Azure App konfiguration och hur du konfigurerar en hanterad identitet
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: 5424b776b977d8a6939bbb28c5d4c9c428cac444
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179620"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Använda hanterade identiteter för Azure App konfiguration

Det här avsnittet visar hur du skapar en hanterad identitet för Azure App konfiguration. Med en hanterad identitet från Azure Active Directory (AAD) kan Azure App konfiguration enkelt komma åt andra AAD-skyddade resurser, till exempel Azure Key Vault. Identiteten hanteras av Azure-plattformen. Du behöver inte etablera eller rotera några hemligheter. Mer information om hanterade identiteter i AAD finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

Ditt program kan beviljas två typer av identiteter:

- En **systemtilldelad identitet** är kopplad till ditt konfigurations arkiv. Den tas bort om konfigurations lagret tas bort. Ett konfigurations lager kan bara ha en tilldelad identitet.
- En **användardefinierad identitet** är en fristående Azure-resurs som kan tilldelas ditt konfigurations arkiv. Ett konfigurations lager kan ha flera användarspecifika identiteter.

## <a name="adding-a-system-assigned-identity"></a>Lägga till en tilldelad identitet

Att skapa ett konfigurations lager för appar med en tilldelad identitet kräver att ytterligare en egenskap anges i arkivet.

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med Azure CLI använder du kommandot [AZ appconfig Identity Assign] i ett befintligt konfigurations lager. Det finns tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure Portal.
- Använd den inbäddade Azure Cloud Shell via knappen "testa", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) (2,1 eller senare) om du föredrar att använda en lokal CLI-konsol.

Följande steg vägleder dig genom att skapa ett konfigurations lager för appar och tilldela det en identitet med hjälp av CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login]. Använd ett konto som är associerat med din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

1. Skapa ett konfigurations lager för appar med hjälp av CLI. Fler exempel på hur du använder CLI med Azure App konfiguration finns i CLI- [exempel för app-konfiguration](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Kör kommandot [AZ appconfig Identity Assign] för att skapa den systemtilldelade identiteten för det här konfigurations arkivet:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Lägga till en användardefinierad identitet

Om du skapar ett konfigurations lager för appar med en tilldelad identitet måste du skapa identiteten och sedan tilldela resurs-ID: n till butiken.

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med Azure CLI använder du kommandot [AZ appconfig Identity Assign] i ett befintligt konfigurations lager. Det finns tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure Portal.
- Använd den inbäddade Azure Cloud Shell via knappen "testa", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda en lokal CLI-konsol.

Följande steg vägleder dig genom att skapa en användardefinierad identitet och ett konfigurations lager för appar och sedan tilldela identiteten till butiken med CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login]. Använd ett konto som är associerat med din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

1. Skapa ett konfigurations lager för appar med hjälp av CLI. Fler exempel på hur du använder CLI med Azure App konfiguration finns i CLI- [exempel för app-konfiguration](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Skapa en användardefinierad identitet som kallas `myUserAssignedIdentity` med cli.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Observera egenskapens värde i kommandots utdata `id` .

1. Kör kommandot [AZ appconfig Identity Assign] för att tilldela den nya användar tilldelnings identiteten till det här konfigurations arkivet. Använd värdet för den `id` egenskap som du antecknade i föregående steg.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Ta bort en identitet

En systemtilldelad identitet kan tas bort genom att inaktivera funktionen med hjälp av kommandot [AZ appconfig Identity Remove](/cli/azure/appconfig/identity#az-appconfig-identity-remove) i Azure CLI. Användare-tilldelade identiteter kan tas bort individuellt. Om du tar bort en tilldelad identitet på det här sättet tas även den bort från AAD. Systemtilldelade identiteter tas också bort automatiskt från AAD när app-resursen tas bort.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en ASP.NET Core-app med Azure App Configuration](quickstart-aspnet-core-app.md)

[AZ appconfig-identitet tilldela]: /cli/azure/appconfig/identity#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
