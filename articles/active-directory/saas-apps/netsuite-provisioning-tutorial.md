---
title: 'Självstudie: Konfigurera Netsuite-OneWorld för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och Netsuite-OneWorld.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: b1c03bafd6d97dd6a60defee00d4efe854315631
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101648092"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Netsuite för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i Netsuite-OneWorld och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till Netsuite.

> [!WARNING]
> Den här etablerings integrationen upphör att fungera med lanseringen av NetSuites våren 2021-uppdatering på grund av en ändring i Netsuite-API: er som används av Microsoft för att etablera användare i Netsuite.  Den här uppdateringen når Netsuite-kunder mellan februari och april 2021. Därför kommer etablerings funktionerna i Netsuite-programmet i Azure Active Directory Enterprise App Gallery att tas bort snart. Programmets SSO-funktioner är oförändrade. Microsoft arbetar med Netsuite för att bygga en ny modern etablerings integrering, men det finns för närvarande ingen ETA på när detta kommer att slutföras.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient.
*   En Netsuite OneWorld-prenumeration. Observera att automatisk användar etablering bara stöds med Netsuite-OneWorld.
*   Ett användar konto i Netsuite med administratörs behörighet.
*   För integrering med Azure AD krävs ett 2FA undantag. Kontakta NetSuites support team för att begära detta undantag.

## <a name="assigning-users-to-netsuite-oneworld"></a>Tilldela användare till Netsuite-OneWorld

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din Netsuite-app. När du har bestämt dig kan du tilldela dessa användare till din Netsuite-app genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-netsuite-oneworld"></a>Viktiga tips för att tilldela användare Netsuite-OneWorld

*   Vi rekommenderar att en enda Azure AD-användare tilldelas till Netsuite för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Netsuite måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera användar etablering

Det här avsnittet vägleder dig genom att ansluta Azure AD till NetSuites etablerings-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i Netsuite baserat på användare och grupp tilldelning i Azure AD.

> [!TIP] 
> Du kan också välja att aktivera SAML-baserade enkla Sign-On för Netsuite genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera användar konto etablering:

Syftet med det här avsnittet är att skapa en översikt över hur du aktiverar användar etablering av Active Directory användar konton till Netsuite.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

1. Om du redan har konfigurerat Netsuite för enkel inloggning söker du efter din instans av Netsuite med Sök fältet. Annars väljer du **Lägg till** och söker efter **Netsuite** i program galleriet. Välj Netsuite från Sök resultaten och Lägg till det i listan över program.

1. Välj din instans av Netsuite och välj fliken **etablering** .

1. Ange **Etableringsläge** som **Automatiskt**. 

    ![Skärm bild som visar Netsuite-etablerings sidan med etablerings läget inställt på automatiskt och andra värden som du kan ange.](./media/netsuite-provisioning-tutorial/provisioning.png)

1. Ange följande konfigurations inställningar under avsnittet **admin credentials** :
   
    a. I text rutan **Administratörs användar namn** anger du ett namn på Netsuite-kontot som har **system administratörs** profilen i Netsuite.com tilldelad.
   
    b. I text rutan **Administratörs lösen ord** skriver du lösen ordet för det här kontot.
      
1. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din Netsuite-app.

1. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan.

1. Klicka på **Spara.**

1. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till Netsuite.**

1. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till Netsuite. Observera att attributen som har valts som **matchande** egenskaper används för att matcha användar kontona i Netsuite för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

1. Om du vill aktivera Azure AD Provisioning-tjänsten för Netsuite ändrar du **etablerings statusen** till **på** i avsnittet Inställningar

1. Klicka på **Spara.**

Den första synkroniseringen av alla användare och/eller grupper som är kopplade till Netsuite påbörjas i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i Netsuite-appen.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](netsuite-tutorial.md)
