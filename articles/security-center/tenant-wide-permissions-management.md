---
title: Bevilja och begär behörigheter för hela klienten i Azure Security Center
description: Lär dig hur du hanterar behörigheter för klient organisationen i Azure Security Center
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.openlocfilehash: b3ddbdf04dc736b6f78a04dc6bb2bc484e67f70f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107950"
---
# <a name="grant-and-request-tenant-wide-visibility"></a>Bevilja och begära synlighet för hela klienten

En användare med rollen Azure Active Directory (AD) för **Global administratör** kan ha ansvar för hela företaget, men saknar Azure-behörighet att visa den organisations information som finns i Azure Security Center. Behörighets höjning krävs eftersom roll tilldelningar för Azure AD inte beviljar åtkomst till Azure-resurser. 

> [!TIP]
> Läs mer om behörighets höjning för rollen global administratör i [öka åtkomsten för att hantera alla Azure-prenumerationer och hanterings grupper](../role-based-access-control/elevate-access-global-admin.md).

## <a name="grant-tenant-wide-permissions-to-yourself"></a>Bevilja behörighet för hela klienten själv

Så här tilldelar du dig själv behörigheter på klient nivå:

1. Om din organisation hanterar resurs åtkomst med [Azure AD Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-configure.md)eller något annat PIM-verktyg måste den globala administratörs rollen vara aktiv för användaren enligt proceduren nedan.

1. Som global administratörs användare utan en tilldelning i rot hanterings gruppen för klient organisationen öppnar du Security Center sidan **Översikt** och väljer länken för den sammanslagna länken för **hela klienten** i banderollbilden. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-banner.png" alt-text="Aktivera behörigheter för klient organisations nivå i Azure Security Center":::

1. Välj den nya Azure-roll som ska tilldelas. 

    :::image type="content" source="media/security-center-management-groups/enable-tenant-level-permissions-form.png" alt-text="Formulär för att definiera behörigheter för klient organisations nivå som ska tilldelas till din användare":::

    > [!TIP]
    > I allmänhet krävs rollen som säkerhets administratör för att tillämpa principer på rotnivån, medan säkerhets läsaren räcker för att tillhandahålla synligheten på klient nivå. Mer information om de behörigheter som har beviljats av de här rollerna finns i beskrivningen av den [inbyggda rollen säkerhets administratör](../role-based-access-control/built-in-roles.md#security-admin) eller den [inbyggda rollen säkerhets läsare](../role-based-access-control/built-in-roles.md#security-reader).
    >
    > För skillnader mellan dessa roller som är särskilt för Security Center, se tabellen i [roller och tillåtna åtgärder](security-center-permissions.md#roles-and-allowed-actions).

    Vyn i hela organisationen uppnås genom att du beviljar roller på klient organisationens rot hanterings grupps nivå.  

1. Logga ut från Azure Portal och logga sedan in igen.

1. När du har förhöjd åtkomst, öppna eller uppdatera Azure Security Center för att kontrol lera att du har insyn i alla prenumerationer under Azure AD-klienten. 


## <a name="request-tenant-wide-permissions-when-yours-are-insufficient"></a>Begär behörigheter för klient åtkomst när dina inte är tillräckliga

Om du loggar in på Security Center och ser en banderoll som visar att vyn är begränsad kan du klicka på genom att skicka en begäran till den globala administratören för din organisation. I begäran kan du ta med den roll som du vill tilldela och den globala administratören fattar ett beslut om vilken roll som ska beviljas. 

Det är det globala administratörs beslutet om du vill acceptera eller avvisa dessa förfrågningar. 

> [!IMPORTANT]
> Du kan bara skicka en begäran var sjunde dag.

Så här begär du utökade behörigheter från den globala administratören:

1. Öppna Azure Security Center från Azure Portal.

1. Om du ser banderollen "du ser begränsad information". Markera det.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions.png" alt-text="Banderoll som informerar användaren om att de kan begära behörigheter för hela klienten.":::

1. I formuläret detaljerad begäran väljer du önskad roll och motivering för varför du behöver dessa behörigheter.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-details.png" alt-text="Informations sida för att begära klient behörighet från din globala Azure-administratör":::

1. Välj **begär åtkomst**.

    Ett e-postmeddelande skickas till den globala administratören. E-postmeddelandet innehåller en länk till Security Center där de kan godkänna eller avvisa begäran.

    :::image type="content" source="media/security-center-management-groups/request-tenant-permissions-email.png" alt-text="E-posta till den globala administratören för nya behörigheter":::

    När den globala administratören väljer **Granska begäran** och slutför processen, skickas ett e-postmeddelande till den begär ande användaren. 

## <a name="next-steps"></a>Nästa steg

Läs mer om Security Center behörigheter på följande relaterade sida:

- [Behörigheter i Azure Security Center](security-center-permissions.md)