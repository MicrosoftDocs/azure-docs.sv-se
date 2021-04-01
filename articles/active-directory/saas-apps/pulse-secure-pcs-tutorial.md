---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med puls säkra datorer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och puls säkra datorer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: d38ff5c8f33bda5b12f6267e7a8cdf477db6c7d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92511451"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med puls säkra datorer

I den här självstudien får du lära dig hur du integrerar puls säkra datorer med Azure Active Directory (Azure AD). När du integrerar puls säkra datorer med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till puls säkra datorer.
* Gör det möjligt för dina användare att vara automatiskt inloggade för att fästa säkra datorer med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Aktivera prenumeration med enkel inloggning (SSO) för säkra datorer.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Puls säkra datorer stöder **SP** -INITIERAd SSO

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>Lägga till puls säkra datorer från galleriet

Om du vill konfigurera integreringen av puls säkra datorer i Azure AD måste du lägga till puls säkra datorer från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **PULSe Secure PCs** i sökrutan.
1. Välj **Pulse Secure PCs** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Konfigurera och testa Azure AD SSO för puls säkra datorer

Konfigurera och testa Azure AD SSO med puls säkra datorer med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på puls säkra datorer.

Utför följande steg för att konfigurera och testa Azure AD SSO med puls säkra datorer:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera puls säkra datorer SSO](#configure-pulse-secure-pcs-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa puls säkra datorer testa användare](#create-pulse-secure-pcs-test-user)** -om du vill ha en motsvarighet till B. Simon i puls säkra datorer som är länkade till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan **Pulse Secure PCs** application integration i Azure Portal letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi`


    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en, svars-URL och identifierare. Kontakta [support teamet för säkra datorer](mailto:support@pulsesecure.net) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. På sidan **Konfigurera puls säkra datorer** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till puls säkra datorer.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **puls säkra datorer**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-pulse-secure-pcs-sso"></a>Konfigurera puls säkra datorer SSO

I det här avsnittet beskrivs de SAML-konfigurationer som krävs för att konfigurera datorer som SAML SP. De andra grundläggande konfigurationerna som att skapa sfärer och roller omfattas inte.

**Pulse Connect-säkra konfigurationer omfattar:**

* Konfigurera Azure AD som SAML Metadata Provider
* Konfigurera SAML auth Server
* Tilldela till respektive sfärer och roller

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Konfigurera Azure AD som SAML Metadata Provider

Utför följande steg på följande sida:

![Puls anslutning för säker konfiguration](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Logga in på den säkra administratörs konsolen för puls anslutning
1. Navigera till **system-> konfiguration – > SAML**
1. Klicka på **ny Metadata-Provider**
1. Ange ett giltigt namn i text rutan **namn**
1. Överför den hämtade metadata-XML-filen från Azure Portal till **Azure AD-metadatafilen**.
1. Välj **acceptera osignerade metadata**
1. Välj roller som **identitets leverantör**
1. Klicka på **Spara ändringar**.

#### <a name="steps-to-create-a-saml-auth-server"></a>Steg för att skapa en SAML-auth-Server:

1. Navigera till **autentisering-> auth-servrar**
1. Välj **nytt: SAML-Server** och klicka på **ny server**

    ![Pulse Connect Secure auth Server](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. Utför följande steg på sidan inställningar:

    ![Pulse Connect Secure auth Server-inställningar](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. Ange **ett server namn** i text rutan.

    b. Välj **SAML Version 2,0** och **konfigurations läge** som **metadata**.

    c. Kopiera det **säkra entitets-ID: t** och klistra in det i rutan **ID URL** i dialog rutan **grundläggande SAML-konfiguration** i Azure Portal.

    d. Välj ID-värde för Azure AD-entiteten från List rutan ID för ID **-providerns entitets-ID**.

    e. Välj URL-värde för Azure AD-inloggning från **list rutan identitets leverantörens enkla Sign-On tjänst-URL**.

    f. **Enkel utloggning** är en valfri inställning. Om det här alternativet väljs, uppmanas du att ange en ny autentisering efter utloggning. Om det här alternativet inte är markerat och du inte har stängt webbläsaren, kan du återansluta utan autentisering.

    ex. Välj den **begärda kontext klassen för autentisering** som **lösen ord** och **jämförelse metod** som **exakt**.

    h. Ange **giltighet för metadata** i antal dagar.
    
    i. Klicka på **Spara ändringar**

### <a name="create-pulse-secure-pcs-test-user"></a>Skapa puls säkra datorer test användare

I det här avsnittet skapar du en användare med namnet Britta Simon i puls säkra datorer. Arbeta med [support teamet för Pulse](mailto:support@pulsesecure.net) Worker-datorer för att lägga till användarna i datorn Pulse Secure PCs. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

1. Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till puls säkra datorer inloggnings-URL där du kan starta inloggnings flödet. 

2. Gå till puls säkra datorer inloggnings-URL direkt och starta inloggnings flödet därifrån.

3. Du kan använda Microsoft Access-panelen. När du klickar på panelen för säkra säkra datorer på åtkomst panelen omdirigeras det till Pulse Secure PCs inloggnings-URL. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Pulse Secure-datorer kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).