---
title: 'Självstudie: användar etablering för Pingboard – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Pingboard.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 29e28e20bd9b471604a450ddb36ef867f0608e06
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553770"
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Pingboard för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver följa för att aktivera automatisk etablering och avetablering av användar konton från Azure Active Directory (Azure AD) till Pingboard.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure AD-klient
* Ett Pingboard-klientens [Pro-konto](https://pingboard.com/pricing)
* Ett användar konto i Pingboard med administratörs behörighet

> [!NOTE]
> Integreringen av Azure AD provisioning är beroende av [Pingboard-API: et](https://pingboard.docs.apiary.io/#), som är tillgängligt för ditt konto.

## <a name="assign-users-to-pingboard"></a>Tilldela användare till Pingboard

Azure AD använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda program. I samband med automatisk etablering av användar konton synkroniseras endast de användare som är tilldelade till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare i Azure AD som behöver åtkomst till Pingboard-appen. Sedan kan du tilldela dessa användare till Pingboard-appen genom att följa anvisningarna här:

[Tilldela en användare till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>Viktiga tips för att tilldela användare till Pingboard

Vi rekommenderar att du tilldelar en enda Azure AD-användare till Pingboard för att testa etablerings konfigurationen. Ytterligare användare kan tilldelas senare.

## <a name="configure-user-provisioning-to-pingboard"></a>Konfigurera användar etablering till Pingboard 

Det här avsnittet vägleder dig genom att ansluta din Azure AD till Pingboard-API: et för användar konto. Du konfigurerar också etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i Pingboard som baseras på användar tilldelningar i Azure AD.

> [!TIP]
> Om du vill aktivera SAML-baserad enkel inloggning för Pingboard, följer du anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-ad"></a>Konfigurera automatisk användar konto etablering till Pingboard i Azure AD

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory**  >  **Enterprise Apps**  >  **alla program** .

1. Om du redan har konfigurerat Pingboard för enkel inloggning söker du efter din instans av Pingboard med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **Pingboard** i program galleriet. Välj **Pingboard** från Sök resultaten och Lägg till den i listan över program.

1. Välj din instans av Pingboard och välj sedan fliken **etablering** .

1. Ange **etablerings läget** till **Automatisk**.

    ![Pingboard-etablering](./media/pingboard-provisioning-tutorial/pingboardazureprovisioning.png)

1. Under avsnittet **admin-autentiseringsuppgifter** använder du följande steg:

    a. I **klient webb adress**anger `https://your_domain.pingboard.com/scim/v2` du och ersätter "your_domain" med din riktiga domän.

    b. Logga in på [Pingboard](https://pingboard.com/) med ditt administratörs konto.

    c. Välj **Add-Ons**  >  **integrerings**  >  **Azure Active Directory**för tillägg.

    d. Gå till fliken **Konfigurera** och välj **Aktivera användar etablering från Azure**.

    e. Kopiera token i **OAuth Bearer-token**och ange den i **hemlig token**.

1. I Azure Portal väljer du **Testa anslutning** för att testa att Azure AD kan ansluta till din Pingboard-app. Om anslutningen Miss lyckas testar du att Pingboard-kontot har administratörs behörighet och provar steget **Testa anslutning** igen.

1. Ange e-postadressen till en person eller grupp som du vill få etablerings fel meddelanden i **e-postaviseringar**. Markera kryss rutan under.

1. Välj **Spara**.

1. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Pingboard**.

1. I avsnittet **mappningar för attribut** granskar du de användarattribut som ska synkroniseras från Azure AD till Pingboard. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Pingboard för uppdaterings åtgärder. Välj **Spara** för att genomföra ändringarna. Mer information finns i [Anpassa mappningar av användar Provisioning-attribut](../app-provisioning/customize-application-attributes.md).

1. Om du vill aktivera Azure AD Provisioning-tjänsten för Pingboard går du till avsnittet **Inställningar** och ändrar **etablerings statusen** till **på**.

1. Välj **Spara** för att starta den första synkroniseringen av användare som är tilldelade till Pingboard.

Den första synkroniseringen tar längre tid att köra än följande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Använd avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablering av aktivitets loggar. Loggarna beskriver alla åtgärder som vidtas av etablerings tjänsten i din Pingboard-app.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapporten om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](pingboard-tutorial.md)
