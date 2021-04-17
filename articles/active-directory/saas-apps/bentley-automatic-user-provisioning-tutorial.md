---
title: 'Självstudie: Konfigurera Tutorialley – automatisk användareablering för automatisk användareablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användarkonton från Azure AD tillLeyley – automatisk användareablering.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 08778fff-f252-45c2-95d4-cc640c288af3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/13/2021
ms.author: Zhchia
ms.openlocfilehash: 0d5aaee56606abcc886310049ad5a087f2aedb5e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590359"
---
# <a name="tutorial-configure-bentley---automatic-user-provisioning-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Tutorialley – automatisk användareablering för automatisk användareablering

I den här självstudien beskrivs de steg du behöver utföra i bådeLeyley – Automatic User Provisioning och Azure Active Directory (Azure AD) för att konfigurera automatisk användareablering. När Azure AD har konfigurerats etablerar och avetablerar Azure AD automatiskt användare och grupper [tillLeyley – automatisk användareablering](https://www.bentley.com) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare iLeyley – automatisk användareablering
> * Ta bort användare iLeyley – automatisk användareablering när de inte längre behöver åtkomst
> * Håll användarattribut synkroniserade mellan Azure AD ochLeyley – automatisk användareablering
> * Etablera grupper och gruppmedlemskap iLeyley – automatisk användareablering

## <a name="prerequisites"></a>Förutsättningar

Scenariot som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klientorganisation](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (till exempel programadministratör, Programadministratör, programägare eller global administratör). 
* Ett federerat konto medLey IMS.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som finns i [etableringsomfånget](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Fastställ vilka data [som ska mappa mellan Azure AD ochMappley – automatisk användareablering.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-bentley---automatic-user-provisioning-to-support-provisioning-with-azure-ad"></a>Steg 2. KonfigureraRankley – automatisk användareablering för att stödja etablering med Azure AD

Kontakta supportteamet förLey User [Provisioning](https://communities.bentley.com/communities/other_communities/licensing_cloud_and_web_services/w/wiki/52836/microsoft-azure-ad-automatic-user-provisioning-configuration) för klientorganisations-URL och hemlig token. De här värdena anges på fliken Etablering i Programmet Azure Portal.

## <a name="step-3-add-bentley---automatic-user-provisioning-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg tillLeyley – automatisk användareablering från Azure AD-programgalleriet

Lägg tillLeyley – automatisk användareablering från Azure AD-programgalleriet för att börja hantera etablering tillLeyley – automatisk användareablering. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper tillLeyley – automatisk användareablering måste du välja en annan roll än **Standardåtkomst.** Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-bentley---automatic-user-provisioning"></a>Steg 5. Konfigurera automatisk användareablering tillLeyley – automatisk användareablering 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-bentley---automatic-user-provisioning-in-azure-ad"></a>Så här konfigurerar du automatisk användareablering förLey – automatisk användareablering i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer **duLeyley - Automatic User Provisioning**.

    ![Länken för automatisk användareablering i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Automatisk etableringsflik](common/provisioning-automatic.png)

5. Under avsnittet **Administratörsautentiseringsuppgifter** anger du url:en för automatisk användareablering av klientorganisation och hemlig token. Klicka **på Testa anslutning** för att se till att Azure AD kan ansluta tillLeyley – automatisk användareablering. Om anslutningen misslyckas ser du till att ditt Konto för automatisk användareablering har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory användare tillLeyley – automatisk användareablering.**

9. Granska de användarattribut som synkroniseras från Azure AD tillMappning – automatisk användareablering i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha användarkontona iLeyley – automatisk användareablering för uppdateringsåtgärder. Om du väljer att ändra det [matchande](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)målattributet måste du se till att API:et För automatisk användareablering har stöd för filtrering av användare baserat på det attributet. Välj knappen **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |title|Sträng|
   |emails[type eq "work"].value|Sträng|
   |preferredLanguage|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |addresses[type eq "work"].streetAddress|Sträng|
   |addresses[type eq "work"].locality|Sträng|
   |addresses[type eq "work"].region|Sträng|
   |addresses[type eq "work"].postalCode|Sträng|
   |addresses[type eq "work"].country|Sträng|
   |phoneNumbers[type eq "work"].value|Sträng|
   |externalId|Sträng|
   |urn:ietf:params:scim:schemas:extension:Wpley:2.0:User:isSoftDeleted|Sträng|

10. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory tillLeyley – automatisk användareablering.**

11. Granska de gruppattribut som synkroniseras från Azure AD tillLeyley – automatisk användareablering i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha grupperna iLeyley – automatisk användareablering för uppdateringsåtgärder. Välj knappen **Spara** för att genomföra ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |externalId|Sträng|
      |medlemmar|Referens|
      |urn:ietf:params:scim:schemas:extension:Kvmley:2.0:Group:description|Sträng|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD-etableringstjänsten förLey – automatisk användareablering ändrar du **Etableringsstatus** **till På** i **avsnittet** Inställningar.

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera tillLey – automatisk användareablering genom att välja önskade värden i **Omfång** i **avsnittet** Inställningar.

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).

## <a name="connector-limitations"></a>Begränsningar för anslutning
* Attributet för företagstillägget "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:manager" stöds inte och tas bort.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
