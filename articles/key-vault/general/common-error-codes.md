---
title: Vanliga felkoder för Azure Key Vault | Microsoft Docs
description: Vanliga felkoder för Azure Key Vault
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: 9ae13b88d767e43c425ceb86d0be455cebc0e6ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96462523"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Vanliga felkoder för Azure Key Vault

Fel koderna som anges i följande tabell kan returneras av en åtgärd i Azure Key Vault

| Felkod | Användar meddelande |
|--|--|
| VaultAlreadyExists |  Ditt försök att skapa ett nytt nyckel valv med det angivna namnet misslyckades eftersom namnet redan används. Om du nyligen har tagit bort ett nyckel valv med det här namnet kan det fortfarande vara i läget Soft Deleted. Du kan kontrol lera om den finns i läget Soft-Deleted [här](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault) |
| VaultNameNotValid |  Valv namnet ska vara 24 tecken, alfanumeriskt och börja med ett alfabet |
| AccessDenied |  Du kanske saknar behörigheter i åtkomst principen för att utföra åtgärden. |
| ForbiddenByFirewall |  Klient adressen är inte auktoriserad och anroparen är inte en betrodd tjänst. |
| ConflictError |  Du begär flera åtgärder på samma objekt.  |
| RegionNotSupported |  Den angivna Azure-regionen stöds inte för den här resursen. |
| SkuNotSupported |  Den angivna SKU-typen stöds inte för den här resursen. |
| ResourceNotFound |  Det gick inte att hitta den angivna Azure-resursen. |
| ResourceGroupNotFound | Det gick inte att hitta den angivna Azure-resurs gruppen. |
| CertificateExpired |  Kontrol lera utgångs datum och giltighets tid för certifikatet. |


## <a name="next-steps"></a>Nästa steg

- Se [guiden för Azure Key Vault utvecklare](developers-guide.md)
- Läs mer om [att autentisera till nyckel valv](authentication.md)