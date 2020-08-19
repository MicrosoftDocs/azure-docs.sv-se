---
title: 'Självstudie: Konfigurera Concur för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Läs hur du konfigurerar enkel inloggning mellan Azure Active Directory och Concur.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 678b7571d54c8938c163801a584ad813c6e5963f
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551174"
---
# <a name="tutorial-configure-concur-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Concur för automatisk användar etablering

Syftet med den här självstudien är att visa de steg du behöver utföra i Concur och Azure AD för att automatiskt etablera och avetablera användar konton från Azure AD till Concur.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande objekt:

*   En Azure Active Directory-klient.
*   En aktive rad Concur-prenumeration med enkel inloggning.
*   Ett användar konto i Concur med grupp administratörs behörighet.

## <a name="assigning-users-to-concur"></a>Tilldela användare till Concur

Azure Active Directory använder ett begrepp som kallas "tilldelningar" för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar konto etablering synkroniseras endast de användare och grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar etablerings tjänsten måste du bestämma vilka användare och/eller grupper i Azure AD som representerar de användare som behöver åtkomst till Concur-appen. När du har bestämt dig kan du tilldela dessa användare till Concur-appen genom att följa anvisningarna här:

[Tilldela en användare eller grupp till en företags app](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-concur"></a>Viktiga tips för att tilldela användare till Concur

*   Vi rekommenderar att en enda Azure AD-användare tilldelas till Concur för att testa etablerings konfigurationen. Ytterligare användare och/eller grupper kan tilldelas senare.

*   När du tilldelar en användare till Concur måste du välja en giltig användar roll. Rollen "standard åtkomst" fungerar inte för etablering.

## <a name="enable-user-provisioning"></a>Aktivera användar etablering

Det här avsnittet vägleder dig genom att ansluta din Azure AD till Concur-API för användar konto och konfigurera etablerings tjänsten för att skapa, uppdatera och inaktivera tilldelade användar konton i Concur baserat på användar-och grupp tilldelning i Azure AD.

> [!Tip] 
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Concur enligt anvisningarna i [Azure Portal](https://portal.azure.com). Enkel inloggning kan konfigureras oberoende av automatisk etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-user-account-provisioning"></a>Konfigurera användar konto etablering:

Syftet med det här avsnittet är att skapa en översikt över hur du aktiverar etablering av Active Directory användar konton till Concur.

Om du vill aktivera appar i utgifts tjänsten måste det finnas en korrekt konfiguration och användning av en webb tjänst administratörs profil. Lägg inte till rollen som WS-administratör i din befintliga administratörs profil som du använder för T&E-postadministrations funktioner.

Concur-konsulter eller klient administratören måste skapa en distinkt profil för webb tjänst administratörer och klient administratören måste använda den här profilen för funktionerna för webb tjänst administratören (till exempel aktivera appar). De här profilerna måste hållas åtskilda från klient administratörens dagliga T&E admin-profil (T&E admin-profilen ska inte ha WSAdmin-rollen tilldelad).

När du skapar profilen som ska användas för att aktivera appen anger du klient administratörens namn i användar profil fälten. Detta tilldelar ägarskapet till profilen. När en eller flera profiler har skapats måste klienten logga in med den här profilen för att klicka på knappen "*Aktivera*" för en partner app i menyn webb tjänster.

Av följande skäl bör den här åtgärden inte utföras med den profil som de använder för normal T&E-administration.

* Klienten måste vara den som klickar på*Ja*i dialog fönstret som visas när en app har Aktiver ATS. Detta klickar på bekräftar att klienten är villig för partner programmet att komma åt sina data, så att du eller partnern inte kan klicka på Ja-knappen.

* Om en klient administratör som har aktiverat en app med hjälp av E-&E admin-profilen lämnar företaget (vilket leder till att profilen inaktive RAS) fungerar inte alla appar som är aktiverade med den profilen förrän appen aktive ras med en annan Active WS-administratörs profil. Det är därför du ska skapa distinkta profil profiler för WS.

* Om en administratör lämnar företaget kan namnet som är kopplat till profilen för WS-admin ändras till ersättnings administratören om det behövs utan att den aktiverade appen påverkas, eftersom profilen inte behöver inaktive ras.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in på **Concur** -klienten.

2. Välj **webb tjänster**på menyn **Administration** .
   
    ![Concur-klient](./media/concur-provisioning-tutorial/IC721729.png "Concur-klient")

3. På den vänstra sidan går du till fönstret **webb tjänster** och väljer **Aktivera partner program**.
   
    ![Aktivera partner program](./media/concur-provisioning-tutorial/ic721730.png "Aktivera partner program")

4. I listan **Aktivera program** väljer du **Azure Active Directory**och klickar sedan på **Aktivera**.
   
    ![Microsoft Azure Active Directory](./media/concur-provisioning-tutorial/ic721731.png "Microsoft Azure Active Directory")

5. Stäng dialog rutan **Bekräfta åtgärd** genom att klicka på **Ja** .
   
    ![Bekräfta åtgärd](./media/concur-provisioning-tutorial/ic721732.png "Bekräfta åtgärd")

6. I [Azure Portal](https://portal.azure.com)bläddrar du till avsnittet **Azure Active Directory > Enterprise-appar > alla program** .

7. Om du redan har konfigurerat Concur för enkel inloggning söker du efter din instans av Concur med hjälp av Sök fältet. Annars väljer du **Lägg till** och söker efter **Concur** i program galleriet. Välj Concur från Sök resultaten och Lägg till den i listan över program.

8. Välj din instans av Concur och välj sedan fliken **etablering** .

9. Ställ in **etablerings läget** på **automatiskt**. 
 
    ![etablerings](./media/concur-provisioning-tutorial/provisioning.png)

10. Under avsnittet **admin-autentiseringsuppgifter** anger du **användar namn** och **lösen ord** för Concur-administratören.

11. I Azure Portal klickar du på **Testa anslutning** för att se till att Azure AD kan ansluta till din Concur-app. Om anslutningen Miss lyckas kontrollerar du att ditt Concur-konto har team administratörs behörighet.

12. Ange e-postadressen till en person eller grupp som ska få etablerings fel meddelanden i fältet **e-postavisering** och markera kryss rutan.

13. Klicka på **Spara.**

14. Under avsnittet mappningar väljer du **synkronisera Azure Active Directory användare till Concur.**

15. I avsnittet **mappningar för attribut** granskar du de användarattribut som synkroniseras från Azure AD till Concur. Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Concur för uppdaterings åtgärder. Välj knappen Spara för att spara ändringarna.

16. Om du vill aktivera Azure AD Provisioning-tjänsten för Concur ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar**

17. Klicka på **Spara.**

Nu kan du skapa ett test konto. Vänta i upp till 20 minuter för att kontrol lera att kontot har synkroniserats med Concur.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](tutorial-list.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera enkel inloggning](concur-tutorial.md)

