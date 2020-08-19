---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med personer | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och personer.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.openlocfilehash: 93b418a2bc4e1b03370b1bb86bd7da95255db0b8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552398"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med personer

I den här självstudien får du lära dig hur du integrerar personer med Azure Active Directory (Azure AD). När du integrerar personer med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till personer.
* Gör det möjligt för användarna att logga in automatiskt till personer med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Användare med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Personer stöder **SP** -INITIERAd SSO
* Mobila program för personer kan nu konfigureras med Azure AD för att aktivera SSO. I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

>[!NOTE]
>ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-people-from-the-gallery"></a>Lägga till personer från galleriet

Om du vill konfigurera integreringen av personer i Azure AD måste du lägga till personer från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **personer** i sökrutan.
1. Välj **personer** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-people"></a>Konfigurera och testa enkel inloggning med Azure AD för personer

Konfigurera och testa Azure AD SSO med personer som använder en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i personer.

Om du vill konfigurera och testa Azure AD SSO med personer, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
2. **[Konfigurera personer SSO](#configure-people-sso)** – för att konfigurera inställningar för enkel inloggning på program sidan.
    1. **[Skapa användare som testar användare](#create-people-test-user)** – så att de har en motsvarighet till B. Simon i personer som är länkade till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering för **personer** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<company name>.peoplehr.net`

    b. I textrutan **Identifierare** skriver du in en URL: `https://www.peoplehr.com`

    c. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med en faktisk inloggnings-URL och svars-URL. Kontakta [personernas kund support team](mailto:customerservices@peoplehr.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

6. I avsnittet **Konfigurera personer** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till personer.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. Välj **personer**i listan program.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-people-sso"></a>Konfigurera personer SSO

1. Om du vill automatisera konfigurationen inom personer måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations programmet** leder dig till programmet People. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på personer. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera personer manuellt öppnar du ett nytt webbläsarfönster och loggar in på företags webbplatsen för personer som administratör och utför följande steg:
   
4. I menyn på vänster sida klickar du på **Inställningar**.

    ![Konfigurera enkel inloggning](./media/people-tutorial/tutorial_people_001.png)

5. Klicka på **Företag**.

    ![Konfigurera enkel inloggning](./media/people-tutorial/tutorial_people_002.png)

6. Klicka på **Bläddra** för att ladda upp den hämtade metadata-filen på **SAML-datafilen med SAML-datafilen i uppladdningen "enkel inloggning**".

    ![Konfigurera enkel inloggning](./media/people-tutorial/tutorial_people_003.png)

### <a name="create-people-test-user"></a>Skapa användare test användare

I det här avsnittet skapar du en användare som heter B. Simon i personer. Arbeta med [kund support teamet](mailto:customerservices@peoplehr.com) för att lägga till användare på människa-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen personer på åtkomst panelen bör du loggas in automatiskt till de personer som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-people-mobile"></a>Testa SSO för personer (mobil)

1. Öppna mobil program för personer. På inloggnings sidan anger du **e-post-ID** och klickar sedan på **enkel inloggning**.

    ![Inloggningen](./media/people-tutorial/test01.png)

2. Ange **organisations-UserID** och klicka på **Nästa**.

    ![E-postmeddelandet](./media/people-tutorial/test02.png)

3. Efter lyckad inloggning visas start sidan för programmet nedan:

    ![En gång](./media/people-tutorial/test03.png)

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova personer med Azure AD](https://aad.portal.azure.com)
