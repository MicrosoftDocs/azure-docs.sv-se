---
title: 'Självstudie: Konfigurera Velpic för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Velpic.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: zhchia
ms.openlocfilehash: fbed4b888c48a518d9f10a91ff0494aa7bdc1843
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88532372"
---
# <a name="tutorial-configuring-velpic-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Velpic för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i Velpic och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till Velpic.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En Velpic-klient med [företags planen](https://www.velpic.com/pricing.html) eller bättre aktive rad
* Ett användar konto i Velpic med administratörs behörighet

## <a name="assigning-users-to-velpic"></a>Tilldela användare till Velpic

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som ska representera de användare som behöver åtkomst till Velpic-appen. När du har bestämt dig kan du tilldela dessa användare till Velpic-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-velpic"></a>Viktiga tips för att tilldela användare till Velpic

* Vi rekommenderar att en enda Azure AD-användare tilldelas till Velpic för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Velpic måste du välja antingen **användar** rollen eller en annan giltig programspecifik roll (om tillgänglig) i tilldelnings dialog rutan. Observera att **standard åtkomst** rollen inte fungerar för etablering, och att dessa användare hoppas över.

## <a name="configuring-user-provisioning-to-velpic"></a>Konfigurera användar etablering till Velpic

Det här avsnittet vägleder dig genom att ansluta din Azure AD till Velpic-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i Velpic baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Velpic enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-account-provisioning-to-velpic-in-azure-ad"></a>Så här konfigurerar du automatisk etablering av användar konton till Velpic i Azure AD:

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program**  .

2. Om du redan har konfigurerat Velpic för enkel inloggning söker du efter din instans av Velpic med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **Velpic** i program galleriet. Välj Velpic från Sök resultaten och Lägg till den i listan över program.

3. Välj din instans av Velpic och välj sedan fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**.

    ![Velpic-etablering](./media/velpic-provisioning-tutorial/Velpic1.png)

5. Under avsnittet **admin credentials** kan du mata in **klient-URL: en&hemlig token** för Velpic. (Du kan hitta de här värdena under ditt Velpic-konto: **Hantera**  >  **Integrering**  >  **Plugin**  >  **Scim**)

    ![Authorization-värden](./media/velpic-provisioning-tutorial/Velpic2.png)

6. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din Velpic-app. Om anslutningen Miss lyckas kontrollerar du att Velpic-kontot har administratörs behörighet och försöker sedan steg 5 igen.

7. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan nedan.

8. Klicka på **Spara**.

9. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till Velpic**.

10. I avsnittet **mappningar för attribut** granskar du de användarattribut som kommer att synkroniseras från Azure AD till Velpic. Observera att attributen som har valts som **matchande** egenskaper kommer att användas för att matcha användar kontona i Velpic för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

11. Om du vill aktivera Azure AD Provisioning-tjänsten för Velpic ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

12. Klicka på **Spara**.

Detta startar den inledande synkroniseringen av alla användare och/eller grupper som är tilldelade till Velpic i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporter som beskriver alla åtgärder som utförs av etablerings tjänsten.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)