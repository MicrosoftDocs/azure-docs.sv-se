---
title: 'Självstudie: Azure Active Directory integration med Ceridian Dayforce HCM | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Ceridian Dayforce HCM.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: jeedes
ms.openlocfilehash: 47e438b53f6aa3c9bc00da77c3532278b99e7269
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88530388"
---
# <a name="tutorial-azure-active-directory-integration-with-ceridian-dayforce-hcm"></a>Självstudie: Azure Active Directory integrering med Ceridian Dayforce HCM

I den här självstudien får du lära dig hur du integrerar Ceridian Dayforce HCM med Azure Active Directory (AD Azure).
Integreringen av Ceridian Dayforce HCM med Azure AD medför följande fördelar:

* Du kan styra vem som har åtkomst till Ceridian Dayforce HCM från Azure AD.
* Du kan konfigurera inställningar så att användarna loggas in automatiskt i Ceridian Dayforce HCM (enkel inloggning) med sina Azure AD-konton.
* Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill ha mer information om SaaS-appintegrering med Azure AD läser du avsnittet om [programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

För att konfigurera Azure AD-integrering med Ceridian Dayforce HCM behöver du följande:

* En Azure AD-prenumeration. Om du inte har någon Azure AD-miljö kan du hämta en månads utvärderingsversion [här](https://azure.microsoft.com/pricing/free-trial/)
* En Ceridian Dayforce HCM-prenumeration med stöd för enkel inloggning

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du enkel inloggning med Azure AD i en testmiljö.

* Ceridian Dayforce HCM stöder **SP**-initierad enkel inloggning

## <a name="adding-ceridian-dayforce-hcm-from-the-gallery"></a>Lägga till Ceridian Dayforce HCM från galleriet

För att konfigurera integreringen av Ceridian Dayforce HCM med Azure AD måste du lägga till Ceridian Dayforce HCM från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till Ceridian Dayforce HCM från galleriet:**

1. I **[Azure-portalen](https://portal.azure.com)** går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**-ikonen.

    ![Azure Active Directory-knappen](common/select-azuread.png)

2. Gå till **Företagsprogram** och välj alternativet **Alla program**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program](common/add-new-app.png)

4. I sökrutan skriver du **Ceridian Dayforce HCM**, väljer **Ceridian Dayforce HCM** från resultatpanelen och klickar sedan på **Lägg till** för att lägga till programmet.

     ![Ceridian Dayforce HCM i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa enkel inloggning i Azure AD med Ceridian Dayforce HCM baserat på en testanvändare med namnet **Britta Simon**.
För att enkel inloggning ska fungera måste en länkrelation mellan en Azure AD-användare och den relaterade användaren i Ceridian Dayforce HCM upprättas.

För att konfigurera och testa enkel inloggning i Azure AD med Ceridian Dayforce HCM måste du utföra följande uppgifter:

1. **[Konfigurera enkel inloggning med Azure AD](#configure-azure-ad-single-sign-on)** – så att användarna kan använda den här funktionen.
2. **[Konfigurera enkel inloggning för Ceridian Dayforce HCM](#configure-ceridian-dayforce-hcm-single-sign-on)** – för att konfigurera inställningar för enkel inloggning på programsidan.
3. **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** – för att testa enkel inloggning med Azure AD med Britta Simon.
4. **[Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user)** – så att Britta Simon kan använda enkel inloggning med Azure AD.
5. **[Skapa testanvändare för Ceridian Dayforce HCM](#create-ceridian-dayforce-hcm-test-user)** – för att skapa en motsvarighet till Britta Simon i Ceridian Dayforce HCM som är länkad till Azure AD-representationen av användaren.
6. **[Testa enkel inloggning](#test-single-sign-on)** – för att verifiera om konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet aktiverar du enkel inloggning med Azure AD i Azure-portalen.

Utför följande steg för att konfigurera enkel inloggning i Azure AD med Ceridian Dayforce HCM:

1. Välj **Enkel inloggning** på sidan för programintegrering med **Ceridian Dayforce HCM** på [Azure-portalen](https://portal.azure.com/).

    ![Konfigurera länk för enkel inloggning](common/select-sso.png)

2. I dialogrutan **Välj en metod för enkel inloggning** väljer du läget **SAML/WS-Fed** för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

3. På sidan **Konfigurera enkel inloggning med SAML** klickar du på **redigeringsikonen** för att öppna dialogrutan **Grundläggande SAML-konfiguration**.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![Information om enkel inloggning med Ceridian Dayforce HCM-domäner och -URL:er](common/sp-identifier-reply.png)

    a. I textrutan **Inloggnings-URL** skriver du URL:en som användarna använder för att logga in till Ceridian Dayforce HCM-programmet.

    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://sso.dayforcehcm.com/<DayforcehcmNamespace>` |
    | För testning | `https://ssotest.dayforcehcm.com/<DayforcehcmNamespace>` |

    b. I textrutan **Identifierare** anger du en URL med följande mönster:

    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://ncpingfederate.dayforcehcm.com/sp` |
    | För testning | `https://fs-test.dayforcehcm.com/sp` |

    c. I textrutan **Svars-URL** skriver du URL:en som används av Azure AD för att publicera svaret.

    | Miljö | URL |
    | :-- | :-- |
    | För produktion | `https://ncpingfederate.dayforcehcm.com/sp/ACS.saml2` |
    | För testning | `https://fs-test.dayforcehcm.com/sp/ACS.saml2` |

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Ceridian Dayforce HCM-kundsupporten](https://www.ceridian.com/support) och be om dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

5. Ceridian Dayforce HCM-programmet förväntar sig att SAML-försäkran har ett specifikt format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet **Användarattribut** på sidan för programintegrering. På sidan **Konfigurera enkel inloggning med SAML** klickar du på knappen **Redigera** för att öppna dialogrutan **Användarattribut**.

    ![image](common/edit-attribute.png)

6. I avsnittet **Användaranspråk** i dialogrutan **Användarattribut** konfigurerar du SAML-tokenattributet på det sätt som visas i bilden ovan och utför följande steg:
    
    | Name | Källattribut|
    | ---------| --------- |
    | name  | user.extensionattribute2 |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna **Namnrymd** tom.

    d. Välj Källa som **Attribut**.

    e. Från listan **Källattribut** väljer du det användarattribut som du vill använda för implementeringen. Om du t.ex. vill använda EmployeeID som unikt användar-ID och du har lagrat attributvärdet i ExtensionAttribute2 väljer du sedan user.extensionattribute2.

    f. Klicka på **OK**

    ex. Klicka på **Spara**.

7. På sidan **Konfigurera enkel inloggning med SAML**, i avsnittet **SAML-signeringscertifikat**, klickar du på **Ladda ned** för att ladda ned **Metadata XML** från de angivna alternativen enligt dina behov och spara den på datorn.

    ![Länk för nedladdning av certifikatet](common/metadataxml.png)

8. I avsnittet **Konfigurera Ceridian Dayforce HCM** kopierar du lämpliga URL:er baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

    a. Inloggnings-URL

    b. Azure AD-identifierare

    c. Utloggnings-URL

### <a name="configure-ceridian-dayforce-hcm-single-sign-on"></a>Konfigurera enkel inloggning för Ceridian Dayforce HCM

För att konfigurera enkel inloggning på **Ceridian Dayforce HCM**-sidan måste du skicka **XML:en med metadata** som du laddat ned och relevanta kopierade URL:er från Azure-portalen till [Ceridian Dayforce HCM-supportteamet](https://www.ceridian.com/support). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](common/users.png)

2. Välj **ny användare** överst på skärmen.

    ![Knappen Ny användare](common/new-user.png)

3. Genomför följande steg i Användaregenskaper.

    ![Dialogrutan Användare](common/user-properties.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **användar namn** skriver du **brittasimon \@ yourcompanydomain. extension**  
    Till exempel BrittaSimon@contoso.com

    c. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan Lösenord.

    d. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet gör du det möjligt för Britta Simon att använda enkel inloggning med Azure genom att ge åtkomst till Ceridian Dayforce HCM.

1. På Azure-portalen väljer du **Företagsprogram**, **Alla program** och sedan **Ceridian Dayforce HCM**.

    ![Bladet Företagsprogram](common/enterprise-applications.png)

2. Välj **Ceridian Dayforce HCM** i listan med program.

    ![Ceridian Dayforce HCM-länk i listan med program](common/all-applications.png)

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”](common/users-groups-blade.png)

4. Klicka på knappen **Lägg till användare** och välj sedan **Användare och grupper** i dialogrutan **Lägg till tilldelning**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

5. I dialogrutan **Användare och grupper** väljer du **Britta Simon** i listan med användare och klickar på knappen **Välj** längst ned på skärmen.

6. Om du förväntar dig ett roll värde i SAML-kontrollen väljer du lämplig roll för användaren i listan i dialog rutan **Välj roll** och klickar sedan på knappen **Välj** längst ned på skärmen.

7. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

### <a name="create-ceridian-dayforce-hcm-test-user"></a>Skapa Ceridian Dayforce HCM-testanvändare

I det här avsnittet ska du skapa en användare med namnet Britta Simon i Ceridian Dayforce HCM. Arbeta med  [Ceridian Dayforce HCM-supportteamet](https://www.ceridian.com/support) när du lägger till användare till Ceridian Dayforce HCM-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på Ceridian Dayforce HCM-panelen på åtkomstpanelen bör du loggas in automatiskt i Ceridian Dayforce HCM-programmet som du har konfigurerat enkel inloggning för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är program åtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

