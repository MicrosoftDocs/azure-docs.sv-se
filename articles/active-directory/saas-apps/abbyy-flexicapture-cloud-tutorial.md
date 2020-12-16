---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ABBYY FlexiCapture Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ABBYY FlexiCapture-molnet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: jeedes
ms.openlocfilehash: 359511e653231ee25f0cd09f32448a0b66b40d3d
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97586745"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-abbyy-flexicapture-cloud"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med ABBYY FlexiCapture Cloud

I den här självstudien får du lära dig att integrera ABBYY FlexiCapture Cloud med Azure Active Directory (Azure AD). När du integrerar ABBYY FlexiCapture Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till ABBYY FlexiCapture-molnet.
* Gör det möjligt för användarna att logga in automatiskt till ABBYY FlexiCapture-molnet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* ABBYY FlexiCapture-funktionen för enkel inloggning (SSO) i molnet.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* ABBYY FlexiCapture-moln stöder **SP-och IDP** -INITIERAd SSO
* ABBYY FlexiCapture Cloud stöder **just-in-Time** User-etablering

## <a name="adding-abbyy-flexicapture-cloud-from-the-gallery"></a>Lägga till ABBYY FlexiCapture Cloud från galleriet

Om du vill konfigurera integreringen av ABBYY FlexiCapture Cloud i Azure AD måste du lägga till ABBYY FlexiCapture Cloud från galleriet i listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **ABBYY FlexiCapture Cloud** i sökrutan.
1. Välj **ABBYY FlexiCapture Cloud** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-abbyy-flexicapture-cloud"></a>Konfigurera och testa Azure AD SSO för ABBYY FlexiCapture Cloud

Konfigurera och testa Azure AD SSO med ABBYY FlexiCapture-molnet med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ABBYY FlexiCapture-molnet.

Utför följande steg för att konfigurera och testa Azure AD SSO med ABBYY FlexiCapture Cloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ABBYY FlexiCapture Cloud SSO](#configure-abbyy-flexicapture-cloud-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ABBYY FlexiCapture Cloud test User](#create-abbyy-flexicapture-cloud-test-user)** – för att få en motsvarighet till B. Simon i ABBYY FlexiCapture-molnet som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan för Cloud Application Integration i **ABBYY FlexiCapture** , letar reda på avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>/AccessToken/Saml`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>/AccessToken/Saml`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.flexicapture.com/FlexiCapture12/Login/<TENANT_NAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [ABBYY FlexiCapture Cloud client support team](mailto:support@abbyy.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera ABBYY FlexiCapture Cloud** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare** och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Ett exempel är `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ABBYY FlexiCapture Cloud.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **ABBYY FlexiCapture Cloud**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-abbyy-flexicapture-cloud-sso"></a>Konfigurera ABBYY FlexiCapture Cloud SSO

Om du vill konfigurera enkel inloggning på **ABBYY FlexiCapture Cloud** -sidan måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [ABBYY FlexiCapture Cloud Support Team](mailto:support@abbyy.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-abbyy-flexicapture-cloud-test-user"></a>Skapa ABBYY FlexiCapture Cloud test User

I det här avsnittet skapas en användare som kallas Britta Simon i ABBYY FlexiCapture-molnet. ABBYY FlexiCapture-molnet stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i ABBYY FlexiCapture-molnet skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till ABBYY FlexiCapture Cloud Sign on URL där du kan starta inloggnings flödet.  

* Gå till ABBYY FlexiCapture Cloud Sign-URL direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal och du bör logga in automatiskt till ABBYY FlexiCapture-molnet som du ställer in SSO för. 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen för ABBYY FlexiCapture-molnet i åtkomst panelen, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till ABBYY FlexiCapture-molnet som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat ABBYY FlexiCapture-molnet kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).