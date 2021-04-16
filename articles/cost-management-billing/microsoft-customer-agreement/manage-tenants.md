---
title: Hantera klienter i ditt Microsoft-kundavtal faktureringskonto – Azure
description: Artikeln hjälper dig att förstå och hantera klienter som är associerade med ditt Microsoft-kundavtal faktureringskonto.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 04/06/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: dc34d0f12430838be29897ccc5cbeee382ecaa2b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493124"
---
# <a name="manage-tenants-in-your-microsoft-customer-agreement-billing-account"></a>Hantera klienter i ditt Microsoft-kundavtal faktureringskonto

Artikeln hjälper dig att förstå och hantera klienter som är associerade med ditt Microsoft-kundavtal faktureringskonto. Använd informationen för att hantera klienter, överföra prenumerationer och administrera faktureringsägarskap samtidigt som du säkerställer säker åtkomst till din faktureringsmiljö.

## <a name="whats-a-tenant"></a>Vad är en klient?

En klientorganisation är en digital representation av din organisation och är främst associerad med en domän, till exempel Microsoft.com. Det är en miljö som hanteras via Azure Active Directory som gör att du kan tilldela användare behörighet att hantera Azure-resurser och fakturering.

Varje klientorganisation är separat och separat från andra klienter, men du kan tillåta gästanvändare från andra klienter att komma åt din klientorganisation för att spåra dina kostnader och hantera fakturering.

## <a name="how-tenants-and-subscriptions-relate-to-billing-account"></a>Hur klienter och prenumerationer relaterar till faktureringskontot

Du använder din Microsoft-kundavtal (faktureringskonto) för att spåra kostnader och hantera fakturering. Varje faktureringskonto har minst en faktureringsprofil. Med faktureringsprofilen kan du hantera din faktura och betalningsmetod. Varje faktureringsprofil innehåller som standard ett fakturaavsnitt. Du kan skapa fler fakturaavsnitt för att gruppera, spåra och hantera kostnader på en mer detaljerad nivå om det behövs.

- Ditt faktureringskonto är associerat med en enda klientorganisation. Det innebär att endast användare som är en del av klientorganisationen kan komma åt ditt faktureringskonto.
- När du skapar en ny Azure-prenumeration för ditt faktureringskonto skapas den alltid i din faktureringskontoklientorganisation. Du kan dock flytta prenumerationer till andra klienter. Du kan också länka befintliga prenumerationer från andra klienter till ditt faktureringskonto. Det gör att du centralt kan hantera fakturering via en klientorganisation samtidigt som du behåller resurser och prenumerationer i andra klienter baserat på dina behov.

Följande diagram visar hur faktureringskonto och prenumerationer är länkade till klienter. Contosos MCA-faktureringskonto är associerat med Klient 1 medan Contosos PAYG-konto är associerat med Klient 2. Anta att Contoso vill betala för sin PAYG-prenumeration via sitt MCA-faktureringskonto. De kan använda en överföring av faktureringsägarskap för att länka prenumerationen till sitt MCA-faktureringskonto. Prenumerationen och dess resurser kommer fortfarande att associeras med klient 2, men de betalas för med hjälp av MCA-faktureringskontot.

:::image type="content" source="./media/manage-tenants/billing-hierarchy-example.png" alt-text="Diagram som visar ett exempel på en faktureringshierarki." border="false" lightbox="./media/manage-tenants/billing-hierarchy-example.png":::

## <a name="manage-subscriptions-under-multiple-tenants-in-a-single-microsoft-customer-agreement"></a>Hantera prenumerationer under flera klienter i en enda Microsoft-kundavtal

