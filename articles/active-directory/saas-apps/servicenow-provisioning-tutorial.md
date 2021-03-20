---
title: 'Självstudie: Konfigurera ServiceNow för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till ServiceNow.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.openlocfilehash: b3b62e7c16106fd9d94d4a3438331dab4ce8b6e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99539053"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Självstudie: Konfigurera ServiceNow för automatisk användar etablering

I den här självstudien beskrivs de steg som du utför i både ServiceNow och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD har kon figurer ATS etablerar den automatiskt och avetablerar användare och grupper till [ServiceNow](https://www.servicenow.com/) med hjälp av Azure AD Provisioning-tjänsten. 

Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i ServiceNow
> * Ta bort användare i ServiceNow när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och ServiceNow
> * Etablera grupper och grupp medlemskap i ServiceNow
> * Tillåt [enkel inloggning](servicenow-tutorial.md) till ServiceNow (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (program administratör, moln program administratör, program ägare eller global administratör)
* En [ServiceNow-instans](https://www.servicenow.com/) av Calgary eller högre
* En [ServiceNow Express-instans](https://www.servicenow.com/) av Helsingfors eller högre
* Ett användar konto i ServiceNow med administratörs rollen

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1: planera etablerings distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Steg 2: Konfigurera ServiceNow till att stödja etablering med Azure AD

1. Identifiera namnet på ServiceNow-instansen. Du kan hitta instans namnet i den URL som du använder för att få åtkomst till ServiceNow. I följande exempel är instans namnet **dev35214**.

   ![Skärm bild som visar en ServiceNow-instans.](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Hämta autentiseringsuppgifter för en administratör i ServiceNow. Gå till användar profilen i ServiceNow och kontrol lera att användaren har administratörs rollen. 

   ![Skärm bild som visar en ServiceNow-administratörs roll.](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Steg 3: Lägg till ServiceNow från Azure AD-programgalleriet

Lägg till ServiceNow från Azure AD-programgalleriet för att börja hantera etablering till ServiceNow. Om du tidigare har konfigurerat ServiceNow för enkel inloggning (SSO) kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integrationen. [Lär dig mer om att lägga till ett program i galleriet](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4: definiera vem som ska finnas inom omfånget för etablering 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet eller baserat på attribut för användaren eller gruppen. Om du väljer att omfånget som ska tillhandahållas till din app baserat på tilldelning kan du använda [stegen för att tilldela användare och grupper till programmet](../manage-apps/assign-user-or-group-access-portal.md). Om du väljer att omfånget som endast ska tillhandahållas baserat på attribut för användaren eller gruppen kan du [använda ett omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

Tänk på följande saker:

* När du tilldelar användare och grupper till ServiceNow måste du välja en annan roll än standard åtkomst. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När omfånget för etablering har angetts till tilldelade användare och grupper kan du styra detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attribut-baserat omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Steg 5: Konfigurera automatisk användar etablering till ServiceNow 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper i TestApp. Du kan basera konfigurationen på användar-och grupp tilldelningar i Azure AD.

Konfigurera automatisk användar etablering för ServiceNow i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla program**.

    ![Skärm bild som visar fönstret företags program.](common/enterprise-applications.png)

2. I listan med program väljer du **ServiceNow**.

    ![Skärm bild som visar en lista över program.](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **etablerings läget** till **Automatisk**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. I avsnittet **admin-autentiseringsuppgifter** anger du dina autentiseringsuppgifter för ServiceNow-administratören och användar namnet. Välj **Testa anslutning** för att se till att Azure AD kan ansluta till ServiceNow. Om anslutningen Miss lyckas kontrollerar du att ServiceNow-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar tjänst etablerings sidan där du kan ange autentiseringsuppgifter för administratören.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etablerings fel. Markera sedan kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Skärm bild som visar rutor för e-postaviseringar.](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. I avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till ServiceNow**.

9. Granska de användarattribut som synkroniseras från Azure AD till ServiceNow i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i ServiceNow för uppdaterings åtgärder. 

   Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att ServiceNow-API: et stöder filtrering av användare baserat på detta attribut. 
   
   Välj knappen **Spara** för att spara ändringarna.

10. I avsnittet **mappningar** väljer **du synkronisera Azure Active Directory grupper till ServiceNow**.

11. Granska gruppattributen som synkroniseras från Azure AD till ServiceNow i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i ServiceNow för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

12. Information om hur du konfigurerar omfångs filter finns i kursen i avsnittet [omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för ServiceNow ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Skärm bild som visar etablerings status växlad på.](common/provisioning-toggle-on.png)

14. Definiera de användare och grupper som du vill etablera till ServiceNow genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Skärm bild som visar alternativ för etablerings omfång.](common/provisioning-scope.png)

15. När du är redo att etablera väljer du **Spara**.

    ![Skärm bild av knappen för att spara en etablerings konfiguration.](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler. Efterföljande cykler sker var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6: övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

- Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som har etablerats eller har misslyckats.
- Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
- Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. [Läs mer om karantäns tillstånd](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Felsökningstips
* När du konfigurerar vissa attribut (till exempel **avdelning** och **plats**) i ServiceNow måste värdena redan finnas i en referens tabell i ServiceNow. Om de inte gör det får du ett **InvalidLookupReference** -fel. 

   Du kan till exempel ha två platser (Seattle, Los Angeles) och tre avdelningar (Sales, ekonomi, marknadsföring) i en viss tabell i ServiceNow. Om du försöker etablera en användare vars avdelning är "försäljning" och vars plats är "Seattle", kommer användaren att etableras korrekt. Om du försöker etablera en användare vars avdelning är "försäljning" och vars plats är "LA", kommer användaren inte att etableras. Platsen "LA" måste läggas till i referens tabellen i ServiceNow, eller så måste attributet användare i Azure AD uppdateras för att matcha formatet i ServiceNow. 
* Om du får ett **EntryJoiningPropertyValueIsMissing** -fel kan du granska [mappningarna för attribut](../app-provisioning/customize-application-attributes.md) för att identifiera matchande attribut. Det här värdet måste finnas på den användare eller grupp som du försöker etablera. 
* Om du vill förstå eventuella krav eller begränsningar (t. ex. om du vill ange en landskod för en användare) granskar du [SERVICENOW SOAP-API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html).
* Etablerings begär Anden skickas som standard till https://{ditt-instance-Name}. service-nu. com/{Table-Name}. Om du behöver en anpassad klient-URL kan du ange hela URL-adressen som instans namn.
* **ServiceNowInstanceInvalid** -felet indikerar ett problem med att kommunicera med ServiceNow-instansen. Följande är fel texten:
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

  Om du har test anslutnings problem kan du försöka välja **Nej** för följande inställningar i ServiceNow:
   
  - **Systemsäkerhet**  >  Inställningar för hög **säkerhet**  >  **Kräv grundläggande autentisering för inkommande schema begär Anden**
  - **System egenskaper**  >  **Webb tjänster**  >  **Kräv grundläggande auktorisering för inkommande SOAP-begäranden**

     ![Skärm bild som visar alternativet för att auktorisera SOAP-begäranden.](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

  Om du fortfarande inte kan lösa problemet kan du kontakta ServiceNow-supporten och be dem att aktivera SOAP-felsökning för att felsöka. 

* Azure AD Provisioning-tjänsten fungerar för närvarande i vissa [IP-intervall](../app-provisioning/use-scim-to-provision-users-and-groups.md#ip-ranges). Om det behövs kan du begränsa andra IP-adressintervall och lägga till dessa specifika IP-intervall i listan över tillåtna program. Den tekniken kommer att tillåta trafikflödet från Azure AD Provisioning-tjänsten till ditt program.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)
