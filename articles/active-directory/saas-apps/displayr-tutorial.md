---
title: 'Självstudie: Azure Active Directory integration med nedspelare | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och displayer.
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
ms.openlocfilehash: a69b4a068e62c6376660a79b3a7c8ab5402b5704
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88536265"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Självstudie: integrera displayer med Azure Active Directory

I den här självstudien får du lära dig att integrera displayer med Azure Active Directory (Azure AD). När du integrerar displayer med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till displayer.
* Gör det möjligt för användarna att logga in automatiskt till displayer med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Prenumeration som är aktive rad för enkel inloggning (SSO).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Displayer stöder **SP** -initierad SSO.

## <a name="adding-displayr-from-the-gallery"></a>Lägga till displayer från galleriet

Om du vill konfigurera en integrering av displayer i Azure AD måste du lägga till displayer från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. Skriv **displayer** i sökrutan i avsnittet **Lägg till från galleriet** .
1. Välj **neduppspelning** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med nedspelare med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i displayer.

Om du vill konfigurera och testa Azure AD SSO med displayer slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** så att användarna kan använda den här funktionen.
2. Konfigurera **[displayer](#configure-displayr)** för att konfigurera SSO-inställningarna på program sidan.
3. **[Skapa en Azure AD test-användare](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** för att aktivera Britta Simon att använda enkel inloggning i Azure AD.
5. **[Skapa displayer-test användare](#create-displayr-test-user)** för att få en motsvarighet till Britta Simon i displayer som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **displayer** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<YOURDOMAIN>.displayr.com`

    b. I text rutan **identifierare (enhets-ID)** anger du en URL med hjälp av följande mönster:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Dessa värden är inte verkliga. Uppdatera dessa värden med faktisk inloggnings-URL och identifierare. Kontakta [displayer-klientens support team](mailto:support@displayr.com) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet Grundläggande SAML-konfiguration i Azure-portalen.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (base64)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på datorn.

   ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Avuppspelnings programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    ![image](common/edit-attribute.png)

1. Förutom över, förväntar sig att fler attribut ska skickas tillbaka i SAML-svar från visnings programmet. I avsnittet **användarattribut &-anspråk** i dialog rutan **grupp anspråk (förhands granskning)** utför du följande steg:

    a. Klicka på **pennan** bredvid **grupper som returneras i anspråk**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Välj **alla grupper** i alternativ listan.

    c. Välj **källattribut** för **grupp-ID**.

    d. Markera **anpassa namnet på grupp anspråket**.

    e. Markera **generera grupper som roll anspråk**.

    f. Klicka på **Spara**.

1. I avsnittet **Konfigurera avuppspelning** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Konfigurera uppspelning

1. Om du vill automatisera konfigurationen i displayer måste du installera **webb läsar tillägget Mina appar med säker inloggning** genom att klicka på **installera tillägget**.

    ![Mina Apps-tillägg](common/install-myappssecure-extension.png)

2. När du har lagt till tillägget i webbläsaren kan du klicka på **installations programmet** för att dirigera dig till displayer-programmet. Därifrån anger du administratörsautentiseringsuppgifter för att logga in på displayer. Webb läsar tillägget kommer automatiskt att konfigurera programmet åt dig och automatisera steg 3-6.

    ![Konfigurera konfiguration](common/setup-sso.png)

3. Om du vill konfigurera displayer manuellt öppnar du ett nytt webbläsarfönster och loggar in på displayer-företags platsen som administratör och utför följande steg:

4. Klicka på **Inställningar** och gå sedan till **konto**.

    ![Konfiguration](./media/displayr-tutorial/config01.png)

5. Växla till **Inställningar** från den övre menyn och rulla ned på sidan för att klicka på **Konfigurera enkel inloggning (SAML)**.

    ![Konfiguration](./media/displayr-tutorial/config02.png)

6. Utför följande steg på sidan **enkel inloggning (SAML)** :

    ![Konfiguration](./media/displayr-tutorial/config03.png)

    a. Markera kryss rutan **aktivera enkel inloggning (SAML)** .

    b. Kopiera det faktiska **ID** -värdet från avsnittet **grundläggande SAML-konfiguration** i Azure AD och klistra in det i text rutan **utfärdare** .

    c. I text rutan **inloggnings-URL** klistrar du in värdet för **inloggnings-URL: en**som du har kopierat från Azure Portal.

    d. I text rutan **Logga ut URL** klistrar du in värdet för **URL för utloggning**som du har kopierat från Azure Portal.

    e. Öppna certifikatet (base64) i anteckningar, kopiera innehållet och klistra in det i text rutan **certifikat** .

    f. **Grupp mappningar** är valfria.

    ex. Klicka på **Spara**.  

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

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till displayer.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **neduppspelning**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-displayr-test-user"></a>Skapa displayer-test användare

Om du vill aktivera Azure AD-användare loggar du in på displayer, de måste vara etablerade i displayer. I displayer är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på displayer som administratör.

2. Klicka på **Inställningar** och gå sedan till **konto**.

    ![Konfiguration av uppspelning](./media/displayr-tutorial/config01.png)

3. Växla till **Inställningar** från den övre menyn och rulla ned sidan tills **användare** klickar på **ny användare**.

    ![Konfiguration av uppspelning](./media/displayr-tutorial/config07.png)

4. Utför följande steg på sidan **ny användare** :

    ![Konfiguration av uppspelning](./media/displayr-tutorial/config06.png)

    a. I text rutan **namn** anger du namnet på användaren som **Brittasimon**.

    b. I textrutan **E-post** anger du användarens e-postadress som `Brittasimon@contoso.com`.

    c. Välj lämplig **grupp medlemskap**.

    d. Klicka på **Spara**.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen uppspelning på åtkomst panelen, bör du loggas in automatiskt på den nedspelare som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
