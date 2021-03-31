---
title: 'Självstudie: Konfigurera Templafy SAML2 för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Templafy SAML2.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.assetid: 8a966ef5-e364-435b-9e29-3caf27ffb498
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/19/2021
ms.author: zhchia
ms.openlocfilehash: 4cc89c1ad94ae738b25e4ea18d5fe687880969c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648245"
---
# <a name="tutorial-configure-templafy-saml2-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Templafy SAML2 för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Templafy SAML2 och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Templafy SAML2.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Templafy-klient](https://www.templafy.com/pricing/).
* Ett användar konto i Templafy med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och TEMPLAFY SAML2](../app-provisioning/customize-application-attributes.md). 

## <a name="assigning-users-to-templafy-saml2"></a>Tilldela användare till Templafy-SAML2

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Templafy SAML2. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Templafy SAML2 genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-templafy-saml2"></a>Viktiga tips för att tilldela användare till Templafy SAML2

* Vi rekommenderar att en enda Azure AD-användare tilldelas Templafy SAML2 för att testa den automatiska konfigurationen av användar etablering. Fler användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Templafy SAML2 måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="step-2-configure-templafy-saml2-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Templafy-SAML2 för att ge stöd för etablering med Azure AD

Innan du konfigurerar Templafy-SAML2 för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på Templafy SAML2.

1. Logga in på din Templafy-administratörs konsol. Klicka på **Administration**.

    ![Templafy-administratörskonsolen](media/templafy-saml-2-provisioning-tutorial/templafy-admin.png)

2. Klicka på **autentiseringsmetod**.

    ![Skärm bild av avsnittet Templafy administration med alternativet autentiseringsmetod inringat.](media/templafy-saml-2-provisioning-tutorial/templafy-auth.png)

3. Kopiera värdet för **scim-API-Key** . Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt Templafy SAML2-program i Azure Portal.

    ![En skärm bild av S-tangenten i M A P I s.](media/templafy-saml-2-provisioning-tutorial/templafy-token.png)

## <a name="step-3-add-templafy-saml2-from-the-gallery"></a>Steg 3. Lägg till Templafy SAML2 från galleriet

Om du vill konfigurera Templafy-SAML2 för automatisk användar etablering med Azure AD måste du lägga till Templafy-SAML2 från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Templafy-SAML2 från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **TEMPLAFY SAML2**, väljer **Templafy SAML2** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Templafy SAML2 i resultat listan](common/search-new-app.png)

## <a name="step-4-configure-automatic-user-provisioning-to-templafy-saml2"></a>Steg 4. Konfigurera automatisk användar etablering till Templafy SAML2 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Templafy-SAML2 baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Templafy genom att följa anvisningarna i [självstudien om enkel inloggning med Templafy](templafy-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-templafy-saml2-in-azure-ad"></a>Konfigurera automatisk användar etablering för Templafy-SAML2 i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TEMPLAFY SAML2**.

    ![Templafy SAML2-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://scim.templafy.com/scim` i **klient-URL**. Mata in det **scim API-nyckel** värde som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Templafy. Om anslutningen Miss lyckas kontrollerar du att Templafy-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Templafy SAML2**.

    ![Templafy SAML2 användar mappningar](media/templafy-saml-2-provisioning-tutorial/user-mapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Templafy SAML2 i avsnittet **mappningar för attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i TEMPLAFY-SAML2 för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |aktiv|Boolesk|
   |displayName|Sträng|
   |title|Sträng|
   |preferredLanguage|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |phoneNumbers[type eq "mobile"].value|Sträng|
   |phoneNumbers [Type EQ "fax"]. värde|Sträng|
   |externalId|Sträng|
   |adresser [Type EQ "Work"]. plats|Sträng|
   |adresser [Type EQ "Work"]. Postnr|Sträng|
   |adresser [Type EQ "Work"]. region|Sträng|
   |adresser [Type EQ "Work"]. streetAddress|Sträng|
   |adresser [Type EQ "Work"]. land|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: avdelning|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: organisation|Sträng|

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Templafy**.

    ![Templafy SAML2 Group-mappningar](media/templafy-saml-2-provisioning-tutorial/group-mapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Templafy SAML2 i avsnittet **mappningar av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i TEMPLAFY-SAML2 för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |medlemmar|Referens|
      |externalId|Sträng|      


12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Templafy-SAML2 ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Templafy SAML2 genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Templafy SAML2.

## <a name="step-5-monitor-your-deployment"></a>Steg 5. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

* Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
* Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)