---
title: Inställningar för ändringsbegäran för ett Access-paket i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig hur du ändrar inställningar för begäran för ett Access-paket i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b6bc00af8d54c8748dd82b934974282e0e8da0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102426968"
---
# <a name="change-request-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Inställningar för ändrings begär Anden för ett Access-paket i hantering av Azure AD-berättigande

Som en Access Package Manager kan du när som helst ändra vilka användare som kan begära ett åtkomst paket genom att redigera principen eller lägga till en ny princip. I den här artikeln beskrivs hur du ändrar inställningarna för begäran för ett befintligt Access-paket.

## <a name="choose-between-one-or-multiple-policies"></a>Välj mellan en eller flera principer

Hur du anger vem som kan begära ett åtkomst paket är med en princip. Innan du skapar en ny princip eller redigerar en befintlig princip i ett Access-paket måste du bestämma hur många principer som krävs för åtkomst paketet. 

När du skapar ett Access-paket kan du ange en inställning för begäran som skapar en princip. De flesta åtkomst paket kommer att ha en enda princip, men ett enda åtkomst paket kan ha flera principer. Du kan skapa flera principer för ett Access-paket om du vill att olika uppsättningar med användare ska kunna tilldelas tilldelningar med olika inställningar för begäran och godkännande. 

En enda princip kan till exempel inte användas för att tilldela interna och externa användare till samma Access-paket. Du kan dock skapa två principer i samma Access-paket, en för interna användare och en för externa användare. Om det finns flera principer som gäller för en användare, uppmanas de vid tidpunkten för begäran att välja den princip som de vill tilldelas. Följande diagram visar ett Access-paket med två principer.

