---
title: 'Självstudie: användar etablering för ThousandEyes – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till ThousandEyes.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: a009361267a364f04237716f3623921c892e2708
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546431"
---
# <a name="tutorial-configure-thousandeyes-for-automatic-user-provisioning"></a>Självstudie: Konfigurera ThousandEyes för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i ThousandEyes och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till ThousandEyes. 

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klient
* En ThousandEyes-klient med [standard planen](https://www.thousandeyes.com/pricing) eller bättre aktive rad 
* Ett användar konto i ThousandEyes med administratörs behörighet 

> [!NOTE]
> Integreringen med Azure AD provisioning är beroende av [THOUSANDEYES scim-API: et](https://success.thousandeyes.com/PublicArticlePage?articleIdParam=kA044000000CnWrCAK), som är tillgängligt för ThousandEyes-team i standard planen eller bättre.

## <a name="assigning-users-to-thousandeyes"></a>Tilldela användare till ThousandEyes

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till ThousandEyes-appen. När du har bestämt dig kan du tilldela dessa användare till ThousandEyes-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-thousandeyes"></a>Viktiga tips för att tilldela användare till ThousandEyes

* Vi rekommenderar att en enda Azure AD-användare tilldelas ThousandEyes för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till ThousandEyes måste du välja antingen **användar** rollen eller en annan giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. **Standard åtkomst** rollen fungerar inte för etablering, och dessa användare hoppas över.

## <a name="configuring-user-provisioning-to-thousandeyes"></a>Konfigurera användar etablering till ThousandEyes 

Det här avsnittet vägleder dig genom att ansluta din Azure AD till ThousandEyes-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i ThousandEyes baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för ThousandEyes enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="configure-automatic-user-account-provisioning-to-thousandeyes-in-azure-ad"></a>Konfigurera automatisk etablering av användar konton till ThousandEyes i Azure AD

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program**  .

2. Om du redan har konfigurerat ThousandEyes för enkel inloggning söker du efter din instans av ThousandEyes med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **ThousandEyes** i program galleriet. Välj ThousandEyes från Sök resultaten och Lägg till den i listan över program.

3. Välj din instans av ThousandEyes och välj sedan fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**.

    ![ThousandEyes-etablering](./media/thousandeyes-provisioning-tutorial/ThousandEyes1.png)

5. Under avsnittet **admin credentials**  kan du mata in **OAuth Bearer-token** som genererats av ditt ThousandEyes-konto (du kan söka efter och eller generera en token under din ThousandEyes konto **profil** ).

    ![ThousandEyes-etablering](./media/thousandeyes-provisioning-tutorial/ThousandEyes2.png)

6. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din ThousandEyes-app. Om anslutningen Miss lyckas kontrollerar du att ThousandEyes-kontot har administratörs behörighet och försöker sedan steg 5 igen.

7. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan "Skicka ett e-postmeddelande när ett fel inträffar".

8. Klicka på **Spara**.

9. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till ThousandEyes**.

10. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till ThousandEyes. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i ThousandEyes för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

11. Om du vill aktivera Azure AD Provisioning-tjänsten för ThousandEyes ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

12. Klicka på **Spara**.

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som har tilldelats ThousandEyes i avsnittet användare och grupper. Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)
