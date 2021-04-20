---
title: 'Självstudie: Konfigurera Zscaler för automatisk användareablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användarkonton till Zscaler.
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
ms.openlocfilehash: 92553e9d598823ee1b812a4f07480dec380b2490
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739430"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Zscaler för automatisk användareablering

Målet med den här självstudien är att demonstrera de steg som ska utföras i Zscaler och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Zscaler.

> [!NOTE]
> I den här självstudien beskrivs en anslutningsapp som bygger på Azure AD-tjänsten för användareablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>

## <a name="prerequisites"></a>Förutsättningar

Scenariot som beskrivs i den här självstudien förutsätter att du redan har följande:

* En Azure AD-klientorganisation.
* En Zscaler-klientorganisation.
* Ett användarkonto i Zscaler med administratörsbehörighet.

> [!NOTE]
> Azure AD-etableringsintegreringen är beroende av Zscaler SCIM API, som är tillgängligt för Zscaler-utvecklare för konton med Enterprise-paketet.

## <a name="adding-zscaler-from-the-gallery"></a>Lägga till Zscaler från galleriet

Innan du konfigurerar Zscaler för automatisk användareablering med Azure AD måste du lägga till Zscaler från Azure AD-programgalleriet i din lista över hanterade SaaS-program.

**Utför följande steg för att lägga till Zscaler från Azure AD-programgalleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Zscaler,** väljer **Zscaler** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

    ![Zscaler i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Tilldela användare till Zscaler

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användareablering synkroniseras endast de användare och/eller grupper som har "tilldelats" till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användareablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler. När du har bestämt dig för detta kan du tilldela dessa användare och/eller grupper till Zscaler genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företagsapp](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Viktiga tips för att tilldela användare till Zscaler

* Vi rekommenderar att en enda Azure AD-användare tilldelas till Zscaler för att testa konfigurationen för automatisk användareablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Zscaler måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelningsdialogrutan. Användare med **standardåtkomstrollen** undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurera automatisk användareablering till Zscaler

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Zscaler baserat på användar- och/eller grupptilldelningar i Azure AD.


> [!NOTE]
> Öppna ett [support ärende](https://help.zscaler.com/) för att skapa en domän på Zscaler.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Zscaler genom att följa anvisningarna i självstudien om enkel inloggning [med Zscaler.](zscaler-tutorial.md) Enkel inloggning kan konfigureras oberoende av automatisk användareablering, men dessa två funktioner kompletterar varandra.

> [!NOTE]
> När användare och grupper etableras eller avetablerar rekommenderar vi att du regelbundet startar om etableringen för att säkerställa att gruppmedlemskapen uppdateras korrekt. Om du gör en omstart kommer tjänsten att omvärdera alla grupper och uppdatera medlemskapen. 

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Så här konfigurerar du automatisk användareablering för Zscaler i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **Företagsprogram,** **Alla program** och sedan **Zscaler.**

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Zscaler**.

    ![Zscaler-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärmbild av sidofältet Zscaler – Etablering av företagsprogram med alternativet Etablering markerat.](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärmbild av sidan Etablering med etableringsläget inställt på Automatiskt.](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. I avsnittet **Autentiseringsuppgifter för** administratör anger du **klientorganisations-URL** och hemlig **token** för ditt Zscaler-konto enligt beskrivningen i steg 6.

6. Om du vill **hämta klient-URL** och hemlig **token** går du **till Inställningar för >-autentisering** i användargränssnittet för Zscaler-portalen och klickar på **SAML** under **Autentiseringstyp.**

    ![Skärmbild av sidan Autentiseringsinställningar.](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klicka på **Konfigurera SAML för** att öppna **SAML-konfigurationsalternativ.**

    ![Skärmbild av dialogrutan Configure S A M L (Konfigurera S A M L) med textrutorna Base U R L och Bearer Token (Bas-U R L och bearer-token) i textrutorna .](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Välj **Aktivera SCIM-Based etablering för att** hämta **bas-URL** och **bearer-token** och spara sedan inställningarna. Kopiera **bas-URL:en** **till klient-URL** och **bearer-token** **till hemlig token** i Azure Portal.

7. När du fyller i fälten som visas i steg 5 klickar du på **Testa anslutning för** att se till att Azure AD kan ansluta till Zscaler. Om anslutningen misslyckas ser du till att Zscaler-kontot har administratörsbehörighet och försöker igen.

    ![Skärmbild av avsnittet Administratörsautentiseringsuppgifter med alternativet Testa anslutning.](./media/zscaler-provisioning-tutorial/test-connection.png)

8. I fältet **E-postavisering** anger du e-postadressen för en person eller grupp som ska få meddelanden om etableringsfel och markerar kryssrutan Skicka ett e-postmeddelande när ett fel **inträffar.**

    ![Skärmbild av textrutan E-postavisering.](./media/zscaler-provisioning-tutorial/notification.png)

9. Klicka på **Spara**.

10. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory till Zscaler**.

    ![Skärmbild av avsnittet Mappningar med alternativet Synkronisera Azure Active Directory användare till Zscaler markerat.](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Zscaler i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha användarkontona i Zscaler för uppdateringsåtgärder. Välj knappen **Spara** för att genomföra ändringarna.

    ![Skärmbild av avsnittet Attributmappningar med sju mappningar.](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory grupper till Zscaler**.

    ![Skärmbild av avsnittet Mappningar med alternativet Synkronisera Azure Active Directory grupper till Zscaler markerat.](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Granska de gruppattribut som synkroniseras från Azure AD till Zscaler i avsnittet **Attributmappning.** Attributen som valts **som Matchande** egenskaper används för att matcha grupperna i Zscaler för uppdateringsåtgärder. Välj knappen **Spara** för att genomföra ändringarna.

    ![Skärmbild av avsnittet Attributmappningar med tre mappningar.](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD-etableringstjänsten för Zscaler ändrar **du Etableringsstatus** **till På** i **avsnittet** Inställningar.

    ![Skärmbild av alternativet Etableringsstatus inställt på På.](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Definiera de användare och/eller grupper som du vill etablera till Zscaler genom att välja önskade värden i **Omfång** i **avsnittet** Inställningar.

    ![Skärmbild av inställningen Omfång med alternativet Synkronisera endast tilldelade användare och grupper markerat.](./media/zscaler-provisioning-tutorial/scoping.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Skärmbild av sidofältet Zscaler – Etablera företagsprogram med alternativet Spara.](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som **definierats i Omfång** i **avsnittet** Inställningar. Den första synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, som sker ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs. Du kan använda **avsnittet** Synkroniseringsinformation för att övervaka förloppet och följa länkar till etableringsaktivitetsrapporten, som beskriver alla åtgärder som utförs av Azure AD-etableringstjänsten på Zscaler.

Mer information om hur du läser Etableringsloggar för Azure AD finns i [Rapportering om automatisk användarkontoetablering.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
