---
title: Lägg till B2B-samarbets användare som en informations arbetare – Azure AD
description: B2B-samarbete gör att informations arbetare och app-ägare kan lägga till gäst användare i Azure AD för åtkomst | Microsoft Docs
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 12/19/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 345cbe2efb644fe2b84d4a6021ff962337e29285
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219920"
---
# <a name="how-users-in-your-organization-can-invite-guest-users-to-an-app"></a>Hur användare i din organisation kan bjuda in gäst användare till en app

När en gäst användare har lagts till i katalogen i Azure AD kan en program ägare skicka gäst användaren en direkt länk till den app som de vill dela. Azure AD-administratörer kan också konfigurera självbetjänings hantering för Galleri-eller SAML-baserade appar i sin Azure AD-klient. På så sätt kan program ägare hantera sina egna gäst användare, även om gäst användarna inte har lagts till i katalogen ännu. När en app har kon figurer ATS för självbetjäning använder program ägaren sin åtkomst panel för att bjuda in en gäst användare till en app eller lägga till en gäst användare i en grupp som har åtkomst till appen. Program hantering via självbetjäning för Galleri-och SAML-baserade appar kräver en del inledande konfiguration av en administratör. Följande är en sammanfattning av installations stegen (mer information finns i [krav](#prerequisites) senare på den här sidan):

 - Aktivera självbetjänings grupp hantering för din klient organisation
 - Skapa en grupp som ska tilldelas appen och ge användaren en ägare
 - Konfigurera appen för självbetjäning och tilldela gruppen till appen

> [!NOTE]
> * Den här artikeln beskriver hur du konfigurerar självbetjänings hantering för Galleri-och SAML-baserade appar som du har lagt till i din Azure AD-klient. Du kan också [Konfigurera självbetjänings Microsoft 365 grupper](../enterprise-users/groups-self-service-management.md) så att användarna kan hantera åtkomst till sina egna Microsoft 365 grupper. Mer information om hur användare kan dela Office-filer och appar med gäst användare finns i [gäst åtkomst i Microsoft 365 grupper](https://support.office.com/article/guest-access-in-office-365-groups-bfc7a840-868f-4fd6-a390-f347bf51aff6) och [dela SharePoint-filer eller mappar](https://support.office.com/article/share-sharepoint-files-or-folders-1fe37332-0f9a-4719-970e-d2578da4941c).
> * Användare kan bara bjuda in gäster om de har rollen som **gäst** .
## <a name="invite-a-guest-user-to-an-app-from-the-access-panel"></a>Bjud in en gäst användare till en app från åtkomst panelen

När en app har kon figurer ATS för självbetjäning kan program ägare använda sin egen åtkomst panel för att bjuda in en gäst användare till appen som de vill dela. Gäst användaren behöver inte läggas till i Azure AD i förväg. 

1. Öppna åtkomst panelen genom att gå till `https://myapps.microsoft.com` .
2. Peka på appen, Välj ellipserna (**...**) och välj sedan **Hantera app**.
 
   ![Skärm bild som visar undermenyn hantera app för Salesforce-appen](media/add-users-iw/access-panel-manage-app.png)
 
3. Överst i listan användare väljer du **+** .
   
   ![Skärm bild som visar plus symbolen för att lägga till medlemmar i appen](media/add-users-iw/access-panel-manage-app-add-user.png)
   
4. Skriv e-postadressen för gäst användaren i sökrutan **Lägg till medlemmar** . Du kan också lägga till ett välkomstmeddelande.
   
   ![Skärm bild som visar fönstret Lägg till medlemmar för att lägga till en gäst](media/add-users-iw/access-panel-invitation.png)
   
5. Välj **Lägg till** för att skicka en inbjudan till gäst användaren. När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.

## <a name="invite-someone-to-join-a-group-that-has-access-to-the-app"></a>Bjuda in någon att ansluta till en grupp som har åtkomst till appen
När en app har kon figurer ATS för självbetjäning kan program ägare bjuda in gäst användare till de grupper som de hanterar som har åtkomst till de appar som de vill dela. Gäst användarna behöver inte redan finnas i katalogen. Program ägaren följer de här stegen för att bjuda in en gäst användare till gruppen så att de kan komma åt appen.

1. Se till att du är ägare till självbetjänings gruppen som har åtkomst till den app som du vill dela.
2. Öppna åtkomst panelen genom att gå till `https://myapps.microsoft.com` .
3. Välj appen **grupper** .
   
   ![Skärm bild som visar appen grupper i åtkomst panelen](media/add-users-iw/access-panel-groups.png)
   
4. Under **grupper som jag äger** väljer du den grupp som har åtkomst till den app som du vill dela.
   
   ![Skärm bild som visar var du väljer en grupp under grupper som jag äger](media/add-users-iw/access-panel-groups-i-own.png)
   
5. Överst i listan grupp medlemmar väljer du **+** .
   
   ![Skärm bild som visar plus symbolen för att lägga till medlemmar i gruppen](media/add-users-iw/access-panel-groups-add-member.png)
   
6. Skriv e-postadressen för gäst användaren i sökrutan **Lägg till medlemmar** . Du kan också lägga till ett välkomstmeddelande.
   
   ![Skärm bild som visar fönstret Lägg till medlemmar för att lägga till en gäst](media/add-users-iw/access-panel-invitation.png)
   
7. Välj **Lägg till** för att automatiskt skicka inbjudan till gäst användaren. När du har skickat inbjudan läggs användarkontot automatiskt till i katalogen som gäst.


## <a name="prerequisites"></a>Förutsättningar

Hantering av självbetjänings program kräver viss inledande installation av en global administratör och en Azure AD-administratör. Som en del av den här installationen konfigurerar du appen för självbetjäning och tilldelar en grupp till appen som program ägaren kan hantera. Du kan också konfigurera gruppen så att alla kan begära medlemskap men kräva en grupp ägares godkännande. (Läs mer om [grupp hantering](../enterprise-users/groups-self-service-management.md)via självbetjäning.) 

> [!NOTE]
> Du kan inte lägga till gäst användare i en dynamisk grupp eller i en grupp som synkroniseras med lokala Active Directory.

### <a name="enable-self-service-group-management-for-your-tenant"></a>Aktivera självbetjänings grupp hantering för din klient organisation
1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör.
2. I navigerings fönstret väljer du **Azure Active Directory**.
3. Välj **grupper**.
4. Under **Inställningar** väljer du **Allmänt**.
5. Under **hantering av självbetjänings grupper**, bredvid **ägare kan hantera begär Anden om grupp medlemskap i åtkomst panelen**, väljer du **Ja**.
6. Välj **Spara**.

### <a name="create-a-group-to-assign-to-the-app-and-make-the-user-an-owner"></a>Skapa en grupp som ska tilldelas appen och ge användaren en ägare
1. Logga in på [Azure Portal](https://portal.azure.com) som en Azure AD-administratör eller global administratör.
2. I navigerings fönstret väljer du **Azure Active Directory**.
3. Välj **grupper**.
4. Välj **ny grupp**.
5. Under **grupptyp** väljer du **säkerhet**.
6. Ange ett **gruppnamn** och en **gruppbeskrivning**.
7. Under **medlemskaps typ** väljer du **tilldelad**.
8. Välj **skapa** och Stäng sidan **grupp** .
9. Öppna gruppen på sidan **grupper – alla grupper** . 
10. Under **Hantera** väljer du **ägare**  >  **Lägg till ägare**. Sök efter den användare som ska hantera åtkomst till programmet. Välj användaren och klicka sedan på **Välj**.

### <a name="configure-the-app-for-self-service-and-assign-the-group-to-the-app"></a>Konfigurera appen för självbetjäning och tilldela gruppen till appen
1. Logga in på [Azure Portal](https://portal.azure.com) som en Azure AD-administratör eller global administratör.
2. I navigerings fönstret väljer du **Azure Active Directory**.
3. Under **Hantera** väljer du **företags program**  >  **alla program**.
4. Leta upp och öppna appen i listan program.
5. Under **Hantera** väljer du **enkel inloggning** och konfigurerar programmet för enkel inloggning. (Mer information finns i [hantera enkel inloggning för företags program](../manage-apps/add-application-portal-setup-sso.md).)
6. Under **Hantera** **väljer du självbetjäning och** konfigurera åtkomst till självbetjänings program. (Mer information finns i [så här använder du självbetjäning för app-åtkomst](../manage-apps/access-panel-manage-self-service-access.md).) 

    > [!NOTE]
    > För den inställning som **gruppen ska tilldelas användare till?** väljer du den grupp som du skapade i föregående avsnitt.
7. Under **Hantera** väljer **du användare och grupper** och kontrollerar att självbetjänings gruppen som du skapade visas i listan.
8. Välj **Lägg till användare**  >  **och grupper** för att lägga till appen i grupp ägarens åtkomst panel. Sök efter grupp ägaren och välj användaren, klicka på **Välj** och klicka sedan på **tilldela** för att lägga till användaren i appen.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar om Azure AD B2B-samarbete:

- [Vad är Azure AD B2B-samarbete?](what-is-b2b.md)
- [Hur lägger Azure Active Directory administratörer till B2B-samarbets användare?](add-users-administrator.md)
- [Inlösen av B2B-samarbets inbjudningar](redemption-experience.md)
- [Prissättning för Externa identiteter](external-identities-pricing.md)