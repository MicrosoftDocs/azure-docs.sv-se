---
title: Styra åtkomsten för externa användare i Azure AD-hantering av rättigheter – Azure Active Directory
description: Lär dig mer om de inställningar som du kan ange för att styra åtkomsten för externa användare i Azure Active Directory rättighets hantering.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 12/23/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: c7c78dcbc34deca769739f82964df41ebfc596ea
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176798"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management"></a>Styra åtkomsten för externa användare i hantering av Azure AD-rättigheter

Hantering av Azure AD-rättigheterna använder [Azure AD Business-to-Business (B2B)](../external-identities/what-is-b2b.md) för att samar beta med personer utanför organisationen i en annan katalog. Med Azure AD B2B autentiserar externa användare till sin arbets katalog, men har en representation i din katalog. Representationen i din katalog gör det möjligt för användaren att tilldelas åtkomst till dina resurser.

I den här artikeln beskrivs de inställningar som du kan ange för att styra åtkomsten för externa användare.

## <a name="how-entitlement-management-can-help"></a>Så här kan hantering av rättigheter hjälpa

När du använder [Azure AD B2B](../external-identities/what-is-b2b.md) -inbjudan måste du redan känna till e-postadresserna till de externa gäst användare som du vill ta med i resurs katalogen och arbeta med. Detta fungerar bra när du arbetar med ett mindre eller kortsiktigt projekt och du redan känner till alla deltagare, men det är svårare att hantera om du har många användare som du vill arbeta med eller om deltagarna ändras med tiden.  Till exempel kanske du arbetar med en annan organisation och har en kontakt punkt med den organisationen, men över tiden kommer ytterligare användare från organisationen också att ha åtkomst.

Med rättighets hantering kan du definiera en princip som gör det möjligt för användare från organisationer som du anger att kunna begära ett åtkomst paket själv. Du kan ange om godkännande krävs och ett förfallo datum för åtkomsten. Om godkännande krävs kan du också bjuda in en eller flera användare från den externa organisationen till din katalog och ange dem som god kännare – eftersom de förmodligen vet vilka externa användare från deras organisation som behöver åtkomst. När du har konfigurerat åtkomst paketet kan du skicka åtkomst paketets länk till din kontakt person (sponsor) i den externa organisationen. Den kontakten kan dela med andra användare i den externa organisationen, och de kan använda den här länken för att begära åtkomst paketet. Användare från den organisationen som redan har bjudits in till din katalog kan också använda den länken.

När en begäran godkänns, etablerar rättighets hantering användaren med nödvändig åtkomst, vilket kan inkludera inbjudan till användaren om de inte redan finns i katalogen. Azure AD skapar automatiskt ett B2B-gäst konto för dem. Observera att en administratör kan ha tidigare begränsat vilka organisationer som tillåts för samarbete genom att ställa in en [B2B-lista med eller neka](../external-identities/allow-deny-list.md) för att tillåta eller blockera inbjudningar till andra organisationer.  Om användaren inte tillåts av listan Tillåt eller blockera kommer de inte att bjudas in.

Eftersom du inte vill att den externa användarens åtkomst till senaste oändligt, anger du ett förfallo datum i principen, till exempel 180 dagar. Efter 180 dagar tar rättighets hanteringen bort all åtkomst som är associerad med det åtkomst paketet, om deras åtkomst inte är utökad. Om användaren som har bjudits in via rättighets hantering inte har några andra åtkomst paket tilldelningar, kommer deras gäst konto att blockeras från att logga in i 30 dagar och sedan tas bort. Detta förhindrar spridning av onödiga konton. Dessa inställningar kan konfigureras enligt beskrivningen i följande avsnitt.

## <a name="how-access-works-for-external-users"></a>Hur åtkomst fungerar för externa användare

Följande diagram och steg ger en översikt över hur externa användare beviljas åtkomst till ett Access-paket.

