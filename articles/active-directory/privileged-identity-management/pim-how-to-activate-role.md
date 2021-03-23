---
title: Aktivera mina Azure AD-roller i PIM-Azure Active Directory | Microsoft Docs
description: Lär dig hur du aktiverar Azure AD-roller i Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 03/22/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa9861dd1ffbdd33f59abe76c7ebbd1befe3e7a6
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799436"
---
# <a name="activate-my-azure-ad-roles-in-pim"></a>Aktivera mina Azure AD-roller i PIM

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) gör det enklare för företag att hantera privilegie rad åtkomst till resurser i Azure AD och andra Microsoft-onlinetjänster som Microsoft 365 eller Microsoft Intune.  

Om du har blivit *berättigad* till en administrativ roll måste du *Aktivera* roll tilldelningen när du behöver utföra privilegierade åtgärder. Om du till exempel ibland hanterar Microsoft 365 funktioner kanske din organisations privilegierade roll administratörer inte gör en permanent global administratör, eftersom rollen även påverkar andra tjänster. De gör i stället att du är behörig för Azure AD-roller som Exchange Online-administratör. Du kan begära att aktivera rollen när du behöver dess behörigheter och sedan har du administratörs behörighet för en fördefinierad tids period.

Den här artikeln är för administratörer som behöver aktivera sin Azure AD-roll i Privileged Identity Management.

## <a name="determine-your-version-of-pim"></a>Fastställ din version av PIM

