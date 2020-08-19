---
title: 'Självstudie: Konfigurera Zscaler Private Access (ZPA) för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Zscaler Private Access (ZPA).
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.author: Zhchia
ms.openlocfilehash: c605efe60e279ca3f5f14055e39bf95dc677542b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545849"
---
# <a name="tutorial-configure-zscaler-private-access-zpa-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Zscaler Private Access (ZPA) för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Zscaler Private Access (ZPA) och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Zscaler Private Access (ZPA).

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En ZPA-klient (Zscaler Private Access)](https://www.zscaler.com/pricing-and-plans#contact-us)
* Ett användar konto i Zscaler Private Access (ZPA) med administratörs behörighet.

## <a name="assigning-users-to-zscaler-private-access-zpa"></a>Tilldela användare till Zscaler Private Access (ZPA)

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Zscaler Private Access (ZPA). När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Zscaler Private Access (ZPA) genom att följa anvisningarna här:
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="important-tips-for-assigning-users-to-zscaler-private-access-zpa"></a>Viktiga tips för att tilldela användare till Zscaler Private Access (ZPA)

* Vi rekommenderar att en enda Azure AD-användare tilldelas Zscaler Private Access (ZPA) för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Zscaler Private Access (ZPA), måste du välja en giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-zscaler-private-access-zpa-for-provisioning"></a>Konfigurera Zscaler Private Access (ZPA) för etablering

1. Logga in på [Administratörs konsolen för Zscaler Private Access (ZPA)](https://admin.private.zscaler.com/). Gå till **Administration > IDP-konfiguration**.

    ![Administratörs konsol för Zscaler Private Access (ZPA)](media/zscaler-private-access-provisioning-tutorial/idpconfig.png)

2.  Kontrol lera att en IdP för **enkel inloggning** har kon figurer ATS. Om ingen IdP har kon figurer ATS kan du lägga till en genom att klicka på plus ikonen i det övre högra hörnet på skärmen.

    ![Zscaler Private Access (ZPA) Lägg till SCIM](media/zscaler-private-access-provisioning-tutorial/plusicon.png)

3. Lägg till en IdP genom att följa anvisningarna i guiden **Lägg till IDP-konfiguration** . Lämna fältet för **enkel inloggning** inställt på **användare**. Ange ett **namn** och välj **domänerna** i list rutan. Klicka på **Nästa** för att navigera till nästa fönster.

    ![Zscaler Private Access (ZPA) Lägg till IdP](media/zscaler-private-access-provisioning-tutorial/addidp.png)

4. Hämta **tjänst leverantörens certifikat**. Klicka på **Nästa** för att navigera till nästa fönster.

    ![Zscaler Private Access (ZPA) SP-certifikat](media/zscaler-private-access-provisioning-tutorial/spcertificate.png)

5. I nästa fönster laddar du ned **tjänst leverantörens certifikat** som hämtades tidigare.

    ![Zscaler Private Access (ZPA) Ladda upp certifikat](media/zscaler-private-access-provisioning-tutorial/uploadfile.png)

6.  Rulla ned för att ange URL: en för **enkel inloggning** och **IDP entitets-ID**.

    ![Zscaler Private Access (ZPA) IdP-ID](media/zscaler-private-access-provisioning-tutorial/idpid.png)

7.  Rulla ned för att **Aktivera scim-synkronisering**. Klicka på knappen **generera ny token** . Kopiera **Bearer-token**. Det här värdet anges i fältet Hemlig token på fliken etablering i ditt ZPA-program (Zscaler Private Access) i Azure Portal.

    ![Zscaler Private Access (ZPA) skapa token](media/zscaler-private-access-provisioning-tutorial/token.png)

8.  För att hitta **klient webb adressen** går du till **Administration > IDP-konfiguration**. Klicka på namnet på den nyligen tillagda IdP-konfigurationen som visas på sidan.

    ![Zscaler Private Access (ZPA) IDP namn](media/zscaler-private-access-provisioning-tutorial/idpname.png)

9.  Rulla ned för att visa **scim-slutpunkten** i slutet av sidan. Kopiera **scim Service Provider-slutpunkten**. Det här värdet anges i fältet klient-URL på fliken etablering i ditt Zscaler Private Access-program (ZPA) i Azure Portal.

    ![Zscaler Private Access (ZPA) SCIM URL](media/zscaler-private-access-provisioning-tutorial/tenanturl.png)


## <a name="add-zscaler-private-access-zpa-from-the-gallery"></a>Lägg till Zscaler Private Access (ZPA) från galleriet

Innan du konfigurerar Zscaler Private Access (ZPA) för automatisk användar etablering med Azure AD måste du lägga till Zscaler Private Access (ZPA) från Azure AD-programgalleriet till listan över hanterade SaaS-program.

**Gör så här om du vill lägga till Zscaler Private Access (ZPA) från Azure AD-programgalleriet:**

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Zscaler Private Access (ZPA)**, väljer **Zscaler Private Access (ZPA)** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Zscaler Private Access (ZPA) i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-zscaler-private-access-zpa"></a>Konfigurera automatisk användar etablering till Zscaler Private Access (ZPA) 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Zscaler Private Access (ZPA) baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Zscaler Private Access (ZPA) genom att följa anvisningarna i [självstudien Zscaler Private Access (ZPA) för enkel inloggning](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner kompletterar varandra.

> [!NOTE]
> Läs mer om Zscaler Private accesss SCIM- [slutpunkt.](https://www.zscaler.com/partners/microsoft)

### <a name="to-configure-automatic-user-provisioning-for-zscaler-private-access-zpa-in-azure-ad"></a>Konfigurera automatisk användar etablering för Zscaler Private Access (ZPA) i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Zscaler Private Access (ZPA)**.

    ![Länken Zscaler Private Access (ZPA) i listan program](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Fliken etablering](common/provisioning.png)

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Fliken etablering](common/provisioning-automatic.png)

5. Under avsnittet **autentiseringsuppgifter för administratörer** inhämtar du **slut punkt svärdet för scim-tjänsteprovidern** som hämtades tidigare i **klient webb adressen**. Mata in värdet för **Bearer-token** som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Zscaler Private Access (ZPA). Om anslutningen Miss lyckas kontrollerar du att kontot för Zscaler Private Access (ZPA) har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postmeddelande](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Zscaler Private Access (ZPA)**.

    ![Zscaler Private Access (ZPA) användar mappningar](media/zscaler-private-access-provisioning-tutorial/usermappings.png)

9. Granska de användarattribut som synkroniseras från Azure AD till Zscaler Private Access (ZPA) i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Zscaler Private Access (ZPA) för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Zscaler Private Access (ZPA) användarattribut](media/zscaler-private-access-provisioning-tutorial/userattributes.png)

10. Under avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till Zscaler Private Access (ZPA)**.

    ![Zscaler Private Access (ZPA) grupp mappningar](media/zscaler-private-access-provisioning-tutorial/groupmappings.png)

11. Granska gruppattributen som synkroniseras från Azure AD till Zscaler Private Access (ZPA) i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Zscaler Private Access (ZPA) för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Zscaler Private Access (ZPA) grupp attribut](media/zscaler-private-access-provisioning-tutorial/groupattributes.png)

12. Information om hur du konfigurerar omfångs filter finns i följande instruktioner i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Zscaler Private Access (ZPA) ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings status växlad på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Zscaler Private Access (ZPA) genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etablerings omfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Etablerings konfigurationen sparas](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Zscaler Private Access (ZPA).

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

