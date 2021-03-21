---
title: Välj hur du vill ge åtkomst till köa data med Azure CLI
titleSuffix: Azure Storage
description: Ange hur data åtgärder ska auktoriseras mot köa data med Azure CLI. Du kan auktorisera data åtgärder med hjälp av autentiseringsuppgifter för Azure AD, med konto åtkomst nyckeln eller med en SAS-token (signatur för delad åtkomst).
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2f7092d8ce184d7021774814e96935e46d1ffb56
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100363176"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Välj hur du vill ge åtkomst till köa data med Azure CLI

Azure Storage innehåller tillägg för Azure CLI som gör att du kan ange hur du vill auktorisera åtgärder för köa data. Du kan auktorisera data åtgärder på följande sätt:

- Med en Azure Active Directory (Azure AD) säkerhets objekt. Microsoft rekommenderar att du använder Azure AD-autentiseringsuppgifter för överlägsen säkerhet och enkel användning.
- Med konto åtkomst nyckeln eller en SAS-token (signatur för delad åtkomst).

## <a name="specify-how-data-operations-are-authorized"></a>Ange hur data åtgärder auktoriseras

Azure CLI-kommandon för att läsa och skriva köa data inkluderar den valfria `--auth-mode` parametern. Ange den här parametern för att ange hur en data åtgärd ska auktoriseras:

- Ange att `--auth-mode` parametern ska `login` Logga in med ett säkerhets objekt för Azure AD (rekommenderas).
- Ange `--auth-mode` parametern till det bakåtkompatibla `key` värdet för att försöka hämta konto åtkomst nyckeln som ska användas för auktorisering. Om du utelämnar `--auth-mode` parametern försöker Azure CLI också hämta åtkomst nyckeln.

Om du vill använda `--auth-mode` parametern kontrollerar du att du har installerat Azure CLI v-2.0.46 eller senare. Kör `az --version` för att kontrol lera den installerade versionen.

> [!NOTE]
> När ett lagrings konto är låst med ett Azure Resource Manager **skrivskyddat** lås, tillåts inte [list nyckel](/rest/api/storagerp/storageaccounts/listkeys) åtgärden för det lagrings kontot. **List nycklar** är en post-åtgärd och alla post-åtgärder förhindras när ett **skrivskyddat** lås har kon figurer ATS för kontot. Av den anledningen måste användare som inte redan har konto nycklar använda Azure AD-autentiseringsuppgifter för att få åtkomst till köa data när kontot är låst med ett **skrivskyddat** lås.

