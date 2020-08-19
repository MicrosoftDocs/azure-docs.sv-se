---
title: 'Självstudie: Azure Active Directory integrering med iQualify LMS | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och iQualify LMS.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 8bf74994a07d69bbe53ec9a7e18f08b4232298bd
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552962"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Självstudie: Azure Active Directory integrering med iQualify LMS

I den här självstudien får du lära dig att integrera iQualify LMS med Azure Active Directory (Azure AD).
Genom att integrera iQualify LMS med Azure AD får du följande fördelar:

* Du kan kontrol lera Azure AD som har åtkomst till iQualify LMS.
* Du kan göra det möjligt för användarna att logga in automatiskt till iQualify LMS (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med iQualify LMS behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* iQualify LMS enkel inloggning aktive rad prenumeration

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* iQualify LMS stöder **SP-och IDP** -INITIERAd SSO
* iQualify LMS stöder **just-in-Time** User-etablering

## <a name="adding-iqualify-lms-from-the-gallery"></a>Lägga till iQualify LMS från galleriet

Om du vill konfigurera integrationen av iQualify LMS i Azure AD måste du lägga till iQualify LMS från galleriet till listan över hanterade SaaS-appar.

**Utför följande steg för att lägga till iQualify LMS från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **IQUALIFY LMS**, väljer **iQualify LMS** från resultat panelen och klickar sedan på knappen **Lägg** till för att lägga till programmet.

     ![iQualify LMS i resultat listan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet konfigurerar och testar du enkel inloggning i Azure AD med iQualify LMS baserat på en test användare som kallas **Britta Simon**.
För att enkel inloggning ska fungera måste en länk relation mellan en Azure AD-användare och den relaterade användaren i iQualify LMS upprättas.

Om du vill konfigurera och testa enkel inloggning med iQualify LMS i Azure AD måste du slutföra följande Bygg stenar:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera IQUALIFY LMS enkel inloggning](#configure-iqualify-lms-single-sign-on)** – så här konfigurerar du inställningarna för enkel inloggning på program sidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa IQUALIFY LMS test User](#create-iqualify-lms-test-user)** – om du vill ha en motsvarighet till Britta Simon i iQualify LMS som är länkad till Azure AD-representation av användare.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning med iQualify LMS i Azure AD:

1. Välj **enkel inloggning**på sidan **iQualify LMS** Application Integration i [Azure Portal](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, utför följande steg:

    ![information om enkel inloggning för iQualify LMS-domäner och URL: er](common/idp-intiated.png)

    a. I textrutan **Identifierare** skriver du en URL med följande mönster:  
    | |
    |--|--|
    | Produktions miljö: `https://<yourorg>.iqualify.com/`|
    | Test miljö: `https://<yourorg>.iqualify.io`|

    b. I textrutan **Svars-URL** skriver du in en URL med följande mönster:
    | |
    |--|--|
    | Produktions miljö: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Test miljö: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    ![information om enkel inloggning för iQualify LMS-domäner och URL: er](common/metadata-upload-additional-signon.png)

    I textrutan **Inloggnings-URL** skriver du in en URL med följande mönster:
    | |
    |--|--|
    | Produktions miljö: `https://<yourorg>.iqualify.com/login` |
    | Test miljö: `https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera värdena med den faktiska identifieraren, svars-URL och inloggnings-URL. Kontakta [IQUALIFY LMS client support team](https://www.iqualify.com/) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

6. Ditt iQualify LMS-program förväntar sig SAML-intyg i ett särskilt format, vilket kräver att du lägger till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut. Klicka på **Redigera** ikon för att öppna dialog rutan **användarattribut** .

    ![image](common/edit-attribute.png)

7. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** så redigerar du anspråken genom att använda **Redigera-ikonen** eller lägga till anspråken genom att använda **Lägg till nytt anspråk** för att konfigurera SAML-tokenattribut som det visas i bilden ovan och utföra följande steg:

    | Name | Källattribut|
    | --- | --- |
    | e-post | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.surname |
    | person_id | "ditt attribut" |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

    > [!Note]
    > Attributet **person_id** är **valfritt**

8. På sidan **Konfigurera enkel inloggning med SAML** går du till avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Certifikat (Base64)** från de angivna alternativen enligt dina behov och sparar det på datorn.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

9. I avsnittet **Konfigurera IQUALIFY LMS** kopierar du lämpliga URL: er enligt ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-iqualify-lms-single-sign-on"></a>Konfigurera iQualify LMS enkel inloggning

1. Öppna ett nytt webbläsarfönster och logga sedan in på din iQualify-miljö som administratör.

1. När du är inloggad klickar du på din avatar längst upp till höger och klickar sedan på **konto inställningar**

    ![Kontoinställningar](./media/iqualify-tutorial/setting1.png)

1. I området konto inställningar klickar du på menyn menyfliksområdet till vänster och klickar på **integreringar**

    ![INTEGRERINGAR](./media/iqualify-tutorial/setting2.png)

1. Under INTEGRERINGar klickar du på **SAML** -ikonen.

    ![SAML-ikon](./media/iqualify-tutorial/setting3.png)

1. Utför följande steg i dialog rutan **Inställningar för SAML-autentisering** :

    ![Inställningar för SAML-autentisering](./media/iqualify-tutorial/setting4.png)

    a. I rutan **URL för enkel inloggning för SAML i SAML** klistrar du in värdet för **inloggnings-URL:** en som KOPIERAts från Azure AD-programmets konfigurations fönster.

    b. I rutan **URL för SAML-utloggning** klistrar du in URL-värdet för **utloggning** som KOPIERAts från Azure AD-programkonfigurations fönstret.

    c. Öppna den hämtade certifikat filen i anteckningar, kopiera innehållet och klistra in det i rutan **offentligt certifikat** .

    d. I **etiketten inloggnings knapp** anger du namnet på knappen som ska visas på inloggnings sidan.

    e. Klicka på **Spara**.

    f. Klicka på **Uppdatera**.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet aktiverar du Britta Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till iQualify LMS.

1. I Azure Portal väljer du **företags program**, väljer **alla program**och väljer sedan **iQualify LMS**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. I listan program väljer du **IQUALIFY LMS**.

    ![IQualify LMS-länken i program listan](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-iqualify-lms-test-user"></a>Skapa iQualify LMS test User

I det här avsnittet skapas en användare som kallas Britta Simon i iQualify LMS. iQualify LMS stöder just-in-Time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i iQualify LMS, skapas en ny efter autentiseringen.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på iQualify LMS-panelen på åtkomst panelen ska du få inloggnings sidan för ditt iQualify LMS-program. 

   ![inloggnings sida](./media/iqualify-tutorial/login.png) 

Klicka på **Logga in med knappen Azure AD** och du bör bli inloggad automatiskt till ditt iQualify LMS-program.

Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)