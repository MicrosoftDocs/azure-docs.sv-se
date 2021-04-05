---
title: Anpassade kontroller i villkorlig åtkomst i Azure AD
description: Lär dig hur anpassade kontroller i Azure Active Directory villkorlig åtkomst fungerar.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7585c91e42b2d3591532756c1ead9ea60b7035e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94837591"
---
# <a name="custom-controls-preview"></a>Anpassade kontroller (förhands granskning)

Anpassade kontroller är en förhands gransknings funktion för Azure Active Directory. När du använder anpassade kontroller omdirigeras användarna till en kompatibel tjänst för att uppfylla autentiseringskrav utanför Azure Active Directory. För att tillfredsställa den här kontrollen omdirigeras användarens webbläsare till den externa tjänsten, utför alla nödvändiga autentiseringar och dirigeras sedan tillbaka till Azure Active Directory. Azure Active Directory verifierar svaret och, om användaren har autentiserats eller verifierats, fortsätter användaren i flödet för villkorlig åtkomst.

> [!NOTE]
> Mer information om ändringar som vi planerar för den anpassade kontroll funktionen finns i februari 2020- [arkivet för nyheter](../fundamentals/whats-new-archive.md#upcoming-changes-to-custom-controls).

## <a name="creating-custom-controls"></a>Skapa anpassade kontroller

Anpassade kontroller fungerar med en begränsad uppsättning godkända autentiseringsproviders. Om du vill skapa en anpassad kontroll kontaktar du först den leverantör som du vill använda. Varje icke-Microsoft-provider har en egen process och krav för att registrera dig, prenumerera på eller på annat sätt bli en del av tjänsten och för att indikera att du vill integrera med villkorlig åtkomst. Vid det här tillfället tillhandahåller providern ett data block i JSON-format. Med den här informationen kan providern och villkorlig åtkomst samar beta för din klient, skapa den nya kontrollen och definiera hur villkorlig åtkomst kan avgöra om användarna har utfört verifiering med providern.

Kopiera JSON-data och klistra sedan in den i den relaterade text rutan. Gör inga ändringar i JSON om du inte uttryckligen förstår den ändring du gör. Genom att göra en ändring kan du bryta anslutningen mellan leverantören och Microsoft och eventuellt låsa dig och dina användare från dina konton.

Alternativet för att skapa en anpassad kontroll finns i avsnittet **Hantera** på sidan för **villkorlig åtkomst** .

![Gränssnitt för anpassade kontroller i villkorlig åtkomst](./media/controls/custom-controls-conditional-access.png)

Om du klickar på **ny anpassad kontroll** öppnas ett blad med en text ruta för dina KONTROLLers JSON-data.  

![Ny anpassad kontroll](./media/controls/new-custom-controls-conditional-access.png)

## <a name="deleting-custom-controls"></a>Tar bort anpassade kontroller

Om du vill ta bort en anpassad kontroll måste du först se till att den inte används i någon princip för villkorlig åtkomst. När du är klar:

1. Gå till listan med anpassade kontroller
1. Klicka på...  
1. Välj **Ta bort**.

## <a name="editing-custom-controls"></a>Redigera anpassade kontroller

Om du vill redigera en anpassad kontroll måste du ta bort den aktuella kontrollen och skapa en ny kontroll med den uppdaterade informationen.

## <a name="known-limitations"></a>Kända begränsningar

Det går inte att använda anpassade kontroller med identitets skydds automatisering som kräver Azure AD Multi-Factor Authentication, Azure AD självbetjäning för återställning av lösen ord (SSPR), som uppfyller kraven för Multi-Factor Authentication-krav, för att höja roller i Privileged Identity Manager (PIM), som en del av Intune-registreringen eller när du ansluter enheter till Azure AD.

## <a name="next-steps"></a>Nästa steg

- [Vanliga principer för villkorlig åtkomst](concept-conditional-access-policy-common.md)

- [Läget Endast rapport](concept-conditional-access-report-only.md)

- [Simulera inloggnings beteende med hjälp av What If verktyget för villkorlig åtkomst](troubleshoot-conditional-access-what-if.md)
