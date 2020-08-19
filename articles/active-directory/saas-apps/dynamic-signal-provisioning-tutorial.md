---
title: 'Självstudie: Konfigurera dynamisk signal för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till dynamisk signal.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: 4d578e089edbd99f8a0d37d805c297b42f60b3f4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88555673"
---
# <a name="tutorial-configure-dynamic-signal-for-automatic-user-provisioning"></a>Självstudie: Konfigurera dynamisk signal för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i dynamisk signal och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till dynamisk signal.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En klient för dynamisk signal](https://dynamicsignal.com/)
* Ett användar konto i dynamisk signal med administratörs behörighet.

## <a name="add-dynamic-signal-from-the-gallery"></a>Lägg till dynamisk signal från galleriet

Innan du konfigurerar dynamisk signal för automatisk användar etablering med Azure AD måste du lägga till en dynamisk signal från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till dynamisk signal från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök anger du **dynamisk signal**, väljer **dynamisk signal** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Dynamic Signal i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-dynamic-signal"></a>Tilldela användare till dynamisk signal

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till dynamisk signal. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till dynamiska signaler genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dynamic-signal"></a>Viktiga tips för att tilldela användare till en dynamisk signal

* Vi rekommenderar att en enda Azure AD-användare tilldelas dynamisk signal för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till en dynamisk signal måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-dynamic-signal"></a>Konfigurera automatisk användar etablering till dynamisk signal 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i dynamisk signal utifrån användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för dynamisk signal genom att följa anvisningarna i [självstudien om dynamisk signal för enkel inloggning](dynamicsignal-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-dynamic-signal-in-azure-ad"></a>Konfigurera automatisk användar etablering för dynamisk signal i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer **Dynamic Signal**.

    ![Dynamic Signal-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **klient-URL: en** och den **hemliga token** för ditt dynamiska signal konto enligt beskrivningen i steg 6.

6. I administrations konsolen för dynamisk signal navigerar du till **admin > Advanced > API**.

    ![Dynamisk signal etablering](./media/dynamic-signal-provisioning-tutorial/secret-token-1.png)

    Kopiera URL **: en för scim-API** till **klient-URL**. Klicka på **Skapa ny token** för att generera en **Bearer-token** och kopiera värdet till den **hemliga token**.

    ![Dynamisk signal etablering](./media/dynamic-signal-provisioning-tutorial/secret-token-2.png)

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till en dynamisk signal. Om anslutningen Miss lyckas ser du till att det dynamiska signal kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till dynamisk signal**.

    ![Användar mappningar för dynamisk signal](media/dynamic-signal-provisioning-tutorial/user-mappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till en dynamisk signal i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i dynamisk signal för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användarattribut för dynamisk signal](media/dynamic-signal-provisioning-tutorial/user-mapping-attributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för dynamisk signal ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till dynamisk signal genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på dynamisk signal.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Den dynamiska signalen stöder inte permanent användare som tar bort från Azure AD. Om du vill ta bort en användare permanent i dynamisk signal, måste åtgärden göras via administratörs konsolen för dynamiskt signal. 
* Dynamisk signal stöder för närvarande inte grupper.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

