---
title: 'Självstudie: Konfigurera PlayVox för automatisk användar etablering med hjälp av Azure Active Directory | Microsoft Docs'
description: Lär dig hur du automatiskt etablerar och avetablerar användar konton från Azure AD till PlayVox.
services: active-directory
documentationcenter: ''
author: Zhchia
writer: Zhchia
manager: beatrizd
ms.assetid: c31c20ab-f6cd-40e1-90ad-fa253ecbc0f8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/18/2020
ms.author: Zhchia
ms.openlocfilehash: 3c7efca5e052c2d0680aa7ca3e1b6d27bfdd7d11
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96862487"
---
# <a name="tutorial-configure-playvox-for-automatic-user-provisioning"></a>Självstudie: Konfigurera PlayVox för automatisk användar etablering

I den här självstudien beskrivs de steg som du följer i både PlayVox och Azure Active Directory (Azure AD) för att konfigurera automatisk användar etablering. När Azure AD konfigureras etablerar och avetablerar Azure AD automatiskt användare eller grupper i [PlayVox](https://www.playvox.com) med hjälp av Azure AD Provisioning-tjänsten. Viktig information om vad den här tjänsten gör och hur den fungerar och för vanliga frågor finns i [Automatisera användar etablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="capabilities-supported"></a>Funktioner som stöds
> [!div class="checklist"]
> * Skapa användare i PlayVox.
> * Ta bort användare i PlayVox när de inte behöver åtkomst längre.
> * Behåll användarattribut synkroniserade mellan Azure AD och PlayVox.

## <a name="prerequisites"></a>Förutsättningar

Scenariot i den här självstudien förutsätter att du redan har följande krav:

* [En Azure AD-klientorganisation](../develop/quickstart-create-new-tenant.md).
* Ett användar konto i Azure AD med [behörighet](../roles/permissions-reference.md) att konfigurera etableringen. Ett konto kan till exempel ha rollen program administratör, moln program administratör, program ägare eller global administratör.
* Ett användar konto i [PlayVox](https://www.playvox.com) med superadministratörs behörighet.

## <a name="step-1-plan-your-provisioning-deployment"></a>Steg 1: planera etablerings distributionen

1. Lär dig [hur etablerings tjänsten fungerar](../app-provisioning/user-provisioning.md).

2. Ta reda på vem som kommer att vara [inom omfånget för etablering](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

3. Ta reda på vilka data som ska [mappas mellan Azure AD och PlayVox](../app-provisioning/customize-application-attributes.md).

## <a name="step-2-configure-playvox-to-support-provisioning-by-using-azure-ad"></a>Steg 2: Konfigurera PlayVox till att stödja etablering med hjälp av Azure AD

1. Logga in på administrations konsolen för PlayVox och gå till **inställningar > API-nycklar**.

2. Välj **skapa API-nyckel**.

    ![Delvis skärm bild som visar platsen för knappen Skapa API-nyckel i användar gränssnittet för PlayVox.](media/playvox-provisioning-tutorial/create.png)

3. Ange ett beskrivande namn för API-nyckeln och välj sedan **Spara**. När du har genererat API-nyckeln väljer du **Stäng**.

4. Välj ikonen **information** på den API-nyckel som du har skapat.

    ![En del skärm bild som visar platsen för informations ikonen, som är ett förstorings glas, i PlayVox-användargränssnittet.](media/playvox-provisioning-tutorial/api.png)

5. Kopiera och spara värdet för **BASE64-nyckeln** . Senare i Azure Portal anger du det här värdet i text rutan **hemlig token** på fliken **etablering** i ditt PlayVox-program.

    ![Skärm bild av rutan information API-nyckel meddelande med värdet BASE64 markerat.](media/playvox-provisioning-tutorial/token.png)

## <a name="step-3-add-playvox-from-the-azure-ad-application-gallery"></a>Steg 3: Lägg till PlayVox från Azure AD-programgalleriet

För att börja hantera etablering till PlayVox, lägger du till PlayVox i Azure AD-klienten från program galleriet. Mer information finns i [snabb start: lägga till ett program till din Azure Active Directory (Azure AD)-klient](../manage-apps/add-application-portal.md).

Om du tidigare har konfigurerat PlayVox för enkel inloggning (SSO) kan du använda samma program. Vi rekommenderar dock att du skapar en separat app när du testar integrationen från början.

## <a name="step-4-define-who-will-be-in-scope-for-provisioning"></a>Steg 4: definiera vem som ska finnas inom omfånget för etablering

Du använder Azure AD Provisioning-tjänsten för det omfång som ska tillhandahållas, baserat antingen på tilldelning till programmet eller på attribut för användaren eller gruppen. Information om hur du tilldelar användare eller grupper till programmet finns i [hantera användar tilldelning för en app i Azure Active Directory](../manage-apps/assign-user-or-group-access-portal.md) om du vill veta mer om hur du tilldelar användare eller grupper till appen. Till omfång som endast ska tillhandahållas baserat på attribut för användaren eller gruppen använder du ett omfångs filter enligt beskrivningen i [attribut-baserad program etablering med omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

Kom ihåg följande punkter:

* När du tilldelar användare till PlayVox måste du välja en annan roll än standard åtkomst. Användare med rollen Standardåtkomst undantas från etableringen och markeras som icke-berättigade i etableringsloggarna. Om standard åtkomst är den enda rollen som är tillgänglig i programmet kan du [Uppdatera applikations manifestet](../develop/howto-add-app-roles-in-azure-ad-apps.md) för att lägga till andra roller.

* Starta i liten skala. Testa med en liten uppsättning användare eller grupper innan de distribueras till alla. När etablerings omfång baseras på tilldelade användare eller grupper kan du kontrol lera storleken på uppsättningen genom att endast tilldela en eller två användare eller grupper till appen. Om etablerings området innehåller alla användare och grupper kan du ange ett [attribut-baserat omfång-filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md) för att begränsa storleken på din test uppsättning.

## <a name="step-5-configure-automatic-user-provisioning-to-playvox"></a>Steg 5: Konfigurera automatisk användar etablering till PlayVox

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare eller grupper, baserat på användar-eller grupp tilldelningar i Azure AD.

Konfigurera automatisk användar etablering för PlayVox i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program** och välj sedan **alla program**.

    ![En del skärm bild av Azure Portal, med företags program och alla program objekt markerade](common/enterprise-applications.png)

2. Sök efter och välj **PlayVox** i listan program.

    ![En del skärm bild av program listan, där programmets sökruta är markerad.](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![En delvis skärm bild som visar ett meny alternativ för etablering.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av fliken etablering som visar det automatiska alternativet som marker ATS i list rutan etablerings läge.](common/provisioning-automatic.png)

5. I avsnittet **admin credentials** anger du din PlayVox- **klient-URL** som:

    `https://{tenant}.playvox.com/scim/v1`

    Ange den **hemliga token** som du kopierade tidigare i steg 2. Välj sedan **Testa anslutning** för att se till att Azure AD kan ansluta till PlayVox. Om anslutningen Miss lyckas kontrollerar du att PlayVox-kontot har administratörs behörighet och försöker igen.

    ![Del skärm bild som visar avsnittet admin-autentiseringsuppgifter, inklusive innehavarens URL och text rutor för hemliga token, och med länken testa anslutning markerad.](common/provisioning-testconnection-tenanturltoken.png)

6. I text rutan **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelandena. Markera sedan kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![Skärm bild som visar text rutan för e-postaviseringar och kryss rutan e-postmeddelande.](common/provisioning-notification-email.png)

7. Välj **Spara**.

8. I avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till PlayVox**.

9. Granska de användarattribut som synkroniseras från Azure AD till PlayVox i avsnittet **attribut-mappning** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i PlayVox för uppdaterings åtgärder. Om du väljer att ändra [matchande målattribut](../app-provisioning/customize-application-attributes.md)kontrollerar du att PlayVox-API: et stöder filtrering av användare baserat på detta attribut. Välj **Spara** för att genomföra ändringarna.

   |Attribut|Typ|Stöds för filtrering|
   |---|---|---|
   |userName|Sträng|&check;|
   |aktiv|Boolesk|
   |displayName|Sträng|
   |emails[type eq "work"].value|Sträng|
   |name.givenName|Sträng|
   |name.familyName|Sträng|
   |namn. formaterad|Sträng|
   |externalId|Sträng|

10. Information om hur du konfigurerar omfångs filter finns i kursen i avsnittet [omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

11. Om du vill aktivera Azure AD Provisioning-tjänsten för PlayVox ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Del skärm bild av avsnittet Inställningar, som visar etablerings statusen inställd på på.](common/provisioning-toggle-on.png)

12. I **Inställningar** definierar du de användare eller grupper som ska etableras för PlayVox genom att välja de värden som du vill ha i **omfånget**.

    ![Del skärm bild av avsnittet inställningar med list rutan omfattning.](common/provisioning-scope.png)

13. När du är redo att etablera väljer du **Spara**.

    ![Skärm bild som visar alternativ för Spara och ta bort.](common/provisioning-configuration-save.png)

Åtgärden startar den initiala synkroniseringscykeln för alla användare och grupper som har definierats i **Omfång** i avsnittet **Inställningar**. Den första cykeln tar längre tid än senare cykler. Senare cykler inträffar ungefär var 40: e minut, förutsatt att Azure AD Provisioning-tjänsten körs.

## <a name="step-6-monitor-your-deployment"></a>Steg 6: övervaka distributionen

När du har konfigurerat etableringen använder du följande resurser för att övervaka distributionen:

* Använd [etablerings loggarna](../reports-monitoring/concept-provisioning-logs.md) för att avgöra vilka användare som har etablerats eller har misslyckats.
* Kontrol lera [förlopps indikatorn](../app-provisioning/application-provisioning-when-will-provisioning-finish-specific-user.md) för att se status för etablerings cykeln och hur nära den är att slutföras.
* Om etableringskonfigurationen verkar innehålla fel, kommer programmet att placeras i karantän. Mer information om karantän tillstånd finns i [program etablering i karantän status](../app-provisioning/application-provisioning-quarantine-status.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)