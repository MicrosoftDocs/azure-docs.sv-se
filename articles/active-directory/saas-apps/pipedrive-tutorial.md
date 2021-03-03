---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med PipeDrive | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och PipeDrive.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/05/2021
ms.author: jeedes
ms.openlocfilehash: 96abe2209d4298f23c47c62fec970430f69cbaf6
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101654433"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pipedrive"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med PipeDrive

I den här självstudien får du lära dig hur du integrerar PipeDrive med Azure Active Directory (Azure AD). När du integrerar PipeDrive med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till PipeDrive.
* Gör det möjligt för användarna att logga in automatiskt till PipeDrive med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* PipeDrive för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* PipeDrive stöder **SP-och IDP** -INITIERAd SSO

## <a name="add-pipedrive-from-the-gallery"></a>Lägg till PipeDrive från galleriet

Om du vill konfigurera integreringen av PipeDrive i Azure AD måste du lägga till PipeDrive från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **PipeDrive** i sökrutan.
1. Välj **PipeDrive** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-pipedrive"></a>Konfigurera och testa Azure AD SSO för PipeDrive

Konfigurera och testa Azure AD SSO med PipeDrive med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i PipeDrive.

Utför följande steg för att konfigurera och testa Azure AD SSO med PipeDrive:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera PIPEDRIVE SSO](#configure-pipedrive-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa PipeDrive test User](#create-pipedrive-test-user)** -om du vill ha en motsvarighet till B. Simon i PipeDrive som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för program integrering i **PipeDrive** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp/metadata.xml`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<COMPANY-NAME>.pipedrive.com/sso/auth/samlp`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<COMPANY-NAME>.pipedrive.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [PipeDrive client support team](mailto:support@pipedrive.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. PipeDrive-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Utöver ovan förväntar sig PipeDrive-programmet att fler attribut skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem enligt dina krav.

    | Namn | Källattribut|
    | ------------ | --------- |
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **certifikat (base64)** och väljer **Hämta** för att ladda ned certifikatet och sparar det på datorn och kopierar även **URL: en för appens Federations-URL** och sparar den på din dator.

    ![Länk för nedladdning av certifikatet](./media/pipedrive-tutorial/certificate-data.png)

1. I avsnittet **Konfigurera PipeDrive** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till PipeDrive.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **PipeDrive**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-pipedrive-sso"></a>Konfigurera PipeDrive SSO

1. Logga in på PipeDrive-webbplatsen i ett annat webbläsarfönster som administratör.

1. Klicka på **användar profil** och välj **Inställningar**.

    ![Skärm bild som visar "Inställningar" som valts från menyn "användar profil".](./media/pipedrive-tutorial/configure-1.png)

1. Rulla ned till Security Center och välj **enkel inloggning**.

    ![Skärm bild som visar "enkel inloggning" valt i "Security Center".](./media/pipedrive-tutorial/configure-2.png)

1. Utför följande steg i avsnittet **SAML-konfiguration för PipeDrive** :

    ![Skärm bild som visar avsnittet "S A M L-konfiguration för PipeDrive" med alla text rutor markerade.](./media/pipedrive-tutorial/configure-3.png)

    a. I text rutan **utfärdare** klistrar du in URL-värdet för **app Federation-Metadata** , som du har kopierat från Azure Portal.

    b. I text rutan **URL för enkel inloggning (SSO)** klistrar du in URL-värdet för **inloggning** , som du har kopierat från Azure Portal.

    c. I text rutan **URL för enkel utloggning (service nivå mål)** klistrar du in URL-värdet för **utloggning** , som du har kopierat från Azure Portal.

    d. Öppna den nedladdade **certifikat filen (base64)** i rutan **x. 509-certifikat** från Azure Portal i anteckningar och kopiera innehållet i den och klistra in i **x. 509-certifikat** text ruta och spara ändringarna.

### <a name="create-pipedrive-test-user"></a>Skapa PipeDrive test användare

1. Logga in på PipeDrive-webbplatsen i ett annat webbläsarfönster som administratör.

1. Rulla ned till företag och välj **Hantera användare**.

    ![Skärm bild som visar "hantera användare" som valts från "företaget"-menyn.](./media/pipedrive-tutorial/user-1.png)

1. Klicka på **Lägg till användare**.
    
    ![Skärm bild som visar sidan "hantera användare" med knappen "Lägg till användare" markerad på höger sida.](./media/pipedrive-tutorial/user-2.png)

1. I avsnittet **Hantera användare** utför du följande steg:

    ![PipeDrive-konfiguration](./media/pipedrive-tutorial/user-3.png)

    a. I text rutan **e-post** anger du användarens e-postadress `B.Simon@contoso.com` .

    b. I text rutan **förnamn** anger du det första namnet på användaren.

    c. I text rutan **efter namn** anger du det senaste namnet på användaren.

    d. Klicka på **Bekräfta och Bjud in användare**.

## <a name="test-sso"></a>Testa SSO 


I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till PipeDrive-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till PipeDrive-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den PipeDrive som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på panelen PipeDrive i Mina appar, om det kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det har kon figurer ATS i IDP-läge, bör du loggas in automatiskt på PipeDrive som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat PipeDrive kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).