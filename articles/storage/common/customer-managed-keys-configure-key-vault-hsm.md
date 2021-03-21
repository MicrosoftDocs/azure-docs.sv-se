---
title: Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (förhands granskning)
titleSuffix: Azure Storage
description: Lär dig hur du konfigurerar Azure Storage kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (för hands version) med hjälp av Azure CLI.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9be9272a898ad48f3553d4c5e48952e1fcdde81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218646"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM (förhands granskning)

Azure Storage krypterar alla data i ett lagrings konto i vila. Som standard krypteras data med Microsoft-hanterade nycklar. Om du vill ha ytterligare kontroll över krypterings nycklarna kan du hantera dina egna nycklar. Kundhanterade nycklar måste lagras i Azure Key Vault eller Key Vault-hanterad maskin varu säkerhets modell (HSM) (för hands version). En Azure Key Vault hanterad HSM är ett FIPS 140-2-nivå 3-verifierad HSM.

Den här artikeln visar hur du konfigurerar kryptering med Kundhanterade nycklar som lagras i en hanterad HSM med hjälp av Azure CLI. Information om hur du konfigurerar kryptering med Kundhanterade nycklar som lagras i ett nyckel valv finns i [Konfigurera kryptering med Kundhanterade nycklar som lagras i Azure Key Vault](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> Kryptering med Kundhanterade nycklar som lagras i Azure Key Vault hanterad HSM är för närvarande en för **hands version**. Se [kompletterande användnings villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) versioner av juridiska villkor som gäller för Azure-funktioner som är i beta, för hands version eller på annat sätt ännu inte släpps till allmän tillgänglighet.
>
> Azure Key Vault och Azure Key Vault Managed HSM stöder samma API: er och hanterings gränssnitt för konfiguration.

## <a name="assign-an-identity-to-the-storage-account"></a>Tilldela lagrings kontot en identitet

Tilldela först en systemtilldelad hanterad identitet till lagrings kontot. Du använder den här hanterade identiteten för att ge lagrings kontots behörigheter åtkomst till hanterad HSM. Mer information om systemtilldelade hanterade identiteter finns i [Vad är hanterade identiteter för Azure-resurser?](../../active-directory/managed-identities-azure-resources/overview.md).

Om du vill tilldela en hanterad identitet med Azure CLI anropar du [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Tilldela en roll till lagrings kontot för åtkomst till den hanterade HSM

Tilldela sedan **krypterings rollen hanterad HSM-krypto** till lagrings kontots hanterade identitet så att lagrings kontot har behörighet till den hanterade HSM. Microsoft rekommenderar att du omfångerar roll tilldelningen till nivån för den enskilda nyckeln för att ge minsta möjliga behörighet till den hanterade identiteten.

Om du vill skapa roll tilldelningen för lagrings kontot anropar du [AZ Key Vault roll tilldelning skapa](/cli/azure/role/assignment#az_role_assignment_create). Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.
  
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

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Konfigurera kryptering med en nyckel i den hanterade HSM

Konfigurera slutligen Azure Storage kryptering med Kundhanterade nycklar för att använda en nyckel som lagras i den hanterade HSM. Nyckel typer som stöds är RSA-HSM-nycklar av storlekarna 2048, 3072 och 4096. Installera Azure CLI-2.12.0 eller senare för att konfigurera kryptering för att använda en kundhanterad nyckel i en hanterad HSM. Mer information finns i [Installera Azure CLI](/cli/azure/install-azure-cli).

Om du vill uppdatera nyckel versionen för en kundhanterad nyckel automatiskt, utelämnar du nyckel versionen när du konfigurerar kryptering med Kundhanterade nycklar för lagrings kontot. Anropa [AZ Storage Account Update](/cli/azure/storage/account#az_storage_account_update) för att uppdatera lagrings kontots krypterings inställningar, som du ser i följande exempel. Inkludera `--encryption-key-source parameter` och Ställ in det på `Microsoft.Keyvault` för att aktivera Kundhanterade nycklar för kontot. Kom ihåg att ersätta plats hållarnas värden inom hakparenteser med dina egna värden.

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

Om du vill uppdatera versionen för en kundhanterad nyckel manuellt inkluderar du nyckel versionen när du konfigurerar kryptering för lagrings kontot:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

När du uppdaterar nyckel versionen manuellt måste du uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen. Börja med att fråga efter Key Vault-URI: n genom att anropa AZ-nyckelpar [show](/cli/azure/keyvault#az-keyvault-show)och för nyckel versionen genom att anropa [AZ Key Vault Key List-versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions). Anropa sedan [AZ Storage Account Update](/cli/azure/storage/account#az-storage-account-update) för att uppdatera lagrings kontots krypterings inställningar för att använda den nya versionen av nyckeln, som du ser i föregående exempel.

## <a name="next-steps"></a>Nästa steg

- [Azure Storage-kryptering av vilande data](storage-service-encryption.md)
- [Kundhanterade nycklar för Azure Storage kryptering](customer-managed-keys-overview.md)
