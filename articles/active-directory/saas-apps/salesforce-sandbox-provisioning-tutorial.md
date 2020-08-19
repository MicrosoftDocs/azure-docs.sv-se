---
title: 'Självstudie: Konfigurera Salesforce-Sandbox för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Salesforce-Sandbox.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 4666768d7e975e976601810bbbfadb1685fa91ff
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88543531"
---
# <a name="tutorial-configure-salesforce-sandbox-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Salesforce-Sandbox för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i Salesforce-Sandbox och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till Salesforce-Sandbox.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient.
*   En giltig klient för Salesforce-Sandbox för utbildning i arbets-eller Salesforce-läge. Du kan använda ett kostnads fritt utvärderings konto för båda tjänsterna.
*   Ett användar konto i Salesforce Sandbox med team administratörs behörighet.

## <a name="assigning-users-to-salesforce-sandbox"></a>Tilldela användare till Salesforce Sandbox

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till din Salesforce Sandbox-app. När du har gjort det här beslutet kan du tilldela dessa användare till din Salesforce Sandbox-app genom att följa instruktionerna i [tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce-sandbox"></a>Viktiga tips för att tilldela användare till Salesforce Sandbox

* Vi rekommenderar att en enda Azure AD-användare tilldelas Salesforce-Sandbox för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Salesforce-Sandbox måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

> [!NOTE]
> Den här appen importerar anpassade roller från Salesforce-Sandbox som en del av etablerings processen, som kunden kanske vill välja när de tilldelar användare.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användar etablering

Det här avsnittet vägleder dig genom att ansluta din Azure AD till Salesforce-Sandboxs API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i Salesforce-Sandbox baserat på användar-och grupp tilldelning i Azure AD.

>[!Tip]
>Du kan också välja att aktivera SAML-baserad enkel inloggning för Salesforce Sandbox, enligt instruktionerna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användar konto

Syftet med det här avsnittet är att skapa en översikt över hur du aktiverar användar etablering av Active Directory användar konton till Salesforce-Sandbox.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

1. Om du redan har konfigurerat Salesforce-Sandbox för enkel inloggning söker du efter din instans av Salesforce Sandbox med Sök fältet. Annars väljer du **Lägg till** och söker efter **Salesforce-Sandbox** i program galleriet. Välj Salesforce-Sandbox från Sök resultaten och Lägg till den i listan över program.

1. Välj din instans av Salesforce Sandbox och välj sedan fliken **etablering** .

1. Ställ in **etablerings läget** på **automatiskt**.

    ![etablerings](./media/salesforce-sandbox-provisioning-tutorial/provisioning.png)

1. Ange följande konfigurations inställningar under avsnittet **admin credentials** :
   
    a. I text rutan **admin-användar** namn anger du ett konto namn för Salesforce-Sandbox som har **system administratörs** profilen i Salesforce.com tilldelad.
   
    b. I text rutan **Administratörs lösen ord** skriver du lösen ordet för det här kontot.

1. Öppna en ny flik och logga in på samma Salesforce Sandbox-administratörskonto för att få en säkerhetstoken för Salesforce. Klicka på ditt namn i det övre högra hörnet på sidan och klicka sedan på **Inställningar**.

     ![Aktivera automatisk användar etablering](./media/salesforce-sandbox-provisioning-tutorial/sf-my-settings.png "Aktivera automatisk användar etablering")

1. I det vänstra navigerings fönstret klickar du på **Mina personliga uppgifter** för att expandera det relaterade avsnittet och klicka sedan på **Återställ min säkerhetstoken**.
  
    ![Aktivera automatisk användar etablering](./media/salesforce-sandbox-provisioning-tutorial/sf-personal-reset.png "Aktivera automatisk användar etablering")

1. Klicka på knappen **Återställ säkerhetstoken** på sidan **Återställ** säkerhetstoken.

    ![Aktivera automatisk användar etablering](./media/salesforce-sandbox-provisioning-tutorial/sf-reset-token.png "Aktivera automatisk användar etablering")

1. Kontrol lera e-postinkorgen som är associerad med det här administratörs kontot. Sök efter ett e-postmeddelande från Salesforce-Sandbox.com som innehåller den nya säkerhetstoken.

1. Kopiera token, gå till Azure AD-fönstret och klistra in den i fältet **hemlig token** .

1. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din Salesforce Sandbox-app.

1. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan.

1. Klicka på **Spara.**  
    
1.  Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till Salesforce-Sandbox.**

1. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till Salesforce-Sandbox. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Salesforce-Sandbox för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

1. Om du vill aktivera Azure AD Provisioning-tjänsten för Salesforce-Sandbox ändrar du **etablerings statusen** till **på** i avsnittet Inställningar

1. Klicka på **Spara.**

Den första synkroniseringen av alla användare och/eller grupper som tilldelats Salesforce-Sandbox startas i avsnittet användare och grupper. Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i Salesforce Sandbox-appen.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-sandbox-tutorial)
