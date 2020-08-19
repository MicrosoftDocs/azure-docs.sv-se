---
title: 'Självstudie: Konfigurera box för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och box.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: 76c0f4f021646b4cbf1a09559e085176353a4a3c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88525620"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Självstudie: Konfigurera box för automatisk användar etablering

Syftet med den här självstudien är att visa de steg som du måste utföra i box och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till box.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver följande saker för att konfigurera Azure AD-integrering med Box:

- En Azure AD-klient
- En företags plan eller bättre

> [!NOTE]
> När du testar stegen i den här självstudien rekommenderar vi att du *inte* använder en produktions miljö.

> [!NOTE]
> Appar måste vara aktiverade i Box-programmet först.

Följ dessa rekommendationer för att testa stegen i den här självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en testmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-box"></a>Tilldela användare till Box 

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till din box-app. När du har bestämt dig kan du tilldela dessa användare till din box-app genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

## <a name="assign-users-and-groups"></a>Tilldela användare och grupper
På fliken **> användare och grupper** i Azure Portal kan du ange vilka användare och grupper som ska beviljas åtkomst till box. Tilldelning av en användare eller grupp gör att följande saker sker:

* Azure AD tillåter den tilldelade användaren (antingen genom direkt tilldelning eller grupp medlemskap) att autentisera till box. Om en användare inte är tilldelad tillåter inte Azure AD att de loggar in på Box och returnerar ett fel på inloggnings sidan för Azure AD.
* En app-panel för Box läggs till i användarens [program start](../manage-apps/end-user-experiences.md).
* Om automatisk etablering har Aktiver ATS läggs de tilldelade användarna och/eller grupperna till i etablerings kön som ska tillhandahållas automatiskt.
  
  * Om endast användar objekt har kon figurer ATS för att tillhandahållas placeras alla direkt tilldelade användare i etablerings kön och alla användare som är medlemmar i alla tilldelade grupper placeras i etablerings kön. 
  * Om grupp objekt har kon figurer ATS för att tillhandahållas, är alla tilldelade grupp objekt etablerade i rutan och alla användare som är medlemmar i dessa grupper. Gruppen och användarens medlemskap bevaras vid skrivning till box.

Du kan använda fliken **attribut > enkel inloggning** för att konfigurera vilka användarattribut (eller anspråk) som visas i rutan Under SAML-baserad autentisering och fliken **attribut > etablering** för att konfigurera hur användare och Gruppattribut flödar från Azure AD till Box under etablerings åtgärderna.

### <a name="important-tips-for-assigning-users-to-box"></a>Viktiga tips för att tilldela användare till Box 

*   Vi rekommenderar att du testar etablerings konfigurationen genom att använda en enda Azure AD-användare som tilldelas till rutan. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Box måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="enable-automated-user-provisioning"></a>Aktivera automatisk användar etablering

Det här avsnittet visar hur du ansluter Azure AD till boxs etablerings-API för användar konto och konfigurerar etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i Box baserat på användar-och grupp tilldelning i Azure AD.

Om automatisk etablering har Aktiver ATS läggs de tilldelade användarna och/eller grupperna till i etablerings kön som ska tillhandahållas automatiskt.
    
 * Om endast användar objekt är konfigurerade för att tillhandahållas placeras direkt tilldelade användare i etablerings kön och alla användare som är medlemmar i alla tilldelade grupper placeras i etablerings kön. 
    
 * Om grupp objekt har kon figurer ATS för att tillhandahållas, är alla tilldelade grupp objekt etablerade i rutan och alla användare som är medlemmar i dessa grupper. Gruppen och användarens medlemskap bevaras vid skrivning till box.

> [!TIP] 
> Du kan också välja att aktivera SAML-baserad enkel inloggning för ruta genom att följa anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-account-provisioning"></a>Konfigurera automatisk etablering av användar konto:

Syftet med det här avsnittet är att skapa en översikt över hur du aktiverar etablering av Active Directory användar konton till box.

1. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

2. Om du redan har konfigurerat rutan för enkel inloggning söker du efter din instans av box med Sök fältet. Annars väljer du **Lägg till** och Sök **efter i** program galleriet. Markera rutan från Sök resultaten och Lägg till den i listan över program.

3. Välj din instans av rutan och välj sedan fliken **etablering** .

4. Ställ in **etablerings läget** på **automatiskt**. 

    ![etablerings](./media/box-userprovisioning-tutorial/provisioning.png)

5. Under avsnittet **admin credentials** klickar du på **auktorisera** för att öppna dialog rutan inloggning i ett nytt webbläsarfönster.

6. Ange de autentiseringsuppgifter som krävs på sidan **Logga in för att ge åtkomst till Box** och klicka sedan på **auktorisera**. 
   
    ![Aktivera automatisk användar etablering](./media/box-userprovisioning-tutorial/IC769546.png "Aktivera automatisk användar etablering")

7. Klicka på **Tillåt åtkomst till rutan** om du vill auktorisera åtgärden och återgå till Azure Portal. 
   
    ![Aktivera automatisk användar etablering](./media/box-userprovisioning-tutorial/IC769549.png "Aktivera automatisk användar etablering")

8. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din box-app. Om anslutningen Miss lyckas, se till att ditt Box-konto har team administratörs behörighet och försök att använda steget **"auktorisera"** igen.

9. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan.

10. Klicka på **Spara.**

11. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till box.**

12. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till box. Attributen som väljs som **matchande** egenskaper används för att matcha användar konton i rutan för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Box ändrar du **etablerings statusen** till **på** i avsnittet Inställningar

14. Klicka på **Spara.**

Som startar den inledande synkroniseringen av en användare och/eller grupper som har tilldelats till rutan i avsnittet användare och grupper. Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablering av aktivitets loggar, som beskriver alla åtgärder som utförs av etablerings tjänsten i din box-app.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

I din box-klient visas synkroniserade användare under **hanterade användare** i **Administratörs konsolen**.

![Integrations status](./media/box-userprovisioning-tutorial/IC769556.png "Integrations status")


## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](box-tutorial.md)
