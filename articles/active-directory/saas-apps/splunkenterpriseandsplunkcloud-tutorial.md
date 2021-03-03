---
title: 'Självstudie: Azure Active Directory integration med Splunk Enterprise och Splunk Cloud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Splunk Enterprise och Splunk Cloud.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 81107b3655ae86d51e36445ce46661d553ab3b5a
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101649873"
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Självstudie: Azure Active Directory integrering med Splunk Enterprise och Splunk Cloud

I den här självstudien får du lära dig att integrera Splunk Enterprise och Splunk Cloud med Azure Active Directory (Azure AD). När du integrerar Splunk Enterprise och Splunk Cloud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Splunk Enterprise och Splunk Cloud.
* Gör det möjligt för användarna att logga in automatiskt till Splunk Enterprise och Splunk Cloud med sina Azure AD-konton.
* Hantera dina konton på en central plats: Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Splunk Enterprise och Splunk Cloud Single Sign-on (SSO)-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Splunk Enterprise och Splunk Cloud stöder **SP**-initierad SSO

## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Lägg till Splunk Enterprise och Splunk Cloud från galleriet

Om du vill konfigurera integreringen av Splunk Enterprise och Splunk Cloud till Azure AD behöver du lägga till Splunk Enterprise och Splunk Cloud från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Splunk Enterprise och Splunk Cloud** i sökrutan.
1. Välj **Splunk Enterprise och Splunk Cloud** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-sso-for-splunk-enterprise-and-splunk-cloud"></a>Konfigurera och testa Azure AD SSO för Splunk Enterprise och Splunk Cloud

Konfigurera och testa Azure AD SSO med Splunk Enterprise och Splunk Cloud med en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Splunk Enterprise och Splunk Cloud.

Utför följande steg för att konfigurera och testa Azure AD SSO med Splunk Enterprise och Splunk Cloud:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Splunk Enterprise och Splunk Cloud SSO](#configure-splunk-enterprise-and-splunk-cloud-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Splunk Enterprise och Splunk Cloud test User](#create-splunk-enterprise-and-splunk-cloud-test-user)** – om du vill ha en motsvarighet till B. Simon i Splunk Enterprise och Splunk Cloud som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **Splunk Enterprise och Splunk Cloud** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på Penn ikonen för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)
4. I avsnittet **grundläggande SAML-konfiguration** , utför följande mönster:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<splunkserverUrl>/app/launcher/home`

    b. I rutan **identifierare** anger du en URL med följande mönster: `<splunkserverUrl>`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<splunkserver>/saml/acs`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [supportteamet för Splunk Enterprise och Splunk Cloud ](https://www.splunk.com/en_us/about-splunk/contact-us.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Splunk Enterprise och Splunk Cloud.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **Splunk Enterprise och Splunk Cloud**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-splunk-enterprise-and-splunk-cloud-sso"></a>Konfigurera Splunk Enterprise och Splunk Cloud SSO

  Om du vill konfigurera enkel inloggning på **Splunk Enterprise och Splunk Cloud**-sidan måste du skicka den hämtade **Federation Metadata-XML:en** och lämpliga kopierade URL:er från Azure-portalen till [Splunk Enterprise- och Splunk Cloud-supportteamet](https://www.splunk.com/en_us/about-splunk/contact-us.html). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-splunk-enterprise-and-splunk-cloud-test-user"></a>Skapa testanvändare för Splunk Enterprise och Splunk Cloud

I det här avsnittet skapar du en användare som heter Britta Simon i Splunk Enterprise och Splunk Cloud. Arbeta med [Splunk Enterprise och Splunk Cloud Support Team](https://www.splunk.com/en_us/about-splunk/contact-us.html) för att lägga till användare i Splunk Enterprise och Splunk Cloud Platform. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till Splunk Enterprise-och Splunk Cloud Sign-URL där du kan starta inloggnings flödet. 

* Gå till Splunk Enterprise och Splunk Cloud Sign-URL direkt och starta inloggnings flödet därifrån.

* Du kan använda Microsoft Mina appar. När du klickar på moln panelen Splunk Enterprise och Splunk i Mina appar omdirigeras det till Splunk Enterprise och splunks URL för moln inloggning. Mer information om Mina appar finns i [Introduktion till Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat Splunk företags-och Splunk-moln kan du genomdriva session Control, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram sessions kontroll med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app)