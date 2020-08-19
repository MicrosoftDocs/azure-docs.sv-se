---
title: 'Självstudie: Konfigurera jives för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och jives.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: fec959ee34f9491b6510390d4b390e652690897e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547094"
---
# <a name="tutorial-configure-jive-for-automatic-user-provisioning"></a>Självstudie: Konfigurera jives för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i jives och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till jives.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient.
*   En jives-aktiverad prenumeration med enkel inloggning.
*   Ett användar konto i jives med grupp administratörs behörighet.

## <a name="assigning-users-to-jive"></a>Tilldela användare till jives

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till jives-appen. När du har bestämt dig kan du tilldela dessa användare till jives-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-jive"></a>Viktiga tips för att tilldela användare till jives

*   Vi rekommenderar att en enda Azure AD-användare tilldelas till jives för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till jives måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera användar etablering

Det här avsnittet vägleder dig genom att ansluta din Azure AD till jives-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i jives baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för jives enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera användar konto etablering:

Syftet med det här avsnittet är att skapa en översikt över hur du aktiverar användar etablering av Active Directory användar konton till jives.
Som en del av den här proceduren måste du ange en säkerhetstoken för användare som du måste begära från Jive.com.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

1. Om du redan har konfigurerat jives för enkel inloggning söker du efter din instans av jives med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **jives** i program galleriet. Välj jives från Sök resultaten och Lägg till den i listan över program.

1. Välj din instans av jives och välj sedan fliken **etablering** .

1. Ställ in **etablerings läget** på **automatiskt**. 

    ![etablerings](./media/jive-provisioning-tutorial/provisioning.png)

1. Ange följande konfigurations inställningar under avsnittet **admin credentials** :
   
    a. I text rutan **Administratörs användar namn för jives** anger du ett jives-kontonamn som har **system administratörs** profilen i Jive.com tilldelad.
   
    b. I text rutan **Administratörs lösen ord för jives** anger du lösen ordet för det här kontot.
   
    c. I text rutan för **jives-klientens URL** anger du URL: en för jives-klienten.
      
      > [!NOTE]
      > Jives-klientens URL är URL som används av din organisation för att logga in på jives.  
      > URL: en har vanligt vis följande format: **www. \<organization\> . jive.com**.          

1. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din jives-app.

1. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan nedan.

1. Klicka på **Spara.**

1. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till jives.**

1. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till jives. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i jives för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

1. Om du vill aktivera Azure AD Provisioning-tjänsten för jives ändrar du **etablerings statusen** till **på** i avsnittet Inställningar

1. Klicka på **Spara.**

Den första synkroniseringen av alla användare och/eller grupper som är kopplade till jives startas i avsnittet användare och grupper. Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i jives-appen.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](jive-tutorial.md)
