---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med StatusPage | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och StatusPage.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: cc3ce56ecd17d627001f4925355c055afdc09d22
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98729673"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-statuspage"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med StatusPage

I den här självstudien får du lära dig hur du integrerar StatusPage med Azure Active Directory (Azure AD).
Genom att integrera StatusPage med Azure AD får du följande fördelar:

* Du kan styra i Azure AD som har åtkomst till StatusPage.
* Du kan göra det möjligt för användarna att logga in automatiskt till StatusPage (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med StatusPage behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* StatusPage-aktiverad prenumeration med enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* StatusPage stöder **IDP** INITIERAd SSO

## <a name="adding-statuspage-from-the-gallery"></a>Lägga till StatusPage från galleriet

Om du vill konfigurera integreringen av StatusPage i Azure AD måste du lägga till StatusPage från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **StatusPage** i sökrutan.
1. Välj **StatusPage** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-statuspage"></a>Konfigurera och testa Azure AD SSO för StatusPage

I det här avsnittet konfigurerar och testar du enkel inloggning med Azure AD med StatusPage baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i StatusPage upprättas.

Utför följande steg för att konfigurera och testa Azure AD SSO med StatusPage:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    1. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera STATUSPAGE SSO](#configure-statuspage-sso)** – om du vill konfigurera inställningar för enskilda Sign-On på program sidan.
    1. **[Skapa StatusPage test User](#create-statuspage-test-user)** – om du vill ha en motsvarighet till Britta Simon i StatusPage som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **AskYourTeam** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. På sidan **Konfigurera enkel inloggning med SAML** utför du följande steg:

    a. I text rutan **identifierare** anger du en URL med något av följande mönster:

    | Identifierare |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/` |
    | `https://<subdomain>.statuspage.io/` |
    |

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

     | Svars-URL |
    |--------------|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume` |
    | `https://<subdomain>.statuspage.io/sso/saml/consume` |
    |

    > [!NOTE]
    > Kontakta StatusPage support-teamet på [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io) för att begära metadata som krävs för att konfigurera enkel inloggning. 
    >
    > a. Kopiera utfärdarens värde från metadata och klistra in det i text rutan **identifierare** .
    >
    > b. Kopiera svars-URL: en från metadata och klistra in den i text rutan **svars-URL** .

5. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera StatusPage** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till StatusPage.

1. I Azure Portal väljer du **företags program**, väljer **alla program** och väljer sedan **StatusPage**.

2. I listan program väljer du **StatusPage**.

3. På menyn till vänster väljer du **Användare och grupper**.

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-statuspage-sso"></a>Konfigurera StatusPage SSO

1. Om du vill automatisera konfigurationen i StatusPage måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera StatusPage** för att dirigera dig till StatusPage-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på StatusPage. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera StatusPage manuellt i ett annat webbläsarfönster loggar du in på StatusPage Company-platsen som administratör.

1. Klicka på **Hantera konto** i huvud verktygsfältet.

    ![Skärm bild som visar hanterat konto valt från StatusPage företags webbplats.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på fliken **enkel inloggning** .

    ![Skärm bild som visar fliken enkel inloggning.](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Utför följande steg på sidan SSO-konfiguration:

    ![Skärm bild som visar sidan S O-inställningar där du kan ange de värden som beskrivs.](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Skärm bild som visar knappen Spara konfiguration.](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. I text rutan för **SSO-mål-URL** klistrar du in värdet för **inloggnings-URL: en** som du har kopierat från Azure Portal.

    b. Öppna det hämtade certifikatet i anteckningar, kopiera innehållet och klistra in det i text rutan **certifikat** .

    c. Klicka på **Spara konfiguration**.

### <a name="create-statuspage-test-user"></a>Skapa StatusPage test användare

Syftet med det här avsnittet är att skapa en användare som kallas Britta Simon i StatusPage.

StatusPage stöder just-in-Time-etablering. Du har redan aktiverat det i [Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-sso).

**Utför följande steg för att skapa en användare med namnet Britta Simon i StatusPage:**

1. Logga in på din StatusPage företags webbplats som administratör.

1. I menyn högst upp klickar du på **Hantera konto**.

    ![Skärm bild som visar hanterat konto valt från StatusPage företags webbplats.](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klicka på fliken **grupp medlemmar** .
  
    ![Skärm bild som visar fliken grupp medlemmar.](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klicka på **Lägg till grupp medlem**.
  
    ![Skärm bild som visar knappen Lägg till grupp medlem.](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Ange **e-postadress**, **förnamn** och efter **namn** för en giltig användare som du vill etablera i de relaterade text rutorna. 

    ![Skärm bild som visar dialog rutan Lägg till en användare där du kan ange de värden som beskrivs.](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Som **roll** väljer du **klient administratör**.

1. Klicka på **Skapa konto**.

### <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ.

* Klicka på testa det här programmet i Azure Portal så bör du loggas in automatiskt på den StatusPage som du har konfigurerat SSO för.

* Du kan använda Microsoft Mina appar. När du klickar på panelen StatusPage i Mina appar, bör du loggas in automatiskt på den StatusPage som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat StatusPage kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).