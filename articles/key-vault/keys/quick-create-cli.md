---
title: Skapa och hämta attribut för en nyckel i Azure Key Vault – Azure CLI
description: Snabb start visar hur du ställer in och hämtar en nyckel från Azure Key Vault med Azure CLI
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8da5e86362f41322102c3b5316df5743e0c2458f
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97977002"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-cli"></a>Snabb start: Ange och hämta en nyckel från Azure Key Vault med Azure CLI

I den här snabb starten skapar du ett nyckel valv i Azure Key Vault med Azure CLI. Azure Key Vault är en molntjänst som fungerar som ett säkert lager för hemligheter. Du kan på ett säkert sätt lagra nycklar, lösenord, certifikat och andra hemligheter. Mer information om Key Vault kan du läsa [översikten](../general/overview.md). Med Azure-CLI:t kan du skapa och hantera Azure-resurser med hjälp av kommandon eller skript. När du har slutfört det kommer du att lagra en nyckel.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Den här snabb starten kräver version 2.0.4 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resursgrupp med namnet *ContosoResourceGroup* på platsen *eastus*.

```azurecli
az group create --name "ContosoResourceGroup" --location eastus
```

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning

Därefter skapar du ett nyckelvalv i resursgruppen du skapade i föregående steg. Du måste ange en del information:

- I den här snabbstarten använder vi **Contoso-vault2**. Du måste ange ett unikt namn när du testar funktionen.
- Resursgruppnamnet **ContosoResourceGroup**.
- Platsen är **USA, östra**.

```azurecli
az keyvault create --name "Contoso-Vault2" --resource-group "ContosoResourceGroup" --location eastus
```

Utdata från denna cmdlet visar egenskaper för nyckelvalvet du precis skapade. Anteckna de två egenskaperna som visas nedan:

- **Valvnamn**: I det här exemplet är namnet **Contoso-vault2**. Du kommer att använda det här namnet i andra Key Vault-kommandon.
- **Valvets URI**: I det här exemplet är det https://contoso-vault2.vault.azure.net/. Program som använder ditt valv via dess REST-API måste använda denna URI.

Nu är ditt Azure-konto det enda kontot med behörighet att utföra åtgärder i det nya valvet.

## <a name="add-a-key-to-key-vault"></a>Lägg till en nyckel i Key Vault

Om du vill lägga till en nyckel i valvet behöver du bara göra några ytterligare steg. Den här nyckeln kan användas av ett program. 

Skriv följande kommandon för att skapa en nyckel med namnet **ExampleKey** :

```azurecli
az keyvault key create --vault-name "Contoso-Vault2" -n ExampleKey --protection software
```

Nu kan du referera till den här nyckeln som du lade till Azure Key Vault med hjälp av dess URI. Använd **för https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** att hämta den aktuella versionen. 

Så här visar du tidigare lagrad nyckel:

```azurecli

az keyvault key show --name "ExampleKey" --vault-name "Contoso-Vault2"
```

Nu har du skapat en Key Vault, lagrat en nyckel och hämtat den.

## <a name="clean-up-resources"></a>Rensa resurser

De andra snabbstarterna och självstudierna i den här samlingen bygger på den här snabbstarten. Om du planerar att fortsätta med efterföljande snabbstarter och självstudier kan du lämna kvar de här resurserna.
När du inte behöver resursgruppen längre kan du använda kommandot [az group delete](/cli/azure/group) till att ta bort resursgruppen och alla relaterade resurser. Så här tar du bort resurserna:

```azurecli
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en Key Vault och sparade en nyckel i den. Om du vill veta mer om Key Vault och hur du integrerar den med dina program, Fortsätt till artiklarna nedan.

- Läs en [Översikt över Azure Key Vault](../general/overview.md)
- Se referensen för [kommandon för Azure CLI-AZ](/cli/azure/keyvault?view=azure-cli-latest)
- Granska [Key Vault säkerhets översikt](../general/security-overview.md)
