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
ms.openlocfilehash: 06f11763498e3e8393d688a71e1c37b466be3f6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539543"
---
# <a name="tutorial-configure-snowflake-for-automatic-user-provisioning"></a>Självstudie: Konfigurera snö för automatisk användar etablering

Den här självstudien visar de steg som du utför i snö och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och grupper till [snö flingor](https://www.Snowflake.com/pricing/). Viktig information om vad den här tjänsten gör, hur det fungerar och vanliga frågor finns i [Vad är automatiserad SaaS app User-etablering i Azure AD?](../app-provisioning/user-provisioning.md). 

> [!NOTE]
> Den här anslutningen är för närvarande en offentlig för hands version. Information om användnings villkor finns i kompletterande användnings [villkor för Microsoft Azure för hands](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)versionerna.

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i snö
> * Ta bort användare i snö när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och snö flingor
> * Etablera grupper och grupp medlemskap i snö flingor
> * Tillåt [enkel inloggning](./snowflake-tutorial.md) till snö flingor (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md)
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (program administratör, moln program administratör, program ägare eller global administratör)
* [En snö flingor-klient](https://www.Snowflake.com/pricing/)
* Ett användar konto i snö med administratörs behörighet

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1: planera etablerings distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och snö](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-snowflake-to-support-provisioning-with-azure-ad"></a>Steg 2: Konfigurera snö till stöd för etablering med Azure AD

Innan du konfigurerar snö flingor för automatisk användar etablering med Azure AD måste du aktivera system för etablering av SCIM (Cross-Domain Identity Management) på snö flingor.

1. Logga in på din snö-administratörskonsolen. Ange följande fråga i det markerade kalkyl bladet och välj sedan **Kör**.

    ![Skärm bild av den snö administrations konsolen med fråga och kör-knappen.](media/Snowflake-provisioning-tutorial/image00.png)

2.  En SCIM-åtkomsttoken skapas för din snö flingor-klient. Om du vill hämta det väljer du länken som är markerad i följande skärm bild.

    ![Skärm bild av ett kalkyl blad i den snö i snö USA med S C i M-åtkomsttoken som kallas för.](media/Snowflake-provisioning-tutorial/image01.png)

3. Kopiera det genererade värdet för token och välj **Done**. Det här värdet anges i rutan **hemlig token** på fliken **etablering** i ditt snö flingor-program i Azure Portal.

    ![Skärm bild av avsnittet information, som visar att den token som kopierats till textfältet och alternativet klar har anropats.](media/Snowflake-provisioning-tutorial/image02.png)

## <a name="step-3-add-snowflake-from-the-azure-ad-application-gallery"></a>Steg 3: Lägg till snö flingor från Azure AD-programgalleriet

Lägg till snö flingor från Azure AD-programgalleriet för att börja hantera etablering till snö flingor. Om du tidigare har konfigurerat snö för enkel inloggning (SSO) kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du först testar integrationen. [Lär dig mer om att lägga till ett program i galleriet](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4: definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet eller baserat på attribut för användaren eller gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda [stegen för att tilldela användare och grupper till programmet](../manage-apps/assign-user-or-group-access-portal.md). Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du [använda ett omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tänk på följande saker:

* När du tilldelar användare och grupper till snö flingor måste du välja en annan roll än standard åtkomst. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfånget för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attribut-baserat omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-snowflake"></a>Steg 5: Konfigurera automatisk användar etablering till snö flingor 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper i snö flingor. Du kan basera konfigurationen på användar-och grupp tilldelningar i Azure AD.

Konfigurera automatisk användar etablering för snö flingor i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**.

    ![Skärm bild som visar fönstret företags program.](common/enterprise-applications.png)

2. Välj **snö** i listan med program.

    ![Skärm bild som visar en lista över program.](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **etablerings läget** till **Automatisk**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. I avsnittet **admin credentials** anger du scim 2,0-bas-URL och autentiseringstoken som du hämtade tidigare i rutorna **klient-URL** och **hemlig token** . 

   Välj **Testa anslutning** för att säkerställa att Azure AD kan ansluta till snö flingor. Om anslutningen Miss lyckas kontrollerar du att ditt snö-konto har administratörs behörighet och försöker igen.

    ![Skärm bild som visar rutor för klient U R L och hemlig token, tillsammans med knappen Testa anslutning.](common/provisioning-testconnection-tenanturltoken.png)

6. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Markera sedan kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Skärm bild som visar rutor för e-postaviseringar.](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory användare till snö flingor**.

9. Granska de användarattribut som synkroniseras från Azure AD till snö flingor i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i snö för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

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

10. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till snö flingor**.

11. Granska gruppattributen som synkroniseras från Azure AD till snö flingor i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i snö för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    |Attribut|Typ|
    |---|---|
    |displayName|Sträng|
    |medlemmar|Referens|

12. Information om hur du konfigurerar omfångs filter finns i kursen i avsnittet [omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för snö, ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

     ![Skärm bild som visar etablerings status växlad på.](common/provisioning-toggle-on.png)

14. Definiera de användare och grupper som du vill etablera till snö flingor genom att välja önskade värden i **omfång** i avsnittet **Inställningar** . 

    Om det här alternativet inte är tillgängligt konfigurerar du de obligatoriska fälten under **admin-autentiseringsuppgifter**, väljer **Spara** och uppdaterar sidan. 

     ![Skärm bild som visar alternativ för etablerings omfång.](common/provisioning-scope.png)

15. När du är redo att etablera väljer du **Spara**.

     ![Skärm bild av knappen för att spara en etablerings konfiguration.](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och grupper som definierats i **omfånget** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar. Efterföljande synkroniseringar sker var 40: e minut, så länge Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6: övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

- Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som har etablerats eller har misslyckats.
- Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
- Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. [Läs mer om karantäns tillstånd](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Kopplings begränsningar

Snö-genererade SCIM-token upphör att gälla om 6 månader. Tänk på att du måste uppdatera dessa token innan de upphör att gälla, så att du kan slutföra etableringen av synkroniseringen. 

## <a name="troubleshooting-tips"></a>Felsökningstips

Azure AD Provisioning-tjänsten fungerar för närvarande i vissa [IP-intervall](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). Om det behövs kan du begränsa andra IP-adressintervall och lägga till dessa specifika IP-intervall i listan över tillåtna program. Den tekniken kommer att tillåta trafikflödet från Azure AD Provisioning-tjänsten till ditt program.

## <a name="change-log"></a>Ändringslogg

* 07/21/2020: aktiverat mjuk borttagning för alla användare (via det aktiva attributet).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg
* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
