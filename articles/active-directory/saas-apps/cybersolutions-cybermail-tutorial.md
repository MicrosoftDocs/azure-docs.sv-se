---
title: 'Självstudie: Azure Active Directory-integrering med enkel inloggning (SSO) med CyberSolutions CYBERMAILΣ | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och CyberSolutions CYBERMAILΣ.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/09/2020
ms.author: jeedes
ms.openlocfilehash: 41c0bdf3eaac1a78d4cdef09ba418155a5a0c919
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92455042"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cybersolutions-cybermail"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med CyberSolutions CYBERMAILΣ

I den här självstudien får du lära dig att integrera CyberSolutions-CYBERMAILΣ med Azure Active Directory (Azure AD). När du integrerar CyberSolutions-CYBERMAILΣ med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till CyberSolutions-CYBERMAILΣ.
* Gör det möjligt för användarna att logga in automatiskt till CyberSolutions CYBERMAILΣ med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* CyberSolutions CYBERMAILΣ-prenumeration med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* CyberSolutions CYBERMAILΣ stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-cybersolutions-cybermail-from-the-gallery"></a>Lägga till CyberSolutions CYBERMAILΣ från galleriet

Om du vill konfigurera integreringen av CyberSolutions-CYBERMAILΣ i Azure AD måste du lägga till CyberSolutions CYBERMAILΣ från galleriet till listan över hanterade SaaS-appar.

1. Logga in på Azure Portal med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program** om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **CyberSolutions CYBERMAILΣ** i sökrutan.
1. Välj **CYBERSOLUTIONS CYBERMAILΣ** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-sso-for-cybersolutions-cybermail"></a>Konfigurera och testa Azure AD SSO för CyberSolutions-CYBERMAILΣ

Konfigurera och testa Azure AD SSO med CyberSolutions CYBERMAILΣ med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i CyberSolutions CYBERMAILΣ.

Om du vill konfigurera och testa Azure AD SSO med CyberSolutions CYBERMAILΣ slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera CYBERSOLUTIONS CYBERMAIL SSO](#configure-cybersolutions-cybermail-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa CYBERSOLUTIONS CYBERMAIL test User](#create-cybersolutions-cybermail-test-user)** – om du vill ha en motsvarighet till B. Simon i CyberSolutions CYBERMAILΣ som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I Azure Portal på sidan **CYBERSOLUTIONS CYBERMAILΣ** Application Integration letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.cybercloud.jp/saml/module.php/saml/sp/metadata.php/m2k_generic_sp`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.cybercloud.jp/cgi-bin/saml_login/saml2-acs/m2k_generic_sp`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.cybercloud.jp`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [CYBERSOLUTIONS CYBERMAILΣ client support team](mailto:tech@cybersolutions.co.jp) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera CYBERSOLUTIONS CYBERMAILΣ** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till CyberSolutions CYBERMAILΣ.

1. I Azure Portal väljer du **företags program** och väljer sedan **alla program**.
1. I listan program väljer du **CYBERSOLUTIONS CYBERMAILΣ**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.
1. Välj **Lägg till användare** och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .
1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig att en roll ska tilldelas användarna kan du välja den från List rutan **Välj en roll** . Om ingen roll har kon figurer ATS för den här appen ser du rollen "standard åtkomst" vald.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-cybersolutions-cybermail-sso"></a>Konfigurera CyberSolutions CYBERMAIL SSO

Om du vill konfigurera enkel inloggning på **CYBERSOLUTIONS CYBERMAILΣ** -sidan måste du skicka den hämtade **XML-metadata för federationsmetadata** och lämpliga kopierade url: er från Azure Portal till [CyberSolutions CYBERMAILΣ support team](mailto:tech@cybersolutions.co.jp). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-cybersolutions-cybermail-test-user"></a>Skapa CyberSolutions CYBERMAIL-test användare

I det här avsnittet skapar du en användare som heter Britta Simon i CyberSolutions CYBERMAILΣ. Arbeta med [CYBERSOLUTIONS CYBERMAILΣ support team](mailto:tech@cybersolutions.co.jp) för att lägga till användarna på CyberSolutions CYBERMAILΣ-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet ska du testa Azure AD-konfigurationen för enkel inloggning med följande alternativ. 

#### <a name="sp-initiated"></a>SP initierad:

* Klicka på **testa det här programmet** i Azure Portal. Detta omdirigeras till CyberSolutions CYBERMAILΣ-inloggnings-URL där du kan starta inloggnings flödet.  

* Gå till CyberSolutions CYBERMAILΣ-inloggnings-URL: en direkt och starta inloggnings flödet därifrån.

#### <a name="idp-initiated"></a>IDP initierad:

* Klicka på **testa det här programmet** i Azure Portal så bör du loggas in automatiskt på den CYBERSOLUTIONS-CYBERMAILΣ som du har konfigurerat SSO för. 

Du kan också använda Microsoft Access-panelen för att testa programmet i vilket läge som helst. När du klickar på panelen CyberSolutions CYBERMAILΣ på åtkomst panelen, om det kon figurer ATS i SP-läge, kommer du att omdirigeras till programmets inloggnings sida för att initiera inloggnings flödet och om det kon figurer ATS i IDP-läge, bör du loggas in automatiskt på CyberSolutions-CYBERMAILΣ som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

När du har konfigurerat CyberSolutions CYBERMAILΣ kan du framtvinga sessionshantering, som skyddar exfiltrering och intrånget för organisationens känsliga data i real tid. Kontroll av sessionen sträcker sig från villkorlig åtkomst. [Lär dig hur du tvingar fram en session med Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-any-app).