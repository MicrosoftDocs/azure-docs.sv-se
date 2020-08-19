---
title: 'Självstudie: användar etablering för LinkedIn-höjning – Azure AD'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till LinkedIn-höjning.
services: active-directory
author: ArvindHarinder1
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/28/2019
ms.author: arvinh
ms.openlocfilehash: e49b35de5cce914edbd8db2ac0c0c8cccd5f811b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549780"
---
# <a name="tutorial-configure-linkedin-elevate-for-automatic-user-provisioning"></a>Självstudie: Konfigurera LinkedIn-höjning för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra på LinkedIn-höjning och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till LinkedIn-höjning.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

* En Azure Active Directory-klientorganisation
* En LinkedIn-höjnings klient
* Ett administratörs konto på LinkedIn upphöjt med åtkomst till LinkedIn-konto Center

> [!NOTE]
> Azure Active Directory integreras med LinkedIn-höjning med [scim](http://www.simplecloud.info/) -protokollet.

## <a name="assigning-users-to-linkedin-elevate"></a>Tilldela användare till LinkedIn-höjning

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som ska representera de användare som behöver åtkomst till LinkedIn-höjning. När du har bestämt dig kan du tilldela dessa användare till LinkedIn-höjning genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-linkedin-elevate"></a>Viktiga tips för att tilldela användare till LinkedIn-höjning

* Vi rekommenderar att en enda Azure AD-användare tilldelas till LinkedIn-höjning för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till LinkedIn-höjning måste du välja **användar** rollen i tilldelnings dialog rutan. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="configuring-user-provisioning-to-linkedin-elevate"></a>Konfigurera användar etablering för LinkedIn-höjning

Det här avsnittet vägleder dig genom att ansluta din Azure AD till LinkedIn-höjning SCIM-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i LinkedIn-höjning baserat på användar-och grupp tilldelning i Azure AD.

**Tips:** Du kan också välja att aktivera SAML-baserad enkel inloggning för LinkedIn-höjning genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner kompletterar varandra.

### <a name="to-configure-automatic-user-account-provisioning-to-linkedin-elevate-in-azure-ad"></a>Konfigurera automatisk användar konto etablering till LinkedIn-höjning i Azure AD:

Det första steget är att hämta din LinkedIn-åtkomsttoken. Om du är företags administratör kan du själv etablera en åtkomsttoken. I ditt konto Center går du till **inställningar &gt; globala inställningar** och öppnar installations panelen för **scim** .

> [!NOTE]
> Om du ansluter till konto Center direkt i stället för via en länk kan du nå det med hjälp av följande steg.

1. Logga in på konto Center.

2. Välj **admin &gt; admin-inställningar** .

3. Klicka på **avancerade integreringar** på den vänstra sid panelen. Du dirigeras till konto centret.

4. Klicka på **+ Lägg till ny scim-konfiguration** och följ proceduren genom att fylla i varje fält.

    > [!NOTE]
    > När tilldela licenser inte är aktiverat innebär det att endast användar data synkroniseras.

    ![LinkedIn-höjning-etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate1.PNG)

    > [!NOTE]
    > När autolicense-tilldelning har Aktiver ATS måste du anteckna program instansen och licens typen. Licenser tilldelas för första och första gången tills alla licenser har vidtagits.

    ![LinkedIn-höjning-etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate2.PNG)

5. Klicka på **generera token**. Du bör se att din åtkomsttoken visas under fältet **åtkomsttoken** .

6. Spara din åtkomsttoken i Urklipp eller på datorn innan du lämnar sidan.

7. Logga sedan in på [Azure Portal](https://portal.azure.com)och bläddra till avsnittet **Azure Active Directory > Enterprise-appar > alla program**  .

8. Om du redan har konfigurerat LinkedIn-höjning för enkel inloggning kan du söka efter din instans av LinkedIn-höjning med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **LinkedIn-höjning** i program galleriet. Välj LinkedIn Upphöj från Sök resultatet och Lägg till det i listan över program.

9. Välj din instans av LinkedIn-höjning och välj sedan fliken **etablering** .

10. Ställ in **etablerings läget** på **automatiskt**.

    ![LinkedIn-höjning-etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate3.PNG)

11. Fyll i följande fält under **admin-autentiseringsuppgifter** :

    * I fältet **klient-URL** anger du `https://api.linkedin.com` .

    * I fältet **hemlig token** anger du den åtkomsttoken som du skapade i steg 1 och klickar på **Testa anslutning** .

    * Du bör se ett meddelande om att det är klart på upperright-sidan i portalen.

12. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan nedan.

13. Klicka på **Spara**.

14. I avsnittet **mappningar för attribut** granskar du de användar-och Gruppattribut som ska synkroniseras från Azure AD till LinkedIn-höjning. Observera att attributen som har valts som **matchande** egenskaper kommer att användas för att matcha användar konton och grupper i LinkedIn-höjning för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

    ![LinkedIn-höjning-etablering](./media/linkedinelevate-provisioning-tutorial/linkedin_elevate4.PNG)

15. Om du vill aktivera Azure AD Provisioning-tjänsten för LinkedIn-höjning ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

16. Klicka på **Spara**.

Detta startar den inledande synkroniseringen av alla användare och/eller grupper som tilldelats LinkedIn-höjning i avsnittet användare och grupper. Observera att den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i din LinkedIn-höjnings app.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
