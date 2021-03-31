---
title: ta med fil
description: ta med fil
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/06/2020
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: ba715d510dc296ffa8f9c0ee58841f284416a118
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "86027347"
---
## <a name="protect-your-access-keys"></a>Skydda dina åtkomst nycklar

Dina åtkomst nycklar för lagrings kontot liknar ett rot lösen ord för ditt lagrings konto. Var alltid noga med att skydda dina åtkomst nycklar. Använd Azure Key Vault för att hantera och rotera dina nycklar på ett säkert sätt. Undvik att distribuera åtkomst nycklar till andra användare, hårdkoda dem eller spara dem var som helst i oformaterad text som är tillgängliga för andra. Rotera dina nycklar om du misstänker att de kan ha komprometterats.

> [!NOTE]
> Microsoft rekommenderar att du använder Azure Active Directory (Azure AD) för att auktorisera begär Anden mot blob-och Queue-data om möjligt, i stället för delad nyckel. Azure AD ger överlägsen säkerhet och enkel användning över delad nyckel. Mer information om hur du auktoriserar åtkomst till data med Azure AD finns i [bevilja åtkomst till Azure-blobbar och köer med hjälp av Azure Active Directory](../articles/storage/common/storage-auth-aad.md).