![Flera principer i ett Access-paket](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

### <a name="how-many-policies-will-i-need"></a>Hur många principer behöver jag?

| Scenario | Antal principer |
| --- | --- |
| Jag vill att alla användare i min katalog ska ha samma inställningar för begäran och godkännande för ett Access-paket | En |
| Jag vill att alla användare i vissa anslutna organisationer ska kunna begära ett Access-paket | En |
| Jag vill tillåta användare i min katalog och även användare utanför katalogen för att begära ett Access-paket | Flera |
| Jag vill ange olika godkännande inställningar för vissa användare | Flera |
| Jag vill att vissa användare får åtkomst till paket tilldelningar så att andra användare kan utöka sin åtkomst | Flera |

Information om prioritets logiken som används när flera principer tillämpas finns i [flera principer](entitlement-management-troubleshoot.md#multiple-policies
).

## <a name="open-an-existing-access-package-and-add-a-new-policy-of-request-settings"></a>Öppna ett befintligt Access-paket och Lägg till en ny princip för begär ande inställningar

Om du har en uppsättning användare som ska ha olika inställningar för begäran och godkännande måste du förmodligen skapa en ny princip. Följ dessa steg om du vill börja lägga till en ny princip i ett befintligt Access-paket:

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **principer** och **Lägg sedan till princip**.

1. Du kommer att börja på fliken **grundläggande** . Ange ett namn och en beskrivning för principen.

    ![Skapa en princip med namn och beskrivning](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Klicka på **Nästa** för att öppna fliken **förfrågningar** .

1. Ändra vilka **användare som kan begära åtkomst** inställningar. Använd stegen i följande avsnitt för att ändra inställningen till något av följande alternativ: 
    - [För användare i din katalog](#for-users-in-your-directory) 
    - [För användare som inte är i din katalog](#for-users-not-in-your-directory)
    - [Ingen (endast administratörs direkta tilldelningar)](#none-administrator-direct-assignments-only)

## <a name="for-users-in-your-directory"></a>För användare i din katalog

Följ dessa steg om du vill tillåta att användare i katalogen kan begära det här åtkomst paketet. När du definierar en princip för begäran kan du ange enskilda användare eller flera vanliga grupper av användare. Din organisation kanske redan har en grupp, till exempel **alla anställda**.  Om gruppen läggs till i principen för användare som kan begära åtkomst, kan alla medlemmar i gruppen sedan begära åtkomst.

1. I avsnittet **användare som kan begära åtkomst** klickar du på **för användare i din katalog**.

    När du väljer det här alternativet visas nya alternativ för att ytterligare finjustera vem i din katalog som kan begära det här åtkomst paketet.

    ![Åtkomst till paket-begär Anden – för användare i din katalog](./media/entitlement-management-access-package-request-policy/for-users-in-your-directory.png)

1. Välj något av följande alternativ:

    |  |  |
    | --- | --- |
    | **Specifika användare och grupper** | Välj det här alternativet om du vill att endast de användare och grupper i din katalog som du anger ska kunna begära det här åtkomst paketet. |
    | **Alla medlemmar (exklusive gäster)** | Välj det här alternativet om du vill att alla medlems användare i din katalog ska kunna begära det här åtkomst paketet. Det här alternativet omfattar inte gäst användare som du kanske har bjudit in till din katalog. |
    | **Alla användare (inklusive gäster)** | Välj det här alternativet om du vill att alla medlems användare och gäst användare i din katalog ska kunna begära det här åtkomst paketet. |

    Gäst användare refererar till externa användare som har bjudits in till din katalog med [Azure AD B2B](../external-identities/what-is-b2b.md). Mer information om skillnaderna mellan medlems användare och gäst användare finns i [Vad är standard användar behörigheter i Azure Active Directory?](../fundamentals/users-default-permissions.md).

1. Om du har valt **vissa användare och grupper** klickar du på **Lägg till användare och grupper**.

1. I fönstret Välj användare och grupper väljer du de användare och grupper som du vill lägga till.

    ![Åtkomst till paket-begär Anden – Välj användare och grupper](./media/entitlement-management-access-package-request-policy/select-users-groups.png)

1. Klicka på **Välj** för att lägga till användare och grupper.

1. Om du vill kräva godkännande följer du stegen i [ändra godkännande inställningar för ett Access-paket i Azure AD-hantering](entitlement-management-access-package-approval-policy.md) för att konfigurera inställningar för godkännande.

1. Gå till avsnittet [Aktivera begär Anden](#enable-requests) .
 
## <a name="for-users-not-in-your-directory"></a>För användare som inte är i din katalog

 **Användare som inte finns i din katalog** refererar till användare som finns i en annan Azure AD-katalog eller domän. Dessa användare har kanske inte bjudits in till din katalog än. Azure AD-kataloger måste konfigureras för att tillåta inbjudningar i **samarbets begränsningar**. Mer information finns i [Aktivera externt samarbete i B2B och hantera vem som kan bjuda in gäster](../external-identities/delegate-invitations.md).

> [!NOTE]
> Ett gäst användar konto skapas för en användare som ännu inte finns i katalogen vars förfrågan godkänns eller godkänns automatiskt. Gästen kommer att bjudas in, men får ingen inbjudan via e-post. I stället får de ett e-postmeddelande när deras tilldelning av åtkomst paket levereras. Senare när gäst användaren inte längre har några åtkomst paket tilldelningar, eftersom deras senaste tilldelning har gått ut eller avbrutits, kommer gäst användar kontot att blockeras från att logga in och sedan tas bort. Om du vill att gäst användare ska finnas kvar i din katalog på obestämd tid, även om de inte har några åtkomst paket, kan du ändra inställningarna för konfiguration av rättighets hantering. Mer information om gäst användar objekt finns i [Egenskaper för en Azure Active Directory B2B-samarbets användare](../external-identities/user-properties.md).

Följ de här stegen om du vill tillåta att användare som inte tillhör din katalog begär det här åtkomst paketet:

1. I avsnittet **användare som kan begära åtkomst** klickar du på **för användare som inte finns i din katalog**.

    När du väljer det här alternativet visas nya alternativ.

    ![Åtkomst till paket-begär Anden – för användare som inte är i din katalog](./media/entitlement-management-access-package-request-policy/for-users-not-in-your-directory.png)

1. Välj något av följande alternativ:

    |  |  |
    | --- | --- |
    | **Vissa anslutna organisationer** | Välj det här alternativet om du vill välja från en lista över organisationer som administratören har lagt till tidigare. Alla användare från de valda organisationerna kan begära det här åtkomst paketet. |
    | **Alla konfigurerade anslutna organisationer** | Välj det här alternativet om alla användare från alla konfigurerade anslutna organisationer kan begära det här åtkomst paketet. Endast användare från konfigurerade anslutna organisationer kan begära åtkomst paket som visas för användare från alla konfigurerade organisationer. |
    | **Alla användare (alla anslutna organisationer och alla nya externa användare)** | Välj det här alternativet om en användare på Internet ska kunna begära det här åtkomst paketet.  Om de inte tillhör en ansluten organisation i din katalog skapas en ansluten organisation automatiskt för dem när de begär paketet. Den automatiskt skapade anslutna organisationen är i ett **föreslaget** tillstånd. Mer information om föreslaget tillstånd finns i [tillstånds egenskaper för anslutna organisationer](entitlement-management-organization.md#state-properties-of-connected-organizations). |

    En ansluten organisation är en extern Azure AD-katalog eller domän som du har en relation med.

1. Om du har valt **vissa anslutna organisationer** klickar du på **Lägg till kataloger** för att välja från en lista över anslutna organisationer som administratören har lagt till tidigare.

1. Skriv namnet eller domän namnet för att söka efter en tidigare ansluten organisation.

    ![Åtkomst till paket-begär Anden – Välj kataloger](./media/entitlement-management-access-package-request-policy/select-directories.png)

    Om den organisation som du vill samar beta med inte finns med i listan kan du be administratören att lägga till den som en ansluten organisation. Mer information finns i [lägga till en ansluten organisation](entitlement-management-organization.md).

1. När du har valt alla anslutna organisationer klickar du på **Välj**.

    > [!NOTE]
    > Alla användare från de valda anslutna organisationerna kommer att kunna begära det här åtkomst paketet. Detta inkluderar användare i Azure AD från alla under domäner som är kopplade till organisationen, om inte dessa domäner blockeras av listan Tillåt eller neka i Azure B2B. Mer information finns i [tillåta eller blockera inbjudningar till B2B-användare från vissa organisationer](../external-identities/allow-deny-list.md).

1. Om du vill kräva godkännande följer du stegen i [ändra godkännande inställningar för ett Access-paket i Azure AD-hantering](entitlement-management-access-package-approval-policy.md) för att konfigurera inställningar för godkännande.
 
1. Gå till avsnittet [Aktivera begär Anden](#enable-requests) .

## <a name="none-administrator-direct-assignments-only"></a>Ingen (endast administratörs direkta tilldelningar)

Följ de här stegen om du vill kringgå åtkomst begär Anden och låta administratörer direkt tilldela vissa användare till det här Access-paketet. Användarna behöver inte begära åtkomst paketet. Du kan fortfarande ange livs cykel inställningar, men det finns inga inställningar för begäran.

1. I avsnittet **användare som kan begära åtkomst** klickar du på **Ingen (endast administratörs direkta tilldelningar**).

    ![Åtkomst till paket-begär Anden-inga enbart administratörs direkta tilldelningar](./media/entitlement-management-access-package-request-policy/none-admin-direct-assignments-only.png)

    När du har skapat åtkomst paketet kan du direkt tilldela vissa interna och externa användare till Access-paketet. Om du anger en extern användare så skapas ett gäst användar konto i din katalog. Information om hur du tilldelar en användare direkt, se [Visa, lägga till och ta bort tilldelningar för ett Access-paket](entitlement-management-access-package-assignments.md).

1. Hoppa till avsnittet [Aktivera begär Anden](#enable-requests) .


## <a name="open-and-edit-an-existing-policy-of-request-settings"></a>Öppna och redigera en befintlig princip för begär ande inställningar

Om du vill ändra inställningarna för begär ande och godkännande för ett Access-paket måste du öppna motsvarande princip. Följ de här stegen för att öppna och redigera inställningarna för begäran för ett Access-paket:

**Nödvändig roll:** Global administratör, användar administratör, katalog ägare eller åtkomst till paket hanteraren

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. I den vänstra menyn klickar du på **åtkomst paket** och öppnar sedan Access-paketet.

1. Klicka på **principer** och sedan på den princip som du vill redigera.

    Fönstret princip information visas längst ned på sidan.

    ![Åtkomst paket – princip informations fönstret](./media/entitlement-management-shared/policy-details.png)

1. Redigera principen genom att klicka på **Redigera** .

    ![Åtkomst paket – redigera princip](./media/entitlement-management-shared/policy-edit.png)

1. Klicka på fliken **förfrågningar** för att öppna inställningarna för begäran.

1. Använd stegen i föregående avsnitt för att ändra inställningarna för begäran efter behov.

1. Gå till avsnittet [Aktivera begär Anden](#enable-requests) .

## <a name="enable-requests"></a>Aktivera begär Anden

1. Om du vill att Access-paketet ska göras omedelbart tillgängligt för användare i förfrågnings principen att begära, flytta aktivera växla till **Ja**.

    Du kan alltid aktivera det i framtiden när du är klar med att skapa åtkomst paketet.

    Om du har valt **Ingen (endast administratörers direkta tilldelningar)** och du anger Tillåt till **Nej** kan administratörer inte tilldela det här åtkomst paketet direkt.

    ![Åtkomst paket-princip-aktivera princip inställning](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Klicka på **Nästa**.

1. Om du vill kräva att beställare tillhandahåller ytterligare information när du begär åtkomst till ett Access-paket kan du använda stegen i [ändra godkännande-och beställarens information (för hands version) inställningar för ett Access-paket i Azure AD-hantering](entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview) för att konfigurera information om beställaren (för hands version).

1. Konfigurera livs cykel inställningar.

1. Om du redigerar en princip klickar du på **Uppdatera**. Om du lägger till en ny princip klickar du på **skapa**.

## <a name="next-steps"></a>Nästa steg

- [Ändra godkännande inställningarna för ett Access-paket](entitlement-management-access-package-approval-policy.md)
- [Ändra livs cykel inställningar för ett Access-paket](entitlement-management-access-package-lifecycle-policy.md)
- [Visa begär Anden för ett Access-paket](entitlement-management-access-package-requests.md)
