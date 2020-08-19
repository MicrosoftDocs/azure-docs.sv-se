---
title: 'Självstudie: Konfigurera SmartFile för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till SmartFile.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: 675d9c76841ba1a4b94b666c7ff4ac4287fc5264
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525198"
---
# <a name="tutorial-configure-smartfile-for-automatic-user-provisioning"></a>Självstudie: Konfigurera SmartFile för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i SmartFile och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till SmartFile.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En SmartFile-klient](https://www.SmartFile.com/pricing/).
* Ett användar konto i SmartFile med administratörs behörighet.

## <a name="assigning-users-to-smartfile"></a>Tilldela användare till SmartFile

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till SmartFile. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till SmartFile genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-smartfile"></a>Viktiga tips för att tilldela användare till SmartFile

* Vi rekommenderar att en enda Azure AD-användare tilldelas SmartFile för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till SmartFile måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-smartfile-for-provisioning"></a>Konfigurera SmartFile för etablering

Innan du konfigurerar SmartFile för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på SmartFile och samla in ytterligare information som behövs.

1. Logga in på administrations konsolen för SmartFile. Gå till det övre högra hörnet i SmartFile-administratörskonsolen. Välj **produkt nyckel**.

    ![SmartFile-administratörskonsolen](media/smartfile-provisioning-tutorial/login.png)

2. Om du vill generera en Bearer-token kopierar du **produkt nyckeln** och **produkt lösen ordet**. Klistra in dem i ett anteckningar med ett kolon mellan dem.
    
     ![SmartFile Lägg till SCIM](media/smartfile-provisioning-tutorial/auth.png)

    ![SmartFile Lägg till SCIM](media/smartfile-provisioning-tutorial/key.png)

## <a name="add-smartfile-from-the-gallery"></a>Lägg till SmartFile från galleriet

Om du vill konfigurera SmartFile för automatisk användar etablering med Azure AD måste du lägga till SmartFile från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till SmartFile från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **SmartFile**, väljer **SmartFile** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![SmartFile i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-smartfile"></a>Konfigurera automatisk användar etablering till SmartFile 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i SmartFile baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för SmartFile genom att följa anvisningarna i [självstudien om enkel inloggning med SmartFile](SmartFile-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om de här två funktionerna är på varandra

### <a name="to-configure-automatic-user-provisioning-for-smartfile-in-azure-ad"></a>Konfigurera automatisk användar etablering för SmartFile i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SmartFile**.

    ![Länken för SmartFile i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5.  Under avsnittet **admin credentials** , inmatat `https://<SmartFile sitename>.smartfile.com/ftp/scim` i **klient-URL**. Ett exempel skulle se ut `https://demo1test.smartfile.com/ftp/scim` . Ange värdet för **Bearer-token** (ProductKey: ProductPassword) som du hämtade tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till SmartFile. Om anslutningen Miss lyckas kontrollerar du att SmartFile-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till SmartFile**.

    ![SmartFile användar mappningar](media/smartfile-provisioning-tutorial/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till SmartFile i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i SmartFile för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![SmartFile-användarattribut](media/smartfile-provisioning-tutorial/userattribute.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till SmartFile**.

    ![SmartFile grupp mappningar](media/smartfile-provisioning-tutorial/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till SmartFile i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i SmartFile för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![SmartFile grupp-attribut](media/smartfile-provisioning-tutorial/groupattribute.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för SmartFile ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till SmartFile genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på SmartFile.

    Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md)
    
## <a name="connector-limitations"></a>Kopplings begränsningar

* SmartFile stöder bara hårda borttagningar. 

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

 [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
