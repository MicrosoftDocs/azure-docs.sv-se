---
title: 'Självstudie: Konfigurera Dropbox för företag för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Dropbox för företag.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: e3dd0a31e23418c5871ac3db80b2ba1543576fac
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555773"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Dropbox för företag för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Dropbox for Business och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Dropbox för företag.

> [!IMPORTANT]
> Microsoft och Dropbox kommer att bli inaktuellt för den gamla Dropbox-integreringen från 04/01/2021. För att undvika avbrott i tjänsten rekommenderar vi att du migrerar till den nya Dropbox-integrering som stöder grupper. Om du vill migrera till den nya Dropbox-integreringen lägger du till och konfigurerar en ny instans av Dropbox för etablering i Azure AD-klienten med hjälp av stegen nedan. När du har konfigurerat den nya Dropbox-integreringen inaktiverar du etableringen av den gamla Dropbox-integreringen för att undvika etablerings konflikter.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Dropbox för företag-klient](https://www.dropbox.com/business/pricing)
* Ett användar konto i Dropbox för företag med administratörs behörighet.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Lägga till Dropbox för företag från galleriet

Innan du konfigurerar Dropbox för företag för automatisk användar etablering med Azure AD måste du lägga till Dropbox för företag från program galleriet i Azure AD till listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Dropbox för företag i program galleriet för Azure AD:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Dropbox för företag**, väljer **Dropbox för företag** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Dropbox for Business i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Tilldela användare till Dropbox för företag

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver ha åtkomst till Dropbox för företag. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Dropbox för företag genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Viktiga tips för att tilldela användare till Dropbox för företag

* Vi rekommenderar att en enda Azure AD-användare tilldelas till Dropbox för företag för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Dropbox för företag måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurera automatisk användar etablering till Dropbox för företag 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Dropbox för företag, baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Dropbox för företag, genom att följa anvisningarna i [självstudien för enkel inloggning i Dropbox for Business](dropboxforbusiness-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Konfigurera automatisk användar etablering för Dropbox för företag i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Dropbox för företag**.

    ![Dropbox for Business-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** klickar du på **auktorisera**. Den öppnar dialog rutan Dropbox for Business login i ett nytt webbläsarfönster.

    ![Etablering ](common/provisioning-oauth.png)

6. På **inloggningen till Dropbox för företag att länka till Azure AD** -dialog rutan loggar du in på Dropbox för företag-klienten och verifierar din identitet.

    ![Logga in för Dropbox för företag](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. När du är klar med steg 5 och 6 klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till Dropbox för företag. Om anslutningen Miss lyckas kontrollerar du att ditt Dropbox för företag-kontot har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-oauth.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Dropbox**.

    ![Användar mappningar för Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Dropbox i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Dropbox för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Dropbox-användarattribut](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Dropbox**.

    ![Grupp mappningar för Dropbox](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Dropbox i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Dropbox för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Attribut för Dropbox-grupp](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Dropbox ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till Dropbox genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Dropbox.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar
 
* Dropbox har inte stöd för att pausa inbjudna användare. Om en inbjuden användare har pausats tas den användaren bort.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

