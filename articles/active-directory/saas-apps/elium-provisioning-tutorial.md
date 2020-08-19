---
title: 'Självstudie: Konfigurera Elium för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Elium.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 08/19/2019
ms.author: Zhchia
ms.openlocfilehash: d9bb21ab4b5610c8318fe4ee869f1140faf55367
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544238"
---
# <a name="tutorial-configure-elium-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Elium för automatisk användar etablering

Den här självstudien visar hur du konfigurerar Elium och Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användare eller grupper till Elium.

> [!NOTE]
> I den här självstudien beskrivs en koppling som är byggd ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör och hur den fungerar och för vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här kopplingen är för närvarande en för hands version. Allmänna användnings villkor för Azure-funktioner i för hands version finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klient
* [En Elium-klient](https://www.elium.com/pricing/)
* Ett användar konto i Elium med administratörs behörighet

## <a name="assigning-users-to-elium"></a>Tilldela användare till Elium

Azure AD använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som får åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering ska du bestämma vilka användare och grupper i Azure AD som behöver åtkomst till Elium. Tilldela sedan dessa användare och grupper till Elium genom att följa stegen i [tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md).

## <a name="important-tips-for-assigning-users-to-elium"></a>Viktiga tips för att tilldela användare till Elium 

Vi rekommenderar att du tilldelar en enda Azure AD-användare till Elium för att testa den automatiska konfigurationen av användar etablering. Fler användare och grupper kan tilldelas senare.

När du tilldelar en användare till Elium måste du välja en giltig, programspecifik roll (om det finns några) i dialog rutan tilldelning. Användare som har **standard åtkomst** rollen undantas från etablering.

## <a name="set-up-elium-for-provisioning"></a>Konfigurera Elium för etablering

Innan du konfigurerar Elium för automatisk användar etablering med Azure AD måste du aktivera system för etablering av SCIM (Cross-Domain Identity Management) på Elium. Följ de här stegen:

1. Logga in på Elium och gå till **Mina profil**  >  **Inställningar**.

    ![Meny alternativet Inställningar i Elium](media/Elium-provisioning-tutorial/setting.png)

1. I det nedre vänstra hörnet, under **Avancerat**, väljer du **säkerhet**.

    ![Säkerhets länk i Elium](media/Elium-provisioning-tutorial/security.png)

1. Kopiera **klient-URL** och värden för **hemliga token** . Du kommer att använda dessa värden senare i motsvarande fält på fliken **etablering** i Elium-programmet i Azure Portal.

    ![Fält för klient-URL och hemligt token i Elium](media/Elium-provisioning-tutorial/token.png)

## <a name="add-elium-from-the-gallery"></a>Lägg till Elium från galleriet

Om du vill konfigurera Elium för automatisk användar etablering med Azure AD måste du också lägga till Elium från Azure AD-programgalleriet till din lista över hanterade program som en tjänst (SaaS)-program. Följ de här stegen:

1. Välj **Azure Active Directory**i den vänstra navigerings panelen i [Azure Portal](https://portal.azure.com).

    ![Meny alternativ för Azure Active Directory](common/select-azuread.png)

1. Gå till **företags program**och välj sedan **alla program**.

     ![Bladet Azure AD Enterprise-program](common/enterprise-applications.png)

1. Om du vill lägga till ett nytt program väljer du **nytt program** längst upp i fönstret.

    ![Ny program länk](common/add-new-app.png)

1. I rutan Sök skriver du **Elium**, väljer **Elium** i listan resultat och väljer sedan **Lägg** till för att lägga till programmet.

    ![Sökruta för Galleri](common/search-new-app.png)

## <a name="configure-automatic-user-provisioning-to-elium"></a>Konfigurera automatisk användar etablering till Elium

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper i Elium, baserat på användar-och grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera enkel inloggning för Elium baserat på Security Assertion Markup Language (SAML) genom att följa anvisningarna i [självstudien om enkel inloggning med Elium](Elium-tutorial.md). Du kan konfigurera enkel inloggning oberoende av automatisk användar etablering, även om de två funktionerna kompletterar varandra.

Följ dessa steg om du vill konfigurera automatisk användar etablering för Elium i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com), Välj **företags program**och välj sedan **alla program**.

    ![Bladet Azure AD Enterprise-program](common/enterprise-applications.png)

1. I listan med program väljer du **Elium**.

    ![Listan program på bladet företags program](common/all-applications.png)

1. Välj fliken **etablering** .

    ![Fliken etablering på bladet företags program](common/provisioning.png)

1. Ställ in **etablerings läget** på **automatiskt**.

    ![Automatisk inställning för etablerings läge](common/provisioning-automatic.png)

1. I avsnittet **admin-autentiseringsuppgifter** skriver du ** \<tenantURL\> /scim/v2** i fältet **klient-URL** . ( **TenantURL** är det värde som hämtades tidigare från administratörs konsolen för Elium.) Skriv också värdet Elium **Secret token** i fältet **hemligt token** . Välj slutligen **Testa anslutning** för att kontrol lera att Azure AD kan ansluta till Elium. Om anslutningen Miss lyckas kontrollerar du att Elium-kontot har administratörs behörighet och försöker igen.

    ![Fälten för klient-URL och hemlig token i admin-autentiseringsuppgifter](common/provisioning-testconnection-tenanturltoken.png)

1. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelandena. Markera sedan kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande](common/provisioning-notification-email.png)

1. Klicka på **Spara**.

1. I avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Elium**.

    ![Synkronisera länk för mappning av Azure AD-användare till Elium](media/Elium-provisioning-tutorial/usermapping.png)

1. Granska de användarattribut som synkroniseras från Azure AD till Elium i avsnittet **mappningar för attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Elium för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna.

    ![Attribut mappningar mellan Azure AD och Elium](media/Elium-provisioning-tutorial/userattribute.png)

1. Följ anvisningarna i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)för att konfigurera omfångs filter.

1. Om du vill aktivera Azure AD Provisioning-tjänsten för Elium ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etablerings statusen har angetts till på](common/provisioning-toggle-on.png)

1. Definiera de användare och grupper som du vill etablera till Elium genom att välja önskade värden i list rutan **omfattning** i avsnittet **Inställningar** .

    ![List rutan etablerings omfång](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Knappen Spara för etablering av konfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den här inledande synkroniseringsprocessen tar längre tid än senare synkroniseringar. Mer information om [hur lång tid det tar att etablera finns i hur lång tid tar det att etablera användare?](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md#how-long-will-it-take-to-provision-users).

Använd avsnittet **aktuell status** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten. Rapporten etablerings aktivitet beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Elium. Mer information finns i [kontrol lera status för användar etablering](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md). För att läsa Azure AD-etablerings loggarna, se [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
