---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med ljust mönster Omnichannel Contact Center | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och ljust mönster Omnichannel Contact Center.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.openlocfilehash: aba8bf7fd022af1abeecfcb931c22714a5f3169d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542674"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bright-pattern-omnichannel-contact-center"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med ljust mönster Omnichannel Contact Center

I den här självstudien får du lära dig att integrera ljust mönster Omnichannel Contact Center med Azure Active Directory (Azure AD). När du integrerar ljust mönster Omnichannel kontakt Center med Azure AD kan du:

* Kontroll i Azure AD som har till gång till ljust mönster Omnichannel kontakt Center.
* Gör det möjligt för användarna att logga in automatiskt till ljust mönster Omnichannel kontakt Center med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Ljust mönster Omnichannel kontakt Center för enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.



* Ljust mönster Omnichannel Contact Center stöder **SP-och IDP** -INITIERAd SSO
* Ljust mönster Omnichannel Contact Center stöder **just-in-Time** User-etablering


## <a name="adding-bright-pattern-omnichannel-contact-center-from-the-gallery"></a>Lägga till ljust mönster Omnichannel kontakt Center från galleriet

Om du vill konfigurera integreringen av ett ljust mönster Omnichannel kontakt Center i Azure AD måste du lägga till ljust mönster Omnichannel kontakt Center från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **ljust mönster Omnichannel kontakt Center** i rutan Sök.
1. Välj **ljust mönster Omnichannel kontakt Center** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bright-pattern-omnichannel-contact-center"></a>Konfigurera och testa enkel inloggning med Azure AD för ljust mönster Omnichannel kontakt Center

Konfigurera och testa Azure AD SSO med ljust mönster Omnichannel kontakt Center med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i ett ljust mönster Omnichannel kontakt Center.

Om du vill konfigurera och testa Azure AD SSO med ljust mönster Omnichannel Contact Center, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera ljust mönster Omnichannel kontakt Center SSO](#configure-bright-pattern-omnichannel-contact-center-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa ett ljust mönster Omnichannel kontakt Center-Test användare](#create-bright-pattern-omnichannel-contact-center-test-user)** – för att få en motsvarighet till B. Simon i ljust mönster Omnichannel kontakt Center som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **ljus mönster Omnichannel kontakt Center** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `<SUBDOMAIN>_sso`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.brightpattern.com/agentdesktop/sso/redirect`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.brightpattern.com/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [ljust mönster Omnichannel kontakt Center klient support team](mailto:support@brightpattern.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. Ett ljust mönster Omnichannel Contact Center-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Förutom över, förväntar sig ett starkt mönster Omnichannel Contact Center-program att få fler attribut att skickas tillbaka i SAML-svar som visas nedan. Dessa attribut är också förifyllda, men du kan granska dem efter behov.

    | Name | Namnområde  |
    | ---------------| --------------- |
    | firstName | user.givenname |
    | lastName | user.surname |
    | e-post | user.mail |

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera ett ljust mönster Omnichannel Contact Center** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till ljust mönster Omnichannel Contact Center.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **ljus mönster Omnichannel Contact Center**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-bright-pattern-omnichannel-contact-center-sso"></a>Konfigurera ljust mönster Omnichannel kontakt Center SSO

Om du vill konfigurera enkel inloggning på ett **ljust mönster Omnichannel kontakt Center** sida måste du skicka det nedladdade **certifikatet (base64)** och lämpliga kopierade url: er från Azure Portal till [starkt mönster Omnichannel Contact Center support team](mailto:support@brightpattern.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-bright-pattern-omnichannel-contact-center-test-user"></a>Skapa ljus mönster Omnichannel kontakt Center Test användare

I det här avsnittet skapas en användare som heter B. Simon i ett ljust mönster Omnichannel kontakt Center. Ljust mönster Omnichannel Contact Center stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i ljust mönster Omnichannel Contact Center, skapas en ny efter autentisering.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen ljus mönster Omnichannel kontakt Center på åtkomst panelen, bör du loggas in automatiskt på det ljus mönster Omnichannel Contact Center som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova ljust mönster Omnichannel kontakt Center med Azure AD](https://aad.portal.azure.com/)

