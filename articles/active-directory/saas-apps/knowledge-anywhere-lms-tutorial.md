---
title: 'Självstudie: Azure Active Directory integrering med kunskap överallt Microsoft Docs'
description: Lär dig att konfigurera enkel inloggning mellan Azure Active Directory och Knowledge Anywhere LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.openlocfilehash: 507298f323fe14e3b114afebed5f2f258145a76e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549951"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Självstudie: integrera kunskaps-LMS överallt med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar kunskap överallt med Azure Active Directory (Azure AD). När du integrerar kunskap överallt med Azure AD kan du:

* Kontroll i Azure AD som har till gång till kunskap överallt i LMS.
* Gör det möjligt för användarna att logga in automatiskt för att få kunskap överallt med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Var medveten om att prenumerationen är aktive rad för inloggning med enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Kunskap överallt som har stöd för **SP** -initierad SSO och stöder **just-in-Time** User-etablering.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Lägga till Knowledge Anywhere LMS från galleriet

När du konfigurerar integreringen av Knowledge Anywhere LMS i Azure Active Directory, måste du lägga till Knowledge Anywhere LMS från galleriet i din lista med hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **kunskap överallt** i sökrutan.
1. Välj **kunskap överallt** i panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med kunskap överallt på LMS med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i kunskap var LMS.

Om du vill konfigurera och testa Azure AD SSO med kunskap överallt i LMS slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. Konfigurera **[kunskap överallt i LMS](#configure-knowledge-anywhere-lms)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** för att testa enkel inloggning i Azure AD med B. Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** att aktivera B. Simon för att använda enkel inloggning i Azure AD.
5. **[Skapa kunskap var LMS-testanvändare](#create-knowledge-anywhere-lms-test-user)** att ha en motsvarighet till B. Simon i kunskap överallt som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan **kunskap var LMS** -programintegration hittar du avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    1. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska identifieraren och svars-URL:en. Detta beskrivs senare i självstudien.

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Inloggnings-URL-värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [Knowledge Anywhere LMS-klientens supportteam](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) för att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera kunskap Anywhere överallt** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Konfigurera kunskap överallt i LMS

1. Om du vill automatisera konfigurationen i kunskap överallt måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations information var LMS** för att dirigera dig till kunskap överallt. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på kunskap överallt. Webbläsartillägget konfigurerar automatiskt programmet åt dig och automatiserar steg 3–7.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera kunskap överallt överallt manuellt, öppnar du ett nytt webbläsarfönster och loggar in på din kunskap överallt som administratör och utför följande steg:

4. Välj fliken **Webbplats**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Välj på fliken **SAML-inställningar**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Klicka på **Lägg till ny**.

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. På sidan **Lägg till/uppdatera SAML-inställningar** utför du följande steg:

    ![Knowledge Anywhere LMS-konfiguration](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Ange IDP-namnet för din organisation. För exempelvis: `Azure`.

    b. I text rutan **entitets-ID för IDP** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    c. I text rutan **IDP URL** klistrar du in **inloggnings-URL** -värdet, som du har kopierat från Azure Portal.

    d. Öppna den nedladdade certifikatfilen från Azure-portalen i Anteckningar, kopiera innehållet för certifikatet och klistra in det i textrutan **Certifikat**.

    e. I text rutan **Logga ut URL** , klistra in URL-värde för **utloggning** , som du har kopierat från Azure Portal.

    f. Välj **Huvudwebbplats** i listrutan för **Domän**.

    ex. Kopiera värdet i **Entitets-ID för SP** och klistra in det i textrutan **Identifierare** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    h. Kopiera värdet för **SP-svar (ACS) URL** och klistra in det i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** på Azure-portalen.

    i. Klicka på **Spara**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B. Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till kunskap överallt.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan med program väljer du **Knowledge Anywhere LMS**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-knowledge-anywhere-lms-test-user"></a>Skapa testanvändare för Knowledge Anywhere LMS

I det här avsnittet skapas en användare som heter B. Simon i kunskap överallt. Knowledge Anywhere LMS stöder just-in-time-etablering av användare, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Knowledge Anywhere LMS, skapas en ny efter autentiseringen.

### <a name="test-sso"></a>Testa SSO

När du väljer fliken kunskap överallt i åtkomst panelen, bör du loggas in automatiskt på det Microsoft-kunskaps-LMS som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)