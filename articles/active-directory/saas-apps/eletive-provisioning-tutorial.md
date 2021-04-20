---
title: 'Självstudie: Konfigurera Eletive för automatisk användareablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du etablerar och avetabler användarkonton automatiskt från Azure AD till Eletive.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 8a775422-e6d7-4cd5-b8d1-cc8a2db24c4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2021
ms.author: Zhchia
ms.openlocfilehash: 393629be5c30eb3d97dae1de1c06e2d4cf7af94d
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741946"
---
# <a name="tutorial-configure-eletive-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Eletive för automatisk användareablering

I den här självstudien beskrivs de steg som du måste utföra i både Eletive och Azure Active Directory (Azure AD) för att konfigurera automatisk användareablering. När Azure AD har konfigurerats etablerar och avetabler azure AD automatiskt användare och grupper till [Eletive](https://app.eletive.com/) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Eletive
> * Ta bort användare i Eletive när de inte längre behöver åtkomst
> * Håll användarattribut synkroniserade mellan Azure AD och Eletive
> * Enkel inloggning till Eletive (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klientorganisation](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (till exempel programadministratör, molnadministratör Programadministratör, programägare eller global administratör). 
* Ett användarkonto i Eletive med administrationsåtkomst.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som finns i [etableringsomfånget](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Fastställ vilka data som [ska mappas mellan Azure AD och Eletive](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes). 

## <a name="step-2-configure-eletive-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Eletive för att stödja etablering med Azure AD

1. Logga in på [Eletive](https://app.eletive.com/). Gå till  ->  **Inställningsfunktioner.**

    ![Funktioner](media/eletive-provisioning-tutorial/settings.png)

2.  Aktivera **integreringar** och **SCIM 2.0**.

    ![Integreringar](media/eletive-provisioning-tutorial/scim.png)

3.  Gå till **Inställningar**  ->  **Integrations**.

4.  Klicka på **Användareablering.**

    ![Flik](media/eletive-provisioning-tutorial/user.png)

5.  Klicka på **Anslut.**

    ![Button (Knapp)](media/eletive-provisioning-tutorial/connect.png)

6.  Kopiera och spara SCIM 2.0-URL:en och bearer-token. Dessa värden anges i fältet Klient-URL och Hemlig token på fliken Etablering i ditt Eletive-program i Azure Portal.


## <a name="step-3-add-eletive-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till Eletive från Azure AD-programgalleriet

Lägg till Eletive från Azure AD-programgalleriet för att börja hantera etablering till Eletive. Om du tidigare har ställt in Eletive för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till Eletive måste du välja en annan roll än **Standardåtkomst.** Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig för programmet är standardåtkomstrollen kan du uppdatera [programmanifestet för att](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) lägga till roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper kan du styra omfånget genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-eletive"></a>Steg 5. Konfigurera automatisk användareablering till Eletive 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-eletive-in-azure-ad"></a>Så här konfigurerar du automatisk användareablering för Eletive i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Eletive**.

    ![Eletive-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Automatisk etableringsflik](common/provisioning-automatic.png)

5. I avsnittet **Administratörsautentiseringsuppgifter** anger du URL:en för den störande klientorganisationen och den hemliga token. Klicka **på Testa anslutning** för att säkerställa att Azure AD kan ansluta till Eletive. Om anslutningen misslyckas ser du till att ditt Eletive-konto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory användare till Eletive**.

9. Granska användarattributen som synkroniseras från Azure AD till Eletive i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha användarkontona i Eletive för uppdateringsåtgärder. Om du väljer att ändra det [matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att Eletive-API:et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |emails[type eq "work"].value|Sträng|
   |externalId|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |preferredLanguage|Sträng|
   |userType|Sträng|
   |urn:ietf:params:scim:schemas:extension:eletive:2.0:User:participateInSurvey|Sträng|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Eletive ändrar **du Etableringsstatus** **till På** i **avsnittet** Inställningar.

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Eletive genom att välja önskade värden i **Omfång** i **avsnittet** Inställningar.

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="more-resources"></a>Fler resurser

* [Hantera användarkontoetablering för Enterprise-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
