---
title: 'Självstudie: Konfigurera Dropbox for Business för automatisk användareablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetabler användarkonton till Dropbox for Business.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 4e718ef30d029995c49efe36f19a6efcf7de0616
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589609"
---
# <a name="tutorial-configure-dropbox-for-business-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Dropbox for Business för automatisk användareablering

Målet med den här självstudien är att demonstrera de steg som ska utföras i Dropbox for Business och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetabler användare och/eller grupper till Dropbox for Business.

> [!IMPORTANT]
> I framtiden kommer Microsoft och Dropbox att bli inaktuella för den gamla Dropbox-integreringen. Detta planerades ursprungligen för 2021-04-01, men har skjutas upp på obestämd tid. Men för att undvika avbrott i tjänsten rekommenderar vi att du migrerar till den nya SCIM 2.0 Dropbox-integreringen som stöder grupper. Om du vill migrera till den nya Dropbox-integreringen lägger du till och konfigurerar en ny instans av Dropbox för etablering i din Azure AD-klient med hjälp av stegen nedan. När du har konfigurerat den nya Dropbox-integreringen inaktiverar du Etablering på den gamla Dropbox-integreringen för att undvika etableringskonflikter. Mer detaljerade anvisningar om hur du migrerar till den nya Dropbox-integreringen finns i Uppdatera till det senaste [Dropbox for Business-programmet med Azure AD.](https://help.dropbox.com/installs-integrations/third-party/update-dropbox-azure-ad-connector)

> [!NOTE]
> I den här självstudien beskrivs en anslutningsapp som byggts ovanpå Azure AD-tjänsten för användareablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Scenariot som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* En Azure AD-klientorganisation
* [En Dropbox for Business-klientorganisation](https://www.dropbox.com/business/pricing)
* Ett användarkonto i Dropbox for Business med administratörsbehörighet.

## <a name="add-dropbox-for-business-from-the-gallery"></a>Lägga till Dropbox for Business från galleriet

Innan du konfigurerar Dropbox for Business för automatisk användareablering med Azure AD måste du lägga till Dropbox for Business från Azure AD-programgalleriet i din lista över hanterade SaaS-program.

**Utför följande steg för att lägga till Dropbox for Business från Azure AD-programgalleriet:**

1. I den **[Azure Portal](https://portal.azure.com)** navigeringspanelen väljer du **Azure Active Directory**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt **program väljer du** knappen Nytt program längst upp i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Dropbox for Business**, väljer Dropbox for **Business** i resultatpanelen och klickar sedan på knappen Lägg till **för** att lägga till programmet.

    ![Dropbox for Business i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-dropbox-for-business"></a>Tilldela användare till Dropbox for Business

Azure Active Directory använder ett begrepp som kallas *tilldelningar för* att avgöra vilka användare som ska få åtkomst till valda appar. När det gäller automatisk användareablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användareablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Dropbox for Business. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Dropbox for Business genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-dropbox-for-business"></a>Viktiga tips för att tilldela användare till Dropbox for Business

* Vi rekommenderar att en enda Azure AD-användare tilldelas Dropbox for Business för att testa konfigurationen för automatisk användareablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Dropbox for Business måste du välja en giltig programspecifik roll (om sådan finns) i tilldelningsdialogrutan. Användare med **standardåtkomstrollen** undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-dropbox-for-business"></a>Konfigurera automatisk användareablering till Dropbox for Business 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Dropbox for Business baserat på användar- och/eller grupptilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Dropbox for Business genom att följa anvisningarna i självstudien om enkel inloggning [för Dropbox for Business.](dropboxforbusiness-tutorial.md) Enkel inloggning kan konfigureras oberoende av automatisk användareablering, men dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-dropbox-for-business-in-azure-ad"></a>Så här konfigurerar du automatisk användareablering för Dropbox for Business i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Dropbox for Business**.

    ![Dropbox for Business-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärmbild av alternativet Hantera med alternativet Etablering.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärmbild av listrutan Etableringsläge med alternativet Automatiskt.](common/provisioning-automatic.png)

5. Under avsnittet **Autentiseringsuppgifter för administratör** klickar du på **Auktorisera**. En inloggningsdialogruta för Dropbox for Business öppnas i ett nytt webbläsarfönster.

    ![Etablering ](common/provisioning-oauth.png)

6. I dialogrutan Logga in på Dropbox for Business för att länka till Azure AD loggar du in på din Dropbox for **Business-klientorganisation** och verifierar din identitet.

    ![Dropbox for Business-inloggning](media/dropboxforbusiness-provisioning-tutorial/dropbox01.png)

7. När du har slutfört steg 5 och 6 klickar du **på Testa anslutning** för att säkerställa att Azure AD kan ansluta till Dropbox for Business. Om anslutningen misslyckas ser du till att ditt Dropbox for Business-konto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-oauth.png)

8. I fältet **E-postavisering** anger du e-postadressen för en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan – Skicka ett e-postmeddelande när ett fel **inträffar.**

    ![E-postavisering](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory användare till Dropbox**.

    ![Dropbox-användarmappningar](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-mapping.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Dropbox i avsnittet **Attributmappning.** Attributen som valts **som Matchande** egenskaper används för att matcha användarkontona i Dropbox för uppdateringsåtgärder. Välj knappen **Spara** för att genomföra ändringarna.

    ![Dropbox-användarattribut](media/dropboxforbusiness-provisioning-tutorial/dropbox-user-attributes.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory grupper till Dropbox**.

    ![Dropbox-gruppmappningar](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-mapping.png)

13. Granska de gruppattribut som synkroniseras från Azure AD till Dropbox i avsnittet **Attributmappning.** Attributen som valts **som Matchande** egenskaper används för att matcha grupperna i Dropbox för uppdateringsåtgärder. Välj knappen **Spara** för att genomföra ändringarna.

    ![Dropbox-gruppattribut](media/dropboxforbusiness-provisioning-tutorial/dropbox-group-attributes.png)

14. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Dropbox ändrar du **Etableringsstatus** **till På** i **avsnittet** Inställningar.

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera till Dropbox genom att välja önskade värden i **Omfång** i **avsnittet** Inställningar.

    ![Etableringsomfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats i** Omfång i **avsnittet** Inställningar. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som sker ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda **avsnittet** Synkroniseringsinformation för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Dropbox.

Mer information om hur du läser Azure AD-etableringsloggarna finns [i Rapportering om automatisk användarkontoetablering.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="connector-limitations"></a>Begränsningar för anslutningsapp
 
* Dropbox har inte stöd för att pausa inbjudna användare. Om en inbjuden användare inaktiveras tas den användaren bort.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

