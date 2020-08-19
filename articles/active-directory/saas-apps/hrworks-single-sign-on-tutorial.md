---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med enkel inloggning för HRworks | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HRworks enkel inloggning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2020
ms.author: jeedes
ms.openlocfilehash: 0c43dfe3ad28417e13196c5ac1e75419906f6eae
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549169"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-hrworks-single-sign-on"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med enkel inloggning för HRworks

I den här självstudien får du lära dig hur du integrerar HRworks enkel inloggning med Azure Active Directory (Azure AD). När du integrerar HRworks enkel inloggning med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till HRworks-enkel inloggning.
* Gör det möjligt för användarna att logga in automatiskt till HRworks enkel inloggning med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* HRworks enkel inloggning (SSO) med enkel inloggning aktive rad (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* HRworks enkel inloggning stöder **SP** -INITIERAd SSO

## <a name="adding-hrworks-single-sign-on-from-the-gallery"></a>Lägga till HRworks enkel inloggning från galleriet

Om du vill konfigurera integrering av HRworks enkel inloggning i Azure AD måste du lägga till HRworks enkel inloggning från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **HRworks enkel inloggning** i sökrutan.
1. Välj **HRworks enkel inloggning** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-hrworks-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD för HRworks enkel inloggning

Konfigurera och testa Azure AD SSO med enkel inloggning med HRworks med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i HRworks enkel inloggning.

Om du vill konfigurera och testa Azure AD SSO med HRworks enkel inloggning, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    * **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    * **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera HRworks enkel inloggning](#configure-hrworks-single-sign-on-sso)** för enkel inloggning – för att konfigurera inställningarna för enkel inloggning på program sidan.
    * **[Skapa HRworks-test för enkel inloggning](#create-hrworks-single-sign-on-test-user)** för att få en motsvarighet till B. Simon i HRworks enkel inloggning som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)på sidan för **enkel inloggning på HRworks** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://login.hrworks.de/?companyId=<companyId>&directssologin=true`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [HRworks support team för enkel inloggning](mailto:nadja.sommerfeld@hrworks.de) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera HRworks enkel inloggning** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till HRworks enkel inloggning.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **HRworks enkel inloggning**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-hrworks-single-sign-on-sso"></a>Konfigurera HRworks enkel inloggning

1. Om du vill automatisera konfigurationen i HRworks enkel inloggning måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren, klickar du på **Konfigurera HRworks Single Sign-on** för att dirigera dig till HRworks-programmet för enkel inloggning. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på HRworks-enkel inloggning. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-4.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera HRworks enkel inloggning manuellt öppnar du ett nytt webbläsarfönster och loggar in på företagets webbplats för HRworks-enkel inloggning som administratör och utför följande steg:

1. Klicka på grunderna för **Administratörs**  >  **Basics**  >  **säkerhet**  >  **enkel inloggning på** den vänstra sidan av meny raden och utför följande steg:

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure01.png)

    a. Markera kryss rutan **Använd enkel inloggning** .

    b. Välj **XML-metadata** som indata- **metod för metadata**.

    c. Välj **individuell NameID-ID** som **värde för NameID**.

    d. I anteckningar öppnar du XML-metadata som du laddade ned från Azure Portal, kopierar innehållet och klistrar in det i text rutan **metadata** .

    e. Klicka på **Spara**.

### <a name="create-hrworks-single-sign-on-test-user"></a>Skapa test användare av HRworks-enkel inloggning

Om du vill aktivera Azure AD-användare loggar du in på HRworks enkel inloggning, de måste vara etablerade i HRworks enkel inloggning. I HRworks enkel inloggning är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på HRworks enkel inloggning som administratör.

1. Klicka på **administratör**  >  **personer**  >  **personer**  >  **ny person** från vänster sida av meny raden.

     ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure02.png)

1. Klicka på **Nästa**på popup-fönstret.

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure03.png)

1. I popup-fönstret **Skapa ny person med ett land för juridiska villkor** fyller du i respektive information som **förnamn**, **efter namn** och klickar på **skapa**.

    ![Konfigurera enkel inloggning](./media/hrworks-single-sign-on-tutorial/configure04.png)

## <a name="test-sso"></a>Testa SSO

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för enkel inloggning på HRworks i åtkomst panelen, bör du loggas in automatiskt på den HRworks enkel inloggning som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova HRworks enkel inloggning med Azure AD](https://aad.portal.azure.com/)