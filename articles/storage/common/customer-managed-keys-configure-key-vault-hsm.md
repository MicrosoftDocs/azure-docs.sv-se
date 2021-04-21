---
title: Konfigurera kryptering med kund hanterade nycklar som lagras i Azure Key Vault Managed HSM (förhandsversion)
titleSuffix: Azure Storage
description: Lär dig hur du Azure Storage kryptering med kund-hanterade nycklar som lagras i Azure Key Vault Managed HSM (förhandsversion) med hjälp av Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9b40c934cb428a31a3feb77195518d5351818d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785369"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Konfigurera kryptering med kund hanterade nycklar som lagras i Azure Key Vault Managed HSM (förhandsversion)

Azure Storage krypterar alla data i ett vilokonto för lagring. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypteringsnycklar kan du hantera dina egna nycklar. Kund hanterade nycklar måste lagras i Azure Key Vault eller Key Vault Managed Hardware Security Model (HSM) (förhandsversion). En Azure Key Vault Managed HSM är en FIPS 140-2 Level 3-verifierad HSM.

Den här artikeln visar hur du konfigurerar kryptering med kund hanterade nycklar som lagras i en hanterad HSM med hjälp av Azure CLI. Information om hur du konfigurerar kryptering med kund hanterade nycklar som lagras i ett nyckelvalv finns i Konfigurera kryptering med kund hanterade nycklar som [lagras i Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> Kryptering med kund hanterade nycklar som lagras i Azure Key Vault Managed HSM finns för närvarande i **FÖRHANDSVERSION.** Se kompletterande [användningsvillkor för förhandsversioner Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) för juridiska villkor som gäller för Azure-funktioner som är i betaversion, förhandsversion eller som inte har släppts allmänt ännu.
>
> Azure Key Vault och Azure Key Vault Managed HSM stöder samma API:er och hanteringsgränssnitt för konfiguration.

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela en identitet till lagringskontot

Först tilldelar du en system tilldelad hanterad identitet till lagringskontot. Du använder den här hanterade identiteten för att ge lagringskontot behörighet att komma åt den hanterade HSM:en. Mer information om system tilldelade hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).

Om du vill tilldela en hanterad identitet med hjälp av Azure CLI [anropar du az storage account update](/cli/azure/storage/account#az_storage_account_update). Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Tilldela en roll till lagringskontot för åtkomst till den hanterade HSM:en

Tilldela sedan **krypteringsrollen för den** hanterade HSM-krypteringstjänsten till lagringskontots hanterade identitet så att lagringskontot har behörighet till den hanterade HSM:en. Microsoft rekommenderar att du omfångsomfånget för rolltilldelningen till nivån för den enskilda nyckeln för att ge så få behörigheter som möjligt till den hanterade identiteten.

Om du vill skapa rolltilldelningen för lagringskontot [anropar du az key vault role assignment create](/cli/azure/role/assignment#az_role_assignment_create). Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Konfigurera kryptering med en nyckel i den hanterade HSM:en

Slutligen konfigurerar du Azure Storage med kund hanterade nycklar för att använda en nyckel som lagras i den hanterade HSM:en. Nyckeltyper som stöds är RSA-HSM-nycklar av storlekarna 2048, 3072 och 4096. Information om hur du skapar en nyckel i en hanterad HSM finns i [Skapa en HSM-nyckel.](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key)

Installera Azure CLI 2.12.0 eller senare för att konfigurera kryptering för att använda en kund hanterad nyckel i en hanterad HSM. Mer information finns i [Installera Azure CLI.](/cli/azure/install-azure-cli)

Om du vill uppdatera nyckelversionen för en kund hanterad nyckel automatiskt utelämnar du nyckelversionen när du konfigurerar kryptering med kund hanterade nycklar för lagringskontot. Anropa [az storage account update](/cli/azure/storage/account#az_storage_account_update) för att uppdatera lagringskontots krypteringsinställningar, som du ser i följande exempel. Inkludera och `--encryption-key-source parameter` ställ in den på för att aktivera kund `Microsoft.Keyvault` hanterade nycklar för kontot. Kom ihåg att ersätta platshållarvärdena inom hakparentes med dina egna värden.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Om du vill uppdatera versionen för en kund hanterad nyckel manuellt inkluderar du nyckelversionen när du konfigurerar kryptering för lagringskontot:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

När du uppdaterar nyckelversionen manuellt måste du uppdatera lagringskontots krypteringsinställningar för att använda den nya versionen. Fråga först efter nyckelvalvs-URI:en genom att anropa [az keyvault show](/cli/azure/keyvault#az_keyvault_show)och för nyckelversionen genom att anropa [az keyvault key list-versions](/cli/azure/keyvault/key#az_keyvault_key_list_versions). Anropa sedan [az storage account update](/cli/azure/storage/account#az_storage_account_update) för att uppdatera lagringskontots krypteringsinställningar för att använda den nya versionen av nyckeln, som du ser i föregående exempel.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kund-hanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
