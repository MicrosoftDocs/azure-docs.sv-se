---
title: 'Självstudie: Azure Active Directory integrering med dmarcian | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och dmarcian.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/01/2019
ms.author: jeedes
ms.openlocfilehash: 8868b17766513ba1e93b25bf2aeff6553c62ba62
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88536163"
---
# <a name="tutorial-integrate-dmarcian-with-azure-active-directory"></a>Självstudie: integrera dmarcian med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar dmarcian med Azure Active Directory (Azure AD). När du integrerar dmarcian med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till dmarcian.
* Gör det möjligt för användarna att logga in automatiskt till dmarcian med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* dmarcian för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* dmarcian har stöd för **SP- och IDP**-initierad enkel inloggning

## <a name="adding-dmarcian-from-the-gallery"></a>Lägga till dmarcian från galleriet

För att konfigurera integrering av dmarcian i Azure AD behöver du lägga till dmarcian från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **dmarcian** i sökrutan.
1. Välj **dmarcian** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med dmarcian med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i dmarcian.

Om du vill konfigurera och testa Azure AD SSO med dmarcian, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera DMARCIAN SSO](#configure-dmarcian-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa dmarcian test User](#create-dmarcian-test-user)** -om du vill ha en motsvarighet till B. Simon i dmarcian som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **dmarcian** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  

    ```http
    https://us.dmarcian.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-eu.com/sso/saml/<ACCOUNT_ID>/sp.xml
    https://dmarcian-ap.com/sso/saml/<ACCOUNT_ID>/sp.xml
    ```

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:

    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-eu.com/login/<ACCOUNT_ID>/handle/
    https://dmarcian-ap.com/login/<ACCOUNT_ID>/handle/
    ```

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    
    ```http
    https://us.dmarcian.com/login/<ACCOUNT_ID>
    https://dmarcian-eu.com/login/<ACCOUNT_ID>
    https://dmarciam-ap.com/login/<ACCOUNT_ID>
    ```
     
    > [!NOTE] 
    > Dessa värden är inte verkliga. Du kommer att uppdatera de här värdena med faktisk identifierare, svars-URL och inloggnings-URL. Detta förklaras senare i självstudien.

4. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på kopieringsknappen för att kopiera **App Federation Metadata-URL** och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/copy-metadataurl.png)

### <a name="configure-dmarcian-sso"></a>Konfigurera dmarcian SSO

1. Om du vill automatisera konfigurationen i dmarcian måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations dmarcian** för att dirigera dig till dmarcian-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på dmarcian. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera dmarcian manuellt öppnar du ett nytt webbläsarfönster och loggar in på din dmarcian-företags webbplats som administratör och utför följande steg:

4. Klicka på **Profil** i det övre högra hörnet och navigera till **Inställningar**.

    ![Inställningarna](./media/dmarcian-tutorial/tutorial_dmarcian_pref.png)

5. Rulla nedåt och klicka på avsnittet **Enkel inloggning** följt av **Konfigurera**.

    ![Enskild](./media/dmarcian-tutorial/tutorial_dmarcian_sso.png)

6. På sidan **Enkel inloggning med SAML** anger du **Status** till **Aktiverad** och utför följande steg:

    ![Autentiseringen](./media/dmarcian-tutorial/tutorial_dmarcian_auth.png)

    * I avsnittet **Add dmarcian to your Identity Provider** (Lägg till dmarcian i identitetsprovidern) klickar du på **COPY** (Kopiera) för att kopiera **URL för konsumenttjänst för försäkran** för din instans och klistrar in den i textrutan **Svars-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    * I avsnittet **Add dmarcian to your Identity Provider** (Lägg till dmarcian i identitetsprovidern) klickar du på **COPY** (Kopiera) för att kopiera **Entitets-ID** för din instans och klistrar in den i textrutan **Identifier** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    * I avsnittet **Konfigurera autentisering** klistrar du i textrutan **Identity Provider Metadata** (Metadata för identitetsprovider) in den **URL för appfederationsmetadata** som du har kopierat från Azure-portalen.

    * I avsnittet **Konfigurera autentisering** klistrar du i textrutan **Attribute Statements** (Attributinstruktioner) in URL:en `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    * I avsnittet **Set up Login URL** (Konfigurera inloggnings-URL) kopierar du **Inloggnings-URL** för din instans och klistrar in den i textrutan **Inloggnings-URL** i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

        > [!Note]
        > Du kan ändra **Inloggnings-URL** enligt din organisation.

    * Klicka på **Spara**.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till dmarcian.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **dmarcian**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-dmarcian-test-user"></a>Skapa dmarcian-testanvändare

För att göra det möjligt för Azure AD-användare att logga in i dmarcian måste de etableras till dmarcian. I dmarcian är etablering en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på dmarcian som säkerhetsadministratör.

2. Klicka på **Profil** i det övre högra hörnet och gå till **Hantera användare**.

    ![Användaren](./media/dmarcian-tutorial/tutorial_dmarcian_user.png)

3. På höger sida av avsnittet **SSO Users** (Användare av enkel inloggning) klickar du på **Lägg till ny användare**.

    ![Lägg till användare](./media/dmarcian-tutorial/tutorial_dmarcian_addnewuser.png)

4. I popup-fönstret **Lägg till ny användare** utför du följande steg:

    ![Den nya användaren](./media/dmarcian-tutorial/tutorial_dmarcian_save.png)

    a. I text rutan **ny användarens e-post** anger du e-postmeddelandet som **brittasimon \@ contoso.com**.

    b. Om du vill ge administratörsrättigheter till användaren väljer du **Make User an Admin** (Gör användaren till administratör).

    c. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på dmarcian-panelen i åtkomstpanelen bör du automatiskt loggas in på dmarcian som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

