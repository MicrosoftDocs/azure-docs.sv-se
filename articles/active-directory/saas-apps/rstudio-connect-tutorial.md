---
title: 'Självstudie: Azure Active Directory integration med RStudio Connect SAML Authentication | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och RStudio Connect SAML-autentisering.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/21/2020
ms.author: jeedes
ms.openlocfilehash: 84e8c7fc1d2655ea0685ac79841a9c467bf766cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182401"
---
# <a name="tutorial-azure-active-directory-integration-with-rstudio-connect-saml-authentication"></a>Självstudie: Azure Active Directory integration med RStudio Connect SAML-autentisering

I den här självstudien får du lära dig att integrera RStudio Connect SAML-autentisering med Azure Active Directory (Azure AD).
Att integrera RStudio Connect SAML-autentisering med Azure AD ger följande fördelar:

* Du kan styra Azure AD som har åtkomst till RStudio Connect SAML-autentisering.
* Du kan göra det möjligt för användarna att logga in automatiskt till RStudio Connect SAML Authentication (enkel inloggning) med deras Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med RStudio Connect SAML-autentisering behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-miljö kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/)
* RStudio Connect SAML-autentisering. Det finns en [kostnads fri utvärderings period på 45 dagar.](https://www.rstudio.com/products/connect/)

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* RStudio Connect SAML-autentisering stöder **SP-och IDP** -INITIERAd SSO

* RStudio Connect SAML-autentisering stöder **just-in-Time** User-etablering

## <a name="adding-rstudio-connect-saml-authentication-from-the-gallery"></a>Lägga till RStudio Connect SAML-autentisering från galleriet

Om du vill konfigurera integreringen av RStudio Connect SAML-autentisering i Azure AD måste du lägga till RStudio Connect SAML-autentisering från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **RStudio Connect SAML Authentication** i sökrutan.
1. Välj **RStudio Anslut SAML-autentisering** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-rstudio-connect-saml-authentication"></a>Konfigurera och testa Azure AD SSO för RStudio Connect SAML-autentisering

Konfigurera och testa Azure AD SSO med RStudio Connect SAML-autentisering med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i RStudio Connect SAML-autentisering.

Utför följande steg för att konfigurera och testa Azure AD SSO med RStudio Connect SAML-autentisering:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    * **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
2. **[Konfigurera RStudio Connect SAML Authentication SSO](#configure-rstudio-connect-saml-authentication-sso)** – om du vill konfigurera enskilda Sign-On inställningar på program sidan.
    * **[Skapa RStudio Connect SAML Authentication test User](#create-rstudio-connect-saml-authentication-test-user)** – om du vill ha en motsvarighet till Britta Simon i RSTUDIO Connect SAML-autentisering som är länkad till Azure AD-representation av användare.
3. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **RStudio Connect SAML Authentication** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg och Ersätt `<example.com>` med din RStudio Connect SAML Authentication Server-adress och port:

    ![RStudio Connect SAML Authentication Domain and URL enkel inloggning information](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<example.com>/__login__/saml`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<example.com>/__login__/saml/acs`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![RStudio Connect SAML Authentication metadata upload](common/metadata-upload-additional-signon.png)

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<example.com>/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. De bestäms från RStudio Connect SAML Authentication Server-adressen ( `https://example.com` i exemplen ovan). Kontakta [RStudio Connect SAML Authentication support team](mailto:support@rstudio.com) om du har problem. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Ditt RStudio Connect SAML Authentication-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. RStudio Connect SAML Authentication Application förväntar sig att **NameIdentifier** mappas med **User. mail**, så du måste redigera mappningen av attribut genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

7. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till RStudio Connect SAML-autentisering.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **RStudio Connect SAML Authentication**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-rstudio-connect-saml-authentication-sso"></a>Konfigurera RStudio Connect SAML Authentication SSO

Om du vill konfigurera enkel inloggning på för **RStudio Connect SAML-autentisering** måste du använda **URL: en för app Federation-Metadata** och den **Server adress** som används ovan. Detta görs i konfigurations filen RStudio Connect SAML Authentication på `/etc/rstudio-connect.rstudio-connect.gcfg` .

Detta är ett exempel på en konfigurations fil:

```
[Server]
SenderEmail =

; Important! The user-facing URL of your RStudio Connect SAML Authentication server.
Address = 

[Http]
Listen = :3939

[Authentication]
Provider = saml

[SAML]
Logging = true

; Important! The URL where your IdP hosts the SAML metadata or the path to a local copy of it placed in the RStudio Connect SAML Authentication server.
IdPMetaData = 

IdPAttributeProfile = azure
SSOInitiated = IdPAndSP
```

Lagra **Server adressen** i `Server.Address` värdet och **URL: en för app Federation-Metadata** i `SAML.IdPMetaData` värdet. Observera att den här exempel konfigurationen använder en okrypterad HTTP-anslutning, medan Azure AD kräver att en krypterad HTTPS-anslutning används. Du kan antingen använda en [omvänd proxy](https://docs.rstudio.com/connect/admin/proxy/) framför RSTUDIO Connect SAML-autentisering eller konfigurera RSTUDIO Connect SAML-autentisering för att [använda https direkt](https://docs.rstudio.com/connect/admin/appendix/configuration/#HTTPS). 

Om du har problem med konfigurationen kan du läsa [Administratörs guiden för RStudio Connect SAML-autentisering](https://docs.rstudio.com/connect/admin/authentication/saml/) eller e-posta [RStudio support-teamet](mailto:support@rstudio.com) för hjälp.

### <a name="create-rstudio-connect-saml-authentication-test-user"></a>Skapa RStudio Connect SAML Authentication test User

I det här avsnittet skapas en användare som heter Britta Simon i RStudio Connect SAML-autentisering. RStudio Connect SAML-autentisering stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i RStudio Connect SAML-autentisering skapas en ny när du försöker komma åt RStudio Connect SAML-autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till RStudio Connect SAML Authentication Sign on URL där du kan starta inloggnings flödet.  

* Gå till RStudio Connect SAML Authentication inloggnings-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den RSTUDIO Connect SAML-autentisering som du konfigurerade SSO för. 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen RStudio Connect SAML Authentication i åtkomst panelen, om den har kon figurer ATS i SP-läge, omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till RStudio Connect SAML-autentiseringen som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat RStudio Connect SAML-autentisering kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).