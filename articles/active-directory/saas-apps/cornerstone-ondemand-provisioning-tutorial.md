---
title: 'Självstudie: Konfigurera en hörn OnDemand för automatisk användar etablering med Azure Active Directory | Microsoft Docs'
description: Lär dig hur du konfigurerar Azure Active Directory att automatiskt etablera och avetablera användar konton till ett hörn OnDemand.
services: active-directory
author: zhchia
writer: zhchia
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: article
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 4200bc9879aba5e18282123be03576cc617a5e14
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549238"
---
# <a name="tutorial-configure-cornerstone-ondemand-for-automatic-user-provisioning"></a>Självstudie: Konfigurera en hörn OnDemand för automatisk användar etablering

I den här självstudien beskrivs de steg som du utför i hörn OnDemand och Azure Active Directory (Azure AD) för att konfigurera Azure AD att automatiskt etablera och avetablera användare eller grupper till ett hörn OnDemand.

> [!NOTE]
> I den här självstudien beskrivs en koppling som är byggd ovanpå Azure AD-tjänsten för användar etablering. Information om vad den här tjänsten gör, hur den fungerar och vanliga frågor finns i [Automatisera användar etablering och avetablering av SaaS-program (Software-as-a-Service) med Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Förutsättningar

Det scenario som beskrivs i den här självstudien förutsätter att du har:

* En Azure AD-klientorganisation.
* En hörn OnDemand OnDemand-klient.
* Ett användar konto i hörn OnDemand med administratörs behörighet.

> [!NOTE]
> Integreringen med Azure AD Provisioning förlitar sig på [webb tjänsten för hörn OnDemand](https://www.cornerstoneondemand.com/). Den här tjänsten är tillgänglig för de viktigaste OnDemand-teamen.

## <a name="add-cornerstone-ondemand-from-the-azure-marketplace"></a>Lägg till ett hörn OnDemand från Azure Marketplace

Innan du konfigurerar ett hörn OnDemand för automatisk användar etablering med Azure AD lägger du till ett hörn OnDemand från Marketplace till din lista över hanterade SaaS-program.

Följ dessa steg om du vill lägga till ett hörn OnDemand från Marketplace.

1. I navigerings fönstret till vänster i [Azure Portal](https://portal.azure.com)väljer du **Azure Active Directory**.

    ![Ikonen Azure Active Directory](common/select-azuread.png)

2. Gå till **företags program**och välj sedan **alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att välja **Nytt program** längst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan anger du ett **hörn OnDemand** och väljer ett **hörn OnDemand** från resultat panelen. Om du vill lägga till programmet väljer du **Lägg till**.

    ![Cornerstone OnDemand i resultatlistan](common/search-new-app.png)

## <a name="assign-users-to-cornerstone-ondemand"></a>Tilldela användare till hörn OnDemand

Azure Active Directory använder ett begrepp som kallas *tilldelningar* för att avgöra vilka användare som ska få åtkomst till valda appar. I samband med automatisk användar etablering synkroniseras endast de användare eller grupper som har tilldelats till ett program i Azure AD.

Innan du konfigurerar och aktiverar automatisk användar etablering ska du bestämma vilka användare eller grupper i Azure AD som behöver åtkomst till ett hörn OnDemand. Om du vill tilldela dessa användare eller grupper till ett hörn OnDemand, följer du anvisningarna i [tilldela en användare eller grupp till en Enterprise-App](../manage-apps/assign-user-or-group-access-portal.md).

### <a name="important-tips-for-assigning-users-to-cornerstone-ondemand"></a>Viktiga tips för att tilldela användare till ett hörn OnDemand

* Vi rekommenderar att du tilldelar en enda Azure AD-användare till ett hörn OnDemand för att testa den automatiska konfigurationen av användar etablering. Du kan tilldela ytterligare användare eller grupper senare.

* När du tilldelar en användare till ett hörn OnDemand, väljer du en giltig programspecifik roll, om sådan finns, i dialog rutan tilldelning. Användare med **standard åtkomst** rollen undantas från etablering.

## <a name="configure-automatic-user-provisioning-to-cornerstone-ondemand"></a>Konfigurera automatisk användar etablering till hörn OnDemand

Det här avsnittet vägleder dig genom stegen för att konfigurera Azure AD Provisioning-tjänsten. Använd den för att skapa, uppdatera och inaktivera användare eller grupper i hörn OnDemand utifrån användar-eller grupp tilldelningar i Azure AD.

Följ dessa steg om du vill konfigurera automatisk användar etablering för ett hörn OnDemand i Azure AD.

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **företags program**  >  **alla programs**  >  **hörn OnDemand**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **hörn OnDemand**.

    ![Länken för hörn OnDemandn i program listan](common/all-applications.png)

3. Välj fliken **etablering** .

    ![Konfiguration av hörn OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningTab.png)

4. Ange **etablerings läget** till **Automatisk**.

    ![Etablerings läge för hörn OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningCredentials.png)

5. Under avsnittet **admin credentials** anger du administratörs användar namn, administratörs lösen ord och domän för ditt konto för ditt hörn OnDemand:

    * I rutan **Administratörs användar namn** fyller du i domänen eller användar namnet för administratörs kontot på ditt hörn OnDemand-klient. Ett exempel är Contoso\admin.

    * I rutan **Administratörs lösen ord** fyller du i lösen ordet som motsvarar administratörens användar namn.

    * I rutan **domän** fyller du i webb tjänstens URL för hörn ondemandn. Till exempel finns tjänsten på `https://ws-[corpname].csod.com/feed30/clientdataservice.asmx` och för contoso är domänen `https://ws-contoso.csod.com/feed30/clientdataservice.asmx` . Mer information om hur du hämtar webb tjänstens URL finns i [den här PDF-filen](https://help.csod.com/help/csod_0/Content/Resources/Documents/WebServices/CSOD_Web_Services_-_User-OU_Technical_Specification_v20160222.pdf).

6. När du har fyllt i rutorna som visas i steg 5 väljer du **Testa anslutning** för att kontrol lera att Azure AD kan ansluta till ett hörn OnDemand. Om anslutningen Miss lyckas kontrollerar du att ditt konto för ditt hörn OnDemand har administratörs behörighet och försöker igen.

    ![Test anslutning för hörn OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/TestConnection.png)

7. I rutan **aviserings-e** -postadress anger du e-postadressen till den person eller grupp som ska ta emot etablerings fel meddelanden. Markera kryss rutan **Skicka ett e-postmeddelande när ett fel inträffar** .

    ![E-postmeddelande om ett hörn OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/EmailNotification.png)

8. Välj **Spara**.

9. Under avsnittet **mappningar** väljer du **Synkronisera Azure Active Directory användare till hörn OnDemand**.

    ![Synkronisering av hörn OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMapping.png)

10. Granska de användarattribut som synkroniseras från Azure AD till en hörn-OnDemand i avsnittet **mappningar av attribut** . Attributen som väljs som **matchande** egenskaper används för att matcha användar kontona i hörn-OnDemand för uppdaterings åtgärder. Välj **Spara**om du vill spara ändringarna.

    ![Attribut mappningar för hörn OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/UserMappingAttributes.png)

11. Följ anvisningarna i [kursen omfångs filter](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)för att konfigurera omfångs filter.

12. Om du vill aktivera Azure AD Provisioning-tjänsten för en hörn OnDemand i avsnittet **Inställningar** ändrar du **etablerings statusen** till **på**.

    ![Etablerings status för hörn OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/ProvisioningStatus.png)

13. Definiera de användare eller grupper som du vill etablera till hörn OnDemand. I avsnittet **Inställningar** väljer du de värden som du vill ha i **omfånget**.

    ![Omfattning av hörn OnDemand](./media/cornerstone-ondemand-provisioning-tutorial/SyncScope.png)

14. När du är redo att etablera väljer du **Spara**.

    ![Hörn OnDemand OnDemand Spara](./media/cornerstone-ondemand-provisioning-tutorial/Save.png)

Den här åtgärden startar den första synkroniseringen av alla användare eller grupper som definierats i **området** i avsnittet **Inställningar** . Den inledande synkroniseringen tar längre tid att utföra än senare synkroniseringar. De inträffar ungefär var 40: e minut så länge Azure AD Provisioning-tjänsten körs. 

Du kan använda avsnittet **synkroniseringsinformation** om du vill övervaka förloppet och följa länkar till etablerings aktivitets rapporten. Rapporten beskriver alla åtgärder som utförs av Azure AD Provisioning-tjänsten på hörn OnDemand.

Information om hur du läser etablerings loggarna i Azure AD finns i [rapportering om automatisk etablering av användar konton](../app-provisioning/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Kopplings begränsningar

Attributet för den här **positionen** förväntar sig ett värde som motsvarar rollerna i hörn OnDemand-portalen. Om du vill hämta en lista över giltiga **positions** värden går du till **redigera användar post > organisations struktur > position** i portalen för hörn OnDemand.

![Hörn OnDemand OnDemand-etablering redigera användar post](./media/cornerstone-ondemand-provisioning-tutorial/UserEdit.png)

![Positions-OnDemand-etablerings position](./media/cornerstone-ondemand-provisioning-tutorial/UserPosition.png)

![Placerings lista för plats-OnDemand-etablering](./media/cornerstone-ondemand-provisioning-tutorial/PostionId.png)

## <a name="additional-resources"></a>Ytterligare resurser

* [Hantera användar konto etablering för företags program](../app-provisioning/configure-automatic-user-provisioning-portal.md)
* [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nästa steg

* [Lär dig hur du granskar loggar och hämtar rapporter om etablerings aktivitet](../app-provisioning/check-status-user-account-provisioning.md)

<!--Image references-->
[1]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_01.png
[2]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_02.png
[3]: ./media/cornerstone-ondemand-provisioning-tutorial/tutorial_general_03.png
