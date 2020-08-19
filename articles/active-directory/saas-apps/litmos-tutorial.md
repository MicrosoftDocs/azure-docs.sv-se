---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Litmos | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Litmos.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.openlocfilehash: 17adf6609adde002773ffec7d8c0e99396febab8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549729"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med Litmos

I den här självstudien får du lära dig hur du integrerar Litmos med Azure Active Directory (Azure AD). När du integrerar Litmos med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Litmos.
* Gör det möjligt för användarna att logga in automatiskt till Litmos med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Litmos för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Litmos stöder **IDP**-initierad enkel inloggning
* Litmos stöder **JIT**-användaretablering (Just-In-Time)

## <a name="adding-litmos-from-the-gallery"></a>Lägga till Litmos från galleriet

För att konfigurera integreringen av Litmos med Azure AD måste du lägga till Litmos från galleriet i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Litmos** i sökrutan.
1. Välj **Litmos** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Konfigurera och testa enkel inloggning med Azure AD för Litmos

Konfigurera och testa Azure AD SSO med Litmos med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Litmos.

Om du vill konfigurera och testa Azure AD SSO med Litmos, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera LITMOS SSO](#configure-litmos-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Litmos test User](#create-litmos-test-user)** -om du vill ha en motsvarighet till B. Simon i Litmos som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Litmos** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **Konfigurera enkel inloggning med SAML** anger du värdena för följande fält:

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<companyname>.litmos.com/account/Login`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med den faktiska identifieraren och svars-URL:en, som beskrivs senare i självstudien, eller kontakta [Litmos kundsupport](https://www.litmos.com/contact-us) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Litmos** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Litmos.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **Litmos** i listan med program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-litmos-sso"></a>Konfigurera Litmos SSO

1. Öppna ett nytt webbläsarfönster och logga in på din Litmos-företagswebbplats som administratör.

2. Klicka på **Konton** i navigeringsfältet till vänster.

    ![Avsnittet Konton på appsidan][22]

3. Klicka på fliken **Integreringar**.

    ![Fliken Integrering][23]

4. Rulla ned till **Tredjepartsintegrationer** på fliken **Integrationer** och klicka på fliken **SAML 2.0**.

    ![Avsnittet SAML 2.0][24]

5. Kopiera värdet under **SAML-slutpunkten för Litmos är:** och klistra in det i textrutan **Svars-URL** i avsnittet **Litmos-domän och -webbadresser** på Azure-portalen.

    ![SAML-slutpunkt][26]

6. Utför följande steg i **Litmos**-programmet:

    ![Litmos-program][25]

    a. Klicka på **Aktivera SAML**.

    b. Öppna ditt base-64-kodade certifikat i Anteckningar, kopiera innehållet till Urklipp och klistra sedan in det i textrutan **SAML X.509-certifikat**.

    c. Klicka på **Spara ändringar**.

### <a name="create-litmos-test-user"></a>Skapa Litmos-testanvändare

Målet med det här avsnittet är att skapa en användare med namnet Britta Simon i Litmos. Litmos-programmet stöder JIT-etablering (Just-in-Time). Det innebär att ett användarkonto skapas automatiskt om det behövs vid ett försök att komma åt programmet via åtkomstpanelen.

**Skapa en användare med namnet Britta Simon i Litmos genom att utföra följande steg:**

1. Öppna ett nytt webbläsarfönster och logga in på din Litmos-företagswebbplats som administratör.

2. Klicka på **Konton** i navigeringsfältet till vänster.

    ![Avsnittet Konton på appsidan][22]

3. Klicka på fliken **Integreringar**.

    ![Fliken Integrationer][23]

4. Rulla ned till **Tredjepartsintegrationer** på fliken **Integrationer** och klicka på fliken **SAML 2.0**.

    ![SAML 2.0][24]

5. Välj **Skapa användare automatiskt**.
  
    ![Skapa användare automatiskt][27]

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Litmos-panelen på åtkomstpanelen bör du automatiskt loggas in i Litmos-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Litmos med Azure AD](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png