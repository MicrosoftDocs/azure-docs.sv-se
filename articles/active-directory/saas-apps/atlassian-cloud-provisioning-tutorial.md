---
title: 'Självstudie: Konfigurera Atlassian Cloud för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Atlassian-molnet.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/27/2019
ms.author: jeedes
ms.openlocfilehash: 691d35267c255c933a8098b99301fbb795a3cd0c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96181075"
---
# <a name="tutorial-configure-atlassian-cloud-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Atlassian Cloud för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Atlassian-molnet och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till [Atlassian-molnet](https://www.atlassian.com/licensing/cloud). Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Atlassian-molnet
> * Ta bort användare i Atlassian-molnet när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Atlassian-molnet
> * Etablera grupper och grupp medlemskap i Atlassian-molnet
> * [Enkel inloggning](./atlassian-cloud-tutorial.md) till Atlassian-molnet (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användarkonto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (t.ex. programadministratör, molnprogramadministratör, programägare eller global administratör).
* [En Atlassian Cloud-klient](https://www.atlassian.com/licensing/cloud)
* Ett användar konto i Atlassian-moln med administratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Atlassian-molnet](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-atlassian-cloud-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Atlassian-molnet så att det stöder etablering med Azure AD

1. Navigera till [Atlassian Organization Manager](https://admin.atlassian.com) **> Välj org >-katalogen**.

    ![Skärm bild av sidan administration med alternativet katalog som kallas för.](./media/atlassian-cloud-provisioning-tutorial/select-directory.png)

2. Klicka på **användar etablering** och klicka på **skapa en katalog**. Kopiera **katalog bas-URL** och **Bearer-token** som anges i fälten **klient-URL** och **hemlig token** på fliken etablering i Atlassian-molnet i Azure AD-portalen.

    ![Skärm bild av sidan administration med alternativet för användar etablering som anropas. ](./media/atlassian-cloud-provisioning-tutorial/secret-token-1.png) ![Skärm bild av sidan Skapa en token.](./media/atlassian-cloud-provisioning-tutorial/secret-token-2.png)
    ![Skärm bild av sidan demonstrations tids katalogens token.](./media/atlassian-cloud-provisioning-tutorial/secret-token-3.png)


## <a name="step-3-add-atlassian-cloud-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Atlassian-moln från Azure AD-programgalleriet

Lägg till Atlassian-moln från Azure AD-programgalleriet för att börja hantera etablering till Atlassian-molnet. Om du tidigare har konfigurerat Atlassian-molnet för enkel inloggning kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD-etableringstjänsten kan du bestämma vem som ska etableras, baserat på tilldelningen till programmet och eller baserat på attribut för användaren/gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Atlassian-molnet måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standardrollen för åtkomst, kan du [uppdatera applikationsmanifest](../develop/howto-add-app-roles-in-azure-ad-apps.md) och lägga till fler roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configuring-automatic-user-provisioning-to-atlassian-cloud"></a>Steg 5. Konfigurera automatisk användar etablering till Atlassian-molnet 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Atlassian-molnet baserat på användar-och/eller grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-atlassian-cloud-in-azure-ad"></a>Konfigurera automatisk användar etablering för Atlassian Cloud i Azure AD:

1. Logga in på [Azure Portal](https://portal.azure.com) och välj **företags program**, Välj **alla program** och välj sedan **Atlassian Cloud**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Atlassian Cloud**.

    ![Atlassian Cloud-länken i programlistan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , skriver du in **klient-URL: en** och den **hemliga token** som hämtades tidigare från ditt Atlassian Cloud-konto. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Atlassian-molnet. Om anslutningen Miss lyckas kontrollerar du att ditt Atlassian Cloud-konto har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Klicka på **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Atlassian-molnet**.

9. Granska de användarattribut som synkroniseras från Azure AD till Atlassian-molnet i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha användar kontona i Atlassian-molnet för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|
   |---|---|
   |userName|Sträng|
   |aktiv|Boolesk|
   |name.familyName|Sträng|
   |name.givenName|Sträng|
   |emails[type eq "work"].value|Sträng|   

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Atlassian-molnet**.

11. Granska gruppattributen som synkroniseras från Azure AD till Atlassian-molnet i avsnittet **Mappning av attribut** . De attribut som väljs som **matchande** egenskaper används för att matcha grupperna i Atlassian-molnet för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|
      |---|---|
      |displayName|Sträng|
      |externalId|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Atlassian-molnet ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Atlassian Cloud genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

16. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6. Övervaka distributionen
När du har konfigurerat etableringen använder du följande resurser till att övervaka distributionen:

1. Använd [etableringsloggarna](../reports-monitoring/concept-provisioning-logs.md) för att se vilka användare som har etablerats och vilka som har misslyckats
2. Kontrollera [förloppsindikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etableringscykeln och hur nära den är att slutföras
3. Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Läs mer om karantänstatus [här](../app-provisioning/application-provisioning-quarantine-status.md).  

## <a name="connector-limitations"></a>Kopplings begränsningar

* Atlassian-molnet tillåter endast etablering av användare från [verifierade domäner](https://confluence.atlassian.com/cloud/organization-administration-938859734.html).
* Atlassian-molnet har inte stöd för grupp namn idag. Det innebär att alla ändringar av displayName för en grupp i Azure AD inte kommer att uppdateras och avspeglas i Atlassian-molnet.
* Värdet för attributet **e-** postanvändare i Azure AD är bara ifyllt om användaren har en Microsoft Exchange-postlåda. Om användaren inte har en, rekommenderar vi att du mappar ett annat önskat attribut till attributet **e-post** i Atlassian-molnet.

## <a name="change-log"></a>Ändringslogg

* 06/15/2020 – stöd för batch-korrigering för grupper har lagts till.

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/atlassian-cloud-provisioning-tutorial/tutorial-general-03.png