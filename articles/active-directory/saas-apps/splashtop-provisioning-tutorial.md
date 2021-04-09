---
title: 'Självstudie: Konfigurera Splashtop för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till Splashtop.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: 8d8c3745-aaa9-4dbd-9fbf-92da4ada2a9e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2021
ms.author: Zhchia
ms.openlocfilehash: af76b051350c896d5de3d2827de1f51ef3191f1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101652668"
---
# <a name="tutorial-configure-splashtop-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Splashtop för automatisk användar etablering

I den här självstudien beskrivs de steg du behöver utföra i både Splashtop och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras, etablerar och avetablerar Azure AD automatiskt användare och grupper i [Splashtop](https://www.splashtop.com/) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md). 


## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i Splashtop
> * Ta bort användare i Splashtop när de inte behöver åtkomst längre
> * Behåll användarattribut synkroniserade mellan Azure AD och Splashtop
> * Etablera grupper och grupp medlemskap i Splashtop
> * [Enkel inloggning](./splashtop-tutorial.md) till Splashtop (rekommenderas)

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klient](../develop/quickstart-create-new-tenant.md) 
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etablering (till exempel program administratör, moln program administratör, program ägare eller global administratör). 
* Ett Splashtop-team med stöd för enkel inloggning. Fyll i det här [kontakt formuläret](https://marketing.splashtop.com/acton/fs/blocks/showLandingPage/a/3744/p/p-0095/t/page/fm/0) om du vill ha en utvärderings version eller prenumerera på funktionen SSO.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1. Planera etablering av distributionen
1. Lär dig mer om [hur etableringstjänsten fungerar](../app-provisioning/user-provisioning.md).
2. Ta reda på vem som finns i [etableringsomfånget](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).
3. Ta reda på vilka data som ska [mappas mellan Azure AD och Splashtop](../app-provisioning/customize-application-attributes.md). 

## <a name="step-2-configure-splashtop-to-support-provisioning-with-azure-ad"></a>Steg 2. Konfigurera Splashtop för att ge stöd för etablering med Azure AD

1. Använd för en ny [SSO-metod](https://support-splashtopbusiness.splashtop.com/hc/articles/360038280751-How-to-apply-for-a-new-SSO-method-) på Splashtop-webbportalen.
2. På webb portalen Splashtop genererar du API- [token](https://support-splashtopbusiness.splashtop.com/hc/articles/360046055352-How-to-generate-the-SCIM-provisioning-token-) för att konfigurera etableringen i Azure AD.

## <a name="step-3-add-splashtop-from-the-azure-ad-application-gallery"></a>Steg 3. Lägg till Splashtop från Azure AD-programgalleriet

Lägg till Splashtop från Azure AD-programgalleriet för att börja hantera etablering till Splashtop. Om du tidigare har konfigurerat Splashtop för SSO kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integreringen i början. Lär dig mer om att lägga till ett program från galleriet [här](../manage-apps/add-application-portal.md). 

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4. Definiera vem som ska finnas i etableringsomfånget 

Med Azure AD Provisioning-tjänsten kan du definiera omfång som ska tillhandahållas baserat på tilldelning till programmet och eller baserat på attribut för användaren och gruppen. Om du väljer att omfånget som ska etableras till din app ska baseras på tilldelning, kan du använda följande [steg](../manage-apps/assign-user-or-group-access-portal.md) för att tilldela användare och grupper till programmet. Om du väljer att omfånget endast ska etableras baserat på attribut för användaren eller gruppen, kan du använda ett omfångsfilter enligt beskrivningen [här](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 

* När du tilldelar användare och grupper till Splashtop måste du välja en annan roll än **standard åtkomst**. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om den enda rollen som är tillgänglig i programmet är standard åtkomst rollen kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till andra roller. 

* Starta i liten skala. Testa med en liten uppsättning användare och grupper innan du distribuerar till alla. När etableringsomfånget har angetts till tilldelade användare och grupper, kan du kontrollera detta genom att tilldela en eller två användare eller grupper till appen. När omfånget är inställt på alla användare och grupper, kan du ange ett [attributbaserat omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md). 


## <a name="step-5-configure-automatic-user-provisioning-to-splashtop"></a>Steg 5. Konfigurera automatisk användar etablering till Splashtop 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och grupper i TestApp baserat på användar-och grupp tilldelningar i Azure AD.

### <a name="to-configure-automatic-user-provisioning-for-splashtop-in-azure-ad"></a>Konfigurera automatisk användar etablering för Splashtop i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **Splashtop**.

    ![Splashtop-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Fliken Etablering](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Fliken etablering automatiskt](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , skriver du in din Splashtop-klient-URL och en hemlig token. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Splashtop. Om anslutningen Miss lyckas kontrollerar du att Splashtop-kontot har administratörs behörighet och försöker igen.

    ![Token](common/provisioning-testconnection-tenanturltoken.png)

6. I fältet **E-postavisering** anger du e-postadressen till den person eller grupp som ska ta emot meddelanden om etableringsfel. Markera sedan kryssrutan **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Splashtop**.

9. Granska de användarattribut som synkroniseras från Azure AD till Splashtop i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Splashtop för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)måste du se till att Splashtop-API: et stöder filtrering av användare baserat på det attributet. Välj knappen **Spara** för att spara ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |aktiv|Boolesk|
   |displayName|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |externalId|Sträng|
   |urn: IETF: params: scim: schemas: tillägg: Splashtop: 2.0: användare: ssoName|Sträng|
   

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory grupper till Splashtop**.

11. Granska gruppattributen som synkroniseras från Azure AD till Splashtop i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha grupperna i Splashtop för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

      |Attribut|Typ|Stöds för filtrering|
      |---|---|---|
      |displayName|Sträng|&check;|
      |externalId|Sträng|
      |medlemmar|Referens|

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Splashtop ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och grupper som du vill etablera till Splashtop genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

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