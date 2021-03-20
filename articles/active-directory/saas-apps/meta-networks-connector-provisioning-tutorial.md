---
title: 'Självstudie: Konfigurera meta Networks Connector för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till meta Networks Connector.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: Zhchia
ms.openlocfilehash: b6a8f192cd26639431cc9fcb6b43e1bc5e8e2843
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353637"
---
# <a name="tutorial-configure-meta-networks-connector-for-automatic-user-provisioning"></a>Självstudie: Konfigurera meta Networks Connector för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i meta Networks Connector och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till meta Networks Connector.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En klient för meta Networks Connector](https://www.metanetworks.com/)
* Ett användar konto i meta Networks Connector med administratörs behörighet.

## <a name="assigning-users-to-meta-networks-connector"></a>Tilldela användare till meta Networks Connector

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till meta Networks Connector. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till meta Networks Connector genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-meta-networks-connector"></a>Viktiga tips för att tilldela användare till meta Networks Connector

* Vi rekommenderar att en enda Azure AD-användare tilldelas till meta Networks Connector för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till meta Networks Connector måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-meta-networks-connector-for-provisioning"></a>Konfigurera meta Networks-anslutning för etablering

1. Logga in på din [Administratörs konsol för meta Networks Connector](https://login.metanetworks.com/login/) med ditt organisations namn. Gå till **Administration > API-nycklar**.

    ![Administratörs konsol för meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/apikey.png)

2.  Klicka på plus tecknet på den övre högra sidan av skärmen för att skapa en ny **API-nyckel**.

    ![Meta Networks Connector plus ikon](media/meta-networks-connector-provisioning-tutorial/plusicon.png)

3.  Ange **API-nyckelns namn** och **Beskrivning av API-nyckeln**.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/keyname.png" alt-text="Skärm bild av administratörs konsolen för meta Networks Connector med markerat ett P I nyckel namn och ett P I Key Description-värden för Azure A D och en P I-nyckel." border="false":::

4.  Aktivera **Skriv** behörighet för **grupper** och **användare**.

    ![Behörighet för meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/privileges.png)

5.  Klicka på **Lägg till**. Kopiera **hemligheten** och spara den eftersom det är den enda gången du kan visa den. Det här värdet anges i fältet Hemlig token på fliken etablering i appen för meta Networks Connector i Azure Portal.

    :::image type="content" source="media/meta-networks-connector-provisioning-tutorial/token.png" alt-text="Skärm bild av ett fönster som talar om för användarna att en P I-nyckel har lagts till. Rutan hemlighet innehåller ett värde som inte kan tolkas och är markerat." border="false":::

6.  Lägg till en IdP genom att gå till **Administration > inställningar > IdP > skapa nytt**.

    ![Meta Networks Connector Lägg till IdP](media/meta-networks-connector-provisioning-tutorial/newidp.png)

7.  På **konfigurations** sidan för IDP kan du **namnge** IDP-konfigurationen och välja en **ikon**.

    ![IdP namn för meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/idpname.png)

    ![IdP-ikon för meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/icon.png)

8.  Under **Konfigurera scim** väljer du det API-nyckel namn som skapades i föregående steg. Klicka på **Spara**.

    ![Meta Networks Connector konfigurera SCIM](media/meta-networks-connector-provisioning-tutorial/configure.png)

9.  Gå till **Administration > inställningar > fliken IDP**. Klicka på namnet på IdP-konfigurationen som skapades i föregående steg för att visa **IDP-ID: t**. Detta **ID** läggs till i slutet av **klient-URL:** en när du anger värdet i fältet **klient-URL** på fliken etablering i appen för ditt meta Network Connector i Azure Portal.

    ![IdP-ID för meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/idpid.png)

## <a name="add-meta-networks-connector-from-the-gallery"></a>Lägg till meta Networks Connector från galleriet

Innan du konfigurerar meta Network Connector för automatisk användar etablering med Azure AD måste du lägga till meta Networks Connector från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Gör så här om du vill lägga till meta Networks Connector från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I rutan Sök anger du **meta Networks Connector**, väljer **meta Networks Connector** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Meta Networks Connector i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-meta-networks-connector"></a>Konfigurera automatisk användar etablering till meta Networks Connector 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i meta Networks Connector baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för meta Networks Connector genom att följa anvisningarna i [självstudien om enkel inloggning i meta Networks Connector](./metanetworksconnector-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om de här två funktionerna är på varandra

### <a name="to-configure-automatic-user-provisioning-for-meta-networks-connector-in-azure-ad"></a>Konfigurera automatisk användar etablering för meta Networks Connector i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Meta Networks Connector**.

    ![Länken för Meta Networks Connector i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://api.metanetworks.com/v1/scim/<IdP ID>` i **klient-URL**. Mata in **scim-autentiseringstoken** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till meta Networks Connector. Om anslutningen Miss lyckas kontrollerar du att det finns administratörs behörighet för ditt meta Network Connector-konto och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till meta Networks Connector**.

    ![Användar mappningar för meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till meta Networks Connector i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i meta Networks-anslutningen för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användarattribut för meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till meta Networks Connector**.

    ![Grupp mappningar för meta Networks Connector](media/meta-networks-connector-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till meta Networks Connector i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i meta Networks-anslutningen för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Attribut för meta Network Connector-grupp](media/meta-networks-connector-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för meta Networks Connector ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till meta Networks-anslutningen genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på meta Networks-anslutningsprogrammet.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)