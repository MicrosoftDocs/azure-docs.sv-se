---
title: 'Självstudie: Azure Active Directory integrering med MVISION Cloud Azure AD SSO-konfiguration | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory-och MVISION-molnet i Azure AD SSO-konfigurationen.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: jeedes
ms.openlocfilehash: eaf6b2526125b13eec67680c292ed1dbae6fcfee
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284438"
---
# <a name="tutorial-integrate-mvision-cloud-azure-ad-sso-configuration-with-azure-active-directory"></a>Självstudie: integrera MVISION Cloud Azure AD SSO-konfiguration med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar MVISION Cloud Azure AD SSO-konfiguration med Azure Active Directory (Azure AD). När du integrerar MVISION Cloud Azure AD SSO-konfiguration med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till MVISION Cloud Azure AD SSO-konfiguration.
* Gör det möjligt för användarna att logga in automatiskt till MVISION Cloud Azure AD SSO-konfiguration med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* MVISION Cloud Azure AD SSO-konfiguration enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* MVISION Cloud Azure AD SSO-konfiguration stöder **SP-och IDP** -initierad SSO.

## <a name="add-mvision-cloud-azure-ad-sso-configuration-from-the-gallery"></a>Lägg till MVISION Cloud Azure AD SSO-konfiguration från galleriet

Om du vill konfigurera integrering av MVISION Cloud Azure AD SSO-konfigurationen i Azure AD måste du lägga till MVISION Cloud Azure AD SSO-konfiguration från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **MVision Cloud Azure AD SSO Configuration** i sökrutan.
1. Välj **MVision Cloud Azure AD SSO-konfiguration** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-mvision-cloud-azure-ad-sso-configuration"></a>Konfigurera och testa Azure AD SSO för MVISION Cloud AD SSO-konfiguration

Konfigurera och testa Azure AD SSO med MVISION Cloud Azure AD SSO-konfiguration med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i MVISION Cloud AD SSO-konfiguration.

Utför följande steg för att konfigurera och testa Azure AD SSO med MVISION Cloud AD SSO-konfiguration:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
    4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
1. **[Konfigurera MVision Cloud Azure AD SSO-konfiguration SSO](#configure-mvision-cloud-azure-ad-sso-configuration-sso)** – om du vill konfigurera enskilda Sign-On inställningar på program sidan.
    1. **[Skapa MVision Cloud Azure AD SSO-konfiguration testa användare](#create-mvision-cloud-azure-ad-sso-configuration-test-user)** – för att få en motsvarighet till Britta Simon i MVision Cloud Azure AD SSO-konfiguration som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Datadog** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<ENV>.myshn.net/shndash/response/saml-postlogin`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<ENV>.myshn.net/shndash/saml/Azure_SSO`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [MVision Cloud Azure AD SSO Configuration Client support team](mailto:support@skyhighnetworks.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

7. I avsnittet **Konfigurera MVision Cloud AD SSO-konfiguration** kopierar du lämpliga URL: er enligt ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till MVISION Cloud Azure AD SSO-konfiguration.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **MVision Cloud Azure AD SSO-konfiguration**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-mvision-cloud-azure-ad-sso-configuration-sso"></a>Konfigurera MVISION Cloud Azure AD SSO SSO

Om du vill konfigurera enkel inloggning på **MVision Cloud Azure AD SSO-konfigurations** sidan måste du skicka det hämtade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [MVision Cloud Azure AD SSO Configuration support team](mailto:support@skyhighnetworks.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-mvision-cloud-azure-ad-sso-configuration-test-user"></a>Skapa MVISION Cloud Azure AD SSO-konfiguration test användare

I det här avsnittet skapar du en användare som heter B. Simon i MVISION Cloud Azure AD SSO-konfiguration. Arbeta med [MVision Cloud Azure AD SSO Configuration support team](mailto:support@skyhighnetworks.com) för att lägga till användarna i MVision-molnet konfigurations plattform för enkel inloggning i Azure AD. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till MVISION Cloud Azure AD SSO Configuration inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till MVISION Cloud Azure AD SSO-inloggning URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt till MVision Cloud Azure AD SSO-konfigurationen som du ställer in SSO för. 

Du kan också använda Microsoft Mina appar för att testa programmet i vilket läge som helst. När du klickar på konfigurations panelen för molnet för Azure AD i MVISION i Mina appar, om den har kon figurer ATS i SP-läge omdirigeras du till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du logga in automatiskt till MVISION Cloud Azure AD SSO-konfigurationen som du ställer in SSO för. Mer information om Mina appar finns i [Introduktion till Mina appar](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat MVISION Cloud Azure AD SSO-konfiguration kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).
