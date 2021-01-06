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
ms.openlocfilehash: 71f4cfa9e7b69e51bafa5364f857f8ab689f2ad0
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955475"
---
# <a name="tutorial-configure-servicenow-for-automatic-user-provisioning"></a>Självstudie: Konfigurera ServiceNow för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både ServiceNow och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [ServiceNow](https://www.servicenow.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i ServiceNow
> * Ta bort användare i ServiceNow när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och ServiceNow
> * Etablera grupper och grupp medlemskap i ServiceNow
> * [Enkel inloggning](servicenow-tutorial.md) till ServiceNow (rekommenderas)

## <a name="prerequisites"></a>Krav

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En [ServiceNow-instans](https://www.servicenow.com/) av Calgary eller högre
* En [ServiceNow Express-instans](https://www.servicenow.com/) av Helsingfors eller högre
* Ett användar konto i ServiceNow med administratörs rollen

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och ServiceNow](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-servicenow-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera ServiceNow för att ge stöd för etablering med Azure AD

1. Identifiera namnet på ServiceNow-instansen. Du kan hitta instans namnet i den URL som du använder för att få åtkomst till ServiceNow. I exemplet nedan är instans namnet dev35214.

   ![ServiceNow-instans](media/servicenow-provisioning-tutorial/servicenow-instance.png)

2. Hämta autentiseringsuppgifter för en administratör i ServiceNow. Navigera till användar profilen i ServiceNow och kontrol lera att användaren har administratörs rollen. 

   ![ServiceNow-administratörs roll](media/servicenow-provisioning-tutorial/servicenow-admin-role.png)


## <a name="step-3-add-servicenow-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till ServiceNow från Azure AD-programgalleriet

Lägg till ServiceNow från Azure AD-programgalleriet för att börja hantera etablering till ServiceNow. Om du tidigare har konfigurerat ServiceNow för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till ServiceNow måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-servicenow"></a>Steg 5. Konfigurera automatisk användar etablering till ServiceNow 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-servicenow-in-azure-ad"></a>Konfigurera automatisk användar etablering för ServiceNow i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **ServiceNow**.

    ![ServiceNow-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** måste du skriva in dina autentiseringsuppgifter och användar namn för ServiceNow-administratören. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till ServiceNow. Om anslutningen Miss lyckas kontrollerar du att ServiceNow-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar sidan tjänst etablering där du kan ange autentiseringsuppgifter för administratören.](./media/servicenow-provisioning-tutorial/servicenow-provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till ServiceNow**.

9. Granska de användarattribut som synkroniseras från Azure AD till ServiceNow i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i ServiceNow för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att ServiceNow-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till ServiceNow**.

11. Granska gruppattributen som synkroniseras från Azure AD till ServiceNow i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i ServiceNow för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för ServiceNow ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till ServiceNow genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="troubleshooting-tips"></a>Felsökningstips
* **InvalidLookupReference:** Vid etablering av vissa attribut, till exempel avdelning och plats i ServiceNow, måste värdena redan finnas i en referens tabell i ServiceNow. Du kan till exempel ha två platser (Seattle, Los Angeles) och tre avdelningar (försäljning, ekonomi, marknadsföring) i tabellen **Infoga tabell namn** i ServiceNow. Om du försöker etablera en användare där hans avdelning är "försäljning" och platsen "Seattle", kommer han att etableras korrekt. Om du försöker etablera en användare med avdelning "försäljning" och platsen "LA" kan användaren inte etableras. Platsen LA måste antingen läggas till i referens tabellen i ServiceNow eller så måste attributet User i Azure AD uppdateras för att matcha formatet i ServiceNow. 
* **EntryJoiningPropertyValueIsMissing:** Granska dina [mappningar av attribut](../app-provisioning/customize-application-attributes.md) för att identifiera matchande attribut. Det här värdet måste finnas på den användare eller grupp som du försöker etablera. 
* Granska [SERVICENOW SOAP API](https://docs.servicenow.com/bundle/newyork-application-development/page/integrate/web-services-apis/reference/r_DirectWebServiceAPIFunctions.html) för att förstå eventuella krav eller begränsningar (till exempel format för att ange landskod för en användare)
* Etablerings begär Anden skickas som standard till https://{ditt-instance-Name}. service-nu. com/{Table-Name}. Om du behöver en anpassad klient-URL kan du ange hela URL: en i fältet instans namn.
* **ServiceNowInstanceInvalid** 
  
  `Details: Your ServiceNow instance name appears to be invalid.  Please provide a current ServiceNow administrative user name and          password along with the name of a valid ServiceNow instance.`                                                              

   Det här felet indikerar ett problem som kommunicerar med ServiceNow-instansen. 
   
   Om du har problem med att testa anslutningen försöker du göra följande inställningar **inaktiverade** i ServiceNow:
   
   1. Välj **säkerhets**  >  **Inställningar** för system säkerhet  >  **kräver grundläggande autentisering för inkommande schema begär Anden**.
   2. Välj **system egenskaper**  >  **webb tjänster**  >  **kräver grundläggande auktorisering för inkommande SOAP-begäranden**.

   ![Auktoriserar SOAP-begäran](media/servicenow-provisioning-tutorial/servicenow-webservice.png)

   Om det löser problemen kontaktar du ServiceNow-supporten och ber dem att aktivera SOAP-felsökning för att felsöka. 

* **IP-intervall** 

   Azure AD Provisioning-tjänsten fungerar för närvarande under ett visst IP-intervall. Så om det behövs kan du begränsa andra IP-adressintervall och lägga till dessa specifika IP-intervall i tillåten för ditt program för att tillåta trafikflöde från Azure AD Provisioning-tjänsten till ditt program. Läs dokumentationen vid [IP-intervall](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups#ip-ranges).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)