---
title: Välj hur du vill tillåta åtkomst till blobdata med Azure CLI
titleSuffix: Azure Storage
description: Ange hur du ska auktorisera dataåtgärder mot blobdata med Azure CLI. Du kan auktorisera dataåtgärder med azure AD-autentiseringsuppgifter, med åtkomstnyckeln för kontot eller med en SAS-token (signatur för delad åtkomst).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 53ad6dd922c1ccebd79aebcd2966b23b38de00e7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776905"
---
# <a name="choose-how-to-authorize-access-to-blob-data-with-azure-cli"></a>Välj hur du vill tillåta åtkomst till blobdata med Azure CLI

Azure Storage tillhandahåller tillägg för Azure CLI som gör att du kan ange hur du vill auktorisera åtgärder för blobdata. Du kan auktorisera dataåtgärder på följande sätt:

- Med ett Azure Active Directory (Azure AD) säkerhetsobjekt. Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter för att få överlägsen säkerhet och enkel användning.
- Med kontoåtkomstnyckeln eller en SAS-token (signatur för delad åtkomst).

## <a name="specify-how-data-operations-are-authorized"></a>Ange hur dataåtgärder auktoriserats

Azure CLI-kommandon för att läsa och skriva blobdata innehåller den valfria `--auth-mode` parametern . Ange den här parametern för att ange hur en dataåtgärd ska auktoreras:

- Ange `--auth-mode` parametern till `login` för att logga in med ett Azure AD-säkerhetsobjekt (rekommenderas).
- Ange `--auth-mode` parametern till det äldre `key` värdet för att försöka hämta åtkomstnyckeln för kontot som ska användas för auktorisering. Om du utelämnar `--auth-mode` parametern försöker Azure CLI också hämta åtkomstnyckeln.

Om du vill `--auth-mode` använda parametern kontrollerar du att du har installerat Azure CLI version 2.0.46 eller senare. Kör `az --version` för att kontrollera den installerade versionen.

> [!NOTE]
> När ett lagringskonto är låst med Azure Resource Manager **skrivskyddad** låsning tillåts inte åtgärden Listnycklar för det lagringskontot. [](/rest/api/storagerp/storageaccounts/listkeys) **Listnycklar** är en POST-åtgärd och alla POST-åtgärder förhindras när **ett ReadOnly-lås** har konfigurerats för kontot. När kontot är låst med ett **ReadOnly-lås** måste därför användare som inte redan har kontonycklarna använda Azure AD-autentiseringsuppgifter för att få åtkomst till blobdata.