Faktureringsägare kan skapa prenumerationer när de har [rätt behörighet](../manage/understand-mca-roles.md#subscription-billing-roles-and-tasks) till faktureringskontot. Som standard finns alla nya prenumerationer som skapats under Microsoft-kundavtal i den Microsoft-kundavtal klientorganisationen.

- Du kan länka prenumerationer från andra klienter till ditt Microsoft-kundavtal faktureringskonto. Faktureringsägarskapet för en prenumeration ändras bara faktureringen. Det påverkar inte tjänstklientorganisationen eller Azure RBAC-roller.
- Om du vill ändra prenumerationsägaren i tjänstklientorganisationen måste du överföra [prenumerationen till en annan Azure Active Directory katalog](../../role-based-access-control/transfer-subscription.md).

## <a name="add-guest-users-to-your-microsoft-customer-agreement-tenant"></a>Lägga till gästanvändare i din Microsoft-kundavtal klientorganisation

Användare som läggs till i din Microsoft-kundavtal faktureringsklient måste bjudas in som gäst för att hantera faktureringsansvar från en annan klientorganisation.

Om du vill bjuda in någon som gäst måste användaren ha en befintlig e-postadress med en domän som skiljer sig från din Azure Active Directory (AD)-domän. Azure AD skickar gästanvändaren ett e-postmeddelande med en länk för autentisering.

:::image type="content" source="./media/manage-tenants/guest-invitation-email.png" alt-text="Skärmbild som visar ett exempel på en e-postinbjudan." lightbox="./media/manage-tenants/guest-invitation-email.png" :::

När en användare läggs till i Microsoft-kundavtal klientorganisationen måste användaren [acceptera inbjudan](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation).

När de väljer **länken Godkänn** inbjudan uppmanas de att autentisera sig med Azure.

:::image type="content" source="./media/manage-tenants/authenticate.png" alt-text="Skärmbild som visar en uppmaning om att autentisera med Azure." lightbox="./media/manage-tenants/authenticate.png" :::

Sedan väljer de **Acceptera**.

:::image type="content" source="./media/manage-tenants/accept-invitation.png" alt-text="Skärmbild som visar en uppmaning om att acceptera inbjudan." lightbox="./media/manage-tenants/accept-invitation.png" :::

När de har accepterat kan [de visa Microsoft-kundavtal faktureringskontot under Cost Management + Billing](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement).

:::image type="content" source="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" alt-text="Skärmbild som visar Microsoft-kundavtal i listan över faktureringskonton." lightbox="./media/manage-tenants/billing-microsoft-customer-agreement-in-list.png" :::

Behörighet att bjuda in gästanvändare styrs av dina Azure AD-inställningar. Värdet för inställningarna visas under Inställningar **på** sidan **Organisationsrelationer.** Kontrollera att inställningen är markerad, annars misslyckas inbjudan. Mer information finns i Begränsa [åtkomstbehörigheter för gästanvändare.](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)

:::image type="content" source="./media/manage-tenants/external-collaboration-settings.png" alt-text="Skärmbild som visar inställningar för externt samarbete." lightbox="./media/manage-tenants/external-collaboration-settings.png" :::

> [!IMPORTANT]
> Gästanvändare får åtkomst till Microsoft-kundavtal klientorganisationen, vilket kan utgöra ett äkerhetsåtgärder. Mer information finns i Lär [dig hur du begränsar gästanvändares standardbehörigheter.](../../active-directory/fundamentals/users-default-permissions.md#restrict-member-users-default-permissions)

## <a name="manage-multiple-microsoft-cloud-services-under-an-azure-ad-tenant"></a>Hantera flera Microsoft-molntjänster under en Azure AD-klientorganisation

Du kan hantera flera molntjänster för din organisation under en enda Azure AD-klientorganisation. Användarkonton för alla Microsofts molnerbjudanden lagras i Azure AD-klientorganisationen, som innehåller användarkonton och grupper. Följande diagram visar ett exempel på en organisation med flera tjänster som använder en gemensam Azure AD-klientorganisation som innehåller konton. Varje tjänst har en egen portal, i blå text, där användarna hanterar sina tjänster.

:::image type="content" source="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png" alt-text="Diagram som visar ett exempel på en organisation med flera tjänster som använder en gemensam Azure AD-klientorganisation som innehåller konton." border="false" lightbox="./media/manage-tenants/diagram-multiple-services-common-azure-ad-tenant-accounts.png":::

## <a name="next-steps"></a>Nästa steg

Läs följande artiklar om du vill lära dig hur du administrerar flexibel faktureringsägarskap och säkerställer säker åtkomst till Microsoft-kundavtal.

- [Så här ställer du in en klientorganisation](../../active-directory/develop/quickstart-create-new-tenant.md)
- [Inbyggda roller i Azure](../../role-based-access-control/built-in-roles.md)
- [Överföra en Azure-prenumeration till en annan Azure AD-katalog](../../role-based-access-control/transfer-subscription.md)
- [Begränsa behörigheter för gäståtkomst (förhandsversion) i Azure Active Directory](../../active-directory/enterprise-users/users-restrict-guest-permissions.md)
- [Lägg till gästanvändare i din katalog i Azure Portal](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Vilka är standardanvändarbehörigheterna i Azure Active Directory?](../../active-directory/external-identities/b2b-quickstart-add-guest-users-portal.md#accept-the-invitation)
- [Vad är Azure Active Directory?](../../active-directory/fundamentals/active-directory-whatis.md)