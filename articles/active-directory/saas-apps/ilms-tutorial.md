---
title: 'Självstudie: Azure Active Directory integrering med iLMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iLMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.openlocfilehash: 219dd6e4a8f04da8b28a28e5473394f0721e4013
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88545221"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Självstudie: integrera iLMS med Azure Active Directory

I den här självstudien får du lära dig hur du integrerar iLMS med Azure Active Directory (Azure AD). När du integrerar iLMS med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till iLMS.
* Gör det möjligt för användarna att logga in automatiskt till iLMS med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* iLMS för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. iLMS stöder **SP-och IDP** -INITIERAd SSO

## <a name="adding-ilms-from-the-gallery"></a>Lägga till iLMS från galleriet

Om du vill konfigurera integreringen av iLMS i Azure AD måste du lägga till iLMS från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **iLMS** i sökrutan.
1. Välj **iLMS** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med iLMS med hjälp av en test användare som kallas **Britta Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i iLMS.

Om du vill konfigurera och testa Azure AD SSO med iLMS, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera ILMS SSO](#configure-ilms-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa iLMS test User](#create-ilms-test-user)** – om du vill ha en motsvarighet till Britta Simon i iLMS som är länkad till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **iLMS** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I text rutan **identifierare** klistrar du in det **ID** -värde som du kopierar från **Service Provider-** avsnittet i SAML-inställningar i iLMS administrations Portal.

    b. I text rutan **svars-URL** klistrar du in värdet för **slut punkt (URL)** som du kopierar från **Service Provider** -avsnittet i SAML-inställningar i iLMS Admin Portal med följande mönster `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I text rutan **inloggnings-URL** klistrar du in värdet för **slut punkt (URL)** som du kopierar från **Service Provider** -avsnittet i SAML-inställningar i iLMS Admin portal som `https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Om du vill aktivera JIT-etablering förväntar ditt iLMS-program SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på ikonen**Redigera** för att öppna dialogrutan Användarattribut.

    > [!NOTE]
    > Du måste aktivera **skapa oidentifierat användar konto** i iLMS för att mappa dessa attribut. Följ anvisningarna [här](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) för att få en uppfattning om attributets konfiguration.

1. Utöver ovan förväntar sig iLMS-programmet att fler attribut skickas tillbaka i SAML-svar. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:

    | Name | Källattribut|
    | --------|------------- |
    | vändning | user.department |
    | regionen | User. State |
    | avdelning | user.jobtitle |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

1. På sidan **Set up Single Sign-On with SAML** (Konfigurera enkel inloggning med SAML) går du till avsnittet **SAML Signing Certificate** (SAML-signeringscertifikat), klickar på **Ladda ned** för att ladda ned **Federation Metadata-XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera iLMS** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ilms-sso"></a>Konfigurera iLMS SSO

1. Logga in på din **iLMS-administratörs Portal** som administratör i ett annat webbläsarfönster.

2. Klicka på **SSO: SAML** under fliken **Inställningar** för att öppna SAML-inställningar och utför följande steg:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/1.png)

3. Expandera avsnittet **tjänst leverantör** och kopiera **ID** och **slut punkt (URL)** -värdet.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/2.png) 

4. Klicka på **Importera metadata**under avsnittet **identitets leverantör** .

5. Välj den **federationsmetadata** som hämtades från Azure Portal från certifikat avsnittet **SAML-signering** .

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Följ stegen nedan om du vill aktivera JIT-etablering för att skapa iLMS-konton för att inte tolka användare:

    a. Kontrol lera **skapa oidentifierat användar konto**.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Mappa attributen i Azure AD med attributen i iLMS. I kolumnen attribut anger du attributets namn eller standardvärdet.

    c. Gå till fliken **affärs regler** och utför följande steg:

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/5.png)

    d. Markera **skapa okända regioner, avdelningar och avdelningar** för att skapa regioner, avdelningar och avdelningar som inte redan finns vid tidpunkten för enkel inloggning.

    e. Markera **Uppdatera användar profil under inloggningen** för att ange om användarens profil ska uppdateras med varje enkel inloggning.

    f. Om alternativet **Uppdatera tomma värden för fält som inte är obligatoriska i användar profilen** är markerat kommer det valfria profil fält som är tomt vid inloggningen också att användarens iLMS-profil ska innehålla tomma värden för dessa fält.

    ex. Markera **skicka e-postmeddelande om fel meddelande** och ange e-postadressen till den användare där du vill ta emot e-postmeddelandet.

7. Klicka på knappen **Spara** för att spara inställningarna.

    ![Konfigurera enkel inloggning](./media/ilms-tutorial/save.png)

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

I det här avsnittet ska du aktivera Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till iLMS.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **iLMS**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **Britta Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-ilms-test-user"></a>Skapa iLMS test användare

Programmet stöder just-in-Time-etablering och när användare har skapats i programmet automatiskt. JIT fungerar om du har klickat på kryss rutan **skapa oidentifierat användar konto** under inställningen SAML-konfiguration på iLMS administrations Portal.

Om du behöver skapa en användare manuellt följer du stegen nedan:

1. Logga in på din iLMS-företags webbplats som administratör.

2. Klicka på **registrera användare** under fliken **användare** för att öppna **Registrera användar** sidan.

   ![Lägga till medarbetare](./media/ilms-tutorial/3.png)

3. Utför följande steg på sidan **registrera användare** .

    ![Lägga till medarbetare](./media/ilms-tutorial/create_testuser_add.png)

    a. Skriv det första namnet som Britta i text rutan för **förnamn** .

    b. I text rutan **efter namn** skriver du det senaste namnet som Simon.

    c. Skriv e-postadressen till användaren som i text rutan **e-post-ID** BrittaSimon@contoso.com .

    d. I list rutan **region** väljer du värdet för region.

    e. Välj värdet för Division i list rutan **indelning** .

    f. I list rutan **avdelning** väljer du värdet för avdelning.

    ex. Klicka på **Spara**.

    > [!NOTE]
    > Du kan skicka e-post till användare genom att markera kryss rutan **Skicka registrerings post** .

### <a name="test-sso"></a>Testa SSO

När du väljer panelen iLMS på åtkomst panelen, bör du loggas in automatiskt på den iLMS som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
