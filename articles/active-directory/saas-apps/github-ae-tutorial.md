---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med GitHub AE | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och GitHub AE.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/18/2021
ms.author: jeedes
ms.openlocfilehash: 537378ef11333bd8942a61ab2bbb4e8a251c2b59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103196816"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github-ae"></a>Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med GitHub AE

I den här självstudien får du lära dig att integrera GitHub AE med Azure Active Directory (Azure AD). När du integrerar GitHub AE med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till GitHub AE.
* Gör det möjligt för användarna att logga in automatiskt till GitHub AE med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* GitHub AE, redo för [initiering](https://docs.github.com/github-ae@latest/admin/configuration/initializing-github-ae).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* GitHub AE stöder **SP** -och **IDP** -initierad SSO
* GitHub AE stöder **just-in-Time** User-etablering

## <a name="adding-github-ae-from-the-gallery"></a>Lägga till GitHub AE från galleriet

Om du vill konfigurera integrationen av GitHub AE i Azure AD måste du lägga till GitHub AE från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **GitHub AE** i sökrutan.
1. Välj **GITHUB AE** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-github-ae"></a>Konfigurera och testa Azure AD SSO för GitHub AE

Konfigurera och testa Azure AD SSO med GitHub AE med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i GitHub AE.

Om du vill konfigurera och testa Azure AD SSO med GitHub AE slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera GITHUB AE SSO](#configure-github-ae-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa GITHUB AE test User](#create-github-ae-test-user)** -om du vill ha en motsvarighet till B. Simon i GitHub AE som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal går du till sidan **GITHUB AE** application integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<YOUR-GITHUB-AE-HOSTNAME>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<YOUR-GITHUB-AE-HOSTNAME>/saml/consume`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

     I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<YOUR-GITHUB-AE-HOSTNAME>/sso`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en, svars-URL och identifierare. Kontakta [GITHUB AE client support team](mailto:support@github.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.


1. GitHub AE-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/default-attributes.png)

1. Redigera **användarattribut & anspråk**.

1. Klicka på **Lägg till nytt anspråk** och ange namnet som **administratör** i text rutan.

1. Expandera **anspråks villkor** och välj **medlemmar** från **användar typ**.

1. Klicka på **Välj grupper** och Sök efter den **grupp** som du vill inkludera det här anspråket, där dess medlemmar ska vara administratörer för GHAE.

1. Välj **attribut** för **källa** och ange **Sant** (utan citat tecken) för **värdet**. 

1. Klicka på **Spara**.

    ![hantera anspråk](./media/github-ae-tutorial/administrator.png)

    > [!NOTE]
    > Följ [länken](https://docs.github.com/en/github-ae@latest/admin/authentication/configuring-authentication-and-provisioning-for-your-enterprise-using-azure-ad)om du vill veta mer om hur du lägger till ett anspråk.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera GITHUB AE** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till GitHub AE.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **GITHUB AE**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-github-ae-sso"></a>Konfigurera GitHub AE SSO

Om du vill konfigurera SSO på GitHub AE-sidan måste du följa instruktionerna [här](https://docs.github.com/github-ae@latest/admin/authentication/configuring-saml-single-sign-on-for-your-enterprise#enabling-saml-sso).

### <a name="create-github-ae-test-user"></a>Skapa GitHub AE-test användare

I det här avsnittet skapas en användare som heter B. Simon i GitHub AE. GitHub AE stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i GitHub AE skapas en ny efter autentiseringen.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till GitHub AE-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till GitHub AE-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den GitHub AE som du konfigurerade SSO för. 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på GitHub AE-panelen på åtkomst panelen, om den har kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt på GitHub AE som du konfigurerar SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

* [Konfigurera användar etablering för ditt företag](https://docs.github.com/github-ae@latest/admin/authentication/configuring-user-provisioning-for-your-enterprise).

* När du har konfigurerat GitHub AE kan du framtvinga kontroll av sessioner, vilket skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).
