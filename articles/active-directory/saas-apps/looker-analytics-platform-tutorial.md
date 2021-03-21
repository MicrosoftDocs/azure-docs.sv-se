---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med looker Analytics-plattform | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och looker Analytics-plattformen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/10/2020
ms.author: jeedes
ms.openlocfilehash: dbb6f6d278256730e77677e78f452615fe4b611e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96180752"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-looker-analytics-platform"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med looker Analytics-plattform

I den här självstudien får du lära dig att integrera looker Analytics-plattform med Azure Active Directory (Azure AD). När du integrerar looker Analytics-plattformen med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till looker Analytics-plattformen.
* Gör det möjligt för användarna att logga in automatiskt till looker Analytics-plattform med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Looker för enkel inloggning i Analytics Platform (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Looker Analytics Platform stöder **SP-och IDP** -INITIERAd SSO
* Looker Analytics Platform stöder **just-in-Time** User-etablering


## <a name="adding-looker-analytics-platform-from-the-gallery"></a>Lägga till looker Analytics-plattform från galleriet

Om du vill konfigurera integrationen av looker Analytics-plattformen i Azure AD måste du lägga till looker Analytics-plattform från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **looker Analytics Platform** i sökrutan.
1. Välj **looker Analytics Platform** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-looker-analytics-platform"></a>Konfigurera och testa Azure AD SSO för looker Analytics-plattform

Konfigurera och testa Azure AD SSO med looker Analytics-plattform med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i looker Analytics-plattformen.

Utför följande steg för att konfigurera och testa Azure AD SSO med looker Analytics-plattform:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera looker Analytics Platform SSO](#configure-looker-analytics-platform-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa looker Analytics Platform test User](#create-looker-analytics-platform-test-user)** – om du vill ha en motsvarighet till B. Simon i looker Analytics-plattformen som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **looker Analytics Platform** application integration i Azure Portal letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `<SPN>_looker`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.looker.com/samlcallback`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.looker.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [looker Analytics Platform client support team](mailto:support@looker.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera looker analys plattform** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till looker Analytics-plattformen.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **looker Analytics Platform**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-looker-analytics-platform-sso"></a>Konfigurera looker Analytics Platform SSO

1. Logga in på webbplatsen för looker Analytics-plattformen som administratör i ett annat webbläsarfönster.

1. Gå till **admin**-  ->  **autentisering**  ->  **SAML**

    ![skärm bild för SAML-alternativ](./media/looker-analytics-platform-tutorial/admin.png)

1. Klistra in information om **federationsmetadata** som du kopierade från Azure Portal i text rutan **IDP metadata** och klicka på **load**.

    ![skärm bild för uppladdning av metadata](./media/looker-analytics-platform-tutorial/metadata.png)

1. Utför följande steg i avsnittet **Inställningar för användar attribut** .

    ![skärm bild för inställningar för användarattribut](./media/looker-analytics-platform-tutorial/user-attribute-settings.png)

    a. Lägg till följande värde i fältet email attr: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    b. Lägg till följande värde i fältet fname attr: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    c. Lägg till följande värde i fältet lname attr: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    d. I **testa användarautentisering** klickar du på **testa SAML-autentisering**. Om sidan som läses in säger "Server svaret har verifierats" har du konfigurerat instansen för SAML-integrering.
    
    e. I **Spara och Använd inställningar** markerar du kryss rutan **Jag har bekräftat konfigurationen ovan och vill aktivera tillämpa den globalt**.

    f. Klicka på knappen **Uppdatera inställningar** .

### <a name="create-looker-analytics-platform-test-user"></a>Skapa en test användare av looker Analytics-plattformen

I det här avsnittet skapas en användare som kallas Britta Simon i looker Analytics-plattformen. Looker Analytics Platform stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i looker Analytics Platform skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigerar till looker Analytics Platform inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till looker Analytics Platform Sign on URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den looker Analytics-plattform som du konfigurerade SSO för. 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen looker Analytics-plattform på åtkomst panelen, om den har kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på den looker Analytics-plattform som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat looker Analytics-plattformen kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).