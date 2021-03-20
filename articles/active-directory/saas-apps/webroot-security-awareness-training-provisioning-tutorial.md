---
title: 'Självstudie: Konfigurera säkerhets medvetenhets utbildning för Webroot för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Webroot Security medvetenhet Training.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 455f4396-930e-4db5-a167-d3ea6a860a17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: Zhchia
ms.openlocfilehash: f015718c59178b8094f535280edadd641d22a806
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98741935"
---
# <a name="tutorial-configure-webroot-security-awareness-training-for-automatic-user-provisioning"></a>Självstudie: Konfigurera säkerhets medvetenhets utbildning för Webroot för automatisk användar etablering

I den här självstudien beskrivs de steg som du behöver utföra i både säkerhets medvetenhets träningen för Webroot och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS, etablerar och avetablerar Azure AD automatiskt användare och grupper i [säkerhets medvetenheten för säkerhets medvetenhet](https://www.webroot.com/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i informations utbildning för säkerhets medvetenhet i Webroot
> * Ta bort användare i säkerhets medvetenhets träning för Webroot när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Webroot Security medvetenhet Training
> * Etablera grupper och grupp medlemskap i säkerhets medvetenhets träning för Webroot

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör).
* En hanterad tjänst leverantörs konsol med säkerhets medvetenhets utbildning för Webroot aktiverat för minst en av dina platser.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Webroot Security medvetenhet Training](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-webroot-security-awareness-training-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera säkerhets medvetenhets träning för Webroot som stöder etablering med Azure AD

### <a name="obtain-a-secret-token"></a>Hämta en hemlig token

Om du vill ansluta din webbplats till Azure AD måste du skaffa en **hemlig token** för platsen i hanterings konsolen för Webroot.

1. Logga in på din [Webroot Management-konsol](https://identity.webrootanywhere.com/v1/Account/login#tab_customers)

2. På fliken **platser** klickar du på kugg hjuls ikonen i kolumnen utbildning av säkerhets medvetenhet för den plats som du vill ansluta till Azure AD.

    ![Kugg hjuls ikon](./media/webroot-security-awareness-training-provisioning-tutorial/gear-icon.png)

3. Klicka på knappen för att **Konfigurera Azure AD-integrering**.

    ![Konfigurera Azure AD-integrering](./media/webroot-security-awareness-training-provisioning-tutorial/configure-azure-ad-integration.png)

4. Kopiera och spara den **hemliga token**. Det här värdet anges i fältet hemligt token på fliken etablering i övnings programmet för säkerhets medvetenhet för Webroot i Azure Portal.

5. Klicka på **Klar**.

    ![Kopiera hemlig token](./media/webroot-security-awareness-training-provisioning-tutorial/copy-secret-token.png)

## <a name="step-3-add-webroot-security-awareness-training-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till säkerhets medvetenhets utbildning för Webroot från Azure AD-programgalleriet

Lägg till webinriktad-utbildning för säkerhets medvetenhet från Azure AD-programgalleriet för att börja hantera etablering till säkerhets medvetenhets träning för Webroot. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till säkerhets medvetenhets utbildningen i Webroot måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-webroot-security-awareness-training"></a>Steg 5. Konfigurera automatisk användar etablering för säkerhets medvetenhets utbildning i Webroot 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-webroot-security-awareness-training-in-azure-ad"></a>Konfigurera automatisk användar etablering för säkerhets medvetenhets träning för Webroot i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **säkerhets medvetenhets utbildning för Webroot**.

    ![Länken för säkerhets medvetenhet för Webroot i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://awarenessapi.webrootanywhere.com/api/v2/scim` i **klient-URL**. Mata in det hemliga token-värdet som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till säkerhets medvetenhets utbildningen för Webroot. Om anslutningen Miss lyckas kontrollerar du att ditt konto för säkerhets medvetenhet i Webroot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar dialog rutan admin-autentiseringsuppgifter där du kan ange din klient U R L och hemlig token.](./media/webroot-security-awareness-training-provisioning-tutorial/provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till Webroot Security medvetenhet Training i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i säkerhets medvetenhets träningen för Webroot för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att API: t för säkerhets medvetenheten Webroot stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |externalId|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |emails[type eq "work"].value|Sträng|

10. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory grupper**.

11. Granska gruppattributen som synkroniseras från Azure AD till Webroot Security medvetenhet Training i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i säkerhets medvetenhets träningen för Webroot för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |medlemmar|Referens|
      |externalId|Sträng|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för säkerhets medvetenhets träning för Webroot, ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Webroot-utbildningen för säkerhets medvetenhet genom att välja önskade värden i **området** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="change-log"></a>Ändringslogg
* 1/21/2021 – stöd för Core-attributet "userName" för användare har lagts till.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)