> [!IMPORTANT]
> Om du utelämnar `--auth-mode` parametern eller anger den till `key` , försöker Azure CLI använda kontots åtkomst nyckel för auktorisering. I det här fallet rekommenderar Microsoft att du ger åtkomst nyckeln antingen i kommandot eller i `AZURE_STORAGE_KEY` miljövariabeln. Mer information om miljövariabler finns i avsnittet [Ange miljövariabler för parametrar för auktorisering](#set-environment-variables-for-authorization-parameters).
>
> Om du inte anger åtkomst nyckeln försöker Azure CLI anropa Azure Storage Resource Provider för att hämta den för varje åtgärd. Att utföra många data åtgärder som kräver ett anrop till resurs leverantören kan leda till begränsning. Mer information om begränsningar för resurs leverantörer finns i [skalbarhets-och prestanda mål för Azure Storage resurs leverantör](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Auktorisera med Azure AD-autentiseringsuppgifter

När du loggar in på Azure CLI med Azure AD-autentiseringsuppgifter returneras en OAuth 2,0-åtkomsttoken. Token används automatiskt av Azure CLI för att auktorisera efterföljande data åtgärder mot Queue Storage. För åtgärder som stöds behöver du inte längre skicka en konto nyckel eller SAS-token med kommandot.

Du kan tilldela behörigheter till köa data till ett säkerhets objekt i Azure AD via rollbaserad åtkomst kontroll (Azure RBAC). Mer information om Azure-roller i Azure Storage finns i [Hantera åtkomst rättigheter för att Azure Storage data med Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Behörigheter för att anropa data åtgärder

Azure Storage-tilläggen stöds för åtgärder på köa data. Vilka åtgärder som kan anropas beror på vilka behörigheter som beviljats för det säkerhets objekt i Azure AD som du loggar in på Azure CLI. Behörigheter till köer tilldelas via Azure RBAC. Om du till exempel har tilldelats rollen **lagrings köns data läsare** kan du köra skript kommandon som läser data från en kö. Om du har tilldelats rollen **lagrings köns data deltagare** kan du köra skript kommandon som läser, skriver eller tar bort en kö eller de data som de innehåller.

Mer information om de behörigheter som krävs för varje Azure Storage-åtgärd i en kö finns i [anropa lagrings åtgärder med OAuth-token](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Exempel: auktorisera en åtgärd för att skapa en kö med Azure AD-autentiseringsuppgifter

I följande exempel visas hur du skapar en kö från Azure CLI med dina autentiseringsuppgifter för Azure AD. Om du vill skapa kön måste du logga in på Azure CLI och du behöver en resurs grupp och ett lagrings konto.

1. Innan du skapar kön ska du tilldela rollen [Storage Queue data Contributor](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) till dig själv. Även om du är kontots ägare behöver du explicita behörigheter för att utföra data åtgärder mot lagrings kontot. Mer information om hur du tilldelar Azure-roller finns i [använda Azure Portal för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > Det kan ta några minuter att sprida Azures roll tilldelningar.

1. Anropa [`az storage queue create`](/cli/azure/storage/queue#az-storage-queue-create) kommandot med den `--auth-mode` parameter som har angetts för `login` att skapa kön med dina AUTENTISERINGSUPPGIFTER för Azure AD. Kom ihåg att ersätta plats hållarnas värden inom vinkelparenteser med dina egna värden:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Auktorisera med konto åtkomst nyckeln

Om du har konto nyckeln kan du anropa alla Azure Storage data åtgärder. I allmänhet är det mindre säkert att använda konto nyckeln. Om konto nyckeln komprometteras kan alla data i ditt konto komprometteras.

I följande exempel visas hur du skapar en kö med hjälp av kontots åtkomst nyckel. Ange konto nyckeln och ange `--auth-mode` parametern med `key` värdet:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Auktorisera med en SAS-token

Om du har en SAS-token kan du anropa data åtgärder som tillåts av SAS. I följande exempel visas hur du skapar en kö med en SAS-token:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Ange miljövariabler för parametrar för auktorisering

Du kan ange auktoriseringsregler i miljövariabler för att undvika att ta med dem vid varje anrop till en Azure Storage data åtgärd. I följande tabell beskrivs tillgängliga miljövariabler.

| Miljövariabel | Beskrivning |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | Namnet på lagringskontot. Den här variabeln ska användas tillsammans med antingen lagrings konto nyckeln eller en SAS-token. Om ingen sådan används försöker Azure CLI Hämta åtkomst nyckeln för lagrings kontot med hjälp av det autentiserade Azure AD-kontot. Om ett stort antal kommandon körs samtidigt kan begränsningen för Azure Storage resurs leverantörs begränsning nås. Mer information om begränsningar för resurs leverantörer finns i [skalbarhets-och prestanda mål för Azure Storage resurs leverantör](../common/scalability-targets-resource-provider.md). |
| **AZURE_STORAGE_KEY** | Nyckeln till lagringskontot. Den här variabeln måste användas tillsammans med lagrings kontots namn. |
| **AZURE_STORAGE_CONNECTION_STRING** | En anslutnings sträng som innehåller lagrings konto nyckeln eller en SAS-token. Den här variabeln måste användas tillsammans med lagrings kontots namn. |
| **AZURE_STORAGE_SAS_TOKEN** | En SAS-token (signatur för delad åtkomst). Den här variabeln måste användas tillsammans med lagrings kontots namn. |
| **AZURE_STORAGE_AUTH_MODE** | Det Authorization-läge som kommandot ska köras med. Tillåtna värden är `login` (rekommenderas) eller `key` . Om du anger `login` så använder Azure CLI dina Azure AD-autentiseringsuppgifter för att auktorisera data åtgärden. Om du anger bakåtkompatibelt `key` läge försöker Azure CLI fråga efter kontots åtkomst nyckel och auktoriserar kommandot med nyckeln. |

## <a name="next-steps"></a>Nästa steg

- [Använd Azure CLI för att tilldela en Azure-roll för åtkomst till blob-och Queue-data](../common/storage-auth-aad-rbac-cli.md)
- [Ge åtkomst till blob-och Queue-data med hanterade identiteter för Azure-resurser](../common/storage-auth-aad-msi.md)
