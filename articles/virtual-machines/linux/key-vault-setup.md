---
title: Konfigurera Azure Key Vault med CLI
description: Så här konfigurerar du Key Vault för virtuell dator med hjälp av Azure CLI.
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/24/2017
ms.author: mimckitt
ms.custom: devx-track-azurecli
ms.openlocfilehash: 84a88c9c563141d6e6e9ba1584d577addcade8fe
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565231"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Konfigurera Key Vault för virtuella datorer med Azure CLI

I Azure Resource Manager stack modelleras hemligheter/certifikat som resurser som tillhandahålls av Key Vault. Mer information om Azure Key Vault finns i [Vad är Azure Key Vault?](../../key-vault/general/overview.md) För att Key Vault ska kunna användas med Azure Resource Manager virtuella datorer måste egenskapen *EnabledForDeployment* på Key Vault vara inställd på True. Den här artikeln visar hur du konfigurerar Key Vault för användning med Azure Virtual Machines (VM) med hjälp av Azure CLI. 

För att utföra de här stegen måste du ha den senaste versionen av [Azure CLI](/cli/azure/install-az-cli2) installerat och inloggad på ett Azure-konto med [AZ-inloggning](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Skapa en Key Vault-lösning
Skapa ett nyckel valv och tilldela distributions principen med [AZ-valv skapa](/cli/azure/keyvault). I följande exempel skapas ett nyckel valv med namnet `myKeyVault` i `myResourceGroup` resurs gruppen:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Uppdatera en Key Vault för användning med virtuella datorer
Ange distributions principen för ett befintligt nyckel valv med [AZ-uppdatering](/cli/azure/keyvault)av nyckel valv. Följande uppdaterar nyckel valvet som heter `myKeyVault` i `myResourceGroup` resurs gruppen:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Använd mallar för att konfigurera Key Vault
När du använder en mall måste du ange `enabledForDeployment` egenskapen till `true` för Key Vault resursen enligt följande:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Nästa steg
För andra alternativ som du kan konfigurera när du skapar en Key Vault med hjälp av mallar, se [skapa ett nyckel valv](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
