---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SumoLogic | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SumoLogic.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: a14235cb4fc20147accc8eb8a0e421cbc4df90f8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533702"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sumologic"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med SumoLogic

I den här självstudien får du lära dig hur du integrerar SumoLogic med Azure Active Directory (Azure AD). När du integrerar SumoLogic med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SumoLogic.
* Gör det möjligt för användarna att logga in automatiskt till SumoLogic med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SumoLogic för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SumoLogic stöder **IDP** INITIERAd SSO

## <a name="adding-sumologic-from-the-gallery"></a>Lägga till SumoLogic från galleriet

Om du vill konfigurera integreringen av SumoLogic i Azure AD måste du lägga till SumoLogic från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SumoLogic** i sökrutan.
1. Välj **SumoLogic** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sumologic"></a>Konfigurera och testa enkel inloggning med Azure AD för SumoLogic

Konfigurera och testa Azure AD SSO med SumoLogic med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SumoLogic.

Om du vill konfigurera och testa Azure AD SSO med SumoLogic, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera SUMOLOGIC SSO](#configure-sumologic-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa SumoLogic test User](#create-sumologic-test-user)** -om du vill ha en motsvarighet till B. Simon i SumoLogic som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **SumoLogic** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    - `https://service.sumologic.com`
    - `https://<tenantname>.us2.sumologic.com`
    - `https://<tenantname>.us4.sumologic.com`
    - `https://<tenantname>.eu.sumologic.com`
    - `https://<tenantname>.jp.sumologic.com`
    - `https://<tenantname>.de.sumologic.com`
    - `https://<tenantname>.ca.sumologic.com`

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    - `https://service.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us2.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.us4.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.eu.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.jp.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.de.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.ca.sumologic.com/sumo/saml/consume/<tenantname>`
    - `https://service.au.sumologic.com/sumo/saml/consume/<tenantname>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL. Kontakta [SumoLogic client support team](https://www.sumologic.com/contact-us/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. SumoLogic-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig SumoLogic-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    |  Name | Källattribut |
    | ---------------| --------------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Roller | user.assignedroles |

    > [!NOTE]
    > Klicka [här](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) om du vill veta mer om hur du konfigurerar **roller** i Azure AD.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera SumoLogic** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SumoLogic.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SumoLogic**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-sumologic-sso"></a>Konfigurera SumoLogic SSO

1. Logga in på din SumoLogic-företags webbplats som administratör i ett annat webbläsarfönster.

1. Gå till **hantera \> säkerhet**.

    ![Hantera](./media/sumologic-tutorial/ic778556.png "Hantera")

1. Klicka på **SAML**.

    ![Globala säkerhets inställningar](./media/sumologic-tutorial/ic778557.png "Globala säkerhets inställningar")

1. Välj **Azure AD**i listan **Välj en konfiguration eller skapa en ny** , och klicka sedan på **Konfigurera**.

    ![Konfigurera SAML 2,0](./media/sumologic-tutorial/ic778558.png "Konfigurera SAML 2,0")

1. I dialog rutan **Konfigurera SAML 2,0** utför du följande steg:

    ![Konfigurera SAML 2,0](./media/sumologic-tutorial/ic778559.png "Konfigurera SAML 2,0")

    a. I text rutan **konfigurations namn** skriver du **Azure AD**.

    b. Välj **fel söknings läge**.

    c. I text rutan **utfärdare** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    d. I text rutan **authn-URL för begäran** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    e. Öppna ditt bas-64-kodade certifikat i anteckningar, kopiera innehållet i det till Urklipp och klistra sedan in hela certifikatet i text rutan för **X. 509-certifikat** .

    f. Som **email-attribut**väljer du **Använd SAML subject**.  

    ex. Välj **SP initierad inloggnings konfiguration**.

    h. I text rutan **inloggnings Sök väg** skriver du **Azure** och klickar på **Spara**.

### <a name="create-sumologic-test-user"></a>Skapa SumoLogic test användare

För att Azure AD-användare ska kunna logga in på SumoLogic måste de tillhandahållas till SumoLogic. När det gäller SumoLogic är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **SumoLogic** -klient.

1. Gå till **Hantera \> Användare**.

    ![Användare](./media/sumologic-tutorial/ic778561.png "Användare")

1. Klicka på **Lägg till**.

    ![Användare](./media/sumologic-tutorial/ic778562.png "Användare")

1. Utför följande steg i dialog rutan **ny användare** :

    ![Ny användare](./media/sumologic-tutorial/ic778563.png "Ny användare")

    a. Skriv relaterad information för det Azure AD-konto som du vill etablera i text rutorna för **förnamn**, **efter namn**och **e-post** .
  
    b. Välj en roll.
  
    c. Som **status**väljer du **aktiv**.
  
    d. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg för SumoLogic av användar konton eller API: er som tillhandahålls av SumoLogic för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen SumoLogic på åtkomst panelen, bör du loggas in automatiskt på den SumoLogic som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova SumoLogic med Azure AD](https://aad.portal.azure.com/)