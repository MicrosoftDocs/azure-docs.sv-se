---
title: Välj hur du vill auktorisera åtkomst till ködata med Azure CLI
titleSuffix: Azure Storage
description: Ange hur du ska auktorisera dataåtgärder mot ködata med Azure CLI. Du kan auktorisera dataåtgärder med azure AD-autentiseringsuppgifter, med åtkomstnyckeln för kontot eller med en SAS-token (signatur för delad åtkomst).
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b3ac012da97194134f58d061dd9d84e945db554
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774475"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Välj hur du vill auktorisera åtkomst till ködata med Azure CLI

Azure Storage tillhandahåller tillägg för Azure CLI som gör att du kan ange hur du vill auktorisera åtgärder för ködata. Du kan auktorisera dataåtgärder på följande sätt:

- Med ett Azure Active Directory (Azure AD) säkerhetsobjekt. Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter för att få överlägsen säkerhet och enkel användning.
- Med kontoåtkomstnyckeln eller en SAS-token (signatur för delad åtkomst).

## <a name="specify-how-data-operations-are-authorized"></a>Ange hur dataåtgärder auktoriserats

Azure CLI-kommandon för att läsa och skriva ködata innehåller den valfria `--auth-mode` parametern . Ange den här parametern för att ange hur en dataåtgärd ska auktoreras:

- Ange `--auth-mode` parametern till `login` för att logga in med ett Azure AD-säkerhetsobjekt (rekommenderas).
- Ange `--auth-mode` parametern till det äldre `key` värdet för att försöka hämta åtkomstnyckeln för kontot som ska användas för auktorisering. Om du utelämnar `--auth-mode` parametern försöker Azure CLI också hämta åtkomstnyckeln.

Om du vill `--auth-mode` använda parametern kontrollerar du att du har installerat Azure CLI v2.0.46 eller senare. Kör `az --version` för att kontrollera den installerade versionen.

> [!NOTE]
> När ett lagringskonto är låst med Azure Resource Manager **skrivskyddad** låsning tillåts inte åtgärden Listnycklar för det lagringskontot. [](/rest/api/storagerp/storageaccounts/listkeys) **Listnycklar** är en POST-åtgärd och alla POST-åtgärder förhindras när **ett ReadOnly-lås** har konfigurerats för kontot. När kontot är låst med ett **ReadOnly-lås** måste därför användare som inte redan har kontonycklarna använda Azure AD-autentiseringsuppgifter för att få åtkomst till ködata.

