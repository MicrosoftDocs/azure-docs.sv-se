---
title: 'Självstudie: Konfigurera TeamViewer för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till TeamViewer.
services: active-directory
author: Zhchia
writer: Zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/27/2020
ms.author: Zhchia
ms.openlocfilehash: 0362f0d69581c252170b4f37f8564caa7acd3d29
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96182248"
---
# <a name="tutorial-configure-teamviewer-for-automatic-user-provisioning"></a>Självstudie: Konfigurera TeamViewer för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både TeamViewer och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [TeamViewer](https://www.teamviewer.com/buy-now/) med Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i TeamViewer
> * Ta bort användare i TeamViewer när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och TeamViewer
> * [Enkel inloggning](./teamviewer-tutorial.md) till TeamViewer (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör). 
* En giltig [styrkorts licens](https://www.teamviewer.com/de/teamviewer-tensor/) för TeamViewer.
* En giltig anpassad identifierare från konfigurationen för [enkel inloggning](https://community.teamviewer.com/t5/Knowledge-Base/Single-Sign-On-with-Azure-Active-Directory/ta-p/60209#toc-hId--473669723) är tillgänglig.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och TeamViewer](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-teamviewer-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera TeamViewer för att stödja etablering med Azure AD

1. Logga in på [TeamViewer Management Console](https://login.teamviewer.com). Gå till **Redigera profil**.

    ![TeamViewer-administratörskonsolen](./media/teamviewer-provisioning-tutorial/admin.png)

2.  Navigera till **appar**. Klicka på **skapa skript-token**.

    ![TeamViewer Create-token](./media/teamviewer-provisioning-tutorial/createtoken.png)

3.  Ange ett namn för skript-token. Klicka på knappen **Spara** .

    ![Namn på TeamViewer-token](./media/teamviewer-provisioning-tutorial/tokenname.png)

4. Kopiera **token** och klicka på **OK**. Det här värdet anges i fältet **hemligt token** för TeamViewer-programmet i Azure Portal.

    ![TeamViewer-token](./media/teamviewer-provisioning-tutorial/token.png)

## <a name="step-3-add-teamviewer-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till TeamViewer från Azure AD-programgalleriet

Lägg till TeamViewer från Azure AD-programgalleriet för att börja hantera etablering till TeamViewer. Om du tidigare har konfigurerat TeamViewer för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till TeamViewer måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-teamviewer"></a>Steg 5. Konfigurera automatisk användar etablering till TeamViewer 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i TestApp baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-teamviewer-in-azure-ad"></a>Konfigurera automatisk användar etablering för TeamViewer i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **TeamViewer**.

    ![TeamViewer-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin-autentiseringsuppgifter** anger `ttps://webapi.teamviewer.com/scim/v2`  du i fältet för **placerings-URL** och anger den skript-token som skapades tidigare i den **hemliga token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till TeamViewer. Om anslutningen Miss lyckas kontrollerar du att TeamViewer-kontot har administratörs behörighet och försöker igen.

    ![Skärm bild som visar dialog rutan admin-autentiseringsuppgifter där du kan ange din klient U R L och hemlig token.](./media/teamViewer-provisioning-tutorial/provisioning.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till TeamViewer**.

9. Granska de användarattribut som synkroniseras från Azure AD till TeamViewer i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i TeamViewer för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att TeamViewer-API: et stöder filtrering av användare baserat på detta attribut. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |displayName|Sträng|
   |aktiv|Boolesk|

10. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för TeamViewer ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

12. Definiera de användare och/eller grupper som du vill etablera till TeamViewer genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

13. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid att utföra än efterföljande cykler, vilket inträffar ungefär var 40:e minut om Azure AD-etableringstjänsten körs. 

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

* Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
* Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)