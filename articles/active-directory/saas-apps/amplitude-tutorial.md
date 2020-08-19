---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med amplitud | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Amplitude.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.openlocfilehash: 6f2b7ec07583859e12711237c6a899d61335c969
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545483"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-amplitude"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med amplitud

I den här självstudien får du lära dig att integrera amplitud med Azure Active Directory (Azure AD). När du integrerar amplitud med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till amplitud.
* Gör det möjligt för användarna att logga in automatiskt till amplitud med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Amplitud för enkel inloggning (SSO) som är aktive rad för en prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Amplitude har stöd för **SP- och IDP**-initierad enkel inloggning
* Amplitude stöder **just-in-time**-användaretablering

## <a name="adding-amplitude-from-the-gallery"></a>Lägga till Amplitude från galleriet

För att konfigurera integrering av Amplitude i Azure AD, behöver du lägga till Amplitude från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **amplitud** i sökrutan.
1. Välj **amplitud** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-amplitude"></a>Konfigurera och testa enkel inloggning med Azure AD för amplitud

Konfigurera och testa Azure AD SSO med amplitud med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och en relaterad användare i amplitud.

Om du vill konfigurera och testa Azure AD SSO med amplitud slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera AMPLITUD SSO](#configure-amplitude-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa amplitud test User](#create-amplitude-test-user)** – om du vill ha en motsvarighet till B. Simon i amplitud som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för **amplitud** -program integration i [Azure Portal](https://portal.azure.com/)letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. Skriv en URL i text rutan **identifierare** : `https://amplitude.com/saml/sso/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://analytics.amplitude.com/saml/sso/<uniqueid>`

    > [!NOTE]
    > Värdet för svars-URL:en är inte verkligt. Senare i den här självstudien får du svars-URL-värdet.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    Skriv en URL i text rutan **inloggnings-URL** :  `https://analytics.amplitude.com/sso`

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera amplitud** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till amplitud.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan med program väljer du **Amplitude**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-amplitude-sso"></a>Konfigurera amplitud SSO

1. Om du vill automatisera konfigurationen i amplitud måste du installera **webb läsar tillägget Mina appar säkra inloggnings webbläsare** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren dirigeras du till amplitud-programmet genom att klicka på **Konfigurera amplitud** . Därifrån anger du administratörsautentiseringsuppgifter för att logga in på amplitud. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera amplitud manuellt öppnar du ett nytt webbläsarfönster och loggar in på din amplitud företags webbplats som administratör och utför följande steg:

1. Klicka på **Plan Admin** (avtalsadministratör) från det vänstra navigeringsfältet.

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure1.png)

1. Välj **Microsoft Azure Active Directory-metadata** från **SSO-integreringen**.

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure2.png)

1. I avsnittet **Konfigurera enkel inloggning** utför du följande steg:

    ![Konfigurera enkel inloggning](./media/amplitude-tutorial/configure3.png)

    a. Öppna hämtade **Xml-metadata** från Azure-portalen i anteckningar och klistra in innehållet i textrutan för **Microsoft Azure Active Directory-metadata**.

    b. Kopiera värdet i **svars-URL:en (ACS)** och klistra in det i textrutan för **svars-URL** under **Grundläggande SAML-konfiguration** på Azure-portalen.

    c. Klicka på **Spara**

### <a name="create-amplitude-test-user"></a>Skapa Amplitude-testanvändare

I det här avsnittet skapas en användare som heter B. Simon i amplitud. Amplitude stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Amplitude skapas en ny efter autentisering.

> [!Note]
> Kontakta [amplitud support teamet](https://amplitude.zendesk.com)om du behöver skapa en användare manuellt.

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Amplitude-panelen i åtkomstpanelen så borde du automatiskt loggas in på den Amplitude som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova amplitud med Azure AD](https://aad.portal.azure.com/)