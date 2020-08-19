---
title: 'Självstudie: konfigurera principer för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till principer.
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
ms.openlocfilehash: ea9a0e52ce424459b6c402eb136d06dd370bab7d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548057"
---
# <a name="tutorial-configure-mypolicies-for-automatic-user-provisioning"></a>Självstudie: konfigurera principer för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i principerna och Azure Active Directory (Azure AD) för att konfigurera Azure AD för att automatiskt etablera och avetablera användare och/eller grupper till principer.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En klient för principer](https://mypolicies.com/index.html#section10).
* Ett användar konto i principer för administratörs behörighet.

## <a name="assigning-users-to-mypolicies"></a>Tilldela användare till principer

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till principer. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till principer genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-mypolicies"></a>Viktiga tips för att tilldela användare principer

* Vi rekommenderar att en enda Azure AD-användare tilldelas principer för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till principer måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-mypolicies-for-provisioning"></a>Konfigurera principer för etablering

Innan du konfigurerar principer för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering för principer.

1. Kontakta dina principer för **support@mypolicies.com** att få den hemliga token som krävs för att konfigurera scim-etablering.

2.  Spara värdet för token som tillhandahålls av principerna som är representativa för principerna. Det här värdet anges i fältet **hemligt token** på fliken etablering i appen för principer för principer i Azure Portal.

## <a name="add-mypolicies-from-the-gallery"></a>Lägg till principer från galleriet

Om du vill konfigurera principer för automatisk användar etablering med Azure AD måste du lägga till principer från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till principer från program galleriet i Azure AD:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **principer**, väljer **principer** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Principer i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-mypolicies"></a>Konfigurera automatisk användar etablering till principer 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i principer som baseras på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för principer enligt anvisningarna i [självstudien](mypolicies-tutorial.md)om att använda principer för enkel inloggning. Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-mypolicies-in-azure-ad"></a>Konfigurera automatisk användar etablering för principer i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **principer**.

    ![Länken för mina principer i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin-autentiseringsuppgifter** , inmatat `https://<myPoliciesCustomDomain>.mypolicies.com/scim` i **klient-URL** , där `<myPoliciesCustomDomain>` är den anpassade domänen för principer. Du kan hämta din kund domän för principer från din URL.
Exempel: `<demo0-qa>` . mypolicies.com.

6. I **hemlig token**anger du det token-värde som hämtades tidigare. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till principer. Om anslutningen Miss lyckas kontrollerar du att kontot för ditt principer har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till principerna**.

    ![Användar mappningar för principer](media/mypolicies-provisioning-tutorial/usermapping.png)

10. Granska de användarattribut som synkroniseras från Azure AD till principer i avsnittet mappning av **attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i principerna för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användar mappningar för principer](media/mypolicies-provisioning-tutorial/userattribute.png)

11. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

12. Om du vill aktivera Azure AD Provisioning-tjänsten för principer ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

13. Definiera de användare och/eller grupper som du vill etablera till principer genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

14. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten för principer.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Principer för principer kräver alltid **användar namn**, **e-post** och **externalId**.
* Principer för att inte använda hård borttagningar för användarattribut stöds inte.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
