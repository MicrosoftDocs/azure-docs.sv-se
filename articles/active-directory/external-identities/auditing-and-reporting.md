---
title: Granskning och rapportering av en B2B-samarbets användare – Azure AD
description: Gäst användar egenskaper kan konfigureras i Azure Active Directory B2B-samarbete
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a805f643e5f93270fe4721f527073baa9a1e4c8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87910274"
---
# <a name="auditing-and-reporting-a-b2b-collaboration-user"></a>Granskning och rapportering av en B2B-samarbets användare
Med gäst användare har du gransknings funktioner som liknar medlems användare. 

## <a name="access-reviews"></a>Åtkomstgranskningar
Du kan använda åtkomst granskningar för att regelbundet kontrol lera om gäst användare fortfarande behöver åtkomst till dina resurser. Funktionen **åtkomst granskningar** finns i **Azure Active Directory** under **externa identiteter**  >  **åtkomst granskningar**. Du kan också söka efter "åtkomst granskningar" från **alla tjänster** i Azure Portal. Information om hur du använder åtkomst granskningar finns i [Hantera gäst åtkomst med åtkomst granskningar för Azure AD](../governance/manage-guest-access-with-access-reviews.md).

## <a name="audit-logs"></a>Granskningsloggar

Azure AD audit-loggarna innehåller poster för system-och användar aktiviteter, inklusive aktiviteter som initieras av gäst användare. För att få åtkomst till gransknings loggar går du till **Azure Active Directory**, under **övervakning**, och väljer **gransknings loggar**. Här är ett exempel på Inbjudnings-och inlösnings historiken för oogle för inbjudna Sam:

![Skärm bild som visar och exempel på gransknings loggens utdata](./media/auditing-and-reporting/audit-log.png)

Du kan se var och en av dessa händelser för att få information. Låt oss till exempel titta på godkännande informationen.

![Skärm bild som visar och exempel på utdata från aktivitets information](./media/auditing-and-reporting/activity-details.png)

Du kan också exportera dessa loggar från Azure AD och använda det rapporterings verktyg som du väljer för att hämta anpassade rapporter.

### <a name="next-steps"></a>Nästa steg

- [Användaregenskaper för B2B-samarbete](user-properties.md)

