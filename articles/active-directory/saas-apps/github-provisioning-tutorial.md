---
title: 'Självstudie: användar etablering för GitHub – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till GitHub.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 7408d7609cbceb4ac39298680b6d3854a2d71306
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589222"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Självstudie: Konfigurera GitHub för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i GitHub och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till GitHub.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klient
* En GitHub-organisation som har skapats i [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) och kräver [GitHub Enterprise-faktureringsplanen](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)
* Ett användar konto i GitHub med administratörs behörighet till organisationen
* Se till att OAuth-åtkomst har angetts för din organisation enligt beskrivningen [här](https://help.github.com/en/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization)
* SCIM-etablering till en enskild organisation stöds bara när SSO är aktiverat på organisations nivå

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av [GITHUB scim-API: et](https://developer.github.com/v3/scim/), som är tillgängligt för [GitHub företags moln](https://help.github.com/articles/github-s-products/#github-enterprise) kunder på [företagets fakturerings plan för GitHub](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations).

## <a name="assigning-users-to-github"></a>Tilldela användare till GitHub

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till GitHub-appen. När du har bestämt dig kan du tilldela dessa användare till GitHub-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Viktiga tips för att tilldela användare till GitHub

* Vi rekommenderar att en enda Azure AD-användare tilldelas GitHub för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till GitHub måste du välja antingen **användar** rollen eller en annan giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. **Standard åtkomst** rollen fungerar inte för etablering, och dessa användare hoppas över.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurera användar etablering till GitHub

Det här avsnittet vägleder dig genom att ansluta din Azure AD till GitHub-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i GitHub baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för GitHub enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurera automatisk etablering av användar konton till GitHub i Azure AD

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program**  .

2. Om du redan har konfigurerat GitHub för enkel inloggning söker du efter din instans av GitHub med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **GitHub** i program galleriet. Välj GitHub från Sök resultaten och Lägg till den i listan över program.

3. Välj din instans av GitHub och välj sedan fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**.

    ![GitHub-etablering](./media/github-provisioning-tutorial/GitHub1.png)

5. Under avsnittet **admin credentials** klickar du på **auktorisera**. Den här åtgärden öppnar en dialog ruta för GitHub-auktorisering i ett nytt webbläsarfönster. Observera att du måste kontrol lera att du har godkänts för att godkänna åtkomst. Följ anvisningarna som beskrivs [här](https://help.github.com/github/setting-up-and-managing-organizations-and-teams/approving-oauth-apps-for-your-organization).

6. Logga in på GitHub med ditt administratörs konto i det nya fönstret. I dialog rutan resulterande auktorisering väljer du det GitHub-team som du vill aktivera etablering för och väljer sedan **auktorisera**. När du är klar återgår du till Azure Portal för att slutföra etablerings konfigurationen.

    ![Dialog rutan auktorisering](./media/github-provisioning-tutorial/GitHub2.png)

7. I Azure Portal, ingångs **klientens URL** och klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till din GitHub-app. Om anslutningen Miss lyckas kontrollerar du att GitHub-kontot har administratörs behörighet och **klient-URL: en** är felaktigt angiven och försöker sedan igen. försök sedan igen (du kan utgöra **klient webb adress** per regel: `https://api.github.com/scim/v2/organizations/<Organization_name>` , du kan hitta dina organisationer under ditt GitHub-konto: **inställnings**  >  **organisationer**).

    ![Dialog rutan auktorisering](./media/github-provisioning-tutorial/GitHub3.png)

8. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan "Skicka ett e-postmeddelande när ett fel inträffar".

9. Klicka på **Spara**.

10. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till GitHub**.

11. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till GitHub. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i GitHub för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

12. Om du vill aktivera Azure AD Provisioning-tjänsten för GitHub ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

13. Klicka på **Spara**.

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som har tilldelats GitHub i avsnittet användare och grupper. Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
