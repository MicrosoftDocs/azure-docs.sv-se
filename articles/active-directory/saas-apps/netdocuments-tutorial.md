---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med nätdokument | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och NetDocuments.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.openlocfilehash: 245818b60647539c7fff7c45dcecdc6840153fb5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549049"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med NetDocuments

I den här självstudien får du lära dig hur du integrerar NetDocuments med Azure Active Directory (Azure AD). När du integrerar NetDocuments med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till NetDocuments.
* Gör det möjligt för användarna att logga in automatiskt till NetDocuments med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* NetDocuments (SSO)-prenumeration med enkel inloggning aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* NetDocuments stöder **SP** -INITIERAd SSO

## <a name="adding-netdocuments-from-the-gallery"></a>Lägga till NetDocuments från galleriet

Om du vill konfigurera en integrering av NetDocuments i Azure AD måste du lägga till NetDocuments från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **NetDocuments** i sökrutan.
1. Välj **netdokument** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Konfigurera och testa enkel inloggning med Azure AD för nätdokument

Konfigurera och testa Azure AD SSO med NetDocuments med hjälp av en test användare som kallas **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i NetDocuments.

Om du vill konfigurera och testa Azure AD SSO med net-dokument, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera NetDocuments SSO](#configure-netdocuments-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa NetDocuments test User](#create-netdocuments-test-user)** – om du vill ha en motsvarighet till B. Simon i NetDocuments som är länkat till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan **netdokuments** program integration och letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. Skriv en URL i text rutan **svars-URL** med följande mönster: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: `http://netdocuments.com/VAULT`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska inloggnings-URL: en och svars-URL: en. Lagrings-ID är ett värde som börjar med **ca-** följt av 8 tecken kod som är associerad med din NetDocuments-lagringsplats. Mer information finns i [Netdokuments federerade identitets Support dokument](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) . Alternativt kan du kontakta [NetDocuments-klientens support team](https://support.netdocuments.com/hc/) för att få dessa värden om du har problem med att konfigurera med hjälp av ovanstående information. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. NetDocuments-programmet förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade attribut mappningar i konfigurationen för SAML-token. Följande skärmbild visar en lista över standardattribut, där **nameidentifier** mappas med **user.userprincipalname**. NetDocuments-programmet förväntar sig att **NameIdentifier** mappas med **Anställningsnr** eller andra anspråk som gäller din organisation som **NameIdentifier**, så du måste redigera attributmappning genom att klicka på ikonen **Redigera** och ändra attributet mappning.

    ![image](common/edit-attribute.png)

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

1. I avsnittet **Konfigurera NetDocuments** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till NetDocuments.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **NetDocuments**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-netdocuments-sso"></a>Konfigurera NetDocuments SSO

1. Logga in på företagets företags webbplats som administratör i ett annat webbläsarfönster.

2. I det övre högra hörnet väljer du ditt namn>**admin**.

3. Välj **Security Center**.
   
    ![Lagringsplats](./media/netdocuments-tutorial/security-center.png "Security Center")

4. Välj **Avancerad autentisering**.
    
    ![Konfigurera avancerade autentiserings alternativ](./media/netdocuments-tutorial/advance-authentication.png "Konfigurera avancerade autentiserings alternativ")

5.  Utför följande steg på fliken **federerad-ID** :   
   
    ![Federerade identiteter](./media/netdocuments-tutorial/federated-id.png "Federerade identiteter")
   
    a. Som **federerad identitets Server typ**väljer du **Active Directory Federation Services (AD FS)**.
    
    b.  Välj **Välj fil**för att ladda upp den hämtade metadatafilen som du har laddat ned från Azure Portal.
    
    c.  Välj **Spara**.

### <a name="create-netdocuments-test-user"></a>Skapa nätdokument test användare

Om du vill att Azure AD-användare ska kunna logga in på NetDocuments måste de tillhandahållas i NetDocuments. Om det gäller net-dokument är etableringen en manuell uppgift.

**Gör följande för att etablera ett användarkonto:**

1. Logga in på din företags webbplats för net- **dokument** som administratör.

2. I det övre högra hörnet väljer du ditt namn>**admin**.
   
    ![Administratör](./media/netdocuments-tutorial/user-admin.png "Administratör")

3. Välj **Användare och grupper**.
   
    ![Lagringsplats](./media/netdocuments-tutorial/users-groups.png "Lagringsplats")

4. I text rutan **e-postadress** skriver du in e-postadressen för ett giltigt Azure Active Directory konto som du vill etablera och klickar sedan på **Lägg till användare**.
   
    ![E-postadress](./media/netdocuments-tutorial/user-mail.png "E-postadress")
   
    > [!NOTE]
    > Den Azure Active Directory konto innehavaren får ett e-postmeddelande som innehåller en länk för att bekräfta kontot innan det blir aktivt. Du kan använda andra NetDocuments för att skapa verktyg för användar konton eller API: er som tillhandahålls av NetDocuments för att etablera Azure Active Directory användar konton.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen netdokument på åtkomst panelen, bör du loggas in automatiskt på de net-dokument som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Testa NetDocuments med Azure AD](https://aad.portal.azure.com/)
