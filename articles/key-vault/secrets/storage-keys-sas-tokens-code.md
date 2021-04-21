---
title: Hämta token för signaturer för delad åtkomst i | Azure Key Vault
description: Funktionen för hanterat lagringskonto ger en sömlös integrering mellan Azure Key Vault och ett Azure Storage-konto. I det här exemplet används Azure SDK för .NET för att hantera SAS-token.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 8c8a7b357efc2e30d1ca93d0b45fd2563c980045
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752758"
---
# <a name="create-sas-definition-and-fetch-shared-access-signature-tokens-in-code"></a>Skapa SAS-definition och hämta token för signatur för delad åtkomst i kod

Du kan hantera ditt lagringskonto med SAS-token (signatur för delad åtkomst) som lagras i ditt nyckelvalv. Mer information finns i Bevilja [begränsad åtkomst till Azure Storage med hjälp av SAS.](../../storage/common/storage-sas-overview.md)

> [!NOTE]
> Vi rekommenderar att du använder rollbaserad åtkomstkontroll [i Azure (Azure RBAC)](../../storage/common/storage-auth-aad.md) för att skydda ditt lagringskonto för överlägsen säkerhet och enkel användning via auktorisering med delad nyckel.

Den här artikeln innehåller exempel på .NET-kod som skapar en SAS-definition och hämtar SAS-token. Se vårt [ShareLink-exempel](/samples/azure/azure-sdk-for-net/share-link/) för fullständig information, inklusive den genererade klienten för Key Vault-hanterade lagringskonton. Information om hur du skapar och lagrar SAS-token finns i Hantera lagringskontonycklar med Key Vault och [Azure CLI](overview-storage-keys.md) eller Hantera lagringskontonycklar med Key Vault [och Azure PowerShell](overview-storage-keys-powershell.md).

## <a name="code-samples"></a>Kodexempel

I följande exempel skapar vi en SAS-mall:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="91-97":::

Med den här mallen kan vi skapa en SAS-definition med hjälp av 

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="137-156":::

När SAS-definitionen har skapats kan du hämta SAS-token som hemligheter med hjälp av `SecretClient` en . Du måste inleda det hemliga namnet med lagringskontots namn följt av ett bindestreck:

:::code language="csharp" source="~/azure-sdk-for-net/sdk/keyvault/samples/sharelink/Program.cs" range="52-58":::

Om din signaturtoken för delad åtkomst snart upphör att gälla kan du hämta samma hemlighet igen för att generera en ny.

Mer information om hur du använder en SAS-token Key Vault hämtad från en sas för att komma åt Azure Storage tjänster finns i Använda en konto-SAS för att få åtkomst [till Blob Service](../../storage/common/storage-account-sas-create-dotnet.md#use-an-account-sas-from-a-client)

> [!NOTE]
> Din app måste vara beredd på att uppdatera SAS om den hämtar 403 från Storage så att du kan hantera de fall där en nyckel har komprometterats och du behöver rotera dem snabbare än den normala rotationsperioden. 

## <a name="next-steps"></a>Nästa steg
- Lär dig hur du [beviljar begränsad åtkomst till Azure Storage resurser med hjälp av SAS](../../storage/common/storage-sas-overview.md).
- Lär dig hur [du hanterar lagringskontonycklar med Key Vault och Azure CLI](overview-storage-keys.md) eller [Azure PowerShell](overview-storage-keys-powershell.md).
- Se [exempel på nyckel för hanterat lagringskonto](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)