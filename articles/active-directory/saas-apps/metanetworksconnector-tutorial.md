---
title: 'Självstudie: Azure Active Directory integrering med meta Networks Connector | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Meta Networks Connector.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: 71aaaeec174611a86b037b693187cc4f49fe1a0c
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88528955"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Självstudie: Azure Active Directory integrering med meta Networks Connector

I den här självstudien lär du dig att integrera Meta Networks Connector med Azure Active Directory (AD Azure).
Integreringen av Meta Networks Connector med Azure AD medför följande fördelar:

* Du kan i Azure AD styra vem som har åtkomst till Meta Networks Connector.
* Du kan göra så att dina användare automatiskt loggas in på Meta Networks Connector (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Meta Networks Connector behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* Meta Networks Connector-prenumeration med enkel inloggning aktiverat

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Meta Networks Connector har stöd för **SP**- och **IDP**-initierad enkel inloggning
 
* Meta Networks Connector har stöd för **Just-in-time**-användaretablering

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Lägga till Meta Networks Connector från galleriet

För att konfigurera integreringen av Meta Networks Connector i Azure AD behöver du lägga till Meta Networks Connector från galleriet till din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Meta Networks Connector från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Meta Networks Connector**, väljer **Meta Networks Connector** i resultatpanelen och klickar på knappen **Lägg till** för att lägga till programmet.

     ![Meta Networks Connector i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med Meta Networks Connector baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Meta Networks Connector upprättas.

För att kunna konfigurera och testa enkel inloggning i Azure AD med Meta Networks Connector slutför du följande byggstenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Meta Networks Connector](#configure-meta-networks-connector-single-sign-on)** – för att konfigurera inställningarna för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa Meta Networks Connector-testanvändare](#create-meta-networks-connector-test-user)** – för att ha en motsvarighet för Britta Simon i Meta Networks Connector som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Meta Networks Connector:

1. I [Azure-portalen](https://portal.azure.com/) går du till programintegreringssidan för **Meta Networks Connector** och väljer **Enkel inloggning**.

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![Information om enkel inloggning med Meta Networks Connector-domän och URL:er](common/idp-intiated.png)

    a. I text rutan **identifierare** anger du en URL med hjälp av följande mönster: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![Information om enkel inloggning med Meta Networks Connector-domän och URL:er](common/both-advanced-urls.png)

    a. I text rutan **inloggnings-URL** skriver du en URL med följande mönster: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. I textrutan **Vidarebefordransstatus** skriver du en URL med följande mönster: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med faktisk identifierare, svars-URL och inloggnings-URL. Detta förklaras senare i självstudien.

6. Meta Networks Connector-programmet förväntar sig SAML-försäkringarna i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i din konfiguration av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![image](common/edit-attribute.png)
    
7. Utöver ovanstående förväntar sig Meta Networks Connector-programmet att några fler attribut skickas tillbaka i SAML-svaret. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** utför du följande steg för att lägga till SAML-tokenattributet enligt det som visas i tabellen nedan:
    
    | Name | Källattribut | Namnområde|
    | ---------------| --------------- | -------- |
    | förnamn | user.givenname | |
    | efternamn | user.surname | |
    | emailaddress| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | name | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | phone | user.telephonenumber | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. I avsnittet **Konfigurera Meta Networks Connector** kopierar du lämpliga URL:er efter behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>Konfigurera enkel inloggning för Meta Networks Connector

1. Öppna en ny flik i webbläsaren och logga in på ditt Meta Networks Connector-administratörskonto.
    
    > [!NOTE]
    > Meta Networks Connector är ett säkert system. Innan du får åtkomst till portalen måste du hämta din offentliga IP-adress till en lista över tillåtna på sidan. Hämta din offentliga IP-adress genom att följa den länk som anges [här](https://whatismyipaddress.com/). Skicka IP-adressen till [klient support teamet för meta Networks Connector](mailto:support@metanetworks.com) för att hämta din IP-adress till en lista över tillåtna.
    
2. Gå till **Administratör** och välj **Inställningar**.
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Se till att **Log Internet Traffic** (Logga Internettrafik) och **Force VPN MFA** (Framtvinga VPN MFA) är inaktiverade.
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Gå till **Administratör** och välj **SAML**.
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Utför följande steg på sidan **DETAILS** (Information):
    
    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Kopiera värdet för **SSO URL** (URL för enkel inloggning) och klistra in det i textrutan **Sign-In URL** (Inloggnings-URL) i avsnittet **Meta Networks Connector Domain and URLs** (Domän och URL:er för Meta Networks Connector).
    
    b. Kopiera värdet för **Recipient URL** (Mottagar-URL) och klistra in det i textrutan **Reply URL** (Svars-URL) i avsnittet **Meta Networks Connector Domain and URLs** (Domän och URL:er för Meta Networks Connector).
    
    c. Kopiera värdet för **Audience URI (SP Entity ID)** (Målgrupps-URI (SP-entitets-ID)) och klistra in det i textrutan **Identifier (Entity ID)** (Identifierare (entitets-ID)) i avsnittet **Meta Networks Connector Domain and URLs** (Domän och URL:er för Meta Networks Connector).
    
    d. Aktivera SAML
    
6. På fliken **GENERAL** (Allmänt) utför du följande steg:

    ![Konfigurera enkel inloggning](./media/metanetworksconnector-tutorial/configure5.png)

    a. I textrutan **Identity Provider Single Sign-On URL** (URL för enkel inloggning för identitetsprovider) klistrar du in värdet för den **inloggnings-URL** som du har kopierat från Azure-portalen.

    b. I textrutan **Identity Provider Issuer** (Utfärdare av identitetsprovider) klistrar du in värdet för den **Azure AD-identifierare** som du har kopierat från Azure-portalen.

    c. Öppna det certifikat som laddats ned från Azure-portalen i Anteckningar. Kopiera och klistra in innehållet i textrutan **X.509 Certificate** (X.509-certifikat).

    d. Aktivera **just-in-time-etablering**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Meta Networks Connector.

1. I Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Meta Networks Connector**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I programlistan väljer du **Meta Networks Connector**.

    ![Länken för Meta Networks Connector i programlistan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-meta-networks-connector-test-user"></a>Skapa testanvändare för Meta Networks Connector

I det här avsnittet skapas en användare som heter Britta Simon i Meta Networks Connector. Meta Networks Connector stöder just-in-time-etablering, vilket är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om det inte redan finns någon användare i Meta Networks Connector skapas en ny när du försöker komma åt Meta Networks Connector.

>[!Note]
>Om du behöver skapa en användare manuellt kontaktar du [kundsupporten för Meta Networks Connector](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Meta Networks Connector-panelen i åtkomstpanelen bör du automatiskt loggas in på Meta Networks Connector som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

