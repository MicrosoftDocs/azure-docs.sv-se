---
title: 'Självstudie: Konfigurera Figma automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till Figma.
services: active-directory
author: zchia
writer: zchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/12/2019
ms.author: zhchia
ms.openlocfilehash: ec509cc04db87602b3e29230253d9b363a5e30d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96353926"
---
# <a name="tutorial-configure-figma-for-automatic-user-provisioning"></a>Självstudie: Konfigurera Figma för automatisk användar etablering

Syftet med den här självstudien är att demonstrera de steg som ska utföras i Figma och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare och/eller grupper till Figma.

> [!NOTE]
> I den här självstudien beskrivs en koppling som skapats ovanpå Azure AD-tjänsten för användar etablering. Viktig information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användaretablering och avetablering för SaaS-program med Azure Active Directory](../app-provisioning/user-provisioning.md).
>
> Den här anslutningen är för närvarande en offentlig för hands version. Mer information om allmänna Microsoft Azure användnings villkor för för hands versions funktioner finns i kompletterande användnings [villkor för Microsoft Azure för](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)hands versioner.

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du redan har följande krav:

* En Azure AD-klientorganisation.
* [En Figma-klient](https://www.figma.com/pricing/).
* Ett användar konto i Figma med administratörs behörighet.

## <a name="assign-users-to-figma"></a>Tilldela användare till Figma.
Azure Active Directory använder ett begrepp som kallas tilldelningar för att avgöra vilka användare som ska få åtkomst till valda appar. I kontexten för automatisk användar etablering synkroniseras endast de användare och/eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering bör du bestämma vilka användare och/eller grupper i Azure AD som behöver åtkomst till Figma. När du har bestämt dig kan du tilldela dessa användare och/eller grupper till Figma genom att följa anvisningarna här:
 
* [Tilldela en användare eller grupp till en företags app](../manage-apps/assign-user-or-group-access-portal.md)
## <a name="important-tips-for-assigning-users-to-figma"></a>Viktiga tips för att tilldela användare till Figma

 * Vi rekommenderar att en enda Azure AD-användare tilldelas Figma för att testa den automatiska konfigurationen av användar etablering. Ytterligare användare och/eller grupper kan tilldelas senare.

* När du tilldelar en användare till Figma måste du välja en giltig programspecifik roll (om tillgängligt) i tilldelnings dialog rutan. Användare med standard åtkomst rollen undantas från etablering.

## <a name="set-up-figma-for-provisioning"></a>Konfigurera Figma för etablering

Innan du konfigurerar Figma för automatisk användar etablering med Azure AD måste du hämta viss etablerings information från Figma.

1. Logga in på din [Figma-administratörs konsol](https://www.Figma.com/). Klicka på kugg hjuls ikonen bredvid din klient.

    :::image type="content" source="media/Figma-provisioning-tutorial/image0.png" alt-text="Skärm bild av Figma-administratörskonsolen. En klient som heter ett D scim-test är synlig. Bredvid klienten markeras en kugg hjuls ikon." border="false":::

2. Gå till **Inställningar för allmän > uppdaterings inloggning**.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma03.png" alt-text="Skärm bild av fliken Allmänt i Figma-administratörskonsolen. Under inloggning och etablering, markeras uppdatera inloggnings inställningar." border="false":::

3. Kopiera **klient-ID: t**. Det här värdet används för att skapa slut punkts-URL: en för SCIM som ska anges i fältet **klient-URL** på fliken etablering i ditt Figma-program i Azure Portal.

    :::image type="content" source="media/Figma-provisioning-tutorial/figma-tenantid.png" alt-text="Skärm bild av avsnittet S A M L s O i Figma-administratörskonsolen. En klient-ID-etikett och en intilliggande länk med texten Copy är markerad." border="false":::

4. Rulla nedåt och klicka på **generera API-token**.

    :::image type="content" source="media/Figma-provisioning-tutorial/token.png" alt-text="Skärm bild av avsnittet S C i M-etablering i Figma-administratörskonsolen. En länk som heter generera en P-token har marker ATS." border="false":::

5. Kopiera värdet för  **API-token** . Det här värdet anges i fältet **hemlig token** på fliken etablering i ditt Figma-program i Azure Portal. 

    :::image type="content" source="media/Figma-provisioning-tutorial/figma04.png" alt-text="Skärm bild av en sida i Figma-administratörskonsolen. Under din etablering av en P I-token markeras en plats hållare för token." border="false":::

## <a name="add-figma-from-the-gallery"></a>Lägg till Figma från galleriet

Om du vill konfigurera Figma för automatisk användar etablering med Azure AD måste du lägga till Figma från Azure AD-programgalleriet i listan över hanterade SaaS-program.

1. Välj **Azure Active Directory** i den vänstra navigerings panelen i **[Azure Portal](https://portal.azure.com)**.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **företags program** och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Om du vill lägga till ett nytt program väljer du knappen **nytt program** överst i fönstret.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du **Figma**, väljer **Figma** i resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

    ![Figma i resultat listan](common/search-new-app.png)

## <a name="configuring-automatic-user-provisioning-to-figma"></a>Konfigurera automatisk användar etablering till Figma 

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten för att skapa, uppdatera och inaktivera användare och/eller grupper i Figma baserat på användar-och/eller grupp tilldelningar i Azure AD.

> [!TIP]
> Du kan också välja att aktivera SAML-baserad enkel inloggning för Figma genom att följa anvisningarna i [självstudien om enkel inloggning med Figma](figma-tutorial.md). Enkel inloggning kan konfigureras oberoende av automatisk användar etablering, även om dessa två funktioner är gemensamt.

### <a name="to-configure-automatic-user-provisioning-for-figma--in-azure-ad"></a>Konfigurera automatisk användar etablering för Figma i Azure AD:

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **Företagsprogram** och sedan **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Figma**.

    ![Figma-länken i program listan](common/all-applications.png)

3. Välj fliken **Etablering**.

    ![Skärm bild av alternativen för att hantera med etablerings alternativet.](common/provisioning.png)

4. Ange **Etableringsläge** som **Automatiskt**.

    ![Skärm bild av list rutan etablerings läge med det automatiska alternativet inringat.](common/provisioning-automatic.png)

5. Under avsnittet **admin credentials** , inmatat `https://www.figma.com/scim/v2/<TenantID>` i **klient-URL** där **TenantID** är värdet som du hämtade från Figma tidigare. Mata in värdet för **API-token** i **hemlig token**. Klicka på **Testa anslutning** för att se till att Azure AD kan ansluta till Figma. Om anslutningen Miss lyckas kontrollerar du att Figma-kontot har administratörs behörighet och försöker igen.

    ![Klient-URL + token](common/provisioning-testconnection-tenanturltoken.png)

8. I fältet **e-postavisering** anger du e-postadressen till den person eller grupp som ska få etablerings fel meddelanden och markerar kryss rutan – **Skicka ett e-postmeddelande när ett fel uppstår**.

    ![E-postavisering](common/provisioning-notification-email.png)

9. Klicka på **Spara**.

10. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till Figma**.

    ![Figma användar mappningar](media/Figma-provisioning-tutorial/figma05.png)

11. Granska de användarattribut som synkroniseras från Azure AD till Figma i avsnittet **Mappning av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i Figma för uppdaterings åtgärder. Välj knappen **Spara** för att spara ändringarna.

    ![Figma-användarattribut](media/Figma-provisioning-tutorial/figma06.png)

12. Information om hur du konfigurerar omfångsfilter finns i följande instruktioner i [självstudien för omfångsfilter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md).

13. Om du vill aktivera Azure AD Provisioning-tjänsten för Figma ändrar du **etablerings statusen** till **på** i avsnittet **Inställningar** .

    ![Etableringsstatus är på](common/provisioning-toggle-on.png)

14. Definiera de användare och/eller grupper som du vill etablera till Figma genom att välja önskade värden i **omfång** i avsnittet **Inställningar** .

    ![Etableringsomfång](common/provisioning-scope.png)

15. När du är redo att etablera klickar du på **Spara**.

    ![Spara etableringskonfiguration](common/provisioning-configuration-save.png)

Den här åtgärden startar den första synkroniseringen av alla användare och/eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än efterföljande synkroniseringar, vilket inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. Du kan använda avsnittet **synkroniseringsinformation** för att övervaka förloppet och följa länkar till etablerings aktivitets rapporten, som beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på Figma.

Mer information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användarkontoetablering för Enterprise-appar](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig att granska loggar och hämta rapporter om etableringsaktivitet](../app-provisioning/check-status-user-account-provisioning.md)