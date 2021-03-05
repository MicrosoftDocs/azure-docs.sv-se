---
title: Konfigurera Azure-attestering med Azure CLI
description: Konfigurera och konfigurera en attesterings leverantör med Azure CLI.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102178719"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Snabb start: Konfigurera Azure-attestering med Azure CLI

Kom igång med [Azure-attestering med hjälp av Azure CLI](/cli/azure/ext/attestation/attestation).

## <a name="prerequisites"></a>Krav

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="get-started"></a>Kom igång

1. Installera det här tillägget med hjälp av följande CLI-kommando

   ```azurecli
   az extension add --name attestation
   ```
   
1. Kontrol lera versionen

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Använd följande kommando för att logga in på Azure:

   ```azurecli
   az login
   ```

1. Om det behövs växlar du till prenumerationen för Azure-attestering:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Registrera resurs leverantören Microsoft. attestering i prenumerationen med [AZ Provider register](/cli/azure/provider#az_provider_register) kommando:

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Mer information om Azure Resource providers och hur du konfigurerar och hanterar dem finns i [Azure Resource providers och-typer](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Du behöver bara registrera en resurs leverantör en gång för en prenumeration.

1. Skapa en resurs grupp för attesterings leverantören. Du kan lägga till andra Azure-resurser i samma resurs grupp, inklusive en virtuell dator med en klient program instans. Kör kommandot [AZ Group Create](/cli/azure/group#az_group_create) för att skapa en resurs grupp eller Använd en befintlig resurs grupp:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Skapa och hantera en attesterings leverantör

Här är kommandon som du kan använda för att skapa och hantera attesterings-providern:

1. Kör kommandot [AZ attestering Create](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) för att skapa en attesterings leverantör utan krav på princip signering:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Kör kommandot [AZ attestering show](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) för att hämta egenskaper för attesterings leverantör, till exempel status och AttestURI:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Det här kommandot visar värden som följande utdata:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

Du kan ta bort en attesterings leverantör med kommandot [AZ attestering](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) :

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Principhantering

Använd de kommandon som beskrivs här för att tillhandahålla princip hantering för en attesterings leverantör, en attesterings typ i taget.

Kommandot [AZ attestering policy show](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) returnerar den aktuella principen för den angivna tee:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> Kommandot visar principen i både text-och JWT-format.

Följande typer av TEE stöds:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Använd kommandot [AZ attestering policy set](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) för att ange en ny princip för den angivna attesterings typen.

Ange en princip i text format för en viss typ av attestering med hjälp av fil Sök väg:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Ange en princip i JWT-format för en viss typ av attestering med hjälp av fil Sök väg:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Nästa steg

- [Så här skapar och signerar du en policy för attestering](author-sign-policy.md)
- [Implementera attestering med en SGX-enklaven med hjälp av kod exempel](/samples/browse/?expanded=azure&terms=attestation)
