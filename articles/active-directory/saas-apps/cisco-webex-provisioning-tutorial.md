---
title: 'Självstudie: konfigurera Cisco-WebEx för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Cisco WebEx.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: bdf2bf4102795d35df7eae658e3a24028da79b33
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551310"
---
# <a name="tutorial-configure-cisco-webex-for-automatic-user-provisioning"></a>Självstudie: konfigurera Cisco-WebEx för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Cisco WebEx och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare till Cisco WebEx.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande en för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Cisco WebEx-klient](https://www.webex.com/pricing/index.html).
* Ett användar konto i Cisco WebEx med administratörs behörighet.

## <a name="adding-cisco-webex-from-the-gallery"></a>Lägga till Cisco Webex från galleriet

Innan du konfigurerar Cisco WebEx för automatisk användar etablering med Azure AD måste du lägga till Cisco WebEx från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Utför följande steg för att lägga till Cisco WebEx från Azure AD-programgalleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Cisco Webex**, väljer **Cisco Webex** på resultatpanelen och klickar på **Lägg till** för att lägga till programmet.

    ![Cisco Webex i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-cisco-webex"></a>Tilldela användare till Cisco WebEx

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare i Azure AD som behöver åtkomst till Cisco WebEx. När du har bestämt dig kan du tilldela dessa användare till Cisco WebEx genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cisco-webex"></a>Viktiga tips för att tilldela användare till Cisco WebEx

* Vi rekommenderar att en enda Azure AD-användare tilldelas Cisco-WebEx för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare kan tilldelas senare.

* När du tilldelar en användare till Cisco WebEx måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-cisco-webex"></a>Konfigurera automatisk användar etablering till Cisco WebEx

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare i Cisco-WebEx baserat på användar tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-cisco-webex-in-azure-ad"></a>Konfigurera automatisk användar etablering för Cisco WebEx i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**, Välj **alla program**och välj sedan **Cisco WebEx**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Cisco Webex** i listan över program.

    ![Cisco Webex-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Cisco WebEx-etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Cisco WebEx-etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **klient webb adressen**och den **hemliga token** för ditt Cisco WebEx-konto.

    ![Cisco WebEx-etablering](./media/cisco-webex-provisioning-tutorial/secrettoken1.png)

6.  I fältet **klient-URL** anger du ett värde i formatet `https://api.ciscoweb.com/v1/scim/[OrgId]` . Du skaffar `[OrgId]` dig genom att logga in på ditt [Cisco WebEx-kontroll nav](https://admin.webex.com/login). Klicka på organisationens namn längst ned till vänster och kopiera värdet från **organisations-ID**. 

    * Hämta värdet för **hemlig token**genom att gå till denna [URL](https://idbroker.webex.com/idb/saml2/jsp/doSSO.jsp?type=login&goto=https%3A%2F%2Fidbroker.webex.com%2Fidb%2Foauth2%2Fv1%2Fauthorize%3Fresponse_type%3Dtoken%26client_id%3DC4ca14fe00b0e51efb414ebd45aa88c1858c3bfb949b2405dba10b0ca4bc37402%26redirect_uri%3Dhttp%253A%252F%252Flocalhost%253A3000%252Fauth%252Fcode%26scope%3Dspark%253Apeople_read%2520spark%253Apeople_write%2520Identity%253ASCIM%26state%3Dthis-should-be-a-random-string-for-security-purpose). På WebEx-inloggnings sidan som visas loggar du in med det fullständiga Cisco WebEx-administratörskontot för din organisation. En felsida visas som säger att platsen inte kan nås, men det är normalt.

        ![Cisco WebEx-etablering](./media/cisco-webex-provisioning-tutorial/test.png)
 
    * Kopiera värdet för den genererade Bearer-token från URL: en som marker ATS nedan. Denna token är giltig i 365 dagar.
        
        ![Cisco WebEx-etablering](./media/cisco-webex-provisioning-tutorial/test1.png)

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till Cisco WebEx. Om anslutningen Miss lyckas kontrollerar du att ditt Cisco WebEx-konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)
   
8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Cisco WebEx**.

    ![Cisco WebEx-etablering](./media/cisco-webex-provisioning-tutorial/usermapping.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Cisco WebEx i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Cisco WebEx för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Cisco WebEx-etablering](./media/cisco-webex-provisioning-tutorial/usermappingattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Cisco-WebEx ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Cisco WebEx genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Cisco WebEx.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Cisco WebEx är för närvarande i Ciscos fas för tidig fält testning (EFT). Kontakta [Ciscos support team](https://www.webex.co.in/support/support-overview.html)om du vill ha mer information. 
* Mer information om Cisco WebEx-konfiguration finns i Cisco-dokumentationen [här](https://help.webex.com/en-us/aumpbz/Synchronize-Azure-Active-Directory-Users-into-cisco-webex-Control-Hub).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)