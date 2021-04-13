---
title: 'Självstudie: Konfigurera Jossel för automatisk användareablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du etablerar och avetablerar användarkonton automatiskt från Azure AD till Jos felsökning.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 6dbb744f-8b8e-4988-b293-ebe079c8c5c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2021
ms.author: Zhchia
ms.openlocfilehash: d2ab0009f036afa38dc9e401223854a034d45e42
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368722"
---
# <a name="tutorial-configure-jostle-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Jossel för automatisk användareablering

I den här självstudien beskrivs de steg som du måste utföra i både Josäge och Azure Active Directory (Azure AD) för att konfigurera automatisk användareablering. När Azure AD har konfigurerats etablerar och avetablerar de automatiskt användare och grupper till [Jos felsökning med](https://www.jostle.me/) hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Josrten
> * Ta bort användare i Jossel när de inte längre behöver åtkomst
> * Håll användarattribut synkroniserade mellan Azure AD och Josattribut
> * [Enkel inloggning till](jostle-tutorial.md) Jos tecken (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Scenariot som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel programadministratör, molnadministratör Programadministratör, programägare eller global administratör). 
* En [Jos tenant](https://www.jostle.me/).
* Ett användarkonto i Josadministration med administratörsbehörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen

1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
1. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
1. Fastställ vilka data som [ska mappa mellan Azure AD och Josmapp .](../app-provisioning/customize-application-attributes.md)

## <a name="step-2-configure-jostle-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Josbo för att stödja etablering med Azure AD

### <a name="automation-account"></a>Automation-konto

Innan du börjar måste du skapa en Automation-användare **i** Ditt Jostra intranät. Det här är det konto som du använder för att konfigurera med Azure. Automation-användare kan skapas i **Administratörsinställningar > användarkonton och data > Hantera Automation-användare.**

Mer information om Automation-användare och hur du skapar en finns i den [här artikeln.](https://forum.jostle.us/hc/en-us/articles/360057364073)

När Automation-användarkontot har skapats måste det **aktiveras** (dvs. vara inloggad på intranätet minst en gång) innan det kan användas för att konfigurera Azure.

### <a name="manage-user-provisioning"></a>Hantera användareablering

Innan du börjar bör du se till att **kontoprenumerationen innehåller funktioner för enkel inloggning/användareablering.** Om den inte gör det kan du kontakta din Kundframgångsansvarig och <success@jostle.me> de kan hjälpa dig att lägga till den i ditt konto.

Nästa steg är att hämta **API-URL:en** och **API-nyckeln** från Josdling:

1. Gå till huvudnavigeringen och klicka på **Administratörsinställningar.**
1. Under **Användardata till/från andra system klickar du** på Hantera **användareablering.** Om du inte ser "Hantera användareablering" här och har verifierat att ditt konto innehåller SSO/användareablering kan du kontakta supporten för att få den här sidan aktiverad i <support@jostle.me> dina administratörsinställningar).
1. I avsnittet **INFORMATION om API:et** för användaretablering går du till fältet Bas-URL, klickar på knappen Kopiera och sparar URL:en någonstans där du enkelt kan komma åt den senare.                                                            

   ![Etablering](media/jostle-provisioning-tutorial/manage-user-provisioning.png)
                
1. Klicka sedan på Lägg **till en ny nyckel**... Knappen
1. På följande skärm går du till fältet **Automation-användare** och använder den nedrullningsna menyn för att välja ditt Automation-användarkonto. 

   ![Integrationskonto](media/jostle-provisioning-tutorial/select-integration-account.png)                                                                                                                                     
1. I fältet **Beskrivning av etablerings-API-nyckel** ger du nyckeln ett namn (t.ex. "Azure") och klickar sedan på **knappen Lägg** till.

1. När nyckeln har genererats **ser** du till att kopiera den direkt och spara den där du sparade din URL (eftersom det är den enda gången nyckeln visas).                                                               
1. Nu ska du använda **API-URL:en och** **API-nyckeln för** att konfigurera integreringen i Azure.
## <a name="step-3-add-jostle-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till Josbo från Azure AD-programgalleriet

Lägg till Josbo från Azure AD-programgalleriet för att börja hantera etableringen till Jospool. Om du tidigare har ställt in Jospool för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen från början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-gallery-app.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Josmapp måste du välja en annan roll än **Standardåtkomst.** Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardåtkomstrollen kan du uppdatera [programmanifestet för att](../develop/howto-add-app-roles-in-azure-ad-apps.md) lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-jostle"></a>Steg 5. Konfigurera automatisk användareablering till Joslådan 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och grupper i Josiste-appen baserat på användar- och grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-jostle-in-azure-ad"></a>Så här konfigurerar du automatisk användareablering för Josrt i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

1. I programlistan väljer du **Jostare**.

    ![Länken för Jos link i programlistan](common/all-applications.png)

1. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

1.  Ange **Etableringsläge** som **Automatiskt**.

    ![Automatisk etableringsflik](common/provisioning-automatic.png)

1. I avsnittet **Administratörsautentiseringsuppgifter** anger du din Josloggningsklient-URL och **information om hemlig token.**  Välj **Testa anslutning** för att säkerställa att Azure AD kan ansluta till Jostare. Om anslutningen misslyckas ser du till att ditt Josadministrationskonto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

1. I fältet **E-postavisering** anger du e-postadressen för en person eller grupp som ska få meddelanden om etableringsfel. Markera kryssrutan **Skicka ett e-postmeddelande när ett fel** inträffar.

    ![E-postavisering](common/provisioning-notification-email.png)

1. Välj **Spara**.

1. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory användare till Josmapp.**

1. Granska de användarattribut som synkroniseras från Azure AD till Jos tecken i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha användarkontona i Josare för uppdateringsåtgärder. Om du ändrar det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att Josmapp-API:et stöder filtrering av användare baserat på det attributet. Välj **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |aktiv|Boolesk|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |emails[type eq "work"].value|Sträng|
   |emails[type eq "personal"].value|Sträng|
   |emails[type eq "alternate1"].value|Sträng|
   |emails[type eq "alternate2"].value|Sträng|  
   |urn:ietf:params:scim:schemas:extension:joslå:2.0:Användare:alternateEmail1Label|Sträng|
   |urn:ietf:params:scim:schemas:extension:joslå:2.0:Användare:alternateEmail2Label |Sträng|  

1. Information om hur du konfigurerar omfångsfilter finns i anvisningarna i [självstudien om omfångsfilter.](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)

1. Om du vill aktivera Azure AD-etableringstjänsten för Jostle ändrar **du Etableringsstatus** **till På** i **avsnittet** Inställningar.

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

1. Definiera de användare eller grupper som du vill etablera till Josanter genom att välja önskade värden i **Omfång** i **avsnittet** Inställningar.

    ![Etableringsomfång](common/provisioning-scope.png)

1. När du är redo att etablera väljer du **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den inledande cykeln tar längre tid än nästa cykler, vilket inträffar ungefär var 40:e minut så länge Azure AD-etableringstjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen

När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etableringsloggarna för](../reports-monitoring/concept-provisioning-logs.md) att avgöra vilka användare som har etablerats korrekt eller misslyckats.
* Kontrollera [förloppsfältet](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är slutförd.
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Mer information om karantäntillstånd finns i [Programetableringsstatus för karantän.](../app-provisioning/application-provisioning-quarantine-status.md)

## <a name="more-resources"></a>Fler resurser

* [Hantera användarkontoetablering för företagsappar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)