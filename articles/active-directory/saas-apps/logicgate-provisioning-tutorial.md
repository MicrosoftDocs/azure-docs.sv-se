---
title: 'Självstudie: Konfigurera LogicGate för automatisk användareablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetabler användarkonton från Azure AD till LogicGate.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: eea988ef-b0f1-4d22-b867-310f167540c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/17/2021
ms.author: Zhchia
ms.openlocfilehash: c9c938ab344a7d861af713fa42e2e39afa1df1b3
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589490"
---
# <a name="tutorial-configure-logicgate-for-automatic-user-provisioning"></a>Självstudie: Konfigurera LogicGate för automatisk användareablering

Den här självstudien beskriver de steg du behöver utföra i både LogicGate och Azure Active Directory (Azure AD) för att konfigurera automatisk användareablering. När Azure AD har konfigurerats etablerar och avetablerar Azure AD automatiskt användare och grupper till [LogicGate](https://www.logicgate.com) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../manage-apps/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i LogicGate
> * Ta bort användare i LogicGate när de inte längre behöver åtkomst
> * Håll användarattribut synkroniserade mellan Azure AD och LogicGate

## <a name="prerequisites"></a>Förutsättningar

Scenariot som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klientorganisation](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant) 
* Ett användarkonto i Azure AD med [behörighet](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En LogicGate-klientorganisation med Enterprise-planen eller bättre aktiverad.
* Ett användarkonto i LogicGate med administratörsbehörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).
2. Ta reda på vem som finns i [etableringsomfånget](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts).
3. Fastställ vilka data som [ska mappa mellan Azure AD och LogicGate.](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) 

## <a name="step-2-configure-logicgate-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera LogicGate för att stödja etablering med Azure AD

1. Logga in på **LogicGate-administratörskonsolen.** Gå till fliken **Start** och klicka på **profilikonen** över det övre högra hörnet.
2. Gå  till **>** **Åtkomstnyckel för profil.**

    ![Fliken Profil](./media/logicgate-provisioning-tutorial/profile.png)

3. Klicka på **Generera åtkomstnyckel.** 
    
    ![Fliken Åtkomst](./media/logicgate-provisioning-tutorial/key.png)

4. Kopiera och spara **åtkomstnyckeln**. Det här värdet anges i fältet **Hemlig token** * på fliken Etablering i LogicGate-programmet i Azure Portal. 
    
    ![Fliken Nyckel](./media/logicgate-provisioning-tutorial/access.png)

## <a name="step-3-add-logicgate-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till LogicGate från Azure AD-programgalleriet

Lägg till LogicGate från Azure AD-programgalleriet för att börja hantera etablering till LogicGate. Om du tidigare har ställt in LogicGate för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 

* När du tilldelar användare och grupper till LogicGate måste du välja en annan roll än **Standardåtkomst.** Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts). 


## <a name="step-5-configure-automatic-user-provisioning-to-logicgate"></a>Steg 5. Konfigurera automatisk användareablering till LogicGate 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-logicgate-in-azure-ad"></a>Så här konfigurerar du automatisk användareablering för LogicGate i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **LogicGate**.

    ![LogicGate-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablera](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken Automatisk](common/provisioning-automatic.png)

5. I avsnittet **Administratörsautentiseringsuppgifter** anger du url:en för LogicGate-klientorganisationen och din hemliga token. Klicka **på Testa anslutning** för att säkerställa att Azure AD kan ansluta till LogicGate. Om anslutningen misslyckas ser du till att ditt LogicGate-konto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory användare till LogicGate.**

9. Granska användarattributen som synkroniseras från Azure AD till LogicGate i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha användarkontona i LogicGate för uppdateringsåtgärder. Om du väljer att ändra det [matchande målattributet](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes)måste du se till att LogicGate-API:et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |emails[type eq "work"].value|Sträng|
   |aktiv|Boolesk|
   |name.givenName|Sträng|
   |name.familyName|Sträng|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../manage-apps/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för LogicGate ändrar du **Etableringsstatus** **till På** i **avsnittet** Inställningar.

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till LogicGate genom att välja önskade värden **i Omfång** i **avsnittet** Inställningar.

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](/azure/active-directory/app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-quarantine-status).  

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../manage-apps/check-status-user-account-provisioning.md)
