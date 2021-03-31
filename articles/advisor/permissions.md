---
title: Behörigheter i Azure Advisor
description: Advisor-behörigheter och hur de kan blockera din möjlighet att konfigurera prenumerationer eller skjuta upp eller stänga av rekommendationer.
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 402a21c47c4cba8f747d5d4601f9c95034c99262
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91712927"
---
# <a name="permissions-in-azure-advisor"></a>Behörigheter i Azure Advisor

Azure Advisor ger rekommendationer baserat på användningen och konfigurationen av dina Azure-resurser och prenumerationer. Advisor använder de [inbyggda roller](../role-based-access-control/built-in-roles.md) som tillhandahålls av [rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../role-based-access-control/overview.md) för att hantera åtkomst till rekommendationer och Advisor-funktioner. 

## <a name="roles-and-their-access"></a>Roller och deras åtkomst

Följande tabell definierar de roller och den åtkomst som de har i Advisor:

| **Role** | **Visa rekommendationer** | **Redigera regler** | **Redigera prenumerations konfiguration** | **Redigera konfiguration av resurs grupp**| **Stäng och Senarelägg rekommendationer**|
|---|:---:|:---:|:---:|:---:|:---:|
|Prenumerations ägare|**Röntgen**|**Röntgen**|**Röntgen**|**Röntgen**|**Röntgen**|
|Prenumerations deltagare|**Röntgen**|**Röntgen**|**Röntgen**|**Röntgen**|**Röntgen**|
|Prenumerations läsare|**Röntgen**|--|--|--|--|
|Resurs grupp ägare|**Röntgen**|--|--|**Röntgen**|**Röntgen**|
|Resurs grupps deltagare|**Röntgen**|--|--|**Röntgen**|**Röntgen**|
|Resurs grupp läsare|**Röntgen**|--|--|--|--|
|Resurs ägare|**Röntgen**|--|--|--|**Röntgen**|
|Resurs deltagare|**Röntgen**|--|--|--|**Röntgen**|
|Resurs läsare|**Röntgen**|--|--|--|--|

> [!NOTE]
> Åtkomst till att Visa rekommendationer är beroende av din åtkomst till rekommendationens påverkade resurser.

## <a name="permissions-and-unavailable-actions"></a>Behörigheter och otillgängliga åtgärder

Brist på rätt behörighet kan blockera din möjlighet att utföra åtgärder i Advisor. Här följer några vanliga problem.

### <a name="unable-to-configure-subscriptions-or-resource-groups"></a>Det gick inte att konfigurera prenumerationer eller resurs grupper

När du försöker konfigurera prenumerationer eller resurs grupper i Advisor kan du se att alternativet att inkludera eller exkludera är inaktiverat. Denna status anger att du inte har tillräcklig behörighet för resurs gruppen eller prenumerationen. För att lösa det här problemet, lär du dig att [bevilja en användar åtkomst](../role-based-access-control/quickstart-assign-role-user-portal.md).

### <a name="unable-to-postpone-or-dismiss-a-recommendation"></a>Det går inte att skjuta upp eller stänga av en rekommendation

Om du får ett fel meddelande när du försöker skjuta upp eller stänga av en rekommendation kanske du inte har tillräcklig behörighet. Se till att du har minst deltagar åtkomst till resursen som påverkas av rekommendationen som du skjuter upp eller tar ur beaktande. För att lösa det här problemet, lär du dig att [bevilja en användar åtkomst](../role-based-access-control/quickstart-assign-role-user-portal.md).

## <a name="next-steps"></a>Nästa steg

Den här artikeln ger en översikt över hur Advisor använder Azure RBAC för att kontrol lera användar behörigheter och hur du löser vanliga problem. Mer information om Advisor finns i:

- [Vad är Azure Advisor?](./advisor-overview.md)
- [Kom igång med Azure Advisor](./advisor-get-started.md)
