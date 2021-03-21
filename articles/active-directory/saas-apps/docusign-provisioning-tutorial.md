---
title: 'Självstudie: Konfigurera DocuSign för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och DocuSign.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 71f95b08584a46fccb0975cd9285150573ac02d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218527"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Självstudie: Konfigurera DocuSign för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i DocuSign och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till DocuSign.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient.
*   En aktive rad DocuSign-prenumeration med enkel inloggning.
*   Ett användar konto i DocuSign med grupp administratörs behörighet.

## <a name="assigning-users-to-docusign"></a>Tilldela användare till DocuSign

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till DocuSign-appen. När du har bestämt dig kan du tilldela dessa användare till DocuSign-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Viktiga tips för att tilldela användare till DocuSign

*   Vi rekommenderar att en enda Azure AD-användare tilldelas DocuSign för att testa etablerings konfigurationen. Ytterligare användare kan tilldelas senare.

*   När du tilldelar en användare till DocuSign måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

> [!NOTE]
> Azure AD har inte stöd för grupp etablering med DocuSign-programmet, endast användare kan tillhandahållas.

## <a name="enable-user-provisioning"></a>Aktivera användar etablering

Det här avsnittet vägleder dig genom att ansluta din Azure AD till DocuSign-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i DocuSign baserat på användar-och grupp tilldelning i Azure AD.

> [!Tip]
> Du kan också välja att aktivera SAML-baserade enkla Sign-On för DocuSign, genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera användar konto etablering:

Syftet med det här avsnittet är att skapa en översikt över hur du aktiverar användar etablering av Active Directory användar konton till DocuSign.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

1. Om du redan har konfigurerat DocuSign för enkel inloggning söker du efter din instans av DocuSign med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **DocuSign** i program galleriet. Välj DocuSign från Sök resultaten och Lägg till den i listan över program.

1. Välj din instans av DocuSign och välj sedan fliken **etablering** .

1. Ange **Etableringsläge** som **Automatiskt**. 

    ![Skärm bild av fliken etablering för DocuSign i Azure Portal. Etablerings läget är inställt på automatiskt och administratörens användar namn, lösen ord och test anslutning är markerat.](./media/docusign-provisioning-tutorial/provisioning.png)

1. Ange följande konfigurations inställningar under avsnittet **admin credentials** :
   
    a. I text rutan **Administratörs användar namn** anger du ett DocuSign-kontonamn som har **system administratörs** profilen i DocuSign.com tilldelad.
   
    b. I text rutan **Administratörs lösen ord** skriver du lösen ordet för det här kontot.

> [!NOTE]
> Om både SSO och användar etablering har kon figurer ATS måste de autentiseringsuppgifter som används för etablering konfigureras för att fungera med både SSO och username/Password.

1. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din DocuSign-app.

1. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan.

1. Klicka på **Spara.**

1. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till DocuSign.**

1. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till DocuSign. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i DocuSign för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

1. Om du vill aktivera Azure AD Provisioning-tjänsten för DocuSign ändrar du **etablerings statusen** till **på** i avsnittet Inställningar

1. Klicka på **Spara.**

Den första synkroniseringen av alla användare som tilldelats DocuSign i avsnittet användare och grupper startas. Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i DocuSign-appen.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="troubleshooting-tips"></a>Felsökningstips
* Etablering av en roll-eller behörighets profil för en användare i DocuSign kan utföras med hjälp av ett uttryck i dina mappningar av attribut med hjälp av funktionerna [switch](../app-provisioning/functions-for-customizing-application-data.md#switch) och [singleAppRoleAssignment](../app-provisioning/functions-for-customizing-application-data.md#singleapproleassignment) . Uttrycket nedan etablerar till exempel ID "8032066" när en användare har rollen "DS-administratör" tilldelad i Azure AD. Ingen behörighets profil etableras om användaren inte har tilldelats någon roll på Azure AD-sidan. ID: t kan hämtas från DocuSign- [portalen](https://support.docusign.com/articles/Default-settings-for-out-of-the-box-DocuSign-Permission-Profiles).

Switch (SingleAppRoleAssignment ([appRoleAssignments]), "", "DS-administratör", "8032066")


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](docusign-tutorial.md)