> [!IMPORTANT]
> Om du utelämnar parametern eller anger den till försöker Azure CLI använda kontoåtkomstnyckeln för `--auth-mode` `key` auktorisering. I det här fallet rekommenderar Microsoft att du anger åtkomstnyckeln antingen i kommandot eller i `AZURE_STORAGE_KEY` miljövariabeln. Mer information om miljövariabler finns i avsnittet Set environment variables for authorization parameters (Ange [miljövariabler för auktoriseringsparametrar).](#set-environment-variables-for-authorization-parameters)
>
> Om du inte anger åtkomstnyckeln försöker Azure CLI anropa Azure Storage resursprovidern för att hämta den för varje åtgärd. Att utföra många dataåtgärder som kräver ett anrop till resursprovidern kan resultera i begränsning. Mer information om resursprovidergränser finns i [Skalbarhets- och prestandamål för Azure Storage resursprovidern](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Auktorisera med Azure AD-autentiseringsuppgifter

När du loggar in på Azure CLI med Azure AD-autentiseringsuppgifter returneras en OAuth 2.0-åtkomsttoken. Denna token används automatiskt av Azure CLI för att auktorisera efterföljande dataåtgärder mot Queue Storage. För åtgärder som stöds behöver du inte längre skicka en kontonyckel eller SAS-token med kommandot .

Du kan tilldela behörigheter till ködata till ett Azure AD-säkerhetsobjekt via rollbaserad åtkomstkontroll i Azure (Azure RBAC). Mer information om Azure-roller i Azure Storage finns i [Hantera åtkomstbehörigheter för att Azure Storage data med Azure RBAC.](../common/storage-auth-aad-rbac-portal.md)

### <a name="permissions-for-calling-data-operations"></a>Behörigheter för att anropa dataåtgärder

Tilläggen Azure Storage stöds för åtgärder med ködata. Vilka åtgärder du kan anropa beror på de behörigheter som beviljas till Azure AD-säkerhetsobjekt som du loggar in på Azure CLI med. Behörigheter till köer tilldelas via Azure RBAC. Om du till exempel har tilldelats rollen **Storage Queue Data Reader** kan du köra skriptkommandon som läser data från en kö. Om du har tilldelats **rollen Storage Queue Data Contributor** kan du köra skriptkommandon som läser, skriver eller tar bort en kö eller de data som de innehåller.

Mer information om de behörigheter som krävs för varje Azure Storage i en kö finns i [Anropa lagringsåtgärder med OAuth-token.](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens)  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Exempel: Auktorisera en åtgärd för att skapa en kö med Azure AD-autentiseringsuppgifter

I följande exempel visas hur du skapar en kö från Azure CLI med dina autentiseringsuppgifter för Azure AD. Om du vill skapa kön måste du logga in på Azure CLI och du behöver en resursgrupp och ett lagringskonto.

1. Innan du skapar kön tilldelar du rollen [Storage Queue Data-deltagare](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) till dig själv. Även om du är kontoägare behöver du explicit behörighet att utföra dataåtgärder mot lagringskontot. Mer information om hur du tilldelar Azure-roller finns i Använda Azure Portal för att tilldela en Azure-roll för [åtkomst till blob- och ködata.](../common/storage-auth-aad-rbac-portal.md)

    > [!IMPORTANT]
    > Azure-rolltilldelningar kan ta några minuter att spridas.

1. Anropa kommandot [`az storage queue create`](/cli/azure/storage/queue#az_storage_queue_create) med `--auth-mode` parametern inställd på för att skapa kön `login` med dina Autentiseringsuppgifter för Azure AD. Kom ihåg att ersätta platshållarvärden inom vinkelparenteser med dina egna värden:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Auktorisera med åtkomstnyckeln för kontot

Om du har kontonyckeln kan du anropa valfri Azure Storage dataåtgärd. I allmänhet är det mindre säkert att använda kontonyckeln. Om kontonyckeln komprometteras kan alla data i ditt konto komprometteras.

I följande exempel visas hur du skapar en kö med åtkomstnyckeln för kontot. Ange kontonyckeln och ange `--auth-mode` parametern med `key` värdet:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Auktorisera med en SAS-token

Om du har en SAS-token kan du anropa dataåtgärder som tillåts av SAS. I följande exempel visas hur du skapar en kö med hjälp av en SAS-token:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Ange miljövariabler för auktoriseringsparametrar

Du kan ange auktoriseringsparametrar i miljövariabler för att undvika att inkludera dem vid varje anrop Azure Storage en dataåtgärd. I följande tabell beskrivs tillgängliga miljövariabler.

| Miljövariabel | Description |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | Namnet på lagringskontot. Den här variabeln ska användas tillsammans med lagringskontonyckeln eller en SAS-token. Om ingen av dem finns försöker Azure CLI hämta åtkomstnyckeln för lagringskontot med hjälp av det autentiserade Azure AD-kontot. Om ett stort antal kommandon körs samtidigt kan begränsningsgränsen Azure Storage resursprovidern nås. Mer information om resursprovidergränser finns i [Skalbarhets- och prestandamål för Azure Storage resursprovidern](../common/scalability-targets-resource-provider.md). |
| **AZURE_STORAGE_KEY** | Nyckeln till lagringskontot. Den här variabeln måste användas tillsammans med lagringskontots namn. |
| **AZURE_STORAGE_CONNECTION_STRING** | En anslutningssträng som innehåller lagringskontonyckeln eller en SAS-token. Den här variabeln måste användas tillsammans med lagringskontots namn. |
| **AZURE_STORAGE_SAS_TOKEN** | En SAS-token (signatur för delad åtkomst). Den här variabeln måste användas tillsammans med lagringskontots namn. |
| **AZURE_STORAGE_AUTH_MODE** | Auktoriseringsläget som kommandot ska köras med. Tillåtna värden `login` är (rekommenderas) eller `key` . Om du anger `login` använder Azure CLI dina Autentiseringsuppgifter för Azure AD för att auktorisera dataåtgärden. Om du anger det äldre läget försöker Azure CLI fråga efter åtkomstnyckeln för kontot `key` och auktorisera kommandot med nyckeln. |

## <a name="next-steps"></a>Nästa steg

- [Använda Azure CLI för att tilldela en Azure-roll för åtkomst till blob- och ködata](../common/storage-auth-aad-rbac-cli.md)
- [Auktorisera åtkomst till blob- och ködata med hanterade identiteter för Azure-resurser](../common/storage-auth-aad-msi.md)
