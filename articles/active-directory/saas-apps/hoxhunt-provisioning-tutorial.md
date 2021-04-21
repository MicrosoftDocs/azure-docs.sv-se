---
title: 'Självstudie: Konfigurera Hoxhunt för automatisk användareablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du etablerar och avetabler användarkonton automatiskt från Azure AD till Hoxhunt.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 24fbe0a4-ab2d-4e10-93a6-c87d634ffbcf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/28/2021
ms.author: Zhchia
ms.openlocfilehash: db33cc43419b4228ca270d3a69c0e88de2c05638
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752056"
---
# <a name="tutorial-configure-hoxhunt-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Hoxhunt för automatisk användareablering

I den här självstudien beskrivs de steg du behöver utföra i både Hoxhunt och Azure Active Directory (Azure AD) för att konfigurera automatisk användareablering. När Azure AD har konfigurerats etablerar och avetabler de automatiskt användare och grupper till [Hoxhunt](https://www.hoxhunt.com/) med hjälp av Azure AD-etableringstjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Hoxhunt
> * Ta bort användare i Hoxhunt när de inte längre behöver åtkomst
> * Håll användarattribut synkroniserade mellan Azure AD och Hoxhunt
> * [Enkel inloggning till](hoxhunt-tutorial.md) Hoxhunt (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande förutsättningar:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel programadministratör, molnadministratör Programadministratör, programägare eller global administratör). 
* En Hoxhunt-klientorganisation.
* SCIM API-nyckel och SCIM-slutpunkts-URL för din organisation (konfigureras av Stöd för Hoxhunt).
## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Fastställ vilka data som [ska mappas mellan Azure AD och Hoxhunt](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-hoxhunt-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Hoxhunt för att stödja etablering med Azure AD
Kontakta [Hoxhunt-supporten för](mailto:support@hoxhunt.com) att få SCIM API-nyckel och SCIM-slutpunkts-URL för att konfigurera Hoxhunt för att stödja etablering med Azure AD.
## <a name="step-3-add-hoxhunt-from-the-azure-ad-application-gallery"></a>Steg 3. Lägga till Hoxhunt från Azure AD-programgalleriet

Lägg till Hoxhunt från Azure AD-programgalleriet för att börja hantera etablering till Hoxhunt. Om du tidigare har ställt in Hoxhunt för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Hoxhunt måste du välja en annan roll än **Standardåtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-hoxhunt"></a>Steg 5. Konfigurera automatisk användareablering till Hoxhunt 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD-etableringstjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar- och/eller grupptilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-hoxhunt-in-azure-ad"></a>Så här konfigurerar du automatisk användareablering för Hoxhunt i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Hoxhunt**.

    ![Hoxhunt-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Automatisk etableringsflik](common/provisioning-automatic.png)

5. I avsnittet **Autentiseringsuppgifter för** administratör anger du din Hoxhunt-klientorganisations-URL och hemlig token. Klicka **på Testa anslutning** för att se till att Azure AD kan ansluta till Hoxhunt. Om anslutningen misslyckas ser du till att ditt Hoxhunt-konto har administratörsbehörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. I avsnittet **Mappningar** väljer du **Synkronisera Azure Active Directory användare till Hoxhunt**.

9. Granska de användarattribut som synkroniseras från Azure AD till Hoxhunt i **avsnittet Attributmappning.** Attributen som valts **som** Matchande egenskaper används för att matcha användarkontona i Hoxhunt för uppdateringsåtgärder. Om du väljer att ändra det [matchande målattributet](../app-provisioning/customize-application-attributes.md)måste du se till att Hoxhunt-API:et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |emails[type eq "work"].value|Sträng|
   |aktiv|Boolesk|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:department|Sträng|
   |addresses[type eq "work"].country|Sträng|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD-etableringstjänsten för Hoxhunt ändrar du **Etableringsstatus** **till På** i **avsnittet** Inställningar.

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till Hoxhunt genom att välja önskade värden i **Omfång** i **avsnittet** Inställningar.

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

* Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
* Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="change-log"></a>Ändringslogg
* 2021-04-20 – Stöd har lagts till för attributet "preferredLanguage" och enterprise extension "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User:division".

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)