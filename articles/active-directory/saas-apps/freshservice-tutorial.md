---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Freshservice | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freshservice.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: fb8b12cdb8fd9ed37ac4086d213183e800d6febc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101651772"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Freshservice

I den här självstudien får du lära dig hur du integrerar Freshservice med Azure Active Directory (Azure AD). När du integrerar Freshservice med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Freshservice.
* Gör det möjligt för användarna att logga in automatiskt till Freshservice med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Freshservice för enkel inloggning (SSO) aktive rad.

> [!NOTE]
> Den här integreringen är också tillgänglig för användning från Azure AD amerikanska myndigheters moln miljö. Du hittar det här programmet i Cloud App-galleriet för Azure AD amerikanska myndigheter och konfigurerar det på samma sätt som du gör från det offentliga molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Freshservice stöder **IDP**-initierad enkel inloggning

## <a name="add-freshservice-from-the-gallery"></a>Lägg till Freshservice från galleriet

För att konfigurera integreringen av Freshservice till Azure AD behöver du lägga till Freshservice från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Freshservice** i sökrutan.
1. Välj **Freshservice** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-freshservice"></a>Konfigurera och testa Azure AD SSO för Freshservice

Konfigurera och testa Azure AD SSO med Freshservice med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Freshservice.

Om du vill konfigurera och testa Azure AD SSO med Freshservice, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera FRESHSERVICE SSO](#configure-freshservice-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Freshservice test User](#create-freshservice-test-user)** -om du vill ha en motsvarighet till B. Simon i Freshservice som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **Freshservice** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<company-name>.freshservice.com`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<company-name>.freshservice.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<company-name>.freshservice.com/login/saml`
    
    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk inloggnings-URL, identifierare och svars-URL. Hämta dessa värden genom att kontakta [supportteamet för Freshservice-klienten](https://support.freshservice.com/). Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera en enskild Sign-On med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Freshservice** på **Azure Portal** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Freshservice.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I programlistan väljer du **Freshservice**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-freshservice-sso"></a>Konfigurera Freshservice SSO

1. Om du vill automatisera konfigurationen i Freshservice måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren kan du klicka på **Konfigurera Freshservice** för att dirigera dig till Freshservice-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Freshservice. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera Freshservice manuellt loggar du in på din Freshservice-företags plats som administratör.

1. Klicka på **admin** på menyn till vänster och välj **supportavdelningen** i de **allmänna inställningarna**.

    ![Administratör](./media/freshservice-tutorial/configure-1.png "Administratör")

1. I **säkerhet** klickar **du på gå till Freshservice 360-säkerhet**.

    ![Säkerhet](./media/freshservice-tutorial/configure-2.png "Säkerhet")

1. I avsnittet **säkerhet** utför du följande steg:

    ![Enkel inloggning](./media/freshservice-tutorial/configure-3.png "Enkel inloggning")
  
    a. För **enkel inloggning** väljer du **på**.

    b. I **inloggnings metoden** väljer du **SAML SSO**.

    c. I det **entitets-ID som tillhandahålls av** text rutan IDP klistrar du in **entitets-ID** -värdet, som du har kopierat från Azure Portal.

    d. I text rutan för **SAML SSO URL** klistrar du in **inloggnings-URL** -värde, som du har kopierat från Azure Portal.

    e. I **signerings alternativen** väljer du **enbart signerade intyg** från List rutan.

    f. I textrutan **Utloggnings-URL** klistrar du värdet för den **utloggnings-URL** som du har kopierat från Azure-portalen.

    ex. I text rutan **säkerhetscertifikat** , klistra in **certifikat (base64)** som du har hämtat tidigare.
  
    h. Klicka på **Spara**.


## <a name="create-freshservice-test-user"></a>Skapa Freshservice-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på FreshService måste de tillhandahållas i FreshService. När det gäller FreshService är etablering en manuell aktivitet.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din **FreshService** -företags webbplats som administratör.

2. Klicka på **admin** i menyn till vänster.

3. I avsnittet **Användarhantering** klickar du på **Beställare**.

    ![Frågare](./media/freshservice-tutorial/create-user-1.png "Frågare")

4. Klicka på **Ny beställare**.

    ![Nya beställare](./media/freshservice-tutorial/create-user-2.png "Nya beställare")

5. I avsnittet **ny beställare** anger du de obligatoriska fälten och klickar på **Spara**.
    ![Ny beställare](./media/freshservice-tutorial/create-user-3.png "Ny beställare")  

    > [!NOTE]
    > Azure Active Directory-kontoinnehavaren får ett e-postmeddelande med en länk för att bekräfta kontot innan det blir aktivt
    >  

    > [!NOTE]
    > Du kan använda andra verktyg för FreshService av användar konton eller API: er som tillhandahålls av FreshService för att etablera Azure AD-användarkonton.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Freshservice-inloggnings-URL där du kan starta inloggnings flödet. 

* Gå till Freshservice-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på panelen Freshservice i Mina appar, bör du loggas in automatiskt på den Freshservice som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

 När du har konfigurerat Freshservice kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).