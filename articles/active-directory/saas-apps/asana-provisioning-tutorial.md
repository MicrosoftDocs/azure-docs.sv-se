---
title: 'Självstudie: användar etablering för asana – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till asana.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.reviewer: celested
ms.openlocfilehash: 11e0606568fdaad20f996a7ae20a622576acea15
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549474"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>Självstudie: Konfigurera asana för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i asana och Azure Active Directory (Azure AD) för att automatiskt etablera och avetablera användar konton från Azure AD till asana.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure AD-klient
* En asana-klient med en [företags](https://www.asana.com/pricing) plan eller bättre aktive rad
* Ett användar konto i asana med administratörs behörighet

> [!NOTE]
> Integreringen av Azure AD provisioning är beroende av [asana-API: et](https://asana.com/developers/api-reference/users), som är tillgängligt för asana.

## <a name="assign-users-to-asana"></a>Tilldela användare till asana

Azure AD använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk etablering av användar konton synkroniseras endast de användare som är tilldelade till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare i Azure AD som behöver åtkomst till asana-appen. Sedan kan du tilldela dessa användare till asana-appen genom att följa anvisningarna här:

[Tilldela en användare till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>Viktiga tips för att tilldela användare till asana

Vi rekommenderar att du tilldelar en enda Azure AD-användare till asana för att testa etablerings konfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configure-user-provisioning-to-asana"></a>Konfigurera användar etablering till asana

Det här avsnittet vägleder dig genom att ansluta din Azure AD till asana-API för användar konto. Du konfigurerar också etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i asana baserat på användar tilldelningar i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för asana, följer du anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>Konfigurera automatisk användar konto etablering till asana i Azure AD

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory**  >  **Enterprise Apps**  >  **alla program** .

1. Om du redan har konfigurerat asana för enkel inloggning söker du efter din instans av asana med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **asana** i program galleriet. Välj **asana** från Sök resultaten och Lägg till den i listan över program.

1. Välj din instans av asana och välj sedan fliken **etablering** .

1. Ange **etablerings läget** till **Automatisk**.

    ![Asana-etablering](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. Under avsnittet **admin credentials** , följer du dessa anvisningar för att generera token och ange den i  **hemlig token**:

    a. Logga in på [asana](https://app.asana.com) med ditt administratörs konto.

    b. Välj profil fotot i det översta fältet och välj dina aktuella inställningar för organisations namn.

    c. Gå till fliken **tjänst konton** .

    d. Välj **Lägg till tjänst konto**.

    e. Uppdatera **namnet** och **om** och profil fotot efter behov. Kopiera token i **token**och välj den i **Spara ändringar**.

1. I Azure Portal väljer du **Testa anslutning** för att se till att Azure AD kan ansluta till din asana-app. Om anslutningen Miss lyckas kontrollerar du att ditt asana-konto har administratörs behörighet och provar steget **Testa anslutning** igen.

1. Ange e-postadressen till en person eller grupp som du vill få etablerings fel meddelanden i  **e-postaviseringar**. Markera kryss rutan under.

1. Välj **Spara**.

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till asana**.

1. I avsnittet **mappningar för attribut** granskar du de användarattribut som ska synkroniseras från Azure AD till asana. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i asana för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna. Mer information finns i [Anpassa mappningar av användar etablerings attribut](../app-provisioning/customize-application-attributes.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för asana går du till avsnittet **Inställningar** och ändrar **etablerings statusen** till **på**.

1. Välj **Spara**.

Nu startar den första synkroniseringen för alla användare som tilldelats asana i avsnittet **användare** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Använd avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablering av aktivitets loggar. Gransknings loggarna beskriver alla åtgärder som utförs av etablerings tjänsten i asana-appen.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapporten om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](asana-tutorial.md)
