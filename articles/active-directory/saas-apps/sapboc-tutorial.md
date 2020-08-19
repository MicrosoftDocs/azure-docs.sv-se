---
title: 'Självstudie: Azure Active Directory integration med SAP Analytics-moln | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och SAP Analytics-molnet.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.openlocfilehash: 9edbb499788b61135d761c5dfa69ce42c9275932
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88548760"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Självstudie: integrera SAP Analytics-moln med Azure Active Directory

I den här självstudien får du lära dig att integrera SAP Analytics-moln med Azure Active Directory (Azure AD). När du integrerar SAP Analytics-molnet med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till SAP Analytics-molnet.
* Gör det möjligt för användarna att logga in automatiskt till SAP Analytics-molnet med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* SAP Analytics Cloud enkel inloggning (SSO) aktive rad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* SAP Analytics-moln stöder **SP** -INITIERAd SSO

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>Lägga till SAP Analytics-moln från galleriet

Om du vill konfigurera integreringen av SAP Analytics-molnet i Azure AD måste du lägga till SAP Analytics-molnet från galleriet till listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **SAP Analytics Cloud** i sökrutan.
1. Välj **moln analys moln** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

Konfigurera och testa Azure AD SSO med SAP Analytics-molnet med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i SAP Analytics-molnet.

Om du vill konfigurera och testa Azure AD SSO med SAP Analytics-molnet slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera SAP Analytics Cloud SSO](#configure-sap-analytics-cloud-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
4. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
5. **[Skapa ett SAP Analytics Cloud test-användare](#create-sap-analytics-cloud-test-user)** – för att få en motsvarighet till B. Simon i SAP Analytics-molnet som är länkat till Azure AD-representation av användare.
6. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

### <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. På sidan för integrering av moln program i **SAP Analytics** i [Azure Portal](https://portal.azure.com/)letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    a. I textrutan **Inloggnings-URL** anger du en URL enligt följande mönster: 

    - `https://<sub-domain>.sapanalytics.cloud/`
    - `https://<sub-domain>.sapbusinessobjects.cloud/`

    b. I textrutan **Identifierare (entitets-ID)** anger du en URL enligt följande mönster: 

    - `<sub-domain>.sapbusinessobjects.cloud`
    - `<sub-domain>.sapanalytics.cloud`

    > [!NOTE] 
    > Värdena i dessa URL:er är bara exempel. Uppdatera värden med de faktisk inloggnings-URL och identifierar-URL. Om du vill hämta inloggnings-URL: en kan du kontakta [support teamet för SAP Analytics Cloud client](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Du kan hämta ID-URL: en genom att ladda ned SAP Analytics Cloud metadata från administratörs konsolen. Detta beskrivs senare i självstudien.

4. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du upp **XML för federationsmetadata** och väljer **Hämta** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

6. I avsnittet **Konfigurera SAP Analytics Cloud** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>Konfigurera SAP Analytics Cloud SSO

1. Logga in på din moln företags webbplats för SAP Analytics som administratör i ett annat webbläsarfönster.

2. Välj **meny**  >  **system**  >  **Administration**.
    
    ![Välj Meny, sedan System och därefter Administration](./media/sapboc-tutorial/config1.png)

3. På fliken **Säkerhet** väljer du ikonen **Redigera** (pennan).
    
    ![På fliken Säkerhet väljer du ikonen Redigera](./media/sapboc-tutorial/config2.png)  

4. För **Autentiseringsmetod** väljer du **SAML Single Sign-On (SSO)** (Enkel inloggning (SSO) med SAML).

    ![Välja enkel inloggning med SAML som autentiseringsmetod](./media/sapboc-tutorial/config3.png)  

5. Ladda ned metadata för tjänstleverantör (steg 1) genom att välja **Ladda ned**. I metadatafilen letar du upp och kopierar värdet för **entityID**. I dialogrutan **Grundläggande SAML-konfiguration** i Azure-portalen klistrar du in värdet i rutan **Identifierare**.

    ![Kopiera och klistra in värdet för entityID](./media/sapboc-tutorial/config4.png)  

6. Ladda upp metadata för tjänstleverantör (steg 2) i den fil som du laddade ned från Azure-portalen genom att gå till **Upload Identity Provider metadata** (Ladda upp metadata för tjänstleverantör) och välja **Ladda upp**.  

    ![Under Upload Identity Provider metadata (Ladda upp metadata för tjänstleverantör) väljer du Ladda upp](./media/sapboc-tutorial/config5.png)

7. I listan **Användarattribut** väljer du det användarattribut (steg 3) som du vill använda för implementeringen. Det här användarattributet mappar till identitetsprovidern. Du ange ett anpassat attribut på användarens sida genom att använda alternativet **Custom SAML Mapping** (Anpassad SAML-mappning). Eller så kan du välja antingen **Email** (E-post) eller **USER ID** (Användar-ID) som användarattribut. I vårt exempel valde vi **Email** (E-post) eftersom vi mappade anspråket för användar-ID med attributet **userprincipalname** i avsnittet **Användarattribut och anspråk** i Azure-portalen. Detta ger ett unikt användar-e-postmeddelande som skickas till moln programmet SAP Analytics i varje lyckad SAML-svar.

    ![Välja användarattribut](./media/sapboc-tutorial/config6.png)

8. Verifiera kontot med identitetsprovidern (steg 4) genom att gå till rutan **Login Credential (Email)** (Autentiseringsuppgifter (e-post)) och ange användarens e-postadress. Välj sedan **Verify Account** (Verifiera konto). Systemet lägger till autentiseringsuppgifter till användarkontot.

    ![Ange e-postadress och välj Verify Account (Verifiera konto)](./media/sapboc-tutorial/config7.png)

9. Välj ikonen **Spara**.

    ![Ikonen Spara](./media/sapboc-tutorial/save.png)

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till SAP Analytics-molnet.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **SAP Analytics-moln**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

### <a name="create-sap-analytics-cloud-test-user"></a>Skapa en SAP Analytics Cloud test-användare

Azure AD-användare måste vara etablerade i SAP Analytics-molnet innan de kan logga in på SAP Analytics-molnet. I SAP Analytics-molnet är etableringen en manuell uppgift.

Så här etablerar du ett användarkonto:

1. Logga in på din SAP Analytics Cloud Company-webbplats som administratör.

2. Välj **meny**  >  **säkerhets**  >  **användare**.

    ![Lägga till medarbetare](./media/sapboc-tutorial/user1.png)

3. På sidan **Användare** lägger du till nya användaruppgifter genom att välja **+**. 

    ![Sidan Lägg till användare](./media/sapboc-tutorial/user4.png)

    Utför sedan följande steg:

    a. I rutan **användar-ID** anger du användar-ID: t för användaren, t. ex. **B**.

    b. I rutan **förnamn** anger du användarens förnamn, t. ex. **B**.

    c. I rutan **LAST NAME** (Efternamn) anger du efternamn för användaren: **Simon**.

    d. I rutan **VISNINGS namn** anger du användarens fullständiga namn, t. ex. **B. Simon**.

    e. I rutan **E-MAIL** (E-postadress) anger du användarens e-postadress: `b.simon@contoso.com`.

    f. På sidan **Select Roles** (Välj roller) väljer du rätt roll för användaren och väljer sedan **OK**.

      ![Välj roll](./media/sapboc-tutorial/user3.png)

    ex. Välj ikonen **Spara**.

### <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen för moln analys i SAP på åtkomst panelen, bör du loggas in automatiskt på det SAP Analytics-moln som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

