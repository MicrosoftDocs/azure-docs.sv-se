---
title: 'Självstudie: användar etablering – LinkedIn Sales Navigator, Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till LinkedIn Sales Navigator.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: ef87a1c7163825fcee8f4a27408719839d8a6d43
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549712"
---
# <a name="tutorial-configure-linkedin-sales-navigator-for-automatic-user-provisioning"></a>Självstudie: Konfigurera LinkedIn Sales Navigator för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i LinkedIn Sales Navigator och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till LinkedIn Sales Navigator.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En LinkedIn Sales Navigator-klient 
* Ett administratörs konto i LinkedIn Sales Navigator med åtkomst till LinkedIn-konto Center

> [!NOTE]
> Azure Active Directory integreras med LinkedIn Sales Navigator med [scim](http://www.simplecloud.info/) -protokollet.

## <a name="assigning-users-to-linkedin-sales-navigator"></a>Tilldela användare till LinkedIn Sales Navigator

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som ska representera de användare som behöver åtkomst till LinkedIn Sales Navigator. När du har bestämt dig kan du tilldela dessa användare till LinkedIn Sales Navigator genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-sales-navigator"></a>Viktiga tips för att tilldela användare till LinkedIn Sales Navigator

* Vi rekommenderar att en enda Azure AD-användare tilldelas till LinkedIn Sales Navigator för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till LinkedIn Sales Navigator måste du välja **användar** rollen i tilldelnings dialog rutan. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="configuring-user-provisioning-to-linkedin-sales-navigator"></a>Konfigurera användar etablering till LinkedIn Sales Navigator

I det här avsnittet får du hjälp med att ansluta din Azure AD till LinkedIn-SCIM för användar konton och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i LinkedIn Sales Navigator, baserat på användar-och grupp tilldelning i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för LinkedIn Sales Navigator genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-sales-navigator-in-azure-ad"></a>Konfigurera automatisk användar konto etablering till LinkedIn Sales Navigator i Azure AD:

Det första steget är att hämta din LinkedIn-åtkomsttoken. Om du är företags administratör kan du själv etablera en åtkomsttoken. I ditt konto Center går du till **inställningar &gt; globala inställningar** och öppnar installations panelen för **scim** .

> [!NOTE]
> Om du ansluter till konto Center direkt i stället för via en länk kan du nå det med hjälp av följande steg.

1. Logga in på konto Center.

2. Välj **admin &gt; admin-inställningar** .

3. Klicka på **avancerade integreringar** på den vänstra sid panelen. Du dirigeras till konto centret.

4. Klicka på **+ Lägg till ny scim-konfiguration** och följ proceduren genom att fylla i varje fält.

    > [!NOTE]
    > När tilldela licenser inte är aktiverat innebär det att endast användar data synkroniseras.

    ![LinkedIn Sales Navigator-etablering](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_1.PNG)

    > [!NOTE]
    > När autolicense-tilldelning har Aktiver ATS måste du anteckna program instansen och licens typen. Licenser tilldelas för första och första gången tills alla licenser har vidtagits.

    ![LinkedIn Sales Navigator-etablering](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_2.PNG)

5. Klicka på **generera token**. Du bör se att din åtkomsttoken visas under fältet **åtkomsttoken** .

6. Spara din åtkomsttoken i Urklipp eller på datorn innan du lämnar sidan.

7. Logga sedan in på [Azure Portal](https://portal.azure.com)och bläddra till avsnittet **Azure Active Directory > Enterprise-appar > alla program**  .

8. Om du redan har konfigurerat LinkedIn Sales Navigator för enkel inloggning söker du efter din instans av LinkedIn Sales Navigator med Sök fältet. Annars väljer du **Lägg till** och söker efter **LinkedIn Sales Navigator** i program galleriet. Välj LinkedIn Sales Navigator från Sök resultaten och Lägg till den i listan över program.

9. Välj din instans av LinkedIn Sales Navigator och välj fliken **etablering** .

10. Ställ in **etablerings läget** på **automatiskt**.

    ![LinkedIn Sales Navigator-etablering](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_3.PNG)

11. Fyll i följande fält under **admin-autentiseringsuppgifter** :

    * I fältet **klient-URL** anger du https://developer.linkedin.com .

    * I fältet **hemlig token** anger du den åtkomsttoken som du skapade i steg 1 och klickar på **Testa anslutning** .

    * Du bör se ett meddelande om att det är klart på upperright-sidan i portalen.

12. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan nedan.

13. Klicka på **Spara**.

14. I avsnittet **mappningar för attribut** granskar du de användar-och Gruppattribut som ska synkroniseras från Azure AD till LinkedIn Sales Navigator. Observera att attributen som har valts som **matchande** egenskaper kommer att användas för att matcha användar konton och grupper i LinkedIn Sales Navigator för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

    ![LinkedIn Sales Navigator-etablering](./media/linkedinsalesnavigator-provisioning-tutorial/linkedin_4.PNG)

15. Om du vill aktivera Azure AD Provisioning-tjänsten för LinkedIn Sales Navigator ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

16. Klicka på **Spara**.

Detta startar den inledande synkroniseringen av alla användare och/eller grupper som har tilldelats LinkedIn Sales Navigator i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i din LinkedIn Sales Navigator-app.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
