---
title: 'Självstudie: Konfigurera Symantec Web Security Service (WSS) för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Symantec Web Security Service (WSS).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/23/2019
ms.author: Zhchia
ms.openlocfilehash: 083bb7549bb555cbbefaf680d401696ecd64b9c9
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544918"
---
# <a name="tutorial-configure-symantec-web-security-service-wss-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Symantec Web Security Service (WSS) för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Symantec Web Security Service (WSS) och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper i Symantec Web Security Service (WSS).

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Symantec Web Security Service-klient (WSS)](https://www.websecurity.symantec.com/buy-renew?inid=brmenu_nav_brhome)
* Ett användar konto i Symantec Web Security Service (WSS) med administratörs behörighet.

## <a name="assigning-users-to-symantec-web-security-service-wss"></a>Tilldela användare till Symantec Web Security Service (WSS)

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Symantec Web Security Service (WSS). När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Symantec Web Security Service (WSS) genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

##  <a name="important-tips-for-assigning-users-to-symantec-web-security-service-wss"></a>Viktiga tips för att tilldela användare till Symantec Web Security Service (WSS)

* Vi rekommenderar att en enda Azure AD-användare tilldelas Symantec Web Security Service (WSS) för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Symantec Web Security Service (WSS) måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="setup-symantec-web-security-service-wss-for-provisioning"></a>Konfigurera Symantec Web Security Service (WSS) för etablering

Innan du konfigurerar Symantec Web Security Service (WSS) för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering i Symantec Web Security Service (WSS).

1. Logga in på [administrations konsolen för Symantec Web Security Service](https://portal.threatpulse.com/login.jsp). Gå till **lösnings**  >  **tjänsten**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/service.png)

2. Navigera till **konto underhålls**  >  **integration**  >  **ny integrering**.

    ![Symantec Web Security Service (WSS)](media/symantec-web-security-service/acount.png)

3.  Välj **användare & grupper av tredje part**. 

    ![Symantec-webbtjänst för säkerhet](media/symantec-web-security-service/third-party-users.png)

4.  Kopiera **scim-URL** och **token**. Dessa värden anges i fältet klient- **URL** och **hemligt token** på fliken etablering i Symantec Web Security Service (WSS)-programmet i Azure Portal.

    ![Symantec-webbtjänst för säkerhet](media/symantec-web-security-service/scim.png)

## <a name="add-symantec-web-security-service-wss-from-the-gallery"></a>Lägg till Symantec Web Security Service (WSS) från galleriet

Om du vill konfigurera Symantec Web Security Service (WSS) för automatisk användar etablering med Azure AD måste du lägga till Symantec Web Security Service (WSS) från Azure AD-programgalleriet i listan över hanterade SaaS-program.

**Gör så här för att lägga till Symantec Web Security Service (WSS) från Azure AD Application Gallery:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Symantec Web Security Service**, väljer **Symantec Web Security Service** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Symantec Web Security Service (WSS) i resultatlistan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-symantec-web-security-service-wss"></a>Konfigurera automatisk användar etablering till Symantec Web Security Service (WSS)

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Symantec Web Security Service (WSS) baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Symantec Web Security Service (WSS) genom att följa anvisningarna i [själv studie kursen om enkel inloggning i Symantec Web Security Service (WSS)](symantec-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-provisioning-for-symantec-web-security-service-wss-in-azure-ad"></a>Konfigurera automatisk användar etablering för Symantec Web Security Service (WSS) i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Symantec Web Security Service**.

    ![Symantec Web Security Service-länken (WSS) i listan med program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet admin credentials måste du skriva **in scim-URL** och **token** som hämtades tidigare i klient- **URL: en** och den **hemliga token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Symantec Web Security Service. Om anslutningen Miss lyckas kontrollerar du att ditt Symantec Web Security Service-konto (WSS) har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Symantec Web Security Service (WSS)**.

    ![Användar mappningar för Symantec Web Security Service (WSS)](media/symantec-web-security-service/usermapping.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Symantec Web Security Service (WSS) i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Symantec Web Security Service (WSS) för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användar mappningar för Symantec Web Security Service (WSS)](media/symantec-web-security-service/userattribute.png)

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Symantecs webb säkerhets tjänst**.

    ![Användar mappningar för Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupmapping.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Symantec Web Security Service (WSS) i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Symantec Web Security Service (WSS) för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användar mappningar för Symantec Web Security Service (WSS)](media/symantec-web-security-service/groupattribute.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Symantec Web Security Service ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Symantec Web Security Service (WSS) genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Mer information om hur lång tid det tar för användare och/eller grupper att etablera finns i [hur lång tid det tar att etablera användare](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Du kan använda avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Symantec Web Security Service (WSS). Mer information finns i [kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
