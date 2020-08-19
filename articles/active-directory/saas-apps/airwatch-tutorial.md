---
title: 'Självstudie: Azure Active Directory integrering med en "flygwatch" | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och AirWatch.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.openlocfilehash: 049066ffc5ce0aea2af956343dfa7ba97b6b5bb4
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542827"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Självstudie: integrera en Azure Active Directory

I den här självstudien får du lära dig hur du integrerar en Azure Active Directory (Azure AD). När du integrerar en flygwatch med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till din flygwatch.
* Gör det möjligt för användarna att logga in automatiskt för att se om de använder sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få en månads kostnads fri utvärderings version [här](https://azure.microsoft.com/pricing/free-trial/).
* Aktive rad prenumeration med enkel inloggning (SSO) i NETWATCH.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö. Vid en inloppet stöd för **SP** -initierad SSO.

## <a name="adding-airwatch-from-the-gallery"></a>Lägga till AirWatch från galleriet

För att konfigurera integrering av AirWatch i Azure AD behöver du lägga till AirWatch från galleriet till din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du " **Se** " i Sök fältet.
1. Välj **flygwatch** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med en test användare med namnet **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i flygwatch.

Om du vill konfigurera och testa Azure AD SSO med en tävling, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera flyg Watch SSO](#configure-airwatch-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Skapa AirWatch-testanvändare](#create-airwatch-test-user)** – för att ha en motsvarighet till Britta Simon i AirWatch som är länkad till en Azure AD-representation av användaren.
5. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integration i programmet för att **Se** avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. På sidan **grundläggande SAML-konfiguration** anger du värden för följande fält:

    1. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. I textrutan **Identifierare (entitets-ID)** anger du värdet som: `AirWatch`

    > [!NOTE]
    > Det här värdet är inte verkligt. Uppdatera värdet med den faktiska inloggnings-URL:en. Kontakta [supportteamet för AirWatch-klienten](https://www.air-watch.com/company/contact-us/) för att hämta det här värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. AirWatch-programmet förväntar sig att SAML-försäkran har ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

1. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Name |  Källattribut|
    |---------------|----------------|
    | UID | user.userprincipalname |
    | | |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned metadata-XML och spara den på din dator.

   ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera en flygbevakning** kopierar du lämpliga URL: er baserat på ditt krav.

   ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>Konfigurera en enkel inloggning med flygwatch

1. Logga in på företagets företags plats som administratör i ett annat webbläsarfönster.

1. På sidan Inställningar. Välj **inställningar > Enterprise-integration > katalog tjänster**.

   ![Inställningar](./media/airwatch-tutorial/ic791921.png "Inställningar")

1. Klicka på fliken **Användare**. I textrutan **Grundläggande unikt namn** skriver du ditt domännamn och klickar sedan på **Spara**.

   ![Användare](./media/airwatch-tutorial/ic791922.png "Användare")

1. Klicka på fliken **Server**.

   ![Server](./media/airwatch-tutorial/ic791923.png "Server")

1. Utför följande steg i avsnittet **LDAP** :

    ![Överför](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. För **Katalogtyp** väljer du **Ingen**.

    b. Välj **Use SAML For Authentication** (Använd SAML för autentisering).

1. I avsnittet **SAML 2,0** , för att överföra det hämtade certifikatet, klickar du på **överför**.

    ![Överför](./media/airwatch-tutorial/ic791932.png "Ladda upp")

1. I avsnittet **Begäran** utför du följande steg:

    ![Förfrågan](./media/airwatch-tutorial/ic791925.png "Förfrågan")  

    a. För **Request Binding Type** (Begär bindningstyp) väljer du **POST**.

    b. I dialog rutan **Konfigurera enkel inloggning på Flygwatch** i Azure Portal kopierar du **inloggnings-URL** -värdet och klistrar in det i text rutan för **enkel inloggning för identitetsprovider** .

    c. För **NameID-format** väljer du **E-postadress**.

    d. Som **säkerhet för autentiseringsbegäran**väljer du **ingen**.

    e. Klicka på **Spara**.

1. Klicka på fliken **Användare** igen.

    ![Användare](./media/airwatch-tutorial/ic791926.png "Användare")

1. I avsnittet **Attribut** utför du följande steg:

    ![Attribut](./media/airwatch-tutorial/ic791927.png "Attribut")

    a. I textrutan **Object Identifier** (Objektidentifierare) skriver du `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. I textrutan **Användarnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. I textrutan **Visningsnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. I textrutan **Förnamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. I textrutan **Efternamn** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. I textrutan **E-post** skriver du `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    ex. Klicka på **Spara**.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till en flygwatch.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I programlistan väljer du **AirWatch**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-airwatch-test-user"></a>Skapa AirWatch-testanvändare

Om du vill att Azure AD-användare ska kunna logga in på en flygwatch-klocka måste de tillhandahållas i en "flygwatch". För AirWatch är etablering en manuell uppgift.

**Konfigurera användaretablering genom att utföra följande steg:**

1. Logga in **på företags platsen** för din operatör som administratör.

2. I navigeringsfönstret på den vänstra sidan klickar du på **Konton** och sedan på **Användare**.
  
   ![Användare](./media/airwatch-tutorial/ic791929.png "Användare")

3. Klicka på **listvy**i menyn **användare** och klicka sedan på **Lägg till > Lägg till användare**.
  
   ![Lägg till användare](./media/airwatch-tutorial/ic791930.png "Lägg till användare")

4. I dialogrutan **Add / Edit User** (Lägg till/redigera användare) utför du följande steg:

   ![Lägg till användare](./media/airwatch-tutorial/ic791931.png "Lägg till användare")

   a. Skriv **Användarnamn**, **Lösenord**, **Bekräfta lösenord**, **Förnamn**, **Efternamn** samt **E-postadress** för ett giltigt Azure Active Directory-konto som du vill etablera i de relaterade textrutorna.

   b. Klicka på **Spara**.

> [!NOTE]
> Du kan använda andra verktyg för att skapa användar konton eller API: er för att skapa Azure AD-användarkonton.

### <a name="test-sso"></a>Testa SSO

När du väljer panelen för att visa en panel i åtkomst panelen, bör du loggas in automatiskt på den flygwatch som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
