---
title: 'Självstudie: Azure Active Directory integration på ett sätt som vi gör | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och hur vi gör.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 310a42d25ce7fb7970777e8f36abbbee562ab01d
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88523903"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Självstudie: integrera hur vi gör med Azure Active Directory

I den här självstudien får du lära dig att integrera hur vi gör med Azure Active Directory (Azure AD). När du integrerar på sätt med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till hur vi gör.
* Gör det möjligt för användarna att vara automatiskt inloggade så att vi kan använda sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Sätt att använda enkel inloggning (SSO) med en prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Hur vi stöder **SP** -INITIERAd SSO
* Sätt som vi stöder **just i tid för** användar etablering

## <a name="adding-way-we-do-from-the-gallery"></a>Lägga till sätt från galleriet

Om du vill konfigurera en integrering av hur vi gör i Azure AD måste du lägga till hur vi gör från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** skriver du **så** här i sökrutan.
1. Välj **hur vi gör** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med hur vi använder en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren på det sätt som vi gör.

Om du vill konfigurera och testa Azure AD SSO med hur vi gör, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera hur vi gör SSO](#configure-way-we-do-sso)** -för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa ett sätt att testa användaren](#create-way-we-do-test-user)** – för att få en motsvarighet till Britta Simon på ett sätt som är länkat till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan [Azure Portal](https://portal.azure.com/) **går du till sidan för program integrering** och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [kund support teamet](mailto:support@waywedo.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (RAW)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

   ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. På sidan **Konfigurera hur vi gör** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Konfigurera hur vi gör SSO

1. Om du vill automatisera konfigurationen på det sätt som vi gör måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

1. När du har lagt till tillägg i webbläsaren klickar du på **installations sätt vi** kommer att omdirigera dig till det sätt vi gör med programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på det sätt vi gör. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

1. Om du vill konfigurera hur vi gör det manuellt öppnar du ett nytt webbläsarfönster och loggar in på ditt sätt som en företags webbplats som administratör och utför följande steg:

1. Klicka på **person ikonen** i det övre högra hörnet på valfri sida och klicka sedan på **konto** i list menyn.

    ![Sätt vi tar hänsyn till](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Klicka på **meny ikonen** för att öppna menyn för push-navigering och klicka på **enkel inloggning**.

    ![Sätt vi gör det enkla](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Utför följande steg på installations sidan för **enkel inloggning** :

    ![Sätt vi sparar](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Klicka på **aktivera enkel inloggning** för att växla till **Ja** för att aktivera enkel inloggning.

    b. I text rutan **namn på enkel inloggning** anger du ditt namn.

    c. I text rutan **entitets-ID** klistrar du in värdet för **Azure AD-identifierare**, som du har kopierat från Azure Portal.

    d. I textrutan **SAML SSO URL** (URL för enkel inloggning med SAML) klistrar du in det värde för **Inloggnings-URL** som du har kopierat från Azure-portalen.

    e. Ladda upp certifikatet genom att klicka på knappen **Välj** bredvid **certifikat**.

    f. **Valfria inställningar** -
    
    * Aktivera lösen ord – när det här alternativet är inaktiverat används de vanliga lösen ords funktionerna så att användarna bara kan använda enkel inloggning.

    * Aktivera automatisk etablering – när det här är aktiverat kommer den e-postadress som används för inloggning att automatiskt jämföras med listan över användare som vi gör. Om e-postadressen inte matchar en aktiv användare på det sätt som vi gör, lägger den automatiskt till ett nytt användar konto för personen som loggar in och begär eventuell information som saknas.

      > [!NOTE]
      > Användare som läggs till via enkel inloggning läggs till som allmänna användare och tilldelas inte någon roll i systemet. En administratör kan gå in och ändra sin säkerhets roll som redigerare eller administratör och kan också tilldela en eller flera roller i ett organisations schema.

    ex. Klicka på Spara för att **Spara** dina inställningar.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till det sätt vi gör.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer **du hur vi gör**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-way-we-do-test-user"></a>Skapa hur vi testar användare

I det här avsnittet skapas en användare som heter Britta Simon på det sätt som vi gör. Det sätt vi stöder just-in-Time-etablering av användare, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns på samma sätt som vi gör, skapas en ny efter autentiseringen.

> [!Note]
> Om du behöver skapa en användare manuellt kontaktar du [kund support teamet](mailto:support@waywedo.com).

### <a name="test-sso"></a>Testa SSO

När du väljer sätt på panelen på åtkomst panelen, bör du loggas in automatiskt på det sätt som du ställer in SSO. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)