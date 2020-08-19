---
title: 'Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med BenSelect | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och BenSelect.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.openlocfilehash: 98740153def94742a8bbba1ee6f4f022b614ecfa
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88537149"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-benselect"></a>Självstudie: Azure Active Directory integration med enkel inloggning (SSO) med BenSelect

I den här självstudien får du lära dig hur du integrerar BenSelect med Azure Active Directory (Azure AD). När du integrerar BenSelect med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till BenSelect.
* Gör det möjligt för användarna att logga in automatiskt till BenSelect med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* BenSelect för enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.


* BenSelect stöder **IDP** INITIERAd SSO

> [!NOTE]
> ID för det här programmet är ett fast sträng värde så att endast en instans kan konfigureras i en klient.

## <a name="adding-benselect-from-the-gallery"></a>Lägga till BenSelect från galleriet

Om du vill konfigurera integreringen av BenSelect i Azure AD måste du lägga till BenSelect från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com) med antingen ett arbets-eller skol konto eller en personlig Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , skriver du **BenSelect** i sökrutan.
1. Välj **BenSelect** från resultat panelen och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.


## <a name="configure-and-test-azure-ad-single-sign-on-for-benselect"></a>Konfigurera och testa enkel inloggning med Azure AD för BenSelect

Konfigurera och testa Azure AD SSO med BenSelect med hjälp av en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i BenSelect.

Om du vill konfigurera och testa Azure AD SSO med BenSelect, slutför du följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera BENSELECT SSO](#configure-benselect-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa BenSelect test User](#create-benselect-test-user)** -om du vill ha en motsvarighet till B. Simon i BenSelect som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/)går du till sidan för program integrering i **BenSelect** , letar upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** anger du värden för följande fält:

    Skriv en URL i text rutan **svars-URL** med följande mönster:  `https://www.benselect.com/enroll/login.aspx?Path=<tenant name>`

    > [!NOTE]
    > Värdet är inte verkligt. Uppdatera värdet för med den faktiska svars-URL:en. Kontakta [BenSelect client support team](mailto:support@selerix.com) för att hämta värdet. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

1. BenSelect-programmet förväntar sig SAML-intyg i ett särskilt format, vilket innebär att du kan lägga till anpassade mappningar av attribut i konfigurationen för SAML-token. I följande skärmbild visas listan över standardattribut.

    ![image](common/edit-attribute.png)

1. Klicka på **redigerings** ikonen för att redigera **namnet på namn identifieraren**.

    ![image](media/benselect-tutorial/mail-prefix1.png)

1. I avsnittet **hantera användar anspråk** utför du följande steg:

    ![image](media/benselect-tutorial/mail-prefix2.png)

    a. Välj **omvandling** som **källa**.

    b. I list rutan **omvandling** väljer du **ExtractMailPrefix ()**.

    c. I list rutan **parameter 1** väljer du **User. UserPrincipalName**.

    d. Klicka på **Spara**.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , Sök efter **certifikat (RAW)** och välj **Ladda ned** för att ladda ned certifikatet och spara det på din dator.

    ![Länk för nedladdning av certifikatet](common/certificateraw.png)

1. I avsnittet **Konfigurera BenSelect** kopierar du lämpliga URL: er baserat på ditt krav.

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

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till BenSelect.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **BenSelect**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Klicka på knappen **tilldela** i dialog rutan **Lägg till tilldelning** .

## <a name="configure-benselect-sso"></a>Konfigurera BenSelect SSO

Om du vill konfigurera enkel inloggning på **BenSelect** sida måste du skicka det hämtade **certifikatet (RAW)** och lämpliga kopierade url: er från Azure Portal till [support teamet för BenSelect](mailto:support@selerix.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

> [!NOTE]
> Du måste ange att denna integrering kräver att SHA256-algoritmen (SHA1 inte stöds) för att ställa in SSO på lämplig server som app2101 osv.

### <a name="create-benselect-test-user"></a>Skapa BenSelect test användare

I det här avsnittet skapar du en användare som heter Britta Simon i BenSelect. Arbeta med [BenSelect support team](mailto:support@selerix.com) för att lägga till användare i BenSelect-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen BenSelect på åtkomst panelen, bör du loggas in automatiskt på den BenSelect som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova BenSelect med Azure AD](https://aad.portal.azure.com/)

