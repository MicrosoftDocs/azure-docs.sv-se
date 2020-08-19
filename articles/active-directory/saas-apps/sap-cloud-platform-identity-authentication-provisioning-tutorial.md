---
title: 'Självstudie: konfigurera SAP Cloud Platform Identity Authentication för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till SAP Cloud Platform Identity Authentication.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 09/19/2019
ms.author: Zhchia
ms.openlocfilehash: c330d8000825b2e0ccbdab8c5c1cdefa4d3d45c8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543383"
---
# <a name="tutorial-configure-sap-cloud-platform-identity-authentication-for-automatic-user-provisioning"></a>Självstudie: konfigurera SAP Cloud Platform Identity Authentication för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i SAP Cloud Platform Identity Authentication och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till SAP Cloud Platform Identity Authentication.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En klient för SAP Cloud Platform Identity Authentication](https://cloudplatform.sap.com/pricing.html)
* Ett användar konto i SAP Cloud Platform Identity Authentication med administratörs behörighet.

## <a name="assigning-users-to-sap-cloud-platform-identity-authentication"></a>Tilldela användare till SAP Cloud Platform Identity Authentication

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till SAP Cloud Platform Identity Authentication. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till SAP Cloud Platform Identity Authentication genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-sap-cloud-platform-identity-authentication"></a>Viktiga tips för att tilldela användare SAP Cloud Platform Identity Authentication

* Vi rekommenderar att en enda Azure AD-användare tilldelas SAP Cloud Platform Identity Authentication för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till SAP Cloud Platform Identity Authentication måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-sap-cloud-platform-identity-authentication-for-provisioning"></a>Konfigurera SAP Cloud Platform Identity Authentication för etablering

1. Logga in på din [SAP Cloud Platform Identity Authentication-konsol](https://sapmsftintegration.accounts.ondemand.com/admin). Navigera till **användare & auktoriseringar > administratörer**.

    ![SAP Cloud Platform Identity Authentication admin-konsol](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/adminconsole.png)

2.  Tryck på knappen **+ Lägg till** i den vänstra panelen för att lägga till en ny administratör i listan. Välj **Lägg till system** och ange namnet på systemet.   

> [!NOTE]
> Administratörs-användaren i SAP Cloud Platform Platform Identity Authentication måste vara av typen **system**. Att skapa en normal administratörs användare kan leda till *obehöriga* fel under etableringen.   

3.  Under Konfigurera auktoriseringar växlar du till växlings knappen mot **Hantera användare** och **hantera grupper**.

    ![SAP Cloud Platform Identity Authentication Add SCIM](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/configurationauth.png)

4. Du får ett e-postmeddelande om att aktivera ditt konto och ange ett lösen ord för **SAP Cloud Platform Identity Authentication Service**.

4.  Kopiera **användar-ID** och **lösen ord**. Dessa värden anges i fälten admin användar namn och administratörs lösen ord på fliken etablering i SAP Cloud Platform Identity Authentication Application i Azure Portal.

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>Lägg till SAP Cloud Platform Identity Authentication från galleriet

Innan du konfigurerar SAP Cloud Platform Identity Authentication för automatisk användar etablering med Azure AD måste du lägga till SAP Cloud Platform Identity Authentication från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Utför följande steg för att lägga till SAP Cloud Platform Identity Authentication från Azure AD Application Gallery:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **SAP Cloud Platform Identity Authentication**, väljer **SAP Cloud Platform Identity Authentication** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![SAP Cloud Platform Identity Authentication i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-sap-cloud-platform-identity-authentication"></a>Konfigurera automatisk användar etablering till SAP Cloud Platform Identity Authentication 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i SAP Cloud Platform Identity Authentication baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för SAP Cloud Platform Identity Authentication genom att följa anvisningarna i [själv studie kursen SAP Cloud Platform Identity Authentication-inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om de här två funktionerna är på varandra

### <a name="to-configure-automatic-user-provisioning-for-sap-cloud-platform-identity-authentication-in-azure-ad"></a>Konfigurera automatisk användar etablering för SAP Cloud Platform Identity Authentication i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **SAP Cloud Platform Identity Authentication**.

    ![Länken för SAP Cloud Platform Identity Authentication i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://<tenantID>.accounts.ondemand.com/service/scim ` i **klient-URL**. Mata in värdena för **användar-ID** och **lösen ord** som hämtades tidigare i **Administratörs användar namn** och **Administratörs lösen ord** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till SAP Cloud Platform Identity Authentication. Om anslutningen Miss lyckas kontrollerar du att ditt SAP Cloud Platform Identity Authentication-konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/testconnection.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till SAP Cloud Platform Identity Authentication**.

    ![SAP Cloud Platform Identity Authentication User-mappningar](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/mapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till SAP Cloud Platform Identity Authentication i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i SAP Cloud Platform Identity Authentication för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användar attribut för SAP Cloud Platform Identity Authentication](media/sap-cloud-platform-identity-authentication-provisioning-tutorial/userattributes.png)

10. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för SAP Cloud Platform Identity Authentication, ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till SAP Cloud Platform Identity Authentication genom att välja önskade värden i **området** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på SAP Cloud Platform Identity Authentication.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* SAP Cloud Platform Identity Authentication SCIM-slutpunkten kräver att vissa attribut är av ett specifikt format. Du kan veta mer om dessa attribut och deras speciella format [här](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/b10fc6a9a37c488a82ce7489b1fab64c.html#).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