![Diagram över livs cykeln för externa användare](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. Du [lägger till en ansluten organisation](entitlement-management-organization.md) för den Azure AD-katalog eller domän som du vill samar beta med.

1. Du skapar ett Access-paket i din katalog som innehåller en princip [för användare som inte finns i din katalog](entitlement-management-access-package-create.md#for-users-not-in-your-directory).

1. Du skickar en [länk till min åtkomst Portal](entitlement-management-access-package-settings.md) till din kontakt i den externa organisationen som de kan dela med sina användare för att begära åtkomst paketet.

1. En extern användare (**begär ande A** i det här exemplet) använder länken min åtkomst Portal för att [begära åtkomst](entitlement-management-request-access.md) till Access-paketet. Hur användaren loggar in beror på vilken typ av katalog eller domän som definierats i den anslutna organisationen.

1. En god kännare [godkänner begäran](entitlement-management-request-approve.md) (eller också godkänns begäran automatiskt).

1. Begäran försätts i [leverans tillstånd](entitlement-management-process.md).

1. Med hjälp av B2B-ansvars processen skapas ett gäst användar konto i din katalog (**begär ande a (gäst)** i det här exemplet). Om en lista [över tillåtna eller en neka-lista](../external-identities/allow-deny-list.md) har definierats tillämpas List inställningen.

1. Gäst användaren tilldelas åtkomst till alla resurser i åtkomst paketet. Det kan ta lite tid innan ändringar görs i Azure AD och till andra Microsoft Online Services eller anslutna SaaS-program. Mer information finns i [när ändringar tillämpas](entitlement-management-access-package-resources.md#when-changes-are-applied).

1. Den externa användaren får ett e-postmeddelande som anger att deras åtkomst har [levererats](entitlement-management-process.md).

1. För att få åtkomst till resurserna kan den externa användaren antingen klicka på länken i e-postmeddelandet eller försöka komma åt någon av katalog resurserna direkt för att slutföra Inbjudnings processen.

1. Beroende på princip inställningarna, som tid steg, går det att tilldela åtkomst paketet för den externa användaren och den externa användarens åtkomst tas bort.

1. Beroende på inställningarna för den externa användarens livs cykel, kommer den externa användaren att blockeras från att logga in och gäst användar kontot tas bort från din katalog när den externa användaren inte längre har några åtkomst paket tilldelningar.

## <a name="settings-for-external-users"></a>Inställningar för externa användare

För att säkerställa att personer utanför organisationen kan begära åtkomst paket och få åtkomst till resurserna i dessa åtkomst paket, finns det vissa inställningar som du bör kontrol lera korrekt.

### <a name="enable-catalog-for-external-users"></a>Aktivera katalog för externa användare

- När du skapar en [ny katalog](entitlement-management-catalog-create.md)är den som standard aktive rad så att externa användare kan begära åtkomst paket i katalogen. Se till att **Aktivera för externa användare** har angetts till **Ja**.

    ![Redigera katalog inställningar](./media/entitlement-management-shared/catalog-edit.png)

### <a name="configure-your-azure-ad-b2b-external-collaboration-settings"></a>Konfigurera dina externa samarbets inställningar för Azure AD B2B

- Genom att tillåta gäster att bjuda in andra gäster till katalogen innebär det att gäst inbjudningar kan ske utanför hantering av rättigheter. Vi rekommenderar att du ställer in **gäster kan bjuda in** till **Nej** för att endast tillåta korrekt reglerade inbjudningar.
- Om du använder listan B2B-Tillåt måste du kontrol lera att alla domäner som du vill använda som partner med hjälp av hantering av rättigheter läggs till i listan. Alternativt, om du använder listan B2B-neka, måste du se till att alla domäner som du vill partner med inte läggs till i listan.
- Om du skapar en rättighets hanterings princip för **alla användare** (alla anslutna organisationer och alla nya externa användare) och en användare inte tillhör en ansluten organisation i din katalog, skapas en ansluten organisation automatiskt för dem när de begär paketet. Alla inställningar för B2B-tillåta eller neka lista som du har prioriteras. Se därför till att inkludera de domäner som du vill inkludera i den här principen i listan över tillåtna om du använder en, och exkluderar dem från listan över nekade om du använder en neka-lista.
- Om du vill skapa en princip för hantering av rättigheter som innehåller **alla användare** (alla anslutna organisationer och nya externa användare) måste du först aktivera e-postautentisering med eng ång slö sen ord för din katalog. Mer information finns i [e-mail Authentication eng ång slö sen ord](../external-identities/one-time-passcode.md).
- Mer information om inställningar för externa samarbets funktioner i Azure AD B2B finns i [Aktivera externt samarbete i B2B och hantera vem som kan bjuda in gäster](../external-identities/delegate-invitations.md).

    ![Externa samarbets inställningar för Azure AD](./media/entitlement-management-external-users/collaboration-settings.png)

### <a name="review-your-conditional-access-policies"></a>Granska dina principer för villkorlig åtkomst

- Se till att undanta gäster från alla principer för villkorlig åtkomst som nya gäst användare inte kan uppfylla eftersom det gör att de inte kan logga in i din katalog. Gäster har till exempel troligt vis ingen registrerad enhet, inte på en känd plats och vill inte registrera sig för Multi-Factor Authentication (MFA), så att du lägger till dessa krav i en princip för villkorlig åtkomst blockerar gäster från att använda rättighets hantering. Mer information finns i [Vad är villkor i Azure Active Directory villkorlig åtkomst?](../conditional-access/concept-conditional-access-conditions.md).

    ![Undantags inställningar för villkorlig åtkomst policy för Azure AD](./media/entitlement-management-external-users/conditional-access-exclude.png)

### <a name="review-your-sharepoint-online-external-sharing-settings"></a>Granska dina inställningar för extern SharePoint Online-delning

- Om du vill inkludera SharePoint Online-webbplatser i dina åtkomst paket för externa användare kontrollerar du att inställningen för extern delning på organisations nivå har angetts till **vem som helst** (användarna inte behöver logga in) eller **nya och befintliga gäster** (gäster måste logga in eller ange en verifierings kod). Mer information finns i [Aktivera eller inaktivera extern delning](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Om du vill begränsa en extern delning utanför hantering av rättigheter kan du ange inställningen för extern delning till **befintliga gäster**. Sedan kommer endast nya användare som bjuds in via rättighets hantering att kunna få åtkomst till dessa webbplatser. Mer information finns i [Aktivera eller inaktivera extern delning](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Se till att inställningarna för webbplats nivå aktiverar gäst åtkomst (samma alternativ för val som tidigare i listan). Mer information finns i [Aktivera och inaktivera extern delning på en plats](/sharepoint/change-external-sharing-site).

### <a name="review-your-microsoft-365-group-sharing-settings"></a>Granska inställningarna för delning av Microsoft 365 grupp

- Om du vill inkludera Microsoft 365 grupper i dina åtkomst paket för externa användare ser du till att **Låt användarna lägga till nya gäster till organisationen** är inställda på **på** för att tillåta gäst åtkomst. Mer information finns i [Hantera gäst åtkomst till Microsoft 365 grupper](/Microsoft 365/admin/create-groups/manage-guest-access-in-groups?view=Microsoft 365-worldwide#manage-groups-guest-access).

- Om du vill att externa användare ska kunna komma åt SharePoint Online-webbplatsen och resurser som är associerade med en Microsoft 365 grupp, se till att aktivera extern SharePoint Online-delning. Mer information finns i [Aktivera eller inaktivera extern delning](/sharepoint/turn-external-sharing-on-or-off#change-the-organization-level-external-sharing-setting).

- Information om hur du anger gäst principen för Microsoft 365 grupper på katalog nivå i PowerShell finns i [exempel: Konfigurera gäst princip för grupper på katalog nivå](../enterprise-users/groups-settings-cmdlets.md#example-configure-guest-policy-for-groups-at-the-directory-level).

### <a name="review-your-teams-sharing-settings"></a>Granska dina team delnings inställningar

- Om du vill inkludera team i dina åtkomst paket för externa användare ser du till att **Tillåt gäst åtkomst i Microsoft Teams** är **aktiverat** för att tillåta gäst åtkomst. Mer information finns i [Konfigurera gäst åtkomst i administrations Center för Microsoft Teams](/microsoftteams/set-up-guests#configure-guest-access-in-the-teams-admin-center).

## <a name="manage-the-lifecycle-of-external-users"></a>Hantera livs cykeln för externa användare

Du kan välja vad som händer när en extern användare, som har bjudits in till din katalog via en begäran om åtkomst paket, inte längre ha några åtkomst paket tilldelningar. Detta kan inträffa om användaren låser alla tilldelningar av åtkomst paket eller om den senaste tilldelningen av åtkomst paket upphör att gälla. När en extern användare inte längre har några åtkomst paket tilldelningar blockeras som standard från att logga in till din katalog. Efter 30 dagar tas deras gäst användar konto bort från katalogen.

**Nödvändig roll:** Global administratör eller användar administratör

1. Klicka på **Azure Active Directory** i Azure Portal och klicka sedan på **identitets styrning**.

1. På den vänstra menyn i avsnittet **rättighets hantering** klickar du på **Inställningar**.

1. Klicka på **Redigera**.

    ![Inställningar för att hantera livs cykeln för externa användare](./media/entitlement-management-external-users/settings-external-users.png)

1. I avsnittet **Hantera livs cykel för externa användare** väljer du de olika inställningarna för externa användare.

1. När en extern användare förlorar sin senaste tilldelning till några åtkomst paket, och om du vill blockera dem från att logga in till den här katalogen, ställer du in den **blockerande externa användaren från att logga in på den här katalogen** till **Ja**.

    > [!NOTE]
    > Om en användare är blockerad från att logga in till den här katalogen, kommer användaren inte att kunna begära åtkomst paketet igen eller begära ytterligare åtkomst i den här katalogen. Konfigurera inte blockering av dem från att logga in om de senare behöver begära åtkomst till andra åtkomst paket.

1. När en extern användare förlorar sin senaste tilldelning till några åtkomst paket, om du vill ta bort deras gäst användar konto i den här katalogen, anger du **ta bort extern användare** till **Ja**.

    > [!NOTE]
    > Hantering av rättigheter tar bara bort konton som har bjudits in via rättighets hantering. Observera också att en användare kommer att blockeras från att logga in och tas bort från den här katalogen även om användaren har lagts till i resurser i den här katalogen som inte har åtkomst till paket tilldelningar. Om gästen fanns i den här katalogen innan du får åtkomst till paket tilldelningar, kommer de att vara kvar. Men om gästen har bjudits in via en tilldelning av Access-paket och efter att ha bjudits in till en OneDrive för företag-eller SharePoint Online-webbplats, kommer de fortfarande att tas bort.

1. Om du vill ta bort gäst användar kontot i den här katalogen kan du ange antalet dagar innan det tas bort. Om du vill ta bort gäst användar kontot så fort de förlorar sin senaste tilldelning till alla åtkomst paket, anger du **antalet dagar innan externa användare tas bort från katalogen** till **0**.

1. Klicka på **Spara**.

## <a name="next-steps"></a>Nästa steg

- [Lägga till en ansluten organisation](entitlement-management-organization.md)
- [För användare som inte är i din katalog](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
- [Felsöka](entitlement-management-troubleshoot.md)
