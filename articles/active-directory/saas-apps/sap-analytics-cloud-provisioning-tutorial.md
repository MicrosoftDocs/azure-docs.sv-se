---
title: 'Självstudie: konfigurera SAP Analytics-moln för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig att automatiskt etablera och avetablera användar konton från Azure AD till SAP Analytics-molnet.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 27d12989-efa8-4254-a4ad-8cb6bf09d839
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2020
ms.author: Zhchia
ms.openlocfilehash: 31e5393cb5de627ebf8832e43302583d6eacbf59
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181503"
---
# <a name="tutorial-configure-sap-analytics-cloud-for-automatic-user-provisioning"></a>Självstudie: konfigurera SAP Analytics-moln för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både SAP Analytics-molnet och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [SAP Analytics-molnet](https://www.sapanalytics.cloud/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i SAP Analytics-molnet
> * Ta bort användare i SAP Analytics-molnet när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och SAP Analytics-molnet
> * [Enkel inloggning](sapboc-tutorial.md) till SAP Analytics-moln (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En SAP Analytics Cloud-klient
* Ett användar konto på en SAP Identity-etablerings administratörs konsol med administratörs behörighet. Se till att du har åtkomst till proxy-systemen i administratörs konsolen för identitets etablering. Om du inte ser panelen **proxyservrar** skapar du en incident för komponenten **BC-IAM-IP-adresser** för att begära åtkomst till den här panelen.
* En OAuth-klient med auktoriserings-klientautentiseringsuppgifter i SAP Analytics-molnet. Läs mer i: [Hantera OAuth-klienter och betrodda identitets leverantörer](https://help.sap.com/viewer/00f68c2e08b941f081002fd3691d86a7/release/en-US/4f43b54398fc4acaa5efa32badfe3df6.html)

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen

1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och SAP Analytics-molnet](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-sap-analytics-cloud-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera SAP Analytics-molnet för att ge stöd för etablering med Azure AD

1. Logga in på [administrations konsolen för SAP Identity-etableringen](https://ips-xlnk9v890j.dispatcher.us1.hana.ondemand.com/) med ditt administratörs konto och välj sedan **proxyservrar**.

   ![SAP-proxyservrar](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems.png.png)

2. Välj **Egenskaper**.

   ![Egenskaper för SAP-proxy-system](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-properties.png)

3. Kopiera **URL: en** och Lägg till i `/api/v1/scim` URL: en. Spara detta för senare användning i fältet **klient-URL** .

   ![Egenskaper för SAP-proxy-system URL](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-url.png)

4. Använd [Postman](https://www.postman.com/) för att utföra ett post-https-anrop till adressen: `<Token URL>?grant_type=client_credentials` där `Token URL` är URL: en i fältet **OAuth2TokenServiceURL** . Det här steget krävs för att generera en åtkomsttoken som ska användas i fältet hemligt token när automatisk etablering konfigureras.

   ![Egenskaper för SAP IP-proxy-system OAuth](./media/sap-analytics-cloud-provisioning-tutorial/sap-proxy-systems-details-oauth.png)

5. I Postman använder du **grundläggande autentisering** och anger OAuth-klient-ID som användare och hemligheten som lösen ord. Anropet returnerar en åtkomsttoken. Behåll detta kopierat för senare användning i fältet **hemlig token** .

   ![Postman POST-begäran](./media/sap-analytics-cloud-provisioning-tutorial/postman-post-request.png)

## <a name="step-3-add-sap-analytics-cloud-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till SAP Analytics-moln från Azure AD-programgalleriet

Lägg till SAP Analytics-moln från Azure AD-programgalleriet för att börja hantera etablering till SAP Analytics-molnet. Om du tidigare har konfigurerat SAP Analytics-molnet för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till SAP Analytics-molnet måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-sap-analytics-cloud"></a>Steg 5. Konfigurera automatisk användar etablering till SAP Analytics-molnet 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-sap-analytics-cloud-in-azure-ad"></a>Konfigurera automatisk användar etablering för SAP Analytics-moln i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **SAP Analytics-moln**.

    ![Moln länken SAP Analytics i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** kan du mata in klient webb adressen som hämtades tidigare i **klient-URL: en**. Mata in värdet för åtkomsttoken som hämtades tidigare i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till en insikt. Om anslutningen Miss lyckas kontrollerar du att ditt SAP Analytics Cloud-konto har administratörs behörighet och försöker igen.

    ![Skärm bild som visar dialog rutan admin-autentiseringsuppgifter där du kan ange din klient U R L och hemlig token.](./media/sap-analytics-cloud-provisioning-tutorial/provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **etablera Azure Active Directory användare**.

9. Granska de användarattribut som synkroniseras från Azure AD till SAP Analytics-molnet i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i SAP Analytics-molnet för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att moln-API: t SAP Analytics stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |aktiv|Boolesk|
   |emails[type eq "work"].value|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Enterprise: 2.0: användare: Manager|Sträng|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för SAP Analytics-molnet ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till SAP Analytics-molnet genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)