---
title: 'Självstudie: användar etablering för CERN, centrala – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera användare till en lista i CERN Central.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.openlocfilehash: 58b991f5b229d924bc933ff34987db24bc895e10
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88529873"
---
# <a name="tutorial-configure-cerner-central-for-automatic-user-provisioning"></a>Självstudie: Konfigurera CERN central för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i CERN Central och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till en användar lista i CERN Central.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En CERN-Central klient

> [!NOTE]
> Azure Active Directory integreras med CERN Central med [scim](http://www.simplecloud.info/) -protokollet.

## <a name="assigning-users-to-cerner-central"></a>Tilldela användare till CERN Central

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD. 

Innan du konfigurerar och aktiverar etablerings tjänsten bör du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till CERN Central. När du har bestämt dig kan du tilldela dessa användare till CERN Central genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-cerner-central"></a>Viktiga tips för att tilldela användare till CERN Central

* Vi rekommenderar att en enda Azure AD-användare tilldelas till CERN central för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När den första testningen har slutförts för en enskild användare rekommenderar CERN Central att du tilldelar hela listan över användare som är avsedda att få åtkomst till en CERN-lösning (inte bara CERN Central) som ska tillhandahållas till CERN-användarens användar lista.  Andra CERN-lösningar utnyttjar den här listan med användare i användar listan.

* När du tilldelar en användare till CERN Central måste du välja **användar** rollen i tilldelnings dialog rutan. Användare med rollen "standard åtkomst" undantas från etablering.

## <a name="configuring-user-provisioning-to-cerner-central"></a>Konfigurera användar etablering till CERN Central

Det här avsnittet vägleder dig genom att ansluta din Azure AD till CERN Centrals användar lista med hjälp av CERNs SCIM-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i CERN Central baserat på användare och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för CERN Central, enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra. Mer information finns i [själv studie kursen om enkel inloggning i CERN](cernercentral-tutorial.md).

### <a name="to-configure-automatic-user-account-provisioning-to-cerner-central-in-azure-ad"></a>Konfigurera automatisk användar konto etablering till CERN Central i Azure AD:

För att kunna etablera användar konton till CERN Central måste du begära ett CERN-system konto från CERN, och generera en OAuth Bearer-token som Azure AD kan använda för att ansluta till CERNs SCIM-slutpunkt. Vi rekommenderar också att integrationen utförs i en miljö med CERN-sandbox innan du distribuerar till produktion.

1. Det första steget är att se till att de personer som hanterar CERN-och Azure AD-integreringen har ett CernerCare-konto, vilket krävs för att få åtkomst till den dokumentation som krävs för att slutföra anvisningarna. Om det behövs kan du använda URL: erna nedan för att skapa CernerCare-konton i varje tillämplig miljö.

   * Sammansättningen  https://sandboxcernercare.com/accounts/create

   * Produktions  https://cernercare.com/accounts/create  

2. Därefter måste ett system konto skapas för Azure AD. Använd anvisningarna nedan om du vill begära ett system konto för dina sand Box-och produktions miljöer.

   * Problemlösning  https://wiki.ucern.com/display/CernerCentral/Requesting+A+System+Account

   * Sammansättningen https://sandboxcernercentral.com/system-accounts/

   * Produktions  https://cernercentral.com/system-accounts/

3. Därefter genererar du en OAuth Bearer-token för varje system konto. Det gör du genom att följa anvisningarna nedan.

   * Problemlösning  https://wiki.ucern.com/display/public/reference/Accessing+Cerner%27s+Web+Services+Using+A+System+Account+Bearer+Token

   * Sammansättningen https://sandboxcernercentral.com/system-accounts/

   * Produktions  https://cernercentral.com/system-accounts/

4. Slutligen måste du skaffa användar domän-ID: t för användar domänen för både Sandbox-och produktions miljöerna i CERN för att slutföra konfigurationen. Information om hur du hämtar detta finns i: https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+SCIM . 

5. Nu kan du konfigurera Azure AD för att etablera användar konton till CERN. Logga in på [Azure Portal](https://portal.azure.com)och bläddra till avsnittet **Azure Active Directory > Enterprise-appar > alla program**  .

6. Om du redan har konfigurerat CERN central för enkel inloggning söker du efter din instans av CERN Central med Sök fältet. Annars väljer du **Lägg till** och söker efter **CERN Central** i program galleriet. Välj CERN Central från Sök resultatet och Lägg till det i listan över program.

7. Välj din instans av CERN Central och välj sedan fliken **etablering** .

8. Ställ in **etablerings läget** på **automatiskt**.

   ![CERN, Central etablering](./media/cernercentral-provisioning-tutorial/Cerner.PNG)

9. Fyll i följande fält under **admin-autentiseringsuppgifter**:

   * I fältet **klient-URL** anger du en URL i formatet nedan och ersätter "User-List-REALM-ID" med sfär-ID: t som du fick i steg #4.

    > Sammansättningen https://user-roster-api.sandboxcernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 
    > 
    > Produktions https://user-roster-api.cernercentral.com/scim/v1/Realms/User-Roster-Realm-ID/ 

   * I fältet **hemlig token** anger du den OAuth Bearer-token som du skapade i steg #3 och klickar på **Testa anslutning**.

   * Du bör se ett meddelande om att det är klart på upperright-sidan i portalen.

1. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan nedan.

1. Klicka på **Spara**.

1. I avsnittet **mappningar för attribut** granskar du de användar-och Gruppattribut som ska synkroniseras från Azure AD till CERN Central. Attributen som väljs som **matchande** egenskaper används för att matcha användar konton och grupper i CERN central för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

1. Om du vill aktivera Azure AD Provisioning-tjänsten för CERN Central ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

1. Klicka på **Spara**.

Detta startar den inledande synkroniseringen av alla användare och/eller grupper som tilldelats till CERN Central i avsnittet användare och grupper. Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i din CERN-centrala app.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [CERN, Central: publicera identitets data med Azure AD](https://wiki.ucern.com/display/public/reference/Publishing+Identity+Data+Using+Azure+AD)
* [Självstudie: Konfigurera CERN central för enkel inloggning med Azure Active Directory](cernercentral-tutorial.md)
* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-provisioning-reporting).
