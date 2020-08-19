---
title: 'Självstudie: Azure Active Directory integrering med Freedcamp | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Freedcamp.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.openlocfilehash: 50b5e21c8dc441187b3d78310db55b84aa1cd158
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88551038"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Självstudie: integrera Freedcamp med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar Freedcamp med Azure Active Directory (Azure AD). När du integrerar Freedcamp med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Freedcamp.
* Gör det möjligt för användarna att logga in automatiskt till Freedcamp med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Freedcamp för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Freedcamp stöder **SP-och IDP** -initierad SSO.

## <a name="adding-freedcamp-from-the-gallery"></a>Lägga till Freedcamp från galleriet

Om du vill konfigurera integreringen av Freedcamp i Azure AD måste du lägga till Freedcamp från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **Freedcamp** i sökrutan.
1. Välj **Freedcamp** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med Freedcamp med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Freedcamp.

Om du vill konfigurera och testa Azure AD SSO med Freedcamp, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. **[Konfigurera Freedcamp](#configure-freedcamp)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera Britta Simon att använda enkel inloggning i Azure AD.
5. **[Skapa Freedcamp test User](#create-freedcamp-test-user)** för att ha en motsvarighet till Britta Simon i Freedcamp som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **Freedcamp** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    1. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** skriver du en URL med följande mönster:  `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Användarna kan också ange URL-värden med avseende på sin egen kund domän och de kanske inte behöver mönstret `freedcamp.com` , de kan ange valfritt kundspecifika värde, specifika för deras program instans. Du kan också kontakta [Freedcamp client support team](mailto:devops@freedcamp.com) för ytterligare information om URL-mönster.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Freedcamp** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Konfigurera Freedcamp

1. Om du vill automatisera konfigurationen i Freedcamp måste du installera **Mina appar säker inloggnings webb läsar tillägg** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägg i webbläsaren, klickar du på **installations Freedcamp** för att dirigera dig till Freedcamp-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på Freedcamp. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-5.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera Freedcamp manuellt öppnar du ett nytt webbläsarfönster och loggar in på din Freedcamp-företags webbplats som administratör och utför följande steg:

4. Klicka på **profil** i det övre högra hörnet på sidan och gå sedan till **mitt konto**.

    ![Freedcamp-konfiguration](./media/freedcamp-tutorial/config01.png)

5. Klicka på **SSO** på vänster sida av meny raden och på sidan **SSO-anslutningar** utför följande steg:

    ![Freedcamp-konfiguration](./media/freedcamp-tutorial/config02.png)

    a. Skriv rubriken i text rutan **rubrik** .

    b. I text rutan **entitets-ID** klistrar du in värdet för **Azure AD-identifieraren** , som du har kopierat från Azure Portal.

    c. I text rutan **inloggnings-URL** klistrar du in URL-värdet för **inloggning** som du har kopierat från Azure Portal.

    d. Öppna det Base64-kodade certifikatet i anteckningar, kopiera dess innehåll och klistra in det i text rutan **certifikat** .

    e. Klicka på **Skicka**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas Britta Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `Britta Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension . Till exempel `BrittaSimon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Freedcamp.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Freedcamp**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-freedcamp-test-user"></a>Skapa Freedcamp test användare

Om du vill aktivera Azure AD-användare loggar du in på Freedcamp, de måste vara etablerade i Freedcamp. I Freedcamp är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på Freedcamp som säkerhets administratör i ett annat webbläsarfönster.

2. I det övre toright hörnet på sidan klickar du på **profil** och navigerar sedan till **Hantera system**.

    ![Freedcamp-konfiguration](./media/freedcamp-tutorial/config03.png)

3. På höger sida av sidan Hantera system utför du följande steg:

    ![Freedcamp-konfiguration](./media/freedcamp-tutorial/config04.png)

    a. Klicka på **Lägg till eller Bjud in användare**.

    b. I text rutan **e-postadress** anger du e-postadressen till användaren `Brittasimon@contoso.com` .

    c. Klicka på **Lägg till användare**.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen Freedcamp på åtkomst panelen, bör du loggas in automatiskt på den Freedcamp som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)