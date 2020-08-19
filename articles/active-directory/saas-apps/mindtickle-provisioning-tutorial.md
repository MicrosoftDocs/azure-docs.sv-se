---
title: 'Självstudie: Konfigurera MindTickle för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till MindTickle.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 3cac57e0f205da7a39447ddd48810b8fd120ab58
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549072"
---
# <a name="tutorial-configure-mindtickle-for-automatic-user-provisioning"></a>Självstudie: Konfigurera MindTickle för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i MindTickle och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till MindTickle.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En MindTickle-klient](https://www.mindtickle.com/)
* Ett användar konto i MindTickle med administratörs behörighet.

## <a name="assigning-users-to-mindtickle"></a>Tilldela användare till MindTickle

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till MindTickle. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till MindTickle genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mindtickle"></a>Viktiga tips för att tilldela användare till MindTickle

* Vi rekommenderar att en enda Azure AD-användare tilldelas MindTickle för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till MindTickle måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-mindtickle-for-provisioning"></a>Konfigurera MindTickle för etablering

Innan du konfigurerar MindTickle för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på MindTickle.


1.  Kontakta  [MindTickle support team](mailto:help@mindtickle.com) för att hämta den JWT-token som krävs för att konfigurera scim-etablering.


## <a name="add-mindtickle-from-the-gallery"></a>Lägg till MindTickle från galleriet

Om du vill konfigurera MindTickle för automatisk användar etablering med Azure AD måste du lägga till MindTickle från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till MindTickle från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **MindTickle**, väljer **MindTickle** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![MindTickle i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mindtickle"></a>Konfigurera automatisk användar etablering till MindTickle 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i MindTickle baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för MindTickle genom att följa anvisningarna i [självstudien om enkel inloggning med MindTickle](mindtickle-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om de här två funktionerna är på varandra

### <a name="to-configure-automatic-user-provisioning-for-mindtickle-in-azure-ad"></a>Konfigurera automatisk användar etablering för MindTickle i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan med program väljer du **MindTickle**.

    ![MindTickle-länken i listan med program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://admin.mindtickle.com/scim` i **klient-URL**. Ange värdet för **JWT-token** som hämtades tidigare i text rutan för hemligt token, ange värdet för **JWT-token** som gavs av MindTickle support team. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till principer. Om anslutningen Miss lyckas kontrollerar du att MindTickle-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till MindTickle**.

    ![MindTickle användar mappningar](media/mindtickle-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till MindTickle i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i MindTickle för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![MindTickle användar mappningar](media/mindtickle-provisioning-tutorial/userattribute.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för MindTickle ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till MindTickle genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [hur lång tid det tar att etablera användare](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users). 

Du kan använda avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på MindTickle. Mer information finns i [kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