Från och med november 2019 uppdateras Azure AD-rollerna i Privileged Identity Management till en ny version som matchar upplevelserna för Azures resurs roller. Detta skapar ytterligare funktioner samt [ändringar i det befintliga API: et](azure-ad-roles-features.md#api-changes). Även om den nya versionen publiceras, vilka procedurer som du följer i den här artikeln beror på vilken version av Privileged Identity Management du för närvarande har. Följ stegen i det här avsnittet för att ta reda på vilken version av Privileged Identity Management du har. När du känner till din version av Privileged Identity Management kan du välja de procedurer i den här artikeln som matchar den versionen.

1. Logga in på [Azure Portal](https://portal.azure.com/) med rollen [privilegie rad roll administratör](../roles/permissions-reference.md#privileged-role-administrator) .
1. Öppna **Azure AD Privileged Identity Management**. Om du har en banderoll överst på sidan Översikt, följer du anvisningarna på fliken **ny version** i den här artikeln. Annars följer du anvisningarna på fliken **tidigare version** .

    [![Välj Azure AD-> Privileged Identity Management.](media/pim-how-to-add-role-to-user/pim-new-version.png)](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox)

# <a name="new-version"></a>[Ny version](#tab/new)

## <a name="activate-a-role-for-new-version"></a>Aktivera en roll för ny version

När du behöver anta en Azure AD-roll kan du begära aktivering genom att öppna **Mina roller** i Privileged Identity Management.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**. Information om hur du lägger till panelen Privileged Identity Management på instrument panelen finns i [börja använda Privileged Identity Management](pim-getting-started.md).

1. Välj **Mina roller** och välj sedan **Azure AD-roller** för att se en lista över dina kvalificerade Azure AD-roller.

    ![Sidan mina roller med roller som du kan aktivera](./media/pim-how-to-activate-role/my-roles.png)

1. Leta upp den roll som du vill aktivera i listan **Azure AD-roller** .

    ![Azure AD-roller – listan Mina berättigade roller](./media/pim-how-to-activate-role/activate-link.png)

1. Välj **Aktivera** för att öppna fönstret aktivera.

    ![Azure AD-roller – aktiverings sidan innehåller varaktighet och omfång](./media/pim-how-to-activate-role/activate-page.png)

1. Välj **Ytterligare verifiering som krävs**"* * och följ anvisningarna för att utföra ytterligare säkerhets verifiering. Du måste bara autentisera en gång per session.

    ![Skärm för att tillhandahålla säkerhets verifiering, till exempel en PIN-kod](./media/pim-resource-roles-activate-your-roles/resources-mfa-enter-code.png)

1. När du har Multi-Factor Authentication väljer **du aktivera innan du fortsätter**.

    ![Verifiera min identitet med MFA innan du aktiverar rollen](./media/pim-how-to-activate-role/activate-role-mfa-banner.png)

1. Om du vill ange ett reducerat omfång väljer du **omfång** för att öppna filter fönstret. I fönstret filter kan du ange de Azure AD-resurser som du behöver åtkomst till. Det är en bra idé att begära åtkomst till de minst resurser som du behöver.

1. Vid behov kan du ange en start tid för anpassad aktivering. Azure AD-rollen aktive ras efter den valda tiden.

1. Ange orsaken till aktiverings förfrågan i rutan **orsak** .

1. Välj **Aktivera**.

    Om [rollen kräver godkännande](pim-resource-roles-approval-workflow.md) för att aktive ras visas ett meddelande i det övre högra hörnet i webbläsaren som informerar dig om att begäran väntar på godkännande.

    ![Begäran om aktivering väntar på godkännande](./media/pim-resource-roles-activate-your-roles/resources-my-roles-activate-notification.png)

## <a name="view-the-status-of-your-requests-for-new-version"></a>Visa status för dina begär Anden för ny version

Du kan visa statusen för väntande begär Anden att aktivera.

1. Öppna Azure AD Privileged Identity Management.

1. Välj **Mina begär Anden** om du vill se en lista över dina Azure AD-roller och Azure-resurs roll förfrågningar.

    ![Mina begär Anden – Azure AD-sida som visar dina väntande förfrågningar](./media/pim-how-to-activate-role/my-requests-page.png)

1. Bläddra till höger för att visa kolumnen **status för begäran** .

## <a name="cancel-a-pending-request-for-new-version"></a>Avbryt en väntande begäran om ny version

Om du inte behöver aktivera en roll som kräver godkännande kan du när som helst avbryta en väntande begäran.

1. Öppna Azure AD Privileged Identity Management.

1. Välj **Mina begär Anden**.

1. För den roll som du vill avbryta väljer du länken **Avbryt** .

    När du väljer Avbryt avbryts begäran. Om du vill aktivera rollen igen måste du skicka in en ny begäran om aktivering.

   ![Listan Mina begär Anden med avbrotts åtgärd markerad](./media/pim-resource-roles-activate-your-roles/resources-my-requests-cancel.png)

## <a name="troubleshoot-for-new-version"></a>Felsöka för ny version

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Behörigheter beviljas inte efter att en roll har aktiverats

När du aktiverar en roll i Privileged Identity Management kan aktiveringen inte genast spridas till alla portaler som kräver den privilegierade rollen. Ibland kan webbcachelagringen i en portal resultera i att ändringen inte börjar gälla omedelbart, även om ändringen har spridits. Om aktiveringen fördröjs loggar du ut från portalen som du försöker utföra åtgärden och loggar sedan in igen. I Azure Portal loggar PIM dig och tillbaka automatiskt.

# <a name="previous-version"></a>[Tidigare version](#tab/previous)

## <a name="activate-a-role-previous-version"></a>Aktivera en roll (tidigare version)

När du behöver utföra en Azure AD-roll kan du begära aktivering genom att använda navigerings alternativet **Mina roller** i Privileged Identity Management.

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Öppna **Azure AD Privileged Identity Management**. Information om hur du lägger till panelen Privileged Identity Management på instrument panelen finns i [börja använda Privileged Identity Management](pim-getting-started.md).

1. Välj **Azure AD-roller**.

1. Välj **Mina roller** om du vill se en lista över dina berättigade Azure AD-roller.

    ![Azure AD-roller – mina roller visar listan med berättigade eller aktiva roller](./media/pim-how-to-activate-role/directory-roles-my-roles.png)

1. Hitta en roll som du vill aktivera.

    ![Azure AD-roller – listan Mina berättigade roller visar aktiverings länken](./media/pim-how-to-activate-role/directory-roles-my-roles-activate.png)

1. Välj **Aktivera** för att öppna fönstret roll aktiverings information.

1. Om din roll kräver Multi-Factor Authentication (MFA), väljer **du verifiera din identitet innan du fortsätter**. Du behöver bara autentisera en gång per session.

    ![Verifiera mitt identitets fönster med MFA innan roll aktivering](./media/pim-how-to-activate-role/directory-roles-my-roles-mfa.png)

1. Välj **verifiera min identitet** och följ instruktionerna för att tillhandahålla ytterligare säkerhets verifiering.

    ![Sidan ytterligare säkerhets verifiering där du tillfrågas om hur du kontaktar dig](./media/pim-how-to-activate-role/additional-security-verification.png)

1. Välj **Aktivera** för att öppna aktiverings fönstret.

    ![Aktiverings fönstret för att ange start tid, varaktighet, biljett och orsak](./media/pim-how-to-activate-role/directory-roles-activate.png)

1. Vid behov kan du ange en start tid för anpassad aktivering.

1. Ange varaktighet för aktivering.

1. I rutan **aktiverings orsak** anger du orsaken till aktiverings förfrågan. Vissa roller kräver att du anger ett fel biljett nummer.

    ![Slutfört aktiverings fönster med anpassad start tid, varaktighet, biljett och orsak](./media/pim-how-to-activate-role/directory-roles-activation-pane.png)

1. Välj **Aktivera**.

    Om rollen inte kräver godkännande visas fönstret **aktiverings status** som visar status för aktiveringen.

    ![Sidan aktiverings status med de tre aktiverings stegen](./media/pim-how-to-activate-role/activation-status.png)

    När alla steg har slutförts väljer du länken **Logga ut** för att logga ut från Azure Portal. När du loggar in på portalen igen kan du nu använda rollen.

    Om [rollen kräver godkännande](./azure-ad-pim-approval-workflow.md) för att aktivera, visas ett Azure-meddelande i det övre högra hörnet i webbläsaren som informerar dig om att begäran väntar på godkännande.

## <a name="view-the-status-of-your-requests-previous-version"></a>Visa status för dina begär Anden (tidigare version)

Du kan visa statusen för väntande begär Anden att aktivera.

1. Öppna Azure AD Privileged Identity Management.

1. Välj **Azure AD-roller**.

1. Välj **Mina begär Anden** om du vill se en lista över dina begär Anden.

    ![Azure AD-roller – listan Mina begär Anden](./media/pim-how-to-activate-role/directory-roles-my-requests.png)

## <a name="deactivate-a-role-previous-version"></a>Inaktivera en roll (tidigare version)

När en roll har Aktiver ATS inaktive ras den automatiskt när tids gränsen (den kvalificerade varaktigheten) har nåtts.

Om du slutför dina administratörs aktiviteter tidigt kan du också inaktivera en roll manuellt i Azure AD Privileged Identity Management.

1. Öppna Azure AD Privileged Identity Management.

1. Välj **Azure AD-roller**.

1. Välj **Mina roller**.

1. Välj **aktiva roller** om du vill se en lista över aktiva roller.

1. Hitta den roll du är klar med och välj sedan **inaktivera**.

## <a name="cancel-a-pending-request-previous-version"></a>Avbryt en väntande begäran (tidigare version)

Om du inte behöver aktivera en roll som kräver godkännande kan du när som helst avbryta en väntande begäran.

1. Öppna Azure AD Privileged Identity Management.

1. Välj **Azure AD-roller**.

1. Välj **Mina begär Anden**.

1. För den roll som du vill avbryta väljer du knappen **Avbryt** .

    När du väljer **Avbryt** avbryts begäran. Om du vill aktivera rollen igen måste du skicka in en ny begäran om aktivering.

   ![Listan Mina begär Anden med knappen Avbryt markerad](./media/pim-how-to-activate-role/directory-role-cancel.png)

## <a name="troubleshoot-previous-version"></a>Felsök (tidigare version)

### <a name="permissions-are-not-granted-after-activating-a-role"></a>Behörigheter beviljas inte efter att en roll har aktiverats

När du aktiverar en roll i Privileged Identity Management kan aktiveringen fördröjas i administrations portarna förutom Azure Portal, till exempel Office 365-portalen. Om aktiveringen är fördröjd loggar du ut från portalen som du är i och loggar sedan in igen. Använd Privileged Identity Management för att kontrol lera att du visas som medlem i rollen.

 ---

## <a name="next-steps"></a>Nästa steg

- [Aktivera mina Azure AD-roller i Privileged Identity Management](pim-how-to-activate-role.md)
