---
title: 'Självstudie: Konfigurera Zscaler ZSCloud för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: I den här självstudien får du lära dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Zscaler ZSCloud.
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
ms.openlocfilehash: fa90cbf1e467416010ae0ba83e9344a84ce52e21
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97936506"
---
# <a name="tutorial-configure-zscaler-zscloud-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Zscaler ZSCloud för automatisk användar etablering

I den här självstudien får du lära dig hur du konfigurerar Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användare och/eller grupper till Zscaler ZSCloud.

> [!NOTE]
> I den här självstudien beskrivs en koppling som bygger på Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör och hur den fungerar och svar på vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).


## <a name="prerequisites"></a>Förutsättningar

För att slutföra stegen som beskrivs i den här självstudien behöver du följande:

* En Azure AD-klientorganisation.
* En Zscaler ZSCloud-klient.
* Ett användar konto i Zscaler-ZSCloud med administratörs behörighet.

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av Zscaler ZSCloud SCIM API, som är tillgängligt för företags konton.

## <a name="add-zscaler-zscloud-from-the-gallery"></a>Lägg till Zscaler ZSCloud från galleriet

Innan du konfigurerar Zscaler-ZSCloud för automatisk användar etablering med Azure AD måste du lägga till Zscaler-ZSCloud från Azure AD-programgalleriet i listan över hanterade SaaS-program.

I [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory** i den vänstra rutan:

![Välj Azure Active Directory](common/select-azuread.png)

Gå till **företags program** och välj sedan **alla program**:

![Företagsprogram](common/enterprise-applications.png)

Om du vill lägga till ett program väljer du **nytt program** överst i fönstret:

![Välj nytt program](common/add-new-app.png)

I rutan Sök anger du **Zscaler ZSCloud**. Välj **Zscaler ZSCloud** i resultaten och välj sedan **Lägg till**.

![Resultat lista](common/search-new-app.png)

## <a name="assign-users-to-zscaler-zscloud"></a>Tilldela användare till Zscaler-ZSCloud

Azure AD-användare måste tilldelas åtkomst till de valda apparna innan de kan använda dem. I samband med automatisk användar etablering synkroniseras endast de användare eller grupper som är kopplade till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler ZSCloud. När du har bestämt dig kan du tilldela dessa användare och grupper till Zscaler ZSCloud genom att följa anvisningarna i [tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-zscaler-zscloud"></a>Viktiga tips för att tilldela användare till Zscaler ZSCloud

* Vi rekommenderar att du först tilldelar en enda Azure AD-användare till Zscaler-ZSCloud för att testa den automatiska konfigurationen av användar etablering. Du kan tilldela fler användare och grupper senare.

* När du tilldelar en användare till Zscaler ZSCloud måste du välja en giltig programspecifik roll (om tillgängligt) i dialog rutan tilldelning. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-automatic-user-provisioning"></a>Konfigurera automatisk användar etablering

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper i Zscaler-ZSCloud baserat på användar-och grupp tilldelningar i Azure AD.

> [!TIP]
> Du kanske också vill aktivera SAML-baserad enkel inloggning för Zscaler ZSCloud. Om du gör det följer du anvisningarna i [självstudien om Zscaler ZSCloud enkel inloggning](zscaler-zsCloud-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, men de två funktionerna kompletterar varandra.

> [!NOTE]
> När användare och grupper etableras eller avetableras, rekommenderar vi att du regelbundet startar om etableringen för att säkerställa att grupp medlemskapen uppdateras korrekt. Om du gör en omstart tvingas tjänsten att utvärdera alla grupper och uppdatera medlemskapet. 

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**  >  **alla program**  >  **Zscaler ZSCloud**:

    ![Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler ZSCloud**:

    ![Programlista](common/all-applications.png)

3. Välj fliken **etablering** :

    ![Zscaler ZSCloud-etablering](./media/zscaler-zscloud-provisioning-tutorial/provisioningtab.png)

4. Ange **etablerings läget** till **automatiskt**:

    ![Ange etablerings läget](./media/zscaler-zscloud-provisioning-tutorial/provisioningcredentials.png)

5. I avsnittet **admin credentials** anger du **klient-URL** och **hemlig token** för ditt Zscaler ZSCloud-konto, enligt beskrivningen i nästa steg.

6. Om du vill hämta **klient-URL** och **hemlig token** går du till **Administration**  >  **autentiseringsinställningar** i Zscaler ZSCloud-portalen och väljer **SAML** under **Autentiseringstyp**:

    ![Autentiseringsinställningar för Zscaler-ZSCloud](./media/zscaler-zscloud-provisioning-tutorial/secrettoken1.png)

    Välj **Konfigurera SAML** för att öppna fönstret **Konfigurera SAML** :

    ![Konfigurera SAML-fönster](./media/zscaler-zscloud-provisioning-tutorial/secrettoken2.png)

    Välj **aktivera SCIM-Based etablering** och kopiera **bas-URL: en** och **Bearer-token** och spara sedan inställningarna. I Azure Portal klistrar du in **bas-URL:** en i rutan **klient-URL** och **Bearer-token** i rutan **hemlig token** .

7. När du har angett värdena i rutorna **klient-URL** och **hemlig token** väljer du **Testa anslutning** för att kontrol lera att Azure AD kan ansluta till Zscaler ZSCloud. Om anslutningen Miss lyckas kontrollerar du att Zscaler ZSCloud-kontot har administratörs behörighet och försöker igen.

    ![Testa anslutningen](./media/zscaler-zscloud-provisioning-tutorial/testconnection.png)

8. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Välj **Skicka ett e-postmeddelande när ett fel uppstår**:

    ![Konfigurera e-postavisering](./media/zscaler-zscloud-provisioning-tutorial/Notification.png)

9. Välj **Spara**.

10. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory användare till ZscalerZSCloud**:

    ![Synkronisera Azure AD-användare](./media/zscaler-zscloud-provisioning-tutorial/usermappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Zscaler ZSCloud i avsnittet **mappningar för attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i Zscaler-ZSCloud för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    ![Skärm bild av avsnittet attribut mappningar med sju mappningar som visas.](./media/zscaler-zscloud-provisioning-tutorial/userattributemappings.png)

12. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till ZscalerZSCloud**:

    ![Synkronisera Azure AD-grupper](./media/zscaler-zscloud-provisioning-tutorial/groupmappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till Zscaler ZSCloud i avsnittet **mappningar av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Zscaler-ZSCloud för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    ![Skärm bild av avsnittet attribut mappningar med tre mappningar som visas.](./media/zscaler-zscloud-provisioning-tutorial/groupattributemappings.png)

14. Information om hur du konfigurerar omfångs filter finns i anvisningarna i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Zscaler-ZSCloud ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** :

    ![Etablerings status](./media/zscaler-zscloud-provisioning-tutorial/provisioningstatus.png)

16. Definiera de användare och/eller grupper som du vill etablera till Zscaler ZSCloud genom att välja de värden som du vill använda under **omfång** i avsnittet **Inställningar** :

    ![Omfattnings värden](./media/zscaler-zscloud-provisioning-tutorial/scoping.png)

17. När du är redo att etablera väljer du **Spara**:

    ![Välja Spara](./media/zscaler-zscloud-provisioning-tutorial/saveprovisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats under **omfång** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid än efterföljande synkroniseringar, vilket sker ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. Du kan övervaka förloppet i avsnittet **synkroniseringsinformation** . Du kan också följa länkar till en etablerings aktivitets rapport, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Zscaler ZSCloud.

Information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-zscloud-provisioning-tutorial/tutorial-general-03.png