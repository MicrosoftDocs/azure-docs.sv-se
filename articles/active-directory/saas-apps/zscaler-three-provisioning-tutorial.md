---
title: 'Självstudie: Konfigurera Zscaler tre för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Zscaler tre.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: b7cb55b5d14208128c894a007d13f4e73fc54b3b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545814"
---
# <a name="tutorial-configure-zscaler-three-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Zscaler tre för automatisk användar etablering

I den här självstudien får du lära dig hur du konfigurerar Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användare och/eller grupper till Zscaler tre.

> [!NOTE]
> I den här självstudien beskrivs en koppling som bygger på Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör och hur den fungerar och svar på vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../active-directory-saas-app-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen som beskrivs i den här självstudien behöver du följande:

* En Azure AD-klientorganisation.
* En Zscaler tre-klient.
* Ett användar konto i Zscaler tre med administratörs behörighet.

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av Zscaler ZSCloud SCIM API, som är tillgängligt för företags konton.

## <a name="adding-zscaler-three-from-the-gallery"></a>Lägga till Zscaler tre från galleriet

Innan du konfigurerar Zscaler tre för automatisk användar etablering med Azure AD måste du lägga till Zscaler tre från Azure AD-programgalleriet i listan över hanterade SaaS-program.

I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**i den vänstra rutan:

![Välj Azure Active Directory](common/select-azuread.png)

Gå till **företags program** och välj sedan **alla program**:

![Företagsprogram](common/enterprise-applications.png)

Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

![Välj nytt program](common/add-new-app.png)

Skriv **Zscaler tre**i rutan Sök. Välj **Zscaler tre** i resultaten och välj sedan **Lägg till**.

![Resultat lista](common/search-new-app.png)

## <a name="assign-users-to-zscaler-three"></a>Tilldela användare till Zscaler tre

Azure AD-användare måste tilldelas åtkomst till de valda apparna innan de kan använda dem. I samband med automatisk användar etablering synkroniseras endast de användare eller grupper som är kopplade till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler tre. När du har bestämt dig kan du tilldela dessa användare och grupper till Zscaler tre genom att följa instruktionerna i [tilldela en användare eller grupp till en Enterprise-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

### <a name="important-tips-for-assigning-users-to-zscaler-three"></a>Viktiga tips för att tilldela användare till Zscaler tre

* Vi rekommenderar att du först tilldelar en enda Azure AD-användare till Zscaler tre för att testa den automatiska konfigurationen av användar etablering. Du kan tilldela fler användare och grupper senare.

* När du tilldelar en användare till Zscaler tre måste du välja en giltig programspecifik roll (om tillgängligt) i dialog rutan tilldelning. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurera automatisk användar etablering

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper i Zscaler tre baserat på användar-och grupp tilldelningar i Azure AD.

> [!TIP]
> Du kanske också vill aktivera SAML-baserad enkel inloggning för Zscaler tre. Om du gör det följer du anvisningarna i [självstudien om Zscaler tre enkla inloggningar](zscaler-three-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, men de två funktionerna kompletterar varandra.

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**  >  **alla program**  >  **Zscaler tre**:

    ![Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler tre**:

    ![Programlista](common/all-applications.png)

3. Välj fliken **etablering** :

    ![Zscaler tre etablering](./media/zscaler-three-provisioning-tutorial/provisioning-tab.png)

4. Ange **etablerings läget** till **automatiskt**:

    ![Ange etablerings läget](./media/zscaler-three-provisioning-tutorial/provisioning-credentials.png)

5. I avsnittet **admin credentials** anger du **klient-URL** och **hemlig token** för ditt Zscaler tre konto, enligt beskrivningen i nästa steg.

6. Om du vill hämta **klient-URL** och **hemlig token**går du till **Administration**  >  **autentiseringsinställningar** i Zscaler tre Portal och väljer **SAML** under **Autentiseringstyp**:

    ![Zscaler tre autentiseringsinställningar](./media/zscaler-three-provisioning-tutorial/secret-token-1.png)

    Välj **Konfigurera SAML** för att öppna fönstret **Konfigurera SAML** :

    ![Konfigurera SAML-fönster](./media/zscaler-three-provisioning-tutorial/secret-token-2.png)

    Välj **Aktivera scim-baserad etablering** och kopiera **bas-URL: en** och **Bearer-token**och spara sedan inställningarna. I Azure Portal klistrar du in **bas-URL:** en i rutan **klient-URL** och **Bearer-token** i rutan **hemlig token** .

7. När du har angett värdena i rutorna **klient-URL** och **hemlig token** väljer du **Testa anslutning** för att kontrol lera att Azure AD kan ansluta till Zscaler tre. Om anslutningen Miss lyckas kontrollerar du att ditt Zscaler tre konto har administratörs behörighet och försöker igen.

    ![Testa anslutningen](./media/zscaler-three-provisioning-tutorial/test-connection.png)

8. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Välj **Skicka ett e-postmeddelande när ett fel uppstår**:

    ![Konfigurera e-postavisering](./media/zscaler-three-provisioning-tutorial/notification.png)

9. Välj **Spara**.

10. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory användare till ZscalerThree**:

    ![Synkronisera Azure AD-användare](./media/zscaler-three-provisioning-tutorial/user-mappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Zscaler tre i avsnittet **mappningar av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Zscaler tre för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    ![Mappningar för attribut](./media/zscaler-three-provisioning-tutorial/user-attribute-mappings.png)

12. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till ZscalerThree**:

    ![Synkronisera Azure AD-grupper](./media/zscaler-three-provisioning-tutorial/group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zscaler tre i avsnittet **mappningar av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Zscaler tre för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    ![Mappningar för attribut](./media/zscaler-three-provisioning-tutorial/group-attribute-mappings.png)

14. Information om hur du konfigurerar omfångs filter finns i anvisningarna i [kursen omfångs filter](./../active-directory-saas-scoping-filters.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Zscaler tre ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** :

    ![Etablerings status](./media/zscaler-three-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare och/eller grupper som du vill etablera till Zscaler tre genom att välja de värden som du vill använda under **omfång** i avsnittet **Inställningar** :

    ![Omfattnings värden](./media/zscaler-three-provisioning-tutorial/scoping.png)

17. När du är redo att etablera väljer du **Spara**:

    ![Välja Spara](./media/zscaler-three-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats under **omfång** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid än efterföljande synkroniseringar, vilket sker ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. Du kan övervaka förloppet i avsnittet **synkroniseringsinformation** . Du kan också följa länkar till en etablerings aktivitets rapport, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Zscaler tre.

Information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-three-provisioning-tutorial/tutorial-general-03.png
