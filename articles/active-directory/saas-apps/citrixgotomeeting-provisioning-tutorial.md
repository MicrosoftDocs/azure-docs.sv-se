---
title: 'Självstudie: Konfigurera GoToMeeting för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GoToMeeting.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 23af7ff9e4740b88d3d1f8cffb3d79a46c8d0d61
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544459"
---
# <a name="tutorial-configure-gotomeeting-for-automatic-user-provisioning"></a>Självstudie: Konfigurera GoToMeeting för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i GoToMeeting och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till GoToMeeting.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient.
*   En aktive rad GoToMeeting-prenumeration med enkel inloggning.
*   Ett användar konto i GoToMeeting med grupp administratörs behörighet.

## <a name="assigning-users-to-gotomeeting"></a>Tilldela användare till GoToMeeting

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till GoToMeeting-appen. När du har bestämt dig kan du tilldela dessa användare till GoToMeeting-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-gotomeeting"></a>Viktiga tips för att tilldela användare till GoToMeeting

*   Vi rekommenderar att en enda Azure AD-användare tilldelas GoToMeeting för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till GoToMeeting måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användar etablering

Det här avsnittet vägleder dig genom att ansluta din Azure AD till GoToMeeting-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i GoToMeeting baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för GoToMeeting enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användar konto:

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

1. Om du redan har konfigurerat GoToMeeting för enkel inloggning söker du efter din instans av GoToMeeting med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **GoToMeeting** i program galleriet. Välj GoToMeeting från Sök resultaten och Lägg till den i listan över program.

1. Välj din instans av GoToMeeting och välj sedan fliken **etablering** .

1. Ställ in **etablerings** läget på **automatiskt**. 

    ![etablerings](./media/citrixgotomeeting-provisioning-tutorial/provisioning.png)

1. Under avsnittet admin credentials, utför följande steg:
   
    a. Skriv användar namnet för en administratör i text rutan **GoToMeeting admin-användarnamn** .

    b. Administratörens lösen ord i text rutan administratörs **lösen ord för GoToMeeting** .

1. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din GoToMeeting-app. Om anslutningen Miss lyckas kontrollerar du att ditt GoToMeeting-konto har team administratörs behörighet och försöker sedan igen med **"admin credentials"** .

1. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan.

1. Klicka på **Spara.**

1. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till GoToMeeting.**

1. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till GoToMeeting. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i GoToMeeting för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

1. Om du vill aktivera Azure AD Provisioning-tjänsten för GoToMeeting ändrar du **etablerings statusen** till **på** i avsnittet Inställningar

1. Klicka på **Spara.**

Den första synkroniseringen av alla användare och/eller grupper som är kopplade till GoToMeeting startas i avsnittet användare och grupper. Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i GoToMeeting-appen.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](https://docs.microsoft.com/azure/active-directory/active-directory-saas-citrix-gotomeeting-tutorial)


