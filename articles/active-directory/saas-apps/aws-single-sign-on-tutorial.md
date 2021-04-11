---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med enkel inloggning för AWS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AWS enkel inloggning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: jeedes
ms.openlocfilehash: 2d0b9e45dc5de0cd4550cf4b9f944fd33ebd7e7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720736"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-aws-single-sign-on"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med enkel inloggning för AWS

I den här självstudien får du lära dig hur du integrerar AWS enkel inloggning med Azure Active Directory (Azure AD). När du integrerar AWS enkel inloggning med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till AWS-enkel inloggning.
* Gör det möjligt för användarna att logga in automatiskt till AWS enkel inloggning med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* AWS enkel inloggning (SSO) med enkel inloggning aktive rad (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* AWS enkel inloggning stöder SP- **och IDP** -initierad SSO.

* AWS enkel inloggning stöder [**Automatisk användar etablering**](./aws-single-sign-on-provisioning-tutorial.md).

## <a name="adding-aws-single-sign-on-from-the-gallery"></a>Lägga till AWS enkel inloggning från galleriet

Om du vill konfigurera integrering av AWS enkel inloggning i Azure AD måste du lägga till AWS enkel inloggning från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **AWS enkel inloggning** i sökrutan.
1. Välj **AWS enkel inloggning** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-aws-single-sign-on"></a>Konfigurera och testa Azure AD SSO för enkel inloggning med AWS

Konfigurera och testa Azure AD SSO med enkel inloggning med AWS med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i AWS enkel inloggning.

Utför följande steg för att konfigurera och testa Azure AD SSO med enkel inloggning med AWS:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera AWS enkel inloggning](#configure-aws-single-sign-on-sso)** för enkel inloggning – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa AWS-test för enkel inloggning](#create-aws-single-sign-on-test-user)** för att få en motsvarighet till B. Simon i AWS enkel inloggning som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan för **enkel inloggning på AWS** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Om du har en **metadata-fil för service providern** går du till avsnittet **grundläggande SAML-konfiguration** och utför följande steg:

    a. Klicka på **Ladda upp metadatafil**.

    b. Klicka på **mapp-logotyp** för att välja den metadatafil som du har laddat ned från avsnittet **Konfigurera AWS enkel inloggning i SSO** (punkt 8) och klicka på **Lägg till**.

    ![image2](common/browse-upload-metadata.png)

    c. När metadatafilen har laddats upp, fylls **ID** och **svars-URL** -värden automatiskt i avsnittet grundläggande SAML-konfiguration:

    ![image3](common/idp-intiated.png)

    > [!Note]
    > Om värdena **Identifierare** och **Svars-URL** inte fylls i automatiskt fyller du i värdena manuellt enligt dina krav.

1. Om du inte har en **metadata-fil för tjänst leverantören** utför du följande steg i avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, ange värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<REGION>.signin.aws.amazon.com/platform/saml/<ID>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<REGION>.signin.aws.amazon.com/platform/saml/acs/<ID>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://portal.sso.<REGION>.amazonaws.com/saml/assertion/<ID>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [AWS-support teamet för enkel inloggning](mailto:aws-sso-partners@amazon.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. AWS-programmet för enkel inloggning förväntar sig SAML-intyg i ett visst format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)


    > [!NOTE]
    > Om ABAC har Aktiver ATS i AWS SSO kan ytterligare attribut skickas som sessionsbaserade direkt till AWS-konton.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera AWS enkel inloggning** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till AWS enkel inloggning.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **AWS enkel inloggning**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-aws-single-sign-on-sso"></a>Konfigurera AWS enkel inloggning

1. Om du vill automatisera konfigurationen i AWS enkel inloggning måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera AWS Single Sign-on** för att dirigera dig till AWS-programmet för enkel inloggning. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på AWS-enkel inloggning. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-10.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera AWS enkel inloggning manuellt i ett annat webbläsarfönster loggar du in på företagets webbplats för AWS-enkel inloggning som administratör.

1. Gå till **tjänsterna-> säkerhet, identitet, & kompatibilitet – > AWS för enkel inloggning**.
2. I det vänstra navigerings fönstret väljer du **Inställningar**.
3. På sidan **Inställningar** letar du reda på **identitets källa** och klickar på **ändra**.

    ![Skärm bild för ändrings tjänst för identitets källa](./media/aws-single-sign-on-tutorial/settings.png)

4. På sidan Ändra identitets källa väljer du **extern identitetsprovider**.

    
    ![Skärm bild för att välja avsnittet extern identitetsprovider](./media/aws-single-sign-on-tutorial/external-identity-provider.png)


1. Utför stegen nedan i avsnittet **Konfigurera extern identitets leverantör** :

    ![Skärm bild som visar avsnittet metadata för hämtning och uppladdning](./media/aws-single-sign-on-tutorial/upload-metadata.png)

    a. I avsnittet **metadata för tjänst leverantör** hittar du **AWS SSO SAML-metadata** och väljer **Hämta metadatafil** för att ladda ned metadatafilen och sparar den på datorn och använder den här metadatafilen för att ladda upp på Azure Portal.

    b. Kopiera **inloggnings-URL-värde för AWS SSO** , klistra in det här värdet i text rutan för **INLOGGNINGs-URL** i **avsnittet grundläggande SAML-konfiguration** i Azure Portal.

    c. I avsnittet **metadata för identitetsprovider** väljer du **Bläddra** för att ladda upp metadatafilen som du har laddat ned från Azure Portal.

    d. Välj **Nästa: granska**.

8. I text rutan skriver du **accepterar** för att ändra identitets källan.

    ![Skärm bild för att bekräfta konfigurationen](./media/aws-single-sign-on-tutorial/accept.png)

9. Klicka på **ändra identitets källa**.

### <a name="create-aws-single-sign-on-test-user"></a>Skapa test användare av AWS-enkel inloggning

1. Öppna **AWS SSO-konsolen**.

2. I det vänstra navigerings fönstret väljer **du användare**.

3. På sidan användare väljer du **Lägg till användare**.

4. Följ dessa steg på sidan Lägg till användare:

    a. I fältet **användar namn** anger du B. Simon.

    b. I fältet **e-postadress** anger du `username@companydomain.extension` . Till exempel `B.Simon@contoso.com`.

    c. I fältet **Bekräfta e-postadress** anger du e-postadressen igen från föregående steg.

    d. I fältet för det första namnet anger du `Jane` .

    e. I fältet efter namn anger du `Doe` .

    f. I fältet visnings namn anger du `Jane Doe` .

    ex. Välj **Nästa: grupper**.

    > [!NOTE]
    > Se till att det angivna användar namnet i AWS SSO matchar användarens inloggnings namn för Azure AD. Detta hjälper dig att undvika eventuella autentiseringsfel.

5. Välj **Lägg till användare**.
6. Sedan tilldelar du användaren till ditt AWS-konto. Det gör du genom att välja **AWS-konton** i det vänstra navigerings fönstret i AWS SSO-konsolen.
7. På sidan AWS-konton väljer du fliken AWS organisation, markerar kryss rutan bredvid det AWS-konto som du vill tilldela till användaren. Välj sedan **tilldela användare**.
8. På sidan tilldela användare letar du upp och markerar kryss rutan bredvid användaren B. Simon. Välj sedan **Nästa: behörighets uppsättningar**.
9. Under avsnittet Välj behörighets uppsättningar markerar du kryss rutan bredvid den behörighets uppsättning som du vill tilldela till användaren B. Simon. Om du inte har en befintlig behörighets uppsättning väljer du **Skapa ny behörighets uppsättning**.

    > [!NOTE]
    > Behörighets uppsättningar definierar åtkomst nivån som användare och grupper har till ett AWS-konto. Mer information om behörighets uppsättningar finns på sidan AWS SSO **permission Sets** .
10. Välj **Slutför**.

> [!NOTE]
> AWS enkel inloggning stöder även automatisk användar etablering, du hittar mer information [här](./aws-single-sign-on-provisioning-tutorial.md) om hur du konfigurerar automatisk användar etablering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till AWS-inloggnings-URL för enkel inloggning där du kan starta inloggnings flödet.  

* Gå till AWS inloggnings-URL för enkel inloggning direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den AWS enkel inloggning för vilken du konfigurerar SSO 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen för enkel inloggning i AWS i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till AWS enkel inloggning för vilken du konfigurerar SSO. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).


## <a name="next-steps"></a>Nästa steg

När du har konfigurerat AWS enkel inloggning kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).