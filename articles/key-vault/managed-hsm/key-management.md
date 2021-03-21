---
title: Hantera nycklar i en hanterad HSM-Azure Key Vault | Microsoft Docs
description: Använd den här artikeln för att hantera nycklar i en hanterad HSM
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8d0cbd35b53bc8460ac8a19e5197d1f560657263
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212050"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Hantera en hanterad HSM med Azure CLI

> [!NOTE]
> Key Vault stöder två typer av resurser: valv och hanterade HSM: er. Den här artikeln är en **hanterad HSM**. Om du vill lära dig hur du hanterar ett valv går du [till hantera Key Vault med hjälp av Azure CLI](../general/manage-with-cli2.md).

En översikt över hanterad HSM finns i [Vad är hanterad HSM?](overview.md)

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen i den här artikeln, måste du ha följande objekt:

* En prenumeration på Microsoft Azure. Om du inte har ett konto kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI-versionen 2.12.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI]( /cli/azure/install-azure-cli).
* En hanterad HSM i din prenumeration. Se [snabb start: etablera och aktivera en hanterad HSM med Azure CLI](quick-create-cli.md) för att etablera och aktivera en hanterad HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Du kan logga in i Azure via CLI genom att skriva:

```azurecli
az login
```

Mer information om inloggnings alternativ via CLI finns i [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli)

> [!NOTE]
> I alla kommandon nedan visas två användnings metoder. Ett med parametrarna **--HSM-Name** och **--Name** (för nyckel namn) och en annan med parametern **--ID** där du kan ange hel URL, inklusive nyckel namn där det är lämpligt. Den sistnämnda metoden är användbar när anroparen (en användare eller ett program) inte har Läs behörighet på kontroll planet och endast begränsad åtkomst till data planet.

## <a name="create-an-hsm-key"></a>Skapa en HSM-nyckel

Använd `az keyvault key create` kommandot för att skapa en nyckel.

### <a name="create-an-rsa-key"></a>Skapa en RSA-nyckel

Exemplet nedan visar hur du skapar en 3072-bitars **RSA** -nyckel som endast ska användas för **wrapKey, unwrapKey** -åtgärder (--OPS). 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Observera att `get` åtgärden endast returnerar attributen för offentlig nyckel och nyckel. Den returnerar inte den privata nyckeln (i händelse av asymmetrisk nyckel eller nyckel material (i händelse av symmetrisk nyckel).

### <a name="create-an-ec-key"></a>Skapa en EG-nyckel

Exemplet nedan visar hur du skapar en **EG** -nyckel med P-256-kurva som endast ska användas för **signering och verifiering** av åtgärder (--OPS) och har två taggar, **användning** och **APPNAME**. Med taggar kan du lägga till ytterligare metadata till nyckeln för spårning och hantering.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Skapa en 256-bitars symmetrisk nyckel

Exemplet nedan visar hur du skapar en 256-bitars **symmetrisk** nyckel som endast ska användas för att **kryptera och dekryptera** åtgärder (--OPS).

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Visa nyckelattribut och Taggar

Använd `az keyvault key show` kommandot för att visa attribut, versioner och taggar för en nyckel.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Lista nycklar

Använd `az keyvault key list` kommandot för att lista alla nycklar inuti en hanterad HSM.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Ta bort en nyckel

Använd `az keyvault key delete` kommandot för att ta bort en nyckel från en hanterad HSM. Observera att mjuk borttagning alltid är aktiverat. Därför kommer en borttagen nyckel att bevaras i Borttaget läge och kan återställas tills antalet lagrings dagar har passerat när nyckeln rensas (tas bort permanent) utan att det går att återställa.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Lista borttagna nycklar

Använd `az keyvault key list-deleted` kommandot för att lista alla nycklar i Borttaget läge i din hanterade HSM.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Återställa (ångra borttagning) en borttagen nyckel

Använd `az keyvault key list-deleted` kommandot för att lista alla nycklar i Borttaget läge i din hanterade HSM. Om du behöver återställa (ångra borttagning) en nyckel med parametern--ID när du återställer en borttagen nyckel måste du anteckna `recoveryId` värdet för den borttagna nyckeln som hämtades från `az keyvault key list-deleted` kommandot.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Rensa (ta bort permanent) en nyckel

Använd `az keyvault key purge` kommandot för att rensa (ta bort permanent) en nyckel.

> [!NOTE]
> Om den hanterade HSM: en har rensnings skyddet aktive rad, är rensa-åtgärden inte tillåten. Nyckeln rensas automatiskt när kvarhållningsperioden har passerat.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Skapa en säkerhets kopia av en enskild nyckel

Använd `az keyvault key backup` för att skapa en nyckel säkerhets kopia. Säkerhets kopierings filen är en krypterad blob som är kryptografiskt bunden till källans säkerhets domän. Den kan bara återställas i HSM: er som delar samma säkerhets domän. Läs mer om [säkerhets domän](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Återställa en enskild nyckel från en säkerhets kopia

Används `az keyvault key restore` för att återställa en enskild nyckel. Den HSM-källa där säkerhets kopian skapades måste dela samma säkerhets domän som den mål-HSM där nyckeln återställs.

> [!NOTE]
> Återställningen Miss lyckas om det finns en nyckel med samma namn i aktivt eller Borttaget tillstånd.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Importera en nyckel från en fil

Använd `az keyvault key import` kommandot för att importera en nyckel (endast RSA och EG) från en fil. Certifikat filen måste ha en privat nyckel och måste använda PEM encoding (enligt definitionen i RFC [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423), [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Om du vill importera en nyckel från din lokala HSM till hanterad HSM, se [Importera HSM-skyddade nycklar till Managed HSM (BYOK)](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>Nästa steg

- En fullständig Azure CLI-referens för Key Vault-kommandon finns i [Key Vault CLI-referens](/cli/azure/keyvault).
- Programmerings referenser finns i [Azure Key Vault Developer ' s guide](../general/developers-guide.md)
- Läs mer om [hanterad HSM-roll hantering](role-management.md)
- Lär dig mer om [metod tips för hanterad HSM](best-practices.md)
