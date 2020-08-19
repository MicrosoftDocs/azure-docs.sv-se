---
title: 'Självstudie: Konfigurera lösen ords hanteraren & digitala valvet för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton för att behålla lösen ords hanteraren & digitalt valv.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 05/07/2019
ms.author: jeedes
ms.openlocfilehash: a9ca77a059625ace13e6798e3fde84f11f3fe1db
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546803"
---
# <a name="tutorial-configure-keeper-password-manager--digital-vault-for-automatic-user-provisioning"></a>Självstudie: Konfigurera lösen ords hanteraren för & Digital Vault för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i lösen ords hanteraren i & Digital Vault och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper för att hålla lösen ords hanteraren & digitalt valv.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En lösen ords hanterare & Digital Vault-klient](https://keepersecurity.com/pricing.html?t=e)
* Ett användar konto i lösen ords hanteraren för lösen ord & digitala valvet med administratörs behörighet.

## <a name="add-keeper-password-manager--digital-vault-from-the-gallery"></a>Lägg till lösen ords hanteraren för lösen ord & digitala valvet från galleriet

Innan du konfigurerar Keepr Password Manager & Digital Vault för automatisk användar etablering med Azure AD måste du lägga till lösen ords hanteraren för lösen ord & digitalt valv från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Gör så här om du vill lägga till lösen ords hanteraren & digitala valvet i Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Behåll lösen ords hanteraren & digitala valvet**, väljer **lösen ords hanterare för lösen ord & digitalt valv** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Keeper Password Manager & Digital Vault i resultatlistan](common/search-new-app.png)

## <a name="assigning-users-to-keeper-password-manager--digital-vault"></a>Tilldela användare till behållar lösen ords hanteraren & Digital Vault

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver ha till gång till lösen ords hanteraren & digitala valvet. När du har bestämt dig kan du tilldela dessa användare och/eller grupper för att behålla lösen ords hanteraren & digitala valvet genom att följa anvisningarna här:

* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-keeper-password-manager--digital-vault"></a>Viktiga tips för att tilldela användare till behållar lösen ords hanteraren & digitala valvet

* Vi rekommenderar att en enda Azure AD-användare tilldelas till lösen ords hanteraren & Digital Vault för att testa konfigurationen för automatisk etablering av användare. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till behållar lösen ords hanteraren & digitala valvet måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configuring-automatic-user-provisioning-to-keeper-password-manager--digital-vault"></a>Konfigurera automatisk användar etablering för att behålla lösen ords hanteraren & digitala valvet 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i behållar lösen ords hanteraren & digitala valv baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Keepr Password Manager & digitala valvet, enligt instruktionerna i [hand boken för lösen ords hanteraren för lösen ord & Digital Vault med enkel inloggning](keeperpasswordmanager-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-keeper-password-manager--digital-vault-in-azure-ad"></a>Konfigurera automatisk användar etablering för Keepr Password Manager & Digital Vault i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Keeper Password Manager & Digital Vault**.

    ![Keeper Password Manager & Digital Vault-länken i programlistan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in **klient-URL: en** och den **hemliga token** för lösen ords hanteraren i lösen ordet & Digital Vault konto enligt beskrivningen i steg 6.

6. Logga in på [Administratörs konsolen](https://keepersecurity.com/console/#login)för din administratör. Klicka på **admin** och välj en befintlig nod eller skapa en ny. Navigera till fliken **etablering** och välj **Lägg till metod**.

    ![Administratörs konsol för förbehållen](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-admin-console.png)

    Välj **scim (system för identitets hantering över domäner**.

    ![Behållar Lägg till SCIM](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-add-scim.png)

    Klicka på **skapa etablerings-token**.

    ![Behållar skapa slut punkt](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-endpoint.png)

    Kopiera värdena för **URL** och **token** och klistra in dem i **klient-URL** och **hemlig token** i Azure AD. Klicka på **Spara** för att slutföra etablerings inställningen på hållaren.

    ![Behållar skapa token](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-create-token.png)

7. När du fyller i fälten som visas i steg 5, klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till behållar lösen ords hanteraren & digitalt valv. Om anslutningen Miss lyckas bör du se till att lösen ords hanteraren & det digitala Valve kontot har administratörs behörighet och försöka igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare för att behålla lösen ords hanteraren & digitalt valv**.

    ![Användar mappningar för användare](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-mappings.png)

11. Granska de användarattribut som synkroniseras från Azure AD till behållar lösen ords hanteraren & digitala valvet i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i lösen ords hanteraren för lösen ord & digitala valvet för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Användarens användar egenskaper](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-user-attributes.png)

12. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper för att behålla lösen ords hanteraren & digitalt valv**.

    ![Behållar grupp mappningar](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-mappings.png)

13. Granska gruppattributen som synkroniseras från Azure AD till behållar lösen ords hanteraren & digitala valvet i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i lösen ords hanteraren för förhands & digitalt valv för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Attribut för behållar grupp](media/keeper-password-manager-digitalvault-provisioning-tutorial/keeper-group-attributes.png)

14. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

15. Om du vill aktivera Azure AD Provisioning-tjänsten för Keepr Password Manager & Digital Vault ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

16. Definiera de användare och/eller grupper som du vill etablera för att behålla lösen ords hanteraren & digitala valvet genom att välja önskade värden i **området** **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

17. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på lösen ords hanteraren för lösen ord & digitalt valv.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

* Keepr Password Manager & digitala valvet kräver att **e-post** och **användar namn** har samma käll värde, eftersom alla uppdateringar av attributen kommer att ändra det andra värdet.
* Behållar lösen ords hanteraren & digitala valvet stöder inte användar borttagningar, bara inaktivera. Inaktiverade användare visas som låsta i användar gränssnittet för administratörs konsolen.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

