---
title: 'Självstudie: Konfigurera snö för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till snö flingor.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/26/2019
ms.author: zhchia
ms.openlocfilehash: cc88712dd686983baed858de47e11dfa44e85350
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955526"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Självstudie: Konfigurera snö för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i snö och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till [snö flingor](https://www.Snowflake.com/pricing/). Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


> [!NOTE]
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i snö
> * Ta bort användare i snö när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och snö flingor
> * Etablera grupper och grupp medlemskap i snö flingor
> * [Enkel inloggning](./snowflake-tutorial.md) till snö flingor (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör).
* [En snö flingor-klient](https://www.Snowflake.com/pricing/).
* Ett användar konto i snö med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och snö](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera snö flingor som stöd för etablering med Azure AD

Innan du konfigurerar snö flingor för automatisk användar etablering med Azure AD måste du aktivera SCIM-etablering på snö flingor.

1. Logga in på din snö-administratörskonsolen. Ange frågan som visas nedan i kalkyl bladet markerat och klicka på **Kör**.

    ![Snö administrations konsol](media/Snowflake-provisioning-tutorial/image00.png)

2.  En SCIM-åtkomsttoken skapas för din snö flingor-klient. Hämta det genom att klicka på länken som marker ATS nedan.

    ![Skärm bild av ett kalkyl blad i den snö i snö USA med S C i M-åtkomsttoken som kallas för.](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopiera det genererade värdet för token och klicka på **Slutför**. Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt snö flingor-program i Azure Portal.

    ![Skärm bild av avsnittet information som visar den token som kopierats till textfältet och alternativet som är klar.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till snö flingor från Azure AD-programgalleriet

Lägg till snö flingor från Azure AD-programgalleriet för att börja hantera etablering till snö flingor. Om du tidigare har konfigurerat snö för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till snö flingor måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Steg 5. Konfigurera automatisk användar etablering till snö flingor 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i snö flingor baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-snowflake-in-azure-ad"></a>Konfigurera automatisk användar etablering för snö flingor i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **snö flingor**.

    ![Snowflake-länken i listan med program](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet admin credentials måste du skriva in **SCIM 2,0-bas-URL och** värden för autentiseringstoken som hämtades tidigare i fälten för **klient-URL** och **hemlig token** . Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till snö flingor. Om anslutningen Miss lyckas kontrollerar du att ditt snö-konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

7. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

8. Klicka på **Spara**.

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till snö flingor**.

10. Granska de användarattribut som synkroniseras från Azure AD till snö flingor i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i snö för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |aktiv|Boolesk|
   |displayName|Sträng|
   |emails[type eq "work"].value|Sträng|
   |userName|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: defaultRole|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: defaultWarehouse|Sträng|

11. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till snö flingor**.

12. Granska gruppattributen som synkroniseras från Azure AD till snö flingor i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i snö för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |medlemmar|Referens|

13. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

14. Om du vill aktivera Azure AD Provisioning-tjänsten för snö, ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

15. Definiera de användare och/eller grupper som du vill etablera till snö flingor genom att välja önskade värden i **omfång** i avsnittet **Inställningar** . Om det här alternativet inte är tillgängligt konfigurerar du de obligatoriska fälten under admin-autentiseringsuppgifter, klickar på **Spara** och uppdaterar sidan. 

    ![Etableringsomfång](common/provisioning-scope.png)

16. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

    Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Kopplings begränsningar

* De snö SCIM token som skapats upphör att gälla om 6 månader. Tänk på att dessa måste uppdateras innan de upphör att gälla för att slutföra etableringen av synkroniseringen. 

## <a name="troubleshooting-tips"></a>Felsökningstips

* **IP-intervall** 

   Azure AD Provisioning-tjänsten fungerar för närvarande under ett visst IP-intervall. Så om det behövs kan du begränsa andra IP-adressintervall och lägga till dessa specifika IP-intervall i tillåten för ditt program för att tillåta trafikflöde från Azure AD Provisioning-tjänsten till ditt program. Läs dokumentationen vid [IP-intervall](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#ip-ranges).

## <a name="change-log"></a>Ändrings logg

* 07/21/2020-aktive rad mjuk borttagning för alla användare (via det aktiva attributet).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md).
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg
* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md).