> [!IMPORTANT]
> Om du utelämnar parametern eller anger den till försöker Azure CLI använda kontoåtkomstnyckeln för `--auth-mode` `key` auktorisering. I det här fallet rekommenderar Microsoft att du anger åtkomstnyckeln antingen på kommandot eller i AZURE_STORAGE_KEY miljövariabeln.  Mer information om miljövariabler finns i avsnittet Set environment variables for authorization parameters (Ange [miljövariabler för auktoriseringsparametrar).](#set-environment-variables-for-authorization-parameters)
>
> Om du inte anger åtkomstnyckeln försöker Azure CLI anropa Azure Storage resursprovidern för att hämta den för varje åtgärd. Att utföra många dataåtgärder som kräver ett anrop till resursprovidern kan resultera i begränsning. Mer information om resursprovidergränser finns i [Skalbarhets- och prestandamål för Azure Storage resursprovidern](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Auktorisera med Azure AD-autentiseringsuppgifter

När du loggar in på Azure CLI med Azure AD-autentiseringsuppgifter returneras en OAuth 2.0-åtkomsttoken. Denna token används automatiskt av Azure CLI för att auktorisera efterföljande dataåtgärder mot Blob- eller Queue Storage. För åtgärder som stöds behöver du inte längre skicka en kontonyckel eller SAS-token med kommandot .

Du kan tilldela behörigheter till blobdata till ett Azure AD-säkerhetsobjekt via rollbaserad åtkomstkontroll i Azure (Azure RBAC). Mer information om Azure-roller i Azure Storage finns i [Hantera åtkomstbehörigheter för att Azure Storage data med Azure RBAC.](../common/storage-auth-aad-rbac-portal.md)

### <a name="permissions-for-calling-data-operations"></a>Behörigheter för att anropa dataåtgärder

Tilläggen Azure Storage stöds för åtgärder på blobdata. Vilka åtgärder du kan anropa beror på de behörigheter som beviljas till Azure AD-säkerhetsobjekt som du loggar in på Azure CLI med. Behörigheter till Azure Storage-containrar tilldelas via Azure RBAC. Om du till exempel har tilldelats rollen **Storage Blob Data Reader** kan du köra skriptkommandon som läser data från en container. Om du har tilldelats **rollen Storage Blob Data-deltagare** kan du köra skriptkommandon som läser, skriver eller tar bort en container eller de data som den innehåller.

Mer information om de behörigheter som krävs för varje Azure Storage på en container finns i [Anropa lagringsåtgärder med OAuth-token.](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Exempel: Auktorisera en åtgärd för att skapa en container med Azure AD-autentiseringsuppgifter

I följande exempel visas hur du skapar en container från Azure CLI med dina autentiseringsuppgifter för Azure AD. Om du vill skapa containern måste du logga in på Azure CLI och du behöver en resursgrupp och ett lagringskonto. Information om hur du skapar dessa resurser finns i [Snabbstart: Skapa, ladda ned och lista blobar med Azure CLI.](../blobs/storage-quickstart-blobs-cli.md)

1. Innan du skapar containern tilldelar du rollen [Storage Blob Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) till dig själv. Även om du är kontoägare behöver du explicit behörighet att utföra dataåtgärder mot lagringskontot. Mer information om hur du tilldelar Azure-roller finns i Använda Azure Portal för att tilldela en Azure-roll för [åtkomst till blob- och ködata.](../common/storage-auth-aad-rbac-portal.md)

    > [!IMPORTANT]
    > Azure-rolltilldelningar kan ta några minuter att spridas.

1. Anropa kommandot [az storage container create med](/cli/azure/storage/container#az_storage_container_create) `--auth-mode` parametern inställd på för att skapa `login` containern med dina autentiseringsuppgifter för Azure AD. Kom ihåg att ersätta platshållarvärden inom vinkelparenteser med dina egna värden:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Auktorisera med åtkomstnyckeln för kontot

Om du har kontonyckeln kan du anropa valfri Azure Storage dataåtgärd. I allmänhet är det mindre säkert att använda kontonyckeln. Om kontonyckeln komprometteras kan alla data i ditt konto komprometteras.

I följande exempel visas hur du skapar en container med åtkomstnyckeln för kontot. Ange kontonyckeln och ange `--auth-mode` parametern med `key` värdet:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

> [!IMPORTANT]
> När ett lagringskonto är låst med Azure Resource Manager **skrivskyddad** låsning tillåts inte åtgärden Listnycklar för det lagringskontot. [](/rest/api/storagerp/storageaccounts/listkeys) **Listnycklar** är en POST-åtgärd och alla POST-åtgärder förhindras när **ett ReadOnly-lås** har konfigurerats för kontot. När kontot är låst med ett **ReadOnly-lås** måste användarna därför komma åt data med Azure AD-autentiseringsuppgifter.

## <a name="authorize-with-a-sas-token"></a>Auktorisera med en SAS-token

Om du har en SAS-token kan du anropa dataåtgärder som tillåts av SAS. I följande exempel visas hur du skapar en container med hjälp av en SAS-token:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Ange miljövariabler för auktoriseringsparametrar

Du kan ange auktoriseringsparametrar i miljövariabler för att undvika att inkludera dem vid varje anrop Azure Storage en dataåtgärd. I följande tabell beskrivs tillgängliga miljövariabler.

| Miljövariabel                  | Description                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    Namnet på lagringskontot. Den här variabeln ska användas tillsammans med lagringskontonyckeln eller en SAS-token. Om ingen av dem finns försöker Azure CLI hämta åtkomstnyckeln för lagringskontot med hjälp av det autentiserade Azure AD-kontot. Om ett stort antal kommandon körs samtidigt kan begränsningsgränsen Azure Storage resursprovidern nås. Mer information om resursprovidergränser finns i [Skalbarhets- och prestandamål för Azure Storage resursprovidern](../common/scalability-targets-resource-provider.md).             |
|    AZURE_STORAGE_KEY                  |    Nyckeln till lagringskontot. Den här variabeln måste användas tillsammans med lagringskontots namn.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    En anslutningssträng som innehåller lagringskontonyckeln eller en SAS-token. Den här variabeln måste användas tillsammans med lagringskontots namn.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    En SAS-token (signatur för delad åtkomst). Den här variabeln måste användas tillsammans med lagringskontots namn.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    Auktoriseringsläget som kommandot ska köras med. Tillåtna värden `login` är (rekommenderas) eller `key` . Om du anger `login` använder Azure CLI dina Autentiseringsuppgifter för Azure AD för att auktorisera dataåtgärden. Om du anger det äldre läget försöker Azure CLI fråga efter åtkomstnyckeln för kontot `key` och auktorisera kommandot med nyckeln.    |

## <a name="next-steps"></a>Nästa steg

- [Använda Azure CLI för att tilldela en Azure-roll för åtkomst till blob- och ködata](../common/storage-auth-aad-rbac-cli.md)
- [Auktorisera åtkomst till blob- och ködata med hanterade identiteter för Azure-resurser](../common/storage-auth-aad-msi.md)